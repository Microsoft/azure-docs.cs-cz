---
title: Práce se servery proxy v Azure Functions
description: Přehled způsobu použití Proxy služby Azure Functions
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: fb263239f99bcb4ec4c893b700d5c1cce078659f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96601369"
---
# <a name="work-with-azure-functions-proxies"></a>Práce s Proxy služby Azure Functions

Tento článek vysvětluje, jak nakonfigurovat Proxy služby Azure Functions a pracovat s nimi. Pomocí této funkce můžete zadat koncové body aplikace Function App, které jsou implementované jiným prostředkem. Tyto proxy servery můžete použít k rozdělení velkého rozhraní API do více aplikací Function App (jako v architektuře mikroslužeb), zatímco pro klienty prezentuje jednu plochu rozhraní API.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Fakturace standardních funkcí se vztahuje na provádění proxy serverů. Další informace najdete v tématu [Azure Functions ceny](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create-a-proxy"></a><a name="create"></a>Vytvoření proxy

V této části se dozvíte, jak vytvořit proxy na portálu Functions.

1. Otevřete [Azure Portal]a pak použijte aplikaci Function App.
2. V levém podokně vyberte **nový proxy server**.
3. Zadejte název proxy serveru.
4. Nakonfigurujte koncový bod, který je vystavený v této aplikaci Function App, zadáním **šablony směrování** a **metod http**. Tyto parametry se chovají podle pravidel pro [aktivační události http].
5. Nastavte **adresu URL back-endu** na jiný koncový bod. Tento koncový bod může být funkce v jiné aplikaci Function App nebo může to být jakékoli jiné rozhraní API. Hodnota nemusí být statická a může odkazovat na [nastavení aplikace] a [parametry z původní žádosti klienta].
6. Klikněte na **Vytvořit**.

Váš proxy server teď ve vaší aplikaci Function App existuje jako nový koncový bod. Z perspektivy klienta je ekvivalentem HttpTrigger v Azure Functions. Nový proxy server můžete vyzkoušet tak, že zkopírujete adresu URL proxy serveru a otestujete ji s vaším oblíbeným klientem HTTP.

## <a name="modify-requests-and-responses"></a><a name="modify-requests-responses"></a>Upravit žádosti a odpovědi

Pomocí Proxy služby Azure Functions můžete upravovat žádosti a odpovědi z back-endu. Tyto transformace mohou používat proměnné, jak jsou definovány v [proměnných použít].

### <a name="modify-the-back-end-request"></a><a name="modify-backend-request"></a>Upravit požadavek back-endu

Ve výchozím nastavení se požadavek back-endu inicializuje jako kopie původní žádosti. Kromě nastavení adresy URL back-endu můžete provádět změny v parametrech metody HTTP, hlaviček a řetězce dotazu. Změněné hodnoty můžou odkazovat na [nastavení aplikace] a [parametry z původní žádosti klienta].

Back-endové požadavky lze upravit na portálu rozbalením části *přepsání požadavku* na stránce s podrobnostmi o proxy serveru. 

### <a name="modify-the-response"></a><a name="modify-response"></a>Úprava odpovědi

Ve výchozím nastavení je odpověď klienta inicializována jako kopie back-endové odpovědi. Můžete provádět změny stavového kódu odpovědi, fráze důvodu, záhlaví a textu. Změněné hodnoty můžou odkazovat na [nastavení aplikace], [parametry z původního požadavku na klienta]a [parametry z back-endové odpovědi].

Back-endové požadavky lze upravit na portálu rozbalením části *přepsání odpovědi* na stránce s podrobnostmi o proxy serveru. 

## <a name="use-variables"></a><a name="using-variables"></a>Použití proměnných

Konfigurace proxy serveru nemusí být statická. Tuto podmínku můžete použít pro použití proměnných z původního požadavku na klienta, z back-endové odpovědi nebo z nastavení aplikace.

### <a name="reference-local-functions"></a><a name="reference-localhost"></a>Místní funkce odkazů
Můžete použít `localhost` pro odkazování na funkci v rámci stejné aplikace Function App přímo bez požadavku na proxy zpětného převodu.

`"backendUri": "https://localhost/api/httptriggerC#1"` provede odkaz na místní funkci aktivovanou protokolem HTTP v trase. `/api/httptriggerC#1`

 
>[!Note]  
>Pokud vaše funkce používá úrovně autorizace *Function, admin nebo sys* , bude nutné zadat kód a ClientID, jak je uvedeno na základě původní adresy URL funkce. V takovém případě by odkaz vypadal takto: `"backendUri": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"` tyto klíče doporučujeme uložit do [nastavení aplikace] a odkazovat na ně v proxy serverech. Tím se vyhnete ukládání tajných kódů ve zdrojovém kódu. 

### <a name="reference-request-parameters"></a><a name="request-parameters"></a>Parametry referenční žádosti

Parametry požadavku můžete použít jako vstupy do vlastnosti back-end adresy URL nebo jako součást úprav požadavků a odpovědí. Některé parametry mohou být vázány ze šablony trasy, která je zadána v základní konfiguraci proxy serveru, a další mohou pocházet z vlastností příchozího požadavku.

#### <a name="route-template-parameters"></a>Parametry šablony směrování
Parametry, které se používají v šabloně směrování, jsou k dispozici na odkaz podle názvu. Názvy parametrů jsou uzavřeny v závorkách ( {} ).

Například pokud má proxy šablonu směrování, například `/pets/{petId}` , adresa URL back-endu může obsahovat hodnotu `{petId}` , jako v `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}` . Pokud se v rámci šablony trasy ukončí zástupný znak, například `/api/{*restOfPath}` , `{restOfPath}` je hodnota řetězcové vyjádření zbývajících segmentů cesty od příchozího požadavku.

#### <a name="additional-request-parameters"></a>Další parametry žádosti
Kromě parametrů šablony směrování lze v konfiguračních hodnotách použít následující hodnoty:

* **{Request. Method}**: metoda HTTP, která se používá pro původní požadavek.
* **{Request. Headers. \<HeaderName\> }**: záhlaví, které lze číst z původního požadavku. Nahraďte *\<HeaderName\>* názvem záhlaví, které chcete číst. Pokud hlavička není obsažena v požadavku, bude tato hodnota prázdným řetězcem.
* **{Request. QueryString. \<ParameterName\> }**: parametr řetězce dotazu, který lze načíst z původního požadavku. Nahraďte *\<ParameterName\>* názvem parametru, který chcete číst. Pokud parametr není součástí požadavku, bude hodnota prázdným řetězcem.

### <a name="reference-back-end-response-parameters"></a><a name="response-parameters"></a>Reference back-endové odezvy parametrů

Parametry odpovědi lze použít jako součást změny odpovědi na klienta. V konfiguračních hodnotách lze použít následující hodnoty:

* **{back-endu. Response. StatusCode}**: stavový kód HTTP vrácený na back-endové odpovědi.
* **{back-endu. Response. statusReason}**: fráze důvod http vrácená na back-endové odpovědi.
* **{back-endu. Response. Headers. \<HeaderName\> }**: záhlaví, které lze číst z back-endové odpovědi. Nahraďte *\<HeaderName\>* názvem záhlaví, které chcete číst. Pokud hlavička není obsažena v odpovědi, bude hodnota prázdným řetězcem.

### <a name="reference-application-settings"></a><a name="use-appsettings"></a>Referenční nastavení aplikace

Můžete také odkazovat na [nastavení aplikace, která jsou definována pro aplikaci Function App](./functions-how-to-use-azure-function-app-settings.md) , a to tak, že se název nastavení zobrazí jako znak procenta (%).

Například adresa URL back-endu *https://%ORDER_PROCESSING_HOST%/api/orders* by měla být "% ORDER_PROCESSING_HOST%" nahrazena hodnotou nastavení ORDER_PROCESSING_HOST.

> [!TIP] 
> Nastavení aplikace pro hostitele back-endu použijte v případě, že máte více nasazení nebo testovacích prostředí. Tímto způsobem se můžete ujistit, že vždycky mluvíte k pravému back-endu pro toto prostředí.

## <a name="troubleshoot-proxies"></a><a name="debugProxies"></a>Řešení potíží s proxy

Přidáním příznaku `"debug":true` k jakémukoli proxy serveru v umožníte `proxies.json` protokolování ladění. Protokoly se ukládají v `D:\home\LogFiles\Application\Proxies\DetailedTrace` a jsou přístupné prostřednictvím pokročilých nástrojů (Kudu). Všechny odpovědi HTTP budou také obsahovat `Proxy-Trace-Location` hlavičku s adresou URL pro přístup k souboru protokolu.

Můžete ladit proxy server ze strany klienta přidáním `Proxy-Trace-Enabled` záhlaví nastaveného na `true` . Tím se také zaznamená trasování do systému souborů a vrátí adresu URL trasování jako hlavičku v odpovědi.

### <a name="block-proxy-traces"></a>Blokovat trasování proxy

Z bezpečnostních důvodů možná nebudete chtít, aby bylo možné vygenerovat trasování všem voláním vaší služby. Nebudou mít přístup k obsahu trasování bez přihlašovacích údajů, ale generování trasování spotřebuje prostředky a zpřístupňuje používání proxy funkcí.

Zcela zakažte trasování přidáním `"debug":false` určitého proxy serveru v `proxies.json` .

## <a name="advanced-configuration"></a>Pokročilá konfigurace

Proxy servery, které nakonfigurujete, se ukládají do *proxies.jsv* souboru, který se nachází v kořenovém adresáři adresáře Function App. Tento soubor můžete ručně upravit a nasadit jako součást aplikace při použití libovolné [metody nasazení](./functions-continuous-deployment.md) , které funkce podporuje. 

> [!TIP] 
> Pokud jste nestavili jednu z metod nasazení, můžete také pracovat s *proxies.js* v souboru na portálu. Přejít do aplikace Function App, vybrat **funkce platformy** a pak vybrat **Editor služby App Service**. Díky tomu můžete zobrazit celou strukturu souborů aplikace Function App a pak provést změny.

*Proxies.js* je definována objektem proxy, který se skládá z pojmenovaných proxy a jejich definic. Případně, pokud je editor podporuje, můžete pro dokončení kódu odkazovat na [schéma JSON](http://json.schemastore.org/proxies) . Příklad souboru může vypadat takto:

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

Každý proxy server má popisný název, například *Proxy1* v předchozím příkladu. Odpovídající objekt definice proxy je definován následujícími vlastnostmi:

* **matchCondition**: Required-objekt definující požadavky, které aktivují spuštění tohoto proxy serveru. Obsahuje dvě vlastnosti, které jsou sdílené pomocí [aktivačních událostí http]:
    * _metody_: pole metod HTTP, na které proxy reaguje. Pokud není zadaný, proxy server odpoví na všechny metody HTTP v trase.
    * _Route_: Required – definuje šablonu směrování, která určuje, na které adresy URL požadavků váš proxy reaguje. Na rozdíl od triggerů HTTP neexistuje žádná výchozí hodnota.
* **netrvalou** adresu: adresa URL záložního prostředku, na který by se měl požadavek nacházet proxy serverem. Tato hodnota může odkazovat na nastavení aplikace a parametry z původní žádosti klienta. Pokud tato vlastnost není zahrnuta, Azure Functions odpoví pomocí protokolu HTTP 200 OK.
* **requestOverrides**: objekt, který definuje transformace na back-end požadavek. Viz [Definování objektu requestOverrides].
* **responseOverrides**: objekt, který definuje transformace na reakci klienta. Viz [Definování objektu responseOverrides].

> [!NOTE] 
> Vlastnost *Route* v proxy služby Azure Functions nedodržuje vlastnost *routePrefix* Function App konfigurace hostitele. Pokud chcete zahrnout předponu jako `/api` , musí být součástí vlastnosti *Route* .

### <a name="disable-individual-proxies"></a><a name="disableProxies"></a> Zakázat jednotlivé proxy servery

Jednotlivé proxy servery můžete zakázat přidáním `"disabled": true` k proxy serveru v `proxies.json` souboru. To způsobí, že všechny žádosti, které splňují matchCondition, vrátí 404.
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

### <a name="application-settings"></a><a name="applicationSettings"></a> Nastavení aplikace

Chování proxy serveru se dá řídit několika nastaveními aplikace. Všechny jsou uvedené v [Referenční příručce k nastavení aplikací Functions](./functions-app-settings.md)

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reserved-characters-string-formatting"></a><a name="reservedChars"></a> Vyhrazené znaky (formátování řetězce)

Proxy přečtou všechny řetězce ze souboru JSON pomocí znaku \ jako řídicího symbolu. Proxy objekty také interpretují složené závorky. Podívejte se na celou sadu níže uvedených příkladů.

|Znak|Řídicí znak|Příklad|
|-|-|-|
|ani|{{nebo}}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="define-a-requestoverrides-object"></a><a name="requestOverrides"></a>Definování objektu requestOverrides

Objekt requestOverrides definuje změny provedené v žádosti při volání prostředku back-endu. Objekt je definován následujícími vlastnostmi:

* **back-end. Request. Method**: metoda HTTP, která se používá k volání back-endu.
* **back-end. Request. \<ParameterName\> QueryString.**: parametr řetězce dotazu, který lze nastavit pro volání back-endu. Nahraďte *\<ParameterName\>* názvem parametru, který chcete nastavit. Všimněte si, že je-li k dispozici prázdný řetězec, je parametr stále obsažen v požadavku back-endu.
* **back-end. Request. \<HeaderName\> Headers.**: záhlaví, které lze nastavit pro volání back-endu. Nahraďte *\<HeaderName\>* názvem záhlaví, které chcete nastavit. Všimněte si, že je-li k dispozici prázdný řetězec, je parametr stále obsažen v požadavku back-endu.

Hodnoty můžou odkazovat na nastavení aplikace a parametry z původní žádosti klienta.

Příklad konfigurace může vypadat takto:

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

### <a name="define-a-responseoverrides-object"></a><a name="responseOverrides"></a>Definování objektu responseOverrides

Objekt requestOverrides definuje změny provedené v odpovědi, která je předána zpět klientovi. Objekt je definován následujícími vlastnostmi:

* **Response. StatusCode**: stavový kód protokolu HTTP, který má být vrácen klientovi.
* **Response. statusReason**: fráze důvod http, která se má vrátit klientovi.
* **Response. body**: řetězcové vyjádření těla, které má být vráceno klientovi.
* **Response. Headers \<HeaderName\> .**: záhlaví, které lze nastavit pro odpověď na klienta. Nahraďte *\<HeaderName\>* názvem záhlaví, které chcete nastavit. Pokud zadáte prázdný řetězec, hlavička není obsažena v odpovědi.

Hodnoty můžou odkazovat na nastavení aplikace, parametry z původního požadavku na klienta a parametry z back-endové odpovědi.

Příklad konfigurace může vypadat takto:

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
> V tomto příkladu je tělo odpovědi nastaveno přímo, takže `backendUri` není potřeba žádná vlastnost. Příklad ukazuje, jak můžete použít Proxy služby Azure Functions pro napodobování rozhraní API.

[Azure Portal]: https://portal.azure.com
[Aktivační události HTTP]: ./functions-bindings-http-webhook.md
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Definování objektu requestOverrides]: #requestOverrides
[Definování objektu responseOverrides]: #responseOverrides
[nastavení aplikace]: #use-appsettings
[Použití proměnných]: #using-variables
[parametry z původní žádosti klienta]: #request-parameters
[parametry z back-endové odpovědi]: #response-parameters
