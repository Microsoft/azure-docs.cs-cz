---
title: Konfigurace nastavení aplikace Function App v Azure Functions
description: Přečtěte si, jak nakonfigurovat nastavení aplikace Function App v Azure Functions.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 04/13/2020
ms.custom: cc996988-fb4f-47, devx-track-azurecli
ms.openlocfilehash: 4db6abeb3e6f4a07780268a6455177e0ca237205
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98598482"
---
# <a name="manage-your-function-app"></a>Správa aplikace Function App 

V Azure Functions aplikace Function App poskytuje kontext spuštění pro vaše jednotlivé funkce. Chování aplikace Function App se vztahuje na všechny funkce hostované danou funkcí Function App. Všechny funkce aplikace Function App musí být ve stejném [jazyce](supported-languages.md). 

Jednotlivé funkce aplikace Function App se nasazují společně a navzájem se škálují. Všechny funkce ve stejné sdílené složce aplikace Function App mají na každou instanci stejnou velikost jako aplikace Function App. 

Připojovací řetězce, proměnné prostředí a další nastavení aplikace jsou definovány samostatně pro každou aplikaci Function App. Všechna data, která musí být sdílená mezi aplikacemi Function App, by se měla ukládat externě v trvalém úložišti.

## <a name="get-started-in-the-azure-portal"></a>Začínáme na webu Azure Portal

1. Začněte tím, že přejdete na [Azure Portal] a přihlásíte se k účtu Azure. Na panelu hledání v horní části portálu zadejte název aplikace Function App a vyberte ji ze seznamu. 

2. V části **Nastavení** v levém podokně vyberte **Konfigurace**.

    :::image type="content" source="./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png" alt-text="Přehled aplikace Function App v Azure Portal":::

Na stránce Přehled můžete přejít na všechno, co potřebujete ke správě aplikace Function App, zejména **[nastavení aplikace](#settings)** a **[funkce platformy](#platform-features)**.

## <a name="work-with-application-settings"></a><a name="settings"></a>Práce s nastavením aplikace

Nastavení aplikace je možné spravovat z [Azure Portal](functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings) a pomocí [Azure CLI](functions-how-to-use-azure-function-app-settings.md?tabs=azurecli#settings) a [Azure PowerShell](functions-how-to-use-azure-function-app-settings.md?tabs=powershell#settings). Můžete také spravovat nastavení aplikace z [Visual Studio Code](functions-develop-vs-code.md#application-settings-in-azure) a ze sady [Visual Studio](functions-develop-vs.md#function-app-settings). 

Tato nastavení jsou uložená zašifrovaná. Další informace najdete v tématu [zabezpečení nastavení aplikace](security-concepts.md#application-settings).

# <a name="portal"></a>[Azure Portal](#tab/portal)

Karta **nastavení aplikace** uchovává nastavení, která používá aplikace Function App. Chcete-li zobrazit hodnoty na portálu, je nutné vybrat možnost **Zobrazit hodnoty** . Chcete-li přidat nastavení na portálu, vyberte možnost **nové nastavení aplikace** a přidejte novou dvojici klíč-hodnota.

![Nastavení aplikace Function App v Azure Portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

[`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list)Příkaz vrátí existující nastavení aplikace, jako v následujícím příkladu:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

[`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)Příkaz přidá nebo aktualizuje nastavení aplikace. Následující příklad vytvoří nastavení s klíčem s názvem `CUSTOM_FUNCTION_APP_SETTING` a hodnotou `12345` :


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

[`Get-AzFunctionAppSetting`](/powershell/module/az.functions/get-azfunctionappsetting)Rutina vrátí existující nastavení aplikace, jako v následujícím příkladu: 

```azurepowershell-interactive
Get-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME>
```

[`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting)Příkaz přidá nebo aktualizuje nastavení aplikace. Následující příklad vytvoří nastavení s klíčem s názvem `CUSTOM_FUNCTION_APP_SETTING` a hodnotou `12345` :

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"CUSTOM_FUNCTION_APP_SETTING" = "12345"}
```

---

### <a name="use-application-settings"></a>Použít nastavení aplikace

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Když vyvíjíte aplikaci funkcí lokálně, musíte zachovat místní kopie těchto hodnot v local.settings.jsv souboru projektu. Další informace najdete v tématu [místní nastavení souboru](functions-run-local.md#local-settings-file).

## <a name="hosting-plan-type"></a>Typ plánu hostování

Když vytvoříte aplikaci Function App, vytvoříte také plán hostování, ve kterém je aplikace spuštěná. Plán může mít jednu nebo víc aplikací Function App. Funkce, škálování a ceny vašich funkcí závisí na typu plánu. Další informace najdete v tématu [možnosti hostování Azure Functions](functions-scale.md).

Typ plánu, který aplikace Function App používá, můžete určit z Azure Portal nebo pomocí rozhraní příkazového řádku Azure CLI nebo Azure PowerShell. 

Následující hodnoty udávají typ plánu:

| Typ plánu | Portál | Azure CLI/PowerShell |
| --- | --- | --- |
| [Využití](consumption-plan.md) | **Využití** | `Dynamic` |
| [Premium](functions-premium-plan.md) | **ElasticPremium** | `ElasticPremium` |
| [Vyhrazeno (App Service)](dedicated-plan.md) | Některé | Některé |

# <a name="portal"></a>[Azure Portal](#tab/portal)

Chcete-li určit typ plánu používaného aplikací Function App, přečtěte si téma **App Service plán** na kartě **Přehled** aplikace Function App v [Azure Portal](https://portal.azure.com). Pokud chcete zobrazit cenovou úroveň, vyberte název **plánu App Service** a v levém podokně vyberte **vlastnosti** .

![Zobrazit plán škálování na portálu](./media/functions-scale/function-app-overview-portal.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Spuštěním následujícího příkazu rozhraní příkazového řádku Azure získáte typ plánu hostování:

```azurecli-interactive
functionApp=<FUNCTION_APP_NAME>
resourceGroup=FunctionMonitoringExamples
appServicePlanId=$(az functionapp show --name $functionApp --resource-group $resourceGroup --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv

```  

V předchozím příkladu nahraďte `<RESOURCE_GROUP>` a `<FUNCTION_APP_NAME>` názvem skupiny prostředků a název aplikace funkcí odpovídající. 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Spusťte následující příkaz Azure PowerShell, který získá typ plánu hostování:

```azurepowershell-interactive
$FunctionApp = '<FUNCTION_APP_NAME>'
$ResourceGroup = '<RESOURCE_GROUP>'

$PlanID = (Get-AzFunctionApp -ResourceGroupName $ResourceGroup -Name $FunctionApp).AppServicePlan
(Get-AzFunctionAppPlan -Name $PlanID -ResourceGroupName $ResourceGroup).SkuTier
```
V předchozím příkladu nahraďte `<RESOURCE_GROUP>` a `<FUNCTION_APP_NAME>` názvem skupiny prostředků a název aplikace funkcí odpovídající. 

---

## <a name="plan-migration"></a>Plánování migrace

Příkazy rozhraní příkazového řádku Azure můžete použít k migraci aplikace Function App mezi plánem spotřeby a plánem Premium ve Windows. Konkrétní příkazy závisí na směru migrace. Přímá migrace na vyhrazený plán (App Service) není v současné době podporovaná.

Tato migrace není podporována na platformě Linux.

### <a name="consumption-to-premium"></a>Spotřeba do úrovně Premium

Pomocí následujícího postupu můžete migrovat plán spotřeby do plánu Premium ve Windows:

1. Spusťte následující příkaz, který vytvoří nový plán App Service (elastické Premium) ve stejné oblasti a skupině prostředků jako vaše existující aplikace Function App.  

    ```azurecli-interactive
    az functionapp plan create --name <NEW_PREMIUM_PLAN_NAME> --resource-group <MY_RESOURCE_GROUP> --location <REGION> --sku EP1
    ```

1. Spuštěním následujícího příkazu migrujete stávající aplikaci Function App do nového plánu Premium.

    ```azurecli-interactive
    az functionapp update --name <MY_APP_NAME> --resource-group <MY_RESOURCE_GROUP> --plan <NEW_PREMIUM_PLAN>
    ```

1. Pokud už nepotřebujete svůj předchozí plán aplikace Function App, po potvrzení, že jste úspěšně migrovali na nový, odstraňte původní plán aplikace Function App. Spuštěním následujícího příkazu zobrazíte seznam všech plánů spotřeby ve vaší skupině prostředků.

    ```azurecli-interactive
    az functionapp plan list --resource-group <MY_RESOURCE_GROUP> --query "[?sku.family=='Y'].{PlanName:name,Sites:numberOfSites}" -o table
    ```

    Plán můžete bezpečně odstranit pomocí nulových webů, což je ten, který jste migrovali z.

1. Spuštěním následujícího příkazu odstraňte plán spotřeby, ze kterého jste migrovali.

    ```azurecli-interactive
    az functionapp plan delete --name <CONSUMPTION_PLAN_NAME> --resource-group <MY_RESOURCE_GROUP>
    ```

### <a name="premium-to-consumption"></a>Premium na spotřebu

Následující postup použijte k migraci plánu Premium na plán spotřeby ve Windows:

1. Spusťte následující příkaz, který vytvoří novou aplikaci Function App (spotřeba) ve stejné oblasti a skupině prostředků jako vaše existující aplikace Function App. Tento příkaz také vytvoří nový plán spotřeby, ve kterém je spuštěná aplikace Function App.

    ```azurecli-interactive
    az functionapp create --resource-group <MY_RESOURCE_GROUP> --name <NEW_CONSUMPTION_APP_NAME> --consumption-plan-location <REGION> --runtime dotnet --functions-version 3 --storage-account <STORAGE_NAME>
    ```

1. Spuštěním následujícího příkazu migrujete stávající aplikaci Function App do nového plánu spotřeby.

    ```azurecli-interactive
    az functionapp update --name <MY_APP_NAME> --resource-group <MY_RESOURCE_GROUP> --plan <NEW_CONSUMPTION_PLAN>
    ```

1. Odstraňte aplikaci funkcí, kterou jste vytvořili v kroku 1, protože potřebujete jenom plán, který jste vytvořili pro spuštění stávající aplikace Function App.

    ```azurecli-interactive
    az functionapp delete --name <NEW_CONSUMPTION_APP_NAME> --resource-group <MY_RESOURCE_GROUP>
    ```

1. Pokud už nepotřebujete předchozí plán aplikace Function App, odstraňte původní plán aplikací Function po potvrzení, že jste úspěšně migrovali na nový. Upozorňujeme, že pokud se plán neodstraní, bude se vám i nadále účtovat plán Premium. Spuštěním následujícího příkazu zobrazíte seznam všech plánů Premium ve vaší skupině prostředků.

    ```azurecli-interactive
    az functionapp plan list --resource-group <MY_RESOURCE_GROUP> --query "[?sku.family=='EP'].{PlanName:name,Sites:numberOfSites}" -o table
    ```

1. Spuštěním následujícího příkazu odstraňte plán Premium, ze kterého jste migrovali.

    ```azurecli-interactive
    az functionapp plan delete --name <PREMIUM_PLAN> --resource-group <MY_RESOURCE_GROUP>
    ```

## <a name="platform-features"></a>Funkce platformy

Aplikace Function App běží v a jsou udržovány nástrojem Azure App Service platformou. Aplikace Function App mají proto přístup k většině funkcí základní webové hostující platformy Azure. V levém podokně získáte přístup k mnoha funkcím App Service platformy, které můžete použít ve svých aplikacích Function App. 

> [!NOTE]
> Ne všechny funkce App Service jsou dostupné, když aplikace Function App běží na plánu hostování spotřeby.

Zbývající část tohoto článku se zaměřuje na následující funkce App Service v Azure Portal, které jsou užitečné pro funkce:

+ [Editor App Service](#editor)
+ [Konzola](#console)
+ [Rozšířené nástroje (Kudu)](#kudu)
+ [Možnosti nasazení](#deployment)
+ [CORS](#cors)
+ [Authentication](#auth)

Další informace o tom, jak pracovat s nastaveními App Service, najdete v tématu [Konfigurace nastavení Azure App Service](../app-service/configure-common.md).

### <a name="app-service-editor"></a><a name="editor"></a>Editor App Service

![Editor App Service](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

Editor App Service je pokročilý editor v portálu, který můžete použít k úpravě konfiguračních souborů JSON a souborů kódu podobně. Výběrem této možnosti se spustí samostatná karta prohlížeče se základním editorem. To umožňuje integraci s úložištěm Git, spuštěním a laděním kódu a úpravou nastavení aplikace Function App. Tento editor nabízí vylepšené vývojové prostředí pro vaše funkce v porovnání s integrovaným editorem funkcí.  

Doporučujeme zvážit vývoj funkcí v místním počítači. Když vyvíjíte místně a publikujete do Azure, soubory projektu jsou na portálu jen pro čtení. Další informace naleznete v tématu [Code and test Azure Functions lokálně](functions-develop-local.md).

### <a name="console"></a><a name="console"></a>Konzola

![Konzola aplikace Function App](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

Konzola na portálu je ideální vývojářský nástroj, když dáváte přednost interakci s aplikací Function App z příkazového řádku. Mezi běžné příkazy patří vytváření a navigace adresářů a souborů a také provádění dávkových souborů a skriptů. 

Při vývoji místně doporučujeme použít [Azure Functions Core Tools](functions-run-local.md) a [Azure CLI].

### <a name="advanced-tools-kudu"></a><a name="kudu"></a>Rozšířené nástroje (Kudu)

![Konfigurace Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

Rozšířené nástroje pro App Service (označované také jako Kudu) poskytují přístup k pokročilým funkcím správy aplikace Function App. Z Kudu můžete spravovat systémové informace, nastavení aplikace, proměnné prostředí, rozšíření webu, hlavičky protokolu HTTP a proměnné serveru. **Kudu** můžete také spustit tak, že přejdete do koncového bodu SCM pro vaši aplikaci Function App, jako je například`https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment-center"></a><a name="deployment"></a>Deployment Center

Při použití řešení správy zdrojového kódu k vývoji a údržbě kódu vašich funkcí vám centrum nasazení umožní sestavovat a nasazovat ze správy zdrojového kódu. Váš projekt se sestaví a nasadí do Azure, když provedete aktualizace. Další informace najdete v tématu [technologie nasazení v Azure Functions](functions-deployment-technologies.md).

### <a name="cross-origin-resource-sharing"></a><a name="cors"></a>Sdílení prostředků různého původu

Aby se zabránilo spouštění škodlivého kódu v klientovi, moderní prohlížeče blokují požadavky webových aplikací na prostředky spuštěné v samostatné doméně. [Sdílení prostředků mezi zdroji (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) umožňuje `Access-Control-Allow-Origin` záhlaví deklarovat, které zdroje mají povolené volání koncových bodů ve vaší aplikaci Function App.

#### <a name="portal"></a>Portál

Když nakonfigurujete seznam **povolených zdrojů** pro aplikaci Function App, `Access-Control-Allow-Origin` automaticky se do všech odpovědí z koncových bodů HTTP ve vaší aplikaci Function App přidá hlavička. 

![Konfigurovat seznam CORS aplikace Function App](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

Při použití zástupného znaku ( `*` ) jsou všechny ostatní domény ignorovány. 

Pomocí [`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) příkazu přidejte doménu do seznamu povolených zdrojů. Následující příklad přidá doménu contoso.com:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

Pomocí [`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) příkazu můžete zobrazit seznam aktuálních povolených zdrojů.

### <a name="authentication"></a><a name="auth"></a>Authentication

![Konfigurace ověřování pro aplikaci Function App](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

Pokud funkce používají aktivační událost HTTP, můžete vyžadovat první ověření volání. App Service podporuje ověřování Azure Active Directory a přihlašování pomocí poskytovatelů sociálních sítí, jako je Facebook, Microsoft a Twitter. Podrobnosti o konfiguraci konkrétních zprostředkovatelů ověřování najdete v tématu [Přehled ověřování Azure App Service](../app-service/overview-authentication-authorization.md). 


## <a name="next-steps"></a>Další kroky

+ [Konfigurace nastavení Azure App Service](../app-service/configure-common.md)
+ [Průběžné nasazování se službou Azure Functions](functions-continuous-deployment.md)

[Azure CLI]: /cli/azure/
[Azure Portal]: https://portal.azure.com
