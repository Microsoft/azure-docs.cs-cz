---
title: Vytvoření nového prostředku založeného na pracovním prostoru Azure Monitor Application Insights | Microsoft Docs
description: Přečtěte si o krocích požadovaných k povolení nových Azure Monitorch Application Insightsch prostředků založených na pracovních prostorech.
ms.topic: conceptual
ms.date: 10/06/2020
ms.openlocfilehash: 3ec0b25a24af13b29a3568165009f8a6d66e0218
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578344"
---
# <a name="workspace-based-application-insights-resources"></a>Prostředky Application Insights založené na pracovním prostoru

Prostředky založené na pracovním prostoru podporují úplnou integraci mezi Application Insights a Log Analytics. Nyní se můžete rozhodnout pro odeslání telemetrie Application Insights do společného pracovního prostoru Log Analytics, který vám umožní úplný přístup ke všem funkcím Log Analytics a zároveň udržuje protokoly aplikací, infrastruktury a platforem v jednom konsolidovaném umístění.

To umožňuje také společné řízení přístupu na základě role Azure (Azure RBAC) napříč vašimi prostředky a eliminuje nutnost dotazů mezi aplikacemi a pracovními prostory.

> [!NOTE]
> Ingestování a uchovávání dat pro prostředky Application Insights založené na pracovních prostorech se účtují prostřednictvím pracovního prostoru Log Analytics, kde se data nacházejí. [Přečtěte si další informace]( ./pricing.md#workspace-based-application-insights) o fakturaci pro prostředky Application Insights založené na pracovních prostorech.

## <a name="new-capabilities"></a>Nové funkce

Application Insights na základě pracovního prostoru vám umožní využít výhod nejnovějších možností Azure Monitor a Log Analytics, včetně:

* [Klíče spravované zákazníkem (CMK)](../logs/customer-managed-keys.md) poskytují šifrování v klidovém prostředí pro vaše data pomocí šifrovacích klíčů, ke kterým máte přístup.
* [Privátní odkaz Azure](../logs/private-link-security.md) umožňuje bezpečně propojit služby Azure PaaS s vaší virtuální sítí pomocí privátních koncových bodů.
* [Přineste si vlastní úložiště (BYOS) pro Profiler a Snapshot Debugger](./profiler-bring-your-own-storage.md) vám poskytne plnou kontrolu nad zásadami šifrování, zásadami správy životnosti a přístupem k síti pro všechna data přidružená k Application Insights Profiler a Snapshot Debugger. 
* [Úrovně rezervace kapacity](../logs/manage-cost-storage.md#pricing-model) umožňují v porovnání s průběžnými platbami ušetřit až 25%. 
* Rychlejší příjem dat prostřednictvím Log Analytics příjmu streamování.

## <a name="create-workspace-based-resource"></a>Vytvoření prostředku založeného na pracovních prostorech

Přihlaste se k [Azure Portal](https://portal.azure.com)a vytvořte prostředek Application Insights:

> [!div class="mx-imgBorder"]
> ![Prostředek Application Insights založený na pracovním prostoru](./media/create-workspace-resource/create-workspace-based.png)

Pokud ještě nemáte existující Log Analytics pracovní prostor, [Projděte si dokumentaci k vytváření pracovních prostorů Log Analytics](../logs/quick-create-workspace.md).

**Prostředky založené na pracovním prostoru jsou aktuálně k dispozici ve všech komerčních oblastech a Azure Government**

Po vytvoření prostředku se v podokně **přehledu** zobrazí příslušné informace o pracovním prostoru:

![Název pracovního prostoru](./media/create-workspace-resource/workspace-name.png)

Kliknutím na modrý text přejdete k přidruženému pracovnímu prostoru Log Analytics, kde můžete využít nové sjednocené prostředí dotazů na pracovní prostor.

> [!NOTE]
> Pořád zajišťujeme úplnou zpětnou kompatibilitu pro vaše Application Insights dotazy na prostředky, sešity a výstrahy založené na protokolu v rámci Application Insights prostředí. Chcete-li se dotazovat/zobrazit proti [nové struktuře nebo schématu tabulky založené na pracovním prostoru](apm-tables.md) , musíte nejprve přejít do svého pracovního prostoru Log Analytics. Výběr **protokolů (analýz)** z podoken Application Insights vám poskytne přístup k prostředí Classic Application Insights dotazů.

## <a name="copy-the-connection-string"></a>Zkopírování připojovacího řetězce

[Připojovací řetězec](./sdk-connection-string.md?tabs=net) identifikuje prostředek, ke kterému chcete přidružit data telemetrie. Umožňuje také upravit koncové body, které prostředek použije jako cíl pro vaši telemetrii. Budete muset zkopírovat připojovací řetězec a přidat ho do kódu aplikace nebo do proměnné prostředí.

## <a name="monitoring-configuration"></a>Konfigurace monitorování

Jakmile se vytvoří prostředek Application Insights založený na pracovním prostoru, konfigurace monitorování je poměrně jednoduchá.

### <a name="code-based-application-monitoring"></a>Monitorování aplikací na základě kódu

Pro monitorování aplikací založených na kódu stačí nainstalovat příslušnou sadu Application Insights SDK a Ukázat ji na klíč instrumentace nebo připojovací řetězec k nově vytvořenému prostředku.  

Podrobnou dokumentaci k nastavení Application Insights SDK pro monitorování založené na kódu najdete v dokumentaci ke konkrétnímu jazyku nebo rozhraní:

- [ASP.NET](./asp-net.md)
- [ASP.NET Core ](./asp-net-core.md)
- [Úlohy na pozadí & moderních konzolových aplikací (.NET/.NET Core)](./worker-service.md)
- [Klasické konzolové aplikace (.NET)](./console.md) 
- [Kompilátor ](./java-get-started.md?tabs=maven)
- [JavaScript](./javascript.md)
- [Node.js](./nodejs.md)
- [Python](./opencensus-python.md)

### <a name="codeless-monitoring-and-visual-studio-resource-creation"></a>Monitorování nekódování a vytváření prostředků sady Visual Studio

Pokud chcete bez kódu sledovat služby, jako je Azure Functions a Azure App Services, budete muset nejprve vytvořit prostředek Application Insights založený na pracovním prostoru a pak ho nasměrovat na tento prostředek během fáze konfigurace monitorování.

I když tyto služby nabízejí možnost vytvořit nový prostředek Application Insights v rámci svého procesu vytváření prostředků, prostředky vytvořené prostřednictvím těchto možností uživatelského rozhraní jsou aktuálně omezené na klasické Application Insights prostředí.

Totéž platí pro prostředí Application Insightsho vytváření prostředků v aplikaci Visual Studio pro ASP.NET a ASP.NET Core. Je nutné vybrat existující prostředek založený na pracovním prostoru z s uživatelským rozhraním pro povolení monitorování sady Visual Studio. Výběr možnosti vytvořit nový prostředek v rámci sady Visual Studio vám omezí vytváření prostředků pro klasický Application Insights.

## <a name="creating-a-resource-automatically"></a>Automatické vytvoření prostředku

### <a name="azure-cli"></a>Azure CLI

Abyste měli přístup k verzi Preview Application Insights příkazy rozhraní příkazového řádku Azure CLI, musíte nejdřív spustit:

```azurecli
 az extension add -n application-insights
```

Pokud tento příkaz nespustíte `az extension add` , zobrazí se chybová zpráva s oznámením: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Nyní můžete spustit následující a vytvořit prostředek Application Insights:

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--only-show-errors]
                                         [--query-access {Disabled, Enabled}]
                                         [--tags]
                                         [--workspace]
```

#### <a name="example"></a>Příklad

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g my_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

Úplnou dokumentaci k Azure CLI pro tento příkaz najdete v dokumentaci k rozhraní příkazového [řádku Azure CLI](/cli/azure/ext/application-insights/monitor/app-insights/component#ext-application-insights-az-monitor-app-insights-component-create).

### <a name="azure-powershell"></a>Azure PowerShell

`New-AzApplicationInsights`Příkaz prostředí PowerShell v současné době nepodporuje vytváření prostředků Application Insights založených na pracovních prostorech. Pokud chcete vytvořit prostředek založený na pracovním prostoru pomocí PowerShellu, můžete použít šablony Azure Resource Manager níže a nasadit pomocí PowerShellu.

### <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

#### <a name="template-file"></a>Soubor šablony

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>Soubor parametrů

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>Změna přidruženého pracovního prostoru

Po vytvoření prostředku Application Insights založeném na pracovním prostoru můžete upravit přidružené pracovní prostory Log Analytics.

V podokně Application Insights prostředku vyberte **vlastnosti**  >  **změnit pracovní prostor**  >  **Log Analytics pracovní prostory** .

## <a name="export-telemetry"></a>Exportovat telemetrii

Funkce starší verze průběžného exportu není u prostředků založených na pracovních prostorech podporována. Místo toho vyberte **nastavení diagnostiky**  >  **Přidat nastavení diagnostiky** v rámci vašeho prostředku Application Insights. Můžete vybrat všechny tabulky nebo podmnožinu tabulek k archivaci do účtu úložiště nebo streamovat do centra událostí Azure.

> [!NOTE]
> Pro export telemetrie se momentálně neúčtují žádné další poplatky. Informace o cenách této funkce budou k dispozici na [stránce s cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).  Před zahájením fakturace budou odeslána oznámení. Pokud se rozhodnete i nadále používat <feature name> po období oznámení, bude se vám účtovat příslušná sazba. 
 

## <a name="next-steps"></a>Další kroky

* [Zkoumání metrik](../essentials/metrics-charts.md)
* [Psaní analytických dotazů](../logs/log-query-overview.md)
