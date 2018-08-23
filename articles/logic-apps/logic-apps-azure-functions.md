---
title: Přidání a spuštění vlastního kódu v Azure Logic Apps s využitím Azure Functions | Dokumentace Microsoftu
description: Zjistěte, jak přidat a spouštění vlastních fragmentech kódu v Azure Logic Apps s využitím Azure Functions
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 08/20/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: a63bd8e3b071ed996db8ad5aeaeb5e451b4d92e9
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "42058688"
---
# <a name="add-and-run-custom-code-snippets-in-azure-logic-apps-with-azure-functions"></a>Přidání a spuštění vlastních fragmentech kódu v Azure Logic Apps s využitím Azure Functions

Když chcete spustit pouze dostatek kód, který provádí určitou úlohu ve svých aplikacích logiky, můžete vytvořit vlastní funkce s [Azure Functions](../azure-functions/functions-overview.md). Tato služba vám pomůže vytvořit Node.js, C# a F # fragmenty kódu, nemusíte vytvářet kompletní aplikace nebo infrastrukturu pro spouštění kódu. Služba Azure Functions poskytuje architekturu bez serverů v cloudu a je užitečná při provádění úlohy, jako jsou tyto příklady:

* Rozšíření chování vaší aplikace logiky s funkcí v Node.js, nebo C#.
* Provádění výpočtů ve vaší aplikaci logiky.
* Pokročilé formátování nebo výpočetní pole ve svých aplikacích logiky.

Můžete také [volání aplikací logiky ze uvnitř Azure functions](#call-logic-app).

## <a name="prerequisites"></a>Požadavky

V tomto článku, budete potřebovat tyto položky:

* Pokud nemáte ještě předplatné Azure <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrovat si bezplatný účet Azure</a>. 

* Aplikace funkcí Azure, což je kontejner pro Azure functions a Azure function. Pokud nemáte aplikaci function app, [nejprve vytvořit aplikaci function app](../azure-functions/functions-create-first-azure-function.md). Potom můžete vytvořit funkci buď [samostatně mimo svou aplikaci logiky](#create-function-external), nebo [z ve svých aplikacích logiky](#create-function-designer) v návrháři aplikace logiky.

  Stávající i nové aplikace function App a funkce mají stejné požadavky pro práci s logic apps:

  * Aplikace function app musí mít stejné předplatné Azure jako svou aplikaci logiky.

  * Vaše funkce například používá aktivační událost HTTP, **triggeru HTTP** šablona funkce pro **JavaScript** nebo **jazyka C#**. 

    Šablonu triggeru HTTP může přijímat obsah, který má `application/json` typ vaší aplikace logiky. 
    Když přidáte funkci Azure pro svou aplikaci logiky, Návrhář aplikace logiky ukazuje vlastní funkce, které jsou vytvořené z této šablony v rámci vašeho předplatného Azure. 

  * Funkce nepoužívá vlastní trasy, pokud jste definovali [definice OpenAPI](../azure-functions/functions-openapi-definition.md), dříve označovaný jako [soubor Swagger](http://swagger.io/). 
  
  * Pokud jste definovali definice OpenAPI pro funkci, Návrhář pro Logic Apps poskytuje pohodlnější a pestřejší prostředí pro práci s parametry funkce. Předtím, než aplikace logiky můžete najít a přístup k funkcím, které mají definice OpenAPI [nastavení aplikace function app pomocí následujících kroků](#function-swagger).

* Aplikace logiky, ve které chcete přidat funkce, včetně [aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts) jako první krok ve své aplikaci logiky 

  Před přidáním akce, které můžete spouštět funkce, aplikace logiky musí spouštět triggerem.

  Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý start: vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-function-external"></a>

## <a name="create-functions-outside-logic-apps"></a>Funkce můžete vytvářet mimo logic apps

V <a href="https://portal.azure.com" target="_blank">webu Azure portal</a>, vytvořit aplikaci funkcí Azure, která musí mít stejné předplatné Azure jako svou aplikaci logiky a pak vytvořte funkci Azure.
Pokud začínáte vytváření Azure functions, přečtěte si postup [vytvoření první funkce na webu Azure Portal](../azure-functions/functions-create-first-azure-function.md), ale mějte na paměti tyto požadavky pro vytvoření funkce, které můžete volat z aplikace logiky:

* Ujistěte se, že jste vybrali **triggeru HTTP** šablona funkce pro buď **JavaScript** nebo **jazyka C#**.

  ![Trigger HTTP - JavaScript nebo C#](./media/logic-apps-azure-functions/http-trigger-function.png)

<a name="function-swagger"></a>

* Volitelně Pokud jste [vygenerovat definici rozhraní API](../azure-functions/functions-openapi-definition.md), dříve označovaný jako [soubor Swagger](http://swagger.io/), funkce, můžete získat pohodlnější a pestřejší prostředí při práci s parametry funkce v návrháři pro Logic Apps. Nastavení aplikace function app, aplikace logiky můžete najít a používat funkce, které mají Swagger popisy, postupujte podle těchto kroků:

  1. Ujistěte se, že právě aktivně běží vaše aplikace function app.

  2. V aplikaci function app nastavení [sdílení prostředků mezi zdroji (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) tak všechny zdroje jsou povolené pomocí následujících kroků:

     1. Z **aplikace Function App** vyberte vaši aplikaci function app > **funkce platformy** > **CORS**.

        ![Vyberte aplikaci funkcí > "Funkce" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

     2. V části **CORS**, přidejte `*` zástupný znak, ale v seznamu odeberte všechny zdroje a zvolte **Uložit**.

        ![Nastavení "CORS * na zástupný znak" * "](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

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

2. Vytvoření a přidání funkce, postupujte podle kroku, která se použije pro váš scénář:

   * Pod poslední krok v pracovním postupu vaší aplikace logiky zvolte **nový krok**.

   * Mezi stávající kroky v pracovním postupu vaší aplikace logiky, najeďte myší na šipku, zvolte na znaménko plus (+), přihlaste se a vyberte **přidat akci**.

3. Do vyhledávacího pole zadejte jako filtr "funkce azure".
Ze seznamu akcí vyberte tuto akci: **zvolte funkci Azure – Azure Functions** 

   ![Vyhledejte "Funkce Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. Ze seznamu aplikací funkce vyberte vaši aplikaci function app. Jakmile se otevře se seznam akcí, vyberte tuto akci: **Azure vytvořit novou funkci – funkce**

   ![Vyberte vaši aplikaci function app](./media/logic-apps-azure-functions/select-function-app-create-function.png)

5. V editoru definic funkcí Definujte funkci:

   1. V **název funkce** zadejte název vaší funkce. 

   2. V **kód** pole, přidat kód do šablony funkce, včetně odpovědi a chcete, aby datová část vrácená do aplikace logiky po ukončení vaší funkce. 

      ![Definovat funkci](./media/logic-apps-azure-functions/function-definition.png)

      V kódu šablony  *`context` objekt* odkazuje na zprávu, která vaše aplikace logiky odešle prostřednictvím **tělo požadavku** pole v pozdějším kroku. 
      Pro přístup `context` objektu z vlastnosti uvnitř funkce, použijte následující syntaxi: 

      `context.body.<property-name>`

      Například k odkazu `content` uvnitř vlastnosti `context` objektu, použijte následující syntaxi: 

      `context.body.content`

      Také obsahuje kód šablony `input` proměnnou, která ukládá hodnotu z `data` parametr tak vaší funkce můžete provádět operace s touto hodnotou. 
      Uvnitř funkce jazyka JavaScript `data` proměnná je také klávesovou zkratku pro `context.body`.

      > [!NOTE]
      > `body` Zde vztahuje pouze `context` objekt a není stejná jako **tělo** token z akce výstup, který může být také předat do funkce. 
 
   3. Jakmile budete hotoví, vyberte **Vytvořit**.

6. V **tělo požadavku** zadejte vaše funkce se uživatelovo zadání, které musí být naformátovaná jako objekt JavaScript Object Notation (JSON). 

   Tento vstup je *objekt kontextu* nebo zprávy, která vaše aplikace logiky odesílá do vaší funkce. Když kliknete na **tělo požadavku** pole, seznamu dynamického obsahu se zobrazí, takže můžete vybrat tokeny pro výstupy z předchozích kroků. Tento příklad určuje, zda kontext datová část obsahuje vlastnost s názvem `content` , který má **z** token uživatele hodnotu z e-mailové aktivační události:

   !["Text žádosti" Příklad: datová část objektu kontextu](./media/logic-apps-azure-functions/function-request-body-example.png)

   Tady není objekt context přetypovat jako řetězec, proto získá obsah objektu přidat přímo do datové části JSON. Nicméně pokud objekt kontextu není token JSON, který se předává řetězec, objekt JSON nebo pole JSON, dojde k chybě. Takže pokud tento příklad používá **Received Time** token místo toho můžete přetypovat objekt context jako řetězec tak, že přidáte uvozovek:  

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

   Pro funkce, které mají definice rozhraní API (Swagger popisy) a jsou [nastavit tak, aby vaše aplikace logiky můžete najít a přístup k těmto funkcím](#function-swagger), můžete vybrat **akce Swaggeru**:

   ![Vyberte vaši aplikaci function app, "Swagger akce" "a funkce Azure](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

5. V **tělo požadavku** zadejte vaše funkce se uživatelovo zadání, které musí být naformátovaná jako objekt JavaScript Object Notation (JSON). 

   Tento vstup je *objekt kontextu* nebo zprávy, která vaše aplikace logiky odesílá do vaší funkce. Když kliknete na **tělo požadavku** pole, seznamu dynamického obsahu se zobrazí, takže můžete vybrat tokeny pro výstupy z předchozích kroků. Tento příklad určuje, zda kontext datová část obsahuje vlastnost s názvem `content` , který má **z** token uživatele hodnotu z e-mailové aktivační události:

   !["Text žádosti" Příklad: datová část objektu kontextu](./media/logic-apps-azure-functions/function-request-body-example.png)

   Tady není objekt context přetypovat jako řetězec, proto získá obsah objektu přidat přímo do datové části JSON. Nicméně pokud objekt kontextu není token JSON, který se předává řetězec, objekt JSON nebo pole JSON, dojde k chybě. Takže pokud tento příklad používá **Received Time** token místo toho můžete přetypovat objekt context jako řetězec tak, že přidáte uvozovek: 

   ![Přetypování objektu jako řetězec](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

6. Chcete-li zadat další podrobnosti, jako jsou metody, která používá, hlavičky požadavku nebo parametry dotazu, zvolte **zobrazit pokročilé možnosti**.

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>Volání aplikací logiky z funkcí

Pokud ji chcete spustit aplikaci logiky z uvnitř funkce Azure, aplikace logiky se musí spouštět s triggerem, který poskytuje volatelnou koncový bod. Například můžete spustit aplikaci logiky s **HTTP**, **žádosti**, **front Azure**, nebo **služby Event Grid** aktivační události. Uvnitř funkce Odeslat požadavek HTTP POST na adresu URL triggeru a zahrnout datovou část, chcete, aby tato aplikace logiky ke zpracování. Další informace najdete v tématu [volání, aktivační události, nebo vnořené aplikace logiky](../logic-apps/logic-apps-http-endpoint.md). 

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o [konektory Logic Apps](../connectors/apis-list.md)
