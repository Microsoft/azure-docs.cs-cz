---
title: Konfigurace nastavení aplikací funkcí v Azure
description: Přečtěte si, jak nakonfigurovat nastavení aplikací azure funkce.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 08/14/2019
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 662a04dbcc39f3fa95b0098eb8fe556b18b3495b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276943"
---
# <a name="manage-your-function-app"></a>Správa aplikace funkcí 

V Azure Functions aplikace funkce poskytuje kontext spuštění pro jednotlivé funkce. Chování aplikace funkce platí pro všechny funkce hostované danou aplikací funkce. Všechny funkce v aplikaci funkce musí být ve stejném [jazyce](supported-languages.md). 

Jednotlivé funkce v aplikaci funkce jsou nasazeny společně a jsou škálovány společně. Všechny funkce ve stejné funkci aplikace sdílet prostředky, na instanci, jako funkce aplikace škáluje. 

Připojovací řetězce, proměnné prostředí a další nastavení aplikace jsou definovány samostatně pro každou aplikaci funkce. Všechna data, která musí být sdílena mezi aplikacemi funkcí, by měla být uložena externě v trvalém úložišti.

Tento článek popisuje, jak nakonfigurovat a spravovat aplikace funkcí. 

> [!TIP]  
> Mnoho možností konfigurace lze také spravovat pomocí [příkazového příkazu k příkazu Azure]. 

## <a name="get-started-in-the-azure-portal"></a>Začínáme na webu Azure Portal

Začněte tím, že přejdete na [portál Azure] a přihlaste se ke svému účtu Azure. Na panelu hledání v horní části portálu zadejte název vaší aplikace Function App a vyberte ji ze seznamu. Po výběru aplikace funkce se zobrazí následující stránka:

![Přehled aplikací pro funkce na webu Azure Portal](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

Na stránce s přehledem můžete přejít na vše, co potřebujete ke správě aplikace, zejména na **[stránce Nastavení aplikace](#settings)** a **[Platformy](#platform-features)**.

## <a name="application-settings"></a><a name="settings"></a>Nastavení aplikace

Karta **Nastavení aplikace** udržuje nastavení používaná aplikací funkcí. Tato nastavení jsou uložena šifrovaná a musíte vybrat **Zobrazit hodnoty,** abyste viděli hodnoty na portálu. K nastavení aplikací můžete také přistupovat pomocí příkazového příkazu k webu Azure.

### <a name="portal"></a>Portál

Chcete-li přidat nastavení na portálu, vyberte **nastavení Nové aplikace** a přidejte nový pár klíč-hodnota.

![Nastavení aplikací funkce na webu Azure Portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

### <a name="azure-cli"></a>Azure CLI

Příkaz [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list) vrátí existující nastavení aplikace, jako v následujícím příkladu:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

Příkaz [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) přidá nebo aktualizuje nastavení aplikace. Následující příklad vytvoří nastavení s `CUSTOM_FUNCTION_APP_SETTING` názvem klíče `12345`a hodnotou :


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

### <a name="use-application-settings"></a>Použití nastavení aplikace

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Při vývoji aplikace funkce místně, je nutné udržovat místní kopie těchto hodnot v souboru projektu local.settings.json. Další informace naleznete v [tématu Soubor místních nastavení](functions-run-local.md#local-settings-file).

## <a name="platform-features"></a>Funkce platformy

![Karta Funkce platformy aplikace.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Aplikace funkcí se spouštějí a spravuje platforma Azure App Service. Vaše funkční aplikace tak mají přístup k většině funkcí základní webhostingové platformy Azure. Karta **Funkce platformy** je místo, kde máte přístup k mnoha funkcím platformy App Service, které můžete použít ve svých funkčních aplikacích. 

> [!NOTE]
> Ne všechny funkce služby App Service jsou k dispozici, když se aplikace funkce spustí v plánu hostování spotřeba.

Zbývající část tohoto článku se zaměřuje na následující funkce služby App Service na webu Azure Portal, které jsou užitečné pro funkce:

+ [Editor služby App Service](#editor)
+ [Konzola](#console)
+ [Pokročilé nástroje (Kudu)](#kudu)
+ [Možnosti nasazení](#deployment)
+ [CORS](#cors)
+ [Ověřování](#auth)

Další informace o tom, jak pracovat s nastavením služby App Service, [najdete v tématu Konfigurace nastavení služby Azure App Service](../app-service/configure-common.md).

### <a name="app-service-editor"></a><a name="editor"></a>Editor služeb aplikace

![Editor služby App Service](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

Editor služby App Service je pokročilý editor na portálu, který můžete použít k úpravě konfiguračních souborů JSON a souborů kódu. Výběrem této možnosti se spustí samostatná karta prohlížeče se základním editorem. To vám umožní integrovat se s úložištěm Git, spustit a ladit kód a upravit nastavení aplikace funkcí. Tento editor poskytuje rozšířené vývojové prostředí pro vaše funkce ve srovnání s vestavěným editorem funkcí.  

Doporučujeme zvážit vývoj funkcí v místním počítači. Když se vyvíjíte místně a publikujete do Azure, vaše soubory projektu jsou jen pro čtení na portálu. Další informace najdete v [tématu Kód a testování funkcí Azure místně](functions-develop-local.md).

### <a name="console"></a><a name="console"></a>Konzola

![Konzola aplikace Function](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

Konzola in-portal je ideální vývojářský nástroj, když dáváte přednost interakci s aplikací funkcí z příkazového řádku. Běžné příkazy zahrnují vytváření a navigaci v adresáři a souborech a také provádění dávkových souborů a skriptů. 

Při vývoji místně doporučujeme používat [nástroje Core Functions Azure](functions-run-local.md) a Azure [CLI].

### <a name="advanced-tools-kudu"></a><a name="kudu"></a>Pokročilé nástroje (Kudu)

![Konfigurace Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

Pokročilé nástroje pro službu App Service (označované také jako Kudu) poskytují přístup k pokročilým funkcím správy vaší aplikace pro funkce. Z Kudu spravujete systémové informace, nastavení aplikací, proměnné prostředí, rozšíření webu, hlavičky HTTP a proměnné serveru. **Kudu** můžete spustit také procházením koncového bodu SCM pro vaši funkční aplikaci, jako je`https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment-center"></a><a name="deployment"></a>Deployment Center

Při použití řešení správy zdrojového kódu k vývoji a údržbě kódu funkcí, Centrum nasazení umožňuje vytvářet a nasazovat ze správy zdrojového kódu. Váš projekt je sestavený a nasazený do Azure, když provádíte aktualizace. Další informace najdete [v tématu Nasazení technologií v Azure Functions](functions-deployment-technologies.md).

### <a name="cross-origin-resource-sharing"></a><a name="cors"></a>Sdílení prostředků různého původu

Chcete-li zabránit spuštění škodlivého kódu v klientovi, moderní prohlížeče blokují požadavky z webových aplikací na prostředky spuštěné v samostatné doméně. [Sdílení prostředků mezi zdroji (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) umožňuje `Access-Control-Allow-Origin` záhlaví deklarovat, které původy mohou volat koncové body v aplikaci funkce.

#### <a name="portal"></a>Portál

Když nakonfigurujete seznam **Povolený** `Access-Control-Allow-Origin` původ pro vaši funkční aplikaci, záhlaví se automaticky přidá ke všem odpovědím z koncových bodů HTTP ve vaší aplikaci funkce. 

![Konfigurace seznamu CORS aplikace funkcí](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

Při použití zástupné znakové )`*`znaku jsou všechny ostatní domény ignorovány. 

Pomocí [`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) příkazu přidejte doménu do seznamu povolených počátků. Následující příklad přidá contoso.com doménu:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

Pomocí [`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) příkazu uveďte aktuální povolený původ.

### <a name="authentication"></a><a name="auth"></a>Ověřování

![Konfigurace ověřování pro aplikaci funkcí](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

Pokud funkce používají aktivační událost PROTOKOLU HTTP, můžete vyžadovat, aby byla volání nejprve ověřena. Služba App Service podporuje ověřování azure active directory a přihlašování se sociálními poskytovateli, jako je Facebook, Microsoft a Twitter. Podrobnosti o konfiguraci konkrétních poskytovatelů ověřování najdete v [tématu Přehled ověřování služby Azure App Service](../app-service/overview-authentication-authorization.md). 


## <a name="next-steps"></a>Další kroky

+ [Konfigurace nastavení služby Azure App Service](../app-service/configure-common.md)
+ [Průběžné nasazování se službou Azure Functions](functions-continuous-deployment.md)

[Azure CLI]: /cli/azure/
[Portál Azure]: https://portal.azure.com
