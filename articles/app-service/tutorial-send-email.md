---
title: 'Kurz: odesílání e-mailů pomocí Logic Apps'
description: Naučte se volat obchodní procesy z aplikace App Service. Posílání e-mailů, tweety a příspěvků na Facebooku, přidávání do seznamů adresátů a mnoho dalších.
ms.topic: tutorial
ms.date: 04/08/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 8072a941cd89290af3e25cc63c4fccccce705df9
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95014658"
---
# <a name="tutorial-send-email-and-invoke-other-business-processes-from-app-service"></a>Kurz: odesílání e-mailů a vyvolání dalších obchodních procesů z App Service

V tomto kurzu se naučíte integrovat aplikaci App Service s vašimi obchodními procesy. To je běžné pro scénáře webových aplikací, jako například:

- Odeslat potvrzující e-mail pro transakci
- Přidat uživatele do skupiny Facebook
- Připojte se k systémům třetích stran, jako je SAP, Salesforce atd.
- Zprávy Standard B2B pro Exchange

V tomto kurzu posíláte e-maily pomocí služby Gmail z aplikace App Service pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Existují i jiné způsoby, jak odesílat e-maily z webové aplikace, jako je například konfigurace SMTP poskytovaná vaším jazykovým rozhraním. Nicméně Logic Apps přináší mnohem větší výkon vaší aplikace App Service, aniž by bylo nutné do kódu přidat složitost. Logic Apps poskytuje jednoduché konfigurační rozhraní pro nejoblíbenější obchodní integrace a vaše aplikace je může kdykoli zavolat s požadavkem HTTP.

## <a name="prerequisite"></a>Požadavek

Nasaďte aplikaci s použitím jazykové architektury podle vašeho výběru na App Service. Postup nasazení ukázkové aplikace najdete v kurzu níže:

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

[Kurz: Vytvoření aplikace ASP.NET se službou SQL Database v Azure](app-service-web-tutorial-dotnet-sqldatabase.md)

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

[Kurz: Vytvoření aplikace ASP.NET Core a SQL Database v Azure App Service](tutorial-dotnetcore-sqldb-app.md)

### <a name="nodejs"></a>[Node.js](#tab/node)

[Kurz: Vytvoření aplikace Node.js a MongoDB v Azure](tutorial-nodejs-mongodb-app.md)

### <a name="php"></a>[PHP](#tab/php)

[Kurz: sestavení aplikace PHP a MySQL v Azure](tutorial-php-mysql-app.md)

### <a name="python"></a>[Python](#tab/python)

[Kurz: spuštění webové aplikace v Pythonu (Django) s PostgreSQL v Azure App Service](tutorial-python-postgresql-app.md)

### <a name="ruby"></a>[Ruby](#tab/ruby)

[Vytvoření aplikace Ruby a Postgres v Azure App Service v systému Linux](tutorial-ruby-postgres-app.md)

---

## <a name="create-the-logic-app"></a>Vytvoření aplikace logiky

1. V [Azure Portal](https://portal.azure.com)vytvořte prázdnou aplikaci logiky podle pokynů v tématu [Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app). Když se zobrazí **návrhář Logic Apps**, vraťte se k tomuto kurzu.
1. Na úvodní stránce pro návrháře Logic Apps vyberte, kdy se v části začátek **přijme požadavek HTTP** **se společným triggerem**.

    ![Snímek obrazovky, který zobrazuje úvodní stránku pro návrháře Logic Apps, kdy se zvýrazní požadavek H T T P](./media/tutorial-send-email/receive-http-request.png)
1. V dialogu pro, **kdy se přijme požadavek HTTP**, vyberte **použít ukázkovou datovou část k vygenerování schématu**.

    ![Snímek obrazovky, který zobrazuje dialogové okno žádosti o vytvoření schématu opion, když se vygenerovala možnost použít ukázkovou datovou část se schématem H T T ](./media/tutorial-send-email/generate-schema-with-payload.png)

1. Zkopírujte následující vzorový kód JSON do textového pole a vyberte **Hotovo**.

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

    Schéma je nyní vygenerováno pro požadovaná data požadavku. V praxi můžete pouze zachytit skutečná data žádosti, která kód aplikace generuje, a nechat si pro vás Azure vygenerovat schéma JSON. 
1. V horní části návrháře Logic Apps vyberte **Uložit**. 

    Teď uvidíte adresu URL triggeru požadavku HTTP. Vyberte ikonu kopírování a zkopírujte ji pro pozdější použití.

    ![Snímek obrazovky, který zvýrazní ikonu kopírování a zkopíruje U R L triggeru žádosti H T T P.](./media/tutorial-send-email/http-request-url.png)

    Tato definice požadavku HTTP je triggerem pro cokoli, co chcete v této aplikaci logiky dělat, může to být Gmail nebo cokoli jiného. Později tuto adresu URL vyvoláte ve vaší aplikaci App Service. Další informace o triggeru žádosti najdete v [referenčních informacích o požadavku a odpovědi HTTP](../connectors/connectors-native-reqres.md).

1. V dolní části návrháře klikněte na **Nový krok**, do vyhledávacího pole Akce zadejte **Gmail** a vyhledejte a vyberte **Odeslat e-mail (v2)**.
    
    > [!TIP]
    > Můžete hledat další typy integrace, například SendGrid, MailChimp, Microsoft 365 a SalesForce. Další informace najdete v [dokumentaci Logic Apps](../logic-apps/index.yml).

1. V dialogovém okně **Gmail** vyberte **Přihlásit** se a přihlaste se k účtu Gmail, ze kterého chcete e-mail odeslat.

    ![Snímek obrazovky, který zobrazuje dialogové okno Gmail, pomocí kterého se přihlašujete k účtu Gmail, ze kterého chcete odesílat e-maily.](./media/tutorial-send-email/gmail-sign-in.png)

1. Po přihlášení klikněte do textového pole **k** a automaticky se otevře dialogové okno dynamického obsahu.

1. U akce **když se přijme požadavek HTTP** , vyberte **Zobrazit další**.

    ![Snímek obrazovky, který zobrazuje tlačítko Zobrazit více vedle okamžiku, kdy se přijala žádost H T T P](./media/tutorial-send-email/expand-dynamic-content.png)

    Nyní byste měli vidět tři vlastnosti z ukázkových dat JSON, která jste použili dříve. V tomto kroku použijete tyto vlastnosti z požadavku HTTP k vytvoření e-mailu.
1. Vzhledem k tomu, že vybíráte hodnotu pro pole **do** , zvolte **e-mail**. Pokud chcete, přepínejte dialogové okno dynamického obsahu kliknutím na **Přidat dynamický obsah**.

    ![Snímek obrazovky, který zobrazuje možnosti e-mailu a zvýrazní možnost Přidat dynamický obsah.](./media/tutorial-send-email/hide-dynamic-content.png)

1. V rozevíracím seznamu **Přidat nový parametr** vyberte **Předmět** a **text**.

1. Klikněte do textového pole **Předmět** a stejným způsobem vyberte možnost **úloha**. Když se kurzor stále nachází v poli **subject (předmět** ), zadejte *vytvořit*. 

1. Klikněte na **text** a stejným způsobem vyberte možnost **nesplnit**. Přesune kurzor nalevo od **termínu splnění** a typ, *na který je tato pracovní položka splatná*.

    > [!TIP]
    > Chcete-li upravit obsah HTML přímo v těle e-mailu, vyberte v horní části okna návrháře Logic Apps **zobrazení kódu** . Stačí zajistit, abyste zachovali kód dynamického obsahu (například `@{triggerBody()?['due']}` ).
    >
    > ![Snímek obrazovky zobrazující zobrazení kódu pro prohlížení obsahu H T M L přímo v těle e-mailu.](./media/tutorial-send-email/edit-rich-html-email.png) 

1. Dále přidejte asynchronní odpověď HTTP do triggeru HTTP. Mezi triggerem HTTP a akcí Gmail klikněte na **+** znaménko a vyberte **Přidat paralelní větev**.

    ![Snímek obrazovky, který ukazuje možnost + znaménko a přidat paralelní větev zvýrazněnou.](./media/tutorial-send-email/add-http-response.png)

1. Do vyhledávacího pole vyhledejte **odpověď** a pak vyberte akci **reakce** .

    ![Snímek obrazovky, který zobrazuje zvýrazněný panel hledání a akci odpovědi](./media/tutorial-send-email/choose-response-action.png)

    Akce odpovědi ve výchozím nastavení odešle HTTP 200. To je pro tento kurz dostatečné. Další informace najdete v [referenčních informacích o žádostech a odpovědích http](../connectors/connectors-native-reqres.md).

1. V horní části návrháře Logic Apps vyberte **Uložit** znovu. 

## <a name="add-http-request-code-to-app"></a>Přidání kódu požadavku HTTP do aplikace

Ujistěte se, že jste zkopírovali adresu URL triggeru požadavku HTTP z předchozích verzí. Protože obsahuje citlivé informace, je osvědčeným postupem Nevkládat je přímo do kódu. Pomocí App Service můžete místo toho odkazovat jako na proměnnou prostředí, a to pomocí nastavení aplikace. 

V [Cloud Shell](https://shell.azure.com)vytvořte nastavení aplikace pomocí následujícího příkazu (nahradit *\<app-name>* , *\<resource-group-name>* a *\<logic-app-url>* ):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings LOGIC_APP_URL="<your-logic-app-url>"
```

Ve svém kódu proveďte standardní příspěvek HTTP na adresu URL pomocí libovolného jazyka klienta HTTP, který je k dispozici pro vaše jazyková rozhraní, s následující konfigurací:

- Tělo žádosti obsahuje stejný formát JSON, který jste zadali do vaší aplikace logiky:

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

- Požadavek obsahuje záhlaví `Content-Type: application/json` . 
- Pokud chcete optimalizovat výkon, odešlete požadavek asynchronně, pokud je to možné.

Příklad zobrazíte kliknutím na kartu preferovaný jazyk/rozhraní.

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

V ASP.NET můžete odeslat příspěvek HTTP pomocí třídy [System .NET. http. HttpClient](/dotnet/api/system.net.http.httpclient) . Například:

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

Pokud testujete tento kód v ukázkové aplikaci pro [kurz: Vytvoření aplikace ASP.NET v Azure pomocí SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md), můžete ji použít k odeslání e-mailového potvrzení v [akci vytvořit](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/blob/master/DotNetAppSqlDb/Controllers/TodosController.cs#L52-L63)po `Todo` Přidání položky. Chcete-li použít asynchronní kód výše, převeďte akci vytvořit na asynchronní.

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

V ASP.NET Core můžete odeslat příspěvek HTTP pomocí třídy [System .NET. http. HttpClient](/dotnet/api/system.net.http.httpclient) . Například:

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
    // Requires DI configuration to access app settings. See https://docs.microsoft.com/azure/app-service/configure-language-dotnetcore#access-environment-variables
    _configuration["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

> [!NOTE]
> Tento kód je zapsán z důvodu jednoduchosti ukázky. V praxi nevytvářejte instanci `HttpClient` objektu pro každý požadavek. Pokud [chcete implementovat odolné požadavky HTTP](/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests), postupujte podle pokynů v části použití IHttpClientFactory.

Pokud testujete tento kód v ukázkové aplikaci pro [kurz: Vytvoření aplikace ASP.NET Core a SQL Database v Azure App Service](tutorial-dotnetcore-sqldb-app.md), můžete ji použít k odeslání e-mailového potvrzení v [akci vytvořit](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial/blob/master/Controllers/TodosController.cs#L56-L65)po `Todo` Přidání položky.

### <a name="nodejs"></a>[Node.js](#tab/node)

V Node.js můžete odeslat příspěvek HTTP snadno pomocí balíčku NPM, jako je [axios](https://www.npmjs.com/package/axios). Například:

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

Pokud testujete tento kód v ukázkové aplikaci pro [kurz: Vytvoření aplikace Node.js a MongoDB v Azure](tutorial-nodejs-mongodb-app.md), můžete ji použít k odeslání e-mailového potvrzení ve [funkci Create](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L14-L27)po [úspěšném uložení článku](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L24).

### <a name="php"></a>[PHP](#tab/php)

V PHP můžete snadno odeslat příspěvek HTTP pomocí [Guzzle](http://docs.guzzlephp.org/en/stable/index.html). Například:

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

Pokud tento kód testujete v ukázkové aplikaci pro [kurz: sestavte aplikaci PHP a MySQL v Azure](tutorial-php-mysql-app.md), můžete ji použít k odeslání e-mailového potvrzení ve [funkci Route::p OST](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48)těsně před příkazem Return.

### <a name="python"></a>[Python](#tab/python)

V Pythonu můžete pomocí [požadavků](https://pypi.org/project/requests/)snadno odeslat příspěvek http. Například:

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

Pokud testujete tento kód v ukázkové aplikaci pro [kurz: spuštění webové aplikace Python (Django) s PostgreSQL v Azure App Service](tutorial-python-postgresql-app.md)můžete použít k odeslání e-mailového potvrzení ve [funkci Route::p OST](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48)těsně před příkazem Return.

### <a name="ruby"></a>[Ruby](#tab/ruby)

V Ruby můžete odeslat příspěvek HTTP snadno pomocí JSONClient. Například:

```ruby
clnt = JSONClient.new
body = { 
    'email' => 'a-valid@emailaddress.com',
    'due' => '4/1/2020',
    'task' => "My new task!"
}

connection = clnt.post_async(ENV['LOGIC_APP_URL'], body)
```

Pokud testujete tento kód v ukázkové aplikaci pro [Vytvoření aplikace Ruby a Postgres v Azure App Service v systému Linux](tutorial-ruby-postgres-app.md), můžete ji použít k odeslání e-mailového potvrzení v akci [vytvořit](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L26-L38) , pokud bude [ @task.save úspěšná](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L30).

---

## <a name="more-resources"></a>Další zdroje informací

[Kurz: Hostování rozhraní RESTful API s CORS v Azure App Service](app-service-web-tutorial-rest-api.md)  
[Referenční dokumentace žádosti a odpovědi HTTP pro Logic Apps](../connectors/connectors-native-reqres.md)  
[Rychlý Start: vytvoření prvního pracovního postupu pomocí Azure Logic Apps-Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)