---
title: Vytvoření aplikace Node.js Express s rozhraním API Azure Cosmos DB pro MongoDB (Část2)
description: Druhá část série kurzů týkající se vytvoření aplikace MongoDB s Angular a Node postavené na službě Azure Cosmos DB s použitím stejných rozhraní API, jako používáte pro MongoDB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: e3af713d6e120e66a0cde5116f4556bb8caedf55
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097840"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---create-a-nodejs-express-app"></a>Vytvoření úhlové aplikace s rozhraním API Azure Cosmos DB pro MongoDB – vytvoření aplikace Node.js Express
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

V tomto výukovém kurzu se dozvíte, jak vytvořit novou aplikaci napsanou v Node.js pomocí Expressu a úhlu a pak ji připojit k [účtu Cosmos nakonfigurovanému pomocí rozhraní API Cosmos DB pro MongoDB](mongodb-introduction.md).

Druhá část kurzu vychází z [úvodu](tutorial-develop-mongodb-nodejs.md) a zabývá se následujícími úlohami:

> [!div class="checklist"]
> * Instalace Angular CLI a jazyka TypeScript
> * Vytvoření nového projektu pomocí Angular
> * Sestavení aplikace pomocí rozhraní Express
> * Testování aplikace v nástroji Postman

## <a name="video-walkthrough"></a>Video s návodem

> [!VIDEO https://www.youtube.com/embed/lIwJIYcGSUg]

## <a name="prerequisites"></a>Předpoklady

Před zahájením této části kurzu se nezapomeňte podívat na [úvodní video](tutorial-develop-mongodb-nodejs.md).

V tomto kurzu budete také potřebovat: 
* [Node.js](https://nodejs.org/) verze 8.4.0 nebo vyšší.
* [Postman](https://www.getpostman.com/)
* [Visual Studio Code](https://code.visualstudio.com/) nebo váš oblíbený editor kódu.

> [!TIP]
> Tento kurz vás postupně provede jednotlivými kroky vytvoření aplikace. Pokud si chcete stáhnout dokončený projekt, v [úložišti angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) na GitHubu můžete získat hotovou aplikaci.

## <a name="install-the-angular-cli-and-typescript"></a>Instalace Angular CLI a jazyka TypeScript

1. Otevřete okno příkazového řádku ve Windows nebo okno Terminálu na Macu a nainstalujte Angular CLI.

    ```bash
    npm install -g @angular/cli
    ```

2. Nainstalujte TypeScript zadáním následujícího příkazu na příkazovém řádku. 

    ```bash
    npm install -g typescript
    ```

## <a name="use-the-angular-cli-to-create-a-new-project"></a>Vytvoření nového projektu pomocí Angular CLI

1. Na příkazovém řádku přejděte do složky, ve které chcete vytvořit nový projekt, a spusťte následující příkaz. Tento příkaz vytvoří novou složku a projekt angular-cosmosdb a nainstaluje komponenty Angular požadované pro novou aplikaci. Použije minimální instalaci (--minimal) a určí, že projekt používá Sass, což je syntax podobná šablonám stylů CSS (pomocí příznaku --style scss).

    ```bash
    ng new angular-cosmosdb --minimal --style scss
    ```

2. Po dokončení příkazu změňte adresář na složku src/client.

    ```bash
    cd angular-cosmosdb
    ```

3. Potom tuto složku otevřete ve Visual Studio Code.

    ```bash
    code .
    ```

## <a name="build-the-app-using-the-express-framework"></a>Sestavení aplikace pomocí rozhraní Express

1. Ve Visual Studio Code v podokně **Průzkumník** klikněte pravým tlačítkem na složku **src** , klikněte na **Nová složka** a pojmenujte novou složku *server* .

2. V podokně **Průzkumník** klikněte pravým tlačítkem na složku **server** , klikněte na **Nový soubor** a pojmenujte nový soubor *index.js* .

3. Zpět na příkazovém řádku pomocí následujícího příkazu nainstalujte analyzátor textu. Ten pomáhá aplikaci parsovat data JSON předávaná prostřednictvím rozhraní API.

    ```bash
    npm i express body-parser --save
    ```

4. Ve Visual Studio Code zkopírujte následující kód do souboru index.js. Tento kód:
    * Odkazuje na Express.
    * Přetáhne analyzátor textu pro čtení dat JSON v textech žádostí.
    * Použije integrovanou funkci path.
    * Nastaví kořenové proměnné pro snadnější hledání kódu.
    * Nastaví port.
    * Nastartuje Express.
    * Sdělí aplikaci, jak používat middleware, který budeme používat k obsluze serveru.
    * Obsluhuje všechno ve složce dist, což bude statický obsah.
    * Obsluhuje aplikaci a na každý požadavek GET, který se nenajde na serveru (pro přímé odkazy), vrátí soubor index.html.
    * Spustí server pomocí funkce app.listen.
    * Pomocí funkce šipky zaznamená, že je port aktivní.
    
   ```node
   const express = require('express');
   const bodyParser = require('body-parser');
   const path = require('path');
   const routes = require('./routes');

   const root = './';
   const port = process.env.PORT || '3000';
   const app = express();

   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(express.static(path.join(root, 'dist/angular-cosmosdb')));
   app.use('/api', routes);
   app.get('*', (req, res) => {
     res.sendFile('dist/angular-cosmosdb/index.html', {root});
   });

   app.listen(port, () => console.log(`API running on localhost:${port}`));
   ```

5. Ve Visual Studio Code v podokně **Průzkumník** klikněte pravým tlačítkem na složku **server** a pak klikněte na **Nový soubor** . Pojmenujte nový soubor *routes.js* . 

6. Do souboru **routes.js** zkopírujte následující kód. Tento kód:
   * Přidá referenci na směrovač Express.
   * Získá hrdiny.
   * Odešle zpět JSON pro definovaného hrdinu.

   ```node
   const express = require('express');
   const router = express.Router();

   router.get('/heroes', (req, res) => {
    res.send(200, [
       {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    ])
   });

   module.exports=router;
   ```

7. Uložte všechny upravené soubory. 

8. V Visual Studio Code klikněte na tlačítko **ladit** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png"::: a pak klikněte na tlačítko ozubeného kolečka :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/gear-button.png"::: . Ve Visual Studio Code se otevře nový soubor launch.js. 

8. Na řádku 11 souboru launch.json změňte `"${workspaceFolder}\\server"` na `"program": "${workspaceRoot}/src/server/index.js"` a uložte soubor.

9. Kliknutím na tlačítko **Spustit ladění** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/start-debugging-button.png"::: Spusťte aplikaci.

    Aplikace by se měla spustit bez chyb.

## <a name="use-postman-to-test-the-app"></a>Testování aplikace pomocí nástroje Postman

1. Teď otevřete nástroj Postman a do pole GET vložte `http://localhost:3000/api/heroes`. 

2. Klikněte na tlačítko **Send** (Odeslat) a získejte odpověď aplikace ve formátu JSON. 

    Tato odpověď značí, že aplikace funguje a je spuštěná v místním prostředí. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part2/azure-cosmos-db-postman.png" alt-text="Postman se zobrazeným požadavkem a odpovědí":::


## <a name="next-steps"></a>Další kroky

V této části kurzu jste provedli následující:

> [!div class="checklist"]
> * Vytvořili jste projekt Node.js pomocí Angular CLI.
> * Otestovali jste aplikaci pomocí nástroje Postman.

Můžete přejít k další části kurzu a vytvořit uživatelské rozhraní.

> [!div class="nextstepaction"]
> [Vytvoření uživatelského rozhraní pomocí Angular](tutorial-develop-mongodb-nodejs-part3.md)
