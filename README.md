# accsc560assign4.1final

/* Purpose of the code: To create a beginning application with Angular on Stackblitz. */
/* Note: Make sure to view in raw mode for under the readme file to see correct spacing of code. */

/* product-list.component.css down below, which is empty except for the Google comment, which I will exclude here in GitHub just in case. */

/* product-list.component.html down below */

<h2>Products</h2>



<div *ngFor = "let product of products">


  <h3>
    <a [title]="product.name + ' details'"
    [routerLink] = "['/products', product.id]">
    {{product.name}}
    </a>
  </h3>

  <!-- ... -->

  <!-- Excluded down below so newer aspects regarding routerLink would function or in this case, from the "Associate a URL Component" section. -->
  <!--<p *ngIf="product.description">
    Description: {{ product.description }}
  </p>

  <button type="button" (click)="share()">
    Share
  </button>

  <app-product-alerts
  [product] = "product"
  (notify)="onNotify()">
</app-product-alerts>-->
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
  products = [...products];

  share() {
    window.alert('The product has been shared!');
  }

  onNotify() {
    window.alert('You will be notified when the product goes on sale');
  }
}



/* product-alerts.component.css doesn't have any text or content within at all in this case. Will move on to next file of product-alerts.*/

/* product-alerts.component.html down below */
<p *ngIf="product && product.price > 700">
  <button type="button" (click)="notify.emit()">Notify Me</button>
</p>

/* product-alerts.component.ts down below. */
import { Component, Input, Output, EventEmitter } from '@angular/core';
import { Product } from '../products';
@Component({
  selector: 'app-product-alerts',
  templateUrl: './product-alerts.component.html',
  styleUrls: ['./product-alerts.component.css']
})
export class ProductAlertsComponent {
  @Input() product: Product | undefined;
  @Output() notify = new EventEmitter();
}

/* app.module.ts file down below, which helps define the main parts that are used to import and make up the application itself */
import { HttpClientModule } from '@angular/common/http';
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { RouterModule } from '@angular/router';
import { ReactiveFormsModule } from '@angular/forms';

import { AppComponent } from './app.component';
import { TopBarComponent } from './top-bar/top-bar.component';
import { ProductListComponent } from './product-list/product-list.component';
import { ProductAlertsComponent } from './product-alerts/product-alerts.component';
import { ProductDetailsComponent } from './product-details/product-details.component';
import { CartComponent } from './cart/cart.component';
import { ShippingComponent } from './shipping/shipping.component';

@NgModule({
  imports: [
    BrowserModule,
    HttpClientModule,
    ReactiveFormsModule,
    RouterModule.forRoot([
      { path: '', component: ProductListComponent },
      { path: 'products/:productId', component: ProductDetailsComponent },
      { path: 'cart', component: CartComponent },
      { path: 'shipping', component: ShippingComponent },
      
    ])
  ],
  declarations: [
    AppComponent,
    TopBarComponent,
    ProductListComponent,
    ProductAlertsComponent,
    ProductDetailsComponent,
    CartComponent,
    ShippingComponent,
    
  ],
  bootstrap: [
    AppComponent
  ]
})
export class AppModule { }

/* product-details files down below */
/*product-details.component.css doesn have any content within and so, will move on to next file within this product-details folder.*/

/*product-details.component.html down below */
<h2> Product Details</h2>

<div *ngIf="product">
  <h3>{{ product.name }}</h3>
  <h4>{{ product.price | currency}}</h4>
  <p>{{ product.description }}</p>
  <button type = "button" (click)="addToCart(product)">Buy</button>
</div>

/*product.details.component.ts down below */
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';
import { Product, products } from '../products';
import { CartService } from '../cart.service';

@Component({
  selector: 'app-product-details',
  templateUrl: './product-details.component.html',
  styleUrls: ['./product-details.component.css']
})
export class ProductDetailsComponent implements OnInit {
  product: Product | undefined;

  constructor(
    private route: ActivatedRoute,
    private cartService: CartService
    ) { }

  addToCart(product: Product) {
    this.cartService.addToCart(product);
    window.alert('Your product has been added to the cart!');
  }

  ngOnInit() {
    //First get the product id from the current route.
    const routeParams = this.route.snapshot.paramMap;
    const productIdFromRoute = Number(routeParams.get('productId'));
  
    //Find the product that correspond with the id provided in route.
    this.product = products.find(product => product.id === productIdFromRoute);
  }

}

/*product-details.component.spec.ts down below */
import { ComponentFixture, TestBed } from '@angular/core/testing';

import { ProductDetailsComponent } from './product-details.component';

describe('ProductDetailsComponent', () => {
  let component: ProductDetailsComponent;
  let fixture: ComponentFixture<ProductDetailsComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [ ProductDetailsComponent ]
    })
    .compileComponents();

    fixture = TestBed.createComponent(ProductDetailsComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });
});

/* cart.service.ts down below */
import { HttpClient } from '@angular/common/http';
import { Product } from './products';
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class CartService {

  items: Product[] = [];
  /* ... */

  addToCart(product: Product) {
    this.items.push(product);
  }

  getItems() {
    return this.items;
  }

  clearCart() {
    this.items = [];
    return this.items;
  }

  getShippingPrices() {
    return this.http.get<{type: string, price: number}[]>
  ('/assets/shipping.json');
  }

  /* ... */

  constructor(
    private http: HttpClient
  ) { }
  /* ... */
}

/* cart folder files down below */
/* cart.component.css doesn't have any content so will move on to the .html file */

/* cart.component.html file down below */
<h3>Cart</h3>

<p>
  <a routerLink="/shipping">Shipping Prices</a>
</p>

<div class="cart-item" *ngFor="let item of items">
  <span>{{ item.name }}</span>
  <span>{{ item.price | currency }}</span>
</div>

<form [formGroup]="checkoutForm" (ngSubmit) = "onSubmit()">
  
  <div>
    <label for = "name">
      Name
    </label>
    <input id="name" type="text" formControlName="name">
  </div>

  <div>
    <label for="address">
      Address
    </label>
    <input id="address" type="text" formControlName="address">
  </div>
  
  <button class="button" type="submit">Purchase</button>
</form>

/* cart.component.ts down below */
import { Component } from '@angular/core';
import { FormBuilder } from '@angular/forms';

import { CartService } from '../cart.service';
@Component({
  selector: 'app-cart',
  templateUrl: './cart.component.html',
  styleUrls: ['./cart.component.css']
})
export class CartComponent {

  items = this.cartService.getItems();

  checkoutForm = this.formBuilder.group({
    name: '',
    address: ''
  });

  constructor(
    private cartService: CartService,
    private formBuilder: FormBuilder,
  ) { }

  onSubmit(): void {
    //Process checkout data here
    this.items = this.cartService.clearCart();
    console.warn('Your order has been submitted', this.checkoutForm.value);
    this.checkoutForm.reset();
  }

}

/* cart.component.spec.ts down below */
import { ComponentFixture, TestBed } from '@angular/core/testing';

import { CartComponent } from './cart.component';

describe('CartComponent', () => {
  let component: CartComponent;
  let fixture: ComponentFixture<CartComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [ CartComponent ]
    })
    .compileComponents();

    fixture = TestBed.createComponent(CartComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });
});

/* shipping file section down below. As reminder, there is no content within the shipping.component.css file. */

/* shipping.component.html down below */
<h3>Shipping Prices</h3>

<div class="shipping-item" *ngFor="let shipping of shippingCosts | async">
  <span>{{ shipping.type }}</span>
  <span>{{ shipping.price | currency }}</span>
</div>

/* shipping.component.ts down below */
import { Component, OnInit} from '@angular/core';

import { Observable } from 'rxjs';
import { CartService } from '../cart.service';


@Component({
  selector: 'app-shipping',
  templateUrl: './shipping.component.html',
  styleUrls: ['./shipping.component.css']
})


export class ShippingComponent implements OnInit {
  
  constructor(private cartService: CartService) { }

  

  shippingCosts!: Observable<{ type: string, price: number }[]>;
  
  ngOnInit(): void {
    this.shippingCosts =  this.cartService.getShippingPrices();
  }
    //Originally before fix: With this.shipping costs =..., this results in coming up with TS2322 error of "Did you mean to call this expression?"
    //Been trying to fix error by deleting and then retyping in code. Worked for others but for some reason, it is not working properly.
  

}

/* shipping.component.spec.ts down below */
import { ComponentFixture, TestBed } from '@angular/core/testing';

import { ShippingComponent } from './shipping.component';

describe('ShippingComponent', () => {
  let component: ShippingComponent;
  let fixture: ComponentFixture<ShippingComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [ ShippingComponent ]
    })
    .compileComponents();

    fixture = TestBed.createComponent(ShippingComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });
});

/* top-bar files down below (excluding top-bar.component.tss since it doesn't have any content besides a comment regarding Google) */

/* top-bar.component.html down below */
<a [routerLink]="['/']">
  <h1>My Store</h1>
</a>

<a routerLink="/cart" class ="button fancy-button">
  <i class ="material-icons">shopping_cart</i>Checkout
</a>

/* top-bar.component.ts down below */
import { Component } from '@angular/core';

@Component({
  selector: 'app-top-bar',
  templateUrl: './top-bar.component.html',
  styleUrls: ['./top-bar.component.css']
})
export class TopBarComponent {

}

/* assets shipping.json file down below */
[
  {
    "type": "Overnight",
    "price": 25.99
  },
  {
    "type": "2-Day",
    "price": 9.99
  },
  {
    "type": "Postal",
    "price": 2.99
  }
]

/* End of code here. There are other files as well. Will include link to the editor as well. */



