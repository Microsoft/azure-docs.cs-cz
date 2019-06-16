---
title: Přidat a volání funkce Azure z Azure Logic Apps
description: Přidání a spuštění Azure functions z aplikací logiky
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.topic: article
ms.date: 06/04/2019
ms.reviewer: klam, LADocs
ms.openlocfilehash: 524b927ec0966199c51cdee93e920d7b847139ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66495124"
---
# <a name="call-azure-functions-from-azure-logic-apps"></a>Volání funkce Azure z Azure Logic Apps

Pokud chcete spustit kód, který provádí určitou úlohu ve svých aplikacích logiky, můžete vytvořit svoji vlastní funkci pomocí [Azure Functions](../azure-functions/functions-overview.md). Tato služba vám pomůže vytvořit Node.js s využitím C#, a F# funkcí, takže není nutné vytvářet kompletní aplikace nebo infrastrukturu pro spouštění kódu. Můžete také [volání aplikací logiky ze uvnitř Azure functions](#call-logic-app). Služba Azure Functions poskytuje architekturu bez serverů v cloudu a je užitečná při provádění úlohy, jako jsou tyto příklady:

* Rozšíření chování vaší aplikace logiky s funkcí v Node.js, nebo C#.
* Provádění výpočtů ve vaší aplikaci logiky.
* Pokročilé formátování nebo výpočetní pole ve svých aplikacích logiky.

Pokud chcete spouštět fragmenty kódu bez vytvoření funkce Azure, přečtěte si postup [přidání a spuštění vloženého kódu](../logic-apps/logic-apps-add-run-inline-code.md).

> [!NOTE]
> Integrace mezi Logic Apps a Azure Functions aktuálně nefunguje při využití slotů povolené.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace funkcí Azure, což je kontejner pro službu Azure functions, společně s Azure function. Pokud nemáte aplikaci function app, [nejprve vytvořit aplikaci function app](../azure-functions/functions-create-first-azure-function.md). Potom můžete vytvořit funkci buď mimo svou aplikaci logiky na webu Azure Portal, nebo [z ve svých aplikacích logiky](#create-function-designer) v návrháři aplikace logiky.

* Při práci s logic apps, platí stejné požadavky na aplikace function App a funkce stávající i nové:

  * Vaše aplikace function app a aplikaci logiky musí používat stejné předplatné Azure.

  * Nová aplikace function App, musíte použít jako zásobníku modulu runtime jazyka JavaScript nebo .NET. Při přidání nové funkce do existující aplikace function App, můžete buď vybrat C# nebo JavaScript.

  * Vaše funkce používá **triggeru HTTP** šablony.

    Šablonu triggeru HTTP může přijímat obsah, který má `application/json` typ vaší aplikace logiky. Když přidáte funkci Azure pro svou aplikaci logiky, Návrhář aplikace logiky ukazuje vlastní funkce, které jsou vytvořené z této šablony v rámci vašeho předplatného Azure.

  * Funkce nepoužívá vlastní trasy, pokud jste definovali [definice OpenAPI](../azure-functions/functions-openapi-definition.md) (dříve označované jako [soubor Swagger](https://swagger.io/)).

  * Pokud máte definici OpenAPI pro funkci, Návrhář pro Logic Apps nabízí bohatší prostředí při práci s parametry funkce. Předtím, než aplikace logiky můžete najít a přístup k funkcím, které mají definice OpenAPI [nastavení aplikace function app pomocí následujících kroků](#function-swagger).

* Aplikace logiky, ve které chcete přidat funkce, včetně [aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts) jako první krok ve své aplikaci logiky

  Před přidáním akce, které spouštět službu functions, aplikace logiky musí spouštět triggerem. Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý start: Vytvořte svou první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>Nalezení funkcí, které mají popisy OpenAPI

Pro pohodlnější a pestřejší prostředí při práci s parametry funkce v návrháři pro Logic Apps [vygeneruje definici rozhraní OpenAPI](../azure-functions/functions-openapi-definition.md), dříve označovaný jako [soubor Swagger](https://swagger.io/), pro funkce. Nastavení aplikace function app, aplikace logiky můžete najít a používat funkce, které mají Swagger popisy, postupujte podle těchto kroků:

1. Ujistěte se, že právě aktivně běží vaše aplikace function app.

1. V aplikaci function app nastavení [sdílení prostředků mezi zdroji (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) tak, aby všechny zdroje jsou povolené pomocí následujících kroků:

   1. Z **aplikace Function App** vyberte vaši aplikaci function app. V pravém podokně vyberte **funkce platformy** > **CORS**.

      ![Vyberte aplikaci funkcí > "Funkce" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. V části **CORS**, přidejte hvězdičku ( **`*`** ) zástupný znak, ale v seznamu odeberte všechny zdroje a zvolte **Uložit**.

      ![Nastavení "CORS * na zástupný znak" * "](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>Hodnoty vlastností přístup uvnitř požadavky HTTP

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

1. Teď přístup k funkci `date` vlastnosti prostřednictvím `data` proměnné a převod hodnoty vlastnosti z typu DateTime DateString zadejte voláním `ToDateString()` funkce. Funkce také vrátí výsledek prostřednictvím `body` v odezvě funkce:

   ```javascript
   body: data.date.ToDateString();
   ```

Teď, když jste vytvořili funkci Azure, postupujte podle kroků týkajících se [přidejte funkce logic Apps](#add-function-logic-app).

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Vytvoření funkce v logic apps

Předtím, než budete moct vytvořit funkci Azure od ve svých aplikacích logiky pomocí návrháře aplikace logiky, musíte nejprve mít aplikaci Azure function app, což je kontejner pro vaše funkce. Pokud nemáte aplikaci function app, nejprve vytvořte tuto aplikaci function app. Zobrazit [vytvoření první funkce na webu Azure Portal](../azure-functions/functions-create-first-azure-function.md).

1. V [webu Azure portal](https://portal.azure.com), otevřete v návrháři aplikace logiky aplikace logiky.

1. Vytvoření a přidání funkce, postupujte podle kroku, která se použije pro váš scénář:

   * Pod poslední krok v pracovním postupu vaší aplikace logiky zvolte **nový krok**.

   * Mezi stávající kroky v pracovním postupu vaší aplikace logiky, najeďte myší na šipku, zvolte na znaménko plus (+), přihlaste se a vyberte **přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "funkce azure". Ze seznamu akcí vyberte tuto akci: **Zvolte funkci Azure – Azure Functions**

   ![Vyhledejte "Funkce Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Ze seznamu aplikací funkce vyberte vaši aplikaci function app. Jakmile se otevře seznam akcí, vyberte tuto akci: **Vytvoření nové funkce Azure Functions –**

   ![Vyberte vaši aplikaci function app](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. V editoru definic funkcí Definujte funkci:

   1. V **název funkce** zadejte název vaší funkce.

   1. V **kód** pole, přidat kód do šablony funkce, včetně odpovědi a datová část, která chcete, aby vrátil do aplikace logiky po ukončení vaší funkce.

      ![Definovat funkci](./media/logic-apps-azure-functions/function-definition.png)

      V kódu šablony  *`context` objekt* odkazuje na zprávu, která vaše aplikace logiky odešle prostřednictvím **tělo požadavku** pole v pozdějším kroku. Pro přístup `context` objektu z vlastnosti uvnitř funkce, použijte následující syntaxi:

      `context.body.<property-name>`

      Například k odkazu `content` uvnitř vlastnosti `context` objektu, použijte následující syntaxi: 

      `context.body.content`

      Také obsahuje kód šablony `input` proměnnou, která ukládá hodnotu z `data` parametr tak vaší funkce můžete provádět operace s touto hodnotou. Uvnitř funkce jazyka JavaScript `data` proměnná je také klávesovou zkratku pro `context.body`.

      > [!NOTE]
      > `body` Zde vztahuje pouze `context` objekt a není stejná jako **tělo** token z akce výstup, který může být také předat do funkce.

   1. Jakmile budete hotoví, vyberte **Vytvořit**.

1. V **tělo požadavku** zadejte vaše funkce se uživatelovo zadání, které musí být naformátovaná jako objekt JavaScript Object Notation (JSON).

   Tento vstup je *objekt kontextu* nebo zprávy, která vaše aplikace logiky odesílá do vaší funkce. Když kliknete na **tělo požadavku** pole, seznamu dynamického obsahu se zobrazí, takže můžete vybrat tokeny pro výstupy z předchozích kroků. Tento příklad určuje, zda kontext datová část obsahuje vlastnost s názvem `content` , který má **z** token uživatele hodnotu z e-mailové aktivační události:

   !["Text žádosti" Příklad: datová část objektu kontextu](./media/logic-apps-azure-functions/function-request-body-example.png)

   Tady není objekt context přetypovat jako řetězec, proto získá obsah objektu přidat přímo do datové části JSON. Nicméně pokud objekt kontextu není token JSON, který se předává řetězec, objekt JSON nebo pole JSON, dojde k chybě. Takže pokud tento příklad používá **Received Time** token místo toho můžete přetypovat objekt context jako řetězec tak, že přidáte uvozovek:  

   ![Přetypování objektu jako řetězec](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Chcete-li zadat další podrobnosti, jako jsou metody, která používá, hlavičky požadavku nebo parametry dotazu, otevřete **přidat nový parametr** seznam a vyberte požadované možnosti.

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Přidat existující funkce logic Apps

Pokud chcete volat existující služba Azure functions z aplikace logiky, můžete přidat Azure functions stejně jako jakoukoli jinou akci v návrháři aplikace logiky.

1. V [webu Azure portal](https://portal.azure.com), otevřete v návrháři aplikace logiky aplikace logiky.

1. V části krok, ve které chcete přidat funkce, zvolte **nový krok**a vyberte **přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "funkce azure". Ze seznamu akcí vyberte tuto akci: **Zvolte funkci Azure – Azure Functions**

   ![Vyhledejte "Funkce Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Ze seznamu aplikací funkce vyberte vaši aplikaci function app. Jakmile se zobrazí seznam funkcí, vyberte vaši funkci.

   ![Vyberte aplikaci funkcí a funkci Azure](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Pro funkce, které mají definice rozhraní API (Swagger popisy) a jsou [nastavit tak, aby vaše aplikace logiky můžete najít a přístup k těmto funkcím](#function-swagger), můžete vybrat **akce Swaggeru**:

   ![Vyberte vaši aplikaci function app, "Swagger akce" "a funkce Azure](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. V **tělo požadavku** zadejte vaše funkce se uživatelovo zadání, které musí být naformátovaná jako objekt JavaScript Object Notation (JSON).

   Tento vstup je *objekt kontextu* nebo zprávy, která vaše aplikace logiky odesílá do vaší funkce. Když kliknete na **tělo požadavku** pole, seznamu dynamického obsahu se zobrazí, tak, že vyberete tokeny pro výstupy z předchozích kroků. Tento příklad určuje, zda kontext datová část obsahuje vlastnost s názvem `content` , který má **z** token uživatele hodnotu z e-mailové aktivační události:

   !["Text žádosti" Příklad: datová část objektu kontextu](./media/logic-apps-azure-functions/function-request-body-example.png)

   Tady není objekt context přetypovat jako řetězec, proto získá obsah objektu přidat přímo do datové části JSON. Nicméně pokud objekt kontextu není token JSON, který se předává řetězec, objekt JSON nebo pole JSON, dojde k chybě. Takže pokud tento příklad používá **Received Time** token místo toho můžete přetypovat objekt context jako řetězec tak, že přidáte uvozovek:

   ![Přetypování objektu jako řetězec](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Chcete-li zadat další podrobnosti, jako jsou metody, která používá, hlavičky požadavku nebo parametry dotazu, otevřete **přidat nový parametr** seznam a vyberte požadované možnosti.

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-azure-functions"></a>Volání aplikací logiky ze služby Azure functions

Pokud ji chcete spustit aplikaci logiky z uvnitř funkce Azure, aplikace logiky se musí spouštět s triggerem, který poskytuje volatelnou koncový bod. Například můžete spustit aplikaci logiky s **HTTP**, **žádosti**, **front Azure**, nebo **služby Event Grid** aktivační události. Uvnitř funkce Odeslat požadavek HTTP POST na adresu URL triggeru a zahrnout datovou část, chcete, aby tato aplikace logiky ke zpracování. Další informace najdete v tématu [volání, aktivační události, nebo vnořené aplikace logiky](../logic-apps/logic-apps-http-endpoint.md).

## <a name="next-steps"></a>Další postup

* Další informace o [konektory Logic Apps](../connectors/apis-list.md)
