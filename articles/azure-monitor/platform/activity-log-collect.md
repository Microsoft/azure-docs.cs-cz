---
title: Shromažďování a analýza protokolu aktivit Azure v Azure Monitoru
description: Shromážděte protokol aktivit Azure v protokolech monitorování Azure a použijte řešení monitorování k analýze a prohledáváme protokol aktivit Azure ve všech vašich předplatných Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/14/2020
ms.openlocfilehash: 098aeaa06a26c57744402722aa3eacc51ea85fb7
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382856"
---
# <a name="collect-and-analyze-azure-activity-log-in-azure-monitor"></a>Shromažďování a analýza protokolu aktivit Azure v Azure Monitoru
[Protokol aktivit Azure](platform-logs-overview.md) je protokol [platformy,](platform-logs-overview.md) který poskytuje přehled o událostech na úrovni předplatného, ke kterým došlo v Azure. Zatímco můžete zobrazit protokol aktivit na webu Azure Portal, měli byste ho nakonfigurovat tak, aby se odesílal do pracovního prostoru Log Analytics, aby povolil další funkce Azure Monitoru. Tento článek popisuje, jak provést tuto konfiguraci a jak odeslat protokol aktivit do úložiště Azure a centra událostí.

Shromažďování protokolu aktivit v pracovním prostoru Analýzy protokolů poskytuje následující výhody:

- Žádné požití dat nebo poplatek za uchovávání dat pro data protokolu aktivit uložených v pracovním prostoru Log Analytics.
- Korelujte data protokolu aktivit s dalšími daty monitorování shromážděnými službou Azure Monitor.
- Pomocí dotazů protokolu můžete provádět komplexní analýzy a získat podrobné přehledy o položkách protokolu aktivit.
- Pomocí výstrah protokolu s položkami aktivity, které umožňují složitější logiku výstrah.
- Uložte položky protokolu aktivit po dobu delší než 90 dní.
- Konsolidujte položky protokolu z více předplatných Azure a tenantů do jednoho umístění pro analýzu společně.

> [!IMPORTANT]
> Shromažďování protokolů mezi tenanty vyžaduje [Azure Lighthouse](/azure/lighthouse).

## <a name="collecting-activity-log"></a>Shromažďování protokolu aktivit
Protokol aktivit se shromažďuje automaticky pro [zobrazení na webu Azure Portal](activity-log-view.md). Chcete-li jej shromažďovat v pracovním prostoru Analýzy protokolů nebo jej odeslat do centra úložiště Azure nebo událostí, vytvořte [diagnostické nastavení](diagnostic-settings.md). Jedná se o stejnou metodu, kterou používají protokoly prostředků, takže je konzistentní pro všechny [protokoly platformy](platform-logs-overview.md).  

Pokud chcete vytvořit diagnostické nastavení pro protokol aktivit, vyberte **nastavení diagnostiky** z nabídky **Protokolu aktivit** v Azure Monitoru. Podrobnosti o vytvoření nastavení v [tématu Vytvoření diagnostického nastavení pro shromažďování protokolů platformy a metrik v Azure](diagnostic-settings.md) najdete podrobnosti o vytvoření nastavení. Popis kategorií, které lze filtrovat, naleznete [v části Kategorie v protokolu aktivit.](activity-log-view.md#categories-in-the-activity-log) Pokud máte nějaké starší nastavení, ujistěte se, že je před vytvořením diagnostického nastavení zakážete. Povolení obou může mít za následek duplicitní data.

![Nastavení diagnostiky](media/diagnostic-settings-subscription/diagnostic-settings.png)


> [!NOTE]
> V současné době můžete vytvořit pouze nastavení diagnostiky na úrovni předplatného pomocí portálu Azure a šablony Správce prostředků. 


## <a name="legacy-settings"></a>Starší nastavení 
Zatímco nastavení diagnostiky jsou upřednostňovanou metodou pro odeslání protokolu aktivit do různých cílů, starší metody budou nadále fungovat, pokud se nerozhodnete nahradit diagnostickým nastavením. Diagnostická nastavení mají oproti starším metodám následující výhody a doporučujeme aktualizovat konfiguraci:

- Konzistentní metoda pro shromažďování všech protokolů platformy.
- Shromažďovat protokol aktivit přes více předplatných a klientů.
- Kolekce filtrů shromažďovat pouze protokoly pro určité kategorie.
- Shromažďovat všechny kategorie protokolu aktivit. Některé kategorie nejsou shromažďovány pomocí starší metody.
- Rychlejší latence pro přihlášce protokolu. Předchozí metoda má latenci přibližně 15 minut, zatímco nastavení diagnostiky přidá pouze asi 1 minutu.



### <a name="log-profiles"></a>Profily protokolů
Profily protokolů jsou starší metodou pro odesílání protokolu aktivit do úložiště Azure nebo centra událostí. Pomocí následujícího postupu pokračujte v práci s profilem protokolu nebo jej zakažte v rámci přípravy na migraci na diagnostické nastavení.

1. Z nabídky **Azure Monitor** na webu Azure Portal vyberte **protokol aktivit**.
3. Klikněte na **Nastavení diagnostiky**.

   ![Nastavení diagnostiky](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Klikněte na fialový banner pro starší prostředí.

    ![Starší verze prostředí](media/diagnostic-settings-subscription/legacy-experience.png)

### <a name="log-analytics-workspace"></a>Pracovní prostor služby Log Analytics
Starší metoda pro shromažďování protokolu aktivit do pracovního prostoru Log Analytics je připojení protokolu v konfiguraci pracovního prostoru. 

1. Z nabídky **pracovníprostory Analýzy protokolů** na portálu Azure vyberte pracovní prostor pro shromažďování protokolu aktivit.
1. V části **Zdroje dat pracovního prostoru** v nabídce pracovního prostoru vyberte protokol aktivit **Azure**.
1. Klikněte na předplatné, které chcete připojit.

    ![Pracovní prostory](media/activity-log-collect/workspaces.png)

1. Kliknutím na **Připojit** připojíte protokol aktivit v předplatném k vybranému pracovnímu prostoru. Pokud je předplatné již připojeno k jinému pracovnímu prostoru, odpojte ho nejprve kliknutím na **Odpojit.**

    ![Připojení pracovních prostorů](media/activity-log-collect/connect-workspace.png)


Chcete-li toto nastavení zakázat, proveďte stejný postup a klepnutím na tlačítko **Odpojit** odeberte odběr z pracovního prostoru.


## <a name="analyze-activity-log-in-log-analytics-workspace"></a>Analýza protokolu aktivit v pracovním prostoru Log Analytics
Když připojíte protokol aktivit k pracovnímu prostoru Analýzy protokolů, položky se zapisují do pracovního prostoru do tabulky s názvem *AzureActivity,* kterou můžete načíst pomocí [dotazu protokolu](../log-query/log-query-overview.md). Struktura této tabulky se liší v závislosti na [kategorii položky protokolu](activity-log-view.md#categories-in-the-activity-log). Informace o popisu jednotlivých kategorií najdete v [tématu schéma událostí protokolu aktivit Azure.](activity-log-schema.md)


### <a name="data-structure-changes"></a>Změny struktury dat
Diagnostická nastavení shromažďovat stejná data jako starší metoda slouží ke shromažďování protokolu aktivit s některými změnami struktury tabulky *AzureActivity.*

Sloupce v následující tabulce byly v aktualizovaném schématu zastaralé. Stále existují v *AzureActivity,* ale nebudou mít žádná data. Nahrazení těchto sloupců není nové, ale obsahují stejná data jako zastaralá sloupec. Jsou v jiném formátu, takže budete muset upravit dotazy protokolu, které je používají. 

| Zastaralá kolona | Náhradní sloupec |
|:---|:---|
| Status aktivity    | ActivityStatusValue    |
| ActivitySubstatus | Hodnota ActivitySubstatusValue |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> V některých případech mohou být hodnoty v těchto sloupcích velkými písmeny. Pokud máte dotaz, který obsahuje tyto sloupce, měli byste použít [=~ operátor](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) provést porovnání malá a velká písmena.

Následující sloupec byly přidány do *AzureActivity* v aktualizovaném schématu:

- Authorization_d
- Claims_d
- Properties_d


## <a name="activity-logs-analytics-monitoring-solution"></a>Řešení monitorování protokolů aktivit Analytics
Řešení monitorování Azure Log Analytics se brzy zastarala a nahradí sešit pomocí aktualizovaného schématu v pracovním prostoru Analýzy protokolů. Řešení můžete stále používat, pokud ho již máte povolené, ale lze ho použít pouze v případě, že shromažďujete protokol aktivit pomocí starších nastavení. 



### <a name="use-the-solution"></a>Použijte řešení
Řešení monitorování jsou přístupná z nabídky **Monitorování** na webu Azure Portal. V části **Přehledy** vyberte **Další** a otevřete stránku **Přehled** s dlaždicemi řešení. Dlaždice **Protokoly aktivit Azure** zobrazuje počet záznamů **AzureActivity** ve vašem pracovním prostoru.

![Dlaždice Protokoly aktivit Azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Kliknutím na dlaždici **Protokoly aktivit Azure** otevřete zobrazení **protokolů aktivit Azure.** Zobrazení obsahuje vizualizační části v následující tabulce. Každá část obsahuje až 10 položek odpovídajících kritériím těchto dílů pro zadaný časový rozsah. Dotaz protokolu, který vrátí všechny odpovídající záznamy, můžete spustit kliknutím na **zobrazit vše** v dolní části dílu.

![Řídicí panel Protokoly aktivit Azure](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>Povolení řešení pro nová předplatná
Brzy už nebudete moct přidat řešení Activity Logs Analytics do svého předplatného pomocí portálu Azure. Můžete jej přidat pomocí následujícího postupu se šablonou správce prostředků. 

1. Zkopírujte následující json do souboru s názvem *ActivityLogTemplate*.json.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. Nasazení šablony pomocí následujících příkazů Prostředí PowerShell:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```


## <a name="next-steps"></a>Další kroky

- Další informace o [protokolu aktivit](platform-logs-overview.md).
- Další informace o [datové platformě Azure Monitor](data-platform.md).
- Pomocí [dotazů protokolu](../log-query/log-query-overview.md) můžete zobrazit podrobné informace z protokolu aktivit.
