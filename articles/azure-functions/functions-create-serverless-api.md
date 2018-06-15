---
title: Vytvoření bez serveru API pomocí Azure Functions | Microsoft Docs
description: Postup vytvoření bez serveru API pomocí Azure Functions
services: functions
author: mattchenderson
manager: cfowler
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: tutorial
ms.date: 05/04/2017
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: 7c3933210c01c81077b594abb8c3183d6e3c58a0
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2017
ms.locfileid: "24811596"
---
# <a name="create-a-serverless-api-using-azure-functions"></a>Vytvoření bez serveru API pomocí Azure Functions

V tomto kurzu se dozvíte, jak Azure Functions umožňuje vytvářet vysoce škálovatelné rozhraní API. Azure Functions se dodává s kolekce integrované HTTP triggerů a vazeb, které usnadňují vytváření koncový bod v různých jazycích, včetně Node.JS, C# a další. V tomto kurzu budete přizpůsobovat aktivační procedury HTTP pro zpracování určitých akcí v návrhu rozhraní API. Také připravíte pro rostoucí vaše rozhraní API pomocí integrace s funkcí proxy Azure a nastavením imitované rozhraní API. Všechny tyto dosahuje nad výpočetním prostředí bez serveru funkce, takže nemusíte si dělat starosti o škálování prostředky – stačí se můžete soustředit na logice rozhraní API.

## <a name="prerequisites"></a>Požadavky 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

Výsledný funkce se použije pro zbytek tohoto kurzu.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Otevřete portál Azure. Chcete-li to provést, přihlaste se k [https://portal.azure.com](https://portal.azure.com) s vaším účtem Azure.

## <a name="customize-your-http-function"></a>Přizpůsobení funkce protokolu HTTP

Ve výchozím nastavení je funkce aktivované protokolem HTTP nakonfigurovaná tak, aby přijímal libovolné metody HTTP. Je také výchozí adresy URL ve formátu `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>`. Pokud jste postupovali podle rychlé spuštění, pak `<funcname>` pravděpodobně vypadá podobně jako "HttpTriggerJS1". V této části upravíte funkce odpovídal pouze na požadavky GET proti `/api/hello` směrovat místo. 

1. Přejděte do funkce na portálu Azure. Vyberte **integrací** v levém navigačním panelu.

    ![Přizpůsobení funkce protokolu HTTP](./media/functions-create-serverless-api/customizing-http.png)

1. Použijte nastavení aktivační událost HTTP jako zadaný v tabulce.

    | Pole | Ukázková hodnota | Popis |
    |---|---|---|
    | Povolených metod HTTP | Vybrané metody | Určuje, jaké metody HTTP lze volat tuto funkci |
    | Vybrané metody HTTP | GET | Umožňuje pouze vybrané HTTP metod, které bude použito ke volat tuto funkci |
    | Šablona trasy | řetězec Ahoj | Určuje, jaké trasy je použito k volání této funkce |
    | Úroveň oprávnění | Anonymní | Volitelné: Zpřístupní funkce bez klíč rozhraní API |

    > [!NOTE] 
    > Všimněte si, že nejsou zahrnuty `/api` základní cesta předpony v šabloně trasy, jak to se provádí globální nastavení.

1. Klikněte na **Uložit**.

Další informace o přizpůsobení funkce protokolu HTTP v [vazby HTTP funkce Azure a webhooku](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#customizing-the-http-endpoint).

### <a name="test-your-api"></a>Test rozhraní API

V dalším kroku funkci nevidíte práce s novou plochy rozhraní API otestujte.
1. Přejděte zpět na stránku vývoj kliknutím na název funkce je v levém navigačním panelu.
1. Klikněte na tlačítko **získat adresu URL funkce** a zkopírujte adresu URL. Měli byste vidět, že používá `/api/hello` nyní trasy.
1. Zkopírujte adresu URL do novou kartu prohlížeče nebo vaše upřednostňované klienta REST. V prohlížečích se ve výchozím nastavení používá GET.
1. Přidání parametrů do řetězce dotazu v URL například`/api/hello/?name=John`
1. Stiskněte tlačítko zadejte potvrďte, že funguje. Měli byste vidět odpovědi "*Hello Jan*"
1. Můžete také zkusit volání koncový bod s jinou metodu HTTP, potvrďte, že funkce není spuštěn. V takovém případě budete muset použít klienta REST, například cURL, Postman nebo Fiddler.

## <a name="proxies-overview"></a>Přehled proxy

V další části bude surface vaše rozhraní API prostřednictvím proxy serveru. Azure proxy funkce umožňuje předat požadavky na jiné prostředky. Můžete definovat koncový bod HTTP stejně jako s triggeru protokolu HTTP, ale místo psaní kódu pro spuštění, kdy se nazývá tohoto koncového bodu, zadejte adresu URL vzdálené implementaci. Umožňuje vytvořit více zdrojů rozhraní API do jednoho plochy rozhraní API, který je snadno klientům využívat. To je zvlášť užitečné, pokud chcete vytvořit rozhraní API jako mikroslužeb.

Proxy server může ukazovat na jakémukoli prostředku, HTTP, jako například:
- Funkce Azure 
- API apps v [služby Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-overview)
- Kontejnery docker v [služby App Service v systému Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)
- Další hostovaným rozhraním API

Další informace o proxy najdete v tématu [práce s Azure funkce proxy].

## <a name="create-your-first-proxy"></a>Vytvoření vašeho prvního proxy

V této části vytvoříte nový proxy, který slouží jako front-end pro celkový rozhraní API. 

### <a name="setting-up-the-frontend-environment"></a>Nastavení prostředí front-endu

Opakováním kroků [vytvořit aplikaci function app](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function#create-a-function-app) k vytvoření nové aplikace funkce, ve kterém vytvoříte proxy. Adresa URL nové aplikace bude sloužit jako front-endu pro naše API a funkce aplikace, kterou jste dříve upravovali bude sloužit jako back-end.

1. Přejděte do nové aplikace funkce front-endu na portálu.
1. Vyberte **funkce** a zvolte **nastavení aplikace**.
1. Přejděte dolů k položce **nastavení aplikace** kde páry klíč/hodnota jsou uloženy a vytvořit nové nastavení s klíčem "HELLO_HOST". Jeho hodnotu nastavte hostitele back-end funkce aplikace, jako například `<YourBackendApp>.azurewebsites.net`. Toto je část adresy URL, který jste zkopírovali dříve při testování funkce protokolu HTTP. Toto nastavení v konfiguraci později budete odkazovat.

    > [!NOTE] 
    > Nastavení aplikace – se doporučují pro konfigurace hostitele, aby se zabránilo závislost pevně prostředí pro proxy server. Pomocí nastavení aplikace znamená, že můžete přesunout konfiguraci proxy serveru mezi prostředími a nastavení prostředí aplikací se použijí.

1. Klikněte na **Uložit**.

### <a name="creating-a-proxy-on-the-frontend"></a>Vytváření proxy server na front-endu

1. Přejděte zpět do aplikace front-endu funkce na portálu.
1. V levém navigačním panelu, klikněte na symbol plus '+' vedle "Proxy".
    ![Vytváření proxy server](./media/functions-create-serverless-api/creating-proxy.png)
1. Použijte nastavení proxy serveru specifikovaný v tabulce. 

    | Pole | Ukázková hodnota | Popis |
    |---|---|---|
    | Name (Název) | HelloProxy | Popisný název, který se používá pouze pro správu |
    | Šablona trasy | / api/hello | Určuje, jaké trasy se použije k vyvolání tento proxy server |
    | Adresa URL back-end | https://%HELLO_HOST%/API/Hello | Určuje koncový bod, ke kterému by měla být žádost směrovány přes proxy server |
    
1. Všimněte si, že proxy neposkytuje `/api` základní cesta předponu a tento musí být součástí šablonu trasy.
1. `%HELLO_HOST%` Syntaxe bude odkazovat nastavení aplikace, který jste vytvořili dříve. Přeložit adresy URL bude odkazovat na původní funkce.
1. Klikněte na možnost **Vytvořit**.
1. Váš nový proxy server můžete vyzkoušet tak, že adresa URL proxy serveru a jeho otestováním v prohlížeči nebo pomocí vašeho oblíbeného klienta HTTP.
    1. Pro použití anonymní funkce:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/hello?name="Proxies"`
    1. Pro funkci s použitím autorizace:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/hello?code=YOURCODE&name="Proxies"`

## <a name="create-a-mock-api"></a>Vytvoření imitované rozhraní API

V dalším kroku použijete proxy serveru k vytvoření imitované rozhraní API pro vaše řešení. To umožňuje vývoje klienta pokroku, bez nutnosti back-end plně implementováno. Později v vývoj může vytvořit novou aplikaci funkce, která podporuje tuto logiku a přesměrování proxy k němu.

Pokud chcete vytvořit toto imitované rozhraní API, vytvoříme nový proxy, toto současně pomocí [Editor služby aplikace](https://github.com/projectkudu/kudu/wiki/App-Service-Editor). Chcete-li začít, přejděte na funkce aplikace v portálu. Vyberte **funkce** a v části **nástroje pro vývoj** najít **App Service Editor**. To kliknutím otevřete Editor služby aplikace na nové kartě.

Vyberte `proxies.json` v levém navigačním panelu. Toto je soubor, který ukládá konfiguraci pro všechny vaše servery proxy. Pokud použijete jednu z [funkce metody nasazení](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment), toto je soubor bude uchovávat ve správě zdrojového kódu. Další informace o tento soubor naleznete v tématu [proxy Upřesnit konfiguraci](https://docs.microsoft.com/azure/azure-functions/functions-proxies#advanced-configuration).

Pokud jste jste postupovali podle dosavadní, vaše proxies.json by měl vypadat asi takto:

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

Dále přidáte imitované rozhraní API. Nahraďte souboru proxies.json s následujícími službami:

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

Tento postup přidá nový proxy, "GetUserByName", bez backendUri vlastnosti. Namísto volání jiný prostředek, upraví odpověď výchozí proxy pomocí přepsání odpovědi. Přepsání požadavku a odpovědi mohou sloužit také ve spojení s adresou URL back-end. To je zvlášť užitečné, když připojení přes proxy server na starší systém, kde budete muset upravit hlavičky, dotaz parametry atd. Další informace o požadavku a odpovědi přepsání najdete v tématu [úprava požadavky a odpovědi v proxy](https://docs.microsoft.com/azure/azure-functions/functions-proxies#a-namemodify-requests-responsesamodifying-requests-and-responses).

Testovat imitované rozhraní API voláním `<YourProxyApp>.azurewebsites.net/api/users/{username}` koncového bodu pomocí prohlížeče nebo vaše oblíbené klienta REST. Nezapomeňte nahradit _{username}_ s řetězcovou hodnotu představující uživatelské jméno.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vytvářet a přizpůsobit rozhraní API na Azure Functions. Také jste zjistili, jak probouzet několik rozhraní API, včetně mocks, společně jako jednotné rozhraní API prostor. Tyto postupy můžete použít k sestavení se rozhraní API jakékoli složitosti při spuštění v modelu bez serveru výpočetní poskytované Azure Functions.

Následující odkazy mohou být užitečné, když budete vyvíjet další rozhraní API:

- [Azure funkce protokolu HTTP a webhooku vazby](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)
- [práce s Azure funkce proxy]
- [Dokumentace rozhraní API funkce Azure (preview)](https://docs.microsoft.com/azure/azure-functions/functions-api-definition-getting-started)


[Create your first function]: https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function
[práce s Azure funkce proxy]: https://docs.microsoft.com/azure/azure-functions/functions-proxies
