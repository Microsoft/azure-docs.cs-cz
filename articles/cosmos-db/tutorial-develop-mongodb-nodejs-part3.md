---
title: Node.Js s využitím Angular aplikace pomocí rozhraní API MongoB (část3)
titleSuffix: Azure Cosmos DB
description: Třetí část série kurzů týkající se vytvoření aplikace MongoDB s Angular a Node postavené na službě Azure Cosmos DB s použitím stejných rozhraní API, jako používáte pro MongoDB.
author: johnpapa
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: jopapa
ms.custom: seodec18
ms.openlocfilehash: 97ebeeb11fcd77f200a6a7e6d66f2ebc542445cc
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542488"
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-3-build-the-ui-with-angular"></a>Vytvoření aplikace MongoDB s Angular a službou Azure Cosmos DB – část 3: Vytvoření uživatelského rozhraní pomocí Angular

Tento vícedílný kurz ukazuje, jak vytvořit novou [účet rozhraní API služby Azure Cosmos DB pro MongoDB](mongodb-introduction.md) aplikace napsané v Node.js s Express a Angular a pak ho připojíte k databázi Azure Cosmos DB.

Třetí část kurzu vychází z [části 2](tutorial-develop-mongodb-nodejs-part2.md) a zabývá se následujícími úlohami:

> [!div class="checklist"]
> * Vytvoření uživatelského rozhraní v Angular
> * Nastavení vzhledu a chování pomocí šablon stylů CSS
> * Místní testování aplikace

## <a name="video-walkthrough"></a>Video s návodem

> [!VIDEO https://www.youtube.com/embed/MnxHuqcJVoM]

## <a name="prerequisites"></a>Požadavky

Před zahájením této části kurzu se ujistěte, že jste dokončili kroky v [části 2](tutorial-develop-mongodb-nodejs-part2.md) tohoto kurzu.

> [!TIP]
> Tento kurz vás postupně provede jednotlivými kroky vytvoření aplikace. Pokud si chcete stáhnout dokončený projekt, v [úložišti angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) na GitHubu můžete získat hotovou aplikaci.

## <a name="build-the-ui"></a>Vytvoření uživatelského rozhraní

1. Ve Visual Studio Code kliknutím na tlačítko Zastavit ![Tlačítko Zastavit ve Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part3/stop-button.png) zastavte aplikaci Node.

2. V okně příkazového řádku ve Windows nebo v okně Terminálu na Macu zadejte následující příkaz, který vygeneruje komponentu heroes. V tomto kódu je g = generovat, c = komponenta, heroes = název komponenty. Kód používá strukturu plochých souborů (--flat), takže se pro komponentu nevytvoří podsložka.

    ```
    ng g c heroes --flat 
    ```

    V okně terminálu se zobrazí potvrzení nových komponent.

    ![Instalace komponenty hero](./media/tutorial-develop-mongodb-nodejs-part3/install-heros-component.png)

    Podívejme se na vytvořené a nahrané soubory. 

3. Ve Visual Studio Code v podokně **Průzkumník** přejděte do složky **src\app** a otevřete nový soubor **heroes.component.ts** generovaný do složky aplikace. Tento soubor komponenty TypeScript byl vytvořený předchozím příkazem.

    > [!TIP]
    > Pokud se složka aplikace ve Visual Studio Code nezobrazuje, stisknutím kombinace kláves CMD + SHIFT + P na Macu nebo Ctrl + Shift + P ve Windows otevřete Paletu příkazů a zadejte *Reload Window* (Znovu načíst okno) pro převzetí změny systému.

4. Ve stejné složce otevřete soubor **app.module.ts** a všimněte si, že na řádku 5 se importovala komponenta `HeroesComponent`, která se také přidala k deklaracím na řádku 10.

    ![Otevření souboru app.module.ts](./media/tutorial-develop-mongodb-nodejs-part3/app-module-file.png)

5. Přejděte zpět do souboru **heroes.component.html** a zkopírujte do něj tento kód. Element `<div>` je kontejner pro celou stránku. Uvnitř kontejneru je seznam hrdinů, který potřebujeme vytvořit tak, aby se hrdina po kliknutí vybral a bylo možné ho upravit nebo odstranit v uživatelském rozhraní. Dále v kódu HTML máme několik stylů, abyste věděli, který hrdina je vybraný. Je tam také oblast pro úpravy, abyste mohli přidat nového hrdinu nebo upravit existujícího. 

    ```html
    <div>
      <ul class="heroes">
        <li *ngFor="let hero of heroes" (click)="onSelect(hero)" [class.selected]="hero === selectedHero">
          <button class="delete-button" (click)="deleteHero(hero)">Delete</button>
          <div class="hero-element">
            <div class="badge">{{hero.id}}</div>
            <div class="name">{{hero.name}}</div>
            <div class="saying">{{hero.saying}}</div>
          </div>
        </li>
      </ul>
      <div class="editarea">
        <button (click)="enableAddMode()">Add New Hero</button>
        <div *ngIf="selectedHero">
          <div class="editfields">
            <div>
              <label>id: </label>
              <input [(ngModel)]="selectedHero.id" placeholder="id" *ngIf="addingHero" />
              <label *ngIf="!addingHero" class="value">{{selectedHero.id}}</label>
            </div>
            <div>
              <label>name: </label>
              <input [(ngModel)]="selectedHero.name" placeholder="name" />
            </div>
            <div>
              <label>saying: </label>
              <input [(ngModel)]="selectedHero.saying" placeholder="saying" />
            </div>
          </div>
          <button (click)="cancel()">Cancel</button>
          <button (click)="save()">Save</button>
        </div>
      </div>
    </div>
    ```

7. Když teď máme hotový kód HTML, potřebujeme ho přidat do souboru **heroes.component.ts**, abychom mohli s šablonou interagovat. Následující kód přidá šablonu do souboru komponenty. Přidal se konstruktor, který získá několik hrdinů a inicializuje komponentu služby hero, která získá všechna data. Tento kód také přidává potřebné metody pro zpracování událostí v uživatelském rozhraní. Následující kód můžete zkopírovat a nahradit jím stávající kód v souboru **heroes.component.ts**. Zobrazení chyb v oblastech Hero a HeroService je očekávané, protože odpovídající komponenty nejsou ještě naimportovány. Tyto chyby opravíte v další části. 

    ```ts
    import { Component, OnInit } from '@angular/core';

    @Component({
      selector: 'app-heroes',
      templateUrl: './heroes.component.html',
      styleUrls: ['./heroes.component.scss']
    })
    export class HeroesComponent implements OnInit {
      addingHero = false;
      heroes: any = [];
      selectedHero: Hero;
    
      constructor(private heroService: HeroService) {}
    
      ngOnInit() {
       this.getHeroes();
      }

      cancel() {
        this.addingHero = false;
        this.selectedHero = null;
      }

      deleteHero(hero: Hero) {
        this.heroService.deleteHero(hero).subscribe(res => {
          this.heroes = this.heroes.filter(h => h !== hero);
          if (this.selectedHero === hero) {
            this.selectedHero = null;
          }
        });
      }

      getHeroes() {
        return this.heroService.getHeroes().subscribe(heroes => {
          this.heroes = heroes;
        });
      }

      enableAddMode() {
        this.addingHero = true;
        this.selectedHero = new Hero();
      }

      onSelect(hero: Hero) {
        this.addingHero = false;
        this.selectedHero = hero;
      }

      save() {
        if (this.addingHero) {
          this.heroService.addHero(this.selectedHero).subscribe(hero => {
            this.addingHero = false;
            this.selectedHero = null;
            this.heroes.push(hero);
          });
        } else {
          this.heroService.updateHero(this.selectedHero).subscribe(hero => {
            this.addingHero = false;
            this.selectedHero = null;
          });
        }
      }
    }
    ```

8. V **Průzkumníku** otevřete soubor **app/app.module.ts** a aktualizujte sekci imports pro přidání importu modulu `FormsModule`. Sekce importu by teď měla vypadat následovně:

    ```
    imports: [
      BrowserModule,
      FormsModule
    ],
    ```

9. V souboru **app/app.module.ts** přidejte na řádku 3 import nového modulu FormsModule. 

    ```
    import { BrowserModule } from '@angular/platform-browser';
    import { NgModule } from '@angular/core';
    import { FormsModule } from '@angular/forms';
    ```

## <a name="use-css-to-set-the-look-and-feel"></a>Nastavení vzhledu a chování pomocí šablon stylů CSS

1. V podokně Průzkumník otevřete soubor **src/styles.scss**.

2. Do souboru **styles.scss** zkopírujte následující kód a nahraďte jím stávající obsah souboru.

    ```css
    /* You can add global styles to this file, and also import other style files */

    * {
      font-family: Arial;
    }
    h2 {
      color: #444;
      font-weight: lighter;
    }
    body {
      margin: 2em;
    }
    
    body,
    input[text],
    button {
      color: #888;
      // font-family: Cambria, Georgia;
    }
    button {
      font-size: 14px;
      font-family: Arial;
      background-color: #eee;
      border: none;
      padding: 5px 10px;
      border-radius: 4px;
      cursor: pointer;
      cursor: hand;
      &:hover {
        background-color: #cfd8dc;
      }
      &.delete-button {
        float: right;
        background-color: gray !important;
        background-color: rgb(216, 59, 1) !important;
        color: white;
        padding: 4px;
        position: relative;
        font-size: 12px;
      }
    }
    div {
      margin: .1em;
    }

    .selected {
      background-color: #cfd8dc !important;
      background-color: rgb(0, 120, 215) !important;
      color: white;
    }

    .heroes {
      float: left;
      margin: 0 0 2em 0;
      list-style-type: none;
      padding: 0;
      li {
        cursor: pointer;
        position: relative;
        left: 0;
        background-color: #eee;
        margin: .5em;
        padding: .5em;
        height: 3.0em;
        border-radius: 4px;
        width: 17em;
        &:hover {
          color: #607d8b;
          color: rgb(0, 120, 215);
          background-color: #ddd;
          left: .1em;
        }
        &.selected:hover {
          /*background-color: #BBD8DC !important;*/
          color: white;
        }
      }
      .text {
        position: relative;
        top: -3px;
      }
      .saying {
        margin: 5px 0;
      }
      .name {
        font-weight: bold;
      }
      .badge {
        /* display: inline-block; */
        float: left;
        font-size: small;
        color: white;
        padding: 0.7em 0.7em 0 0.5em;
        background-color: #607d8b;
        background-color: rgb(0, 120, 215);
        background-color:rgb(134, 183, 221);
        line-height: 1em;
        position: relative;
        left: -1px;
        top: -4px;
        height: 3.0em;
        margin-right: .8em;
        border-radius: 4px 0 0 4px;
        width: 1.2em;
      }
    }

    .header-bar {
      background-color: rgb(0, 120, 215);
      height: 4px;
      margin-top: 10px;
      margin-bottom: 10px;
    }

    label {
      display: inline-block;
      width: 4em;
      margin: .5em 0;
      color: #888;
      &.value {
        margin-left: 10px;
        font-size: 14px;
      }
    }

    input {
      height: 2em;
      font-size: 1em;
      padding-left: .4em;
      &::placeholder {
          color: lightgray;
          font-weight: normal;
          font-size: 12px;
          letter-spacing: 3px;
      }
    }

    .editarea {
      float: left;
      input {
        margin: 4px;
        height: 20px;
        color: rgb(0, 120, 215);
      }
      button {
        margin: 8px;
      }
      .editfields {
        margin-left: 12px;
      }
    }
    ``` 
3. Uložte soubor. 

## <a name="display-the-component"></a>Zobrazení komponenty

Když teď máme komponentu, jak ji zobrazíme na obrazovce? Pojďme upravit výchozí komponenty v souboru **app.component.ts**.

1. V podokně Průzkumník otevřete soubor **/app/app.component.ts**, změňte nadpis na Heroes a pak přidejte název komponenty, kterou jsme vytvořili v souboru **heroes.components.ts** (app-heroes) pro přidání reference na tuto novou komponentu. Obsah souboru by teď měl vypadat takto: 

    ```ts
    import { Component } from '@angular/core';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.scss'],
      template: `
      <h1>Heroes</h1>
      <div class="header-bar"></div>
      <app-heroes></app-heroes>
    `
    })
    export class AppComponent {
      title = 'app';
    }

    ```

2. V souboru **heroes.components.ts** jsou další komponenty, na které odkazujeme, například komponenta Hero, takže potřebujeme vytvořit i ty. Na příkazovém řádku Angular CLI vytvořte model hero a soubor **hero.ts** pomocí následujícího příkazu, kde g = generovat, cl = třída a hero = název třídy.

    ```bash
    ng g cl hero
    ```

3. V podokně Průzkumník otevřete soubor **src\app\hero.ts**. V souboru **hero.ts** nahraďte obsah následujícím kódem, který přidá třídu Hero s ID, jménem (name) a slavným výrokem (saying).

    ```ts
      export class Hero {
      id: number;
      name: string;
      saying: string;
    }
    ```

4. Přejděte zpět do souboru **heroes.components.ts** a všimněte si, že na řádku `selectedHero: Hero;` (řádek 10) má `Hero` červené podtržení. 

5. Klikněte na výraz `Hero` a Visual Studio Code na levé straně bloku kódu zobrazí ikonu žárovky. 

    ![Žárovka ve Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part3/light-bulb.png)

6. Klikněte na žárovku a pak klikněte na **Importovat Hero z umístění /app/hero**. nebo **Importovat Hero z umístění ./hero**. (Zpráva se mění v závislosti na vašem nastavení.)

    Na řádku 2 se objeví nový řádek kódu. Pokud řádek 2 odkazuje na /app/hero, upravte ho tak, aby odkazoval na soubor hero v místní složce (./hero). Řádek 2 by měl vypadat takto:

   ```
   import { Hero } from "./hero";
   ``` 

    Tím je postaráno o model, pořád ale potřebujeme vytvořit službu.

## <a name="create-the-service"></a>Vytvoření služby

1. Na příkazovém řádku Angular CLI vytvořte v souboru **app.module.ts** službu hero zadáním následujícího příkazu, kde g = generovat, s = služba, hero = název služby a příznak -m značí vložení do souboru app.module.

    ```bash
    ng g s hero -m app.module
    ```

2. Ve Visual Studio Code přejděte zpět do souboru **heroes.components.ts**. Všimněte si, že na řádku `constructor(private heroService: HeroService) {}` (řádek 13) má `HeroService` červené podtržení. Klikněte na `HeroService` a na levé straně bloku kódu se zobrazí žárovka. Klikněte na žárovku a pak klikněte na **Importovat HeroService z umístění ./hero.service**. nebo **Importovat HeroService z umístění /app/hero.service**.

    Kliknutím na žárovku se vloží nový řádek kódu na řádku 2. Pokud řádek 2 odkazuje na složku /app/hero.service, upravte ho tak, aby odkazoval na soubor hero v místní složce (./hero.service). Řádek 2 by měl vypadat takto:
    
    ```javascript
    import { HeroService } from "./hero.service"
    ```

3. Ve Visual Studio Code otevřete soubor **hero.service.ts** a zkopírujte do něj následující kód, kterým nahradíte obsah souboru.

    ```ts
    import { Injectable } from '@angular/core';
    import { HttpClient } from '@angular/common/http';
    
    import { Hero } from './hero';
    
    const api = '/api';

    @Injectable()
    export class HeroService {
      constructor(private http: HttpClient) {}

      getHeroes() {
        return this.http.get<Array<Hero>>(`${api}/heroes`)
      }

      deleteHero(hero: Hero) {
        return this.http.delete(`${api}/hero/${hero.id}`);
      }

      addHero(hero: Hero) {
        return this.http.post<Hero>(`${api}/hero/`, hero);
      }

      updateHero(hero: Hero) {
        return this.http.put<Hero>(`${api}/hero/${hero.id}`, hero);
      }
    }
    ```

    Tento kód používá nejnovější verzi modulu HttpClient, kterou Angular nabízí. Tento modul je potřeba zadat, takže to teď provedeme.

4. Ve Visual Studio Code otevřete soubor **app.module.ts** a importujte modul HttpClientModule tak, že aktualizujete část importování, aby zahrnovala HttpClientModule.

    ```ts
    imports: [
      BrowserModule,
      FormsModule,
      HttpClientModule
    ],
    ```

5. V souboru **app.module.ts** přidejte do seznamu k importování příkaz pro import modulu HttpClientModule.

    ```ts
    import { HttpClientModule } from '@angular/common/http';
    ```

6. Uložte všechny soubory ve Visual Studio Code.

## <a name="build-the-app"></a>Sestavení aplikace

1. Na příkazovém řádku zadejte následující příkaz, který sestaví aplikaci Angular. 

    ```bash
    ng b
    ``` 

    Pokud dojde k nějakým problémům, v okně terminálu se zobrazí informace o souborech, které potřebují opravu. Po dokončení sestavení se nové soubory přesunou do složky **dist**. Pokud chcete, můžete nové soubory ve složce **dist** zkontrolovat.

    Teď aplikaci spustíme.

2. Ve Visual Studio Code klikněte na tlačítko **Ladit** ![Ikona Ladit ve Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png) na levé straně a pak klikněte na tlačítko **Spustit ladění** ![Ikona Spustit ladění ve Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part3/start-debugging-button.png).

3. Nyní otevřete internetový prohlížeč a přejděte na adresu **localhost:3000**, kde se zobrazí místně spuštěná aplikace.

     ![Místně spuštěná aplikace Heroes](./media/tutorial-develop-mongodb-nodejs-part3/azure-cosmos-db-mongodb-mean-app.png)

## <a name="next-steps"></a>Další postup

V této části kurzu jste provedli následující:

> [!div class="checklist"]
> * Vytvořili jste uživatelské rozhraní v Angular.
> * Místně jste otestovali aplikaci.

Můžete přejít k další části kurzu a vytvořit účet služby Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Vytvoření účtu služby Azure Cosmos DB pomocí Azure CLI](tutorial-develop-mongodb-nodejs-part4.md)
