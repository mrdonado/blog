---
layout: post
title:  "Unit Testing in Angular 2: Common Exceptions"
date:   2017-01-21
category: coding
backgrounds:
    - https://blog.jdonado.com/assets/images/jsbg.jpg
thumb: https://blog.jdonado.com/assets/images/scaffold-parthenon.jpg
tags: angular angular2 unit testing
---

# Unit Testing in Angular 2: Common Exceptions

I've been working on an Angular 2 project ([or Angular 3, or 4, or whatever...](http://angularjs.blogspot.de/2016/12/ok-let-me-explain-its-going-to-be.html?utm_content=bufferec238&utm_medium=social&utm_source=facebook.com&utm_campaign=buffer)) during the last couple of months, and I'm still learning how to unit test a project based in this new framework.

I'd like to share with you some of the most common exceptions I've found when first writing unit tests, and the solutions to them.

## Problem: 'your-directive-name' is not a known element

It happens when you use a custom component in a template. You have two options here:

- Import `CUSTOM_ELEMENTS_SCHEMA` from `@angular/core` and add it to the `schemas` array of your `TestBed`:

{% highlight javascript %}

TestBed.configureTestingModule({
  schemas: [CUSTOM_ELEMENTS_SCHEMA],
  ...
});

{% endhighlight %}

This way it will just be ignored, and you can test that custom component in some other test (best practice: keep your unit tests simple and test just one thing at a time).

- If you absolutely need to include the actual custom component in your test, you can import it in your spec and include it into the `declarations` field of the TestBed.

## Problem: Can't bind to 'ngModel' since it isn't a known property of 'input'

Solution: import `FormsModule` and include it into the `imports` field of the TestBed.

## Problem: No provider for YourService!

Solution: import `YourService` and include it into the `providers` of the TestBed. At best you should include a mocked version, since you probably want to test that service in another specific test.

{% highlight javascript %}

const serviceMock = { someKey: 'someValue'};
TestBed.configureTestingModule({
  providers: [
    {
      provide: YourService,
      useValue: serviceMock
    }
  ]
  ...
});

{% endhighlight %}

## Problem: No provider for Http!

In case of the Http service, testing it requires a bit more of boilerplate. When some part of your code needs to do HTTP requests, you might also get the following errors when testing:

- `Problem: No provider for ConnectionBackend!`
- `Problem: No provider for RequestOptions!`

Here are the steps needed to properly test a service that uses Angular 2's `Http` service:

1.- Add the following imports to your spec:

{% highlight javascript %}

import {
  BaseRequestOptions,
  Http,
  Response,
  ResponseOptions,
  RequestMethod,
  XHRBackend
} from '@angular/http';

import {
  MockBackend,
  MockConnection
} from '@angular/http/testing';

{% endhighlight %}

2.- Declare a MockBackend type variable in your describe block:

{% highlight javascript %}
  let backend: MockBackend;
{% endhighlight %}

3.- Add the following lines to the providers of your `TestBed`:

{% highlight javascript %}
providers: [
  MockBackend,
  BaseRequestOptions,
  {
    provide: Http,
    useFactory: (backendInstance: MockBackend, defaultOptions: BaseRequestOptions) => {
      return new Http(backendInstance, defaultOptions);
    },
    deps: [MockBackend, BaseRequestOptions]
  }
],
{% endhighlight %}

4.- Inject a MockBackend before each test:

{% highlight javascript %}
beforeEach(inject([MockBackend],
  (_mockBackend) => {
    backend = _mockBackend;
  }));
{% endhighlight %}

5.- Now you're ready to test your service with a mocked backend by simply subscribing to it. Here you have an example:

{% highlight javascript %}
it('should post some key with some value', (done) => {
  backend.connections.subscribe((connection: MockConnection) => {
    expect(connection.request.method).toEqual(RequestMethod.Post);
    const responseBody = JSON.parse(connection.request.getBody());
    expect(responseBody.someKey).toEqual('someValue');
    // Mock a response with an empty object
    connection.mockRespond(new Response(new ResponseOptions({})));
    done();
  });
  yourService.postSomething();
});
{% endhighlight %}

## Example

Here's a working example for an `AnalysisFormComponent` unit test, that mocks a `LogService` and performs an HTTP POST request.

{% highlight javascript %}

import { LogService } from '../../../services/log.service';
import { async, ComponentFixture, inject, TestBed } from '@angular/core/testing';
import { AnalysisFormComponent } from './analysis-form.component';
import { FormsModule } from '@angular/forms';

import {
  BaseRequestOptions,
  Http,
  Response,
  ResponseOptions,
  RequestMethod,
  XHRBackend
} from '@angular/http';

import {
  MockBackend,
  MockConnection
} from '@angular/http/testing';

describe('AnalysisFormComponent', () => {
  let component: AnalysisFormComponent;
  let fixture: ComponentFixture<AnalysisFormComponent>;
  let backend: MockBackend;

  const logServiceSpy = jasmine.createSpyObj('logService', ['sendMessage']);

  beforeEach(async(() => {
    TestBed.configureTestingModule({
      declarations: [AnalysisFormComponent],
      imports: [FormsModule],
      providers: [
        MockBackend,
        BaseRequestOptions,
        {
          provide: LogService,
          useValue: logServiceSpy
        },
        {
          provide: Http,
          useFactory: (backendInstance: MockBackend, defaultOptions: BaseRequestOptions) => {
            return new Http(backendInstance, defaultOptions);
          },
          deps: [MockBackend, BaseRequestOptions]
        }
      ],
    })
      .compileComponents();
  }));

  beforeEach(() => {
    fixture = TestBed.createComponent(AnalysisFormComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  beforeEach(inject([MockBackend],
    (_mockBackend) => {
      backend = _mockBackend;
    }));

  it('should exist', () => {
    expect(component).toBeTruthy();
  });

  it('should clear the form after closing', () => {
    component.message = 'someMessage';
    component.userName = 'someUserName';
    component.userDescription = 'someDescription';
    expect(component.message).toEqual('someMessage');
    expect(component.userName).toEqual('someUserName');
    expect(component.userDescription).toEqual('someDescription');
    component.closeForm();
    expect(component.message).toEqual('');
    expect(component.userName).toEqual('');
    expect(component.userDescription).toEqual('');
  });

  it('should send a POST request to the server when creating a new job for the analysis engine', (done) => {
    backend.connections.subscribe((connection: MockConnection) => {
      expect(connection.request.method).toEqual(RequestMethod.Post);
      const responseBody = JSON.parse(connection.request.getBody());
      expect(responseBody.source).toEqual('web');
      expect(responseBody.user_name).toEqual('someUserName');
      expect(responseBody.user_description).toEqual('someDescription');
      expect(responseBody.message).toEqual('someMessage');
      connection.mockRespond(new Response(new ResponseOptions({})));
      done();
    });
    component.message = 'someMessage';
    component.userName = 'someUserName';
    component.userDescription = 'someDescription'; component.sendNewJob();
  });

  it('should log an error message when the analysis couldn\'t be POSTed', (done) => {
    const errorMessage = 'An error occurred!!!';
    backend.connections.subscribe((connection: MockConnection) => {
      expect(connection.request.method).toEqual(RequestMethod.Post);
      const responseBody = JSON.parse(connection.request.getBody());
      connection.mockError(new Error(errorMessage));
    });
    component.message = 'someMessage';
    component.userName = 'someUserName';
    component.userDescription = 'someDescription';
    try {
      component.sendNewJob();
    } catch (err) {
      expect(err.message).toEqual(errorMessage);
      expect(logServiceSpy.sendMessage).toHaveBeenCalledWith(jasmine.stringMatching('a problem'));
      done();
    }
  });

});

{% endhighlight %}
