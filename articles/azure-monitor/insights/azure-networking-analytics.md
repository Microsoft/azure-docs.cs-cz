---
title: Řešení Azure Networking Analytics v Azure Monitor | Microsoft Docs
description: Řešení Azure Networking Analytics v Azure Monitor můžete použít ke kontrole protokolů skupin zabezpečení sítě Azure a protokolů Azure Application Gateway.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2018
ms.openlocfilehash: 4dc5b84ff127aef173deecfd2be705004d92ee0c
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449928"
---
# <a name="azure-networking-monitoring-solutions-in-azure-monitor"></a>Řešení monitorování sítě Azure v Azure Monitor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor nabízí následující řešení pro monitorování sítí:
* Network Performance Monitor (NPM) do
    * Monitorování stavu sítě
* Přehled služby Azure Application Gateway Analytics
    * Protokoly Application Gateway Azure
    * Metriky Azure Application Gateway
* Řešení pro monitorování a audit síťové aktivity v cloudové síti
    * [Analýza provozu](../../networking/network-monitoring-overview.md#traffic-analytics) 
    * Azure Network Security Group Analytics

## <a name="network-performance-monitor-npm"></a>Network Performance Monitor (NPM)

Řešení pro správu [Network Performance Monitor](../../networking/network-monitoring-overview.md) je řešení pro monitorování sítě, které monitoruje stav, dostupnost a dostupnost sítí.  Slouží k monitorování připojení mezi:

* Veřejný cloud a místní
* Datová centra a umístění uživatelů (pobočky)
* Podsítě hostující různé úrovně vícevrstvé aplikace.

Další informace najdete v tématu [Network Performance Monitor](../../networking/network-monitoring-overview.md).

## <a name="azure-application-gateway-and-network-security-group-analytics"></a>Analýzy skupin zabezpečení sítě a Application Gateway Azure
Použití řešení:
1. Přidejte řešení pro správu do Azure Monitor a
2. Povolte diagnostiku pro přesměrování diagnostiky do pracovního prostoru Log Analytics v Azure Monitor. Není nutné zapisovat protokoly do úložiště objektů BLOB v Azure.

Můžete povolit diagnostiku a odpovídající řešení pro jednu nebo obě skupiny zabezpečení Application Gateway a sítě.

Pokud nepovolíte protokolování diagnostických prostředků pro konkrétní typ prostředku, ale nainstalujete řešení, ovládací panel pro tento prostředek je prázdný a zobrazí se chybová zpráva.

> [!NOTE]
> V lednu 2017 se změnil podporovaný způsob odesílání protokolů z aplikačních bran a skupin zabezpečení sítě do pracovního prostoru Log Analytics. Pokud se zobrazí řešení **Azure Networking Analytics (nepoužívané)** , přečtěte si téma [migrace z původního řešení pro analýzu sítě](#migrating-from-the-old-networking-analytics-solution) , kde najdete kroky, které je potřeba provést.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Kontrola informací o kolekci síťových dat Azure
Řešení Azure Application Gateway Analytics a Správa analýzy skupin zabezpečení sítě shromažďují protokoly diagnostiky přímo ze skupin Azure Application Gateway a zabezpečení sítě. Není nutné zapisovat protokoly do úložiště objektů BLOB v Azure a pro shromažďování dat není nutné žádného agenta.

V následující tabulce jsou uvedeny metody shromažďování dat a další podrobnosti o tom, jak se shromažďují data pro Azure Application Gateway analýzy a analýzy skupin zabezpečení sítě.

| Platforma | Přímý Agent | Agent Operations Manager pro Systems Center | Azure | Operations Manager požadováno? | Data agenta Operations Manager odeslána prostřednictvím skupiny pro správu | Četnost shromažďování dat |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |při přihlášení |


## <a name="azure-application-gateway-analytics-solution-in-azure-monitor"></a>Řešení Azure Application Gateway Analytics v Azure Monitor

![Symbol Azure Application Gateway Analytics](media/azure-networking-analytics/azure-analytics-symbol.png)

Pro aplikační brány se podporují tyto protokoly:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

Pro aplikační brány se podporují následující metriky:


* propustnost 5 minut

### <a name="install-and-configure-the-solution"></a>Instalace a konfigurace řešení
K instalaci a konfiguraci řešení Azure Application Gateway Analytics použijte následující pokyny:

1. Povolte řešení Azure Application Gateway Analytics z [webu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureAppGatewayAnalyticsOMS?tab=Overview) nebo pomocí procesu popsaného v tématu [Přidání Azure Monitor řešení z galerie řešení](./solutions.md).
2. Povolte protokolování diagnostiky pro [aplikační brány](../../application-gateway/application-gateway-diagnostics.md) , které chcete monitorovat.

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Povolení diagnostiky Azure Application Gateway na portálu

1. V Azure Portal přejděte na prostředek Application Gateway a sledujte ho.
2. Výběrem *diagnostické protokoly* otevřete následující stránku.

   ![Snímek obrazovky se stránkou diagnostické protokoly pro prostředek Application Gateway, který ukazuje možnost zapnout diagnostiku.](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics01.png)
3. Kliknutím na *zapnout diagnostiku* otevřete následující stránku.

   ![Snímek obrazovky stránky pro konfiguraci nastavení diagnostiky Možnost pro odeslání do Log Analytics je vybrána, protože se jedná o tři typy protokolů a metriku.](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics02.png)
4. Pokud chcete zapnout diagnostiku, klikněte *na pod* položkou *stav*.
5. Kliknutím na zaškrtávací políčko pro *odeslání Log Analytics*.
6. Vyberte existující pracovní prostor Log Analytics nebo vytvořte pracovní prostor.
7. Kliknutím zaškrtněte políčko **protokol** pro každý typ protokolu, který chcete shromáždit.
8. Kliknutím na *Uložit* Povolte protokolování diagnostiky do Azure monitor.

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>Povolení diagnostiky sítě Azure pomocí PowerShellu

Následující skript PowerShellu poskytuje příklad, jak povolit protokolování prostředků pro aplikační brány.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzApplicationGateway -Name 'ContosoGateway'

Set-AzDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>Použití Azure Application Gateway Analytics
![Obrázek dlaždice Azure Application Gateway Analytics](media/azure-networking-analytics/log-analytics-appgateway-tile.png)

Po kliknutí na dlaždici **Azure Application Gateway Analytics** v přehledu můžete zobrazit souhrny protokolů a pak přejít k podrobnostem v následujících kategoriích:

* Protokoly Application Gateway Accessu
  * Chyby klienta a serveru pro protokoly přístupu Application Gateway
  * Žádosti za hodinu pro každý Application Gateway
  * Neúspěšné žádosti za hodinu pro každý Application Gateway
  * Chyby uživatelského agenta pro aplikační brány
* Application Gateway výkon
  * Stav hostitele pro Application Gateway
  * Maximální a 95. percentilu pro Application Gateway neúspěšné žádosti

![Snímek obrazovky s řídicím panelem protokolů Application Gateway, kde se zobrazují dlaždice s daty pro chyby brány, požadavky a žádosti o selhání](media/azure-networking-analytics/log-analytics-appgateway01.png)

![Snímek obrazovky s řídicím panelem protokolů Application Gateway, ve kterém se zobrazují dlaždice s daty pro chyby podle uživatelského agenta, stavu hostitele a neúspěšných požadavků.](media/azure-networking-analytics/log-analytics-appgateway02.png)

Na řídicím panelu **Azure Application Gateway Analytics** zkontrolujte souhrnné informace v jednom z oken a potom kliknutím na jednu Zobrazte podrobné informace na stránce prohledávání protokolu.

Na kterékoli stránce pro prohledávání protokolu můžete zobrazit výsledky podle času, podrobných výsledků a historie prohledávání protokolu. K zúžení výsledků můžete také filtrovat podle omezujících vlastností.


## <a name="azure-network-security-group-analytics-solution-in-azure-monitor"></a>Řešení analýzy skupin zabezpečení sítě Azure v Azure Monitor

![Symbol Azure Network Security Group Analytics](media/azure-networking-analytics/azure-analytics-symbol.png)

> [!NOTE]
> Řešení analýzy skupin zabezpečení sítě se přesouvá na podporu komunity, protože její funkce byla nahrazena [Analýza provozu](../../network-watcher/traffic-analytics.md).
> - Řešení je teď dostupné v [šablonách pro rychlý Start Azure](https://azure.microsoft.com/resources/templates/oms-azurensg-solution/) a už brzy nebude dostupné v Azure Marketplace.
> - Stávajícím zákazníkům, kteří už toto řešení přidali do svého pracovního prostoru, bude i nadále fungovat bez jakýchkoli změn.
> - Microsoft bude dál podporovat odesílání protokolů prostředků NSG do vašeho pracovního prostoru pomocí nastavení diagnostiky.

Pro skupiny zabezpečení sítě jsou podporovány následující protokoly:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>Instalace a konfigurace řešení
K instalaci a konfiguraci řešení Azure Networking Analytics použijte následující pokyny:

1. Pomocí postupu popsaného v tématu [přidání Azure monitor řešení z galerie řešení](./solutions.md)povolte řešení Azure Network Security Group Analytics.
2. Povolte protokolování diagnostiky pro prostředky [skupiny zabezpečení sítě](../../virtual-network/virtual-network-nsg-manage-log.md) , které chcete monitorovat.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Povolení diagnostiky skupiny zabezpečení sítě Azure na portálu

1. V Azure Portal přejděte na prostředek skupiny zabezpečení sítě, který se bude monitorovat.
2. Výběrem *diagnostické protokoly* otevřete následující stránku

   ![Snímek obrazovky se stránkou diagnostické protokoly pro prostředek skupiny zabezpečení sítě, který ukazuje možnost zapnout diagnostiku.](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics01.png)
3. Kliknutím na *zapnout diagnostiku* otevřete následující stránku.

   ![Snímek obrazovky stránky pro konfiguraci nastavení diagnostiky Stav je nastaven na zapnuto, je vybrána možnost Odeslat do Log Analytics a jsou vybrány dva typy protokolů.](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics02.png)
4. Pokud chcete zapnout diagnostiku, *klikněte na* pod položkou *stav* .
5. Kliknutím na zaškrtávací políčko pro *odeslání Log Analytics*
6. Vyberte existující pracovní prostor Log Analytics nebo vytvořte pracovní prostor.
7. Zaškrtněte políčko **protokol** pro každý typ protokolu, který se má shromažďovat.
8. Kliknutím na *Uložit* Povolte protokolování diagnostiky na Log Analytics

### <a name="enable-azure-network-diagnostics-using-powershell"></a>Povolení diagnostiky sítě Azure pomocí PowerShellu

Následující skript PowerShellu poskytuje příklad, jak povolit protokolování prostředků pro skupiny zabezpečení sítě.
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Použití analýzy skupin zabezpečení sítě Azure
Po kliknutí na dlaždici **Azure Network Security Group Analytics** na přehledu můžete zobrazit souhrny protokolů a pak přejít k podrobnostem v následujících kategoriích:

* Blokované toky ve skupině zabezpečení sítě
  * Pravidla skupiny zabezpečení sítě s blokovanými toky
  * Adresy MAC s blokovanými toky
* Povolené toky ve skupině zabezpečení sítě
  * Pravidla skupiny zabezpečení sítě s povolenými toky
  * Adresy MAC s povolenými toky

![Snímek obrazovky s daty pro skupinu zabezpečení sítě: blokované toky, včetně pravidel s blokovanými toky a adresou MAC s blokovanými toky.](media/azure-networking-analytics/log-analytics-nsg01.png)

![Snímek obrazovky dlaždic s daty pro skupinu zabezpečení sítě: povolené toky, včetně pravidel s povolenými toky a adresami MAC s povolenými toky.](media/azure-networking-analytics/log-analytics-nsg02.png)

Na řídicím panelu **Analytics skupiny zabezpečení sítě Azure** zkontrolujte souhrnné informace v jednom z oken a potom kliknutím na jednu Zobrazte podrobné informace na stránce prohledávání protokolu.

Na kterékoli stránce pro prohledávání protokolu můžete zobrazit výsledky podle času, podrobných výsledků a historie prohledávání protokolu. K zúžení výsledků můžete také filtrovat podle omezujících vlastností.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Migrace ze starého řešení pro analýzu sítě
V lednu 2017 se v pracovním prostoru Log Analytics změnil podporovaný způsob odesílání protokolů ze skupin Azure Application Gateway a skupin zabezpečení sítě Azure. Tyto změny poskytují následující výhody:
+ Protokoly se zapisují přímo do Azure Monitor bez nutnosti používat účet úložiště.
+ Menší latence od času, kdy jsou do nich generovány protokoly, které jsou k dispozici v Azure Monitor
+ Méně kroků konfigurace
+ Společný formát pro všechny typy diagnostiky Azure

Použití aktualizovaných řešení:

1. [Konfigurace diagnostiky, která se pošle přímo do Azure Monitor z Azure Application Gateway](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [Konfigurace diagnostiky, která se má odeslat přímo Azure Monitor ze skupin zabezpečení sítě Azure](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. Povolte *Azure Application Gateway Analytics* a řešení *Azure Network Security Group Analytics* pomocí procesu popsaného v tématu [Přidání Azure monitor řešení z galerie řešení](solutions.md)
3. Aktualizovat všechny uložené dotazy, řídicí panely nebo výstrahy, aby používaly nový datový typ
   + Typ je AzureDiagnostics. K filtrování síťových protokolů Azure můžete použít ResourceType.

     | Namísto: | Použije |
     | --- | --- |
     | NetworkApplicationgateways &#124;, kde OperationName = = "ApplicationGatewayAccess" | AzureDiagnostics &#124; kde ResourceType = = "APPLICATIONGATEWAYS" a NázevOperace = = "ApplicationGatewayAccess" |
     | NetworkApplicationgateways &#124;, kde OperationName = = "ApplicationGatewayPerformance" | AzureDiagnostics &#124; kde ResourceType = = "APPLICATIONGATEWAYS" a NázevOperace = = "ApplicationGatewayPerformance" |
     | NetworkSecuritygroups | AzureDiagnostics &#124; kde ResourceType = = "NETWORKSECURITYGROUPS" |

   + Pro každé pole, které má \_ v názvu příponu s, \_ d nebo \_ g, změňte první znak na malá písmena.
   + Pro každé pole, které má příponu \_ o v názvu, jsou data rozdělena do jednotlivých polí na základě názvů vnořených polí.
4. Odeberte řešení *Azure Networking Analytics (zastaralé)* .
   + Pokud používáte PowerShell, použijte `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

Data shromážděná před změnou nejsou v novém řešení viditelná. Můžete pokračovat v dotazování na tato data pomocí starého názvu typu a pole.

## <a name="troubleshooting"></a>Řešení potíží
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Další kroky
* K zobrazení podrobných dat diagnostiky Azure použijte [dotazy protokolu v Azure monitor](../log-query/log-query-overview.md) .

