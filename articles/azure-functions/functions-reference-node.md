---
title: Referenční dokumentace pro vývojáře JavaScriptu pro Azure Functions | Microsoft Docs
description: Naučte se vyvíjet funkce pomocí JavaScriptu.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: funkce azure, funkce, zpracování událostí, webhook, dynamické výpočty, architektura bez serverů
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: azure-functions
ms.devlang: nodejs
ms.topic: reference
ms.date: 02/24/2019
ms.author: glenga
ms.openlocfilehash: 62115dd519336c728b679e4e698182a50660a464
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949892"
---
# <a name="azure-functions-javascript-developer-guide"></a>Azure Functions příručka pro vývojáře JavaScriptu

Tato příručka obsahuje informace o složitými rozhraními psaní Azure Functions pomocí JavaScriptu.

Funkce JavaScriptu je export `function` , který se spustí, když se aktivuje (triggery[se konfigurují v Function. JSON](functions-triggers-bindings.md)). První argument předaný do každé funkce je `context` objekt, který se používá pro příjem a odesílání dat vazby, protokolování a komunikaci s modulem runtime.

V tomto článku se předpokládá, že už jste si přečetli [Azure Functions referenci pro vývojáře](functions-reference.md). Dokončete rychlý Start funkcí a vytvořte svoji první funkci pomocí [Visual Studio Code](functions-create-first-function-vs-code.md) nebo na [portálu](functions-create-first-azure-function.md).

Tento článek také podporuje [vývoj aplikací TypeScript](#typescript).

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

V kořenu projektu se nachází sdílený soubor [Host. JSON](functions-host-json.md) , který se dá použít ke konfiguraci aplikace Function App. Každá funkce má složku s vlastním souborem kódu (. js) a konfiguračním souborem vazby (Function. JSON). Název `function.json`nadřazeného adresáře je vždy název vaší funkce.

Rozšíření vazby požadovaná ve [verzi 2. x](functions-versions.md) modulu runtime Functions jsou definována v `extensions.csproj` souboru se skutečnými soubory `bin` knihoven ve složce. Při vývoji místně je nutné [zaregistrovat rozšíření vazby](./functions-bindings-register.md#extension-bundles). Při vývoji funkcí v Azure Portal se tato registrace provede za vás.

## <a name="exporting-a-function"></a>Export funkce

Funkce JavaScriptu musí být exportovány [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) prostřednictvím ( [`exports`](https://nodejs.org/api/modules.html#modules_exports)nebo). Vyexportovaná funkce by měla být funkce JavaScriptu, která se spustí, když se aktivuje.

Ve výchozím nastavení vyhledává modul runtime Functions funkci v `index.js`, kde `index.js` sdílí stejný nadřazený adresář jako odpovídající `function.json`. Ve výchozím nastavení by vaše exportovaná funkce měla být jediným exportem z jeho souboru nebo export s názvem `run` nebo `index`. Chcete-li nakonfigurovat umístění souboru a název exportu funkce, přečtěte si informace o [konfiguraci vstupního bodu funkce](functions-reference-node.md#configure-function-entry-point) níže.

Vaše exportovaná funkce je předána počtem argumentů při provádění. První argument, který bude trvat, je `context` vždy objekt. Pokud je funkce synchronní (nevrací příslib), je nutné předat `context` objekt, protože volání `context.done` je vyžadováno pro správné použití.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Export asynchronní funkce
Při použití [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklarace nebo prostého JavaScriptu [příslibů](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) ve verzi 2. x modulu runtime Functions není nutné explicitně volat [`context.done`](#contextdone-method) zpětné volání k signalizaci, že byla funkce dokončena. Vaše funkce se dokončí po dokončení exportované asynchronní funkce nebo příslib. V případě funkcí cílících na modul runtime verze 1. x je nutné [`context.done`](#contextdone-method) i nadále volat, když je proveden provádění kódu.

Následující příklad představuje jednoduchou funkci, která protokoluje, že byla aktivována, a okamžitě dokončí provádění.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Při exportu asynchronní funkce můžete také nakonfigurovat výstupní vazbu, aby se `return` hodnota vybrala. To se doporučuje, pokud máte jenom jednu výstupní vazbu.

Chcete-li přiřadit výstup `return`pomocí, `name` změňte vlastnost na `$return` hodnotu `function.json`v.

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
V jazyce JavaScript jsou [vazby](functions-triggers-bindings.md) konfigurovány a definovány ve funkci Function. JSON. Funkce pracují s vazbami různými způsoby.

### <a name="inputs"></a>Vstupy
Vstup je rozdělen do dvou kategorií v Azure Functions: jeden je vstup triggeru a druhý je další vstup. Trigger a další vstupní vazby (vazby `direction === "in"`) mohou být čteny funkcí třemi způsoby:
 - **_[Doporučeno]_ Jako parametry předané do vaší funkce.** Jsou předány funkci ve stejném pořadí, v jakém jsou definovány v *Function. JSON*. Vlastnost definovaná v *Function. JSON* nemusí odpovídat názvu parametru, i když by měla být. `name`
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Jako členové [`context.bindings`](#contextbindings-property) objektu.** Každý člen je pojmenován `name` vlastností definovanými v *Function. JSON*.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **Jako vstupy pomocí objektu jazyka [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) JavaScript.** To je v podstatě totéž jako předání vstupů jako parametrů, ale umožňuje dynamicky zpracovávat vstupy.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Výstupy
Výstupy (vazby `direction === "out"`) mohou být do funkce zapisovány pomocí několika způsobů. Ve všech případech `name` vlastnost vazby, jak je definováno v *Function. JSON* , odpovídá názvu člena objektu zapsaného do funkce. 

Do výstupních vazeb můžete přiřadit data jedním z následujících způsobů (tyto metody nekombinovat):

- **_[Doporučeno pro více výstupů]_ Vrácení objektu.** Pokud používáte funkci vracející asynchronní nebo příslib, můžete vrátit objekt s přiřazenými výstupními daty. V následujícím příkladu jsou výstupní vazby pojmenovány "httpResponse" a "queueOutput" v *Function. JSON*.

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
- **Přiřazení hodnot k `context.bindings`**  těmto hodnotám můžete přiřadit přímo kontextu. Bindings.

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

Chcete-li definovat datový typ pro vstupní vazbu, použijte `dataType` vlastnost v definici vazby. Chcete-li například číst obsah požadavku HTTP v binárním formátu, použijte typ `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Možnosti pro `dataType` jsou: `binary`, `stream`a .`string`

## <a name="context-object"></a>kontextový objekt
Modul runtime používá `context` objekt k předání dat do a z vaší funkce a k umožnění komunikace s modulem runtime. Kontextový objekt lze použít pro čtení a nastavení dat z vazeb, zápis protokolů a použití `context.done` zpětného volání, pokud je vaše exportovaná funkce synchronní.

`context` Objekt je vždy prvním parametrem funkce. Mělo by být zahrnuto, protože má důležité metody jako `context.done` a `context.log`. Objekt můžete pojmenovat libovolným způsobem, `ctx` který chcete (například nebo `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>Context. Bindings – vlastnost

```js
context.bindings
```

Vrátí pojmenovaný objekt, který se používá ke čtení nebo přiřazení dat vazby. Data vazby vstupu a triggeru se dají použít při čtení vlastností `context.bindings`na. Data vazby výstupu lze přiřadit přidáním dat do`context.bindings`

Například následující definice vazeb ve funkci Function. JSON umožňují přístup k obsahu fronty z `context.bindings.myInput` a přiřazování výstupů do fronty pomocí. `context.bindings.myOutput`

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

Vrátí pojmenovaný objekt, který obsahuje metadata triggeru a data vyvolání funkce (`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`). Příklad metadat triggeru najdete v tomto [příkladu centra událostí](functions-bindings-event-hubs.md#trigger---javascript-example).

### <a name="contextdone-method"></a>Context. hotový – metoda

```js
context.done([err],[propertyBag])
```

Umožňuje modulu runtime zjistit, že váš kód byl dokončen. Pokud funkce používá [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklaraci, nemusíte používat `context.done()`. `context.done` Zpětné volání je implicitně voláno. Asynchronní funkce jsou k dispozici v uzlu 8 nebo novější verzi, která vyžaduje verzi 2. x modulu runtime Functions.

Není-li funkce asynchronní funkcí, je **nutné zavolat** `context.done` , aby bylo možné informovat modul runtime o dokončení funkce. Vypršel časový limit spuštění, pokud chybí.

`context.done` Metoda umožňuje předat zpět do modulu runtime chybu definovanou uživatelem a objekt JSON obsahující výstupní data vazby. Vlastnosti předané `context.done` pro přepsání jsou `context.bindings` nastaveny u objektu.

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

Umožňuje zapisovat do protokolů funkcí streamování na výchozí úrovni trasování. V `context.log`systému jsou k dispozici další metody protokolování, které umožňují psát protokoly funkcí na jiných úrovních trasování:


| Metoda                 | Popis                                |
| ---------------------- | ------------------------------------------ |
| **error(_message_)**   | Zapisuje do protokolování na úrovni chyb nebo snižuje.   |
| **warn(_message_)**    | Zapíše do protokolování na úrovni upozornění nebo sníží. |
| **info(_message_)**    | Provede zápis do protokolování na úrovni informací nebo nižší.    |
| **verbose (_zpráva_)** | Zapisuje do protokolování na úrovni podrobností.           |

Následující příklad zapíše protokol na úrovni trasování upozornění:

```javascript
context.log.warn("Something has happened."); 
```

Prahovou [hodnotu na úrovni trasování můžete nakonfigurovat pro protokolování](#configure-the-trace-level-for-console-logging) v souboru Host. JSON. Další informace o zápisu protokolů najdete v tématu [zápis výstupů trasování](#writing-trace-output-to-the-console) níže.

Přečtěte si [Azure Functions monitorování](functions-monitoring.md) , kde najdete další informace o zobrazení a dotazování protokolů funkcí.

## <a name="writing-trace-output-to-the-console"></a>Zápis výstupu trasování do konzoly 

Ve funkcích použijte `context.log` metody pro zápis výstupu trasování do konzoly. Ve funkcích v2. x jsou výstupy trasování `console.log` pomocí zachyceny na úrovni Function App. To znamená, že výstupy z `console.log` nejsou vázány na konkrétní vyvolání funkce a nejsou zobrazeny v protokolech konkrétní funkce. Nicméně se šíří na Application Insights. Ve funkcích v1. x nelze použít `console.log` k zápisu do konzoly.

Když zavoláte `context.log()`, zpráva se zapíše do konzoly na výchozí úrovni trasování, což je úroveň trasování _informací_ . Následující kód zapisuje do konzoly na úrovni trasování informací:

```javascript
context.log({hello: 'world'});  
```

Tento kód je ekvivalentní k výše uvedenému kódu:

```javascript
context.log.info({hello: 'world'});  
```

Tento kód zapisuje do konzoly na úrovni chyby:

```javascript
context.log.error("An error has occurred.");  
```

Vzhledem k tomu, že _Chyba_ je nejvyšší úroveň trasování, toto trasování se zapisuje do výstupu na všech úrovních trasování, pokud je povoleno protokolování.

Všechny `context.log` metody podporují stejný formát parametru, který je podporován metodou Node. js [util. Format](https://nodejs.org/api/util.html#util_util_format_format). Vezměte v úvahu následující kód, který zapisuje protokoly funkcí pomocí výchozí úrovně trasování:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Stejný kód můžete také napsat v následujícím formátu:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Konfigurace úrovně trasování pro protokolování konzoly

Funkce 1. x umožňuje definovat prahovou úroveň trasování pro zápis do konzoly, která usnadňuje řízení způsobu zápisu trasování do konzoly z vaší funkce. Chcete-li nastavit prahovou hodnotu pro všechna trasování zapsaná do konzoly `tracing.consoleLevel` , použijte vlastnost v souboru Host. JSON. Toto nastavení platí pro všechny funkce aplikace Function App. Následující příklad nastaví prahovou hodnotu trasování pro povolení podrobného protokolování:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

Hodnoty **consoleLevel** odpovídají názvům `context.log` metod. Chcete-li zakázat veškeré protokolování trasování do konzoly, nastavte **consoleLevel** na _off_. Další informace naleznete v tématu [reference Host. JSON](functions-host-json-v1.md).

## <a name="http-triggers-and-bindings"></a>Aktivační události a vazby HTTP

Aktivační události HTTP a Webhooku a výstupní vazby HTTP používají objekty žádosti a odpovědi, které reprezentují zprávy HTTP.  

### <a name="request-object"></a>Request – objekt

Objekt `context.req` (Request) má následující vlastnosti:

| Vlastnost      | Popis                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | Objekt, který obsahuje tělo žádosti.               |
| _záhlaví_     | Objekt, který obsahuje hlavičky požadavku.                   |
| _method_      | Metoda HTTP požadavku.                                |
| _originalUrl_ | Adresa URL požadavku.                                        |
| _params_      | Objekt, který obsahuje parametry směrování požadavku. |
| _query_       | Objekt, který obsahuje parametry dotazu.                  |
| _rawBody_     | Tělo zprávy jako řetězec.                           |


### <a name="response-object"></a>Objekt odpovědi

Objekt `context.res` (Response) má následující vlastnosti:

| Vlastnost  | Popis                                               |
| --------- | --------------------------------------------------------- |
| _body_    | Objekt, který obsahuje tělo odpovědi.         |
| _záhlaví_ | Objekt, který obsahuje hlavičky odpovědi.             |
| _isRaw_   | Indikuje, že pro odpověď se přeskočilo formátování.    |
| _status_  | Stavový kód protokolu HTTP odpovědi.                     |

### <a name="accessing-the-request-and-response"></a>Přístup k žádosti a odpovědi 

Když pracujete s triggery HTTP, můžete získat přístup k objektům požadavků HTTP a odpovědí několika způsoby:

+ **Od `req` a `res` vlastnosti`context` objektu.** Tímto způsobem můžete použít vzor konvenční pro přístup k datům http z objektu Context namísto použití úplného `context.bindings.name` vzoru. Následující příklad ukazuje, jak získat přístup `req` k objektům a `res` v `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
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
+ **_[Pouze odpověď]_ Voláním `context.res.send(body?: any)`.** Je vytvořena odpověď protokolu HTTP se vstupem `body` jako text odpovědi. `context.done()`je implicitně volána.

+ **_[Pouze odpověď]_ Voláním `context.done()`.** Speciální typ vazby HTTP vrátí odpověď, která je předána `context.done()` metodě. Následující výstupní vazba protokolu HTTP definuje `$return` výstupní parametr:

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

## <a name="node-version"></a>Verze uzlu

Následující tabulka ukazuje verzi Node. js, kterou používá každá hlavní verze běhového modulu Functions:

| Verze funkcí | Verze Node.js | 
|---|---|
| verze | 6.11.2 (uzamčeno modulem runtime) |
| 2.x  | _Aktivní LTS_ a _Údržba LTS_ verze Node. js (Doporučené 8.11.1 a 10.14.1). Nastavte verzi pomocí [nastavení aplikace](functions-how-to-use-azure-function-app-settings.md#settings)WEBSITE_NODE_DEFAULT_VERSION.|

Aktuální verzi, kterou používá modul runtime, můžete zobrazit zkontrolováním výše uvedeného nastavení aplikace nebo tiskem `process.version` z libovolné funkce.

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

Když nasazujete aplikace Function App ze správy `package.json` zdrojového kódu, všechny soubory v úložišti se ve `npm install` své složce spustí během nasazování. Ale při nasazení prostřednictvím portálu nebo rozhraní příkazového řádku budete muset balíčky nainstalovat ručně.

Existují dva způsoby, jak nainstalovat balíčky do Function App: 

### <a name="deploying-with-dependencies"></a>Nasazení pomocí závislostí
1. Nainstalujte všechny požadované balíčky místně spuštěním `npm install`.

2. Nasaďte kód a ujistěte se, že `node_modules` je složka součástí nasazení. 


### <a name="using-kudu"></a>Použití Kudu
1. Přejděte do části `https://<function_app_name>.scm.azurewebsites.net` (Soubor > Nový > Jiné).

2. Klikněte na **ladit konzolu** > **cmd**.

3. Do složky wwwroot v horní části stránky přetáhněte soubor Package. JSON. `D:\home\site\wwwroot`  
    Soubory můžete do aplikace Function App nahrávat i jiným způsobem. Další informace najdete v tématu [Postup aktualizace souborů aplikace Function App](functions-reference.md#fileupdate). 

4. Po nahrání souboru Package. JSON spusťte `npm install` příkaz v **konzole vzdáleného spuštění Kudu**.  
    Tato akce stáhne balíčky uvedené v souboru Package. JSON a restartuje aplikaci Function App.

## <a name="environment-variables"></a>Proměnné prostředí

V funkcích jsou [nastavení aplikace](functions-app-settings.md), jako jsou například připojovací řetězce služby, vystavena jako proměnné prostředí během provádění. K těmto nastavením můžete přistupovat pomocí `process.env`, jak je znázorněno v druhé a třetí volání do `context.log()` protokolu `AzureWebJobsStorage` a proměnné prostředí a `WEBSITE_SITE_NAME` :

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Při místním spuštění se nastavení aplikace čtou ze souboru [Local. Settings. JSON](functions-run-local.md#local-settings-file) projektu.

## <a name="configure-function-entry-point"></a>Konfigurovat vstupní bod funkce

`function.json` Vlastnosti ,které`entryPoint` lze použít ke konfiguraci umístění a názvu exportované funkce. `scriptFile` Tyto vlastnosti mohou být důležité při překládání JavaScriptu.

### <a name="using-scriptfile"></a>Použití`scriptFile`

Ve výchozím nastavení je funkce JavaScriptu spouštěna z `index.js`, soubor, který sdílí stejný nadřazený adresář jako odpovídající. `function.json`

`scriptFile`lze použít k získání struktury složky, která vypadá jako v následujícím příkladu:

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

`scriptFile` Parametr `function.json` for`myNodeFunction` by měl obsahovat vlastnost ukazující na soubor s exportovanou funkcí, která má být spuštěna.

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Použití`entryPoint`

V `scriptFile` (nebo `index.js`) musí být funkce exportována pomocí `module.exports` , aby bylo možné je vyhledat a spustit. Ve výchozím nastavení je funkce, která se spustí, když se aktivuje, jediný export z tohoto souboru, `run`export s názvem nebo export `index`s názvem.

Dá se nakonfigurovat pomocí `entryPoint` v `function.json`, jako v následujícím příkladu:

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

Při spuštění s `--inspect` parametrem proces Node. js naslouchá klientovi ladění na zadaném portu. V Azure Functions 2. x můžete zadat argumenty pro předání procesu Node. js, který spouští váš kód, přidáním proměnné prostředí nebo nastavení `languageWorkers:node:arguments = <args>`aplikace. 

Chcete-li ladit místně `"languageWorkers:node:arguments": "--inspect=5858"` , `Values` přidejte do souboru [Local. Settings. JSON](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file) a připojte ladicí program k portu 5858.

Při ladění pomocí vs Code `--inspect` je parametr automaticky přidán `port` pomocí hodnoty v souboru Launch. JSON projektu.

Ve verzi 1. x nebude nastavení `languageWorkers:node:arguments` fungovat. Port pro ladění můžete vybrat s [`--nodeDebugPort`](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start) parametrem na Azure Functions Core Tools.

## <a name="typescript"></a>TypeScript

Když cílíte na verzi 2. x modulu runtime Functions, [Azure Functions pro Visual Studio Code](functions-create-first-function-vs-code.md) a [Azure Functions Core Tools](functions-run-local.md) vám umožní vytvářet aplikace funkcí pomocí šablony, která podporuje projekty aplikace funkcí TypeScript. Šablona generuje a `package.json` `tsconfig.json` vytvoří soubory projektu, které usnadňují přeorientaci, spouštění a publikování funkcí jazyka JavaScript z kódu TypeScript pomocí těchto nástrojů.

Vygenerovaný `.funcignore` soubor se používá k určení, které soubory jsou vyloučeny při publikování projektu do Azure.  

Soubory TypeScript (. TS) se do `dist` výstupního adresáře přecházejí do souborů JavaScriptu (. js). Šablony TypeScript používají [ `scriptFile` parametr](#using-scriptfile) v `function.json` k označení umístění odpovídajícího souboru. js ve `dist` složce. Umístění výstupu je nastaveno šablonou `outDir` pomocí parametru `tsconfig.json` v souboru. Pokud změníte toto nastavení nebo název složky, modul runtime nemůže najít kód, který se má spustit.

> [!NOTE]
> Experimentální podpora TypeScript existuje verze 1. x modulu runtime Functions. Experimentální verze přechází soubory TypeScript do souborů JavaScriptu při vyvolání funkce. Ve verzi 2. x byla tato experimentální podpora nahrazena metodou založenou na nástroji, která se transpilation před inicializací hostitele a během procesu nasazení.

Způsob, jakým místně vyvíjíte a nasazujete z projektu TypeScript, závisí na vašem vývojovém nástroji.

### <a name="visual-studio-code"></a>Visual Studio Code

[Azure Functions for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) Extension umožňuje vyvíjet funkce pomocí TypeScript. Základní nástroje jsou požadavkem rozšíření Azure Functions.

Pokud chcete vytvořit aplikaci funkcí TypeScript v Visual Studio Code, při `TypeScript` vytváření aplikace Function App vyberte jako svůj jazyk.

Když stisknete klávesu **F5** ke spuštění aplikace místně, transpilation se provede před inicializací hostitele (Func. exe). 

Když nasadíte aplikaci Function App do Azure pomocí tlačítka **nasadit do aplikace Function App...** , Azure Functions rozšíření nejprve vygeneruje sestavení JavaScriptu připravené pro produkční soubory JavaScript ze zdrojových souborů TypeScriptu.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Existuje několik způsobů, jak se projekt TypeScript při použití základních nástrojů liší od projektu jazyka JavaScript.

#### <a name="create-project"></a>Vytvoření projektu

Chcete-li vytvořit projekt aplikace funkcí TypeScript pomocí základních nástrojů, je při vytváření aplikace Function App nutné zadat možnost jazyka TypeScript. Můžete to udělat jedním z následujících způsobů:

- Spusťte příkaz, vyberte `node` jako zásobník jazyka a pak vyberte `typescript`. `func init`

- Spusťte příkaz `func init --worker-runtime typescript`.

#### <a name="run-local"></a>Spustit místní

Pokud chcete kód aplikace Function App spustit lokálně pomocí základních nástrojů, použijte následující příkazy místo `func host start`: 

```command
npm install
npm start
```

`npm start` Příkaz je ekvivalentní k následujícím příkazům:

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

Když vytváříte aplikaci Function App, která používá plán App Service, doporučujeme místo plánu s více Vcpuy vybrat plán s jedním vCPU. V současné době fungují funkce JavaScript Functions efektivněji na virtuálních počítačích s jednou vCPU a použití větších virtuálních počítačů nevytváří očekávaná vylepšení výkonu. V případě potřeby můžete ruční horizontální navýšení kapacity rozšířit přidáním více instancí virtuálních počítačů s jedním vCPU nebo můžete povolit automatické škálování. Další informace najdete v tématu [Ruční nebo automatické škálování počtu instancí](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json).

### <a name="cold-start"></a>Studený start

Při vývoji Azure Functions v modelu hostování bez serveru je to realita. *Studená Start* odkazuje na skutečnost, že při prvním spuštění aplikace Function po určité době nečinnosti trvá spuštění déle. Pro funkce JavaScriptu s velkými stromy závislostí se může jednat o významné spuštění. Chcete-li urychlit proces spuštění, [Spusťte své funkce jako soubor balíčku](run-functions-from-deployment-package.md) , pokud je to možné. Mnoho metod nasazení ve výchozím nastavení používá model Run z balíčků, ale pokud se setkáváte velkými studenými starty a neběží tímto způsobem, může tato změna nabízet významné vylepšení.

### <a name="connection-limits"></a>Omezení připojení

Když v Azure Functions aplikaci použijete klienta pro konkrétní služby, nevytvářejte nového klienta s každým voláním funkce. Místo toho vytvořte jednoho statického klienta v globálním oboru. Další informace najdete v tématu [Správa připojení v Azure Functions](manage-connections.md).

### <a name="use-async-and-await"></a>Použijte `async` a`await`

Při psaní Azure Functions v JavaScriptu, byste měli napsat kód pomocí `async` klíčových slov a. `await` Psaní kódu pomocí `async` a `await` místo zpětných volání nebo `.then` a `.catch` pomocí příslibů pomáhá vyhnout se dvěma běžným problémům:
 - Vyvolává nezachycené výjimky, které způsobí [selhání procesu Node. js](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly), což může mít vliv na spuštění dalších funkcí.
 - Neočekávané chování, například chybějící protokoly z Context. log, způsobené asynchronními voláními, která nejsou správně očekávána.

V následujícím příkladu je asynchronní metoda `fs.readFile` vyvolána s funkcí zpětného volání při prvním pokusu jako jeho druhý parametr. Tento kód způsobuje oba problémy uvedené výše. Výjimka, která se explicitně nezachycuje ve správném rozsahu, nastala celý proces (problém #1). Volání `context.done()` mimo rozsah funkce zpětného volání znamená, že vyvolání funkce může skončit před čtením souboru (problém #2). V tomto příkladu zavoláte `context.done()` příliš časné výsledky v chybějících položkách protokolu `Data from file:`začínajících na.

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

Použití klíčových slov `await`apomáhá zabránit oběma těmto chybám. `async` Měli byste použít funkci [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) Node. js k zapnutí funkcí s možnostmi zpětného volání ve stylu s možnostmi await.

V následujícím příkladu všechny neošetřené výjimky vyvolané během provádění funkce selžou pouze jednotlivé vyvolání, které vyvolalo výjimku. Klíčové slovo znamená, že následující `readFileAsync` kroky budou provedeny `readFile` pouze po dokončení. `await` Pomocí `async` a `await` nenítakénutné`context.done()` volat zpětné volání.

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    try {
        const data = await readFileAsync('./hello.txt');
    } catch (err) {
        context.log.error('ERROR', err);
        // This rethrown exception will be handled by the Functions Runtime and will only fail the individual invocation
        throw err;
    }
    context.log(`Data from file: ${data}`);
}
```

## <a name="next-steps"></a>Další kroky

Další informace naleznete v následujících materiálech:

+ [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
+ [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
+ [Aktivační události a vazby Azure Functions](functions-triggers-bindings.md)

[' Func Azure functionapp Publish ']: functions-run-local.md#project-file-deployment
