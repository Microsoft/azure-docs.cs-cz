---
title: Konfigurace nastavení aplikace Azure Function App | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat nastavení aplikace Azure function app.
services: ''
documentationcenter: .net
author: ggailey777
manager: jeconnoc
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 92ca09040836dfc55a9d709b12a0ee01192d6bac
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65957388"
---
# <a name="how-to-manage-a-function-app-in-the-azure-portal"></a>Správa aplikace function app na webu Azure Portal 

Aplikace function app ve službě Azure Functions poskytuje kontext spuštění pro jednotlivé funkce. Chování funkce aplikací platí pro všechny funkce, které jsou hostované aplikace pro danou funkci. Toto téma popisuje, jak konfigurovat a spravovat vaše aplikace function App na webu Azure Portal.

Pokud chcete začít, přejděte [webu Azure portal](https://portal.azure.com) a přihlaste se ke svému účtu Azure. Na panelu hledání v horní části portálu zadejte název vaší aplikace Function App a vyberte ji ze seznamu. Po výběru aplikace function app, naleznete na následující stránce:

![Přehled funkcí aplikace na webu Azure Portal](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

Můžete přejít na všechno, co potřebujete ke správě aplikace function app na stránce Přehled zejména **[nastavení aplikace](#settings)** a **[funkce:](#platform-features)** .

## <a name="settings"></a>Nastavení aplikace

**Nastavení aplikace** kartu uchovává nastavení, které používají vaši aplikaci function app.

![Nastavení aplikace Function app na webu Azure Portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

Tato nastavení jsou uloženy v zašifrované podobě a je nutné vybrat **zobrazí hodnoty** zobrazit hodnoty na portálu.

Chcete-li přidat nastavení **nové nastavení aplikace** a přidejte nový pár klíč hodnota.

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Když vyvíjíte aplikaci function app místně, tyto hodnoty se zachovají v souboru local.settings.json projektu.

## <a name="platform-features"></a>Funkce platformy

![Funkce kartě funkce platformy aplikace.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Aplikace Function App běží v a spravuje, platformu Azure App Service. Vaše aplikace function App v důsledku toho mají přístup k většině funkcí Azure core webhosting platformy. **Funkce platformy** je karta, kde přístup k mnoha funkcím platformy App Service, můžete použít ve vaší aplikace function App. 

> [!NOTE]
> Ne všechny funkce App Service jsou k dispozici při spuštění aplikace function app na spotřebu plán hostování.

Zbývající část tohoto tématu, zaměřuje se na následující funkce služby App Service na webu Azure Portal, které jsou užitečné pro funkce:

+ [App Service editor](#editor)
+ [Console](#console)
+ [Rozšířené nástroje (Kudu)](#kudu)
+ [Možnosti nasazení](#deployment)
+ [CORS](#cors)
+ [Ověřování](#auth)
+ [Definice rozhraní API](#swagger)

Další informace o tom, jak pracovat s nastavením služby App Service najdete v tématu [konfigurace nastavení služby Azure App Service](../app-service/configure-common.md).

### <a name="editor"></a>App Service Editor

| | |
|-|-|
| ![App Service editoru aplikace Function app.](./media/functions-how-to-use-azure-function-app-settings/function-app-appsvc-editor.png)  | Editor služby App Service je pokročilé editor na portálu, který vám umožní upravit konfigurační soubory JSON a nabídne soubory kódu. Pokud vyberete tuto možnost, spustí se na samostatné kartě prohlížeče v základním editoru. To umožňuje integraci s úložištěm Git, spouštět a ladit kód a upravit nastavení aplikace function app. Tento editor poskytuje vylepšené vývojářské prostředí pro funkce ve srovnání s výchozím okně aplikace funkce.    |

![Editor služby App Service](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="console"></a>Konzoly

| | |
|-|-|
| ![Funkce konzoly aplikace na webu Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-console.png) | Konzole na portálu je ideální pro vývojáře nástroj, když budete chtít pracovat s vaší aplikace function app z příkazového řádku. Běžné příkazy zahrnout adresáře a vytváření souborů a navigace, jakož i provádí dávkové soubory a skripty. |

![Aplikace konzoly – funkce](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

### <a name="kudu"></a>Rozšířené nástroje (Kudu)

| | |
|-|-|
| ![Aplikace Function app Kudu na portálu Azure portal](./media/functions-how-to-use-azure-function-app-settings/function-app-advanced-tools.png) | Pokročilé nástroje pro službu App Service (Kudu) poskytují přístup k pokročilým funkcím pro správu vaší aplikace function App. Z modulu Kudu můžete spravovat informace o systému, nastavení aplikace, proměnné prostředí, rozšíření webu, hlavičky protokolu HTTP a proměnných serveru. Můžete také spustit **Kudu** tak, že přejdete na koncový bod správce řízení služeb pro aplikace function app, jako je třeba `https://<myfunctionapp>.scm.azurewebsites.net/` |

![Konfigurace modulu Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)


### <a name="a-namedeploymentdeployment-options"></a><a name="deployment">Možnosti nasazení

| | |
|-|-|
| ![Možnosti nasazení aplikace funkcí na webu Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-deployment-source.png) | Funkce vám umožní vyvíjet kódu funkce v místním počítači. Pak můžete nahrát projektu lokální funkce aplikace do Azure. Kromě tradičních nahrání přes protokol FTP funkce vám umožní nasadit aplikaci function app pomocí oblíbených průběžnou integraci řešení, jako jsou GitHub, Azure DevOps, Dropbox, Bitbucket nebo jiné. Další informace najdete v tématu [průběžné nasazování pro službu Azure Functions](functions-continuous-deployment.md). Nahrát ručně pomocí FTP a místního Gitu, je také nutné [nakonfigurujete své přihlašovací údaje nasazení](functions-continuous-deployment.md#credentials). |


### <a name="cors"></a>CORS

| | |
|-|-|
| ![Aplikace Function app CORS na portálu Azure portal](./media/functions-how-to-use-azure-function-app-settings/function-app-cors.png) | Pokud chcete zabránit spuštění škodlivého kódu ve vašich službách, blokuje služby App Service volání vaší aplikace function App z externích zdrojů. Služba Functions podporuje prostředků mezi zdroji (CORS), aby vám umožní definovat "povolených" povolené zdroje, ze kterých může přijmout funkce vzdálené žádosti pro sdílení obsahu.  |

![Konfigurace aplikace Function App CORS](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

### <a name="auth"></a>Ověřování

| | |
|-|-|
| ![Funkce ověřování aplikace na webu Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-authentication.png) | Použijete-li funkce triggeru HTTP, můžete vyžadovat volání nejdřív ověřit. App Service podporuje ověřování Azure Active Directory a přihlaste se pomocí poskytovatelé služeb sociálních sítí, jako je Facebook, Microsoft a Twitter. Podrobnosti o konfiguraci zprostředkovatele konkrétní ověřování najdete v tématu [Přehled ověřování služby Azure App Service](../app-service/overview-authentication-authorization.md). |

![Konfigurace ověřování aplikace function App](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)


### <a name="swagger"></a>Definice rozhraní API

| | |
|-|-|
| ![Aplikace – funkce rozhraní API swaggeru definici na webu Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-api-definition.png) | Služba Functions podporuje Swagger a umožňuje klientům snadněji využívat funkce aktivované protokolem HTTP. Další informace o vytvoření definice rozhraní API ve Swaggeru [hostovat rozhraní RESTful API s CORS v Azure App Service](../app-service/app-service-web-tutorial-rest-api.md). Proxy služby Functions lze také definovat jednu rovinu rozhraní API pro více funkcí. Další informace najdete v tématu [práce s proxy služby Azure Functions](functions-proxies.md). |

![Konfigurovat rozhraní API aplikace Function App](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)



## <a name="next-steps"></a>Další postup

+ [Konfigurace nastavení služby Azure App Service](../app-service/configure-common.md)
+ [Průběžné nasazování se službou Azure Functions](functions-continuous-deployment.md)



