---
title: Referenční informace pro vývojáře jazyka JavaScript pro službu Azure Functions | Dokumentace Microsoftu
description: Naučte se vyvíjet funkce pomocí jazyka JavaScript.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: funkce azure, funkce, zpracování událostí, webhook, dynamické výpočty, architektura bez serverů
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: azure-functions
ms.devlang: nodejs
ms.topic: reference
ms.date: 10/26/2018
ms.author: glenga
ms.openlocfilehash: 44a2cb196e1a89b0abe229f478c0b76b146aa268
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52620621"
---
# <a name="azure-functions-javascript-developer-guide"></a>Příručka pro vývojáře Azure Functions JavaScript

Tato příručka obsahuje informace o složitými rozhraními vytváření Azure Functions s použitím jazyka JavaScript.

Funkce jazyka JavaScript je exportovaná `function` , který se spustí při aktivaci ([aktivační události jsou nakonfigurované v function.json](functions-triggers-bindings.md)). První argument je předán každá funkce `context` objekt, který se používá pro příjem a odesílání vazby dat, protokolování a komunikaci s modulem runtime.

Tento článek předpokládá, že jste si už přečetli [referenční informace pro vývojáře Azure Functions](functions-reference.md). Také by se měla dokončit funkce Rychlý start k vytvoření první funkce pomocí [Visual Studio Code](functions-create-first-function-vs-code.md) nebo [na portálu](functions-create-first-azure-function.md).

## <a name="folder-structure"></a>struktura složek

Struktura složek požadované pro projekt jazyka JavaScript vypadá takto. Toto výchozí nastavení je možné změnit. Další informace najdete v tématu [Soubor_skriptu](#using-scriptfile) níže v části.

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
 | - bin
```

V kořenovém adresáři projektu neexistuje sdílené [host.json](functions-host-json.md) soubor, který můžete použít ke konfiguraci aplikace function app. Každá funkce má složku s vlastními souboru s kódem (.js) a vazbu konfigurační soubor (function.json). Název `function.json`jeho nadřazený adresář je vždy název vaší funkce.

Rozšíření vazby vyžaduje [verze 2.x](functions-versions.md) funkce modulu runtime jsou definovány v `extensions.csproj` souboru se soubory knihovny v `bin` složky. Při vývoji místně, musíte [registraci rozšíření vazby](functions-triggers-bindings.md#local-development-azure-functions-core-tools). Při vytváření funkcí na webu Azure Portal, je tato registrace provede za vás.

## <a name="exporting-a-function"></a>Funkce exportu

Funkce jazyka JavaScript, musí být exportován prostřednictvím [ `module.exports` ](https://nodejs.org/api/modules.html#modules_module_exports) (nebo [ `exports` ](https://nodejs.org/api/modules.html#modules_exports)). Exportované funkce by měla být funkce JavaScriptu, který se spustí při spuštění.

Ve výchozím nastavení, modul runtime služby Functions hledá vaši funkci v `index.js`, kde `index.js` sdílí stejné nadřazený adresář jako odpovídající `function.json`. Ve výchozím nastavení, by měl být exportované funkce jenom export z jeho souboru nebo export s názvem `run` nebo `index`. Nakonfigurujte umístění souboru a exportujte název vaší funkce, přečtěte si informace o [konfigurace vstupní bod vaše funkce](functions-reference-node.md#configure-function-entry-point) níže.

Exportované funkce je předána počet argumentů na spuštění. První argument trvá, než je vždy `context` objektu. Pokud vaše funkce je synchronní (nevrátí příslib), musíte předat `context` objektu jako volání funkce `context.done` se vyžaduje pro správné použití.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Export asynchronní funkce
Při použití [ `async function` ](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) prohlášení nebo prostý JavaScript [příslibů](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) ve verzi 2.x modul runtime služby Functions, není nutné explicitně volat [ `context.done` ](#contextdone-method) zpětné volání pro signalizaci, že vaše funkce byla dokončena. Funkce skončí po dokončení asynchronní exportované funkce/Promise. Pro funkce cílení na modul runtime verze 1.x, musí stále volat [ `context.done` ](#contextdone-method) až váš kód provádí.

V následujícím příkladu je jednoduchou funkci, která zaznamená, že byla spuštěna a okamžitě se dokončí provádění.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Při exportu asynchronní funkci, můžete taky nakonfigurovat výstupní vazbu provést `return` hodnotu. To se doporučuje, pokud máte jenom jeden výstupní vazbu.

K přiřazení výstupu pomocí `return`, změnit `name` vlastnost `$return` v `function.json`.

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

V takovém případě funkce by měl vypadat jako v následujícím příkladu:

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
V jazyce JavaScript [vazby](functions-triggers-bindings.md) se konfigurují a definované v function.json funkce. Funkce s vazby pracovat několika způsoby.

### <a name="inputs"></a>Vstupy
Vstup dělí do dvou kategorií ve službě Azure Functions: jeden je vstup triggeru a druhý je další vstupy. Aktivační události a dalších vstupních vazeb (vazby `direction === "in"`) lze číst pomocí funkce třemi způsoby:
 - **_[Doporučuje]_  Jako parametry předaný do funkce.** Jsou předávány do funkce ve stejném pořadí, ve kterém jsou definovány v *function.json*. Všimněte si, `name` vlastnosti definované v *function.json* nemusí odpovídat názvu parametru, přestože by měl.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Jako členy [ `context.bindings` ](#contextbindings-property) objektu.** Každý člen je pojmenován podle `name` vlastnosti definované v *function.json*.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **Jako vstupy pomocí jazyka JavaScript [ `arguments` ](https://msdn.microsoft.com/library/87dw3w1k.aspx) objektu.** To je v podstatě stejný jako vyhovující vstupy jako parametry, ale umožňuje dynamicky zpracovávat vstupy.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Výstupy
Výstupy (vazby `direction === "out"`) je možné zapisovat na funkci v několika způsoby. Ve všech případech `name` vlastnost vazby, jak jsou definovány v *function.json* odpovídá názvu členem objektu napsané ve své funkci. 

Data můžete přiřadit výstupních vazeb v jednom z následujících způsobů. Tyto metody by neměli kombinovat.
- **_[Doporučuje pro několik výstupů]_  Vrácení objektu.** Pokud používáte async/Promise, vrací funkce, můžete se vrátit objekt s přiřazenou výstupní data. V následujícím příkladu výstupních vazeb se pojmenují "httpResponse" a "queueOutput" *function.json*.

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
  
  Pokud používáte synchronní funkce, můžete se vrátit objekt pomocí [ `context.done` ](#contextdone-method) (viz příklad).
- **_[Doporučuje pro jeden výstup]_  Návratová hodnota přímo a pomocí názvu $return vazby.** Tento postup funguje pouze pro asynchronní/Promise vrácení funkce. Viz příklad v [export asynchronní funkce](#exporting-an-async-function). 
- **Přiřazování hodnot k `context.bindings`**  přímo do context.bindings můžete přiřadit hodnoty.

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
 
### <a name="bindings-data-type"></a>Datový typ vazby

Chcete-li definovat datový typ pro vstupní vazby, použijte `dataType` vlastnost v definici vazby. Například pokud chcete číst obsah požadavku HTTP v binárním formátu, použijte typ `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Možnosti pro `dataType` jsou: `binary`, `stream`, a `string`.

## <a name="context-object"></a>objekt kontextu
Modul runtime používá `context` objekt k předávání dat do a z vaší funkce a umožnit vám komunikovat s modulem runtime. Objekt kontextu je možné pro čtení a nastavení dat od vazeb, zápis protokolů a použití `context.done` zpětného volání při synchronní exportované funkce.

`context` Objekt je vždy první parametr funkce. By měly být zahrnuty, protože má důležité metody, jako `context.done` a `context.log`. Můžete pojmenovat objekt cokoli, co byste chtěli (například `ctx` nebo `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>Vlastnost Context.Bindings

```js
context.bindings
```

Vrátí objekt s názvem, který obsahuje všechny vstupní a výstupní data. Například následující definice vazby ve vaší function.json umožňují přístup k obsahu z fronty z `context.bindings.myInput` a přiřaďte výstupy do fronty pomocí `context.bindings.myOutput`.

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

Můžete také definujte výstupní vazby dat pomocí `context.done` metoda místo `context.binding` objektu (viz níže).

### <a name="contextbindingdata-property"></a>Vlastnost context.bindingData

```js
context.bindingData
```

Vrátí objekt s názvem, který obsahuje data volání triggeru metadata a funkce (`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`). Například aktivační událost metadat najdete v tomto [služby event hubs – příklad](functions-bindings-event-hubs.md#trigger---javascript-example).

### <a name="contextdone-method"></a>Context.Done – metoda

```js
context.done([err],[propertyBag])
```

Umožňuje prostředí runtime vědět, že váš kód byla dokončena. Když vaše funkce používá [ `async function` ](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) prohlášení, není potřeba použít `context.done()`. `context.done` Zpětného volání je implicitně volána. Asynchronní funkce jsou k dispozici v Node 8 nebo novější verzi, která vyžaduje verzi 2.x modul runtime služby Functions.

Pokud funkce není asynchronní funkci **musí volat** `context.done` informovat modul runtime dokončení vaší funkce. Časy spuštění, pokud není nalezena.

`context.done` Metoda umožňuje předat zpět oba uživatelem definované chybové modul runtime a objekt JSON obsahující výstupní vazbu data. Předat vlastnosti `context.done` přepsat nic nastavit `context.bindings` objektu.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>Context.log – metoda  

```js
context.log(message)
```

Umožňuje zapisovat do protokolů streamování funkce na výchozí úrovni trasování. Na `context.log`, další protokolování metody jsou k dispozici, které vám umožňují zapisovat protokoly funkce na jiných úrovních trasování:


| Metoda                 | Popis                                |
| ---------------------- | ------------------------------------------ |
| **Chyba (_zpráva_)**   | Zapíše chyba úroveň protokolování nebo nižší.   |
| **upozornění (_zpráva_)**    | Zapíše do protokolování nebo nižší úroveň pro upozornění. |
| **informace o (_zpráva_)**    | Zapíše informace o úroveň protokolování nebo nižší.    |
| **verbose (_zpráva_)** | Zápisy na podrobné úrovni protokolování.           |

Následující příklad zapíše protokolu na úroveň trasování varování:

```javascript
context.log.warn("Something has happened."); 
```

Je možné [nakonfigurovat prahové hodnoty úroveň trasování pro protokolování](#configure-the-trace-level-for-console-logging) host.json souboru. Další informace o psaní protokolů, najdete v části [zápis trasování výstupy](#writing-trace-output-to-the-console) níže.

Čtení [monitorování Azure Functions](functions-monitoring.md) získat další informace o zobrazení a dotazování protokolů funkce.

## <a name="writing-trace-output-to-the-console"></a>Zápisu výstupu sledování do konzoly 

Ve službě Functions použijete `context.log` metody zapsat výstup trasování do konzoly. V funkce v2.x výstup trasování pomocí `console.log` jsou zachyceny na úrovni aplikace Function App. To znamená, že výstupem z `console.log` nejsou vázané na volání určité funkce a proto nejsou zobrazeny v protokolech určité funkce. , Však rozšířit do Application Insights. Funkce v1.x, nemůžete použít `console.log` k zápisu do konzoly.

Při volání `context.log()`, vaše zapíše se do konzoly na výchozí úrovni trasování, který je _informace_ úroveň trasování. Následující kód, zapíše do konzoly na úroveň trasování informace:

```javascript
context.log({hello: 'world'});  
```

Tento kód je ekvivalentní k výše uvedený kód:

```javascript
context.log.info({hello: 'world'});  
```

Tento kód se zapíše do konzoly na úrovni Chyba:

```javascript
context.log.error("An error has occurred.");  
```

Protože _chyba_ je trasování nejvyšší úrovně, toto trasování bude zapsáno do výstupu na všech úrovních trasování tak dlouho, dokud je povoleno protokolování.

Všechny `context.log` metody podporují stejný formát parametru, který podporuje na Node.js [util.format metoda](https://nodejs.org/api/util.html#util_util_format_format). Vezměte v úvahu následující kód, který zapisuje protokoly funkce s použitím výchozí úroveň trasování:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Můžete také napsat stejný kód v následujícím formátu:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Nakonfiguruje úroveň trasování pro protokolování konzoly

Funkce umožňuje definovat prahové hodnoty úroveň trasování pro zápis do konzoly, která usnadňuje ovládací prvek, který způsob trasování jsou zapsány do konzoly z vaší funkce. Chcete-li nastavení prahové hodnoty pro všechna trasování zapsána do konzoly, použijte `tracing.consoleLevel` vlastnost v souboru host.json. Toto nastavení platí pro všechny funkce v aplikaci function app. Následující příklad nastaví prahovou hodnotu trasování Zapnutí podrobného protokolování:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

Hodnoty **consoleLevel** odpovídají názvům `context.log` metody. Chcete-li zakázat všechny protokolování trasování do konzoly, nastavte **consoleLevel** k _vypnout_. Další informace najdete v tématu [referenční materiály k host.json](functions-host-json.md).

## <a name="http-triggers-and-bindings"></a>HTTP aktivačními událostmi a vazbami

HTTP a triggerů webhooků a HTTP výstupní vazby pomocí žádostí a odpovědí objekty představují zasílání zpráv protokolu HTTP.  

### <a name="request-object"></a>Objekt žádosti

`context.req` (Požadavek) má následující vlastnosti:

| Vlastnost      | Popis                                                    |
| ------------- | -------------------------------------------------------------- |
| _Text_        | Objekt, který obsahuje text žádosti.               |
| _Záhlaví_     | Objekt, který obsahuje hlavičky požadavku.                   |
| _– Metoda_      | Metoda HTTP požadavku.                                |
| _PůvodníAdresaURL_ | Adresa URL požadavku.                                        |
| _params_      | Objekt, který obsahuje směrování parametry požadavku. |
| _Dotaz_       | Objekt, který obsahuje parametry dotazu.                  |
| _rawBody_     | Tělo zprávy jako řetězec.                           |


### <a name="response-object"></a>Objekt odpovědi

`context.res` (Odpověď) má následující vlastnosti:

| Vlastnost  | Popis                                               |
| --------- | --------------------------------------------------------- |
| _Text_    | Objekt, který obsahuje text odpovědi.         |
| _Záhlaví_ | Objekt, který obsahuje hlavičky odpovědi.             |
| _isRaw_   | Označuje, že formátování se přeskočí pro odpověď.    |
| _Stav_  | Stavový kód HTTP odpovědi.                     |

### <a name="accessing-the-request-and-response"></a>Přístup k požadavku a odpovědi 

Při práci s triggerů HTTP, můžete přístup k objektům HTTP požadavků a odpovědí v několika způsoby:

+ **Z `req` a `res` vlastnosti `context` objektu.** Tímto způsobem můžete použít konvenční vzor k datům přístup protokolu HTTP z objektu context, místo nutnosti použít úplnou `context.bindings.name` vzor. Následující příklad ukazuje, jak získat přístup k `req` a `res` objektů `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Z pojmenované vstupní a výstupní vazby.** Tímto způsobem triggeru HTTP a vazby fungují stejně jako všechny vazby. Následující příklad nastaví objektu odpovědi pomocí pojmenovaná `response` vazby: 

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
+ **_[Pouze odpovědi]_  Voláním `context.res.send(body?: any)`.** Je vytvořen odpověď HTTP se vstupem `body` jako text odpovědi. `context.done()` je implicitně volána.

+ **_[Pouze odpovědi]_  Voláním `context.done()`.** Zvláštní druh vazby HTTP vrátí odpověď, která je předána `context.done()` metody. Následující HTTP výstupní vazby definuje `$return` výstupní parametr:

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

## <a name="node-version"></a>Uzel verze

V následující tabulce jsou uvedeny verze Node.js používá každá hlavní verze modul runtime služby Functions:

| Verze funkcí | Verze Node.js | 
|---|---|
| 1.x | 6.11.2 (uzamčeno modulem runtime) |
| 2.x  | _Aktivní LTS_ a sudým číslem _aktuální_ verze Node.js (8.11.1 a 10.6.0 doporučeno). Nastavení verze s použitím WEBSITE_NODE_DEFAULT_VERSION [nastavení aplikace, které](functions-how-to-use-azure-function-app-settings.md#settings).|

Zobrazí aktuální verzi, která používá modul runtime kontrolou nad nastavení aplikace nebo tisk `process.version` z jakékoli funkce.

## <a name="dependency-management"></a>Správa závislostí
Chcete-li použít komunity knihovny do Javascriptového kódu, jak je popsáno následujícím příkladu, je nutné se ujistit, že jsou nainstalovány všechny závislosti na vaší aplikace funkcí v Azure.

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
> Byste měli definovat `package.json` souboru v kořenovém adresáři aplikace Function App. Definování souboru umožňuje všechny funkce v aplikaci sdílet stejné balíčky uložené v mezipaměti, která poskytuje nejlepší výkon. Pokud nastane konflikt verzí ho mohli vyřešit tak, že přidáte `package.json` soubor do složky na konkrétní funkce.  

Při nasazování aplikace Function App ze správy zdrojových kódů, všechny `package.json` soubor přítomen v úložišti, se aktivuje `npm install` ve své složce během nasazení. Ale pokud nasazujete prostřednictvím portálu nebo rozhraní příkazového řádku, budete muset ručně nainstalujte balíčky.

Existují dva způsoby, jak nainstalovat balíčky na aplikace Function App: 

### <a name="deploying-with-dependencies"></a>Nasazení se závislostmi
1. Nainstalujte všechny potřebné balíčky místně spuštěním `npm install`.

2. Nasaďte svůj kód a ujistěte se, že `node_modules` složky je součástí nasazení. 


### <a name="using-kudu"></a>Pomocí Kudu
1. Přejděte do části `https://<function_app_name>.scm.azurewebsites.net` (Soubor > Nový > Jiné).

2. Klikněte na tlačítko **konzole ladění** > **CMD**.

3. Přejděte na `D:\home\site\wwwroot`a pak přetáhněte soubor package.json **wwwroot** složky v horní polovině stránky.  
    Soubory můžete nahrát také aplikaci function App jinými způsoby. Další informace najdete v tématu [aktualizace souborů aplikace funkce](functions-reference.md#fileupdate). 

4. Po nahrání souboru package.json, spusťte `npm install` v příkaz **vzdálené spuštění konzoly Kudu**.  
    Tato akce stáhne balíčky uvedené v souboru package.json a restartuje aplikaci function app.

## <a name="environment-variables"></a>Proměnné prostředí

Ve službě Functions [nastavení aplikace](functions-app-settings.md), jako je například připojení služby jsou řetězce, zveřejní jako proměnné prostředí během provádění. Může přistupovat tato nastavení pomocí `process.env`, jak je znázorněno zde `GetEnvironmentVariable` funkce:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));

    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Při místním spuštění, nastavení aplikace se načítají z [local.settings.json](functions-run-local.md#local-settings-file) souboru projektu.

## <a name="configure-function-entry-point"></a>Konfigurace funkce vstupního bodu

`function.json` Vlastnosti `scriptFile` a `entryPoint` je možné nakonfigurovat umístění a název exportované funkce. Tyto vlastnosti můžou být důležité, pokud JavaScript je transpiled.

### <a name="using-scriptfile"></a>použití `scriptFile`

Ve výchozím nastavení, funkce jazyka JavaScript je spouštěna z `index.js`, soubor, který sdílí stejné nadřazený adresář jako odpovídající `function.json`.

`scriptFile` je možné získat strukturu složek, která vypadá jako v následujícím příkladu:

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - nodeFunction.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

`function.json` Pro `myNodeFunction` by měl obsahovat `scriptFile` vlastnosti odkazující na soubor s exportovanou funkci spustit.

```json
{
  "scriptFile": "../lib/nodeFunction.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>použití `entryPoint`

V `scriptFile` (nebo `index.js`), funkce musí být exportovány pomocí `module.exports` aby bylo možné najít a spustit. Ve výchozím nastavení, je funkce, která se spustí při aktivaci jenom export ze souboru, export s názvem `run`, nebo exportovat pojmenované `index`.

To lze konfigurovat pomocí `entryPoint` v `function.json`, jako v následujícím příkladu:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

V v2.x funkce, která podporuje `this` parametr uživatele funkcí, kód funkce potom možné jako v následujícím příkladu:

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    function logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

V tomto příkladu je důležité si uvědomit, že probíhá Export objektu, neobejde se to ale bez guarantess kolem zachování stavu mezi spuštěními.

## <a name="considerations-for-javascript-functions"></a>Důležité informace pro funkce jazyka JavaScript

Při práci s funkcí jazyka JavaScript, mějte na paměti aspekty uvedené v následujících částech.

### <a name="choose-single-vcpu-app-service-plans"></a>Zvolte jeden virtuální procesor plány služby App Service

Když vytvoříte aplikaci function app, který používá plán služby App Service, doporučujeme, že vyberete jeden virtuální procesor plán spíše než plán s více virtuálních procesorů. V současné době funkce jazyka JavaScript funkce efektivněji běží na virtuálních počítačích s jedním virtuálním procesorem a pomocí větší virtuální počítače nevytvoří vylepšení očekávaný výkon. Pokud je to nezbytné, můžete ručně škálovat přidáváním dalších instancí virtuálních počítačů na jeden virtuální procesor, nebo můžete povolit automatické škálování. Další informace najdete v tématu [ruční nebo automatické škálování počtu instancí](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json).    

### <a name="typescript-and-coffeescript-support"></a>Podpora TypeScript a CoffeeScript

Protože přímou podporu ještě neexistuje pro automatické kompilaci TypeScript nebo CoffeeScript prostřednictvím modulu runtime, musí být zpracována mimo modul runtime v době nasazení těchto podpory. 

### <a name="cold-start"></a>Studený Start

Při spuštění vývoj Azure Functions bez serveru hostování modelu cold jsou realitou. *Studený start* odkazuje na skutečnost, že při spuštění aplikace function app poprvé po určité době nečinnosti, trvá déle, se spustí. Pro funkce jazyka JavaScript s stromové struktury velké závislost zejména studený start můžou být významné. Ke zrychlení procesu studený start [spouštět funkce jako soubor balíčku](run-functions-from-deployment-package.md) Pokud je to možné. Mnoho metod nasazení pomocí spustit z balíčku modelu ve výchozím nastavení, ale pokud vnímají velké souvisejícím s úplným spuštěním a neběží tímto způsobem, tato změna může nabídnout výrazným vylepšením.

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících materiálech:

+ [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
+ [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
+ [Azure Functions aktivačními událostmi a vazbami](functions-triggers-bindings.md)
