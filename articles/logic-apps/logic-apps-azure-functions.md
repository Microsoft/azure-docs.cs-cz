---
title: Přidání a spuštění vlastního kódu v Azure Logic Apps s využitím Azure Functions | Dokumentace Microsoftu
description: Zjistěte, jak přidat a spouštění vlastních fragmentech kódu v Azure Logic Apps s využitím Azure Functions
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/25/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 20ad738541554279ff9fd6dd6babe90a38676c00
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263186"
---
# <a name="add-and-run-custom-code-snippets-in-azure-logic-apps-with-azure-functions"></a>Přidání a spuštění vlastních fragmentech kódu v Azure Logic Apps s využitím Azure Functions

Pokud chcete vytvořit a spustit pouze dostatek kód, který řeší konkrétní problém ve svých aplikacích logiky, můžete vytvořit vaše vlastní funkce pomocí [Azure Functions](../azure-functions/functions-overview.md). Tato služba poskytuje funkce pro vytváření a spouštění vlastních fragmentech kódu napsané v Node.js nebo C# ve svých aplikacích logiky, ale nemusíme se starat o vytváření celé aplikace nebo infrastrukturu pro spouštění kódu. Služba Azure Functions poskytuje architekturu bez serverů v cloudu a je užitečná při provádění úlohy, jako jsou tyto příklady:

* Rozšíření chování vaší aplikace logiky s využitím functions podporuje Node.js, nebo C#.
* Provádění výpočtů ve vaší aplikaci logiky.
* Pokročilé formátování nebo výpočetní pole ve svých aplikacích logiky.

Můžete také [volání aplikací logiky ze uvnitř Azure functions](#call-logic-app).

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle tohoto článku, tady jsou položky, které potřebujete:

* Pokud nemáte ještě předplatné Azure <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrovat si bezplatný účet Azure</a>. 

* Aplikace logiky, ve které chcete přidat funkci

  Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý start: vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* A [aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts) jako první krok ve své aplikaci logiky 

  Před přidáním akce spouštění služby functions, aplikace logiky musí spouštět triggerem.

* Aplikace funkcí Azure, což je kontejner pro Azure functions a Azure function. Pokud nemáte aplikaci function app, je nutné [nejprve vytvořit aplikaci function app](../azure-functions/functions-create-first-azure-function.md). Potom můžete vytvořit funkci buď [samostatně mimo svou aplikaci logiky](#create-function-external), nebo [z ve svých aplikacích logiky](#create-function-designer) v návrháři aplikace logiky.

  Nové i stávající Azure function apps a functions mají stejné požadavky pro práci s aplikací logiky:

  * Aplikace function app musí patřit do stejného předplatného Azure jako svou aplikaci logiky.

  * Musíte použít funkci **obecný webhook** šablona funkce pro buď **JavaScript** nebo **jazyka C#**. Tato šablona může přijímat obsah, který má `application/json` typ vaší aplikace logiky. Tyto šablony také pomoci najít a zobrazit vlastní funkce, které vytvoříte s těmito šablonami, při přidání těchto funkcí logic Apps návrhář aplikace logiky.

  * Zkontrolujte, že šablona funkce **režimu** je nastavena na **Webhooku** a **typu Webhooku** je nastavena na **obecný JSON**.

    1. Přihlaste se k <a href="https://portal.azure.com" target="_blank">portálu Azure</a>.
    2. V hlavní nabídce Azure zvolte **aplikace Function App**. 
    3. V **aplikace Function App** vyberte vaši aplikaci function app, rozbalte vaši funkci a vyberte **integrace**. 
    4. Zkontrolujte vaše šablona **režimu** je nastavena na **Webhooku** a že **typu Webhooku** je nastavena na **obecný JSON**. 

  * Pokud má vaše funkce [definice rozhraní API](../azure-functions/functions-openapi-definition.md), dříve označovaný jako [soubor Swagger](http://swagger.io/), Návrhář pro Logic Apps poskytuje pohodlnější a pestřejší prostředí pro práci s parametry funkce. 
  Předtím, než aplikace logiky můžete najít a přístup k funkcím, které mají Swagger popisy [nastavení aplikace function app pomocí následujících kroků](#function-swagger).

<a name="create-function-external"></a>

## <a name="create-functions-outside-logic-apps"></a>Funkce můžete vytvářet mimo logic apps

V <a href="https://portal.azure.com" target="_blank">webu Azure portal</a>, vytvořit aplikaci funkcí Azure, která musí mít stejné předplatné Azure jako svou aplikaci logiky a pak vytvořte funkci Azure. Pokud do služby Azure Functions začínáte, přečtěte si postup [vytvoření první funkce na webu Azure Portal](../azure-functions/functions-create-first-azure-function.md), ale mějte na paměti tyto požadavky pro vytvoření funkce Azure, které můžete přidat a volat z aplikací logiky.

* Ujistěte se, že jste vybrali **obecný webhook** šablona funkce pro buď **JavaScript** nebo **jazyka C#**.

  ![Obecný webhook – JavaScript nebo C#](./media/logic-apps-azure-functions/generic-webhook.png)

* Po vytvoření funkce Azure, zkontrolujte, že šablony **režimu** a **typu Webhooku** jsou správně nastaveny vlastnosti.

  1. V **aplikace Function App** seznamu, rozbalte funkci a vyberte **integrace**. 

  2. Zkontroluje, jestli vaše šablony **režimu** je nastavena na **Webhooku** a že **typu Webhooku** je nastavena na **obecný JSON**. 

     ![Vlastnosti šablony funkce "Integrace"](./media/logic-apps-azure-functions/function-integrate-properties.png)

<a name="function-swagger"></a>

* Volitelně Pokud jste [vygenerovat definici rozhraní API](../azure-functions/functions-openapi-definition.md), dříve označovaný jako [soubor Swagger](http://swagger.io/), funkce, můžete získat pohodlnější a pestřejší prostředí při práci s parametry funkce v návrháři pro Logic Apps. Nastavení aplikace function app, aplikace logiky můžete najít a přístup k funkcím, které mají popisy Swaggeru:

  * Ujistěte se, že právě aktivně běží vaše aplikace function app.

  * V aplikaci function app nastavení [sdílení prostředků mezi zdroji (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) tak, že jsou povolené všechny zdroje:

    1. Počínaje **aplikace Function App** vyberte vaši aplikaci function app > **funkce platformy** > **CORS**.

       ![Vyberte aplikaci funkcí > "Funkce" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

    2. V části **CORS**, přidejte `*` zástupný znak, ale v seznamu odeberte všechny zdroje a zvolte **Uložit**.

       ![Vyberte aplikaci funkcí > "Funkce" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

### <a name="access-property-values-inside-http-requests"></a>Hodnoty vlastností přístup uvnitř požadavky HTTP

Funkce Webhooku můžete přijímat požadavky HTTP jako vstupy a předat tyto žádosti do dalších funkcí. Například i když má Logic Apps [funkce, které provádějí převod hodnoty data a času](../logic-apps/workflow-definition-language-functions-reference.md), tato funkce JavaScriptu základní ukázka ukazuje, jak přistupovat k vlastnosti uvnitř žádost o objekt, který je předán do funkce a provádění operací na Hodnota této vlastnosti. Pro přístup k vlastnosti uvnitř objekty, v tomto příkladu [tečka (.)](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors): 

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

Zde je, co se stane, že v této funkci:

1. Vytvoří funkci `data` proměnné a přiřadí `body` objektu `request` objektu na tuto proměnnou. Funkce pomocí operátoru tečka (.) tak, aby odkazovaly `body` objektu `request` objektu: 

   ```javascript
   var data = request.body;
   ```

2. Teď přístup k funkci `date` vlastnosti prostřednictvím `data` proměnné a převod hodnoty vlastnosti z typu DateTime DateString zadejte voláním `ToDateString()` funkce. Funkce také vrátí výsledek prostřednictvím `body` v odezvě funkce: 

   ```javascript
   body: data.date.ToDateString();
   ```

Teď, když jste vytvořili funkci Azure, postupujte podle kroků týkajících se [přidejte funkce logic Apps](#add-function-logic-app).

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Vytvoření funkce v logic apps

Předtím, než budete moct vytvořit funkci Azure od ve svých aplikacích logiky v návrháři aplikace logiky, musíte nejprve mít aplikaci Azure function app, což je kontejner pro vaše funkce. Pokud nemáte aplikaci function app, nejprve vytvořte tuto aplikaci function app. Zobrazit [vytvoření první funkce na webu Azure Portal](../azure-functions/functions-create-first-azure-function.md). 

1. V <a href="https://portal.azure.com" target="_blank">webu Azure portal</a>, otevřete v návrháři aplikace logiky aplikace logiky. 

2. V části krok, kde chcete vytvořit a přidat funkci, zvolte **nový krok** > **přidat akci**. 

3. Do vyhledávacího pole zadejte jako filtr "funkce azure".
Ze seznamu akcí vyberte tuto akci: **zvolte funkci Azure – Azure Functions** 

   ![Vyhledejte "Funkce Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. Ze seznamu aplikací funkce vyberte vaši aplikaci function app. Jakmile se otevře se seznam akcí, vyberte tuto akci: **Azure vytvořit novou funkci – funkce**

   ![Vyberte vaši aplikaci function app](./media/logic-apps-azure-functions/select-function-app-create-function.png)

5. V editoru definic funkcí Definujte funkci:

   1. V **název funkce** zadejte název vaší funkce. 

   2. V **kód** pole, přidat kód vaší funkce do šablony, včetně odpovědi a chcete, aby datová část vrácená do aplikace logiky po ukončení vaší funkce. 
   Objekt kontextu v kódu šablony popisuje zprávy a obsah, který vaše aplikace logiky předává do funkce, například:

      ![Definovat funkci](./media/logic-apps-azure-functions/function-definition.png)

      Uvnitř funkce můžete odkazovat vlastnosti v objektu context pomocí následující syntaxe:

      ```text
      context.<token-name>.<property-name>
      ```
      Například tady je syntaxe, které byste použili:

      ```text
      context.body.content
      ```

   3. Jakmile budete hotoví, vyberte **Vytvořit**.

6. V **tělo požadavku** zadejte objekt context předat jako vstup funkce, které musí být ve formátu v zápisu JSON (JavaScript Object). Když kliknete na **tělo požadavku** pole, seznamu dynamického obsahu se otevře, takže tokeny pro vlastnosti, které jsou k dispozici můžete vybrat z předchozích kroků. 

   Tento příklad předává objekt v **tělo** token e-mailové aktivační události:  

   !["Text žádosti" Příklad: datová část objektu kontextu](./media/logic-apps-azure-functions/function-request-body-example.png)

   Návrhář aplikace logiky založené na obsahu v objektu context, vygeneruje šablonu funkce, můžete následně upravit vložená. 
   Logic Apps vytvoří také založené na objektu kontextu vstupní proměnné.

   V tomto příkladu není objekt context přetypovat jako řetězec, proto získá obsah přidat přímo do datové části JSON. 
   Nicméně pokud objekt není token JSON, který musí být řetězec, objekt JSON nebo pole JSON, dojde k chybě. 
   K přetypování objekt context jako řetězec, přidáte dvojité uvozovky, například:

   ![Přetypování objektu jako řetězec](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

7. Chcete-li zadat další podrobnosti, jako jsou metody, která používá, hlavičky požadavku nebo parametry dotazu, zvolte **zobrazit pokročilé možnosti**.

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Přidat existující funkce logic Apps

Pokud chcete volat existující služba Azure functions z aplikace logiky, můžete přidat Azure functions stejně jako jakoukoli jinou akci v návrháři aplikace logiky. 

1. V <a href="https://portal.azure.com" target="_blank">webu Azure portal</a>, otevřete v návrháři aplikace logiky aplikace logiky. 

2. V části krok, ve které chcete přidat funkce, zvolte **nový krok** > **přidat akci**. 

3. Do vyhledávacího pole zadejte jako filtr "funkce azure".
Ze seznamu akcí vyberte tuto akci: **zvolte funkci Azure – Azure Functions** 

   ![Vyhledejte "Funkce Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. Ze seznamu aplikací funkce vyberte vaši aplikaci function app. Jakmile se zobrazí seznam funkcí, vyberte vaši funkci. 

   ![Vyberte aplikaci funkcí a funkci Azure](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Pro funkce, které mají definice rozhraní API (Swagger popisy) a které jsou [nastavit tak, aby vaše aplikace logiky můžete najít a přístup k těmto funkcím](#function-swagger), můžete vybrat **akce Swaggeru**:

   ![Vyberte vaši aplikaci function app, "Swagger akce" "a funkce Azure](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

5. V **tělo požadavku** zadejte objekt context předat jako vstup funkce, které musí být ve formátu v zápisu JSON (JavaScript Object). Tento objekt kontextu popisuje zprávy a obsah, který vaše aplikace logiky odešle do vaší funkce. 

   Když kliknete na **tělo požadavku** pole, seznamu dynamického obsahu se otevře, takže tokeny pro vlastnosti, které jsou k dispozici můžete vybrat z předchozích kroků. 
   Tento příklad předává objekt v **tělo** token e-mailové aktivační události:

   !["Text žádosti" Příklad: datová část objektu kontextu](./media/logic-apps-azure-functions/function-request-body-example.png)

   V tomto příkladu není objekt context přetypovat jako řetězec, proto získá obsah přidat přímo do datové části JSON. 
   Nicméně pokud objekt není token JSON, který musí být řetězec, objekt JSON nebo pole JSON, dojde k chybě. 
   K přetypování objekt context jako řetězec, přidáte dvojité uvozovky, například:

   ![Přetypování objektu jako řetězec](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

6. Chcete-li zadat další podrobnosti, jako jsou metody, která používá, hlavičky požadavku nebo parametry dotazu, zvolte **zobrazit pokročilé možnosti**.

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>Volání aplikací logiky z funkcí

Spustit aplikaci logiky z uvnitř funkce Azure, musí mít tato aplikace logiky volatelných koncového bodu, nebo přesněji řečeno, **žádosti** aktivační události. Z uvnitř funkce, odešlete požadavek HTTP POST na adresu URL pro daný **požadavek** aktivovat a zahrnout datovou část, chcete, aby tato aplikace logiky ke zpracování. Další informace najdete v tématu [volání, aktivační události, nebo vnořené aplikace logiky](../logic-apps/logic-apps-http-endpoint.md). 

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o [konektory Logic Apps](../connectors/apis-list.md)
