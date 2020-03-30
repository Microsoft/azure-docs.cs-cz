---
title: Řešení Azure Networking Analytics ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Pomocí řešení Azure Networking Analytics v Azure Monitoru můžete zkontrolovat protokoly skupiny zabezpečení sítě Azure a protokoly Azure Application Gateway.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2018
ms.openlocfilehash: 1045f86db5e1a9ed1979a266937974045e401e27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275565"
---
# <a name="azure-networking-monitoring-solutions-in-azure-monitor"></a>Řešení monitorování sítí Azure ve službě Azure Monitor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor nabízí následující řešení pro monitorování vašich sítí:
* Sledování výkonu sítě (NPM)
    * Sledování stavu sítě
* Azure Application Gateway analytics ke kontrole
    * Protokoly aplikační brány Azure
    * Metriky Azure Application Gateway
* Řešení pro sledování a auditování síťových aktivit v cloudové síti
    * [Analýza provozu](https://docs.microsoft.com/azure/networking/network-monitoring-overview#traffic-analytics) 
    * Azure Network Security Group Analytics

## <a name="network-performance-monitor-npm"></a>Sledování výkonu sítě (NPM)

Řešení [pro správu sledování výkonu sítě](https://docs.microsoft.com/azure/networking/network-monitoring-overview) je řešení pro monitorování sítě, které monitoruje stav, dostupnost a dosažitelnost sítí.  Používá se ke sledování připojení mezi:

* Veřejný cloud a místní
* Datová centra a uživatelská lokace (pobočky)
* Podsítě hostující různé vrstvy vícevrstvé aplikace.

Další informace naleznete v [tématu Sledování výkonu sítě](https://docs.microsoft.com/azure/networking/network-monitoring-overview).

## <a name="azure-application-gateway-and-network-security-group-analytics"></a>Azure Application Gateway a analýza skupiny zabezpečení sítě
Použití řešení:
1. Přidejte řešení pro správu do Služby Azure Monitor a
2. Povolte diagnostiku a nasměrujte diagnostiku do pracovního prostoru Analýzy protokolů ve službě Azure Monitor. Není nutné zapisovat protokoly do úložiště objektů blob Azure.

Můžete povolit diagnostiku a odpovídající řešení pro jednu nebo obě skupiny application gateway a síťové zabezpečení.

Pokud nepovolíte protokolování diagnostických prostředků pro určitý typ prostředku, ale nainstalujete řešení, jsou řídicí panely pro tento prostředek prázdné a zobrazí se chybová zpráva.

> [!NOTE]
> V lednu 2017 se změnil podporovaný způsob odesílání protokolů z aplikačních bran a skupin zabezpečení sítě do pracovního prostoru Log Analytics. Pokud se zobrazí řešení **Azure Networking Analytics (zastaralé),** podívejte se na [migraci ze starého řešení Networking Analytics](#migrating-from-the-old-networking-analytics-solution) pro kroky, které je třeba provést.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Projděte si podrobnosti o shromažďování dat v síti Azure
Azure Application Gateway analytics a řešení pro správu analýzy skupiny network security group shromažďují protokoly diagnostiky přímo ze skupin y Azure Application Gateways a Network Security. Není nutné zapisovat protokoly do úložiště objektů blob Azure a pro shromažďování dat není vyžadován žádný agent.

V následující tabulce jsou uvedeny metody shromažďování dat a další podrobnosti o tom, jak se shromažďují data pro analýzy Azure Application Gateway a analýzy skupiny zabezpečení sítě.

| Platforma | Přímý agent | Agent operations manageru systémového centra | Azure | Vyžaduje se provozní manažer? | Data agenta Operations Manager odeslaná prostřednictvím skupiny pro správu | Četnost shromažďování dat |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |při přihlášení |


## <a name="azure-application-gateway-analytics-solution-in-azure-monitor"></a>Analytické řešení Azure Application Gateway v Azure Monitoru

![Symbol Azure Application Gateway Analytics](media/azure-networking-analytics/azure-analytics-symbol.png)

Pro aplikační brány jsou podporovány následující protokoly:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

Následující metriky jsou podporovány pro aplikační brány:again


* 5 minutová propustnost

### <a name="install-and-configure-the-solution"></a>Instalace a konfigurace řešení
K instalaci a konfiguraci analytického řešení Azure Application Gateway použijte následující pokyny:

1. Povolte analytické řešení Azure Application Gateway z [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureAppGatewayAnalyticsOMS?tab=Overview) nebo pomocí procesu popsaného v části Přidat řešení Azure Monitor z [Galerie řešení](../../azure-monitor/insights/solutions.md).
2. Povolte protokolování diagnostiky pro [aplikační brány, které](../../application-gateway/application-gateway-diagnostics.md) chcete sledovat.

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Povolení diagnostiky aplikační brány Azure na portálu

1. Na webu Azure Portal přejděte na prostředek aplikační brány, který chcete monitorovat.
2. Výběrem *možnosti Protokoly diagnostiky* otevřete následující stránku.

   ![image prostředku azure aplikační brány](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics01.png)
3. Kliknutím *na Zapnout diagnostiku* otevřete následující stránku.

   ![image prostředku azure aplikační brány](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics02.png)
4. Chcete-li zapnout diagnostiku, klepněte na tlačítko *Zapnuto* v části *Stav*.
5. Zaškrtněte políčko *Odeslat do analýzy protokolů*.
6. Vyberte existující pracovní prostor Log Analytics nebo vytvořte pracovní prostor.
7. Klikněte na zaškrtávací políčko v části **Protokol** pro každý z typů protokolu shromažďovat.
8. Kliknutím na *Uložit* povolíte protokolování diagnostiky do Azure Monitoru.

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>Povolení diagnostiky sítě Azure pomocí PowerShellu

Následující skript prostředí PowerShell poskytuje příklad, jak povolit protokolování prostředků pro aplikační brány.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzApplicationGateway -Name 'ContosoGateway'

Set-AzDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>Použití analýzy Služby Azure Application Gateway
![Obrázek analytické dlaždice Azure Application Gateway](media/azure-networking-analytics/log-analytics-appgateway-tile.png)

Po kliknutí na dlaždici **Analýzy služby Azure Application Gateway** v přehledu můžete zobrazit souhrny protokolů a potom přejít k podrobnostem pro následující kategorie:

* Protokoly přístupu k aplikační bráně
  * Chyby klienta a serveru pro protokoly přístupu k aplikační bráně
  * Požadavky za hodinu pro každou aplikační bránu
  * Neúspěšné požadavky za hodinu pro každou aplikační bránu
  * Chyby podle uživatelského agenta pro aplikační brány
* Výkon aplikační brány
  * Stav hostitele pro aplikační bránu
  * Maximální a 95. percentil pro požadavky application gateway selhaly

![obrázek analytického řídicího panelu Azure Application Gateway](media/azure-networking-analytics/log-analytics-appgateway01.png)

![obrázek analytického řídicího panelu Azure Application Gateway](media/azure-networking-analytics/log-analytics-appgateway02.png)

Na řídicím panelu **analýzy Služby Azure Application Gateway** zkontrolujte souhrnné informace v jednom z listů a kliknutím na jeden z nich zobrazte podrobné informace na stránce hledání protokolu.

Na libovolné stránce hledání protokolu můžete zobrazit výsledky podle času, podrobných výsledků a historie hledání protokolu. Můžete také filtrovat podle omezujících vlastností a zúžit výsledky.


## <a name="azure-network-security-group-analytics-solution-in-azure-monitor"></a>Analytické řešení Skupiny zabezpečení sítě Azure ve službě Azure Monitor

![Symbol Azure Network Security Group Analytics](media/azure-networking-analytics/azure-analytics-symbol.png)

> [!NOTE]
> Analytické řešení skupiny Network Security Group přechází na komunitní podporu, protože jeho funkce byla nahrazena [službou Traffic Analytics](../../network-watcher/traffic-analytics.md).
> - Řešení je teď dostupné v [azure quickstart šablony](https://azure.microsoft.com/resources/templates/oms-azurensg-solution/) a brzy už nebude k dispozici na Azure Marketplace.
> - Pro stávající zákazníky, kteří již přidali řešení do svého pracovního prostoru, bude nadále fungovat bez evidenčních změn.
> - Společnost Microsoft bude i nadále podporovat odesílání protokolů prostředků skupiny NSG do pracovního prostoru pomocí nastavení diagnostiky.

Pro skupiny zabezpečení sítě jsou podporovány následující protokoly:

* Událost NetworkSecurityGroupEvent
* Čítač pravidel networksecuritygroup

### <a name="install-and-configure-the-solution"></a>Instalace a konfigurace řešení
K instalaci a konfiguraci řešení Azure Networking Analytics použijte následující pokyny:

1. Povolte analytické řešení Skupiny zabezpečení azure ze stránek [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureNSGAnalyticsOMS?tab=Overview) nebo pomocí procesu popsaného v části Přidat řešení Azure Monitor z [Galerie řešení](../../azure-monitor/insights/solutions.md).
2. Povolte protokolování diagnostiky pro prostředky [skupiny zabezpečení sítě,](../../virtual-network/virtual-network-nsg-manage-log.md) které chcete sledovat.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Povolení diagnostiky skupiny zabezpečení sítě Azure na portálu

1. Na webu Azure Portal přejděte na prostředek skupiny zabezpečení sítě, který chcete monitorovat.
2. Výběrem *možnosti Protokoly diagnostiky* otevřete následující stránku.

   ![obrázek prostředku skupiny zabezpečení sítě Azure](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics01.png)
3. Kliknutím *na Zapnout diagnostiku* otevřete následující stránku.

   ![obrázek prostředku skupiny zabezpečení sítě Azure](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics02.png)
4. Chcete-li zapnout diagnostiku, klikněte *na Možnost Zapnout* v části *Stav.*
5. Klikněte na zaškrtávací políčko *Odeslat do analýzy protokolů.*
6. Vyberte existující pracovní prostor Log Analytics nebo vytvořte pracovní prostor
7. Klikněte na zaškrtávací políčko v části **Protokol** pro každý z typů protokolu, které chcete shromáždit.
8. Chcete-li povolit protokolování diagnostiky do služby Log Analytics, klepněte na tlačítko *Uložit.*

### <a name="enable-azure-network-diagnostics-using-powershell"></a>Povolení diagnostiky sítě Azure pomocí PowerShellu

Následující skript prostředí PowerShell poskytuje příklad povolení protokolování prostředků pro skupiny zabezpečení sítě
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Použití analýzy skupiny zabezpečení sítě Azure
Po kliknutí na dlaždici **Analýzy skupiny zabezpečení sítě Azure** v přehledu můžete zobrazit souhrny protokolů a potom přejít k podrobnostem pro následující kategorie:

* Skupina zabezpečení sítě blokovala toky
  * Pravidla skupiny zabezpečení sítě s blokovanými toky
  * MAC adresy s blokovanými toky
* Skupina zabezpečení sítě povolila toky
  * Pravidla skupiny zabezpečení sítě s povolenými toky
  * MAC adresy s povolenými toky

![obrázek analytického řídicího panelu skupiny Azure Network Security Group](media/azure-networking-analytics/log-analytics-nsg01.png)

![obrázek analytického řídicího panelu skupiny Azure Network Security Group](media/azure-networking-analytics/log-analytics-nsg02.png)

Na řídicím panelu **analýzy skupiny zabezpečení sítě Azure** zkontrolujte souhrnné informace v jednom z listů a potom kliknutím na jeden zobrazte podrobné informace na stránce hledání protokolu.

Na libovolné stránce hledání protokolu můžete zobrazit výsledky podle času, podrobných výsledků a historie hledání protokolu. Můžete také filtrovat podle omezujících vlastností a zúžit výsledky.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Migrace ze starého řešení Networking Analytics
V lednu 2017 se změnil podporovaný způsob odesílání protokolů z bran Azure Application Gateways a Skupiny zabezpečení sítě Azure do pracovního prostoru Log Analytics. Tyto změny poskytují následující výhody:
+ Protokoly se zapisují přímo do Azure Monitoru bez nutnosti používat účet úložiště
+ Menší latence od okamžiku, kdy jsou generovány protokoly k jejich dostupnosti ve službě Azure Monitor
+ Méně kroků konfigurace
+ Běžný formát pro všechny typy diagnostiky Azure

Použití aktualizovaných řešení:

1. [Konfigurace diagnostiky, která se má odesílat přímo do Azure Monitoru z aplikačních bran Azure](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [Konfigurace diagnostiky, která se má odesílat přímo do Azure Monitoru ze skupin zabezpečení sítě Azure](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. Povolení *azure application gateway analytics* a řešení *Azure Network Security Group Analytics* pomocí procesu popsaného v části Přidat řešení Azure Monitor z Galerie [řešení](solutions.md)
3. Aktualizace všech uložených dotazů, řídicích panelů nebo výstrah za účelem použití nového datového typu
   + Typ je AzureDiagnostics. ResourceType můžete použít k filtrování do síťových protokolů Azure.

     | Namísto: | Použít: |
     | --- | --- |
     | NetworkApplicationgateways &#124; kde OperationName=="ApplicationGatewayAccess" | AzureDiagnostics &#124; kde ResourceType=="APPLICATIONGATEWAYS" a OperationName=="ApplicationGatewayAccess" |
     | NetworkApplicationgateways &#124; kde OperationName=="ApplicationGatewayPerformance" | AzureDiagnostics &#124; kde ResourceType=="APPLICATIONGATEWAYS" a OperationName=="ApplicationGatewayPerformance" |
     | Skupiny zabezpečení sítě | AzureDiagnostics &#124; kde ResourceType=="NETWORKSECURITYGROUPS" |

   + Pro každé pole, které má \_příponu \_ \_s, d nebo g v názvu, změňte první znak na malá písmena
   + Pro každé pole, které má \_příponu o v názvu, jsou data rozdělena do jednotlivých polí na základě názvů vnořených polí.
4. Odeberte řešení *Azure Networking Analytics (Zastaralé).*
   + Pokud používáte PowerShell, použijte`Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

Data shromážděná před změnou nejsou v novém řešení viditelná. Můžete pokračovat v dotazování na tato data pomocí starých názvů type a polí.

## <a name="troubleshooting"></a>Řešení potíží
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Další kroky
* Pomocí [dotazů protokolu v Azure Monitoru](../log-query/log-query-overview.md) zobrazte podrobná diagnostická data Azure.
