---
title: Migrace klasického prostředku Azure Monitor Application Insights do prostředku založeného na pracovním prostoru | Microsoft Docs
description: Seznamte se s kroky potřebnými k upgradu Azure Monitorch klasických prostředků Application Insights na nový model založený na pracovním prostoru.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 5791abe33dee2e62aadb00ae1024338e1e44a900
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100584257"
---
# <a name="migrate-to-workspace-based-application-insights-resources"></a>Migrace na prostředky Application Insights na základě pracovního prostoru

Tato příručka vás provede procesem migrace klasického Application Insights prostředku do prostředku založeného na pracovním prostoru. Prostředky založené na pracovním prostoru podporují úplnou integraci mezi Application Insights a Log Analytics. Prostředky založené na pracovních prostorech odesílají Application Insights telemetrie do společného pracovního prostoru Log Analytics, který umožňuje přístup k [nejnovějším funkcím Azure monitor](#new-capabilities) a udržování protokolů aplikací, infrastruktury a platforem v jednom konsolidovaném umístění.

Prostředky založené na pracovních prostorech umožňují společné řízení přístupu na základě role Azure (Azure RBAC) napříč vašimi prostředky a eliminují nutnost dotazů mezi aplikacemi a pracovními prostory.

**Prostředky založené na pracovním prostoru jsou aktuálně k dispozici ve všech komerčních oblastech a v Azure USA – státní správa.**

## <a name="new-capabilities"></a>Nové funkce

Application Insights na základě pracovního prostoru vám umožní využít výhod všech nejnovějších možností Azure Monitor a Log Analytics včetně:

* [Klíče spravované zákazníkem (CMK)](../logs/customer-managed-keys.md) poskytují šifrování v klidovém prostředí pro vaše data pomocí šifrovacích klíčů, ke kterým máte přístup.
* [Privátní odkaz Azure](../logs/private-link-security.md) umožňuje bezpečně propojit služby Azure PaaS s vaší virtuální sítí pomocí privátních koncových bodů.
* [Přineste si vlastní úložiště (BYOS) pro Profiler a Snapshot Debugger](./profiler-bring-your-own-storage.md) vám poskytne plnou kontrolu nad zásadami šifrování, zásadami správy životnosti a přístupem k síti pro všechna data přidružená k Application Insights Profiler a Snapshot Debugger. 
* [Úrovně rezervace kapacity](../logs/manage-cost-storage.md#pricing-model) umožňují v porovnání s průběžnými platbami ušetřit až 25%. 
* Rychlejší příjem dat prostřednictvím Log Analytics příjmu streamování.

## <a name="migration-process"></a>Proces migrace

Když migrujete do prostředku založeného na pracovním prostoru, nepřesunou se žádná data z úložiště klasických prostředků do nového úložiště založeného na pracovních prostorech. Pokud se rozhodnete migrovat, místo toho se změní umístění, do kterého se zapisují nová data do Log Analyticsho pracovního prostoru, přičemž se zachovává přístup k datům vašich klasických prostředků. 

Vaše Klasická data prostředků budou trvalá a budou se řídit nastaveními uchovávání v klasickém Application Insights prostředku. Všechna nová data ingestovaná po migraci budou podléhat [Nastavení uchování](../logs/manage-cost-storage.md#change-the-data-retention-period) přidruženého pracovního prostoru Log Analytics, který také podporuje [různá nastavení uchovávání informací podle datového typu](../logs/manage-cost-storage.md#retention-by-data-type).
Proces migrace je **trvalý a nemůže být obrácený**. Jakmile provedete migraci prostředku do Application Insights založeného na pracovních prostorech, bude to vždycky prostředek založený na pracovním prostoru. Jakmile však migrujete, budete moci cílový pracovní prostor kdykoli změnit podle potřeby. 

> [!NOTE]
> Ingestování a uchovávání dat pro prostředky Application Insights založené na pracovních prostorech se [účtují prostřednictvím pracovního prostoru Log Analytics](../logs/manage-cost-storage.md) , kde se data nacházejí. Pokud jste pro data ingestovaná do klasického Application Insights prostředku od migrace vybrali uchovávání dat větší než 90 dní, uchovávání dat se bude dál účtovat prostřednictvím tohoto prostředku Application Insights. [Přečtěte si další informace]( ./pricing.md#workspace-based-application-insights) o fakturaci pro prostředky Application Insights založené na pracovních prostorech.

Pokud nepotřebujete migrovat existující prostředek a místo toho chcete vytvořit nový prostředek Application Insights založený na pracovním prostoru, použijte [Průvodce vytvářením prostředků založených na pracovních prostorech](create-workspace-resource.md).

## <a name="pre-requisites"></a>Požadavky 

- Log Analytics pracovní prostor s režimem řízení přístupu nastaveným na **`use resource or workspace permissions`** nastavení. 

    - Prostředky Application Insights založené na pracovním prostoru nejsou kompatibilní s pracovními prostory nastavenými na vyhrazené **`workspace based permissions`** nastavení. Další informace o řízení přístupu k pracovnímu prostoru Log Analytics najdete v části [Log Analytics konfigurace pokynů pro režim řízení přístupu](../logs/manage-access.md#configure-access-control-mode) .

    - Pokud ještě nemáte existující Log Analytics pracovní prostor, [Projděte si dokumentaci k vytváření pracovních prostorů Log Analytics](../logs/quick-create-workspace.md).
    
- Průběžný export není u prostředků založených na pracovních prostorech podporován a je třeba jej zakázat.
Po dokončení migrace můžete pomocí [nastavení diagnostiky](../essentials/diagnostic-settings.md) nakonfigurovat archivaci dat do účtu úložiště nebo streamovat do služby Azure Event hub.  

- V části **Obecné**  >  **použití a odhadované náklady**  >  na pracovní prostor Log Analytics ověřte aktuální nastavení uchovávání **dat** . Toto nastavení bude mít vliv na to, jak dlouho se budou po migraci prostředku Application Insights ukládat všechna data, která se budou přijímat. Pokud v současné době ukládáte Application Insights data déle než výchozí 90 dní a chcete zachovat tuto větší dobu uchování, možná budete muset upravit nastavení uchovávání pracovního prostoru.

## <a name="migrate-your-resource"></a>Migrace prostředku

Tato část vás provede procesem migrace klasického Application Insights prostředku na nový typ prostředku založený na pracovním prostoru.

1. Z prostředku Application Insights v levém panelu nabídek vyberte **vlastnosti** pod nadpisem **Konfigurovat** .

    ![Vlastnosti zvýrazněné v červeném poli](./media/convert-classic-resource/properties.png)

2. Vyberte **`Migrate to Workspace-based`**.
    
     ![Tlačítko migrace prostředku](./media/convert-classic-resource/migrate.png)

3. Vyberte pracovní prostor Log Analytics, ve kterém chcete ukládat veškerou budoucí příjem Application Insights telemetrie.

     ![Uživatelské rozhraní Průvodce migrací s možností výběru pracovního prostoru Targe](./media/convert-classic-resource/migration.png)
    

Po migraci prostředku se v podokně **přehledu** zobrazí příslušné informace o pracovním prostoru:

![Název pracovního prostoru](./media/create-workspace-resource/workspace-name.png)

Kliknutím na modrý text přejdete k přidruženému pracovnímu prostoru Log Analytics, kde můžete využít nové sjednocené prostředí dotazů na pracovní prostor.

## <a name="understanding-log-queries"></a>Principy dotazů protokolu

Pořád zajišťujeme úplnou zpětnou kompatibilitu pro vaše Application Insights dotazy na prostředky, sešity a výstrahy založené na protokolu v rámci Application Insights prostředí. 

Pokud chcete zapisovat dotazy k [nové struktuře nebo schématu tabulky založené na pracovním prostoru](apm-tables.md), musíte nejprve přejít do svého pracovního prostoru Log Analytics. 

Při dotazování přímo z uživatelského rozhraní Log Analytics v pracovním prostoru se zobrazí pouze data, která jsou přijímána po migraci. Pro zobrazení klasických Application Insights dat a nových dat, která se ingestují po migraci v jednotném prostředí pro dotazování, použijte v rámci migrovaných prostředků Application Insights dotazování protokolů (Analytics).

## <a name="programmatic-resource-migration"></a>Migrace programového prostředku

### <a name="azure-cli"></a>Azure CLI

Abyste měli přístup k verzi Preview Application Insights příkazy rozhraní příkazového řádku Azure CLI, musíte nejdřív spustit:

```azurecli
 az extension add -n application-insights
```

Pokud tento příkaz nespustíte `az extension add` , zobrazí se chybová zpráva s oznámením: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Nyní můžete spustit následující a vytvořit prostředek Application Insights:

```azurecli
az monitor app-insights component update --app
                                         --resource-group
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--query-access {Disabled, Enabled}]
                                         [--retention-time]
                                         [--workspace]
```

#### <a name="example"></a>Příklad

```azurecli
az monitor app-insights component update --app your-app-insights-resource-name -g your_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

Úplnou dokumentaci k Azure CLI pro tento příkaz najdete v dokumentaci k rozhraní příkazového [řádku Azure CLI](/cli/azure/ext/application-insights/monitor/app-insights/component#ext-application-insights-az-monitor-app-insights-component-update).

### <a name="azure-powershell"></a>Azure PowerShell

`Update-AzApplicationInsights`Příkaz prostředí PowerShell v současné době nepodporuje migraci klasického Application Insights prostředku na základě pracovního prostoru. Pokud chcete vytvořit prostředek založený na pracovním prostoru pomocí PowerShellu, můžete použít šablony Azure Resource Manager níže a nasadit pomocí PowerShellu.

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

V podokně Application Insights prostředku vyberte **vlastnosti**  >  **změnit pracovní prostor**  >  **Log Analytics pracovní prostory**.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="access-mode"></a>Režim přístupu

**Chybová zpráva:** *vybraný pracovní prostor je konfigurován pomocí režimu přístupu na základě pracovního prostoru. Může to mít vliv na některé funkce APM. V nastavení pracovního prostoru vyberte jiný pracovní prostor nebo povolte přístup na základě prostředků. Tuto chybu můžete přepsat pomocí rozhraní příkazového řádku.* 

Aby mohl prostředek Application Insights založený na pracovním prostoru správně fungovat, je třeba změnit režim řízení přístupu cílového pracovního prostoru Log Analytics na nastavení **oprávnění prostředku nebo pracovního prostoru** . Toto nastavení se nachází v uživatelském rozhraní log Analyticsového prostoru v části **vlastnosti**  >  **režimu řízení přístupu**. Podrobné pokyny najdete v tématu věnovaném [Log Analytics konfiguraci pokynů pro režim řízení přístupu](../logs/manage-access.md#configure-access-control-mode). Pokud je režim řízení přístupu nastavený na nastavení výhradní přístup k **pracovnímu prostoru vyžadovat** , migrace pomocí možnosti migrace na portálu zůstane blokovaná.

Pokud nemůžete změnit režim řízení přístupu z důvodů zabezpečení pro aktuální cílový pracovní prostor, doporučujeme vytvořit nový pracovní prostor Log Analytics, který se bude používat pro migraci. 

### <a name="continuous-export"></a>Průběžný export

**Chybová zpráva:** *před pokračováním je třeba zakázat průběžný export. Po migraci použijte nastavení diagnostiky pro export.* 

Funkce starší verze průběžného exportu není u prostředků založených na pracovních prostorech podporována. Před migrací je potřeba zakázat průběžný export.

1. V zobrazení prostředků Application Insights v části **Konfigurovat** nadpis vyberte **průběžný export**.

    ![Položka nabídky průběžného exportu](./media/convert-classic-resource/continuous-export.png)

2. Vyberte **Zakázat**.

    ![Tlačítko pro vypnutí průběžného exportu](./media/convert-classic-resource/disable.png)

- Jakmile vyberete zakázat, můžete přejít zpět na uživatelské rozhraní migrace. Pokud se zobrazí stránka upravit průběžný export vás vyzve k uložení nastavení, můžete pro tuto výzvu Vybrat OK, protože se nevztahují k zakázání nebo povolení průběžného exportu.

- Po úspěšné migraci prostředku Application Insights do služby založené na pracovním prostoru můžete pomocí nastavení diagnostiky nahradit funkce, které průběžný export používá k poskytnutí. V rámci vašeho prostředku Application Insights vyberte **nastavení diagnostiky**  >  **Přidat nastavení diagnostiky** . Můžete vybrat všechny tabulky nebo podmnožinu tabulek k archivaci do účtu úložiště nebo streamovat do centra událostí Azure. Podrobné pokyny k nastavení diagnostiky najdete v [pokynech k nastavení diagnostiky Azure monitor](../essentials/diagnostic-settings.md).

### <a name="retention-settings"></a>Nastavení uchovávání

**Upozornění:** *vaše přizpůsobené nastavení uchovávání Application Insights se nebude vztahovat na data odesílaná do pracovního prostoru. Budete ho muset znovu nakonfigurovat samostatně.*

Před migrací není nutné provádět žádné změny, ale tato zpráva vám upozorní na to, že aktuální nastavení uchovávání Application Insights není nastavené na výchozí dobu uchování 90 dní. Tato zpráva upozornění znamená, že před migrací a zahájením ingestování nových dat můžete změnit nastavení uchovávání informací pro váš pracovní prostor Log Analytics. 

Můžete kontrolovat aktuální nastavení uchování pro Log Analytics v části **Obecné**  >  **použití a odhadované náklady** na  >  **uchovávání dat** v rámci Log Analytics uživatelského rozhraní. Toto nastavení bude mít vliv na to, jak dlouho se budou po migraci prostředku Application Insights ukládat všechna data, která se budou přijímat.

## <a name="next-steps"></a>Další kroky

* [Zkoumání metrik](../essentials/metrics-charts.md)
* [Psaní analytických dotazů](../logs/log-query-overview.md)
