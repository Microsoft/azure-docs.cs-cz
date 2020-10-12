---
title: Přizpůsobení koncového bodu HTTP v Azure Functions
description: Přečtěte si, jak přizpůsobit koncový bod triggeru HTTP v Azure Functions
author: mattchenderson
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: 440eb1f39284f8d99a8d6b9067b018c4a54fcd27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87083017"
---
# <a name="customize-an-http-endpoint-in-azure-functions"></a>Přizpůsobení koncového bodu HTTP v Azure Functions

V tomto článku se dozvíte, jak Azure Functions umožňuje vytvářet vysoce škálovatelná rozhraní API. Azure Functions se dodává s kolekcí integrovaných triggerů HTTP a vazeb, které usnadňují vytváření koncových bodů v nejrůznějších jazycích, včetně Node.js, C# a dalších. V tomto článku budete přizpůsobovat Trigger HTTP, který bude zpracovávat konkrétní akce v návrhu rozhraní API. Také se připravujete pro rostoucí rozhraní API integrací s Proxy služby Azure Functions a nastavením přípravných rozhraní API. Tyto úlohy se provádí na základě funkcí prostředí COMPUTE bez serveru, takže se nemusíte starat o škálování prostředků – stačí se zaměřit na logiku rozhraní API.

## <a name="prerequisites"></a>Požadavky 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

Výsledná funkce bude použita pro zbytek tohoto článku.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.

## <a name="customize-your-http-function"></a>Přizpůsobení funkce HTTP

Ve výchozím nastavení je funkce triggeru protokolu HTTP nakonfigurovaná tak, aby přijímala jakoukoli metodu HTTP. Můžete použít také výchozí adresu URL, `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>` . V této části upravíte funkci tak, aby odpovídala pouze na požadavky GET `/api/hello` . 

1. Přejděte ke své funkci na webu Azure Portal. V nabídce vlevo vyberte **integrace** a v části **Trigger**vyberte **http (REQ)** .

    :::image type="content" source="./media/functions-create-serverless-api/customizing-http.png" alt-text="Přizpůsobení funkce HTTP":::

1. Použijte nastavení triggeru HTTP, jak je uvedeno v následující tabulce.

    | Pole | Ukázková hodnota | Description |
    |---|---|---|
    | Šablona trasy | /hello | Určuje, jaká trasa se používá k vyvolání této funkce. |
    | Úroveň autorizace | Anonymní | Volitelné: Zpřístupňuje vaši funkci bez klíče rozhraní API. |
    | Vybrané metody HTTP | GET | Umožňuje, aby se k vyvolání této funkce daly použít jen vybrané metody HTTP. |

    Do šablony trasy jste nezahrnuli `/api` předponu základní cesty, protože je zpracovávána globálním nastavením.

1. Vyberte **Uložit**.

Další informace o přizpůsobení funkcí HTTP najdete v tématu [Azure Functions vazby HTTP](./functions-bindings-http-webhook.md).

### <a name="test-your-api"></a>Testování rozhraní API

V dalším kroku otestujte svoji funkci, abyste viděli, jak funguje s novým povrchem rozhraní API:
1. Na stránce funkce vyberte v nabídce vlevo příkaz **Code + test** .

1. V horní nabídce vyberte **získat adresu URL funkce** a zkopírujte adresu URL. Potvrďte, že teď používá `/api/hello` cestu.
 
1. Zkopírujte tuto adresu URL do nové záložky prohlížeče nebo preferovaného klienta REST. 

   Ve výchozím nastavení používají prohlížeče.
 
1. Přidejte parametry do řetězce dotazu v adrese URL. 

   Například, `/api/hello/?name=John`.
 
1. Stisknutím klávesy ENTER potvrďte, že funguje. Měla by se zobrazit odpověď "*Hello. Jan*".

1. Můžete také vyzkoušet volání koncového bodu s jinou metodou protokolu HTTP pro potvrzení, že funkce není provedena. Uděláte to tak, že použijete klienta REST, jako je například kudrlinkou, post nebo Fiddler.

## <a name="proxies-overview"></a>Přehled proxy

V další části budete své rozhraní API nakládat prostřednictvím proxy serveru. Proxy služby Azure Functions umožňuje předávat žádosti jiným prostředkům. Koncový bod HTTP definujete stejně jako u triggeru HTTP. Nicméně místo psaní kódu, který se má provést při volání tohoto koncového bodu, poskytnete adresu URL vzdálené implementace. Díky tomu můžete vytvořit více zdrojů rozhraní API do jediného povrchu rozhraní API, které můžou klienti snadno využít. to je užitečné, pokud chcete své rozhraní API sestavit jako mikroslužby.

Proxy může odkazovat na libovolný prostředek HTTP, například na:
- Azure Functions 
- Aplikace API ve službě [Azure App Service](../app-service/overview.md)
- Kontejnery Dockeru ve službě [App Service v Linuxu](../app-service/containers/app-service-linux-intro.md)
- Jakékoli jiné hostované rozhraní API

Další informace o proxy najdete v článku [Práce s Proxy služby Azure Functions].

## <a name="create-your-first-proxy"></a>Vytvoření prvního proxy

V této části vytvoříte nový proxy server, který slouží jako front-end pro vaše celkové rozhraní API. 

### <a name="setting-up-the-frontend-environment"></a>Nastavení prostředí front-endu

Zopakováním postupu v článku o [vytvoření aplikace funkcí](./functions-create-first-azure-function.md#create-a-function-app) vytvořte novou aplikaci funkcí, ve které vytvoříte proxy. Tato nová adresa URL aplikace slouží jako front-end pro naše rozhraní API a aplikace Function App, kterou jste předtím upravovali, slouží jako back-end.

1. Na portálu přejděte na novou front-endovou aplikaci funkcí.
1. Vyberte **Funkce platformy** a zvolte **Nastavení aplikace**.
1. Posuňte se dolů k **nastavení aplikace**, kde jsou uložené páry klíč/hodnota a vytvořte nové nastavení s klíčem `HELLO_HOST` . Nastavte jeho hodnotu na hostitele vaší back-endové aplikace funkcí, například `<YourBackendApp>.azurewebsites.net`. Tato hodnota je součástí adresy URL, kterou jste zkopírovali dříve při testování funkce HTTP. Na toto nastavení později odkážete v konfiguraci.

    > [!NOTE] 
    > Ke konfiguraci hostitele se doporučuje použít nastavení aplikace. Zabráníte tím, aby byla u proxy pevně zakódovaná závislost na prostředí. Když použijete nastavení aplikace, můžete konfiguraci proxy přesouvat mezi prostředími, přičemž se použijí nastavení aplikace specifická pro dané prostředí.

1. Vyberte **Uložit**.

### <a name="creating-a-proxy-on-the-frontend"></a>Vytvoření proxy na front-endu

1. Přejděte zpátky na aplikaci funkcí front-end na portálu.

1. V nabídce vlevo vyberte **proxy servery**a pak vyberte **Přidat**. 

1. Na stránce **nový proxy** použijte nastavení v následující tabulce a pak vyberte **vytvořit**.

    | Pole | Ukázková hodnota | Popis |
    |---|---|---|
    | Název | HelloProxy | Popisný název sloužící jen ke správě |
    | Šablona trasy | /api/remotehello | Určuje, jaká trasa se používá k vyvolání tohoto proxy. |
    | Adresa URL back-endu | https://%HELLO_HOST%/api/hello | Určuje koncový bod, na který má být žádost přes proxy směrována. |

    
    :::image type="content" source="./media/functions-create-serverless-api/creating-proxy.png" alt-text="Přizpůsobení funkce HTTP":::

    Proxy služby Azure Functions neposkytuje `/api` předponu základní cesty, která musí být obsažena v šabloně trasy. `%HELLO_HOST%`Syntaxe odkazuje na nastavení aplikace, které jste vytvořili dříve. Přeložená adresa URL bude odkazovat na vaši původní funkci.

1. Vyzkoušejte si nový proxy tak, že zkopírujete adresu URL proxy serveru a otestujete ji v prohlížeči nebo pomocí vašeho oblíbeného klienta HTTP:
    - Pro anonymní použití funkce:   `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?name="Proxies"` .
    - Pro funkci s autorizačním použitím:   `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?code=YOURCODE&name="Proxies"` .

## <a name="create-a-mock-api"></a>Vytvoření napodobeniny rozhraní API

Potom použijete proxy server k vytvoření rozhraní API pro vaše řešení. Tento proxy server umožňuje vývoj klientů, aniž by bylo nutné plně implementovat back-end. Později ve vývojovém prostředí můžete vytvořit novou aplikaci Function App, která podporuje tuto logiku a přesměruje na ni svůj proxy server.

Pokud chcete vytvořit toto rozhraní API pro vytváření, vytvoříme nový proxy server, tentokrát pomocí [Editor služby App Service](https://github.com/projectkudu/kudu/wiki/App-Service-Editor). Začněte tím, že na portálu přejdete na aplikaci funkcí. Vyberte **funkce platformy**a v části **vývojové nástroje** Najděte **Editor služby App Service**. Na nové kartě se otevře Editor služby App Service.

Vyberte `proxies.json` v levém navigačním panelu. Tento soubor uchovává konfiguraci pro všechny vaše proxy servery. Pokud používáte jednu z [metod nasazení Functions](./functions-continuous-deployment.md), můžete tento soubor udržovat ve správě zdrojového kódu. Další informace o tomto souboru najdete v článku o [pokročilé konfiguraci proxy](./functions-proxies.md#advanced-configuration).

Pokud jste následovali společně, proxies.jsby měl vypadat takto:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

Dále přidáte své rozhraní API pro návrhy. Nahraďte proxies.jssouboru následujícím kódem:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        },
        "GetUserByName" : {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/users/{username}"
            },
            "responseOverrides": {
                "response.statusCode": "200",
                "response.headers.Content-Type" : "application/json",
                "response.body": {
                    "name": "{username}",
                    "description": "Awesome developer and master of serverless APIs",
                    "skills": [
                        "Serverless",
                        "APIs",
                        "Azure",
                        "Cloud"
                    ]
                }
            }
        }
    }
}
```

Tento kód přidá nový proxy server `GetUserByName` bez `backendUri` Vlastnosti. Místo volání jiného prostředku upravuje výchozí odpověď od proxy pomocí přepisu odpovědi. Přepisy žádostí a odpovědí lze použít také ve spojení s adresou URL back-endu. Tato technika je užitečná hlavně při proxy serveru do starší verze systému, kde možná budete muset změnit hlavičky, parametry dotazů a tak dále. Další informace o přepisech žádostí a odpovědí najdete v článku o [úpravách žádostí a odpovědí v proxy](./functions-proxies.md).

Otestujte napodobeninu rozhraní API voláním koncového bodu `<YourProxyApp>.azurewebsites.net/api/users/{username}` pomocí prohlížeče nebo oblíbeného klienta REST. Nezapomeňte nahradit _{username}_ řetězcovou hodnotou představující uživatelské jméno.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak vytvořit a přizpůsobit rozhraní API na Azure Functions. Také jste zjistili, jak spojit více rozhraní API (včetně napodobenin) do jednoho sjednoceného rozsahu rozhraní API. Pomocí těchto technik můžete vytvořit jakkoli složité rozhraní API, a zároveň používat bezserverový výpočetní model, který poskytuje služba Azure Functions.

Při dalším vývoji rozhraní API vám mohou přijít vhod následující odkazy:

- [Vazby HTTP ve službě Azure Functions](./functions-bindings-http-webhook.md)
- [Práce s Proxy služby Azure Functions]
- [Dokumentace rozhraní API služby Azure Functions (verze Preview)](./functions-openapi-definition.md)


[Create your first function]: ./functions-create-first-azure-function.md
[Práce s Proxy služby Azure Functions]: ./functions-proxies.md
