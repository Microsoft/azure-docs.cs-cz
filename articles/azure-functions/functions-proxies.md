---
title: Práce s proxy serverů ve službě Azure Functions | Dokumentace Microsoftu
description: Přehled o tom, jak používat proxy služby Azure Functions
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: da46d33e05a6f76687d3b8982855d477cbabfe86
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55895614"
---
# <a name="work-with-azure-functions-proxies"></a>Práce s proxy služby Azure Functions

Tento článek vysvětluje postup konfigurace a práce s proxy služby Azure Functions. Pomocí této funkce můžete určovat koncové body na aplikaci funkcí, které jsou implementované jiným prostředkem. Tyto servery proxy můžete použít k rozdělení velké rozhraní API do více aplikací funkcí (jako v architektuře mikroslužeb), přitom stále poskytují jednotnou rovinu rozhraní API pro klienty.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Standardní funkce fakturace se vztahuje na spuštění proxy serveru. Další informace najdete v tématu [ceny Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create"></a>Vytvořit proxy

V této části se dozvíte, jak vytvořit proxy server na portálu Functions.

1. Otevřít [Azure Portal]a potom přejděte ke své aplikaci function app.
2. V levém podokně vyberte **nový proxy server**.
3. Zadejte název pro váš proxy server.
4. Konfigurace koncového bodu, který je zveřejněný na tuto aplikaci function app tak, že zadáte **šablonu trasy** a **metody HTTP**. Tyto parametry se chovají podle pravidel pro [Aktivační události HTTP].
5. Nastavte **URL back-end** do jiného koncového bodu. Tento koncový bod může být funkce v jiné aplikaci function app, nebo může být jiné rozhraní API. Hodnota nemusí být statická a může odkazovat [nastavení aplikace] a [parametry z původního požadavku klienta].
6. Klikněte na možnost **Vytvořit**.

Váš proxy server nyní existuje jako nový koncový bod na vaši aplikaci function app. Z pohledu klienta je ekvivalentní HttpTrigger ve službě Azure Functions. Nový proxy server můžete vyzkoušet tak, že zkopírujete adresu URL proxy serveru a testováním pomocí svého oblíbeného klienta HTTP.

## <a name="modify-requests-responses"></a>Upravit požadavky a odpovědi

Pomocí proxy služby Azure Functions můžete upravit požadavků a odpovědí z back endu. Tyto transformace můžete použít proměnné, jak jsou definovány v [Použití proměnných].

### <a name="modify-backend-request"></a>Upravit požadavek back-end

Ve výchozím nastavení je požadavek back-end inicializován jako kopii původního požadavku. Kromě nastavení adresa URL back-end, můžete provádět změny metody HTTP, hlaviček a parametrů řetězce dotazu. Změněné hodnoty může odkazovat na [nastavení aplikace] a [parametry z původního požadavku klienta].

Požadavky na back-end lze upravit na portálu tak, že rozbalíte *přepsání žádosti* na stránce Podrobnosti o proxy serveru. 

### <a name="modify-response"></a>Upravit odpověď

Ve výchozím nastavení odpověď klienta inicializován jako kopii odpovědi back-end. Stavový kód odpovědi, frázi důvodu, záhlaví a text můžete provádět změny. Změněné hodnoty může odkazovat na [nastavení aplikace], [parametry z původního požadavku klienta], a [parametry z odpovědi back-end].

Požadavky na back-end lze upravit na portálu tak, že rozbalíte *přepsání odpovědi* na stránce Podrobnosti o proxy serveru. 

## <a name="using-variables"></a>Použití proměnných

Konfigurace proxy serveru, nemusí být statické. Podmínka vyhodnocena ho na použití proměnné z původního požadavku klienta, odpovědi back-end nebo nastavení aplikace.

### <a name="reference-localhost"></a>Referenční dokumentace lokální funkce
Můžete použít `localhost` odkazu na funkci uvnitř stejné aplikace function app přímo, bez žádost o proxy serveru umožňujícím zpětnou transformaci.

`"backendurl": "https://localhost/api/httptriggerC#1"` bude odkazovat na místní funkci aktivovanou protokolem HTTP na trasy `/api/httptriggerC#1`

 
>[!Note]  
>Pokud vaše funkce používá *funkci, správce nebo sys* úrovní autorizace, budete muset zadat kód a clientId, podle původní adresu URL funkce. V tomto případě odkaz vypadat nějak takto: `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"`

### <a name="request-parameters"></a>Odkaz na parametry žádosti

Parametry požadavku můžete použít jako vstupy pro vlastnost adresa URL back-end nebo jako součást úpravy žádostí a odpovědí. Některé parametry lze navázat z určený v konfiguraci proxy serveru základní šablonu trasy a ostatní můžou pocházet z vlastnosti příchozího požadavku.

#### <a name="route-template-parameters"></a>Parametry šablony trasy
Parametry, které se používají v šabloně trasy je možné odkazovat podle názvu. Názvy parametrů jsou uzavřeny ve složených závorkách ({}).

Pokud proxy server má šablonu trasy, jako například `/pets/{petId}`, adresa URL back-end může obsahovat hodnotu `{petId}`, například `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Pokud šablonu trasy ukončí v zástupné znaky, jako například `/api/{*restOfPath}`, hodnota `{restOfPath}` je řetězcové vyjádření zbývající segmentů cesty z příchozího požadavku.

#### <a name="additional-request-parameters"></a>Další parametry požadavku
Kromě parametrů šablony trasy je možné v rozšíření konfiguračních hodnot následující hodnoty:

* **{request.method}** : Metoda protokolu HTTP, který se používá u původního požadavku.
* **{request.headers.\<HeaderName\>}**: Hlavičky, který může číst z původního požadavku. Nahraďte *\<HeaderName\>* s názvem záhlaví, který chcete číst. Pokud není k dispozici hlavičky v požadavku, hodnota bude prázdný řetězec.
* **{request.querystring. \<ParameterName\>}**: Parametr řetězce dotazu, který může číst z původního požadavku. Nahraďte *\<ParameterName\>* s názvem parametru, který chcete číst. Pokud parametr není k dispozici v požadavku, hodnota bude prázdný řetězec.

### <a name="response-parameters"></a>Odkaz na odpověď back-end parametry

Parametry odpovědi může sloužit jako součást úpravy odpověď klientovi. V rozšíření konfiguračních hodnot je možné následující hodnoty:

* **{backend.response.statusCode}**: Stavový kód HTTP vrácená v odpovědi back-end.
* **{backend.response.statusReason}**: Fráze důvodu HTTP vrácená v odpovědi back-end.
* **{backend.response.headers.\<HeaderName\>}**: Hlavičky, který může číst z odpovědi back-end. Nahraďte *\<HeaderName\>* s názvem záhlaví chcete číst. Pokud hlavičce nejsou zahrnuty v odpovědi, hodnota bude prázdný řetězec.

### <a name="use-appsettings"></a>Nastavení odkazu na aplikaci

Je také možné odkazovat [definované pro aplikaci function app nastavení aplikace](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) tím, že název nastavení znaky procenta (%).

Například adresu URL back-end z *https://%ORDER_PROCESSING_HOST%/api/orders* musela "ORDER_PROCESSING_HOST %" nahrazeno hodnotou ORDER_PROCESSING_HOST nastavení.

> [!TIP] 
> Použít nastavení aplikace pro back-end hostitele, pokud máte více nasazení nebo testovací prostředí. Díky tomu, abyste měli jistotu, že vždy mluvíme do správné back endu pro příslušné prostředí.

## <a name="debugProxies"></a>Poradce při potížích s proxy servery

Přidáním příznak `"debug":true` na jakýkoli proxy server ve vaší `proxies.json` vám umožní povolit protokolování ladění. Protokoly se ukládají v `D:\home\LogFiles\Application\Proxies\DetailedTrace` a přístupné přes Rozšířené nástroje (kudu). Bude také obsahovat žádné odpovědi protokolu HTTP `Proxy-Trace-Location` záhlaví s adresou URL pro přístup k souboru protokolu.

Můžete ladit proxy ze strany klienta tak, že přidáte `Proxy-Trace-Enabled` záhlaví nastavena na `true`. Také se protokolu trasování do systému souborů a vrátí adresu URL pro trasovacího jako záhlaví v odpovědi.

### <a name="block-proxy-traces"></a>Blokovat proxy trasování

Z bezpečnostních důvodů nechcete umožnit všem volání služby pro generování trasování. Nebudou mít přístup k obsahu trasování bez své přihlašovací údaje, ale generuje se trasování využívá prostředky a uvádí, že používáte proxy serverů funkcí.

Úplně zakázat trasování tak, že přidáte `"debug":false` žádné konkrétní proxy serveru ve vaší `proxies.json`.

## <a name="advanced-configuration"></a>Pokročilá konfigurace

Proxy servery, které nakonfigurujete jsou uložené v *proxies.json* soubor, který je umístěn v kořenovém adresáři aplikace funkce. Můžete ručně upravit tento soubor a nasadit ho jako součást vaší aplikace, když použijete některou z [metody nasazení](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) této funkce podporuje. 

> [!TIP] 
> Pokud jste nenastavili jedné z metod nasazení, můžete také pracovat s *proxies.json* souboru na portálu. Přejděte do aplikace function app, vyberte **funkce platformy**a pak vyberte **App Service Editor**. Díky tomu můžete zobrazit celý soubor strukturu vaší aplikace function App a pak proveďte změny.

*Soubor Proxies.JSON* je definován objekt proxy, který se skládá z pojmenované proxy serverů a jejich definice. Případně, pokud editor podporuje, můžete odkazovat [schématu JSON](http://json.schemastore.org/proxies) pro dokončování kódu. Příklad souboru může vypadat nějak takto:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

Každý proxy má popisný název, například *proxy1* v předchozím příkladu. Odpovídající objekt definice proxy serveru je určené následující vlastnosti:

* **matchCondition**: Vyžaduje--objekt definující požadavků, které aktivují provádění tento proxy server. Obsahuje dvě vlastnosti, které jsou sdíleny s [Aktivační události HTTP]:
    * _metody_: Pole metody HTTP, které odpovídá proxy serveru. Pokud se nezadá, proxy serveru reaguje na všechny metody HTTP na trasy.
    * _trasa_: Požadováno – definuje šablonu trasy, řízení, které žádosti adresy URL vašeho proxy serveru reaguje na. Na rozdíl od v aktivační události HTTP, není žádná výchozí hodnota.
* **backendUri**: Adresa URL back-end prostředků, ke kterému by měl být požadavek směrovány přes proxy server. Tato hodnota může odkazovat nastavení aplikace a parametry z původního požadavku klienta. Pokud tato vlastnost není zahrnutý, Azure Functions odpoví HTTP 200 OK.
* **requestOverrides**: Objekt, který definuje transformaci na požadavek back-end. Zobrazit [Definování requestOverrides objektu].
* **responseOverrides**: Objekt, který definuje transformaci na odpověď klienta. Zobrazit [Definování responseOverrides objektu].

> [!NOTE] 
> *Trasy* nerespektuje vlastnost v proxy služby Azure Functions *routePrefix* vlastnost konfigurace hostitele aplikace Function App. Pokud chcete například zahrnout předponu `/api`, musí být zahrnut v *trasy* vlastnost.

### <a name="disableProxies"></a> Zakázat jednotlivé proxy

Jednotlivé proxy můžete zakázat přidáním `"disabled": true` k proxy serveru v `proxies.json` souboru. To způsobí, že všechny žádosti matchCondition schůzku vrátit kód 404.
```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "Root": {
            "disabled":true,
            "matchCondition": {
                "route": "/example"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

### <a name="applicationSettings"></a> Nastavení aplikace

Chování proxy můžete ovlivnit pomocí několika nastavení aplikace. Všechny jsou uvedeny v [odkaz nastavení aplikace funkcí](./functions-app-settings.md)

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azurefunctionproxydisablelocalcall)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azurefunctionproxybackendurldecodeslashes)

### <a name="reservedChars"></a> Vyhrazené znaky (formátování řetězce)

Proxy servery číst všechny řetězce z JSON soubor, pomocí \ jako escape symbol. Proxy servery také interpretaci složených závorek. Zobrazit úplnou sadu následující příklady.

|Znak|Řídicí znak|Příklad:|
|-|-|-|
|{nebo}|{{nebo}}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="requestOverrides"></a>Definování requestOverrides objektu

Objekt requestOverrides definuje změny provedené na požadavek při volání na prostředek back-end. Objekt je definován následující vlastnosti:

* **backend.Request.Method**: Metoda protokolu HTTP, který se používá k volání back endu.
* **backend.Request.QueryString. \<ParameterName\>**: Parametr řetězce dotazu, který lze nastavit pro volání do back endu. Nahraďte *\<ParameterName\>* s názvem parametru, který chcete nastavit. Pokud je zadán prázdný řetězec, parametr není zahrnutý v požadavku back-end.
* **backend.request.headers.\<HeaderName\>**: Záhlaví, které lze nastavit pro volání do back endu. Nahraďte *\<HeaderName\>* s názvem záhlaví, který chcete nastavit. Pokud zadáte prázdný řetězec, záhlaví není zahrnutý v požadavku back-end.

Hodnoty můžete odkazovat na parametry a nastavení aplikace z původního požadavku klienta.

Příklad konfigurace může vypadat nějak takto:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>",
            "requestOverrides": {
                "backend.request.headers.Accept": "application/xml",
                "backend.request.headers.x-functions-key": "%ANOTHERAPP_API_KEY%"
            }
        }
    }
}
```

### <a name="responseOverrides"></a>Definování responseOverrides objektu

Objekt requestOverrides definuje změny provedené v odpovědi, který je předán zpět do klienta. Objekt je definován následující vlastnosti:

* **response.statusCode**: Stavový kód HTTP má být vrácena klientovi.
* **response.statusReason**: Fráze důvodu HTTP má být vrácena klientovi.
* **Response.body**: Řetězcové vyjádření abych mohl být vrácen do klienta.
* **response.headers.\<HeaderName\>**: Záhlaví, které lze nastavit pro odpověď klientovi. Nahraďte *\<HeaderName\>* s názvem záhlaví, který chcete nastavit. Pokud zadáte prázdný řetězec, záhlaví není zahrnutý v odpovědi.

Hodnoty, se může odkazovat na nastavení aplikace, parametry z původního požadavku klienta a parametry z odpovědi back-end.

Příklad konfigurace může vypadat nějak takto:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "responseOverrides": {
                "response.body": "Hello, {test}",
                "response.headers.Content-Type": "text/plain"
            }
        }
    }
}
```
> [!NOTE] 
> V tomto příkladu tělo odpovědi nastavena přímo, takže ne `backendUri` vlastnost je potřeba. Příklad ukazuje, jak můžete použít proxy služby Azure Functions pro vytvoření modelu rozhraní API.

[Azure Portal]: https://portal.azure.com
[Aktivační události HTTP]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Definování requestOverrides objektu]: #requestOverrides
[Definování responseOverrides objektu]: #responseOverrides
[Nastavení aplikace]: #use-appsettings
[Použití proměnných]: #using-variables
[parametry z původního požadavku klienta]: #request-parameters
[parametry z odpovědi back-end]: #response-parameters
