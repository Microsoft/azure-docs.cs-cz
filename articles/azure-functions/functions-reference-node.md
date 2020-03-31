---
title: Referenční příručka pro vývojáře JavaScriptu pro funkce Azure
description: Zjistěte, jak vyvíjet funkce pomocí JavaScriptu.
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: reference
ms.date: 12/17/2019
ms.openlocfilehash: 345df8e1ea88caa6f8dbe941245c1f989c3e81c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276826"
---
# <a name="azure-functions-javascript-developer-guide"></a>Průvodce vývojářem JavaScriptu azure funkce

Tato příručka obsahuje informace o složitosti psaní funkcí Azure s JavaScriptem.

Funkce JavaScriptu je `function` exportovaný, který se spustí při aktivaci[(aktivační události jsou konfigurovány v function.json).](functions-triggers-bindings.md) První argument předaný každé `context` funkci je objekt, který se používá pro příjem a odesílání dat vazby, protokolování a komunikaci s runtime.

Tento článek předpokládá, že jste si již přečetli [odkaz na vývojáře Azure Functions](functions-reference.md). Pomocí funkce [Visual Studio Code](functions-create-first-function-vs-code.md) nebo na [portálu](functions-create-first-azure-function.md)můžete provést rychlý start funkcí a vytvořit tak první funkci.

Tento článek také podporuje [vývoj aplikací Typu TypeScript](#typescript).

## <a name="folder-structure"></a>Struktura složek

Požadovaná struktura složek pro projekt JavaScriptu vypadá takto. Toto výchozí nastavení lze změnit. Další informace naleznete v části [scriptFile](#using-scriptfile) níže.

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

V kořenovém adresáři projektu je sdílený soubor [host.json,](functions-host-json.md) který lze použít ke konfiguraci aplikace funkce. Každá funkce má složku s vlastním souborem kódu (.js) a konfiguračním souborem vazby (function.json). Název `function.json`nadřazeného adresáře je vždy název funkce.

V `extensions.csproj` souboru jsou definovány přípony vazby požadované ve [verzi 2.x](functions-versions.md) běhu `bin` Functions se skutečnými soubory knihovny ve složce. Při vývoji místně, musíte [zaregistrovat rozšíření vazby](./functions-bindings-register.md#extension-bundles). Při vývoji funkcí na webu Azure Portal se tato registrace provádí za vás.

## <a name="exporting-a-function"></a>Export funkce

Funkce JavaScriptu musí [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) být [`exports`](https://nodejs.org/api/modules.html#modules_exports)exportovány přes (nebo). Exportovaná funkce by měla být funkce JavaScriptu, která se spustí při aktivaci.

Ve výchozím nastavení hledá zaběhu Funkce `index.js`vaši `index.js` funkci v aplikaci `function.json`, kde sdílí stejný nadřazený adresář jako odpovídající . Ve výchozím případě by exportovaná funkce měla být jediným exportem `run` `index`ze svého souboru nebo exportem s názvem nebo . Chcete-li nakonfigurovat umístění souboru a název exportu funkce, přečtěte si níže [o konfiguraci vstupního bodu funkce.](functions-reference-node.md#configure-function-entry-point)

Exportovaná funkce je předána několik argumentů o provádění. První argument, který trvá, `context` je vždy objekt. Pokud je vaše funkce synchronní (nevrátí Promise), musíte `context` předat objekt, `context.done` jako volání je nutné pro správné použití.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Export asynchronní funkce
Při použití [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklarace nebo prostý JavaScript [Promises](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) ve verzi 2.x funkce runtime, není nutné explicitně volat zpětné [`context.done`](#contextdone-method) volání k signalizaci, že vaše funkce byla dokončena. Vaše funkce se dokončí po dokončení exportované asynchronní funkce/Promise. Pro funkce zaměřené na modul runtime verze [`context.done`](#contextdone-method) 1.x musíte stále volat, když je váš kód hotový.

Následující příklad je jednoduchá funkce, která protokoluje, že byla spuštěna a okamžitě dokončí spuštění.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Při exportu asynchronní funkce můžete také nakonfigurovat `return` výstupní vazbu tak, aby převzala hodnotu. To se doporučuje, pokud máte pouze jednu výstupní vazbu.

Chcete-li přiřadit `return`výstup `name` pomocí `$return` , `function.json`změňte vlastnost na v .

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

V takovém případě by vaše funkce měla vypadat jako následující příklad:

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
V jazyce JavaScript jsou [vazby konfigurovány](functions-triggers-bindings.md) a definovány v souboru function.json funkce. Funkce interagují s vazbami několika způsoby.

### <a name="inputs"></a>Vstupy
Vstup jsou rozděleny do dvou kategorií v Azure Functions: jeden je vstup aktivační události a druhý je další vstup. Trigger a další vstupní vazby `direction === "in"`(vazby) lze číst funkcí třemi způsoby:
 - **_[Doporučeno]_ Jako parametry předávané vaší funkci.** Jsou předány funkci ve stejném pořadí, v jakém jsou definovány v *souboru function.json*. Vlastnost `name` definovaná v *souboru function.json* nemusí odpovídat názvu parametru, i když by měla.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Jako členové [`context.bindings`](#contextbindings-property) objektu.** Každý člen je `name` pojmenován vlastností definovanou v *souboru function.json*.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **Jako vstupy pomocí [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) objektu JavaScript.** To je v podstatě stejné jako předávání vstupů jako parametry, ale umožňuje dynamicky zpracovávat vstupy.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Výstupy
Výstupy (vazby `direction === "out"`) mohou být zapsány funkcí několika způsoby. Ve všech případech `name` vlastnost vazby, jak je definována v *function.json* odpovídá název člena objektu zapsány do funkce. 

Data výstupních vazeb můžete přiřadit jedním z následujících způsobů (tyto metody nekombinujte):

- **_[Doporučeno pro více výstupů]_ Vracím objekt.** Pokud používáte funkci vrácení async/Promise, můžete vrátit objekt s přiřazenými výstupními daty. V níže uvedeném příkladu jsou výstupní vazby pojmenovány "httpResponse" a "queueOutput" v *function.json*.

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

  Pokud používáte synchronní funkci, můžete tento objekt [`context.done`](#contextdone-method) vrátit pomocí (viz příklad).
- **_[Doporučeno pro jeden výstup]_ Vrácení hodnoty přímo a pomocí názvu vazby $return.** To funguje pouze pro asynchronní/Promise vracející funkce. Viz příklad při [exportu asynchronní funkce](#exporting-an-async-function). 
- **Přiřazení hodnot `context.bindings` ** Hodnoty můžete přiřadit přímo context.bindings.

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

Chcete-li definovat datový typ pro `dataType` vstupní vazbu, použijte vlastnost v definici vazby. Chcete-li například číst obsah požadavku HTTP v binárním formátu, použijte typ `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Možnosti `dataType` `binary`jsou: `stream`, `string`, a .

## <a name="context-object"></a>kontextový objekt
Runtime používá `context` objekt k předání dat do a z vaší funkce a umožňují komunikovat s runtime. Objekt kontextu lze použít pro čtení a nastavení dat z vazeb, `context.done` zápis protokolů a použití zpětného volání, když exportované funkce je synchronní.

Objekt `context` je vždy prvním parametrem funkce. Mělo by být zahrnuto, protože `context.done` `context.log`má důležité metody, jako jsou a . Objekt můžete pojmenovat, jak chcete (například `ctx` nebo `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>context.bindings, vlastnost

```js
context.bindings
```

Vrátí pojmenovaný objekt, který se používá ke čtení nebo přiřazení dat vazby. Vstupní a aktivační vazba data lze `context.bindings`přistupovat čtení vlastností na . Výstupní vazby dat lze přiřadit přidáním dat do`context.bindings`

Například následující definice vazby ve vašem function.json umožňují přístup `context.bindings.myInput` k obsahu fronty z `context.bindings.myOutput`a přiřadit výstupy do fronty pomocí .

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

Můžete definovat výstupní data vazby `context.done` pomocí metody `context.binding` namísto objektu (viz níže).

### <a name="contextbindingdata-property"></a>vlastnost context.bindingData

```js
context.bindingData
```

Vrátí pojmenovaný objekt, který obsahuje data aktivační`invocationId`chodů a vyvolání funkce ( , `sys.methodName`, `sys.utcNow`. `sys.randGuid`). Příklad metadat aktivační události naleznete v tomto [příkladu centra událostí](functions-bindings-event-hubs-trigger.md).

### <a name="contextdone-method"></a>context.done metoda

```js
context.done([err],[propertyBag])
```

Umožňuje za běhu vědět, že váš kód byl dokončen. Pokud vaše funkce [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) používá deklaraci, není `context.done()`nutné používat . Zpětné `context.done` volání je implicitně voláno. Asynchronní funkce jsou k dispozici v uzlu 8 nebo novější verzi, která vyžaduje verzi 2.x modulu runtime Functions.

Pokud vaše funkce není asynchronní funkce, **musíte volat** `context.done` informovat modul runtime, že vaše funkce je dokončena. Časový čas spuštění, pokud chybí.

Metoda `context.done` umožňuje předat zpět uživatelem definovanou chybu do runtime a objektu JSON obsahujícího data výstupní vazby. Vlastnosti `context.done` předány přepsat cokoli `context.bindings` nastavit na objektu.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>metoda context.log  

```js
context.log(message)
```

Umožňuje zapisovat do protokolů funkce streamování na výchozí úrovni trasování. Zapnuto `context.log`, jsou k dispozici další metody protokolování, které umožňují zápis protokolů funkcí na jiné úrovně trasování:


| Metoda                 | Popis                                |
| ---------------------- | ------------------------------------------ |
| **chyba(_zpráva_)**   | Zapíše do protokolování úrovně chyb nebo nižší.   |
| **upozornit(_zpráva_)**    | Zapíše do protokolování úrovně upozornění nebo nižší. |
| **info(_zpráva_)**    | Zapíše do protokolování úrovně informací nebo nižší.    |
| **verbose(_zpráva_)** | Zapíše podrobné úrovně protokolování.           |

Následující příklad zapíše protokol na úroveň trasování upozornění:

```javascript
context.log.warn("Something has happened."); 
```

Můžete [nakonfigurovat prahovou hodnotu úrovně trasování pro protokolování](#configure-the-trace-level-for-console-logging) v souboru host.json. Další informace o zápisu protokolů naleznete [v tématu zápis výstupy trasování](#writing-trace-output-to-the-console) níže.

Přečtěte si [monitorování funkcí Azure](functions-monitoring.md) další informace o zobrazení a dotazování protokoly funkcí.

## <a name="writing-trace-output-to-the-console"></a>Zápis výstupu trasování do konzoly 

V functions, můžete `context.log` použít metody k zápisu výstup trasování do konzoly. Funkce v2.x, trasovací `console.log` výstupy pomocí jsou zachyceny na úrovni funkce app. To znamená, že `console.log` výstupy z nejsou vázány na vyvolání konkrétní funkce a nejsou zobrazeny v protokolech konkrétní funkce. Jejich šíření však se šíří do Application Insights. V functions v1.x nelze `console.log` použít k zápisu do konzoly.

Při volání `context.log()`je zpráva zapsána do konzoly na výchozí úrovni trasování, což je úroveň trasování _informací._ Následující kód zapisuje do konzoly na úrovni trasování informací:

```javascript
context.log({hello: 'world'});  
```

Tento kód je ekvivalentní výše uvedenému kódu:

```javascript
context.log.info({hello: 'world'});  
```

Tento kód zapisuje do konzoly na úrovni chyby:

```javascript
context.log.error("An error has occurred.");  
```

Vzhledem k tomu, _že chyba_ je nejvyšší úroveň trasování, toto trasování je zapsána do výstupu na všech úrovních trasování tak dlouho, dokud je povoleno protokolování.

Všechny `context.log` metody podporují stejný formát parametrů, který je podporován metodou Node.js [util.format](https://nodejs.org/api/util.html#util_util_format_format). Zvažte následující kód, který zapisuje protokoly funkcí pomocí výchozí úrovně trasování:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Můžete také napsat stejný kód v následujícím formátu:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Konfigurace úrovně trasování pro protokolování konzoly

Funkce 1.x umožňuje definovat prahovou úroveň trasování pro zápis do konzoly, což usnadňuje řízení způsobu, jakým jsou trasování zapsány do konzoly z vaší funkce. Chcete-li nastavit prahovou hodnotu pro všechna `tracing.consoleLevel` trasování zapsaná do konzoly, použijte vlastnost v souboru host.json. Toto nastavení platí pro všechny funkce ve vaší aplikaci funkce. Následující příklad nastaví prahovou hodnotu trasování, aby bylo možné podrobné protokolování:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

Hodnoty **consoleLevel** odpovídají názvům `context.log` metod. Chcete-li zakázat veškeré protokolování trasování do konzoly, nastavte **konzoluLevel** na _vypnuto_. Další informace naleznete v [tématu host.json reference](functions-host-json-v1.md).

## <a name="http-triggers-and-bindings"></a>Aktivační události a vazby HTTP

Aktivační události HTTP a webhooku a výstupní vazby PROTOKOLU HTTP používají objekty požadavku a odpovědi k reprezentaci zasílání zpráv HTTP.  

### <a name="request-object"></a>Požadavek na objekt

Objekt `context.req` (request) má následující vlastnosti:

| Vlastnost      | Popis                                                    |
| ------------- | -------------------------------------------------------------- |
| _Tělo_        | Objekt, který obsahuje tělo požadavku.               |
| _Záhlaví_     | Objekt, který obsahuje hlavičky požadavku.                   |
| _Metoda_      | Metoda HTTP požadavku.                                |
| _originalUrl_ | Adresa URL požadavku.                                        |
| _params_      | Objekt, který obsahuje parametry směrování požadavku. |
| _query_       | Objekt, který obsahuje parametry dotazu.                  |
| _rawBody_     | Text zprávy jako řetězec.                           |


### <a name="response-object"></a>Objekt odpovědi

Objekt `context.res` (response) má následující vlastnosti:

| Vlastnost  | Popis                                               |
| --------- | --------------------------------------------------------- |
| _Tělo_    | Objekt, který obsahuje tělo odpovědi.         |
| _Záhlaví_ | Objekt, který obsahuje hlavičky odpovědí.             |
| _isRaw_   | Označuje, že formátování je přeskočeno pro odpověď.    |
| _status_  | Stavový kód HTTP odpovědi.                     |
| _Soubory cookie_ | Pole objektů souborů cookie HTTP, které jsou nastaveny v odpovědi. Objekt souboru cookie `name` `value`HTTP má , a `maxAge` další `sameSite`vlastnosti souboru cookie, například nebo . |

### <a name="accessing-the-request-and-response"></a>Přístup k požadavku a odpovědi 

Při práci s aktivačními událostmi HTTP můžete přistupovat k objektům požadavků a odpovědí HTTP několika způsoby:

+ **Od `req` `res` a vlastnosti na objektu. `context`** Tímto způsobem můžete použít konvenční vzor pro přístup k datům PROTOKOLU HTTP `context.bindings.name` z objektu kontextu, namísto nutnosti použít úplný vzor. Následující příklad ukazuje, jak `req` `res` získat přístup `context`k objektům a na :

    ```javascript
    // You can access your HTTP request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your HTTP response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Z pojmenované vstupní a výstupní vazby.** Tímto způsobem aktivační událost HTTP a vazby pracovat stejně jako všechny ostatní vazby. Následující příklad nastaví objekt odpovědi `response` pomocí pojmenované vazby: 

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
+ **_[Pouze odpověď]_ Voláním `context.res.send(body?: any)`.** Odpověď HTTP je vytvořena `body` se vstupem jako tělo odpovědi. `context.done()`implicitně volána.

+ **_[Pouze odpověď]_ Voláním `context.done()`.** Zvláštní typ vazby HTTP vrátí odpověď, `context.done()` která je předána metodě. Následující výstupní vazba `$return` PROTOKOLU HTTP definuje výstupní parametr:

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

## <a name="scaling-and-concurrency"></a>Změna velikosti a souběžnost

Ve výchozím nastavení funkce Azure automaticky monitoruje zatížení vaší aplikace a podle potřeby vytvoří další instance hostitele pro node.js. Funkce používá vestavěné (nikoli uživatelsky konfigurovatelné) prahové hodnoty pro různé typy aktivačních událostí k rozhodnutí, kdy přidat instance, jako je stáří zpráv a velikost fronty pro QueueTrigger. Další informace najdete [v tématu Jak fungují plány Spotřeba a Premium](functions-scale.md#how-the-consumption-and-premium-plans-work).

Toto chování škálování je dostačující pro mnoho aplikací Node.js. U aplikací vázaných na procesor můžete dále zlepšit výkon pomocí více jazykových pracovních procesů.

Ve výchozím nastavení má každá instance hostitele Functions pracovní proces v jednom jazyce. Pomocí nastavení [aplikace FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) můžete zvýšit počet pracovních procesů na hostitele (až 10). Azure Functions se pak pokusí rovnoměrně distribuovat souběžné vyvolání funkcí napříč těmito pracovníky. 

FUNCTIONS_WORKER_PROCESS_COUNT platí pro každého hostitele, který funkce vytvoří při škálování z vaší aplikace tak, aby splňovaly požadavky. 

## <a name="node-version"></a>Verze uzlu

V následující tabulce jsou uvedeny aktuální podporované verze node.js pro každou hlavní verzi modulu runtime Functions podle operačního systému:

| Verze funkcí | Verze uzlu (Windows) | Verze uzlu (Linux) |
|---|---| --- |
| 1.x | 6.11.2 (uzamčeno za běhu) | neuvedeno |
| 2.x  | ~8<br/>~10 (doporučeno)<br/>~12<sup>*</sup> | ~8 (doporučeno)<br/>~10  |
| 3.x | ~10<br/>~12 (doporučeno)  | ~10<br/>~12 (doporučeno) |

<sup>*</sup>Uzel ~12 je aktuálně povolen ve verzi 2.x běhu Functions. Pro nejlepší výkon však doporučujeme použít functions runtime verze 3.x s uzětem ~12. 

Aktuální verzi, kterou runtime používá, můžete zobrazit kontrolou výše uvedeného nastavení aplikace nebo tiskem `process.version` z libovolné funkce. Zacilte na verzi v Azure nastavením [nastavení](functions-how-to-use-azure-function-app-settings.md#settings) WEBSITE_NODE_DEFAULT_VERSION aplikace `~10`na podporovanou verzi LTS, například .

## <a name="dependency-management"></a>Správa závislostí
Chcete-li v kódu JavaScriptu používat komunitní knihovny, jak je znázorněno v níže uvedeném příkladu, musíte zajistit, aby se všechny závislosti nainstalovaly do aplikace Function App v Azure.

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
> Měli byste `package.json` definovat soubor v kořenovém adresáři aplikace funkce. Definování souboru umožňuje, aby všechny funkce v aplikaci sdílely stejné balíčky uložené v mezipaměti, což poskytuje nejlepší výkon. Pokud dojde ke konfliktu verze, můžete jej `package.json` vyřešit přidáním souboru do složky určité funkce.  

Při nasazování aplikací funkcí `package.json` ze správy zdrojového kódu, `npm install` jakýkoli soubor v repo, spustí ve své složce během nasazení. Ale při nasazování přes portál nebo CLI, budete muset ručně nainstalovat balíčky.

Existují dva způsoby instalace balíčků do aplikace Function: 

### <a name="deploying-with-dependencies"></a>Nasazení pomocí závislostí
1. Nainstalujte všechny potřebné balíčky `npm install`místně spuštěním .

2. Nasaďte kód a ujistěte se, že `node_modules` složka je součástí nasazení. 


### <a name="using-kudu"></a>Použití Kudu
1. Přejděte do části `https://<function_app_name>.scm.azurewebsites.net` (Soubor > Nový > Jiné).

2. Klepněte na **položku Ladění konzoly** > **CMD**.

3. Přejděte `D:\home\site\wwwroot`na soubor package.json a přetáhněte jej do složky **wwwroot** v horní polovině stránky.  
    Soubory můžete do aplikace funkcí nahrávat i jinými způsoby. Další informace naleznete v tématu [Jak aktualizovat soubory aplikací funkcí](functions-reference.md#fileupdate). 

4. Po nahrání souboru package.json `npm install` spusťte příkaz v **konzole vzdáleného spuštění Kudu**.  
    Tato akce stáhne balíčky uvedené v souboru package.json a restartuje aplikaci funkce.

## <a name="environment-variables"></a>Proměnné prostředí

V funkcích jsou [nastavení aplikace](functions-app-settings.md), například připojovací řetězce služby, během provádění vystavena jako proměnné prostředí. K těmto nastavením `process.env`můžete přistupovat pomocí , jak `context.log()` je znázorněno zde v druhém a třetím volání, kde zaznamenáváme proměnné `AzureWebJobsStorage` prostředí a `WEBSITE_SITE_NAME` proměnné prostředí:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Při místním spuštění se nastavení aplikace načtou ze souboru projektu [local.settings.json.](functions-run-local.md#local-settings-file)

## <a name="configure-function-entry-point"></a>Konfigurace vstupního bodu funkce

`function.json` Vlastnosti `scriptFile` `entryPoint` a lze použít ke konfiguraci umístění a názvu exportované funkce. Tyto vlastnosti mohou být důležité při transponování JavaScriptu.

### <a name="using-scriptfile"></a>Použití metody `scriptFile`

Ve výchozím nastavení je funkce JavaScript uváděna ze `index.js`souboru , který sdílí stejný nadřazený adresář jako odpovídající . `function.json`

`scriptFile`lze získat strukturu složek, která vypadá jako následující příklad:

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

For `function.json` `myNodeFunction` by měl `scriptFile` obsahovat vlastnost ukazující na soubor s exportovnou funkcí, která má být spuštěna.

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Použití metody `entryPoint`

V `scriptFile` (nebo `index.js`) musí být `module.exports` funkce exportována pomocí, aby mohla být nalezena a spuštěna. Ve výchozím nastavení je funkce, která se spustí při aktivaci, jediným exportem z tohoto souboru, exportem s názvem `run`nebo exportem s názvem `index`.

To lze nakonfigurovat pomocí `entryPoint` v `function.json`, jako v následujícím příkladu:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

Ve functions v2.x, `this` který podporuje parametr v uživatelských funkcích, může být kód funkce stejně jako v následujícím příkladu:

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

V tomto příkladu je důležité si uvědomit, že i když je objekt exportován, neexistují žádné záruky pro zachování stavu mezi spuštěními.

## <a name="local-debugging"></a>Místní ladění

Při spuštění `--inspect` s parametrem proces Node.js naslouchá ladicímu klientovi na zadaném portu. V Azure Functions 2.x můžete zadat argumenty, které mají být předávány do procesu Node.js, který spouští váš kód přidáním proměnné prostředí nebo nastavení `languageWorkers:node:arguments = <args>`aplikace . 

Chcete-li ladit místně, přidejte `"languageWorkers:node:arguments": "--inspect=5858"` pod v souboru `Values` [local.settings.json](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file) a připojte ladicí program k portu 5858.

Při ladění pomocí kódu VS `--inspect` je parametr automaticky `port` přidán pomocí hodnoty v souboru launch.json projektu.

Ve verzi 1.x `languageWorkers:node:arguments` nebude nastavení fungovat. Ladicí port lze vybrat [`--nodeDebugPort`](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start) s parametrem v nástrojích Azure Functions Core Tools.

## <a name="typescript"></a>TypeScript

Když cílíte na verzi 2.x runtime Functions, azure [functions for Visual Studio Code](functions-create-first-function-vs-code.md) a nástroje Azure Functions Core [Tools](functions-run-local.md) vám umožní vytvářet aplikace funkcí pomocí šablony, která podporuje projekty aplikací funkcí TypeScript. Šablona generuje `package.json` `tsconfig.json` a projektové soubory, které usnadňují transpile, spouštění a publikování funkcí Jazyka JavaScript z kódu Typu Script pomocí těchto nástrojů.

Generovaný `.funcignore` soubor se používá k označení, které soubory jsou vyloučeny při publikování projektu do Azure.  

Soubory TypeScript (.ts) jsou ve výstupním adresáři `dist` transponovány do souborů JavaScriptu (.js). Šablony jazyka TypeScript používají `function.json` [ `scriptFile` parametr](#using-scriptfile) v aplikace k označení umístění `dist` odpovídajícího souboru JS ve složce. Výstupní umístění je nastaveno šablonou `outDir` pomocí `tsconfig.json` parametru v souboru. Pokud změníte toto nastavení nebo název složky, runtime nebude moci najít kód, který chcete spustit.

> [!NOTE]
> Experimentální podpora pro TypeScript existuje verze 1.x běhu Funkce. Experimentální verze transpiles TypeScript soubory do javascriptových souborů, když je vyvolána funkce. Ve verzi 2.x byla tato experimentální podpora nahrazena metodou řízenou nástrojem, která provádí transpilaci před inicializací hostitele a během procesu nasazení.

Způsob, jakým místně vyvíjet a nasazovat z projektu TypeScript závisí na vývojový nástroj.

### <a name="visual-studio-code"></a>Visual Studio Code

Rozšíření [Kódu Funkce Azure pro Visual Studio](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) umožňuje vyvíjet funkce pomocí TypeScriptu. Základní nástroje je požadavek rozšíření Azure Functions.

Chcete-li vytvořit aplikaci funkce TypeScript `TypeScript` v kódu Sady Visual Studio, zvolte při vytváření aplikace funkce jako jazyk.

Když stisknete **Klávesu F5,** abyste aplikaci spouštěli místně, transpilace se provádí před inicializací hostitele (func.exe). 

Když nasadíte aplikaci funkce do Azure pomocí **aplikace Deploy to function... tlačítko,** rozšíření Azure Functions nejprve generuje sestavení javascriptových souborů připravených pro produkční prostředí ze zdrojových souborů TypeScript.

### <a name="azure-functions-core-tools"></a>Základní nástroje Azure Functions

Existuje několik způsobů, jak se projekt Jazyka TypeScript liší od projektu Jazyka JavaScript při použití základních nástrojů.

#### <a name="create-project"></a>Vytvoření projektu

Chcete-li vytvořit projekt aplikace funkce Jazyka TypeScript pomocí základních nástrojů, musíte při vytváření aplikace pro funkci určit volbu Jazyka TypeScript. Můžete to provést jedním z následujících způsobů:

- Spusťte `func init` příkaz, vyberte `node` jako zásobník `typescript`jazyků a pak vyberte .

- Spusťte příkaz `func init --worker-runtime typescript`.

#### <a name="run-local"></a>Spustit místní

Chcete-li kód aplikace funkce spustit místně pomocí základních nástrojů, použijte místo následujících `func host start`příkazů: 

```command
npm install
npm start
```

Příkaz `npm start` je ekvivalentní následujícím příkazům:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>Publikování aplikací do Azure

Před použitím [`func azure functionapp publish`] příkazu k nasazení do Azure vytvoříte sestavení javascriptových souborů připravené pro produkční prostředí ze zdrojových souborů Typu Script. 

Následující příkazy připravují a publikují projekt jazyka TypeScript pomocí základních nástrojů: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

V tomto příkazu nahraďte `<APP_NAME>` názvem aplikace funkce.

## <a name="considerations-for-javascript-functions"></a>Důležité informace o funkcích JavaScriptu

Při práci s funkcemi jazyka JavaScript mějte na paměti důležité informace v následujících částech.

### <a name="choose-single-vcpu-app-service-plans"></a>Výběr plánů služby aplikace s jedním virtuálním procesorem

Když vytvoříte aplikaci funkce, která používá plán služby App Service, doporučujeme vybrat plán s jedním virtuálním procesorem, nikoli plán s více virtuálními procesory. Funkce dnes spouští funkce JavaScriptu efektivněji na virtuálních počítačích s jedním virtuálním procesorem a použití větších virtuálních počítačů nevede k očekávanému zlepšení výkonu. V případě potřeby můžete ručně horizontální navýšení kapacity přidáním dalších instancí virtuálního počítače s jedním virtuálním procesorem nebo můžete povolit automatické škálování. Další informace naleznete v tématu [Škálování počtu instancí ručně nebo automaticky](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json).

### <a name="cold-start"></a>Studený start

Při vývoji funkcí Azure v modelu hostování bez serveru jsou studené starty realitou. *Studený start* odkazuje na skutečnost, že při spuštění aplikace funkce poprvé po určité době nečinnosti trvá spuštění déle. Pro funkce JavaScriptu s velkými stromy závislostí zejména může být studený start významný. Chcete-li urychlit proces studeného startu, [spusťte funkce jako soubor balíčku,](run-functions-from-deployment-package.md) pokud je to možné. Mnoho metod nasazení používá spuštění z modelu balíčku ve výchozím nastavení, ale pokud dochází k velké studené starty a nejsou spuštěny tímto způsobem, tato změna může nabídnout významné zlepšení.

### <a name="connection-limits"></a>Limity připojení

Při použití klienta specifického pro službu v aplikaci Azure Functions, nevytvářejte nového klienta s každou vyvolání funkce. Místo toho vytvořte jednoho statického klienta v globálním oboru. Další informace najdete [v tématu správa připojení v Azure Functions](manage-connections.md).

### <a name="use-async-and-await"></a>Použití `async` a`await`

Při psaní funkce Azure v JavaScriptu, `async` `await` měli byste napsat kód pomocí a klíčová slova. Psaní kódu `async` `await` pomocí a místo `.then` zpětná volání nebo a `.catch` s Promises pomáhá vyhnout se dvěma běžným problémům:
 - Vyvolání nezachycené výjimky, které [selhání procesu Node.js](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly), potenciálně ovlivňuje provádění dalších funkcí.
 - Neočekávané chování, jako je například chybějící protokoly z context.log, způsobené asynchronní volání, které nejsou správně očekávány.

V níže uvedeném příkladu je `fs.readFile` asynchronní metoda vyvolána s funkcí zpětného volání první chyby jako druhý parametr. Tento kód způsobí, že oba výše uvedené problémy. Výjimka, která není explicitně zachycena ve správném oboru, havarovala celý proces (problém #1). Volání `context.done()` mimo rozsah funkce zpětného volání znamená, že vyvolání funkce může skončit před čtením souboru (problém #2). V tomto příkladu volání `context.done()` příliš brzy výsledky `Data from file:`chybějící položky protokolu počínaje .

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

`async` Použití `await` a klíčová slova pomáhá vyhnout se oběma tyto chyby. Pomocí funkce [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) nástroje Node.js byste měli změnit funkce ve stylu zpětného volání první chyby na dostupné funkce.

V níže uvedeném příkladu všechny neošetřené výjimky vyvolané během spuštění funkce pouze nezdaří jednotlivé vyvolání, která vyvolala výjimku. Klíčové `await` slovo znamená, `readFileAsync` že `readFile` následující kroky se spustí až po dokončení. S `async` `await`a , také není nutné `context.done()` volat zpětné volání.

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

Další informace najdete v následujících materiálech:

+ [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
+ [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
+ [Azure Funkce aktivační události a vazby](functions-triggers-bindings.md)

['func azure functionapp publish']: functions-run-local.md#project-file-deployment
