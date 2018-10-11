---
title: Vytvoření bezserverového rozhraní API pomocí služby Azure Functions | Microsoft Docs
description: Jak vytvořit bezserverové rozhraní API pomocí služby Azure Functions
services: functions
author: mattchenderson
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: tutorial
ms.date: 05/04/2017
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: 9a35c1205c0b564c8d0db1fbd0535d41bb9c84a0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989902"
---
# <a name="create-a-serverless-api-using-azure-functions"></a>Vytvoření bezserverového rozhraní API pomocí služby Azure Functions

V tomto kurzu se dozvíte, jak služba Azure Functions umožňuje vytvářet vysoce škálovatelná rozhraní API. Služba Azure Functions se dodává s kolekcí integrovaných triggerů a vazeb HTTP, které usnadňují vytvoření koncového bodu v nejrůznějších jazycích včetně Node.JS, C# a dalších. V tomto kurzu přizpůsobíte trigger HTTP tak, aby ve vašem návrhu rozhraní API obsluhoval konkrétní akce. Zároveň se připravíte na rozšíření tohoto rozhraní API integrací Proxy služby Azure Functions a nastavením napodobenin rozhraní API. To vše se provádí v bezserverovém výpočetním prostředí služby Functions, takže se nemusíte starat o škálování prostředků, ale zaměříte se jen na logiku svého rozhraní API.

## <a name="prerequisites"></a>Požadavky 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

Výsledná funkce se bude používat ve zbývajících částech tohoto kurzu.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Otevřete web Azure Portal. To uděláte tak, že se k webu [https://portal.azure.com](https://portal.azure.com) přihlásíte pod svým účtem Azure.

## <a name="customize-your-http-function"></a>Přizpůsobení funkce HTTP

Funkce aktivovaná protokolem HTTP je standardně nakonfigurovaná tak, aby přijímala jakoukoli metodu HTTP. Používá také výchozí adresu URL ve formátu `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>`. Pokud jste postupovali podle tohoto rychlého startu, pak `<funcname>` pravděpodobně vypadá jako „HttpTriggerJS1“. V této části upravíte tuto funkci tak, aby místo toho reagovala jen na žádosti GET vůči trase `/api/hello`. 

1. Přejděte ke své funkci na webu Azure Portal. V levém navigačním panelu vyberte **Integrace**.

    ![Přizpůsobení funkce HTTP](./media/functions-create-serverless-api/customizing-http.png)

1. Použijte nastavení triggeru HTTP uvedená v tabulce.

    | Pole | Ukázková hodnota | Popis |
    |---|---|---|
    | Povolené metody HTTP | Vybrané metody | Určuje, jaké metody HTTP mohou být použity k vyvolání této funkce. |
    | Vybrané metody HTTP | GET | Umožňuje, aby se k vyvolání této funkce daly použít jen vybrané metody HTTP. |
    | Šablona trasy | /hello | Určuje, jaká trasa se používá k vyvolání této funkce. |
    | Úroveň autorizace | Anonymní | Volitelné: Zpřístupňuje vaši funkci bez klíče rozhraní API. |

    > [!NOTE] 
    > Všimněte si, že jste do šablony trasy nezahrnuli předponu základní trasy `/api`, protože to se řeší globálním nastavením.

1. Klikněte na **Uložit**.

Další informace o přizpůsobení funkcí HTTP najdete v článku o [vazbách HTTP ve službě Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#customizing-the-http-endpoint).

### <a name="test-your-api"></a>Testování rozhraní API

V dalším kroku svou funkci otestujete, abyste viděli, jak funguje s novým rozsahem rozhraní API.
1. Kliknutím na název funkce v levém navigačním panelu se vraťte na stránku pro vývoj.
1. Klikněte na **Získat adresu URL funkce** a zkopírujte adresu URL. Měli byste vidět, že se teď používá trasa `/api/hello`.
1. Zkopírujte tuto adresu URL do nové záložky prohlížeče nebo preferovaného klienta REST. Prohlížeče ve výchozím nastavení použijí GET.
1. Přidejte do řetězce dotazu v adrese URL parametry, například `/api/hello/?name=John`.
1. Stisknutím klávesy Enter potvrďte funkčnost. Měli byste vidět odpověď „*Hello John*“.
1. Můžete také zkusit volání koncového bodu pomocí jiné metody HTTP a potvrdit tak, že se tato funkce nespustí. K tomuto účelu budete potřebovat klienta REST, například cURL, Postman nebo Fiddler.

## <a name="proxies-overview"></a>Přehled proxy

V další části zpřístupníte rozhraní API prostřednictvím proxy. Proxy služby Azure Functions umožňuje předávat žádosti jiným prostředkům. Koncový bod HTTP definujete stejně jako u triggeru HTTP, ale místo psaní kódu, který se má provést při volání tohoto koncového bodu, zadáte adresu URL vzdálené implementace. To vám umožňuje složit více zdrojů rozhraní API do jednoho rozsahu rozhraní API, který mohou klienti snadno využívat. Zvláště to oceníte, pokud chcete vytvořit rozhraní API ve formě mikroslužeb.

Proxy může odkazovat na libovolný prostředek HTTP, například na:
- Azure Functions 
- Aplikace API ve službě [Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-overview)
- Kontejnery Dockeru ve službě [App Service v Linuxu](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)
- Jakékoli jiné hostované rozhraní API

Další informace o proxy najdete v článku [Práce s Proxy služby Azure Functions].

## <a name="create-your-first-proxy"></a>Vytvoření prvního proxy

V této části vytvoříte nový proxy, který slouží jako front-end pro vaše kompletní rozhraní API. 

### <a name="setting-up-the-frontend-environment"></a>Nastavení prostředí front-endu

Zopakováním postupu v článku o [vytvoření aplikace funkcí](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function#create-a-function-app) vytvořte novou aplikaci funkcí, ve které vytvoříte proxy. Adresa URL této nové aplikace bude sloužit jako front-end našeho rozhraní API a dříve upravená aplikace funkcí bude sloužit jako back-end.

1. Na portálu přejděte na novou front-endovou aplikaci funkcí.
1. Vyberte **Funkce platformy** a zvolte **Nastavení aplikace**.
1. Přejděte dolů na **Nastavení aplikace**, kde jsou uložené dvojice klíč/hodnota, a vytvořte nové nastavení s klíčem HELLO_HOST. Nastavte jeho hodnotu na hostitele vaší back-endové aplikace funkcí, například `<YourBackendApp>.azurewebsites.net`. Jedná se o část adresy URL, kterou jste dříve zkopírovali při testování funkce HTTP. Na toto nastavení později odkážete v konfiguraci.

    > [!NOTE] 
    > Ke konfiguraci hostitele se doporučuje použít nastavení aplikace. Zabráníte tím, aby byla u proxy pevně zakódovaná závislost na prostředí. Když použijete nastavení aplikace, můžete konfiguraci proxy přesouvat mezi prostředími, přičemž se použijí nastavení aplikace specifická pro dané prostředí.

1. Klikněte na **Uložit**.

### <a name="creating-a-proxy-on-the-frontend"></a>Vytvoření proxy na front-endu

1. Vraťte se ke své front-endové aplikaci funkcí na portálu.
1. V levém navigačním panelu klikněte na znaménko plus + vedle Proxy.
    ![Vytvoření proxy](./media/functions-create-serverless-api/creating-proxy.png)
1. Použijte nastavení proxy uvedená v tabulce. 

    | Pole | Ukázková hodnota | Popis |
    |---|---|---|
    | Název | HelloProxy | Popisný název sloužící jen ke správě |
    | Šablona trasy | /api/hello | Určuje, jaká trasa se používá k vyvolání tohoto proxy. |
    | Adresa URL back-endu | https://%HELLO_HOST%/api/hello | Určuje koncový bod, na který má být žádost přes proxy směrována. |
    
1. Všimněte si, že proxy neposkytují předponu základní cesty `/api`; musí být součástí šablony trasy.
1. Syntaxe `%HELLO_HOST%` bude odkazovat na dříve vytvořené nastavení aplikace. Přeložená adresa URL bude odkazovat na vaši původní funkci.
1. Klikněte na možnost **Vytvořit**.
1. Nový proxy vyzkoušíte tak, že zkopírujete adresu URL proxy a otestujete ji v prohlížeči nebo oblíbeném klientovi HTTP.
    1. Pro anonymní funkci použijte tento kód:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/hello?name="Proxies"`
    1. Pro funkci s autorizací použijte tento kód:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/hello?code=YOURCODE&name="Proxies"`

## <a name="create-a-mock-api"></a>Vytvoření napodobeniny rozhraní API

V dalším kroku použijete proxy k vytvoření napodobeniny rozhraní API pro své řešení. Umožníte tím další vývoj klienta bez potřeby plně implementovaného back-endu. Později při vývoji můžete vytvořit novou aplikaci funkcí, která podporuje tuto logiku, a přesměrovat na ni váš proxy.

Při vytváření této napodobeniny rozhraní API vytvoříme nový proxy, tentokrát pomocí [Editoru služby App Service](https://github.com/projectkudu/kudu/wiki/App-Service-Editor). Začněte tím, že na portálu přejdete na aplikaci funkcí. Vyberte **Funkce platformy** a v oblasti **Vývojové nástroje** najděte **Editor služby App Service**. Kliknutím na tuto možnost se v nové záložce otevře Editor služby App Service.

Vyberte `proxies.json` v levém navigačním panelu. Jedná se o soubor, ve kterém je uložená konfigurace všech vašich proxy. Pokud používáte jednu z těchto [metod nasazení služby Functions](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment), budete tento soubor udržovat ve správě zdrojového kódu. Další informace o tomto souboru najdete v článku o [pokročilé konfiguraci proxy](https://docs.microsoft.com/azure/azure-functions/functions-proxies#advanced-configuration).

Pokud jste zatím dodržovali náš postup, měl by soubor proxies.json vypadat takto:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/hello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

V dalším kroku přidáte napodobeninu rozhraní API. Nahraďte soubor proxies.json následujícím kódem:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/hello"
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

Tím přidáte nový proxy GetUserByName bez vlastnosti backendUri. Místo volání jiného prostředku upravuje výchozí odpověď od proxy pomocí přepisu odpovědi. Přepisy žádostí a odpovědí lze použít také ve spojení s adresou URL back-endu. To je zvlášť užitečné, když přes proxy provádíte přesměrování na zastaralý systém, kde je potřeba upravit hlavičky, parametry dotazu atd. Další informace o přepisech žádostí a odpovědí najdete v článku o [úpravách žádostí a odpovědí v proxy](https://docs.microsoft.com/azure/azure-functions/functions-proxies#a-namemodify-requests-responsesamodifying-requests-and-responses).

Otestujte napodobeninu rozhraní API voláním koncového bodu `<YourProxyApp>.azurewebsites.net/api/users/{username}` pomocí prohlížeče nebo oblíbeného klienta REST. Nezapomeňte nahradit _{username}_ řetězcovou hodnotou představující uživatelské jméno.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak sestavit a přizpůsobit rozhraní API ve službě Azure Functions. Také jste zjistili, jak spojit více rozhraní API (včetně napodobenin) do jednoho sjednoceného rozsahu rozhraní API. Pomocí těchto technik můžete vytvořit jakkoli složité rozhraní API, a zároveň používat bezserverový výpočetní model, který poskytuje služba Azure Functions.

Při dalším vývoji rozhraní API vám mohou přijít vhod následující odkazy:

- [Vazby HTTP ve službě Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)
- [Práce s Proxy služby Azure Functions]
- [Dokumentace rozhraní API služby Azure Functions (verze Preview)](https://docs.microsoft.com/azure/azure-functions/functions-api-definition-getting-started)


[Create your first function]: https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function
[Práce s Proxy služby Azure Functions]: https://docs.microsoft.com/azure/azure-functions/functions-proxies
