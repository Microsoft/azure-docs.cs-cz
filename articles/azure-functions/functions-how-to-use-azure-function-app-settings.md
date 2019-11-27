---
title: Konfigurace nastavení aplikace Function App v Azure
description: Přečtěte si, jak nakonfigurovat nastavení Azure Function App.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 08/14/2019
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 662a04dbcc39f3fa95b0098eb8fe556b18b3495b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230579"
---
# <a name="manage-your-function-app"></a>Správa aplikace Function App 

V Azure Functions aplikace Function App poskytuje kontext spuštění pro vaše jednotlivé funkce. Chování aplikace Function App se vztahuje na všechny funkce hostované danou funkcí Function App. Všechny funkce aplikace Function App musí být ve stejném [jazyce](supported-languages.md). 

Jednotlivé funkce aplikace Function App se nasazují společně a navzájem se škálují. Všechny funkce ve stejné sdílené složce aplikace Function App mají na každou instanci stejnou velikost jako aplikace Function App. 

Připojovací řetězce, proměnné prostředí a další nastavení aplikace jsou definovány samostatně pro každou aplikaci Function App. Všechna data, která musí být sdílená mezi aplikacemi Function App, by se měla ukládat externě v trvalém úložišti.

Tento článek popisuje, jak nakonfigurovat a spravovat aplikace Function App. 

> [!TIP]  
> Řadu možností konfigurace je možné spravovat taky pomocí [Azure CLI]. 

## <a name="get-started-in-the-azure-portal"></a>Začínáme na webu Azure Portal

Začněte tím, že přejdete na [Azure Portal] a přihlásíte se k účtu Azure. Na panelu hledání v horní části portálu zadejte název vaší aplikace Function App a vyberte ji ze seznamu. Po výběru aplikace Function App se zobrazí následující stránka:

![Přehled aplikace Function App v Azure Portal](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

Na stránce Přehled můžete přejít na všechno, co potřebujete ke správě aplikace Function App, zejména **[nastavení aplikace](#settings)** a **[funkce platformy](#platform-features)** .

## <a name="settings"></a>Nastavení aplikace

Karta **nastavení aplikace** uchovává nastavení, která používá aplikace Function App. Tato nastavení jsou šifrovaná a je nutné vybrat možnost **Zobrazit hodnoty** a zobrazit hodnoty na portálu. K nastavení aplikace můžete přistupovat také pomocí Azure CLI.

### <a name="portal"></a>Portál

Chcete-li přidat nastavení na portálu, vyberte možnost **nové nastavení aplikace** a přidejte novou dvojici klíč-hodnota.

![Nastavení aplikace Function App v Azure Portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

### <a name="azure-cli"></a>Azure CLI

Příkaz [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list) vrátí existující nastavení aplikace, jako v následujícím příkladu:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

Příkaz [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) přidá nebo aktualizuje nastavení aplikace. Následující příklad vytvoří nastavení s klíčem s názvem `CUSTOM_FUNCTION_APP_SETTING` a hodnotou `12345`:


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

### <a name="use-application-settings"></a>Použít nastavení aplikace

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Když vyvíjíte aplikaci funkcí lokálně, musíte zachovat místní kopie těchto hodnot v souboru projektu Local. Settings. JSON. Další informace najdete v tématu [místní nastavení souboru](functions-run-local.md#local-settings-file).

## <a name="platform-features"></a>Funkce platformy

![Karta funkce Function App Platform](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Aplikace Function App běží na a jsou udržované Azure App Service platformou. Aplikace Function App mají proto přístup k většině funkcí základní webové hostující platformy Azure. Karta **funkce platformy** je místo, kde máte přístup k mnoha funkcím App Service platformy, kterou můžete použít ve svých aplikacích Function App. 

> [!NOTE]
> Ne všechny funkce App Service jsou dostupné, když aplikace Function App běží na plánu hostování spotřeby.

Zbývající část tohoto článku se zaměřuje na následující funkce App Service v Azure Portal, které jsou užitečné pro funkce:

+ [Editor App Service](#editor)
+ [Console](#console)
+ [Rozšířené nástroje (Kudu)](#kudu)
+ [Možnosti nasazení](#deployment)
+ [CORS](#cors)
+ [Ověřování](#auth)

Další informace o tom, jak pracovat s nastaveními App Service, najdete v tématu [Konfigurace nastavení Azure App Service](../app-service/configure-common.md).

### <a name="editor"></a>Editor služby App Service

![Editor App Service](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

Editor App Service je pokročilý editor v portálu, který můžete použít k úpravě konfiguračních souborů JSON a souborů kódu podobně. Výběrem této možnosti se spustí samostatná karta prohlížeče se základním editorem. To umožňuje integraci s úložištěm Git, spuštěním a laděním kódu a úpravou nastavení aplikace Function App. Tento editor nabízí vylepšené vývojové prostředí pro vaše funkce v porovnání s integrovaným editorem funkcí.  

Doporučujeme zvážit vývoj funkcí v místním počítači. Když vyvíjíte místně a publikujete do Azure, soubory projektu jsou na portálu jen pro čtení. Další informace naleznete v tématu [Code and test Azure Functions lokálně](functions-develop-local.md).

### <a name="console"></a>Stromu

![Konzola aplikace Function App](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

Konzola na portálu je ideální vývojářský nástroj, když dáváte přednost interakci s aplikací Function App z příkazového řádku. Mezi běžné příkazy patří vytváření a navigace adresářů a souborů a také provádění dávkových souborů a skriptů. 

Při vývoji místně doporučujeme použít [Azure Functions Core Tools](functions-run-local.md) a [Azure CLI].

### <a name="kudu"></a>Rozšířené nástroje (Kudu)

![Konfigurace Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

Rozšířené nástroje pro App Service (označované také jako Kudu) poskytují přístup k pokročilým funkcím správy aplikace Function App. Z Kudu můžete spravovat systémové informace, nastavení aplikace, proměnné prostředí, rozšíření webu, hlavičky protokolu HTTP a proměnné serveru. **Kudu** můžete také spustit tak, že přejdete do koncového bodu SCM pro vaši aplikaci Function App, například `https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment"></a>Centrum nasazení

Při použití řešení správy zdrojového kódu k vývoji a údržbě kódu vašich funkcí vám centrum nasazení umožní sestavovat a nasazovat ze správy zdrojového kódu. Váš projekt se sestaví a nasadí do Azure, když provedete aktualizace. Další informace najdete v tématu [technologie nasazení v Azure Functions](functions-deployment-technologies.md).

### <a name="cors"></a>Sdílení prostředků mezi zdroji

Aby se zabránilo spouštění škodlivého kódu v klientovi, moderní prohlížeče blokují požadavky webových aplikací na prostředky spuštěné v samostatné doméně. [Sdílení prostředků mezi zdroji (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) umožňuje `Access-Control-Allow-Origin` záhlaví deklarovat, které zdroje mají povolené volání koncových bodů ve vaší aplikaci Function App.

#### <a name="portal"></a>Portál

Když nakonfigurujete seznam **povolených zdrojů** pro aplikaci Function App, automaticky se do všech odpovědí z koncových bodů HTTP ve vaší aplikaci Function App přidá hlavička `Access-Control-Allow-Origin`. 

![Konfigurovat seznam CORS aplikace Function App](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

Pokud se použije zástupný znak (`*`), budou se všechny ostatní domény ignorovat. 

Pomocí příkazu [`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) přidejte doménu do seznamu povolených zdrojů. Následující příklad přidá doménu contoso.com:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

K vypsání aktuálních povolených zdrojů použijte příkaz [`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) .

### <a name="auth"></a>Přihlašovací

![Konfigurace ověřování pro aplikaci Function App](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

Pokud funkce používají aktivační událost HTTP, můžete vyžadovat první ověření volání. App Service podporuje ověřování Azure Active Directory a přihlašování pomocí poskytovatelů sociálních sítí, jako je Facebook, Microsoft a Twitter. Podrobnosti o konfiguraci konkrétních zprostředkovatelů ověřování najdete v tématu [Přehled ověřování Azure App Service](../app-service/overview-authentication-authorization.md). 


## <a name="next-steps"></a>Další kroky

+ [Konfigurace nastavení Azure App Service](../app-service/configure-common.md)
+ [Průběžné nasazování se službou Azure Functions](functions-continuous-deployment.md)

[Azure CLI]: /cli/azure/
[Azure Portal]: https://portal.azure.com
