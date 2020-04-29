---
title: Shromažďování a analýza protokolu aktivit Azure v Azure Monitor
description: Shromažďovat protokol aktivit Azure v Azure Monitor protokoly a používat řešení pro monitorování k analýze a hledání protokolu aktivit Azure napříč všemi předplatnými Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/14/2020
ms.openlocfilehash: 098aeaa06a26c57744402722aa3eacc51ea85fb7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81382856"
---
# <a name="collect-and-analyze-azure-activity-log-in-azure-monitor"></a>Shromažďování a analýza protokolu aktivit Azure v Azure Monitor
[Protokol aktivit Azure](platform-logs-overview.md) je [protokol platformy](platform-logs-overview.md) , který poskytuje přehled o událostech na úrovni předplatného, ke kterým došlo v Azure. I když můžete zobrazit protokol aktivit v Azure Portal, měli byste ho nakonfigurovat tak, aby se odesílaly do pracovního prostoru Log Analytics, aby bylo možné povolit další funkce Azure Monitor. Tento článek popisuje, jak provést tuto konfiguraci a jak odeslat protokol aktivit do služby Azure Storage a centra událostí.

Shromažďování protokolu aktivit v pracovním prostoru Log Analytics přináší následující výhody:

- Neúčtují se žádné příjem dat ani doba uchovávání dat pro data protokolu aktivit uložená v pracovním prostoru Log Analytics.
- Korelujte data protokolu aktivit s dalšími daty monitorování shromážděnými pomocí Azure Monitor.
- Pomocí dotazů protokolu můžete provádět komplexní analýzy a získat podrobné přehledy o položkách protokolu aktivit.
- Používejte výstrahy protokolu se záznamy aktivit a umožňují komplexnější logiku výstrahy.
- Ukládání záznamů protokolu aktivit po dobu delší než 90 dní.
- Konsolidujte položky protokolu z několika předplatných Azure a klientů do jednoho umístění pro účely analýzy dohromady.

> [!IMPORTANT]
> Shromažďování protokolů napříč klienty vyžaduje [Azure Lighthouse](/azure/lighthouse).

## <a name="collecting-activity-log"></a>Shromažďování protokolu aktivit
Protokol aktivit je automaticky shromažďován pro [zobrazení v Azure Portal](activity-log-view.md). Pokud ho chcete shromažďovat v pracovním prostoru Log Analytics nebo ho odeslat do služby Azure Storage nebo centra událostí, vytvořte [nastavení diagnostiky](diagnostic-settings.md). Jedná se o stejnou metodu, jakou používají protokoly prostředků, aby byly konzistentní pro všechny [protokoly platforem](platform-logs-overview.md).  

Chcete-li vytvořit nastavení diagnostiky pro protokol aktivit, vyberte možnost **nastavení diagnostiky** z nabídky **protokol aktivit** v Azure monitor. Podrobnosti o vytvoření nastavení najdete v tématu [Vytvoření nastavení diagnostiky pro shromažďování protokolů a metrik platforem v Azure](diagnostic-settings.md) . Popis kategorií, které můžete filtrovat, najdete [v části kategorie v protokolu aktivit](activity-log-view.md#categories-in-the-activity-log) . Pokud máte nějaká starší nastavení, před vytvořením nastavení diagnostiky je nezapomeňte zakázat. U obou povolených může být výsledkem duplicitní data.

![Nastavení diagnostiky](media/diagnostic-settings-subscription/diagnostic-settings.png)


> [!NOTE]
> V současné době můžete k nastavení diagnostiky na úrovni předplatného vytvořit jenom pomocí Azure Portal a Správce prostředků šablony. 


## <a name="legacy-settings"></a>Starší nastavení 
I když jsou nastavení diagnostiky upřednostňovanou metodou pro odeslání protokolu aktivit do různých míst, starší metody budou fungovat i v případě, že se nerozhodnete nahradit nastavením diagnostiky. Nastavení diagnostiky mají oproti starším metodám následující výhody a doporučuje se aktualizovat konfiguraci:

- Konzistentní způsob shromažďování všech protokolů platforem.
- Shromažďování protokolů aktivit napříč několika předplatnými a klienty.
- Vyfiltruje shromažďování, aby se shromáždily jenom protokoly pro konkrétní kategorie.
- Shromáždí všechny kategorie protokolů aktivit. Některé kategorie nejsou shromažďovány pomocí starší metody.
- Rychlejší latence pro přijímání protokolů. Předchozí metoda má latenci přibližně 15 minut, zatímco nastavení diagnostiky se přidá pouze přibližně 1 minutu.



### <a name="log-profiles"></a>Profily protokolů
Profily protokolu představují starší metodu pro posílání protokolu aktivit do služby Azure Storage nebo centra událostí. Pomocí následujícího postupu můžete pokračovat v práci s profilem protokolu nebo ho zakázat při přípravě na migraci na nastavení diagnostiky.

1. V nabídce **Azure monitor** v Azure Portal vyberte **Protokol aktivit**.
3. Klikněte na **Nastavení diagnostiky**.

   ![Nastavení diagnostiky](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Klikněte na fialový banner pro starší verze prostředí.

    ![Starší verze prostředí](media/diagnostic-settings-subscription/legacy-experience.png)

### <a name="log-analytics-workspace"></a>Pracovní prostor služby Log Analytics
Starší metoda shromažďování protokolu aktivit do pracovního prostoru Log Analytics připojuje protokol v konfiguraci pracovního prostoru. 

1. V nabídce **Log Analytics pracovní prostory** v Azure Portal vyberte pracovní prostor pro shromáždění protokolu aktivit.
1. V části **zdroje dat pracovního prostoru** v nabídce pracovního prostoru vyberte **Protokol aktivit Azure**.
1. Klikněte na předplatné, které chcete připojit.

    ![Pracovní prostory](media/activity-log-collect/workspaces.png)

1. Kliknutím na **připojit** připojte protokol aktivit v předplatném k vybranému pracovnímu prostoru. Pokud je předplatné už připojené k jinému pracovnímu prostoru, odpojte ho kliknutím na **Odpojit** .

    ![Připojit pracovní prostory](media/activity-log-collect/connect-workspace.png)


Chcete-li nastavení zakázat, proveďte stejný postup a kliknutím na tlačítko **Odpojit** odeberte odběr z pracovního prostoru.


## <a name="analyze-activity-log-in-log-analytics-workspace"></a>Analýza protokolu aktivit v Log Analytics pracovním prostoru
Když připojíte Protokol aktivit k pracovnímu prostoru Log Analytics, položky se zapisují do pracovního prostoru do tabulky s názvem *AzureActivity* , kterou můžete načíst pomocí [dotazu protokolu](../log-query/log-query-overview.md). Struktura této tabulky se liší v závislosti na [kategorii záznamu protokolu](activity-log-view.md#categories-in-the-activity-log). Popis každé kategorie najdete v tématu [schéma událostí protokolu aktivit Azure](activity-log-schema.md) .


### <a name="data-structure-changes"></a>Změny struktury dat
Nastavení diagnostiky shromáždí stejná data jako starší metoda, která se používá ke shromáždění protokolu aktivit s některými změnami struktury tabulky *AzureActivity* .

Sloupce v následující tabulce byly zastaralé v aktualizovaném schématu. Stále existují v *AzureActivity* , ale nebudou mít žádná data. Náhrada pro tyto sloupce není nová, ale obsahuje stejná data jako zastaralé sloupce. Jsou v jiném formátu, takže možná budete muset upravit dotazy protokolů, které je používají. 

| Zastaralý sloupec | Sloupec pro nahrazení |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> V některých případech mohou být hodnoty v těchto sloupcích velkými písmeny. Pokud máte dotaz, který obsahuje tyto sloupce, měli byste použít [operátor = ~](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) pro porovnání velkých a malých písmen.

Do *AzureActivity* v aktualizovaném schématu byly přidány následující sloupce:

- Authorization_d
- Claims_d
- Properties_d


## <a name="activity-logs-analytics-monitoring-solution"></a>Řešení monitorování analýz protokolů aktivit
Řešení Azure Log Analytics monitoring bude brzy zastaralé a nahradí sešit pomocí aktualizovaného schématu v pracovním prostoru Log Analytics. Řešení můžete používat i v případě, že ho už máte povolený, ale dá se použít jenom v případě, že shromažďujete protokol aktivit pomocí nastavení starší verze. 



### <a name="use-the-solution"></a>Použití řešení
K monitorování řešení se dostanete z nabídky **monitor** v Azure Portal. V části **přehledy** vyberte **Další** a otevřete stránku **Přehled** s dlaždicemi řešení. Dlaždice **protokoly aktivit Azure** zobrazuje počet záznamů **AzureActivity** ve vašem pracovním prostoru.

![Dlaždice protokolů aktivit Azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Kliknutím na dlaždici **protokoly aktivit Azure** otevřete zobrazení **protokolů aktivit Azure** . Zobrazení obsahuje části vizualizace v následující tabulce. Každá část obsahuje až 10 položek, které odpovídají kritériím této části pro zadaný časový rozsah. Kliknutím na **Zobrazit vše** v dolní části části můžete spustit dotaz protokolu, který vrátí všechny odpovídající záznamy.

![Řídicí panel protokolů aktivit Azure](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>Povolit řešení pro nové odběry
V tuto chvíli už brzy nebude možné do předplatného přidat řešení Activity Log Analytics pomocí Azure Portal. Můžete ji přidat pomocí následujícího postupu se šablonou Resource Manageru. 

1. Zkopírujte následující JSON do souboru s názvem *ActivityLogTemplate*. JSON.

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

2. Nasaďte šablonu pomocí následujících příkazů PowerShellu:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```


## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [protokolu aktivit](platform-logs-overview.md).
- Přečtěte si další informace o [Azure monitor datovou platformu](data-platform.md).
- Pomocí [dotazů protokolu](../log-query/log-query-overview.md) můžete zobrazit podrobné informace z protokolu aktivit.
