---
title: Kurz k MongoDB, Angular a Node pro Azure – Část 6 | Dokumentace Microsoftu
description: Šestá část série kurzů týkající se vytvoření aplikace MongoDB s Angular a Node postavené na službě Azure Cosmos DB s použitím stejných rozhraní API, jako používáte pro MongoDB.
services: cosmos-db
author: johnpapa
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/17/2018
ms.author: jopapa
ms.custom: mvc
ms.openlocfilehash: a1705913e1656901d0a87a3cebb2eb69a6c7ad63
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43698583"
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-6-add-post-put-and-delete-functions-to-the-app"></a>Vytvoření aplikace MongoDB s Angular a službou Azure Cosmos DB – Část 6: Přidání funkcí Post, Put a Delete do aplikace

Tento vícedílný kurz ukazuje, jak vytvořit novou aplikaci [MongoDB API](mongodb-introduction.md) napsanou v Node.js s Express a Angular a jak ji připojit k databázi Azure Cosmos DB.

Šestá část kurzu vychází z [části 5](tutorial-develop-mongodb-nodejs-part5.md) a zabývá se následujícími úlohami:

> [!div class="checklist"]
> * Vytvoření funkcí Post, Put a Delete pro službu hero
> * Spuštění aplikace

> [!VIDEO https://www.youtube.com/embed/Y5mdAlFGZjc]

## <a name="prerequisites"></a>Požadavky

Před zahájením této části kurzu se ujistěte, že jste dokončili kroky v [části 5](tutorial-develop-mongodb-nodejs-part5.md) tohoto kurzu.

> [!TIP]
> Tento kurz vás postupně provede jednotlivými kroky vytvoření aplikace. Pokud si chcete stáhnout dokončený projekt, v [úložišti angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) na GitHubu můžete získat hotovou aplikaci.

## <a name="add-a-post-function-to-the-hero-service"></a>Přidání funkce Post do služby hero

1. Ve Visual Studio Code otevřete soubory **routes.js** a **hero.service.js** vedle sebe stisknutím tlačítka **Rozdělit editor** ![Tlačítko Rozdělit editor ve Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part6/split-editor-button.png).

    Podívejte se, že řádek 7 v souboru routes.js volá funkci `getHeroes` na řádku 5 v souboru **hero.service.js**.  Stejné párování potřebujeme vytvořit i pro funkce post, put a delete. 

    ![Soubory routes.js a hero.service.js ve Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part6/routes-heroservicejs.png)
    
    Začněme napsáním kódu pro službu hero. 

2. Zkopírujte následující kód do souboru **hero.service.js** za funkci `getHeroes` a před část `module.exports`. Tento kód:  
   * Použije model hero k odeslání nového hrdiny.
   * Zkontroluje, jestli nedošlo k chybě, a vrátí hodnotu stavu 500.

   ```javascript
   function postHero(req, res) {
     const originalHero = { uid: req.body.uid, name: req.body.name, saying: req.body.saying };
     const hero = new Hero(originalHero);
     hero.save(error => {
       if (checkServerError(res, error)) return;
       res.status(201).json(hero);
       console.log('Hero created successfully!');
     });
   }

   function checkServerError(res, error) {
     if (error) {
       res.status(500).send(error);
       return error;
     }
   }
   ```

3. V souboru **hero.service.js** aktualizujte část `module.exports` tak, aby zahrnovala funkci `postHero`. 

    ```javascript
    module.exports = {
      getHeroes,
      postHero
    };
    ```

4. V souboru **routes.js** za směrovač `get` přidejte směrovač pro funkci `post`. Tento směrovač odesílá hrdiny jednotlivě. Strukturování směrovacího souboru tímto způsobem jasně ukazuje všechny dostupné koncové body rozhraní API a skutečnou práci nechává na souboru **hero.service.js**.

    ```javascript
    router.post('/hero', (req, res) => {
      heroService.postHero(req, res);
    });
    ```

5. Spusťte aplikaci a zkontrolujte, že vše funguje. Ve Visual Studio Code uložte všechny provedené změny, klikněte na tlačítko **Ladit** ![Ikona Ladit ve Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part6/debug-button.png) na levé straně a pak klikněte na tlačítko **Spustit ladění** ![Ikona Spustit ladění ve Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part6/start-debugging-button.png).

6. Nyní přejděte zpět do internetového prohlížeče a stisknutím klávesy F12 (na většině počítačů) otevřete Vývojářské nástroje a kartu Síť. Přejděte na adresu [http://localhost:3000](http://localhost:3000) a sledujte prováděná volání přes síť.

    ![Karta Síť v prohlížeči Chrome ukazující aktivitu sítě](./media/tutorial-develop-mongodb-nodejs-part6/add-new-hero.png)

7. Přidejte nového hrdinu kliknutím na tlačítko **Add New Hero** (Přidat nového hrdinu). Zadejte ID 999, jméno (name) Fred a slavný výrok (saying) „Hello“ a pak klikněte na **Save** (Uložit). Na kartě Síť by se mělo zobrazit odeslání požadavku POST pro nového hrdinu. 

    ![Karta Síť v prohlížeči Chrome se zobrazenou aktivitou sítě pro funkce Get a Post](./media/tutorial-develop-mongodb-nodejs-part6/post-new-hero.png)

    Vraťme se zpět a přidejme do aplikace funkce Put a Delete.

## <a name="add-the-put-and-delete-functions"></a>Přidání funkcí Put a Delete

1. V souboru **routes.js** za směrovač post přidejte směrovače `put` a `delete`.

    ```javascript
    router.put('/hero/:uid', (req, res) => {
      heroService.putHero(req, res);
    });

    router.delete('/hero/:uid', (req, res) => {
      heroService.deleteHero(req, res);
    });
    ```

2. Zkopírujte následující kód do souboru **hero.service.js** za funkci `checkServerError`. Tento kód:
   * Vytvoří funkce `put` a `delete`.
   * Provede kontrolu, jestli se hrdina našel.
   * Provede zpracování chyb. 

   ```javascript
   function putHero(req, res) {
     const originalHero = {
       uid: parseInt(req.params.uid, 10),
       name: req.body.name,
       saying: req.body.saying
     };
     Hero.findOne({ uid: originalHero.uid }, (error, hero) => {
       if (checkServerError(res, error)) return;
       if (!checkFound(res, hero)) return;

      hero.name = originalHero.name;
       hero.saying = originalHero.saying;
       hero.save(error => {
         if (checkServerError(res, error)) return;
         res.status(200).json(hero);
         console.log('Hero updated successfully!');
       });
     });
   }

   function deleteHero(req, res) {
     const uid = parseInt(req.params.uid, 10);
     Hero.findOneAndRemove({ uid: uid })
       .then(hero => {
         if (!checkFound(res, hero)) return;
         res.status(200).json(hero);
         console.log('Hero deleted successfully!');
       })
       .catch(error => {
         if (checkServerError(res, error)) return;
       });
   }

   function checkFound(res, hero) {
     if (!hero) {
       res.status(404).send('Hero not found.');
       return;
     }
     return hero;
   }
   ```

3. V souboru **hero.service.js** exportujte nové moduly:

   ```javascript
    module.exports = {
      getHeroes,
      postHero,
      putHero,
      deleteHero
    };
    ```

4. Teď, když jste aktualizovali kód, klikněte ve Visual Studio Code na tlačítko **Restartovat** ![Tlačítko Restartovat ve Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part6/restart-debugger-button.png).

5. V internetovém prohlížeči aktualizujte stránku a klikněte na tlačítko **Add New Hero** (Přidat nového hrdinu). Přidejte nového hrdinu s ID 9, jménem (name) Starlord a slavným výrokem (saying) „Hi“. Kliknutím na tlačítko **Save** (Uložit) nového hrdinu uložte.

6. Nyní vyberte hrdinu **Starlord**, změňte slavný výrok (saying) z „Hi“ na „Bye“ a klikněte na tlačítko **Save** (Uložit). 

    Teď můžete na kartě Síť vybrat příslušné ID a zobrazit datovou část. V datové části se zobrazí, ze slavný výrok (saying) je teď nastavený na „Bye“.

    ![Aplikace Heroes a karta Síť se zobrazenou datovou částí](./media/tutorial-develop-mongodb-nodejs-part6/put-hero-function.png) 

    V uživatelském rozhraní také můžete některého z hrdinů odstranit a podívat se, jak dlouho dokončení operace odstranění trvá. Vyzkoušejte to kliknutím na tlačítko Delete (Odstranit) u hrdiny se jménem Fred.

    ![Aplikace Heroes a karta Síť se zobrazenými časy dokončení funkcí](./media/tutorial-develop-mongodb-nodejs-part6/times.png) 

    Pokud stránku aktualizujete, na kartě Síť se zobrazí čas potřebný k získání hrdinů. I když jsou tyto časy rychlé, hodně záleží na tom, kde na světě se vaše data nacházejí, a na vaší schopnosti geograficky je replikovat do oblastí blízko vašim uživatelům. Další informace o geografické replikaci najdete v dalším kurzu, který bude vydaný už brzy.

## <a name="next-steps"></a>Další kroky

V této části kurzu jste provedli následující:

> [!div class="checklist"]
> * Přidali jste do aplikace funkce Post, Put a Delete. 

Brzy se sem vraťte pro další videa v této sérii kurzů.

