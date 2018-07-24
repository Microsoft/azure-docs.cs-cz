---
title: 'Kurz: Monitorování protokolů brány Azure Firewall'
description: V tomto kurzu se dozvíte, jak povolit a spravovat protokoly brány Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: a4922fda80b957138a9929090f9d3c349348185d
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991862"
---
# <a name="tutorial-monitor-azure-firewall-logs"></a>Kurz: Monitorování protokolů brány Azure Firewall

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

Příklady v článcích o bráně Azure Firewall předpokládají, že už máte veřejnou verzi Preview brány Azure Firewall zapnutou. Další informace najdete v tématu o [povolení veřejné verze Preview brány Azure Firewall](public-preview.md).

Bránu Azure Firewall můžete monitorovat pomocí protokolů brány firewall. K auditu operací na prostředcích brány Azure Firewall můžete také použít protokoly aktivit.

Některé z těchto protokolů jsou přístupné z webu Azure Portal. Protokoly můžete odeslat do služeb [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), Storage a Event Hubs a analyzovat je můžete ve službě Log Analytics nebo jinými nástroji, jako je Excel nebo Power BI.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Povolit protokolování prostřednictvím webu Azure Portal
> * Povolit protokolování prostřednictvím PowerShellu
> * Zobrazit a analyzovat protokol aktivit
> * Zobrazit a analyzovat protokoly pravidel sítě a aplikace

## <a name="diagnostic-logs"></a>Diagnostické protokoly

 Pro bránu Azure Firewall jsou k dispozici následující diagnostické protokoly:

* **Protokol pravidel aplikace**

   Protokol pravidel aplikace se ukládá do účtu úložiště, streamuje do služby Event Hubs a/nebo odesílá do služby Log Analytics, pouze pokud jste to povolili v bráně Azure Firewall. Každé nové připojení, které odpovídá jednomu z vašich nakonfigurovaných pravidel aplikace, vytvoří pro dané přijaté nebo odepřené připojení protokol. Jak je vidět v následujícím příkladu, data se protokolují ve formátu JSON:

   ```
   Category: access logs are either application or network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **Protokol pravidel sítě**

   Protokol pravidel sítě se ukládá do účtu úložiště, streamuje do služby Event Hubs a/nebo odesílá do služby Log Analytics, pouze pokud jste to povolili v bráně Azure Firewall. Každé nové připojení, které odpovídá jednomu z vašich nakonfigurovaných pravidel aplikace, vytvoří pro dané přijaté nebo odepřené připojení protokol. Jak je vidět v následujícím příkladu, data se protokolují ve formátu JSON:

   ```
   Category: access logs are either application or network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

Protokoly můžete ukládat třemi způsoby:

* **Učet úložiště**: Účty úložiště jsou nejvhodnější pro ukládání protokolů na delší dobu, které budete kontrolovat pouze v případě potřeby.
* **Centra událostí**: Centra událostí jsou skvělou volbou pro integrování protokolů s jinými nástroji správy akcí a informací o zabezpečení (SEIM), abyste o svých prostředcích získávali upozornění.
* **Log Analytics**: Tato služba je nejvhodnější pro obecné monitorování aplikací v reálném čase nebo sledování trendů.

## <a name="activity-logs"></a>Protokoly aktivit

   Položky protokolu aktivit se ve výchozím nastavení shromažďují a můžete si je zobrazit na webu Azure Portal.

   Všechny operace odeslané do předplatného Azure si můžete zobrazit v [protokolech aktivit Azure](../azure-resource-manager/resource-group-audit.md) (dříve operační protokoly a protokoly auditu).

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Povolení diagnostického protokolování prostřednictvím webu Azure Portal

Než se data v protokolech po dokončení tohoto procesu zapnutí protokolování diagnostiky zobrazí, může to trvat několik minut. Pokud nic nevidíte, zkuste to znovu po několika minutách.

1. Na webu Azure Portal otevřete skupinu prostředků brány firewall a klikněte na bránu firewall.
2. V části **Monitorování** klikněte na **Diagnostické protokoly**.

   Pro bránu Azure Firewall jsou k dispozici dva protokoly pro konkrétní služby:

   * Protokol pravidel aplikace
   * Protokol pravidel sítě

3. Pokud chcete začít shromažďovat data, klikněte na **Zapnout diagnostiku**.
4. Stránka **Nastavení diagnostiky** obsahuje nastavení diagnostických protokolů. 
5. V tomto příkladu ukládá protokoly služba Log Analytics, takže jako název zadejte **Log Analytics brány Firewall**.
6. Pracovní prostor nakonfigurujete kliknutím na **Poslat do Log Analytics**. Diagnostické protokoly můžete ukládat také pomocí služby center událostí a účtu úložiště.
7. V části **Log Analytics** klikněte na **Konfigurovat**.
8. Na stránce Pracovní prostory OMS klikněte na **Vytvořit nový pracovní prostor**.
9. Na stránce **Pracovní prostor Log Analytics** zadejte pro **Pracovní prostor OMS** nový název **firewall-oms**.
10. Vyberte předplatné, použijte existující skupinu prostředků brány firewall (**Test-FW-RG**), jako umístění vyberte **USA – východ** a u cenové úrovně zvolte **Free**.
11. Klikněte na **OK**.
   ![Spuštění procesu konfigurace][1]
12. V části **Protokol** začněte shromažďovat protokoly pravidel aplikace a sítě kliknutím na **AzureFirewallApplicationRule** a **AzureFirewallNetworkRule**.
   ![Uložení nastavení diagnostiky][2]
13. Klikněte na **Uložit**.

## <a name="enable-logging-with-powershell"></a>Povolení protokolování prostřednictvím PowerShellu

Protokolování aktivit je u každého prostředku Správce prostředků povolené automaticky. Abyste mohli začít shromažďovat data dostupná prostřednictvím těchto protokolů, musíte zapnout protokolování diagnostiky.

Protokolování diagnostiky zapnete následovně:

1. Poznamenejte si ID prostředku účtu úložiště, kam se data protokolu ukládají. Tato hodnota má formát: */subscriptions/\<ID předplatného\>/resourceGroups/\<název skupiny prostředků\>/providers/Microsoft.Storage/storageAccounts/\<název účtu úložiště\>*.

   Můžete použít libovolný účet úložiště z vašeho předplatného. Tuto informaci najdete pomocí webu Azure Portal na stránce **vlastností** prostředku.

2. Poznamenejte si ID prostředku brány Firewall, který má zapnuté protokolování. Tato hodnota má formát: */subscriptions/\<ID předplatného\>/resourceGroups/\<název skupiny prostředků\>/providers/Microsoft.Network/azureFirewalls/\<název brány firewall\>*.

   Tuto informaci najdete pomocí webu Azure Portal.

3. Protokolování diagnostiky zapnete pomocí následující rutiny PowerShellu:

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```
    
> [!TIP] 
>Diagnostické protokoly vlastí účet úložiště nevyžadují. Za používání úložiště k protokolování přístupu a výkonu se účtují poplatky.

## <a name="view-and-analyze-the-activity-log"></a>Zobrazení a analýza protokolu aktivit

Data protokolu aktivit si můžete zobrazit použitím jedné z následujících metod:

* **Nástroje Azure**: Načtěte informace z protokolu aktivit prostřednictvím Azure PowerShellu, Azure CLI, rozhraní Azure REST API nebo webu Azure Portal. Podrobné pokyny k jednotlivým metodám najdete v článku o [operacích s protokoly aktivit ve Správci prostředků](../azure-resource-manager/resource-group-audit.md).
* **Power BI**: Pokud ještě účet [Power BI](https://powerbi.microsoft.com/pricing) nemáte, můžete ho vyzkoušet zdarma. Díky [balíčku obsahu protokoly aktivit Azure pro Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) můžete svá data analyzovat pomocí předkonfigurovaných řídicích panelů, které můžete použít okamžitě nebo si je upravit.

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>Zobrazení a analyzování protokolů pravidel sítě a aplikace

Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) shromažďuje soubory protokolů čítačů a událostí. Zahrnuje vizualizace a výkonné vyhledávací funkce k analýze protokolů.

Můžete se také připojit k účtu úložiště a načíst položky protokolu JSON s protokoly přístupu a výkonu. Po stažení souborů JSON je můžete převést do formátu CSV a zobrazit si je v Excelu, Power BI nebo jiném nástroji s vizualizací dat.

> [!TIP]
> Pokud znáte Visual Studio a máte představu, jak u konstant a proměnných v jazyce C# měnit hodnoty, můžete použít [nástroje pro převedení protokolů](https://github.com/Azure-Samples/networking-dotnet-log-converter), které jsou k dispozici na GitHubu.


## <a name="next-steps"></a>Další kroky

Když jste nakonfigurovali bránu firewall, aby shromažďovala protokoly, můžete teď prozkoumat službu Log Analytics a zobrazit si svá data.

> [!div class="nextstepaction"]
> [Síťová řešení monitorování v Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)

[1]: ./media/tutorial-diagnostics/figure1.png
[2]: ./media/tutorial-diagnostics/figure2.png
