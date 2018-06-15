---
title: Testování funkcí Azure Functions | Microsoft Docs
description: Azure functions otestujte pomocí Postman, cURL a Node.js.
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: ''
tags: ''
keywords: Azure funkce, funkce, zpracování událostí, webhooků, dynamické výpočetní, bez serveru architektura testování
ms.assetid: c00f3082-30d2-46b3-96ea-34faf2f15f77
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/02/2017
ms.author: wesmc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 41796a8cdde0756e5157ba276463a56b07679d04
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23838951"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Strategie pro testování kódu v Azure Functions

Toto téma popisuje různé způsoby, kterými k testování funkcí, včetně použití následující obecné přístupy:

+ Nástroje založené na protokolu HTTP, například cURL, Postman a i webový prohlížeč pro webové aktivační procedury
+ Azure Storage Explorer k testování aktivační události na základě Azure Storage
+ Karta testu na portálu Azure Functions
+ Spustí časovač – funkce
+ Testování aplikace nebo framework

Všechny tyto metody testování pomocí funkce aktivační událost HTTP, který přijímá vstupu prostřednictvím parametr řetězce dotazu nebo textu požadavku. V první části vytvoříte tuto funkci.

## <a name="create-a-function-for-testing"></a>Vytvořit funkci pro testování
Pro většinu v tomto kurzu používáme mírně upravenou verzi šablony funkce HttpTrigger JavaScript, která je dostupná, když vytvoříte funkci. Pokud potřebujete pomoc, vytváření funkce, přečtěte si to [kurzu](functions-create-first-azure-function.md). Vyberte **HttpTrigger - JavaScript** při vytváření funkci test v šabloně [portál Azure].

Výchozí šablony funkce je v podstatě funkce "hello, world", která vrátí zpět názvem z požadavku textu nebo dotaz, řetězec parametr `name=<your name>`.  Budeme budete aktualizovat kód, který taky umožňují zadat název a adresu jako obsah JSON v textu požadavku. Funkce pak vrátí tyto zpět klientovi, pokud je k dispozici.   

Aktualizujte funkce následující kód, který budeme používat pro testování:

```javascript
module.exports = function (context, req) {
    context.log("HTTP trigger function processed a request. RequestUri=%s", req.originalUrl);
    context.log("Request Headers = " + JSON.stringify(req.headers));
    var res;

    if (req.query.name || (req.body && req.body.name)) {
        if (typeof req.query.name != "undefined") {
            context.log("Name was provided as a query string param...");
            res = ProcessNewUserInformation(context, req.query.name);
        }
        else {
            context.log("Processing user info from request body...");
            res = ProcessNewUserInformation(context, req.body.name, req.body.address);
        }
    }
    else {
        res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done(null, res);
};
function ProcessNewUserInformation(context, name, address) {
    context.log("Processing user information...");
    context.log("name = " + name);
    var echoString = "Hello " + name;
    var res;

    if (typeof address != "undefined") {
        echoString += "\n" + "The address you provided is " + address;
        context.log("address = " + address);
    }
    res = {
        // status: 200, /* Defaults to 200 */
        body: echoString
    };
    return res;
}
```

## <a name="test-a-function-with-tools"></a>Testování funkce nástroje
Mimo portál Azure existují různé nástroje, které můžete použít k aktivaci funkcí pro testování. Mezi ně patří HTTP testování nástroje (na základě uživatelského rozhraní i příkaz řádku), nástroje pro Azure Storage přístupu a i jednoduchý webový prohlížeč.

### <a name="test-with-a-browser"></a>Testování s prohlížečem
Webový prohlížeč je jednoduchý způsob, jak funkce aktivační události prostřednictvím protokolu HTTP. Můžete použít prohlížeč pro požadavky GET, které nevyžadují datovou část textu a pouze dotazu použijte řetězec parametry.

Chcete-li otestovat funkci definovaného dříve, zkopírujte **Url funkce** z portálu. Má následující tvar:

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Připojení `name` parametru řetězce dotazu. Použijte skutečný název pro `<Enter a name here>` zástupný symbol.

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>&name=<Enter a name here>

Vložte adresu URL do prohlížeče a měli byste obdržet odpověď podobný následujícímu.

![Snímek obrazovky Chrome kartu prohlížeče s odpovědí testu](./media/functions-test-a-function/browser-test.png)

V tomto příkladu je prohlížeč Chrome, který zabalí vrácený řetězec v kódu XML. Jiné prohlížeče zobrazí hodnotu řetězce.

Na portálu **protokoly** okně výstup podobný následujícímu přihlášen provádění funkce:

    2016-03-23T07:34:59  Welcome, you are now connected to log-streaming service.
    2016-03-23T07:35:09.195 Function started (Id=61a8c5a9-5e44-4da0-909d-91d293f20445)
    2016-03-23T07:35:10.338 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==&name=Glenn from a browser
    2016-03-23T07:35:10.338 Request Headers = {"cache-control":"max-age=0","connection":"Keep-Alive","accept":"text/html","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T07:35:10.338 Name was provided as a query string param.
    2016-03-23T07:35:10.338 Processing User Information...
    2016-03-23T07:35:10.369 Function completed (Success, Id=61a8c5a9-5e44-4da0-909d-91d293f20445)

### <a name="test-with-postman"></a>Testování s Postman
Doporučeným nástrojem pro testování většinu funkcí je Postman, který se integruje s prohlížeč Chrome. Instalaci Postman naleznete v tématu [získat Postman](https://www.getpostman.com/). Postman umožňuje řídit mnoho dalších atributů požadavku HTTP.

> [!TIP]
> Pomocí protokolu HTTP testování nástroj, který jste nejvíce vyhovuje. Zde jsou některé alternativy Postman:  
>
> * [Fiddler](http://www.telerik.com/fiddler)  
> * [Tlapa](https://luckymarmot.com/paw)  
>
>

Testování funkce se obsah žádosti v Postman:

1. Spustit Postman z **aplikace** tlačítko v levém horním rohu okna prohlížeče Chrome.
2. Kopie vašeho **Url funkce**a vložte jej do Postman. Obsahuje parametr řetězce dotazu přístupový kód.
3. Změnit metodu protokolu HTTP k **POST**.
4. Klikněte na tlačítko **textu** > **nezpracovaná**, a přidejte textu požadavku JSON, která je podobný následujícímu:

    ```json
    {
        "name" : "Wes testing with Postman",
        "address" : "Seattle, WA 98101"
    }
    ```
5. Klikněte na tlačítko **odeslat**.

Následující obrázek ukazuje příklad funkce jednoduché echo testování v tomto kurzu.

![Snímek obrazovky Postman uživatelské rozhraní](./media/functions-test-a-function/postman-test.png)

Na portálu **protokoly** okně výstup podobný následujícímu přihlášen provádění funkce:

    2016-03-23T08:04:51  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:04:57.107 Function started (Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)
    2016-03-23T08:04:57.763 HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==
    2016-03-23T08:04:57.763 Request Headers = {"cache-control":"no-cache","connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T08:04:57.763 Processing user info from request body...
    2016-03-23T08:04:57.763 Processing User Information...
    2016-03-23T08:04:57.763 name = Wes testing with Postman
    2016-03-23T08:04:57.763 address = Seattle, W.A. 98101
    2016-03-23T08:04:57.795 Function completed (Success, Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)

### <a name="test-with-curl-from-the-command-line"></a>Testování pomocí cURL z příkazového řádku
Často Pokud testujete softwaru, není nutné žádné další než příkazový řádek pro pomáhají ladit aplikace vypadat. Nijak se to neliší při testování funkcí. Všimněte si, že cURL je k dispozici ve výchozím nastavení počítače se systémem Linux. V systému Windows, musíte nejdřív stáhnout a nainstalovat [cURL nástroj](https://curl.haxx.se/).

Chcete-li otestovat funkci, která definovaného dříve, zkopírujte **URL funkce** z portálu. Má následující tvar:

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Toto je adresa URL pro spuštění funkce. Tento test pomocí cURL příkazu na příkazovém řádku, aby GET (`-G` nebo `--get`) požadavku vůči funkce:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Tento příklad konkrétní vyžaduje parametr řetězce dotazu, které lze předat jako Data (`-d`) v příkazu cURL:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code> -d name=<Enter a name here>

Spusťte příkaz a zobrazí se následující výstup funkce na příkazovém řádku:

![Výstup – snímek obrazovky příkazového řádku](./media/functions-test-a-function/curl-test.png)

Na portálu **protokoly** okně výstup podobný následujícímu přihlášen provádění funkce:

    2016-04-05T21:55:09  Welcome, you are now connected to log-streaming service.
    2016-04-05T21:55:30.738 Function started (Id=ae6955da-29db-401a-b706-482fcd1b8f7a)
    2016-04-05T21:55:30.738 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/HttpTriggerNodeJS1?code=XXXXXXX&name=Azure Functions
    2016-04-05T21:55:30.738 Function completed (Success, Id=ae6955da-29db-401a-b706-482fcd1b8f7a)

### <a name="test-a-blob-trigger-by-using-storage-explorer"></a>Testovací aktivační události objektu blob pomocí Průzkumníka úložiště
Funkce aktivační události objektu blob můžete otestovat pomocí [Azure Storage Explorer](http://storageexplorer.com/).

1. V [portál Azure] pro vaši aplikaci funkce vytvořit jazyka C#, F # nebo JavaScript funkci aktivační události objektu blob. Nastavte cestu k monitorování pro název vašeho kontejneru objektů blob. Například:

        files
2. Klikněte  **+**  tlačítko Vybrat nebo vytvořit účet úložiště, kterou chcete použít. Poté klikněte na **Vytvořit**.
3. Vytvořte textový soubor s následujícím textem a uložte jej:

        A text file for blob trigger function testing.
4. Spustit [Azure Storage Explorer](http://storageexplorer.com/)a připojte se ke kontejneru objektů blob v účtu úložiště, který je monitorován.
5. Klikněte na tlačítko **nahrát** nahrát textový soubor.

    ![Snímek obrazovky Průzkumníka úložiště](./media/functions-test-a-function/azure-storage-explorer-test.png)

Ve výchozím kódu funkce aktivační události objektu blob sestavy zpracování objektů blob v protokolech:

    2016-03-24T11:30:10  Welcome, you are now connected to log-streaming service.
    2016-03-24T11:30:34.472 Function started (Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)
    2016-03-24T11:30:34.472 C# Blob trigger function processed: A text file for blob trigger function testing.
    2016-03-24T11:30:34.472 Function completed (Success, Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)

## <a name="test-a-function-within-functions"></a>Testování funkce v rámci funkcí
Azure Functions portál je navržena tak, aby vám otestovat HTTP a spustí časovač funkce. Můžete také vytvořit funkce pro aktivaci dalších funkcí, které jsou testování.

### <a name="test-with-the-functions-portal-run-button"></a>Testování s funkcí portálu tlačítko Spustit
Poskytuje portálu **spustit** tlačítko, které můžete použít k provedení některých omezené testování. Obsah žádosti můžete zadat pomocí tlačítka, ale nelze zadat parametrů řetězce dotazu nebo aktualizovat hlavičky žádosti.

Testování funkce aktivace protokolu HTTP jsme vytvořili předtím přidáním řetězec formátu JSON, který je podobná následující **text žádosti** pole. Klikněte **spustit** tlačítko.

```json
{
    "name" : "Wes testing Run button",
    "address" : "USA"
}
```

Na portálu **protokoly** okně výstup podobný následujícímu přihlášen provádění funkce:

    2016-03-23T08:03:12  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:03:17.357 Function started (Id=753a01b0-45a8-4125-a030-3ad543a89409)
    2016-03-23T08:03:18.697 HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/wesmchttptriggernodejs1
    2016-03-23T08:03:18.697 Request Headers = {"connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T08:03:18.697 Processing user info from request body...
    2016-03-23T08:03:18.697 Processing User Information...
    2016-03-23T08:03:18.697 name = Wes testing Run button
    2016-03-23T08:03:18.697 address = USA
    2016-03-23T08:03:18.744 Function completed (Success, Id=753a01b0-45a8-4125-a030-3ad543a89409)


### <a name="test-with-a-timer-trigger"></a>Testování s aktivační událost časovače
Některé funkce nelze testovat adekvátní s nástroji pro, již bylo zmíněno dříve. Představte si třeba funkci fronty aktivační událost, která se spustí v případě, že je vyřazeno zprávu do [Azure Queue storage](../storage/queues/storage-dotnet-how-to-use-queues.md). Vždy můžete napsat kód pro vyřadit zprávu do fronty, a příklady v konzolovém projektu se poskytuje později v tomto článku. Je však jiný přístup, které můžete použít, který testuje funkce přímo.  

Můžete použít aktivační událost časovače nakonfigurovaný s frontou výstup vazby. Tento časovač aktivační kód pak můžete napsat zkušebních zpráv do fronty. Tato část vás provede příklad.

Další podrobné informace o používání vazby s Azure Functions najdete v tématu [referenční informace pro vývojáře Azure Functions](functions-reference.md).

#### <a name="create-a-queue-trigger-for-testing"></a>Vytvořit aktivační událost fronty pro testování
K předvedení tento přístup, jsme nejprve vytvořit funkci fronty aktivační událost, která chceme testování pro frontu s názvem `queue-newusers`. Tato funkce zpracovává informace o názvu a adresy do fronty úložiště pro nového uživatele.

> [!NOTE]
> Pokud používáte jiný fronty názvu, ujistěte se, že použijete název odpovídá [pojmenování front a MetaData](https://msdn.microsoft.com/library/dd179349.aspx) pravidla. Jinak dojde k chybě.
>
>

1. V [portál Azure] funkce aplikace, klikněte na tlačítko **novou funkci** > **QueueTrigger - C#**.
2. Zadejte název fronty musí být sledován pomocí funkce fronty:

        queue-newusers
3. Klikněte  **+**  tlačítko Vybrat nebo vytvořit účet úložiště, kterou chcete použít. Poté klikněte na **Vytvořit**.
4. Nechte toto okno prohlížeče portálu otevřené, tak můžete sledovat položky protokolu pro kód výchozí funkce fronty šablony.

#### <a name="create-a-timer-trigger-to-drop-a-message-in-the-queue"></a>Vytvořit aktivační událost časovače odpojení zprávu ve frontě
1. Otevřete [portál Azure] v nové okno prohlížeče a přejděte do aplikaci funkce.
2. Klikněte na tlačítko **novou funkci** > **TimerTrigger - C#**. Zadejte výraz cron nastavit, jak často kód časovače testy funkce fronty. Poté klikněte na **Vytvořit**. Pokud chcete testovací spouštět každých 30 sekund, můžete použít následující [výraz CRON](https://wikipedia.org/wiki/Cron#CRON_expression):

        */30 * * * * *
3. Klikněte **integrací** kartě nové aktivační události časovače.
4. V části **výstup**, klikněte na tlačítko **+ nový výstupní**. Pak klikněte na tlačítko **fronty** a **vyberte**.
5. Poznámka: název, který používáte pro **objekt fronty zpráv**. Můžete použít v kódu funkce časovače.

        myQueue
6. Zadejte název fronty, kde je zpráva odeslána:

        queue-newusers
7. Klikněte  **+**  tlačítko vyberte účet úložiště, který jste použili dříve s aktivační událost fronty. Potom klikněte na **Uložit**.
8. Klikněte **vývoj** kartě aktivační události časovače.
9. Pro funkci jazyka C# časovače, můžete použít následující kód, tak dlouho, dokud jste použili stejný název zpráva objekt fronty, kde je uvedena výše. Potom klikněte na **Uložit**.

    ```cs
    using System;

    public static void Run(TimerInfo myTimer, out String myQueue, TraceWriter log)
    {
        String newUser =
        "{\"name\":\"User testing from C# timer function\",\"address\":\"XYZ\"}";

        log.Verbose($"C# Timer trigger function executed at: {DateTime.Now}");   
        log.Verbose($"{newUser}");   

        myQueue = newUser;
    }
    ```

V tomto okamžiku časovače funkce jazyka C# spustí každých 30 sekund, pokud jste použili výraz cron příklad. Protokoly pro funkci časovače sestav každé spuštění:

    2016-03-24T10:27:02  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.004 Function started (Id=04061790-974f-4043-b851-48bd4ac424d1)
    2016-03-24T10:27:30.004 C# Timer trigger function executed at: 3/24/2016 10:27:30 AM
    2016-03-24T10:27:30.004 {"name":"User testing from C# timer function","address":"XYZ"}
    2016-03-24T10:27:30.004 Function completed (Success, Id=04061790-974f-4043-b851-48bd4ac424d1)

V okně prohlížeče pro funkci fronty, můžete zjistit každou zprávu zpracovává:

    2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
    2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"User testing from C# timer function","address":"XYZ"}
    2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)

## <a name="test-a-function-with-code"></a>Testování funkce pomocí kódu
Musíte vytvořit externí aplikace nebo framework pro testování funkcí.

### <a name="test-an-http-trigger-function-with-code-nodejs"></a>Testování funkce aktivace protokolu HTTP s kódem: Node.js
Aplikace Node.js můžete použít k provedení požadavku HTTP pro funkci otestovat.
Nezapomeňte nastavit:

* `host` v možnostech požadavek na hostiteli funkce aplikace.
* Název funkce v `path`.
* Přístupový kód (`<your code>`) v `path`.

Příklad kódu:

```javascript
var http = require("http");

var nameQueryString = "name=Wes%20Query%20String%20Test%20From%20Node.js";

var nameBodyJSON = {
    name : "Wes testing with Node.JS code",
    address : "Dallas, T.X. 75201"
};

var bodyString = JSON.stringify(nameBodyJSON);

var options = {
  host: "functions841def78.azurewebsites.net",
  //path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9&" + nameQueryString,
  path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9",
  method: "POST",
  headers : {
      "Content-Type":"application/json",
      "Content-Length": Buffer.byteLength(bodyString)
    }    
};

callback = function(response) {
  var str = ""
  response.on("data", function (chunk) {
    str += chunk;
  });

  response.on("end", function () {
    console.log(str);
  });
}

var req = http.request(options, callback);
console.log("*** Sending name and address in body ***");
console.log(bodyString);
req.end(bodyString);
```


Výstup:

    C:\Users\Wesley\testing\Node.js>node testHttpTriggerExample.js
    *** Sending name and address in body ***
    {"name" : "Wes testing with Node.JS code","address" : "Dallas, T.X. 75201"}
    Hello Wes testing with Node.JS code
    The address you provided is Dallas, T.X. 75201

Na portálu **protokoly** okně výstup podobný následujícímu přihlášen provádění funkce:

    2016-03-23T08:08:55  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:08:59.736 Function started (Id=607b891c-08a1-427f-910c-af64ae4f7f9c)
    2016-03-23T08:09:01.153 HTTP trigger function processed a request. RequestUri=http://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1/?code=XXXXXXXXXX==
    2016-03-23T08:09:01.153 Request Headers = {"connection":"Keep-Alive","host":"functionsExample.azurewebsites.net"}
    2016-03-23T08:09:01.153 Name not provided as query string param. Checking body...
    2016-03-23T08:09:01.153 Request Body Type = object
    2016-03-23T08:09:01.153 Request Body = [object Object]
    2016-03-23T08:09:01.153 Processing User Information...
    2016-03-23T08:09:01.215 Function completed (Success, Id=607b891c-08a1-427f-910c-af64ae4f7f9c)


### <a name="test-a-queue-trigger-function-with-code-c"></a>Testování funkce aktivační událost fronty s kódem: C# #
Jsme již bylo zmíněno dříve, můžete otestovat aktivační procedury fronty pomocí kódu, chcete-li vyřadit zprávu ve frontě. Následující příklad kódu podle uvedené v kódu jazyka C# [Začínáme s Azure Queue storage](../storage/queues/storage-dotnet-how-to-use-queues.md) kurzu. Kód pro jiné jazyky je také k dispozici prostřednictvím tohoto připojení.

Chcete-li otestovat tento kód v aplikaci konzoly, postupujte takto:

* [Konfigurace připojovacího řetězce úložiště v souboru app.config](../storage/queues/storage-dotnet-how-to-use-queues.md).
* Předat `name` a `address` jako parametry pro aplikaci. Například, `C:\myQueueConsoleApp\test.exe "Wes testing queues" "in a console app"`. (Tento kód přijímá název a adresu pro nového uživatele jako argumenty příkazového řádku za běhu.)

Příklad C# kódu:

```cs
static void Main(string[] args)
{
    string name = null;
    string address = null;
    string queueName = "queue-newusers";
    string JSON = null;

    if (args.Length > 0)
    {
        name = args[0];
    }
    if (args.Length > 1)
    {
        address = args[1];
    }

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["StorageConnectionString"]);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference(queueName);

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    if (name != null)
    {
        if (address != null)
            JSON = String.Format("{{\"name\":\"{0}\",\"address\":\"{1}\"}}", name, address);
        else
            JSON = String.Format("{{\"name\":\"{0}\"}}", name);
    }

    Console.WriteLine("Adding message to " + queueName + "...");
    Console.WriteLine(JSON);

    CloudQueueMessage message = new CloudQueueMessage(JSON);
    queue.AddMessage(message);
}
```

V okně prohlížeče pro funkci fronty, můžete zjistit každou zprávu zpracovává:

    2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
    2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"Wes testing queues","address":"in a console app"}
    2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)


<!-- URLs. -->

[portál Azure]: https://portal.azure.com
