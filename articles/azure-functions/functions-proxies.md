---
title: Práce se servery proxy ve funkcích Azure
description: Přehled používání azure functions proxy
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: 09e4616bc7cbb4361ad067ed64984ed95e9a20c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849186"
---
# <a name="work-with-azure-functions-proxies"></a>Práce s Azure Functions Proxy

Tento článek vysvětluje, jak nakonfigurovat a pracovat s Azure Functions Proxy. Pomocí této funkce můžete zadat koncové body v aplikaci funkce, které jsou implementovány jiným prostředkem. Tyto proxy servery můžete rozdělit velké rozhraní API do více funkčních aplikací (jako v architektuře mikroslužeb), zatímco stále představujete jeden povrch rozhraní API pro klienty.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Standardní funkce fakturace se vztahuje na spuštění proxy serveru. Další informace najdete v tématu [Azure Functions pricing](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create-a-proxy"></a><a name="create"></a>Vytvoření proxy serveru

Tato část ukazuje, jak vytvořit proxy server na portálu Funkce.

1. Otevřete [portál Azure]a přejděte do aplikace funkce.
2. V levém podokně vyberte **Nový proxy server**.
3. Zadejte název pro váš proxy.
4. Nakonfigurujte koncový bod, který je vystaven v této aplikaci funkce zadáním **šablony trasy** a **metod HTTP**. Tyto parametry se chovají podle pravidel pro [aktivační události HTTP].
5. Nastavte **adresu URL back-endu** na jiný koncový bod. Tento koncový bod může být funkce v jiné aplikaci funkce nebo může být jakékoli jiné rozhraní API. Hodnota nemusí být statická a může odkazovat na [nastavení a] parametry aplikace [z původního požadavku klienta].
6. Klikněte na **Vytvořit**.

Váš proxy server nyní existuje jako nový koncový bod v aplikaci funkce. Z pohledu klienta je ekvivalentní httptrigger v Azure functions. Můžete vyzkoušet svůj nový proxy zkopírováním proxy URL a testování mne s vaším oblíbeným klientem HTTP.

## <a name="modify-requests-and-responses"></a><a name="modify-requests-responses"></a>Úprava požadavků a odpovědí

Pomocí Azure Functions Proxy můžete upravit požadavky a odpovědi z back-endu. Tyto transformace lze použít proměnné, jak je definováno v [použít proměnné].

### <a name="modify-the-back-end-request"></a><a name="modify-backend-request"></a>Úprava požadavku back-endu

Ve výchozím nastavení je požadavek back-end inicializován jako kopie původního požadavku. Kromě nastavení adresy URL back-end u vyřizujete také parametry metody HTTP, záhlaví a řetězce dotazu. Upravené hodnoty mohou odkazovat na [nastavení a] parametry aplikace [z původního požadavku klienta].

Back-endové požadavky lze upravit na portálu rozbalením části *přepsání požadavku* na stránce podrobností proxy serveru. 

### <a name="modify-the-response"></a><a name="modify-response"></a>Změnit odpověď

Ve výchozím nastavení je odpověď klienta inicializována jako kopie odpovědi back-end. Můžete provést změny ve stavovém kódu odpovědi, frázi důvodu, záhlaví a textu. Upravené hodnoty mohou odkazovat na [nastavení aplikace], [parametry z původního požadavku klienta]a [parametry z back-endové odpovědi].

Back-endové požadavky lze upravit na portálu rozbalením části *přepsání odpovědi* na stránce podrobností proxy serveru. 

## <a name="use-variables"></a><a name="using-variables"></a>Použití proměnných

Konfigurace proxy nemusí být statická. Můžete ji podmínit použitím proměnných z původního požadavku klienta, odpovědi back-end nebo nastavení aplikace.

### <a name="reference-local-functions"></a><a name="reference-localhost"></a>Odkaz na místní funkce
Můžete použít `localhost` k odkazování na funkci uvnitř stejné aplikace funkce přímo, bez požadavku na rezonančný proxy server.

`"backendurl": "https://localhost/api/httptriggerC#1"`bude odkazovat na místní funkci spuštěnou http na trase`/api/httptriggerC#1`

 
>[!Note]  
>Pokud vaše funkce používá úrovně autorizace *funkce, admin nebo sys,* budete muset zadat kód a clientId, podle původní adresy URL funkce. V takovém případě by odkaz `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"` vypadal takto: Doporučujeme tyto klíče ukládat v [nastavení aplikace] a odkazovat na ně v proxy serverech. Tím se zabrání ukládání tajných kódů ve zdrojovém kódu. 

### <a name="reference-request-parameters"></a><a name="request-parameters"></a>Parametry referenčního požadavku

Parametry požadavku můžete použít jako vstupy do vlastnosti adresy URL back-end u nebo jako součást úpravy požadavků a odpovědí. Některé parametry mohou být vázány ze šablony trasy, která je zadána v konfiguraci základního proxy serveru, a jiné mohou pocházet z vlastností příchozího požadavku.

#### <a name="route-template-parameters"></a>Parametry šablony trasy
Parametry, které se používají v šabloně postupu, jsou k dispozici pro odkazování podle názvu. Názvy parametrů jsou uzavřeny{}ve složených závorkách ( ).

Pokud má například proxy server šablonu `/pets/{petId}`trasy, například , může `{petId}`adresa URL `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`back-endu obsahovat hodnotu aplikace , jako v . Pokud šablona trasy končí zástupný znak, například `/api/{*restOfPath}`, hodnota `{restOfPath}` je řetězec reprezentace zbývajících segmentů cesty z příchozí požadavek.

#### <a name="additional-request-parameters"></a>Další parametry požadavku
Kromě parametrů šablony trasy lze v konfiguračních hodnotách použít následující hodnoty:

* **{request.method}**: Metoda HTTP použitá u původního požadavku.
* **{request.headers.\< HeaderName\>}**: Hlavička, kterou lze číst z původního požadavku. Nahraďte * \<název záhlaví,\> * které chcete číst. Pokud záhlaví není zahrnuta v požadavku, hodnota bude prázdný řetězec.
* **{request.querystring.\< ParameterName\>}**: Parametr řetězce dotazu, který lze číst z původního požadavku. Nahraďte * \<název parametru,\> * který chcete číst. Pokud parametr není zahrnuta v požadavku, hodnota bude prázdný řetězec.

### <a name="reference-back-end-response-parameters"></a><a name="response-parameters"></a>Referenční parametry back-endové odpovědi

Parametry odpovědi lze použít jako součást úpravy odpovědi klientovi. V konfiguračních hodnotách lze použít následující hodnoty:

* **{backend.response.statusCode}**: Stavový kód HTTP vrácený při odpovědi back-end.
* **{backend.response.statusReason}**: Fráze důvodu HTTP, která je vrácena na odpověď back-end.
* **{backend.response.headers.\< HeaderName\>}**: Záhlaví, které lze číst z odpovědi back-end. Nahraďte * \<název záhlaví,\> * které chcete číst. Pokud záhlaví není zahrnuta v odpovědi, hodnota bude prázdný řetězec.

### <a name="reference-application-settings"></a><a name="use-appsettings"></a>Referenční nastavení aplikace

Můžete také odkazovat na [nastavení aplikace definovaná pro aplikaci funkce](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) tak, že název nastavení obklopíte znaky procenta (%).

Například adresa URL *https://%ORDER_PROCESSING_HOST%/api/orders* back-endu by nahradila %ORDER_PROCESSING_HOST%" hodnotou nastavení ORDER_PROCESSING_HOST.

> [!TIP] 
> Nastavení aplikace použijte pro back-endové hostitele, pokud máte více nasazení nebo testovacích prostředí. Tímto způsobem se můžete ujistit, že jste vždy mluví na správné back-end pro toto prostředí.

## <a name="troubleshoot-proxies"></a><a name="debugProxies"></a>Poradce při potížích se servery proxy

Přidáním příznaku `"debug":true` do libovolného proxy ve vašem `proxies.json` povolíte protokolování ladění. Protokoly jsou `D:\home\LogFiles\Application\Proxies\DetailedTrace` uloženy a přístupné prostřednictvím pokročilých nástrojů (kudu). Všechny odpovědi HTTP budou `Proxy-Trace-Location` také obsahovat záhlaví s adresou URL pro přístup k souboru protokolu.

Proxy server můžete ladit ze strany `Proxy-Trace-Enabled` klienta `true`přidáním sady hlaviček do aplikace . To bude také protokolovat trasování do systému souborů a vrátit adresu URL trasování jako záhlaví v odpovědi.

### <a name="block-proxy-traces"></a>Blokovat trasování proxy serveru

Z bezpečnostních důvodů možná nebudete chtít povolit komukoli, kdo volá vaši službu, aby vygeneroval trasování. Nebudou mít přístup k obsahu trasování bez přihlašovacích údajů, ale generování trasování spotřebovává prostředky a zveřejňuje, že používáte funkce proxy.

Zakázat trasování úplně `"debug":false` přidáním do `proxies.json`libovolného serveru proxy ve vašem .

## <a name="advanced-configuration"></a>Pokročilá konfigurace

Proxy servery, které nakonfigurujete, jsou uloženy v souboru *proxy.json,* který je umístěn v kořenovém adresáři aplikace funkce. Tento soubor můžete ručně upravit a nasadit jako součást aplikace, když použijete některou z [metod nasazení,](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) které funkce podporují. 

> [!TIP] 
> Pokud jste nenastavili jednu z metod nasazení, můžete také pracovat se souborem *proxy.json* na portálu. Přejděte do aplikace funkce, vyberte **funkce platformy**a pak vyberte **Editor služby App Service**. Tímto způsobem můžete zobrazit celou strukturu souborů aplikace funkce a potom provést změny.

*Soubor Proxy.json* je definován objektem proxy serverů, který se skládá z pojmenovaných proxy serverů a jejich definic. Volitelně, pokud jej editor podporuje, můžete odkazovat na [schéma JSON](http://json.schemastore.org/proxies) pro dokončení kódu. Ukázkový soubor může vypadat takto:

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

Každý proxy server má popisný název, například *proxy1* v předchozím příkladu. Odpovídající objekt definice proxy serveru je definován následujícími vlastnostmi:

* **matchCondition**: Povinné -- objekt definující požadavky, které aktivují spuštění tohoto proxy serveru. Obsahuje dvě vlastnosti, které jsou sdíleny s [aktivačními událostmi HTTP]:
    * _Metody_: Pole metod HTTP, na které proxy server reaguje. Pokud není zadán, proxy server odpoví na všechny metody HTTP na trase.
    * _trasa_: Povinné – definuje šablonu trasy a řídí, na které adresy URL požadavků proxy odpovídá. Na rozdíl od aktivačních událostí PROTOKOLU HTTP neexistuje žádná výchozí hodnota.
* **backendUri**: Adresa URL back-endového prostředku, ke kterému by měl být požadavek proxied. Tato hodnota může odkazovat na nastavení a parametry aplikace z původního požadavku klienta. Pokud tato vlastnost není zahrnuta, Azure Functions odpoví HTTP 200 OK.
* **requestOverrides**: Objekt, který definuje transformace na požadavek back-end. Viz [Definování objektu requestOverrides].
* **responseOverrides**: Objekt, který definuje transformace na odpověď klienta. Viz [Definování objektu responseOverrides].

> [!NOTE] 
> Vlastnost *route* v Azure Functions Proxy nerespektuje *vlastnost routePrefix* konfigurace hostitele aplikace funkce. Pokud chcete zahrnout předponu, `/api`například , musí být zahrnuta do vlastnosti *trasy.*

### <a name="disable-individual-proxies"></a><a name="disableProxies"></a>Zakázání jednotlivých proxy serverů

Jednotlivé servery proxy můžete `"disabled": true` zakázat přidáním `proxies.json` do proxy serveru v souboru. To způsobí, že všechny požadavky splňující matchCondition vrátit 404.
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

### <a name="application-settings"></a><a name="applicationSettings"></a>Nastavení aplikace

Chování proxy serveru lze řídit několika nastaveními aplikace. Všechny jsou popsány v [odkazu Nastavení aplikace funkce](./functions-app-settings.md)

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reserved-characters-string-formatting"></a><a name="reservedChars"></a>Vyhrazené znaky (formátování řetězců)

Proxy servery číst všechny řetězce ze souboru JSON, pomocí \ jako řídicí symbol. Proxy servery také interpretují složené závorky. Podívejte se na úplný soubor příkladů níže.

|Znak|Uvozený znak|Příklad|
|-|-|-|
|{ nebo }|{{ nebo }}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="define-a-requestoverrides-object"></a><a name="requestOverrides"></a>Definování objektu requestOverrides

Objekt requestOverrides definuje změny provedené v požadavku při volání back-endového prostředku. Objekt je definován následujícími vlastnostmi:

* **backend.request.method**: Metoda HTTP, která se používá k volání back-endu.
* **řetězec backend.request.querystring. ParameterName\>: Parametr řetězce dotazu, který lze nastavit pro volání back-endu. \<** Nahraďte * \<název parametru,\> * který chcete nastavit. Vezměte prosím na vědomí, že pokud je k dispozici prázdný řetězec, parametr je stále součástí požadavku back-end.
* **backend.request.headers. HeaderName\>: Záhlaví, které lze nastavit pro volání back-endu. \<** Nahraďte * \<název záhlaví,\> * které chcete nastavit. Pokud zadáte prázdný řetězec, záhlaví není zahrnuta v požadavku back-end.

Hodnoty mohou odkazovat na nastavení a parametry aplikace z původního požadavku klienta.

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

Objekt requestOverrides definuje změny, které jsou provedeny v odpovědi, která je předána zpět klientovi. Objekt je definován následujícími vlastnostmi:

* **response.statusCode**: Stavový kód HTTP, který má být vrácen klientovi.
* **response.statusReason**: Http důvod fráze, které mají být vráceny klientovi.
* **response.body**: Řetězcová reprezentace těla, které má být vráceno klientovi.
* **response.headers. HeaderName\>: Záhlaví, které lze nastavit pro odpověď klientovi. \<** Nahraďte * \<název záhlaví,\> * které chcete nastavit. Pokud zadáte prázdný řetězec, záhlaví není zahrnuta v odpovědi.

Hodnoty mohou odkazovat na nastavení aplikace, parametry z původního požadavku klienta a parametry z back-endové odpovědi.

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
> V tomto příkladu je tělo odpovědi `backendUri` nastaveno přímo, takže není potřeba žádná vlastnost. Příklad ukazuje, jak můžete použít Azure Functions Proxy pro zesměšňování API.

[Portál Azure]: https://portal.azure.com
[Aktivační události PROTOKOLU HTTP]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Definování objektu requestOverrides]: #requestOverrides
[Definování objektu responseOverrides]: #responseOverrides
[nastavení aplikace]: #use-appsettings
[Použití proměnných]: #using-variables
[parametry z původního požadavku klienta]: #request-parameters
[parametry z back-endové odpovědi]: #response-parameters
