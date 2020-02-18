# Topicos-Proyecto-Final
Proyecto final de topicos tema: aplicacion de clima consumiendo de una api-rest 

Para el desarrollo de esta aplicación se requiere los siguientes pre-requisitos:

- Instalar node.js: https://nodejs.org/es/
 
-Instalar Ionic: 
  
  Para instalar Ionic necesitamos ejecutar el siguiente comando:
  npm install -g ionic

-Instalar cordova:
 
 npm install -g cordova

-Tener instalado un editor te código: Visual Studio Code, Atom, Sublime Text, etc.

Crear un proyecto en firebase.
Para ingresar a firebase se requiere una cuenta de gmail, una vez que accedemos a firebase creamos un proyecto y dentro de ese proyecto crearemos una aplicación de tipo web de la cual copiaremos las credenciales.

Habilitar autenticación por email y contraseña en firebase:
Entrar en el proyecto creado y dirigirse a la parte izquierda en el menu seleccionar Autentication, despues seleccionar la pestaña de metodos de autenticación y habilitar la opción de Email.

Creación del proyecto en ionic:
Cumplidos estos pre-requisitos, empezaremos creando el proyecto en ionic para lo cual se abre una terminal en el directorio donde se desee que se ubique el proyecto una vez abierta la terminal en el directorio de prefrencia se ejecuta en el terminal el siguiente comando:

ionic start <nombre del proyecto> blank

Una vez creado el proyecto abrir el mismo con el editor de codigo elegido para mi caso visual studio code, ya abierto tambien abrimos la terminal integrada de visual studio code.

Agregar credenciales de firebase a nuestro proyecto:
Abrir en la carpeta enviroments el archivo enviroment.ts y dentro agregar sus credenciales de su aplicacion en firebase de la siguiente forma:


export const environment = {
  production: false,
  firebaseConfig : {
    apiKey: "YOUR_apiKey",
    authDomain: "YOUR_authDomain",
    databaseURL: "authDomain",
    projectId: "YOUR_authDomain",
    storageBucket: "YOUR_authDomain",
    messagingSenderId: "YOUR_messagingSenderId",
    appId: "YOUR_appId",
    measurementId: "YOUR_measurementId"
  }

};

Instalar firebase en el proyecto ionic:
En dentro de nuestro proyecto ionic en la terminal ejecutar: npm install firebase @angular/fire save

Importar librerias en el archivo app.module.ts:

import { AngularFireModule } from '@angular/fire';
import { AngularFireAuthModule } from '@angular/fire/auth';
import { environment } from '../environments/environment';

@NgModule({
  declarations: [AppComponent],
  entryComponents: [],
  imports: [
    BrowserModule, 
    IonicModule.forRoot(), 
    AppRoutingModule,
    AngularFireModule.initializeApp(environment.firebaseConfig),
    AngularFireAuthModule
    ],
  providers: [
    StatusBar,
    SplashScreen,
    { provide: RouteReuseStrategy, useClass: IonicRouteStrategy }
  ],
  bootstrap: [AppComponent]
})
export class AppModule {}

+)Crear carpeta shared con archivo user.class.ts en la carpeta app, codigo del archivo user.class.ts:

export class User {
    email: string;
    password: string;
}

+)Crear servicio para autenticación:

en la terminal abierta dentro del proyecto ionic ejecutar: ionic generate service services/auth

+)en el archivo auth.service.ts el codigo queda de la siguiente manera:

import { Injectable } from '@angular/core';
import { AngularFireAuth } from '@angular/fire/auth';

import { User } from '../shared/user.class';

@Injectable({
  providedIn: 'root'
})
export class AuthService {

  
public isLogged: any = false;
constructor(public afAuth: AngularFireAuth) {
  afAuth.authState.subscribe(user => (this.isLogged = user));
}


// login
async onLogin(user: User) {
  try {
    return await this.afAuth.auth.signInWithEmailAndPassword(
      user.email,
      user.password);
  } catch (error) {
    console.log('Error on login', error);
  }
}

async onRegister(user: User) {
  try {
    return await this.afAuth.auth.createUserWithEmailAndPassword(
      user.email,
      user.password);
  } catch (error) {
    console.log('Error on register user', error);
  }
}
}

+)Crear formulario de login, en archivo home.page.html codificar lo siguiente:

<ion-header>
  <ion-toolbar color="secondary">
    <ion-title>Login</ion-title>
  </ion-toolbar>
</ion-header>

<ion-content padding>
  <ion-card>
    <ion-card-content>
      <ion-item>
        <ion-label>Email</ion-label>
      <ion-input
      type="text" [(ngModel)]="user.email"></ion-input>
      </ion-item>
      <ion-item>
        <ion-label>Password</ion-label>
      <ion-input
      type="password" [(ngModel)]="user.password"></ion-input>
      </ion-item>
      <ion-item-divider>
        <ion-button size="medium" (click)="onLogin()">Login</ion-button>
      </ion-item-divider>
      <ion-item-divider>
        <ion-button size="medium" routerLink="/register">Registrarse</ion-button>
      </ion-item-divider>
    </ion-card-content>
  </ion-card>
</ion-content>

+)En archivo home.page.ts codificar lo siguiente:

import { Component } from '@angular/core';
import { AuthService } from '../services/auth.service';
import { User } from './../shared/user.class';
import { Router } from '@angular/router';

@Component({
  selector: 'app-home',
  templateUrl: 'home.page.html',
  styleUrls: ['home.page.scss'],
})
export class HomePage {

  user: User = new User();
  constructor(
    private authSvc: AuthService,
    private router: Router) { }

  ngOnInit() {
  }

  async onLogin(){
    const user = await this.authSvc.onLogin(this.user);
    
    if(user){
      console.log('Usuario logeado');
      this.router.navigateByUrl('/weather');
    }
  }

}

+)Crear pagina de registro de usuarios:
Dentro del nuestro proyecto ionic abrir una terminal y ejecutar: ionic generate page register

+)Crear formulario de registro de usuario, en archivo register.page.html codificar lo siguiente:

<ion-header>
  <ion-toolbar color="secondary">
    <ion-title>Registro de Usuario</ion-title>
  </ion-toolbar>
</ion-header>

<ion-content padding>
  <ion-card>
    <ion-card-content>
      <ion-item>
        <ion-label>Email</ion-label>
      <ion-input
      type="text" [(ngModel)]="user.email"></ion-input>
      </ion-item>
      <ion-item>
        <ion-label>Password</ion-label>
      <ion-input
      type="password" [(ngModel)]="user.password"></ion-input>
      </ion-item>
      <ion-item-divider>
        <ion-button size="medium" (click)="onRegister()">Registro</ion-button>
      </ion-item-divider>
      <ion-item-divider>

        <ion-button size="medium" routerLink="/home">Login</ion-button>
      </ion-item-divider>
    </ion-card-content>
  </ion-card>
</ion-content>

+)En archivo register.page.ts:

import { Component, OnInit } from '@angular/core';
import { AuthService } from '../services/auth.service';
import { User } from './../shared/user.class';
import { Router } from '@angular/router'

@Component({
  selector: 'app-register',
  templateUrl: './register.page.html',
  styleUrls: ['./register.page.scss'],
})
export class RegisterPage implements OnInit {

  user: User = new User();
  constructor(
    private authSvc: AuthService,
    private router: Router
  ) { }

  ngOnInit() {
  }

  async onRegister(){
    const user = await this.authSvc.onRegister(this.user);
    
    if(user){
      console.log('Usuario creado');
      this.router.navigateByUrl('/weather');
    }
  }

}

+)Crear pagina donde se creara la aplicación de clima:
Dentro de nuestro proyecto ionic abrir terminal y ejecutar: ionic generate page weather

+)Descargar carpeta img de este repositorio y pegarla en la carpeta assets del proyecto ionic

+)Ejecutar la aplicación:
Dentro de la carpeta de nuestro proyecto ionic abrir terminal y ejecutar: ionic serve

El proyecto se mostrara en el localhost:8100

Con esto se tendra un login y registro de usuarios para acceder a la aplicación.











  
