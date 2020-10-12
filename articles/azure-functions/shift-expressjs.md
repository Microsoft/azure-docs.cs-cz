---
title: Přesunutí z Express.js do Azure Functions
description: Naučte se Refaktorovat Express.js koncové body do Azure Functions.
author: craigshoemaker
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: cshoe
ms.openlocfilehash: 266df5371ff5f47526fa9d6567c62e31d51ebb05
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87810220"
---
# <a name="shifting-from-expressjs-to-azure-functions"></a>Přesunutí z Express.js do Azure Functions

Express.js je jedním z nejoblíbenějších Node.js architektur pro webové vývojáře a zůstává vynikající volbou pro vytváření aplikací, které slouží koncovým bodům rozhraní API.

Při migraci kódu na architekturu bez serveru ovlivňuje refaktoring Express.js koncové body následující oblasti:

- **Middleware**: Express.js obsahuje robustní kolekci middlewaru. Mnoho modulů middlewaru již není vyžadováno na základě Azure Functions a možností [API Management Azure](../api-management/api-management-key-concepts.md) . Před migrací koncových bodů je nutné replikovat nebo nahradit všechny logiky, které jsou zpracovány základním middlewarem middleware.

- **Různá rozhraní API**: rozhraní API, které se používá ke zpracování požadavků a odpovědí, se liší mezi Azure Functions a Express.js. Následující příklad podrobně popisuje požadované změny.

- **Výchozí trasa**: ve výchozím nastavení jsou koncové body Azure Functions zpřístupněny v rámci `api` trasy. Pravidla směrování lze konfigurovat prostřednictvím [ `routePrefix` _host.js_ v souboru](./functions-bindings-http-webhook-output.md#hostjson-settings).

- **Konfigurace a konvence**: aplikace Functions používá soubor _function.json_ k definování příkazů http, definování zásad zabezpečení a může nakonfigurovat [vstup a výstup](./functions-triggers-bindings.md)funkce. Ve výchozím nastavení název složky, která obsahuje soubory funkcí, definuje název koncového bodu, ale název můžete změnit prostřednictvím `route` vlastnosti v [function.js](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) v souboru.

> [!TIP]
> Přečtěte si další informace v interaktivním kurzu [refaktoring Node.js a Express rozhraní API k rozhraním API bez serveru pomocí Azure Functions](/learn/modules/shift-nodejs-express-apis-serverless/).

## <a name="example"></a>Příklad

### <a name="expressjs"></a>Express.js

Následující příklad ukazuje typický `GET` koncový bod Express.js.

```javascript
// server.js
app.get('/hello', (req, res) => {
  try {
    res.send("Success!");
  } catch(error) {
    const err = JSON.stringify(error);
    res.status(500).send(`Request error. ${err}`);
  }
});
```

Při `GET` odeslání požadavku na se `/hello` `HTTP 200` vrátí odpověď obsahující výjimku `Success` . Pokud koncový bod nalezne chybu, odpověď se zobrazí v `HTTP 500` podrobnostech o chybě.

### <a name="azure-functions"></a>Azure Functions

Azure Functions uspořádá soubory konfigurace a kódu do jediné složky pro každou funkci. Ve výchozím nastavení název složky Určuje název funkce.

Například funkce s názvem `hello` má složku s následujícími soubory.

``` files
| - hello
|  - function.json
|  - index.js
```

Následující příklad implementuje stejný výsledek jako výše uvedený Express.js koncový bod, ale s Azure Functions.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// hello/index.js
module.exports = async function (context, req) {
  try {
    context.res = { body: "Success!" };
  } catch(error) {
    const err = JSON.stringify(error);
    context.res = {
      status: 500,
      body: `Request error. ${err}`
    };
  }
};
```

# <a name="typescript"></a>[TypeScript](#tab/typescript)

```typescript
// hello/index.ts
import { AzureFunction, Context, HttpRequest } from "@azure/functions";

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
  try {
    context.res = { body: "Success!" };
  } catch (error) {
    const err = JSON.stringify(error);
    context.res = {
      status: 500,
      body: `Request error. ${err}`,
    };
  }
};

export default httpTrigger;
```

---

Při přesunu do funkcí jsou provedeny následující změny:

- **Modul:** Kód funkce je implementován jako modul JavaScriptu.

- **Objekt kontextu a odpovědi**: [`context`](./functions-reference-node.md#context-object) umožňuje komunikaci s modulem runtime funkce. Z kontextu můžete číst data žádosti a nastavit odpověď funkce. Synchronní kód vyžaduje, abyste volali `context.done()` k dokončení provádění, zatímco `asyc` funkce vyřeší požadavek implicitně.

- **Zásady vytváření názvů**: název složky, který obsahuje soubory Azure Functions, se ve výchozím nastavení používá jako název koncového bodu (dá se přepsat v [function.json](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint)).

- **Konfigurace**: v [function.jsv](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) souboru definujte příkazy HTTP, například `POST` nebo `PUT` .

Následující _function.jsv_ souboru obsahuje informace o konfiguraci funkce.

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

Definováním `get` v poli `methods` je funkce k dispozici pro požadavky HTTP `GET` . Pokud chcete, aby vaše rozhraní API přijímalo `POST` žádosti o podporu, můžete `post` také přidat do pole.

## <a name="next-steps"></a>Další kroky

- Další informace o interaktivním kurzu [refaktoring Node.js a expresní rozhraní API k rozhraním API bez serveru pomocí Azure Functions](/learn/modules/shift-nodejs-express-apis-serverless/)