# accsc560assign4.1draft1

/* Purpose of the code: To create a beginning application with Angular on Stackblitz. */
/* Important Note: Was able to get the code working up to the Pass data to child component. Tried to understand error as noted by ezkeal and the following users, but can't seem to figure out how to import and state the component class within the app.module file. I did attempt to insert and import them manually but this caused more errors to occur. So, did fork newer version from user sonunkapoor but understand the purpose behind the child component, which is to create child component and then pass the data between it and the parent component so one can create a button to notify someone about a phone price. Did also attempt to do following steps like navigation but was unable to access terminal nor was the command recognized, which resulted in another error when typing in the first navigation prompt in the console line. */
/* Note: Make sure to view in raw mode for under the readme file to see correct spacing of code. */

/* product-list.component.css down below, which is empty except for the Google comment, which I will exclude here in GitHub just in case. */

/* product-list.component.html down below */

<h2>Products</h2>

<div *ngFor="let product of products">

	<h3>
		<a [title]="product.name + ' details'">
			{{ product.name }}
		</a>
	</h3>

	<p *ngIf="product.description">
		Description: {{ product.description }}
	</p>

	<button (click)="share()">
    Share
  </button>

	<app-product-alerts 
	[product]="product" 
	(notify)="onNotify()">
	</app-product-alerts>

</div>

/* product-list.component.ts down below */
import { Component } from '@angular/core';

import { products } from '../products';

@Component({
  selector: 'app-product-list',
  templateUrl: './product-list.component.html',
  styleUrls: ['./product-list.component.css']
})
export class ProductListComponent {
  products = products;

  share() {
    window.alert('The product has been shared!');
  }

  onNotify() {
    window.alert('You will be notified when the product goes on sale');
  }
}


/* product-alerts.component.css doesn't have any text or content within at all in this case. Will move on to next file of product-alerts.*/

/* product-alerts.component.html down below */
<p *ngIf="product.price > 700">
  <button (click)="notify.emit()">Notify Me</button>
</p>

/* product-alerts.component.ts down below. I did change the first line to line up with the tutorial. */
import { Component, Input, Output, EventEmitter } from '@angular/core';

@Component({
  selector: 'app-product-alerts',
  templateUrl: './product-alerts.component.html',
  styleUrls: ['./product-alerts.component.css']
})
export class ProductAlertsComponent {
  @Input() product;
  @Output() notify = new EventEmitter();
}

/* app.module.ts file down below, which helps define the main parts that are used to import and make up the application itself */.
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { RouterModule } from '@angular/router';
import { ReactiveFormsModule } from '@angular/forms';

import { AppComponent } from './app.component';
import { TopBarComponent } from './top-bar/top-bar.component';
import { ProductListComponent } from './product-list/product-list.component';
import { ProductAlertsComponent } from './product-alerts/product-alerts.component';

@NgModule({
  imports: [
    BrowserModule,
    ReactiveFormsModule,
    RouterModule.forRoot([
      { path: '', component: ProductListComponent },
    ])
  ],
  declarations: [
    AppComponent,
    TopBarComponent,
    ProductListComponent,
    ProductAlertsComponent
  ],
  bootstrap: [ AppComponent ]
})
export class AppModule { }

