---
title: Konfigurace nastavení služby Azure Function App | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat nastavení Azure Function App.
author: ggailey777
manager: gwallace
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 27e56c3bcaa08fee25c362cdfed64c61b92873cb
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982658"
---
# <a name="how-to-manage-a-function-app"></a>Správa aplikace Function App

V Azure Functions aplikace Function App poskytuje kontext spuštění pro vaše jednotlivé funkce. Chování aplikace Function App se vztahuje na všechny funkce hostované danou funkcí Function App. Toto téma popisuje, jak nakonfigurovat a spravovat aplikace Function App v Azure Portal.

Začněte tím, že přejdete na [Azure Portal](https://portal.azure.com) a přihlásíte se k účtu Azure. Na panelu hledání v horní části portálu zadejte název vaší aplikace Function App a vyberte ji ze seznamu. Po výběru aplikace Function App se zobrazí následující stránka:

![Přehled aplikace Function App v Azure Portal](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

Na stránce Přehled můžete přejít na všechno, co potřebujete ke správě aplikace Function App, zejména **[nastavení aplikace](#settings)** a **[funkce platformy](#platform-features)** .

## <a name="settings"></a>Nastavení aplikace

Karta **nastavení aplikace** uchovává nastavení, která používá aplikace Function App.

![Nastavení aplikace Function App v Azure Portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

Tato nastavení jsou šifrovaná a je nutné vybrat možnost **Zobrazit hodnoty** a zobrazit hodnoty na portálu.

Chcete-li přidat nastavení, vyberte možnost **nové nastavení aplikace** a přidejte novou dvojici klíč-hodnota.

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Když vyvíjíte aplikaci funkcí lokálně, tyto hodnoty se udržují v souboru projektu Local. Settings. JSON.

## <a name="platform-features"></a>Funkce platformy

![Karta funkce Function App Platform](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Aplikace Function App běží na a jsou udržované Azure App Service platformou. Aplikace Function App mají proto přístup k většině funkcí základní webové hostující platformy Azure. Karta **funkce platformy** je místo, kde máte přístup k mnoha funkcím App Service platformy, kterou můžete použít ve svých aplikacích Function App. 

> [!NOTE]
> Ne všechny funkce App Service jsou dostupné, když aplikace Function App běží na plánu hostování spotřeby.

Zbývající část tohoto tématu se zaměřuje na následující funkce App Service v Azure Portal, které jsou užitečné pro funkce:

+ [Editor App Service](#editor)
+ [Console](#console)
+ [Rozšířené nástroje (Kudu)](#kudu)
+ [Možnosti nasazení](#deployment)
+ [CORS](#cors)
+ [Autentizace](#auth)
+ [Definice rozhraní API](#swagger)

Další informace o tom, jak pracovat s nastaveními App Service, najdete v tématu [Konfigurace nastavení Azure App Service](../app-service/configure-common.md).

### <a name="editor"></a>App Service Editor

| | |
|-|-|
| ![Editor aplikace Function App App Service.](./media/functions-how-to-use-azure-function-app-settings/function-app-appsvc-editor.png)  | Editor App Service je pokročilý editor v portálu, který můžete použít k úpravě konfiguračních souborů JSON a souborů kódu podobně. Výběrem této možnosti se spustí samostatná karta prohlížeče se základním editorem. To umožňuje integraci s úložištěm Git, spuštěním a laděním kódu a úpravou nastavení aplikace Function App. Tento editor nabízí vylepšené vývojové prostředí pro vaše funkce v porovnání s výchozím oknem aplikace Function App.    |

![Editor App Service](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="console"></a>Stromu

| | |
|-|-|
| ![Konzola aplikace Function App v Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-console.png) | Konzola na portálu je ideální vývojářský nástroj, když dáváte přednost interakci s aplikací Function App z příkazového řádku. Mezi běžné příkazy patří vytváření a navigace adresářů a souborů a také provádění dávkových souborů a skriptů. |

![Konzola aplikace Function App](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

### <a name="kudu"></a>Rozšířené nástroje (Kudu)

| | |
|-|-|
| ![Aplikace Function App Kudu v Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-advanced-tools.png) | Rozšířené nástroje pro App Service (označované také jako Kudu) poskytují přístup k pokročilým funkcím správy aplikace Function App. Z Kudu můžete spravovat systémové informace, nastavení aplikace, proměnné prostředí, rozšíření webu, hlavičky protokolu HTTP a proměnné serveru. **Kudu** můžete také spustit tak, že přejdete do koncového bodu SCM pro vaši aplikaci Function App, jako je například`https://<myfunctionapp>.scm.azurewebsites.net/` |

![Konfigurace Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)


### <a name="a-namedeploymentdeployment-options"></a><a name="deployment">Možnosti nasazení

| | |
|-|-|
| ![Možnosti nasazení aplikace Function App v Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-deployment-source.png) | Funkce umožňují vyvíjet kód funkce na místním počítači. Potom můžete do Azure nahrát svůj projekt místní aplikace Function App. Kromě tradičního nahrání serveru FTP vám funkce umožní nasadit aplikaci Function App s oblíbenými řešeními pro kontinuální integraci, jako je GitHub, Azure DevOps, Dropbox, Bitbucket a další. Další informace najdete v tématu [průběžné nasazování pro Azure Functions](functions-continuous-deployment.md). Pokud chcete nahrávat ručně pomocí FTP nebo místního Gitu, musíte taky [nakonfigurovat přihlašovací údaje pro nasazení](functions-continuous-deployment.md#credentials). |


### <a name="cors"></a>CORS

| | |
|-|-|
| ![Funkce CORS aplikace Function App v Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-cors.png) | Aby se zabránilo spouštění škodlivého kódu ve vašich službách, App Service blokuje volání aplikací Function App z externích zdrojů. Funkce podporuje sdílení prostředků mezi zdroji (CORS) a umožňuje definovat "seznam povolených" povolených zdrojů, ze kterých můžou vaše funkce přijímat vzdálené požadavky.  |

![Konfigurace CORS Function App](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

### <a name="auth"></a>Přihlašovací

| | |
|-|-|
| ![Ověřování aplikace Function App v Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-authentication.png) | Pokud funkce používají aktivační událost HTTP, můžete vyžadovat první ověření volání. App Service podporuje ověřování Azure Active Directory a přihlašování pomocí poskytovatelů sociálních sítí, jako je Facebook, Microsoft a Twitter. Podrobnosti o konfiguraci konkrétních zprostředkovatelů ověřování najdete v tématu [Přehled ověřování Azure App Service](../app-service/overview-authentication-authorization.md). |

![Konfigurace ověřování pro aplikaci Function App](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)


### <a name="swagger"></a>Definice rozhraní API

| | |
|-|-|
| ![Rozhraní API aplikace Function App – definice Swagger v Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-api-definition.png) | Funkce podporují Swagger, aby mohli klienti snadněji využívat funkce aktivované protokolem HTTP. Další informace o vytváření definic rozhraní API pomocí Swagger najdete na webu [hostování rozhraní RESTful API s CORS v Azure App Service](../app-service/app-service-web-tutorial-rest-api.md). K definování jediného povrchu rozhraní API pro více funkcí můžete použít také proxy služby Functions. Další informace najdete v tématu [práce s proxy služby Azure Functions](functions-proxies.md). |

![Konfigurace rozhraní API Function App](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)



## <a name="next-steps"></a>Další postup

+ [Konfigurace nastavení Azure App Service](../app-service/configure-common.md)
+ [Průběžné nasazování se službou Azure Functions](functions-continuous-deployment.md)



