---
title: 'Kurz: odesílání e-mailů pomocí logic aplikací'
description: Přečtěte si, jak vyvolat obchodní procesy z aplikace App Service. Posílejte e-maily, tweety a příspěvky na Facebooku, přidávejte do seznamů adresátů a mnoho dalšího.
ms.topic: tutorial
ms.date: 04/08/2020
ms.custom: mvc
ms.openlocfilehash: 44f2dfb83e96e1d8fa31ee1acf350193b954dbd8
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892850"
---
# <a name="tutorial-send-email-and-invoke-other-business-processes-from-app-service"></a>Kurz: Odeslání e-mailu a vyvolání dalších obchodních procesů ze služby App Service

V tomto kurzu se dozvíte, jak integrovat aplikaci App Service s obchodními procesy. To je běžné pro scénáře webových aplikací, jako jsou:

- Odeslání potvrzovacího e-mailu pro transakci
- Přidání uživatele do skupiny Facebook
- Připojte se k systémům třetích stran, jako je SAP, SalesForce atd.
- Standardní zprávy B2B serveru Exchange

V tomto kurzu odesíláte e-maily s Gmailem z aplikace App Service pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Existují i jiné způsoby odesílání e-mailů z webové aplikace, jako je konfigurace SMTP poskytovaná jazykovým rámcem. Aplikace Logic Apps však přináší mnohem více energie do aplikace App Service bez přidání složitosti vašeho kódu. Logic Apps poskytuje jednoduché konfigurační rozhraní pro nejoblíbenější obchodní integrace a vaše aplikace je může kdykoli volat pomocí požadavku HTTP.

## <a name="prerequisite"></a>Požadavek

Nasaďte si aplikaci s jazykovou architekturou podle vašeho výběru do služby App Service. Chcete-li postupovat podle kurzu k nasazení ukázkové aplikace, přečtěte si níže:

# <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

[Kurz: Vytvoření aplikace ASP.NET se službou SQL Database v Azure](app-service-web-tutorial-dotnet-sqldatabase.md)

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

[Kurz: Vytvoření aplikace ASP.NET core a sql database ve službě Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)

# <a name="nodejs"></a>[Node.js](#tab/node)

[Kurz: Vytvoření aplikace Node.js a MongoDB v Azure](app-service-web-tutorial-nodejs-mongodb-app.md)

# <a name="php"></a>[PHP](#tab/php)

[Kurz: Vytvoření aplikace PHP a MySQL v Azure](app-service-web-tutorial-php-mysql.md)

# <a name="python"></a>[Python](#tab/python)

[Kurz: Spuštění webové aplikace Python (Django) s PostgreSQL ve službě Azure App Service](containers/tutorial-python-postgresql-app.md)

# <a name="ruby"></a>[Ruby](#tab/ruby)

[Vytvoření aplikace Ruby a Postgres ve službě Azure App Service na Linuxu](containers/tutorial-ruby-postgres-app.md)

---

## <a name="create-the-logic-app"></a>Vytvoření aplikace logiky

1. Na [webu Azure Portal](https://portal.azure.com)vytvořte prázdnou aplikaci logiky podle pokynů v části [Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app). Když se zobrazí **Návrhář logických aplikací**, vraťte se k tomuto kurzu.
1. Na úvodní stránce aplikace Logic Apps vyberte **Při přijetí požadavku HTTP** v části Spustit s běžnou aktivační **událostí**.

    ![](./media/tutorial-send-email/receive-http-request.png)
1. V dialogovém okně **Při přijetí požadavku HTTP**vyberte použít **ukázkovou datovou část ke generování schématu**.

    ![](./media/tutorial-send-email/generate-schema-with-payload.png)

1. Zkopírujte následující ukázku JSON do textového pole a vyberte **Hotovo**.

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

    Schéma je nyní generováno pro požadovaná data požadavku. V praxi můžete jen zachytit skutečná data požadavku, která generuje kód aplikace, a nechat Azure generovat schéma JSON za vás. 
1. V horní části Návrháře logických aplikací vyberte **Uložit**. 

    Nyní můžete zobrazit adresu URL aktivační události požadavku HTTP. Vyberte ikonu kopírování, kterou chcete zkopírovat pro pozdější použití.

    ![](./media/tutorial-send-email/http-request-url.png)

    Tato definice požadavku HTTP je aktivační událost pro cokoli, co chcete dělat v této aplikaci logiky, ať už je to Gmail nebo cokoli jiného. Později vyvoláte tuto adresu URL v aplikaci App Service. Další informace o aktivační události požadavku naleznete v [odkazu na požadavek/odpověď HTTP](../connectors/connectors-native-reqres.md).

1. V dolní části návrháře klikněte na **Nový krok**, do vyhledávacího pole akce zadejte **Gmail** a najděte a vyberte **Odeslat e-mail (V2).**
    
    > [!TIP]
    > Můžete vyhledat další typy integrací, jako je SendGrid, MailChimp, Office 365 a SalesForce. Další informace naleznete v [dokumentaci k logicaplikacím](https://docs.microsoft.com/azure/logic-apps/).
1. V dialogovém okně **Gmail** vyberte **Přihlásit se** a přihlásit se k účtu Gmail, ze kterého chcete e-mail poslat.

    ![](./media/tutorial-send-email/gmail-sign-in.png)

1. Po přihlášení klikněte do textového pole **Do** a automaticky se otevře dialogové okno dynamického obsahu.

1. Vedle akce **Při přijetí požadavku HTTP** vyberte Zobrazit **další**.

    ![](./media/tutorial-send-email/expand-dynamic-content.png)

    Nyní byste měli vidět tři vlastnosti z ukázkových dat JSON, které jste použili dříve. V tomto kroku použijete tyto vlastnosti z požadavku HTTP k vytvoření e-mailu.
1. Vzhledem k tomu, že vybíráte hodnotu pro pole **Do,** zvolte **e-mail**. Pokud chcete, vypněte dialogové okno dynamického obsahu klepnutím na **přidat dynamický obsah**.

    ![](./media/tutorial-send-email/hide-dynamic-content.png)

1. V rozevíracím seznamu **Přidat nový parametr** vyberte **Předmět** a **Tělo**.

1. Klepněte do textového pole **Předmět** a stejným způsobem zvolte **úkol**. Pokud je kurzor stále v poli **Předmět,** zadejte *příkaz created*. 

1. Klikněte na **tělo**a stejným způsobem zvolte **splatné**. Přesuňte kurzor nalevo od **splatnosti** a zadejte *Tato pracovní položka je splatná na*.

    > [!TIP]
    > Pokud chcete upravit obsah HTML přímo v těle e-mailu, vyberte **zobrazení kódu** v horní části okna Návrhář eaplikací logiky. Jen se ujistěte, že jste zachovali kód dynamického obsahu `@{triggerBody()?['due']}`(například)
    >
    > ![](./media/tutorial-send-email/edit-rich-html-email.png) 

1. Dále přidejte asynchronní odpověď HTTP do aktivační události HTTP. Mezi aktivační událostí HTTP a akcí **+** Gmail klikněte na znaménko a vyberte **Přidat paralelní větev**.

    ![](./media/tutorial-send-email/add-http-response.png)

1. Ve vyhledávacím poli vyhledejte **odpověď**a vyberte akci **Odpověď.**

    ![](./media/tutorial-send-email/choose-response-action.png)

    Ve výchozím nastavení odešle akce odpovědi protokol HTTP 200. To je dost dobré pro tento výukový program. Další informace naleznete v [odkazu na požadavek/odpověď HTTP](../connectors/connectors-native-reqres.md).

1. V horní části Návrháře logických aplikací vyberte **Uložit** znovu. 

## <a name="add-http-request-code-to-app"></a>Přidání kódu požadavku HTTP do aplikace

Ujistěte se, že jste zkopírovali adresu URL aktivační události požadavku HTTP z předchozích. Vzhledem k tomu, že obsahuje citlivé informace, je osvědčeným postupem, abyste je nevložili přímo do kódu. Pomocí služby App Service na něj můžete odkazovat jako na proměnnou prostředí pomocí nastavení aplikace. 

V [prostředí Cloud Shell](https://shell.azure.com)vytvořte nastavení aplikace pomocí následujícího příkazu (nahraďte * \<>s názvem aplikace *, * \<>název skupiny prostředků *a * \<>logic-app-url *):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings LOGIC_APP_URL="<your-logic-app-url>"
```

V kódu vytvořte standardní příspěvek HTTP na adresu URL pomocí libovolného klientského jazyka HTTP, který je k dispozici pro váš jazykový rámec, s následující konfigurací:

- Tělo požadavku obsahuje stejný formát JSON, který jste zadali do aplikace logiky:

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

- Požadavek obsahuje `Content-Type: application/json`nadpis . 
- Chcete-li optimalizovat výkon, odešlete požadavek asynchronně, pokud je to možné.

Kliknutím na kartu preferovaný jazyk/architektura zobrazíte příklad.

# <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

V ASP.NET můžete odeslat příspěvek HTTP s třídou [System.Net.Http.HttpClient.](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) Příklad:

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // requires using System.Configuration;
    ConfigurationManager.AppSettings["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

Pokud testujete tento kód v ukázkové aplikaci pro [kurz: Vytvoření ASP.NET aplikace v Azure s databází SQL](app-service-web-tutorial-dotnet-sqldatabase.md), můžete ji použít k odeslání potvrzení e-mailu v akci [Vytvořit](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/blob/master/DotNetAppSqlDb/Controllers/TodosController.cs#L52-L63)po přidání `Todo` položky. Chcete-li použít výše uvedený asynchronní kód, převeďte akci Vytvořit na asynchronní.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

V ASP.NET Core můžete odeslat příspěvek HTTP s třídou [System.Net.Http.HttpClient.](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) Příklad:

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // Requires DI configuration to access app settings. See https://docs.microsoft.com/azure/app-service/containers/configure-language-dotnetcore#access-environment-variables
    _configuration["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

> [!NOTE]
> Tento kód je napsán pro jednoduchost demonstrace. V praxi není konkretizovat `HttpClient` objekt pro každý požadavek. Postupujte podle pokynů na [Use IHttpClientFactory implementovat odolné požadavky HTTP](https://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient -http-requests).

Pokud testujete tento kód v ukázkové aplikaci pro [kurz: Vytvoření aplikace ASP.NET core a SQL Database ve službě Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md), můžete ho použít k odeslání potvrzení e-mailu v akci [Vytvořit](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial/blob/master/Controllers/TodosController.cs#L56-L65)po přidání `Todo` položky.

# <a name="nodejs"></a>[Node.js](#tab/node)

V Node.js můžete snadno odeslat http příspěvek pomocí balíčku npm, jako [je axios](https://www.npmjs.com/package/axios). Příklad:

```javascript
// Requires npm install --save axios
const axios = require('axios');

var jsonData = {
        email: "a-valid@emailaddress.com",
        due: "4/1/2020",
        task: "My new task!"
};

(async function(data) {
    try {
        const response = await axios.post(process.env.LOGIC_APP_URL, jsonData);
        console.log(response.status);
    } catch (error) {
        console.log(error);
    }
})(jsonData);

```

Pokud testujete tento kód v ukázkové aplikaci pro [kurz: Vytvoření aplikace Node.js a MongoDB v Azure](app-service-web-tutorial-nodejs-mongodb-app.md), můžete ho použít k odeslání potvrzení e-mailu ve [funkci vytvořit](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L14-L27)po [úspěšném uložení článku](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L24).

# <a name="php"></a>[PHP](#tab/php)

V PHP, můžete poslat HTTP post snadno s [Guzzle](http://docs.guzzlephp.org/en/stable/index.html). Příklad:

```php
// Requires composer require guzzlehttp/guzzle:~6.0
use GuzzleHttp\Client;
...
$client = new Client();
$options = [
    'json' => [ 
        'email' => 'a-valid@emailaddress.com',
        'due' => '4/1/2020',
        'task' => "My new task!"
    ]
];

$promise = $client-> postAsync(getenv($LOGIC_APP_URL), $options)->then( 
    function ($response) {
        return $response->getStatusCode();
    }, function ($exception) {
        return $exception->getResponse();
    }
);

$response = $promise->wait();
// Requires Laravel to run Log::info(). Check the documentation of your preferred framework for logging instructions.
Log::info(print_r($response, TRUE));
```

Pokud testujete tento kód v ukázkové aplikaci pro [kurz: Sestavení aplikace PHP a MySQL v Azure](app-service-web-tutorial-php-mysql.md), můžete ji použít k odeslání potvrzení e-mailu ve [funkci Route::post](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48)těsně před příkazem return.

# <a name="python"></a>[Python](#tab/python)

V Pythonu můžete snadno odeslat http příspěvek s [požadavky](https://pypi.org/project/requests/). Příklad:

```python
# Requires pip install requests && pip freeze > requirements.txt
import requests
...
payload = {
    "email": "a-valid@emailaddress.com",
    "due": "4/1/2020",
    "task": "My new task!"
}
response = requests.post("https://prod-112.westeurope.logic.azure.com:443/workfl$
print(response.status_code)
```
<!-- ```python
# Requires pip install aiohttp && pip freeze > requirements.txt
import aiohttp
...
payload = {
        'email': 'a-valid@emailaddress.com',
        'due': '4/1/2020',
        'task': 'My new task!'
}
async with aiohttp.post('http://httpbin.org/post', data=json.dump(payload)) as resp:
    print(await resp.status())
``` -->

Pokud testujete tento kód v ukázkové aplikaci pro [kurz: Spusťte webovou aplikaci Python (Django) s PostgreSQL ve službě Azure App Service](containers/tutorial-python-postgresql-app.md), můžete ho použít k odeslání potvrzení e-mailu ve funkci [Route::post](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48)těsně před příkazem return.

# <a name="ruby"></a>[Ruby](#tab/ruby)

V Ruby můžete snadno odeslat http příspěvek pomocí [JSONClient](https://www.rubydoc.info/gems/httpclient/JSONClient). Příklad:

```ruby
clnt = JSONClient.new
body = { 
    'email' => 'a-valid@emailaddress.com',
    'due' => '4/1/2020',
    'task' => "My new task!"
}

connection = clnt.post_async(ENV['LOGIC_APP_URL'], body)
```

Pokud testujete tento kód v ukázkové aplikaci build [a Ruby a Postgres v Azure App Service na Linuxu](containers/tutorial-ruby-postgres-app.md), můžete ho použít k odeslání potvrzení e-mailu v akci [vytvořit,](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L26-L38) [když @task.save uspěje](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L30).

---

# <a name="more-resources"></a>Další zdroje informací

[Kurz: Hostování rozhraní RESTful API s CORS v Azure App Service](app-service-web-tutorial-rest-api.md)  
[Odkaz na požadavek/odpověď HTTP pro logice apps](../connectors/connectors-native-reqres.md)  
[Úvodní příručka: Vytvoření prvního pracovního postupu pomocí Azure Logic Apps – portál Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md)