---
title: Referenční dokumentace pro vývojáře JavaScriptu pro Azure Functions
description: Naučte se vyvíjet funkce pomocí JavaScriptu.
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: conceptual
ms.date: 07/17/2020
ms.custom: devx-track-js
ms.openlocfilehash: 5b9ffdec83fb613b7df0b5a3227ca66c55e54fe9
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422548"
---
# <a name="azure-functions-javascript-developer-guide"></a>Azure Functions příručka pro vývojáře JavaScriptu

Tato příručka obsahuje podrobné informace, které vám pomůžou s úspěšným vývojem Azure Functions pomocí JavaScriptu.

Jako Express.js, Node.js nebo vývojář JavaScriptu, pokud jste Azure Functions, je třeba si nejdřív přečíst jeden z následujících článků:

| Začínáme | Koncepty| Učení s asistencí |
| -- | -- | -- | 
| <ul><li>[Node.js funkce pomocí Visual Studio Code](./create-first-function-vs-code-node.md)</li><li>[Node.js funkce pomocí terminálu/příkazového řádku](./create-first-function-cli-java.md)</li></ul> | <ul><li>[Příručka pro vývojáře](functions-reference.md)</li><li>[Možnosti hostování](functions-scale.md)</li><li>[Funkce TypeScriptu](#typescript)</li><li>[Požadavky na výkon &nbsp;](functions-best-practices.md)</li></ul> | <ul><li>[Vytváření bezserverových aplikací](/learn/paths/create-serverless-applications/)</li><li>[Refaktoring rozhraní API bez serveru Node.js a Express](/learn/modules/shift-nodejs-express-apis-serverless/)</li></ul> |

## <a name="javascript-function-basics"></a>Základy funkcí JavaScriptu

Funkce JavaScriptu (Node.js) je export `function` , který se spustí, když se aktivuje ([triggery se konfigurují v function.json](functions-triggers-bindings.md)). První argument předaný do každé funkce je `context` objekt, který se používá pro příjem a odesílání dat vazby, protokolování a komunikaci s modulem runtime.

## <a name="folder-structure"></a>Struktura složek

Požadovaná struktura složky pro projekt JavaScriptu vypadá následovně. Toto výchozí nastavení lze změnit. Další informace najdete v části [scriptFile](#using-scriptfile) níže.

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
```

V kořenu projektu je k dispozici sdílený [host.jspro](functions-host-json.md) soubor, který lze použít ke konfiguraci aplikace Function App. Každá funkce má složku s vlastním souborem kódu (. js) a konfiguračním souborem vazby (function.json). Název `function.json` nadřazeného adresáře je vždy název vaší funkce.

Rozšíření vazby požadovaná ve [verzi 2. x](functions-versions.md) modulu runtime Functions jsou definována v souboru se `extensions.csproj` skutečnými soubory knihoven ve `bin` složce. Při vývoji místně je nutné [zaregistrovat rozšíření vazby](./functions-bindings-register.md#extension-bundles). Při vývoji funkcí v Azure Portal se tato registrace provede za vás.

## <a name="exporting-a-function"></a>Export funkce

Funkce JavaScriptu musí být exportovány prostřednictvím [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (nebo [`exports`](https://nodejs.org/api/modules.html#modules_exports) ). Vyexportovaná funkce by měla být funkce JavaScriptu, která se spustí, když se aktivuje.

Ve výchozím nastavení vyhledává modul runtime Functions funkci v `index.js` , kde `index.js` sdílí stejný nadřazený adresář jako odpovídající `function.json` . Ve výchozím nastavení by vaše exportovaná funkce měla být jediným exportem z jeho souboru nebo export s názvem `run` nebo `index` . Chcete-li nakonfigurovat umístění souboru a název exportu funkce, přečtěte si informace o [konfiguraci vstupního bodu funkce](functions-reference-node.md#configure-function-entry-point) níže.

Vaše exportovaná funkce je předána počtem argumentů při provádění. První argument, který bude trvat, je vždy `context` objekt. Pokud je funkce synchronní (nevrací příslib), je nutné předat `context` objekt, protože volání `context.done` je vyžadováno pro správné použití.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Export asynchronní funkce
Při použití [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklarace nebo prostého JavaScriptu [příslibů](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) ve verzi 2. x modulu runtime Functions není nutné explicitně volat [`context.done`](#contextdone-method) zpětné volání k signalizaci, že byla funkce dokončena. Vaše funkce se dokončí po dokončení exportované asynchronní funkce nebo příslib. V případě funkcí cílících na modul runtime verze 1. x je nutné i nadále volat, [`context.done`](#contextdone-method) když je proveden provádění kódu.

Následující příklad představuje jednoduchou funkci, která protokoluje, že byla aktivována, a okamžitě dokončí provádění.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Při exportu asynchronní funkce můžete také nakonfigurovat výstupní vazbu, aby se hodnota vybrala `return` . To se doporučuje, pokud máte jenom jednu výstupní vazbu.

Chcete-li přiřadit výstup pomocí `return` , změňte `name` vlastnost na hodnotu `$return` v `function.json` .

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

V takovém případě by měla funkce vypadat jako v následujícím příkladu:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>Vazby 
V jazyce JavaScript jsou [vazby](functions-triggers-bindings.md) konfigurovány a definovány ve function.jsfunkce na. Funkce pracují s vazbami různými způsoby.

### <a name="inputs"></a>Vstupy
Vstup je rozdělen do dvou kategorií v Azure Functions: jeden je vstup triggeru a druhý je další vstup. Trigger a další vstupní vazby (vazby `direction === "in"` ) mohou být čteny funkcí třemi způsoby:
 - **_[Doporučeno]_ Jako parametry předané do vaší funkce.** Jsou předány do funkce ve stejném pořadí, v jakém jsou definovány v *function.js*. `name`Vlastnost definovaná v *function.js* se nemusí shodovat s názvem vašeho parametru, i když by měla být.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Jako členové [`context.bindings`](#contextbindings-property) objektu.** Každý člen je pojmenován `name` vlastností definovanými v *function.js*.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **Jako vstupy pomocí objektu jazyka JavaScript [`arguments`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments) .** To je v podstatě totéž jako předání vstupů jako parametrů, ale umožňuje dynamicky zpracovávat vstupy.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Výstupy
Výstupy (vazby `direction === "out"` ) mohou být do funkce zapisovány pomocí několika způsobů. Ve všech případech `name` vlastnost vazby, jak je definováno v *function.js* , odpovídá názvu člena objektu zapsaného do funkce. 

Do výstupních vazeb můžete přiřadit data jedním z následujících způsobů (tyto metody nekombinovat):

- **_[Doporučeno pro více výstupů]_ Vrácení objektu.** Pokud používáte funkci vracející asynchronní nebo příslib, můžete vrátit objekt s přiřazenými výstupními daty. V následujícím příkladu jsou výstupní vazby pojmenovány "httpResponse" a "queueOutput" v *function.jsna*.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  Používáte-li synchronní funkci, můžete tento objekt vrátit pomocí [`context.done`](#contextdone-method) (viz příklad).
- **_[Doporučeno pro jeden výstup]_ Vrácení hodnoty přímo a pomocí názvu $return vazby.** To funguje jenom pro funkce asynchronního nebo příslibu. Viz příklad při [Exportování asynchronní funkce](#exporting-an-async-function). 
- **Přiřazení hodnot k `context.bindings`** Hodnoty můžete přiřadit přímo kontextu. Bindings.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>Datový typ vazeb

Chcete-li definovat datový typ pro vstupní vazbu, použijte `dataType` vlastnost v definici vazby. Chcete-li například číst obsah požadavku HTTP v binárním formátu, použijte typ `binary` :

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Možnosti pro `dataType` jsou: `binary` , `stream` a `string` .

## <a name="context-object"></a>kontextový objekt

Modul runtime používá `context` objekt k předávání dat do a z funkce a modulu runtime. Slouží ke čtení a nastavení dat z vazeb a pro zápis do protokolů `context` je objekt vždy prvním parametrem předaným funkci.

U funkcí, které obsahují synchronní kód, objekt context obsahuje `done` zpětné volání, které je voláno při zpracování funkce. Explicitní volání `done` není nutné při psaní asynchronního kódu; `done` zpětné volání se volá implicitně.

```javascript
module.exports = (context) => {

    // function logic goes here

    context.log("The function has executed.");

    context.done();
};
```

Kontext předaný do funkce zpřístupňuje `executionContext` vlastnost, což je objekt s následujícími vlastnostmi:

| Název vlastnosti  | Typ  | Popis |
|---------|---------|---------|
| `invocationId` | Řetězec | Poskytuje jedinečný identifikátor pro konkrétní vyvolání funkce. |
| `functionName` | Řetězec | Poskytuje název běžící funkce. |
| `functionDirectory` | Řetězec | Poskytuje adresář App Functions. |

Následující příklad ukazuje, jak vrátit `invocationId` .

```javascript
module.exports = (context, req) => {
    context.res = {
        body: context.executionContext.invocationId
    };
    context.done();
};
```

### <a name="contextbindings-property"></a>Context. Bindings – vlastnost

```js
context.bindings
```

Vrátí pojmenovaný objekt, který se používá ke čtení nebo přiřazení dat vazby. Data vazby vstupu a triggeru se dají použít při čtení vlastností na `context.bindings` . Data vazby výstupu lze přiřadit přidáním dat do `context.bindings`

Například následující definice vazeb ve vaší function.jsv nástroji umožňují přístup k obsahu fronty z `context.bindings.myInput` a přiřazování výstupů do fronty pomocí `context.bindings.myOutput` .

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

Můžete zvolit definování výstupních dat vazby pomocí `context.done` metody místo `context.binding` objektu (viz níže).

### <a name="contextbindingdata-property"></a>Context. bindingData – vlastnost

```js
context.bindingData
```

Vrátí pojmenovaný objekt, který obsahuje metadata triggeru a data vyvolání funkce ( `invocationId` , `sys.methodName` , `sys.utcNow` , `sys.randGuid` ). Příklad metadat triggeru najdete v tomto [příkladu centra událostí](functions-bindings-event-hubs-trigger.md).

### <a name="contextdone-method"></a>Context. hotový – metoda

```js
context.done([err],[propertyBag])
```

Umožňuje modulu runtime zjistit, že váš kód byl dokončen. Pokud funkce používá [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklaraci, nemusíte používat `context.done()` . `context.done`Zpětné volání je implicitně voláno. Asynchronní funkce jsou k dispozici v uzlu 8 nebo novější verzi, která vyžaduje verzi 2. x modulu runtime Functions.

Není-li funkce asynchronní funkcí, je **nutné zavolat** , `context.done` aby bylo možné informovat modul runtime o dokončení funkce. Vypršel časový limit spuštění, pokud chybí.

`context.done`Metoda umožňuje předat zpět do modulu runtime chybu definovanou uživatelem a objekt JSON obsahující výstupní data vazby. Vlastnosti předané pro `context.done` přepsání jsou nastaveny u `context.bindings` objektu.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>Context. log – metoda  

```js
context.log(message)
```

Umožňuje zapisovat do protokolů funkcí streamování na výchozí úrovni trasování s dostupnými dalšími úrovněmi protokolování. Protokolování trasování je podrobně popsáno v následující části. 

## <a name="write-trace-output-to-logs"></a>Zapsat výstup trasování do protokolů

Ve funkcích použijte `context.log` metody pro zápis výstupu trasování do protokolů a konzoly. Když zavoláte `context.log()` , zpráva se zapíše do protokolů na výchozí úrovni trasování, což je úroveň trasování _informací_ . Funkce se integrují s Azure Application Insights pro lepší zachycení protokolů aplikací Function App. Application Insights, součást Azure Monitor, poskytuje zařízení pro shromažďování, vizuální vykreslování a analýzu telemetrie aplikací i výstupů trasování. Další informace najdete v tématu [monitorování Azure Functions](functions-monitoring.md).

Následující příklad zapíše protokol na úrovni trasování informací, včetně ID vyvolání:

```javascript
context.log("Something has happened. " + context.invocationId); 
```

Všechny `context.log` metody podporují stejný formát parametru, který je podporován [metodou Node.js util. Format](https://nodejs.org/api/util.html#util_util_format_format). Vezměte v úvahu následující kód, který zapisuje protokoly funkcí pomocí výchozí úrovně trasování:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Stejný kód můžete také napsat v následujícím formátu:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

> [!NOTE]  
> Nepoužívejte `console.log` k zápisu výstupů trasování. Vzhledem k tomu, že výstup z `console.log` je zachycen na úrovni aplikace Function App, není svázán s konkrétním voláním funkce a není zobrazen v protokolech konkrétní funkce. Verze 1. x modulu runtime Functions navíc nepodporuje použití `console.log` k zápisu do konzoly.

### <a name="trace-levels"></a>Úrovně trasování

Kromě výchozí úrovně jsou k dispozici následující metody protokolování, které umožňují psát protokoly funkcí na konkrétní úrovně trasování.

| Metoda                 | Popis                                |
| ---------------------- | ------------------------------------------ |
| **Chyba ( _zpráva_ )**   | Zapíše událost na úrovni chyby do protokolů.   |
| **upozornit ( _zpráva_ )**    | Zapíše událost úrovně upozornění do protokolů. |
| **informace ( _zpráva_ )**    | Provede zápis do protokolování na úrovni informací nebo nižší.    |
| **verbose ( _zpráva_ )** | Zapisuje do protokolování na úrovni podrobností.           |

Následující příklad zapíše stejný protokol na úrovni trasování upozornění místo úrovně info:

```javascript
context.log.warn("Something has happened. " + context.invocationId); 
```

Vzhledem k tomu, že _Chyba_ je nejvyšší úroveň trasování, toto trasování se zapisuje do výstupu na všech úrovních trasování, pokud je povoleno protokolování.

### <a name="configure-the-trace-level-for-logging"></a>Konfigurace úrovně trasování pro protokolování

Funkce umožňují definovat mezní úroveň trasování pro zápis do protokolů nebo do konzoly nástroje. Konkrétní nastavení prahové hodnoty závisí na vaší verzi modulu runtime Functions.

# <a name="v2x"></a>[v2. x +](#tab/v2)

Chcete-li nastavit prahovou hodnotu pro trasování zapsané do protokolů, použijte `logging.logLevel` vlastnost v souboru host.js. Tento objekt JSON umožňuje definovat výchozí prahovou hodnotu pro všechny funkce ve vaší aplikaci Function App a můžete definovat konkrétní prahové hodnoty pro jednotlivé funkce. Další informace najdete v tématu [Postup konfigurace monitorování pro Azure Functions](configure-monitoring.md).

# <a name="v1x"></a>[V1. x](#tab/v1)

Chcete-li nastavit prahovou hodnotu pro všechna trasování zapsaná do protokolů a konzolu, použijte `tracing.consoleLevel` vlastnost v souboru host.js. Toto nastavení platí pro všechny funkce aplikace Function App. Následující příklad nastaví prahovou hodnotu trasování pro povolení podrobného protokolování:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

Hodnoty **consoleLevel** odpovídají názvům `context.log` metod. Chcete-li zakázat veškeré protokolování trasování do konzoly, nastavte **consoleLevel** na _off_. Další informace najdete v tématu [host.jsv odkazu v1. x](functions-host-json-v1.md).

---

### <a name="log-custom-telemetry"></a>Vlastní telemetrie protokolu

Ve výchozím nastavení funkce zapisuje výstup jako trasování do Application Insights. Pro lepší kontrolu můžete místo toho použít [sadu SDK Application Insights Node.js](https://github.com/microsoft/applicationinsights-node.js) k posílání vlastních dat telemetrie do instance služby Application Insights. 

# <a name="v2x"></a>[v2. x +](#tab/v2)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.traceContext.traceparent};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

# <a name="v1x"></a>[V1. x](#tab/v1)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.operationId};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

---

`tagOverrides`Parametr nastaví `operation_Id` ID vyvolání funkce. Toto nastavení umožňuje korelovat všechny automaticky vygenerované a vlastní telemetrie pro dané volání funkce.

## <a name="http-triggers-and-bindings"></a>Aktivační události a vazby HTTP

Aktivační události HTTP a Webhooku a výstupní vazby HTTP používají objekty žádosti a odpovědi, které reprezentují zprávy HTTP.  

### <a name="request-object"></a>Request – objekt

`context.req`Objekt (Request) má následující vlastnosti:

| Vlastnost      | Popis                                                    |
| ------------- | -------------------------------------------------------------- |
| _těles_        | Objekt, který obsahuje tělo žádosti.               |
| _záhlaví_     | Objekt, který obsahuje hlavičky požadavku.                   |
| _Metoda_      | Metoda HTTP požadavku.                                |
| _originalUrl_ | Adresa URL požadavku                                        |
| _params_      | Objekt, který obsahuje parametry směrování požadavku. |
| _zadávání_       | Objekt, který obsahuje parametry dotazu.                  |
| _rawBody_     | Tělo zprávy jako řetězec.                           |


### <a name="response-object"></a>Objekt odpovědi

`context.res`Objekt (Response) má následující vlastnosti:

| Vlastnost  | Popis                                               |
| --------- | --------------------------------------------------------- |
| _těles_    | Objekt, který obsahuje tělo odpovědi.         |
| _záhlaví_ | Objekt, který obsahuje hlavičky odpovědi.             |
| _isRaw_   | Indikuje, že pro odpověď se přeskočilo formátování.    |
| _stav_  | Stavový kód protokolu HTTP odpovědi.                     |
| _soubory cookie_ | Pole objektů cookie protokolu HTTP, které jsou nastaveny v odpovědi. Objekt souborů cookie protokolu HTTP má `name` , `value` a další vlastnosti souboru cookie, jako je například `maxAge` nebo `sameSite` . |

### <a name="accessing-the-request-and-response"></a>Přístup k žádosti a odpovědi 

Když pracujete s triggery HTTP, můžete získat přístup k objektům požadavků HTTP a odpovědí několika způsoby:

+ **Od `req` a `res` vlastnosti `context` objektu.** Tímto způsobem můžete použít vzor konvenční pro přístup k datům HTTP z objektu Context namísto použití úplného `context.bindings.name` vzoru. Následující příklad ukazuje, jak získat přístup k `req` `res` objektům a v `context` :

    ```javascript
    // You can access your HTTP request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your HTTP response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Z pojmenované vstupní a výstupní vazby.** Tímto způsobem fungují triggery a vazby HTTP stejně jako jakékoli jiné vazby. Následující příklad nastaví objekt Response pomocí pojmenované `response` vazby: 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ **_[Pouze odpověď]_ Voláním `context.res.send(body?: any)` .** Je vytvořena odpověď protokolu HTTP se vstupem `body` jako text odpovědi. `context.done()` je implicitně volána.

+ **_[Pouze odpověď]_ Voláním `context.done()` .** Speciální typ vazby HTTP vrátí odpověď, která je předána `context.done()` metodě. Následující výstupní vazba protokolu HTTP definuje `$return` výstupní parametr:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="scaling-and-concurrency"></a>Škálování a souběžnost

Ve výchozím nastavení Azure Functions automaticky monitoruje zatížení aplikace a v případě potřeby vytvoří další instance hostitelů pro Node.js. Funkce používá předdefinované (neuživatelsky konfigurovatelné) prahové hodnoty pro různé typy triggerů k rozhodnutí, kdy přidat instance, například stáří zpráv a velikost fronty pro QueueTrigger. Další informace najdete v tématu [Jak fungují plány spotřeby a Premium](functions-scale.md#how-the-consumption-and-premium-plans-work).

Toto chování škálování je dostatečné pro mnoho Node.jsch aplikací. Pro aplikace vázané na procesor můžete zvýšit výkon pomocí více pracovních procesů v jazyce.

Ve výchozím nastavení má každá instance hostitele Functions pracovní proces s jedním jazykem. Počet pracovních procesů na hostitele můžete zvýšit (až 10) pomocí nastavení aplikace [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) . Azure Functions se pak pokusí rovnoměrně distribuovat souběžná volání funkcí mezi tyto pracovní procesy. 

FUNCTIONS_WORKER_PROCESS_COUNT se vztahuje na každého hostitele, který funkce vytvoří při horizontálním navýšení kapacity aplikace, aby splňovala požadavky. 

## <a name="node-version"></a>Verze uzlu

Následující tabulka uvádí aktuální podporované verze Node.js pro každou hlavní verzi modulu runtime funkcí podle operačního systému:

| Verze funkcí | Verze uzlu (Windows) | Verze uzlu (Linux) |
|---|---| --- |
| verze | 6.11.2 (uzamčeno modulem runtime) | neuvedeno |
| 2.x  | ~ 8<br/>~ 10 (doporučeno)<br/>~ 12<sup>*</sup> | ~ 8 (doporučeno)<br/>~ 10  |
| 3.x | ~ 10<br/>~ 12 (doporučeno)  | ~ 10<br/>~ 12 (doporučeno) |

<sup>*</sup>Uzel ~ 12 je aktuálně povolen pro verzi 2. x modulu runtime Functions. Pro nejlepší výkon ale doporučujeme použít modul runtime Functions verze 3. x s uzlem ~ 12. 

Aktuální verzi, kterou používá modul runtime, můžete zobrazit zkontrolováním výše uvedeného nastavení aplikace nebo tiskem `process.version` z libovolné funkce. Cílovou verzi v Azure můžete nastavit tak, že nastavíte [nastavení WEBSITE_NODE_DEFAULT_VERSION aplikace](functions-how-to-use-azure-function-app-settings.md#settings) na podporovanou verzi LTS, například `~10` .

## <a name="dependency-management"></a>Správa závislostí
Aby bylo možné používat knihovny komunity v kódu JavaScriptu, jak je znázorněno v následujícím příkladu, je nutné zajistit, aby všechny závislosti byly nainstalovány v Function App v Azure.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> Měli byste definovat `package.json` soubor v kořenu Function App. Definování souboru umožňuje všem funkcím v aplikaci sdílet stejné balíčky v mezipaměti, což poskytuje nejlepší výkon. Pokud dojde ke konfliktu verze, můžete ho vyřešit přidáním `package.json` souboru do složky konkrétní funkce.  

Když nasazujete aplikace Function App ze správy zdrojového kódu, všechny `package.json` soubory v úložišti se ve `npm install` své složce spustí během nasazování. Ale při nasazení prostřednictvím portálu nebo rozhraní příkazového řádku budete muset balíčky nainstalovat ručně.

Existují dva způsoby, jak nainstalovat balíčky do Function App: 

### <a name="deploying-with-dependencies"></a>Nasazení pomocí závislostí
1. Nainstalujte všechny požadované balíčky místně spuštěním `npm install` .

2. Nasaďte kód a ujistěte se, že `node_modules` je složka součástí nasazení. 


### <a name="using-kudu"></a>Použití Kudu
1. Přejděte na `https://<function_app_name>.scm.azurewebsites.net`.

2. Klikněte na **ladit konzolu**  >  **cmd**.

3. Přejít na `D:\home\site\wwwroot` a poté přetáhněte package.jsdo souboru do složky **wwwroot** v horní polovině stránky.  
    Soubory můžete do aplikace Function App nahrávat i jiným způsobem. Další informace najdete v tématu [Postup aktualizace souborů aplikace Function App](functions-reference.md#fileupdate). 

4. Po nahrání package.jsv souboru spusťte `npm install` příkaz v **konzole vzdáleného spuštění Kudu**.  
    Tato akce stáhne balíčky uvedené v package.jsv souboru a restartuje aplikaci Function App.

## <a name="environment-variables"></a>Proměnné prostředí

V funkcích jsou [nastavení aplikace](functions-app-settings.md), jako jsou například připojovací řetězce služby, vystavena jako proměnné prostředí během provádění. K těmto nastavením můžete přistupovat pomocí `process.env` , jak je znázorněno v druhé a třetí volání do `context.log()` protokolu `AzureWebJobsStorage` a `WEBSITE_SITE_NAME` proměnné prostředí a:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Při místním spuštění jsou nastavení aplikace načítána z [local.settings.jsv](functions-run-local.md#local-settings-file) souboru projektu.

## <a name="configure-function-entry-point"></a>Konfigurovat vstupní bod funkce

`function.json`Vlastnosti `scriptFile` , které `entryPoint` lze použít ke konfiguraci umístění a názvu exportované funkce. Tyto vlastnosti mohou být důležité při překládání JavaScriptu.

### <a name="using-scriptfile"></a>Používání akce `scriptFile`

Ve výchozím nastavení je funkce JavaScriptu spouštěna z `index.js` , soubor, který sdílí stejný nadřazený adresář jako odpovídající `function.json` .

`scriptFile` lze použít k získání struktury složky, která vypadá jako v následujícím příkladu:

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - sayHello.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

`function.json`Parametr for `myNodeFunction` by měl obsahovat `scriptFile` vlastnost ukazující na soubor s exportovanou funkcí, která má být spuštěna.

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Používání akce `entryPoint`

V `scriptFile` (nebo `index.js` ) musí být funkce exportována pomocí, aby bylo `module.exports` možné je vyhledat a spustit. Ve výchozím nastavení je funkce, která se spustí, když se aktivuje, jediný export z tohoto souboru, export s názvem `run` nebo export s názvem `index` .

Dá se nakonfigurovat pomocí `entryPoint` v `function.json` , jako v následujícím příkladu:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

Ve Functions v2. x, který podporuje `this` parametr v uživatelských funkcích, může být kód funkce poté jako v následujícím příkladu:

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

V tomto příkladu je důležité si uvědomit, že i když je objekt exportován, neexistují žádné záruky pro zachování stavu mezi prováděními.

## <a name="local-debugging"></a>Místní ladění

Při spuštění s `--inspect` parametrem Node.js proces naslouchá klientovi ladění na zadaném portu. V Azure Functions 2. x můžete zadat argumenty, které mají být předávány do procesu Node.js, který spouští váš kód přidáním proměnné prostředí nebo nastavení aplikace `languageWorkers:node:arguments = <args>` . 

Chcete-li ladit místně, přidejte `"languageWorkers:node:arguments": "--inspect=5858"` `Values` do souboru [local.settings.js](./functions-run-local.md#local-settings-file) a připojte ladicí program k portu 5858.

Při ladění pomocí VS Code `--inspect` je parametr automaticky přidán pomocí `port` hodnoty v launch.jsprojektu v souboru.

Ve verzi 1. x nebude nastavení `languageWorkers:node:arguments` fungovat. Port pro ladění můžete vybrat s [`--nodeDebugPort`](./functions-run-local.md#start) parametrem na Azure Functions Core Tools.

## <a name="typescript"></a>TypeScript

Když cílíte na verzi 2. x modulu runtime Functions, [Azure Functions pro Visual Studio Code](./create-first-function-cli-typescript.md) a [Azure Functions Core Tools](functions-run-local.md) vám umožní vytvářet aplikace funkcí pomocí šablony, která podporuje projekty aplikace funkcí TypeScript. Šablona generuje `package.json` a vytvoří `tsconfig.json` soubory projektu, které usnadňují přeorientaci, spouštění a publikování funkcí jazyka JavaScript z kódu TypeScript pomocí těchto nástrojů.

Vygenerovaný `.funcignore` soubor se používá k určení, které soubory jsou vyloučeny při publikování projektu do Azure.  

Soubory TypeScript (. TS) se do výstupního adresáře přecházejí do souborů JavaScriptu (. js) `dist` . Šablony TypeScript používají [ `scriptFile` parametr](#using-scriptfile) v `function.json` k označení umístění odpovídajícího souboru. js ve `dist` složce. Umístění výstupu je nastaveno šablonou pomocí `outDir` parametru v `tsconfig.json` souboru. Pokud změníte toto nastavení nebo název složky, modul runtime nemůže najít kód, který se má spustit.

Způsob, jakým místně vyvíjíte a nasazujete z projektu TypeScript, závisí na vašem vývojovém nástroji.

### <a name="visual-studio-code"></a>Visual Studio Code

[Azure Functions for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) Extension umožňuje vyvíjet funkce pomocí TypeScript. Základní nástroje jsou požadavkem rozšíření Azure Functions.

Pokud chcete vytvořit aplikaci funkcí TypeScript v Visual Studio Code, `TypeScript` při vytváření aplikace Function App vyberte jako svůj jazyk.

Když stisknete klávesu **F5** ke spuštění aplikace místně, transpilation se provede před inicializací hostitele (func.exe). 

Když nasadíte aplikaci Function App do Azure pomocí tlačítka **nasadit do aplikace Function App...** , Azure Functions rozšíření nejprve vygeneruje sestavení JavaScriptu připravené pro produkční soubory JavaScript ze zdrojových souborů TypeScriptu.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Existuje několik způsobů, jak se projekt TypeScript při použití základních nástrojů liší od projektu jazyka JavaScript.

#### <a name="create-project"></a>Vytvoření projektu

Chcete-li vytvořit projekt aplikace funkcí TypeScript pomocí základních nástrojů, je při vytváření aplikace Function App nutné zadat možnost jazyka TypeScript. Můžete to udělat jedním z následujících způsobů:

- Spusťte `func init` příkaz, vyberte `node` jako zásobník jazyka a pak vyberte `typescript` .

- Spusťte příkaz `func init --worker-runtime typescript`.

#### <a name="run-local"></a>Spustit místní

Pokud chcete kód aplikace Function App spustit lokálně pomocí základních nástrojů, použijte následující příkazy místo `func host start` : 

```command
npm install
npm start
```

`npm start`Příkaz je ekvivalentní k následujícím příkazům:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>Publikování do Azure

Než použijete [`func azure functionapp publish`] příkaz k nasazení do Azure, vytvoříte sestavení JavaScriptu připravené pro produkční soubory z zdrojových souborů TypeScriptu. 

Následující příkazy připraví a publikují projekt TypeScript pomocí základních nástrojů: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

V tomto příkazu nahraďte `<APP_NAME>` názvem vaší aplikace Function App.

## <a name="considerations-for-javascript-functions"></a>Pokyny pro funkce JavaScriptu

Při práci s funkcemi JavaScriptu si pamatujte na informace v následujících oddílech.

### <a name="choose-single-vcpu-app-service-plans"></a>Výběr plánů s jednou vCPU App Service

Když vytváříte aplikaci Function App, která používá plán App Service, doporučujeme místo plánu s více Vcpuy vybrat plán s jedním vCPU. V současné době fungují funkce JavaScript Functions efektivněji na virtuálních počítačích s jednou vCPU a použití větších virtuálních počítačů nevytváří očekávaná vylepšení výkonu. V případě potřeby můžete ruční horizontální navýšení kapacity rozšířit přidáním více instancí virtuálních počítačů s jedním vCPU nebo můžete povolit automatické škálování. Další informace najdete v tématu [Ruční nebo automatické škálování počtu instancí](../azure-monitor/platform/autoscale-get-started.md?toc=/azure/app-service/toc.json).

### <a name="cold-start"></a>Studený start

Při vývoji Azure Functions v modelu hostování bez serveru je to realita. *Studená Start* odkazuje na skutečnost, že při prvním spuštění aplikace Function po určité době nečinnosti trvá spuštění déle. Pro funkce JavaScriptu s velkými stromy závislostí se může jednat o významné spuštění. Chcete-li urychlit proces spuštění, [Spusťte své funkce jako soubor balíčku](run-functions-from-deployment-package.md) , pokud je to možné. Mnoho metod nasazení ve výchozím nastavení používá model Run z balíčků, ale pokud se setkáváte velkými studenými starty a neběží tímto způsobem, může tato změna nabízet významné vylepšení.

### <a name="connection-limits"></a>Omezení připojení

Když v Azure Functions aplikaci použijete klienta pro konkrétní služby, nevytvářejte nového klienta s každým voláním funkce. Místo toho vytvořte jednoho statického klienta v globálním oboru. Další informace najdete v tématu [Správa připojení v Azure Functions](manage-connections.md).

### <a name="use-async-and-await"></a>Použijte `async` a `await`

Při psaní Azure Functions v JavaScriptu, byste měli napsat kód pomocí `async` `await` klíčových slov a. Psaní kódu pomocí `async` a `await` místo zpětných volání nebo `.then` a `.catch` pomocí příslibů pomáhá vyhnout se dvěma běžným problémům:
 - Vyvolává nezachycené výjimky, které způsobí [selhání procesu Node.js](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly), což může mít vliv na spouštění dalších funkcí.
 - Neočekávané chování, například chybějící protokoly z Context. log, způsobené asynchronními voláními, která nejsou správně očekávána.

V následujícím příkladu je asynchronní metoda `fs.readFile` vyvolána s funkcí zpětného volání při prvním pokusu jako jeho druhý parametr. Tento kód způsobuje oba problémy uvedené výše. Výjimka, která se explicitně nezachycuje ve správném rozsahu, nastala celý proces (problém #1). Volání `context.done()` mimo rozsah funkce zpětného volání znamená, že vyvolání funkce může skončit před čtením souboru (problém #2). V tomto příkladu zavoláte `context.done()` příliš časné výsledky v chybějících položkách protokolu začínajících na `Data from file:` .

```javascript
// NOT RECOMMENDED PATTERN
const fs = require('fs');

module.exports = function (context) {
    fs.readFile('./hello.txt', (err, data) => {
        if (err) {
            context.log.error('ERROR', err);
            // BUG #1: This will result in an uncaught exception that crashes the entire process
            throw err;
        }
        context.log(`Data from file: ${data}`);
        // context.done() should be called here
    });
    // BUG #2: Data is not guaranteed to be read before the Azure Function's invocation ends
    context.done();
}
```

Použití `async` `await` klíčových slov a pomáhá zabránit oběma těmto chybám. Měli byste použít funkci Node.js Utility [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) k zapnutí funkcí s možnostmi zpětného volání na základě chyb na funkce, které lze očekávat.

V následujícím příkladu všechny neošetřené výjimky vyvolané během provádění funkce selžou pouze jednotlivé vyvolání, které vyvolalo výjimku. `await`Klíčové slovo znamená, že následující kroky `readFileAsync` budou provedeny pouze po `readFile` dokončení. Pomocí `async` a `await` není také nutné volat `context.done()` zpětné volání.

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    let data;
    try {
        data = await readFileAsync('./hello.txt');
    } catch (err) {
        context.log.error('ERROR', err);
        // This rethrown exception will be handled by the Functions Runtime and will only fail the individual invocation
        throw err;
    }
    context.log(`Data from file: ${data}`);
}
```

## <a name="next-steps"></a>Další kroky

Další informace naleznete v následujících zdrojích:

+ [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
+ [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
+ [Aktivační události a vazby Azure Functions](functions-triggers-bindings.md)

[' Func Azure functionapp Publish ']: functions-run-local.md#project-file-deployment
