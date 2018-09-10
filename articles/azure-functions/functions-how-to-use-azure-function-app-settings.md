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
ms.openlocfilehash: 90b5009b3b260a0f4245459467ae248f800ad818
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44094531"
---
# <a name="how-to-manage-a-function-app-in-the-azure-portal"></a>Správa aplikace function app na webu Azure Portal 

Aplikace function app ve službě Azure Functions poskytuje kontext spuštění pro jednotlivé funkce. Chování funkce aplikací platí pro všechny funkce, které jsou hostované aplikace pro danou funkci. Toto téma popisuje, jak konfigurovat a spravovat vaše aplikace function App na webu Azure Portal.

Pokud chcete začít, přejděte [webu Azure portal](http://portal.azure.com) a přihlaste se ke svému účtu Azure. Na panelu hledání v horní části portálu zadejte název vaší aplikace Function App a vyberte ji ze seznamu. Po výběru aplikace function app, naleznete na následující stránce:

![Přehled funkcí aplikace na webu Azure Portal](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

## <a name="favorite"></a>Oblíbené funkce na portálu 

Může být někdy obtížné najít prostředky v [Azure Portal]. Aby bylo snazší najít aplikace function App, kterou jste vytvořili, přidáte aplikace Function App do oblíbených položek na portálu. 

1. Přihlaste se k webu [Azure Portal].

2. Kliknutím na šipku vlevo dole rozbalte všechny služby, do pole **Filtr** zadejte `Functions` a potom klikněte na hvězdičku vedle možnosti **Aplikace Function App**.  
 
    ![Vytvoření aplikace Function App na webu Azure Portal](./media/functions-how-to-use-azure-function-app-settings/functions-favorite-function-apps.png)

    Tím přidáte do nabídky na levé straně portálu ikonu Funkce.

3. Zavřete nabídku a potom přejděte úplně dolů, kde uvidíte ikonu Funkce. Kliknutím na tuto ikonu zobrazíte seznam všech aplikací Function App. Po kliknutí na určitou aplikaci Function App můžete pracovat s funkcemi této aplikace. 
 
    ![Aplikace Function App do oblíbených položek](./media/functions-how-to-use-azure-function-app-settings/functions-function-apps-hub.png)
 
[Azure Portal]: https://portal.azure.com/

## <a name="manage-app-service-settings"></a>Karta nastavení aplikace – funkce

![Přehled funkce aplikace na webu Azure Portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

**Nastavení** je karta, kde můžete aktualizovat verze modulu runtime Functions používá aplikace function App. Je také kde budete spravovat hostitele klíčů používá k omezení přístupu k protokolu HTTP k všech funkcí hostované aplikace function app.

Služba Functions podporuje hostování Consumption a plánů hostování služby App Service. Další informace najdete v tématu [zvolte správného plánu služby pro službu Azure Functions](functions-scale.md). Pro poskytují lépe předvídatelnou v plánu Consumption funkce vám umožní omezit využití platformy tak, že nastavíte denní kvótu využití v gigabajtech sekund. Jakmile se denní kvóta využití naplní, aplikace function app je zastavená. Aplikace function app zastavena v důsledku dosažení kvóty útraty může být ze stejné oblasti jako zřízení denní kvóta útraty znovu zapnout. Zobrazit [stránce s cenami za Azure Functions](http://azure.microsoft.com/pricing/details/functions/) podrobné informace o fakturaci.   

## <a name="platform-features-tab"></a>Kartě funkce platformy

![Funkce kartě funkce platformy aplikace.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Aplikace Function App běží v a spravuje, platformu Azure App Service. Vaše aplikace function App v důsledku toho mají přístup k většině funkcí Azure core webhosting platformy. **Funkce platformy** je karta, kde přístup k mnoha funkcím platformy App Service, můžete použít ve vaší aplikace function App. 

> [!NOTE]
> Ne všechny funkce App Service jsou k dispozici při spuštění aplikace function app na spotřebu plán hostování.

Zbývající část tohoto tématu, zaměřuje se na následující funkce služby App Service na webu Azure Portal, které jsou užitečné pro funkce:

+ [Editor služby App Service](#editor)
+ [Nastavení aplikace](#settings) 
+ [Console](#console)
+ [Rozšířené nástroje (Kudu)](#kudu)
+ [Možnosti nasazení](#deployment)
+ [CORS](#cors)
+ [Ověřování](#auth)
+ [Definice rozhraní API](#swagger)

Další informace o tom, jak pracovat s nastavením služby App Service najdete v tématu [konfigurace nastavení služby Azure App Service](../app-service/web-sites-configure.md).

### <a name="editor"></a>App Service Editor

| | |
|-|-|
| ![App Service editoru aplikace Function app.](./media/functions-how-to-use-azure-function-app-settings/function-app-appsvc-editor.png)  | Editor služby App Service je pokročilé editor na portálu, který vám umožní upravit konfigurační soubory JSON a nabídne soubory kódu. Pokud vyberete tuto možnost, spustí se na samostatné kartě prohlížeče v základním editoru. To umožňuje integraci s úložištěm Git, spouštět a ladit kód a upravit nastavení aplikace function app. Tento editor poskytuje vylepšené vývojářské prostředí pro funkce ve srovnání s výchozím okně aplikace funkce.    |

![Editor služby App Service](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="settings"></a>Nastavení aplikace

| | |
|-|-|
| ![Nastavení aplikace Function app.](./media/functions-how-to-use-azure-function-app-settings/function-app-application-settings.png) | App Service **nastavení aplikace** je okno, kde můžete nakonfigurovat a spravovat verze rozhraní framework, vzdálené ladění, nastavení aplikace a připojovacích řetězců. Pokud vaše aplikace function app se integrují s ostatními Azure a služby třetích stran, můžete upravit tato nastavení zde. Pokud chcete odstranit nastavení, posuňte doprava a vyberte **X** ikony na pravém konci řádku (není vidět na následujícím obrázku).

![Konfigurace nastavení aplikace](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-settings.png)

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
| ![Možnosti nasazení aplikace funkcí na webu Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-deployment-source.png) | Funkce vám umožní vyvíjet kódu funkce v místním počítači. Pak můžete nahrát projektu lokální funkce aplikace do Azure. Kromě tradičních nahrání přes protokol FTP funkce vám umožní nasadit aplikaci function app pomocí oblíbených průběžnou integraci řešení, jako jsou GitHub, VSTS, Dropbox, Bitbucket a dalších. Další informace najdete v tématu [průběžné nasazování pro službu Azure Functions](functions-continuous-deployment.md). Nahrát ručně pomocí FTP a místního Gitu, je také nutné [nakonfigurujete své přihlašovací údaje nasazení](functions-continuous-deployment.md#credentials). |


### <a name="cors"></a>CORS

| | |
|-|-|
| ![Aplikace Function app CORS na portálu Azure portal](./media/functions-how-to-use-azure-function-app-settings/function-app-cors.png) | Pokud chcete zabránit spuštění škodlivého kódu ve vašich službách, blokuje služby App Service volání vaší aplikace function App z externích zdrojů. Služba Functions podporuje prostředků mezi zdroji (CORS), aby vám umožní definovat "povolených" povolené zdroje, ze kterých může přijmout funkce vzdálené žádosti pro sdílení obsahu.  |

![Konfigurace aplikace Function App CORS](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

### <a name="auth"></a>Ověřování

| | |
|-|-|
| ![Funkce ověřování aplikace na webu Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-authentication.png) | Použijete-li funkce triggeru HTTP, můžete vyžadovat volání nejdřív ověřit. App Service podporuje ověřování Azure Active Directory a přihlaste se pomocí poskytovatelé služeb sociálních sítí, jako je Facebook, Microsoft a Twitter. Podrobnosti o konfiguraci zprostředkovatele konkrétní ověřování najdete v tématu [Přehled ověřování služby Azure App Service](../app-service/app-service-authentication-overview.md). |

![Konfigurace ověřování aplikace function App](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)


### <a name="swagger"></a>Definice rozhraní API

| | |
|-|-|
| ![Aplikace – funkce rozhraní API swaggeru definici na webu Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-api-definition.png) | Služba Functions podporuje Swagger a umožňuje klientům snadněji využívat funkce aktivované protokolem HTTP. Další informace o vytvoření definice rozhraní API ve Swaggeru [hostovat rozhraní RESTful API s CORS v Azure App Service](../app-service/app-service-web-tutorial-rest-api.md). Proxy služby Functions lze také definovat jednu rovinu rozhraní API pro více funkcí. Další informace najdete v tématu [práce s proxy služby Azure Functions](functions-proxies.md). |

![Konfigurovat rozhraní API aplikace Function App](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)



## <a name="next-steps"></a>Další postup

+ [Konfigurace nastavení služby Azure App Service](../app-service/web-sites-configure.md)
+ [Průběžné nasazování se službou Azure Functions](functions-continuous-deployment.md)



