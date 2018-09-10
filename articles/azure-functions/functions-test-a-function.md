---
title: Testování Azure Functions | Dokumentace Microsoftu
description: Testování Azure functions pomocí nástroje Postman, cURL a Node.js.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure functions, funkce, zpracování událostí, webhooky, dynamické výpočty, architekturu bez serveru, testování
ms.assetid: c00f3082-30d2-46b3-96ea-34faf2f15f77
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 02/02/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1aa1c3a3c0dd4985662b5ceb4acd7250cf2d0186
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44093222"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Strategie pro testování kódu ve službě Azure Functions

Toto téma popisuje různé způsoby testování funkcí, včetně použití následujících obecných přístupů:

+ Nástroje založené na protokolu HTTP, například cURL, Postman a dokonce i webový prohlížeč pro triggery založené na web
+ Průzkumník služby Azure Storage, k otestování triggery založené na Azure Storage
+ Karta testu na portálu Azure Functions
+ Funkce aktivované pomocí časovače
+ Testování aplikace nebo rozhraní framework

Všechny tyto metody testování použijte funkci triggeru HTTP, který přijímá vstup prostřednictvím parametru řetězce dotazu nebo textu požadavku. Můžete vytvořit tuto funkci pomocí webu Azure portal v první části.

## <a name="create-a-simple-function-for-testing-using-the-azure-portal"></a>Vytvořit jednoduchou funkci pro testování s využitím webu Azure portal
Pro většinu tohoto kurzu používáme mírně upravenou verzi šablony funkce HttpTrigger JavaScript, která je dostupná při vytváření funkce. Pokud potřebujete pomoc při vytváření funkce, přečtěte si [kurzu](functions-create-first-azure-function.md). Zvolte **HttpTrigger – JavaScript** šablony při vytváření funkce test v [Azure Portal].

Výchozí šablona funkce je v podstatě funkci "hello world", která se vrátí zpět z požadavku text nebo dotazu řetězcový parametr, název `name=<your name>`.  Aktualizujeme kód, který umožňuje také jako obsah JSON v textu požadavku zadejte název a adresu. Funkce pak vrátí tyto zpět klientovi, pokud je k dispozici.   

Aktualizujte funkci následující kód, který budeme používat pro testování:

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

## <a name="test-a-function-with-tools"></a>Otestovat funkci s nástroji
Mimo na webu Azure portal existují různé nástroje, které můžete použít k aktivaci funkcí pro testování. Patří mezi ně HTTP testovací nástroje (na základě uživatelského rozhraní i příkaz řádek), Azure Storage přístup k nástrojům a dokonce i jednoduchý webový prohlížeč.

### <a name="test-with-a-browser"></a>Testování pomocí prohlížeče
Webový prohlížeč je jednoduchý způsob, jak funkce triggeru přes protokol HTTP. Můžete použít prohlížeč pro požadavky GET, které nevyžadují, aby datová část textu, a pouze dotazy použijte řetězec parametry.

Chcete-li otestovat funkci jsme definovali dříve, zkopírujte **adresu Url funkce** z portálu. Má následující tvar:

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Připojit `name` parametr řetězce dotazu. Použití skutečný název `<Enter a name here>` zástupný symbol.

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>&name=<Enter a name here>

Vložte adresu URL do prohlížeče a měli obdržet odpověď podobná následující.

![Snímek obrazovky Chrome kartu prohlížeče s odpovědí testu](./media/functions-test-a-function/browser-test.png)

V tomto příkladu je pro prohlížeč Chrome, která zabalí vráceného řetězce ve formátu XML. Jiné prohlížeče zobrazí hodnotu řetězce.

Na portálu **protokoly** okna výstup podobný následujícímu se protokoluje při provádění funkce:

    2016-03-23T07:34:59  Welcome, you are now connected to log-streaming service.
    2016-03-23T07:35:09.195 Function started (Id=61a8c5a9-5e44-4da0-909d-91d293f20445)
    2016-03-23T07:35:10.338 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==&name=Glenn from a browser
    2016-03-23T07:35:10.338 Request Headers = {"cache-control":"max-age=0","connection":"Keep-Alive","accept":"text/html","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T07:35:10.338 Name was provided as a query string param.
    2016-03-23T07:35:10.338 Processing User Information...
    2016-03-23T07:35:10.369 Function completed (Success, Id=61a8c5a9-5e44-4da0-909d-91d293f20445)

### <a name="test-with-postman"></a>Testování pomocí nástroje Postman
Doporučeným nástrojem k testování většinu funkcí je nástroj Postman, která se integruje s prohlížečem Chrome. K instalaci nástroje Postman, naleznete v tématu [získat Postman](https://www.getpostman.com/). Postman umožňuje řídit mnoho další atributy požadavku HTTP.

> [!TIP]
> Použijte protokol HTTP testování nástroj, který se nejvíce vyhovuje. Tady jsou některé alternativy k Postman:  
>
> * [Fiddler](http://www.telerik.com/fiddler)  
> * [S privilegovaným přístupem](https://luckymarmot.com/paw)  
>
>

V nástroji Postman otestovat funkci s hlavní část žádosti:

1. Spusťte Postman z **aplikace** tlačítko v levém horním rohu okna prohlížeče Chrome.
2. Kopii vaší **adresu Url funkce**a vložte ho do nástroje Postman. Obsahuje kód pro přístup k parametru řetězce dotazu.
3. Změnit metodu HTTP k **příspěvek**.
4. Klikněte na tlačítko **tělo** > **nezpracovaná**, a přidejte text požadavku JSON podobný následujícímu:

    ```json
    {
        "name" : "Wes testing with Postman",
        "address" : "Seattle, WA 98101"
    }
    ```
5. Klikněte na tlačítko **odeslat**.

Následující obrázek ukazuje příklad jednoduchého echo funkce testování v tomto kurzu.

![Snímek obrazovky nástroje Postman uživatelského rozhraní](./media/functions-test-a-function/postman-test.png)

Na portálu **protokoly** okna výstup podobný následujícímu se protokoluje při provádění funkce:

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
Často při testování softwaru, není potřeba žádné další příkazového řádku pro ladění vaší aplikace vypadat. To se nijak neliší při testování funkcí. Všimněte si, cURL je k dispozici ve výchozím nastavení v systémech založených na Linuxu. Na Windows, musíte nejdřív stáhnout a nainstalovat [nástroj cURL](https://curl.haxx.se/).

Chcete-li otestovat funkci, kterou jsme definovali dříve, zkopírujte **adresu URL funkce** z portálu. Má následující tvar:

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Toto je adresa URL pro spuštění vaší funkce. Otestovat pomocí příkazu cURL na příkazovém řádku, aby GET (`-G` nebo `--get`) požadavku vůči funkce:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

V tomto konkrétním příkladu vyžaduje parametr řetězce dotazu, které mohou být předány jako Data (`-d`) v příkazu cURL:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code> -d name=<Enter a name here>

Spusťte příkaz a zobrazí se následující výstup funkce na příkazovém řádku:

![Snímek obrazovky z příkazového řádku výstupu](./media/functions-test-a-function/curl-test.png)

Na portálu **protokoly** okna výstup podobný následujícímu se protokoluje při provádění funkce:

    2016-04-05T21:55:09  Welcome, you are now connected to log-streaming service.
    2016-04-05T21:55:30.738 Function started (Id=ae6955da-29db-401a-b706-482fcd1b8f7a)
    2016-04-05T21:55:30.738 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/HttpTriggerNodeJS1?code=XXXXXXX&name=Azure Functions
    2016-04-05T21:55:30.738 Function completed (Success, Id=ae6955da-29db-401a-b706-482fcd1b8f7a)

### <a name="test-a-blob-trigger-by-using-storage-explorer"></a>Testování aktivační událost objektů blob pomocí Storage Exploreru
Aktivační funkce objektů blob můžete otestovat pomocí [Průzkumníka služby Azure Storage](http://storageexplorer.com/).

1. V [Azure Portal] vaši aplikaci function App vytvořit funkci aktivační událost objektů blob v C#, F # nebo JavaScript. Nastavte cestu k monitorování pro název kontejneru objektů blob. Příklad:

        files
2. Klikněte na tlačítko **+** tlačítko a vyberte nebo vytvořte účet úložiště, kterou chcete použít. Poté klikněte na **Vytvořit**.
3. Vytvořte textový soubor s následujícím textem a uložte ho:

        A text file for blob trigger function testing.
4. Spustit [Průzkumníka služby Azure Storage](http://storageexplorer.com/)a připojte se ke kontejneru objektů blob v účtu úložiště, který je monitorován.
5. Klikněte na tlačítko **nahrát** nahrát textový soubor.

    ![Snímek obrazovky Průzkumníka služby Storage](./media/functions-test-a-function/azure-storage-explorer-test.png)

Kód funkce aktivační událost objektů blob výchozí sestavy zpracování objektů blob v protokolech:

    2016-03-24T11:30:10  Welcome, you are now connected to log-streaming service.
    2016-03-24T11:30:34.472 Function started (Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)
    2016-03-24T11:30:34.472 C# Blob trigger function processed: A text file for blob trigger function testing.
    2016-03-24T11:30:34.472 Function completed (Success, Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)

## <a name="test-a-function-within-functions"></a>Testování funkce v rámci funkcí
Funkce Azure portal je navržena tak, aby test protokolu HTTP a funkce aktivované časovačem. Můžete také vytvořit functions k aktivaci dalších funkcí, které testujete.

### <a name="test-with-the-functions-portal-run-button"></a>Testování s funkcí portálu tlačítko Spustit
Portál poskytuje **spustit** tlačítko, které můžete použít k provedení některých omezené testování. Pomocí tlačítka můžete poskytnout tělo požadavku, ale nelze zadat parametry řetězce dotazu nebo aktualizovat hlavičky žádosti.

Otestovat funkci triggeru HTTP, který jsme vytvořili dříve přidáním řetězec JSON podobný následujícímu v **text žádosti** pole. Klikněte **spustit** tlačítko.

```json
{
    "name" : "Wes testing Run button",
    "address" : "USA"
}
```

Na portálu **protokoly** okna výstup podobný následujícímu se protokoluje při provádění funkce:

    2016-03-23T08:03:12  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:03:17.357 Function started (Id=753a01b0-45a8-4125-a030-3ad543a89409)
    2016-03-23T08:03:18.697 HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/wesmchttptriggernodejs1
    2016-03-23T08:03:18.697 Request Headers = {"connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T08:03:18.697 Processing user info from request body...
    2016-03-23T08:03:18.697 Processing User Information...
    2016-03-23T08:03:18.697 name = Wes testing Run button
    2016-03-23T08:03:18.697 address = USA
    2016-03-23T08:03:18.744 Function completed (Success, Id=753a01b0-45a8-4125-a030-3ad543a89409)


### <a name="test-with-a-timer-trigger"></a>Testování s triggerem timer
Některé funkce nelze provést test odpovídajícím způsobem pomocí nástrojů, které již bylo zmíněno dříve. Představte si třeba funkce aktivační událost fronty, která se spustí, když je zprávu zařazenou do [Azure Queue storage](../storage/queues/storage-dotnet-how-to-use-queues.md). Vždy můžete napsat kód k vyřadit zprávu do fronty a příklad tohoto objektu v projektu konzoly je k dispozici dále v tomto článku. Je však jiný přístup, který můžete použít, který testuje funkce přímo.  

Můžete použít aktivační událost časovače nakonfigurovaný s frontou výstupní vazbu. Tento časovač aktivační kód pak můžou zapisovat zkušebních zpráv do fronty. Tato část vás provede příkladem.

Další podrobné informace o používání vazeb s využitím Azure Functions najdete v tématu [referenční informace pro vývojáře Azure Functions](functions-reference.md).

#### <a name="create-a-queue-trigger-for-testing"></a>Vytvořit aktivační událost fronty pro účely testování
Abychom si předvedli tento přístup, vytvoříme nejprve funkci aktivační událost fronty, která chceme otestovat pro frontu s názvem `queue-newusers`. Tato funkce zpracuje název a informace o adresách zařazenou do fronty úložiště pro nového uživatele.

> [!NOTE]
> Pokud použijete název jinou frontu, ujistěte se, že použijete název odpovídá [pojmenování front a MetaData](https://msdn.microsoft.com/library/dd179349.aspx) pravidla. Jinak dojde k chybě.
>
>

1. V [Azure Portal] aplikace function app, klikněte na tlačítko **novou funkci** > **QueueTrigger – C#**.
2. Zadejte název fronty musí být sledován pomocí funkce fronty:

        queue-newusers
3. Klikněte na tlačítko **+** tlačítko a vyberte nebo vytvořte účet úložiště, kterou chcete použít. Poté klikněte na **Vytvořit**.
4. Ponechte toto okno prohlížeče portálu otevřené, tak můžete sledovat položky protokolu pro výchozí kód šablony funkce fronty.

#### <a name="create-a-timer-trigger-to-drop-a-message-in-the-queue"></a>Vytvoření triggeru časovače vyřadit zprávu ve frontě
1. Otevřít [Azure Portal] v novém okně prohlížeče a přejděte do aplikace function app.
2. Klikněte na tlačítko **novou funkci** > **TimerTrigger – C#**. Zadejte výraz cron nastavit, jak často kód časovače testuje vaši funkci fronty. Poté klikněte na **Vytvořit**. Pokud chcete test spouští každých 30 sekund, můžete použít následující [výraz CRON](https://wikipedia.org/wiki/Cron#CRON_expression):

        */30 * * * * *
3. Klikněte na tlačítko **integrace** karty pro vaše Nová aktivační událost časovače.
4. V části **výstup**, klikněte na tlačítko **+ nový výstup**. Pak klikněte na tlačítko **fronty** a **vyberte**.
5. Poznámka: název, který používáte pro **objekt fronty zpráv**. Můžete použít v kódu funkce časovače.

        myQueue
6. Zadejte název fronty, odešle se zpráva:

        queue-newusers
7. Klikněte na tlačítko **+** tlačítko a vyberte účet úložiště, který jste použili s triggerem queue. Potom klikněte na **Uložit**.
8. Klikněte na tlačítko **vývoj** kartu pro trigger časovače.
9. Následující kód můžete použít pro funkce jazyka C# časovače, jako jste použili stejné fronty zpráv název objektu je uvedeno výše. Potom klikněte na **Uložit**.

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

V tomto okamžiku časovače funkce jazyka C# spustí každých 30 sekund, pokud jste použili v podobě výrazu cron příklad. Protokoly pro funkci časovače sestavy každé spuštění:

    2016-03-24T10:27:02  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.004 Function started (Id=04061790-974f-4043-b851-48bd4ac424d1)
    2016-03-24T10:27:30.004 C# Timer trigger function executed at: 3/24/2016 10:27:30 AM
    2016-03-24T10:27:30.004 {"name":"User testing from C# timer function","address":"XYZ"}
    2016-03-24T10:27:30.004 Function completed (Success, Id=04061790-974f-4043-b851-48bd4ac424d1)

V okně prohlížeče pro funkci fronty můžete zobrazit každou zprávu zpracovává:

    2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
    2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"User testing from C# timer function","address":"XYZ"}
    2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)

## <a name="test-a-function-with-code"></a>Otestovat funkci s kódem
Budete muset vytvořit externí aplikace nebo rozhraní k testování vašich funkcí.

### <a name="test-an-http-trigger-function-with-code-nodejs"></a>Otestovat funkci triggeru HTTP s kódem: Node.js
Aplikace v Node.js můžete použít k provedení požadavku HTTP otestovat vaši funkci.
Ujistěte se, že nastavení:

* `host` v možnostech požadavek na hostitele aplikace funkce.
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

Na portálu **protokoly** okna výstup podobný následujícímu se protokoluje při provádění funkce:

    2016-03-23T08:08:55  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:08:59.736 Function started (Id=607b891c-08a1-427f-910c-af64ae4f7f9c)
    2016-03-23T08:09:01.153 HTTP trigger function processed a request. RequestUri=http://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1/?code=XXXXXXXXXX==
    2016-03-23T08:09:01.153 Request Headers = {"connection":"Keep-Alive","host":"functionsExample.azurewebsites.net"}
    2016-03-23T08:09:01.153 Name not provided as query string param. Checking body...
    2016-03-23T08:09:01.153 Request Body Type = object
    2016-03-23T08:09:01.153 Request Body = [object Object]
    2016-03-23T08:09:01.153 Processing User Information...
    2016-03-23T08:09:01.215 Function completed (Success, Id=607b891c-08a1-427f-910c-af64ae4f7f9c)


### <a name="test-a-queue-trigger-function-with-code-c"></a>Otestovat funkci aktivační událost fronty s kódem: C# #
Už jsme zmínili výše, můžete otestovat aktivační událost fronty vyřadit zprávu ve frontě pomocí kódu. Následující příklad kódu je založena na uvedené v kódu jazyka C# [Začínáme se službou Azure Queue storage](../storage/queues/storage-dotnet-how-to-use-queues.md) kurzu. Kód pro ostatní jazyky je také k dispozici z tohoto odkazu.

K otestování tohoto kódu v konzolové aplikaci, musíte mít:

* [Konfigurace připojovacího řetězce úložiště v souboru app.config](../storage/queues/storage-dotnet-how-to-use-queues.md).
* Předat `name` a `address` jako parametry pro aplikaci. Například, `C:\myQueueConsoleApp\test.exe "Wes testing queues" "in a console app"`. (Tento kód přijímá název a adresu pro nového uživatele jako argumenty příkazového řádku za běhu.)

Příklad jazyka C# kód:

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

V okně prohlížeče pro funkci fronty můžete zobrazit každou zprávu zpracovává:

    2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
    2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"Wes testing queues","address":"in a console app"}
    2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)


<!-- URLs. -->

[Azure Portal]: https://portal.azure.com
