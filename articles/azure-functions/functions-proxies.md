---
title: Práce se servery proxy v Azure Functions
description: Přehled o tom, jak používat proxy služby Azure Functions
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: dffdffdfa80d940c4a50d0a6630c665164f24d5c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230459"
---
# <a name="work-with-azure-functions-proxies"></a>Práce s proxy služby Azure Functions

Tento článek vysvětluje postup konfigurace a práce s proxy služby Azure Functions. Pomocí této funkce můžete určovat koncové body na aplikaci funkcí, které jsou implementované jiným prostředkem. Tyto servery proxy můžete použít k rozdělení velké rozhraní API do více aplikací funkcí (jako v architektuře mikroslužeb), přitom stále poskytují jednotnou rovinu rozhraní API pro klienty.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Standardní funkce fakturace se vztahuje na spuštění proxy serveru. Další informace najdete v tématu [Azure Functions ceny](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create"></a>Vytvoření proxy

V této části se dozvíte, jak vytvořit proxy server na portálu Functions.

1. Otevřete [Azure Portal]a pak použijte aplikaci Function App.
2. V levém podokně vyberte **nový proxy server**.
3. Zadejte název pro váš proxy server.
4. Nakonfigurujte koncový bod, který je vystavený v této aplikaci Function App, zadáním **šablony směrování** a **metod http**. Tyto parametry se chovají podle pravidel pro [aktivační události http].
5. Nastavte **adresu URL back-endu** na jiný koncový bod. Tento koncový bod může být funkce v jiné aplikaci function app, nebo může být jiné rozhraní API. Hodnota nemusí být statická a může odkazovat na [nastavení aplikace] a [parametry z původního požadavku na klienta].
6. Klikněte na možnost **Vytvořit**.

Váš proxy server nyní existuje jako nový koncový bod na vaši aplikaci function app. Z pohledu klienta je ekvivalentní HttpTrigger ve službě Azure Functions. Nový proxy server můžete vyzkoušet tak, že zkopírujete adresu URL proxy serveru a testováním pomocí svého oblíbeného klienta HTTP.

## <a name="modify-requests-responses"></a>Upravit žádosti a odpovědi

Pomocí proxy služby Azure Functions můžete upravit požadavků a odpovědí z back endu. Tyto transformace mohou používat proměnné, jak jsou definovány v [Použití proměnných].

### <a name="modify-backend-request"></a>Upravit požadavek back-endu

Ve výchozím nastavení je požadavek back-end inicializován jako kopii původního požadavku. Kromě nastavení adresa URL back-end, můžete provádět změny metody HTTP, hlaviček a parametrů řetězce dotazu. Změněné hodnoty můžou odkazovat na [nastavení aplikace] a [parametry z původního požadavku na klienta].

Back-endové požadavky lze upravit na portálu rozbalením části *přepsání požadavku* na stránce s podrobnostmi o proxy serveru. 

### <a name="modify-response"></a>Úprava odpovědi

Ve výchozím nastavení odpověď klienta inicializován jako kopii odpovědi back-end. Stavový kód odpovědi, frázi důvodu, záhlaví a text můžete provádět změny. Změněné hodnoty můžou odkazovat na [nastavení aplikace], [parametry z původního požadavku na klienta]a [parametry z back-endové odpovědi].

Back-endové požadavky lze upravit na portálu rozbalením části *přepsání odpovědi* na stránce s podrobnostmi o proxy serveru. 

## <a name="using-variables"></a>Použití proměnných

Konfigurace proxy serveru, nemusí být statické. Podmínka vyhodnocena ho na použití proměnné z původního požadavku klienta, odpovědi back-end nebo nastavení aplikace.

### <a name="reference-localhost"></a>Místní funkce odkazů
Pomocí `localhost` můžete přímo odkazovat na funkci v rámci stejné aplikace Function App bez požadavku na proxy zpětného převodu.

`"backendurl": "https://localhost/api/httptriggerC#1"` se na trase odkazuje na místní funkci aktivovanou protokolem HTTP `/api/httptriggerC#1`

 
>[!Note]  
>Pokud vaše funkce používá úrovně autorizace *Function, admin nebo sys* , bude nutné zadat kód a ClientID, jak je uvedeno na základě původní adresy URL funkce. V takovém případě by odkaz vypadal jako: `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"` doporučujeme ukládat tyto klíče do [nastavení aplikace] a odkazovat na ty v proxy serverech. Tím se vyhnete ukládání tajných kódů ve zdrojovém kódu. 

### <a name="request-parameters"></a>Parametry referenční žádosti

Parametry požadavku můžete použít jako vstupy pro vlastnost adresa URL back-end nebo jako součást úpravy žádostí a odpovědí. Některé parametry lze navázat z určený v konfiguraci proxy serveru základní šablonu trasy a ostatní můžou pocházet z vlastnosti příchozího požadavku.

#### <a name="route-template-parameters"></a>Parametry šablony trasy
Parametry, které se používají v šabloně trasy je možné odkazovat podle názvu. Názvy parametrů jsou uzavřeny ve složených závorkách ({}).

Například pokud má proxy šablonu směrování, například `/pets/{petId}`, adresa URL back-endu může zahrnovat hodnotu `{petId}`, jako v `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Pokud se v rámci šablony trasy ukončí zástupný znak, například `/api/{*restOfPath}`, hodnota `{restOfPath}` je řetězcová reprezentace zbývajících segmentů cesty od příchozího požadavku.

#### <a name="additional-request-parameters"></a>Další parametry požadavku
Kromě parametrů šablony trasy je možné v rozšíření konfiguračních hodnot následující hodnoty:

* **{Request. Method}** : metoda HTTP, která se používá pro původní požadavek.
* **{Request. Headers.\<záhlaví\>}** : záhlaví, které lze číst z původního požadavku. Nahraďte *\<header\>* názvem záhlaví, které chcete číst. Pokud není k dispozici hlavičky v požadavku, hodnota bude prázdný řetězec.
* **{Request. QueryString.\<ParameterName\>}** : parametr řetězce dotazu, který se dá načíst z původního požadavku. *\>\<ParameterName* nahraďte názvem parametru, který chcete číst. Pokud parametr není k dispozici v požadavku, hodnota bude prázdný řetězec.

### <a name="response-parameters"></a>Reference back-endové odezvy parametrů

Parametry odpovědi může sloužit jako součást úpravy odpověď klientovi. V rozšíření konfiguračních hodnot je možné následující hodnoty:

* **{back-endu. Response. StatusCode}** : stavový kód HTTP vrácený na back-endové odpovědi.
* **{back-endu. Response. statusReason}** : fráze důvod http vrácená na back-endové odpovědi.
* **{back-endu. Response. Headers.\<záhlaví\>}** : záhlaví, které lze číst z back-endové odpovědi. Nahraďte *\<header\>* názvem záhlaví, které chcete číst. Pokud hlavičce nejsou zahrnuty v odpovědi, hodnota bude prázdný řetězec.

### <a name="use-appsettings"></a>Referenční nastavení aplikace

Můžete také odkazovat na [nastavení aplikace, která jsou definována pro aplikaci Function App](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) , a to tak, že se název nastavení zobrazí jako znak procenta (%).

Například adresa URL back-endu *https://%ORDER_PROCESSING_HOST%/api/orders* by měla% ORDER_PROCESSING_HOST% nahrazena hodnotou nastavení ORDER_PROCESSING_HOST.

> [!TIP] 
> Použít nastavení aplikace pro back-end hostitele, pokud máte více nasazení nebo testovací prostředí. Díky tomu, abyste měli jistotu, že vždy mluvíme do správné back endu pro příslušné prostředí.

## <a name="debugProxies"></a>Řešení potíží s proxy

Přidáním příznaku `"debug":true` k jakémukoli proxy serveru v `proxies.json` povolíte protokolování ladění. Protokoly se ukládají v `D:\home\LogFiles\Application\Proxies\DetailedTrace` a jsou dostupné prostřednictvím pokročilých nástrojů (Kudu). Všechny odpovědi HTTP budou obsahovat také hlavičku `Proxy-Trace-Location` s adresou URL pro přístup k souboru protokolu.

Proxy server můžete ladit ze strany klienta přidáním `Proxy-Trace-Enabled` záhlaví nastaveného na `true`. Také se protokolu trasování do systému souborů a vrátí adresu URL pro trasovacího jako záhlaví v odpovědi.

### <a name="block-proxy-traces"></a>Blokovat proxy trasování

Z bezpečnostních důvodů nechcete umožnit všem volání služby pro generování trasování. Nebudou mít přístup k obsahu trasování bez své přihlašovací údaje, ale generuje se trasování využívá prostředky a uvádí, že používáte proxy serverů funkcí.

Zcela zakažte trasování tím, že přidáte `"debug":false` k jakémukoli konkrétnímu proxy serveru v `proxies.json`.

## <a name="advanced-configuration"></a>Pokročilá konfigurace

Proxy servery, které nakonfigurujete, se ukládají do souboru *proxy. JSON* , který se nachází v kořenovém adresáři adresáře Function App. Tento soubor můžete ručně upravit a nasadit jako součást aplikace při použití libovolné [metody nasazení](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) , které funkce podporuje. 

> [!TIP] 
> Pokud jste nestavili jednu z metod nasazení, můžete také na portálu pracovat se souborem *proxy. JSON* . Přejít do aplikace Function App, vybrat **funkce platformy**a pak vybrat **Editor služby App Service**. Díky tomu můžete zobrazit celý soubor strukturu vaší aplikace function App a pak proveďte změny.

*Proxy soubory. JSON* jsou definovány objektem proxy, který se skládá z pojmenovaných proxy a jejich definic. Případně, pokud je editor podporuje, můžete pro dokončení kódu odkazovat na [schéma JSON](http://json.schemastore.org/proxies) . Příklad souboru může vypadat nějak takto:

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

Každý proxy server má popisný název, například *Proxy1* v předchozím příkladu. Odpovídající objekt definice proxy serveru je určené následující vlastnosti:

* **matchCondition**: Required-objekt definující požadavky, které aktivují spuštění tohoto proxy serveru. Obsahuje dvě vlastnosti, které jsou sdílené pomocí [Aktivační události HTTP]:
    * _metody_: pole metod HTTP, na které proxy reaguje. Pokud se nezadá, proxy serveru reaguje na všechny metody HTTP na trasy.
    * _Route_: Required – definuje šablonu směrování, která určuje, na které adresy URL požadavků váš proxy reaguje. Na rozdíl od v aktivační události HTTP, není žádná výchozí hodnota.
* **netrvalou**adresu: adresa URL záložního prostředku, na který by se měl požadavek nacházet proxy serverem. Tato hodnota může odkazovat nastavení aplikace a parametry z původního požadavku klienta. Pokud tato vlastnost není zahrnutý, Azure Functions odpoví HTTP 200 OK.
* **requestOverrides**: objekt, který definuje transformace na back-end požadavek. Viz [Definování objektu requestOverrides].
* **responseOverrides**: objekt, který definuje transformace na reakci klienta. Viz [Definování objektu responseOverrides].

> [!NOTE] 
> Vlastnost *Route* v proxy služby Azure Functions nedodržuje vlastnost *routePrefix* Function App konfigurace hostitele. Chcete-li zahrnout předponu, například `/api`, musí být zahrnuta do vlastnosti *Route* .

### <a name="disableProxies"></a>Zakázat jednotlivé proxy servery

Jednotlivé proxy servery můžete zakázat přidáním `"disabled": true` k proxy serveru v souboru `proxies.json`. To způsobí, že všechny žádosti, které splňují matchCondition, vrátí 404.
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

### <a name="applicationSettings"></a>Nastavení aplikace

Chování proxy můžete ovlivnit pomocí několika nastavení aplikace. Všechny jsou uvedené v [Referenční příručce k nastavení aplikací Functions](./functions-app-settings.md)

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reservedChars"></a>Vyhrazené znaky (formátování řetězce)

Proxy přečtou všechny řetězce ze souboru JSON pomocí znaku \ jako řídicího symbolu. Proxy objekty také interpretují složené závorky. Podívejte se na celou sadu níže uvedených příkladů.

|Znak|Řídicí znak|Příklad|
|-|-|-|
|{nebo}|{{nebo}}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|.|\\\"| `\"example\"` --> `"example"`

### <a name="requestOverrides"></a>Definování objektu requestOverrides

Objekt requestOverrides definuje změny provedené na požadavek při volání na prostředek back-end. Objekt je definován následující vlastnosti:

* **back-end. Request. Method**: metoda HTTP, která se používá k volání back-endu.
* **back-end. Request. QueryString.\<ParameterName\>** : parametr řetězce dotazu, který lze nastavit pro volání back-endu. *\>\<ParameterName* nahraďte názvem parametru, který chcete nastavit. Pokud je zadán prázdný řetězec, parametr není zahrnutý v požadavku back-end.
* **back-end. Request. Headers.\<header\>** : záhlaví, které lze nastavit pro volání back-endu. Nahraďte *\<header\>* názvem záhlaví, které chcete nastavit. Pokud zadáte prázdný řetězec, záhlaví není zahrnutý v požadavku back-end.

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

### <a name="responseOverrides"></a>Definování objektu responseOverrides

Objekt requestOverrides definuje změny provedené v odpovědi, který je předán zpět do klienta. Objekt je definován následující vlastnosti:

* **Response. StatusCode**: stavový kód protokolu HTTP, který má být vrácen klientovi.
* **Response. statusReason**: fráze důvod http, která se má vrátit klientovi.
* **Response. body**: řetězcové vyjádření těla, které má být vráceno klientovi.
* **Response. Headers.\<header\>** : záhlaví, které lze nastavit pro odpověď na klienta. Nahraďte *\<header\>* názvem záhlaví, které chcete nastavit. Pokud zadáte prázdný řetězec, záhlaví není zahrnutý v odpovědi.

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
> V tomto příkladu je tělo odpovědi nastaveno přímo, takže není potřeba žádná `backendUri` vlastnost. Příklad ukazuje, jak můžete použít proxy služby Azure Functions pro vytvoření modelu rozhraní API.

[Azure Portal]: https://portal.azure.com
[Aktivační události HTTP]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Definování objektu requestOverrides]: #requestOverrides
[Definování objektu responseOverrides]: #responseOverrides
[nastavení aplikace]: #use-appsettings
[Použití proměnných]: #using-variables
[parametry z původního požadavku na klienta]: #request-parameters
[parametry z back-endové odpovědi]: #response-parameters
