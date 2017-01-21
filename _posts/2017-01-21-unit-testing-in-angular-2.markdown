---
layout: post
title:  "Unit Testing in Angular 2: Common Exceptions"
date:   2017-01-21
category: coding
backgrounds:
    - http://blog.jdonado.com/assets/images/jsbg.jpg
thumb: http://blog.jdonado.com/assets/images/scaffold-parthenon.jpg
tags: angular angular2 unit testing
---

### Unit Testing in Angular 2: Common Exceptions

I've been working on an Angular 2 project ([or Angular 3, or 4, or whatever...](http://angularjs.blogspot.de/2016/12/ok-let-me-explain-its-going-to-be.html?utm_content=bufferec238&utm_medium=social&utm_source=facebook.com&utm_campaign=buffer)) during the last couple of months, and I'm still learning how to unit test a project based in this new framework.

I'd like to share with you some of the most common exceptions I've found when first writing unit tests, and the solutions to them.

#### Problem: 'your-directive-name' is not a known element
Solution: import the component where `your-directive-name` is defined and include it into the declarations field of the TestBed.

#### Problem: Can't bind to 'ngModel' since it isn't a known property of 'input'
Solution: import `FormsModule` and include it into the `imports` field of the TestBed.

#### Problem: No provider for YourService!
Solution: import `YourService` and include it into the `providers` of the TestBed.

The same error can appear if you need Http: `No provider for Http!`. The same solution applies.

#### Problem: No provider for ConnectionBackend!
That appears when you test components or services where Http requests are performed.
Solution: import `ConnectionBackend` and include it together with the rest of providers of the TestBed.

#### Problem: No provider for RequestOptions!
Solution: import `HttpModule` and include it into the `imports` of TestBed.

### Example
Here's some code for a `HomeComponent` unit test, that uses a `LoginComponent` and loads a `SessionService`, requiring `Http` and all the aforementioned dependencies in a single example.

I just want to illustrate how to load and specify the dependencies needed in order to bootstrap a test with them and the associated errors if any of these steps are missing.

It's not an example about how to test a component (that would be subject for another post). What we test here is just whether the `HomeComponent` component is successfully created.

{% highlight javascript %}

/* tslint:disable:no-unused-variable */
import { async, ComponentFixture, TestBed } from '@angular/core/testing';
import { By } from '@angular/platform-browser';
import { DebugElement } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { SessionService } from '../../services/session.service';
import { Http, HttpModule, Headers, RequestMethod, RequestOptions, Response, ConnectionBackend } from '@angular/http';
import { HomeComponent } from './home.component';
import { LoginComponent } from '../login/login.component';

describe('HomeComponent', () => {
  let component: HomeComponent;
  let fixture: ComponentFixture<HomeComponent>;

  beforeEach(async(() => {
    TestBed.configureTestingModule({
      imports: [FormsModule, HttpModule],
      providers: [SessionService, Http, ConnectionBackend],
      declarations: [HomeComponent, LoginComponent]
    })
      .compileComponents();
  }));

  beforeEach(() => {
    fixture = TestBed.createComponent(HomeComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('exists', () => {
    expect(component).toBeTruthy();
  });
});

{% endhighlight %}
