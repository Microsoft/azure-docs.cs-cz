---
title: Referenční informace pro vývojáře JavaScript pro Azure Functions | Microsoft Docs
description: Pochopit, jak vyvíjet funkce pomocí jazyka JavaScript.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: funkce azure, funkce, zpracování událostí, webhook, dynamické výpočty, architektura bez serverů
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: functions
ms.devlang: nodejs
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/04/2018
ms.author: tdykstra
ms.openlocfilehash: 78f29cd4a20861e40bb7f7f398979b8d93387a7b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="azure-functions-javascript-developer-guide"></a>Příručka vývojáře Azure funkce JavaScript

Možnosti JavaScript Azure Functions umožňuje snadno exportovat funkci, která se předá jako `context` objekt pro komunikaci s modulem runtime a pro příjem a odesílání dat přes vazby.

Tento článek předpokládá, že jste si již přečetli [referenční informace pro vývojáře Azure Functions](functions-reference.md).

## <a name="exporting-a-function"></a>Export funkce
Všechny funkce jazyka JavaScript, musíte exportovat jedné `function` prostřednictvím `module.exports` pro modul runtime vyhledat funkci a spustit ho. Tato funkce musí vždy zahrnovat `context` objektu.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // Additional inputs can be accessed by the arguments property
    if(arguments.length === 4) {
        context.log('This function has 4 inputs');
    }
};
// or you can include additional inputs in your arguments
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
};
```

Vazeb `direction === "in"` se předají jako argumenty funkce, což znamená, že můžete používat [ `arguments` ](https://msdn.microsoft.com/library/87dw3w1k.aspx) dynamicky zpracovat nové vstupy (například pomocí `arguments.length` Iterujte přes všechny vstupy). Tato funkce je vhodné, pokud máte pouze aktivační událost a žádné další vstupy, protože vaše data aktivační události můžete předvídatelné přístup bez odkazující na váš `context` objektu.

Argumenty, které jsou vždy funkci byl předán společně v pořadí, ve které se vyskytují v *function.json*i v případě, že je v příkazu exportuje nezadáte. Pokud máte například `function(context, a, b)` a změňte ji na `function(context, a)`, stále můžete získat hodnotu `b` v kódu funkce tím, že odkazuje na `arguments[2]`.

Všechny vazby, bez ohledu na směru, jsou také předají `context` objektu (viz následující skript). 

## <a name="context-object"></a>objekt kontextu
Modul runtime používá `context` objekt k předávání dat do a z funkce a umožnit vám komunikovat s modulem runtime.

`context` Objekt je vždy první parametr funkce a musí být zahrnut, protože obsahuje metody, jako `context.done` a `context.log`, které jsou nutné k využití modulu runtime správně. Můžete pojmenovat objekt ať chcete (například `ctx` nebo `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

### <a name="contextbindings-property"></a>Vlastnost Context.Bindings

```
context.bindings
```
Vrátí objekt s názvem, který obsahuje všechny vaše vstupní a výstupní data. Například následující definice vazby v vaše *function.json* umožňuje přístup k obsahu z fronty `context.bindings.myInput` objektu. 

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
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

### <a name="contextdone-method"></a>Context.Done – metoda
```
context.done([err],[propertyBag])
```

Informuje o modul runtime, který váš kód byl dokončen. Je třeba volat `context.done`, nebo jinak se modul runtime nikdy ví, že funkce je dokončena a provádění bude časový limit. 

`context.done` Metoda umožňuje předat zpět i uživatelem definované chybové modul runtime a kontejner objektů a vlastností, který přepíše vlastnosti na `context.bindings` objektu.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

### <a name="contextlog-method"></a>Context.log – metoda  

```
context.log(message)
```
Umožňuje zapisovat do protokolů streamování konzoly na výchozí úrovni trasování. Na `context.log`, další metody protokolování jsou k dispozici, který umožňuje zapisovat do protokolu konzoly na jiných úrovních trasování:


| Metoda                 | Popis                                |
| ---------------------- | ------------------------------------------ |
| **Chyba (_zpráva_)**   | Zapíše chyba úroveň protokolování nebo nižší.   |
| **warn (_zpráva_)**    | Zapíše do varovná úroveň protokolování nebo nižší. |
| **informace o (_zpráva_)**    | Zapíše informace o úroveň protokolování nebo nižší.    |
| **verbose (_zpráva_)** | Zapíše na podrobné úrovni protokolování.           |

V následujícím příkladu se zapíše do konzoly na úrovni trasování upozornění:

```javascript
context.log.warn("Something has happened."); 
```
Můžete nastavit prahovou hodnotu úroveň trasování pro protokolování v souboru host.json nebo ho vypnout.  Další informace o tom, jak zapsat do protokolů, najdete v další části.

## <a name="binding-data-type"></a>Datový typ vazby

Datový typ pro vstupní vazbu, použijte `dataType` vlastnost v definici vazby. Například pokud chcete číst obsah požadavku HTTP v binárním formátu, použijte typ `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Další možnosti pro `dataType` jsou `stream` a `string`.

## <a name="writing-trace-output-to-the-console"></a>Zápis trasování výstup do konzoly 

Ve funkcích, můžete použít `context.log` metody k zápisu výstupu trasování do konzoly. V tomto okamžiku nelze použít `console.log` k zápisu do konzoly.

Při volání `context.log()`, zprávy se zapíše do konzoly na výchozí úrovni trasování, která je _informace_ úroveň trasování. Následující kód zapíše do konzoly na úrovni trasování informace:

```javascript
context.log({hello: 'world'});  
```

Předchozí kód je ekvivalentní následující kód:

```javascript
context.log.info({hello: 'world'});  
```

Následující kód zapíše do konzoly na úrovni Chyba:

```javascript
context.log.error("An error has occurred.");  
```

Protože _chyba_ je trasování nejvyšší úrovně, toto trasování bude zapsáno do výstupu na všech úrovních trasování, dokud je povoleno protokolování.  


Všechny `context.log` metody podporují stejný formát parametr, který je podporován Node.js [util.format metoda](https://nodejs.org/api/util.html#util_util_format_format). Vezměte v úvahu následující kód, který zapisuje do konzoly pomocí výchozí úroveň trasování:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Můžete taky napsat stejný kód v následujícím formátu:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Nakonfiguruje úroveň trasování pro protokolování konzoly

Funkce umožňuje definovat úroveň trasování prahová hodnota pro zápis do konzoly, která usnadňuje řízení, které trasování způsob, jak se zapisují do konzoly z funkcí. Pokud chcete nastavit prahovou hodnotu pro všechny trasování zapsána do konzoly, použijte `tracing.consoleLevel` vlastnost v souboru host.json. Toto nastavení platí pro všechny funkce v aplikaci funkce. Následující příklad nastavuje mezní hodnotu trasování Zapnutí podrobného protokolování:

```json
{ 
    "tracing": {      
        "consoleLevel": "verbose"     
    }
}  
```

Hodnoty z **consoleLevel** odpovídají názvy `context.log` metody. Chcete-li zakázat veškeré protokolování trasování do konzoly, nastavte **consoleLevel** k _vypnout_. Další informace najdete v tématu [host.json odkaz](functions-host-json.md).

## <a name="http-triggers-and-bindings"></a>HTTP triggerů a vazeb

HTTP a aktivační události webhooku a HTTP výstupu vazby používají žádosti a odpovědi objekty k reprezentaci zasílání zpráv protokolu HTTP.  

### <a name="request-object"></a>Objekt žádosti

`request` Objekt má následující vlastnosti:

| Vlastnost      | Popis                                                    |
| ------------- | -------------------------------------------------------------- |
| _Text_        | Objekt, který obsahuje text žádosti.               |
| _Záhlaví_     | Objekt, který obsahuje hlavičky žádosti.                   |
| _– Metoda_      | Metoda HTTP požadavku.                                |
| _PůvodníAdresaURL_ | Adresa URL požadavku.                                        |
| _params_      | Objekt, který obsahuje parametry směrování žádosti. |
| _Dotaz_       | Objekt, který obsahuje parametry dotazu.                  |
| _rawBody_     | Tělo zprávy jako řetězec.                           |


### <a name="response-object"></a>Objekt odpovědi

`response` Objekt má následující vlastnosti:

| Vlastnost  | Popis                                               |
| --------- | --------------------------------------------------------- |
| _Text_    | Objekt, který obsahuje text odpovědi.         |
| _Záhlaví_ | Objekt, který obsahuje hlavičky odpovědi.             |
| _isRaw_   | Označuje, že formátování bylo přeskočeno pro odpověď.    |
| _Stav_  | Stavový kód HTTP odpovědi.                     |

### <a name="accessing-the-request-and-response"></a>Přístup k požadavku a odpovědi 

Při práci s aktivace protokolu HTTP, se můžete dostat objekty žádosti a odpovědi protokolu HTTP v jednom ze tří způsobů:

+ Z pojmenované vstup a výstup vazby. Tímto způsobem triggeru protokolu HTTP a vazeb fungovat stejně jako ostatní vazby. Následující příklad nastaví objekt odpovědi pomocí pojmenovaná `response` vazby: 

    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```

+ Z `req` a `res` vlastnosti `context` objektu. Tímto způsobem můžete použít konvenční vzor pro přístup protokolu HTTP k datům z objektu context, místo nutnosti použít celý `context.bindings.name` vzor. Následující příklad ukazuje, jak získat přístup `req` a `res` objekty na `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ Při volání `context.done()`. Zvláštní druh vazby HTTP vrátí odpověď, který je předán `context.done()` metoda. Následující HTTP výstup vazba definuje `$return` výstupní parametr:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    Tato vazba výstup očekává, kde zadáte odpovědi při volání `done()`, a to takto:

    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version-and-package-management"></a>Správa verzí a balíček uzlu

Následující tabulka uvádí verze Node.js používané každou hlavní verzi modulu runtime funkce:

| Verze funkcí | Verze Node.js | 
|---|---|
| 1.x | 6.11.2 (uzamčený modulu runtime) |
| 2.x  |> = 8.4.0 s aktuální LTS 8.9.4 nedoporučuje. Nastavit verzi pomocí WEBSITE_NODE_DEFAULT_VERSION [nastavení aplikace](functions-how-to-use-azure-function-app-settings.md#settings).|

Zobrazí aktuální verzi, která používá modul runtime tisk `process.version` z žádné funkce.

Následující kroky umožňují mezi ně patřit balíčky v aplikaci funkce: 

1. Přejděte do části `https://<function_app_name>.scm.azurewebsites.net` (Soubor > Nový > Jiné).

2. Klikněte na tlačítko **ladění konzoly** > **CMD**.

3. Přejděte na `D:\home\site\wwwroot`a poté přetáhněte souboru package.json k **wwwroot** složku v horní polovině stránky.  
    Soubory můžete nahrát také do vaší aplikace funkce jinými způsoby. Další informace najdete v tématu [jak aktualizovat soubory aplikace funkce](functions-reference.md#fileupdate). 

4. Po nahrání souboru package.json, spusťte `npm install` v příkazu **Kudu vzdálené spuštění konzoly**.  
    Tato akce stáhne balíčky uvedené v souboru package.json a restartuje aplikaci funkce.

Po instalaci balíčků, které potřebujete, můžete je importovat do funkce voláním `require('packagename')`, jako v následujícím příkladu:

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

Měli byste `package.json` souboru v kořenu aplikace funkce. Definování souboru umožňuje všechny funkce v aplikaci sdílet stejné balíčky v mezipaměti, který poskytuje nejlepší výkon. Pokud dojde ke konfliktu verze, abyste ho mohli vyřešit přidáním `package.json` soubor ve složce konkrétní funkce.  

## <a name="environment-variables"></a>Proměnné prostředí
Proměnné prostředí nebo nastavení hodnoty aplikace, použijte `process.env`, jak je vidět tady v `GetEnvironmentVariable` funkce:

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
## <a name="considerations-for-javascript-functions"></a>Důležité informace týkající se funkce jazyka JavaScript

Pokud pracujete s funkce jazyka JavaScript, nezapomeňte aspekty uvedené v následujících dvou částech.

### <a name="choose-single-vcpu-app-service-plans"></a>Vyberte virtuální jeden procesor plány služby App Service

Když vytvoříte aplikaci funkce, která používá plán služby App Service, doporučujeme vybrat plán single-virtuální procesory než plán s více Vcpu. V současné době funkce jazyka JavaScript funkce efektivněji běží na virtuálních počítačích jedním-virtuální procesory a větší virtuální počítače pomocí nevytváří vylepšení očekávaný výkon. Pokud je to nezbytné, můžete ručně škálovat tak, že přidáte více instancí virtuálního počítače virtuální jeden procesor nebo můžete povolit automatické škálování. Další informace najdete v tématu [ruční nebo automatické škálování počtu instancí](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json).    

### <a name="typescript-and-coffeescript-support"></a>Podpora typeScript a CoffeeScript
Protože přímé podpory ještě neexistuje pro automatické kompilaci TypeScript nebo CoffeeScript prostřednictvím modulu runtime, tato podpora se musí zpracovávat mimo modulu runtime v době nasazení. 

## <a name="next-steps"></a>Další postup
Další informace najdete v následujících materiálech:

* [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
* [Azure funkce triggerů a vazeb](functions-triggers-bindings.md)

