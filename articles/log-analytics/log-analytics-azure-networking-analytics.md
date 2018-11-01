---
title: Řešení Azure Networking Analytics ve službě Log Analytics | Dokumentace Microsoftu
description: Řešení Azure Networking Analytics ve službě Log Analytics můžete použít ke kontrole protokolů skupiny zabezpečení sítě Azure a Azure Application Gateway protokoly.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: ewinner
editor: ''
ms.assetid: 66a3b8a1-6c55-4533-9538-cad60c18f28b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/21/2018
ms.author: richrund
ms.component: ''
ms.openlocfilehash: bc2b93312ed005154aa7d2530fb5493278a15c67
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50418598"
---
# <a name="azure-networking-monitoring-solutions-in-log-analytics"></a>Sítě Azure monitoring řešení v Log Analytics

Log Analytics nabízí následující řešení pro monitorování vaší sítě:
* Network Performance Monitor (NPM) pro
 * Monitorování stavu sítě
* Azure Application Gateway analytics ke kontrole
 * Protokoly služby Azure Application Gateway
 * Metriky Azure Application Gateway
* Řešení pro monitorování a auditu síťové aktivity v síti cloudu
* [Analýza provozu](https://docs.microsoft.com/azure/networking/network-monitoring-overview#traffic-analytics) 
* Analýzy skupin zabezpečení sítě Azure

## <a name="network-performance-monitor-npm"></a>Network Performance Monitor (NPM)

[Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) řešení pro správu je řešení, která sleduje stav, dostupnosti a dosažitelnosti sítí pro monitorování sítě.  Používá se k monitorování připojení mezi:

* Veřejný cloud a místní
* Datová centra a umístění uživatele (firemní pobočky)
* Podsítě poskytující hostování různé úrovně víceúrovňových aplikací.

Další informace najdete v tématu [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview).

## <a name="azure-application-gateway-and-network-security-group-analytics"></a>Analýzy Azure Application Gateway a skupinu zabezpečení sítě
Použití řešení:
1. Přidejte řešení pro správu do služby Log Analytics a
2. Povolte diagnostiku pro přesměrování diagnostiky k pracovnímu prostoru Log Analytics. Není nutné pro zápis protokolů do úložiště objektů Blob v Azure.

Pro jeden nebo oba Application Gateway a skupin zabezpečení sítě můžete povolit diagnostiku a odpovídající řešení.

Pokud nepovolujte protokolování diagnostiky pro konkrétní typ prostředku, ale řešení nainstalovat, oken řídicí panel pro tento prostředek jsou prázdné a zobrazí chybovou zprávu.

> [!NOTE]
> V lednu 2017 změnit podporovaným způsobem odesílání protokolů z brány Application Gateway a skupiny zabezpečení sítě ke službě Log Analytics. Pokud se zobrazí **Azure Networking Analytics (zastaralé)** řešení, najdete [migraci ze staré řešení analýzy sítě](#migrating-from-the-old-networking-analytics-solution) pro je potřeba postupovat podle kroků.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Přečtěte si podrobné informace o shromažďování dat sítě Azure
Azure Application Gateway analytics a řešení pro správu skupinu zabezpečení sítě analytics shromažďovat diagnostické protokoly přímo ze služby Azure Application Gateway a skupiny zabezpečení sítě. Není nutné pro zápis protokolů do úložiště objektů Blob v Azure a vyžaduje pro shromažďování dat se žádný agent.

V následující tabulce jsou uvedeny metody shromažďování dat a další podrobnosti o jak shromažďuje data pro Azure Application Gateway analytics a analýzy skupin zabezpečení sítě.

| Platforma | Přímý agent | Systémy agenta System Center Operations Manager | Azure | Nástroj Operations Manager vyžaduje? | Dat agenta nástroje Operations Manager odeslaná pomocí skupiny pro správu | Četnost shromažďování dat |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |Po přihlášení |


## <a name="azure-application-gateway-analytics-solution-in-log-analytics"></a>Řešení analýzy Azure Application Gateway ve službě Log Analytics

![Azure Application Gateway Analytics symbol](media/log-analytics-azure-networking-analytics/azure-analytics-symbol.png)

Tyto protokoly jsou podporovány pro brány Application Gateway:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

Pro brány Application Gateway se podporují následující metriky: znovu


* propustnost 5 minut

### <a name="install-and-configure-the-solution"></a>Instalace a konfigurace řešení
Pomocí následujících pokynů k instalaci a konfiguraci řešení analýzy Azure Application Gateway:

1. Povolení řešení analýzy Azure Application Gateway z [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureAppGatewayAnalyticsOMS?tab=Overview) nebo pomocí procesu popsaného v [přidání řešení Log Analytics z Galerie řešení](log-analytics-add-solutions.md).
2. Povolení protokolování diagnostiky [brány Application Gateway](../application-gateway/application-gateway-diagnostics.md) chcete monitorovat.

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Povolení diagnostiky Azure Application Gateway na portálu

1. Na webu Azure Portal přejděte k prostředku aplikační brány k monitorování
2. Vyberte *diagnostické protokoly* otevřete následující stránku

   ![Obrázek prostředku Azure Application Gateway](media/log-analytics-azure-networking-analytics/log-analytics-appgateway-enable-diagnostics01.png)
3. Klikněte na tlačítko *zapnout diagnostiku* otevřete následující stránku

   ![Obrázek prostředku Azure Application Gateway](media/log-analytics-azure-networking-analytics/log-analytics-appgateway-enable-diagnostics02.png)
4. Chcete-li zapnout diagnostiku, klikněte na tlačítko *na* pod *stav*
5. Klikněte na zaškrtávací políčko pro *odesílat do Log Analytics*
6. Vyberte existující pracovní prostor Log Analytics nebo vytvořit pracovní prostor
7. Klikněte na zaškrtávací políčko **protokolu** pro každý typ protokolu pro shromažďování
8. Klikněte na tlačítko *Uložit* povolení protokolování diagnostiky ke službě Log Analytics

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>Povolení diagnostiky sítě Azure pomocí Powershellu

Následující skript prostředí PowerShell poskytuje příklad toho, jak povolit diagnostické protokolování pro brány application Gateway.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzureRmApplicationGateway -Name 'ContosoGateway'

Set-AzureRmDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>Pomocí Azure Application Gateway analytics
![Image z Azure Application Gateway analytics dlaždici](media/log-analytics-azure-networking-analytics/log-analytics-appgateway-tile.png)

Po klepnutí **Azure Application Gateway analytics** dlaždici v přehledu, můžete zobrazení souhrnných informací o protokolů a pak přejdete k podrobnostem v těchto kategoriích:

* Application Gateway přístup k protokolům
  * Chyby klienta a serveru pro službu Application Gateway přístup k protokolům
  * Žádosti za hodinu pro každá služba Application Gateway
  * Neúspěšné žádosti za hodinu pro každá služba Application Gateway
  * Chyby podle uživatelského agenta pro brány Application Gateway
* Výkon Application Gateway
  * Stav hostitele pro službu Application Gateway
  * Maximální a 95. percentilu pro službu Application Gateway neúspěšné požadavky

![Obrázek řídicího panelu Azure Application Gateway analytics](media/log-analytics-azure-networking-analytics/log-analytics-appgateway01.png)

![Obrázek řídicího panelu Azure Application Gateway analytics](media/log-analytics-azure-networking-analytics/log-analytics-appgateway02.png)

Na **Azure Application Gateway analytics** řídicí panel, zkontrolujte souhrnné informace u některého okna a klikněte na z nich se má zobrazit podrobné informace na stránce vyhledávání protokolu.

Na žádném z vyhledávací stránky protokolů můžete zobrazit výsledky podle času, podrobné výsledky a historii hledání protokolu. Můžete také filtrovat podle omezujících vlastností můžete zúžit výsledky.


## <a name="azure-network-security-group-analytics-solution-in-log-analytics"></a>Skupiny zabezpečení sítě Azure řešení analýzy ve službě Log Analytics

![Skupiny zabezpečení sítě Azure Analytics symbol](media/log-analytics-azure-networking-analytics/azure-analytics-symbol.png)

> [!NOTE]
> Řešení analýzy skupin zabezpečení sítě se přesouvá do podpory prostřednictvím komunity, od jeho funkce nahradila ji [analýzu provozu](../network-watcher/traffic-analytics.md).
> - Toto řešení je nyní k dispozici v [šablony pro rychlý start Azure](https://azure.microsoft.com/resources/templates/oms-azurensg-solution/) a bude už brzo k dispozici na webu Azure Marketplace.
> - Pro stávající zákazníky, kteří už přidat řešení do svého pracovního prostoru budou dál fungovat beze změny.
> - Microsoft bude nadále podporovat odesílání diagnostických protokolů NSG do pracovního prostoru pomocí nastavení diagnostiky.

Tyto protokoly jsou podporovány pro skupiny zabezpečení sítě:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>Instalace a konfigurace řešení
Pomocí následujících pokynů k instalaci a konfiguraci řešení Azure Networking Analytics:

1. Povolit skupiny zabezpečení sítě Azure analytického řešení z [Azure marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureNSGAnalyticsOMS?tab=Overview) nebo pomocí procesu popsaného v [přidání řešení Log Analytics z Galerie řešení](log-analytics-add-solutions.md).
2. Povolení protokolování diagnostiky [skupinu zabezpečení sítě](../virtual-network/virtual-network-nsg-manage-log.md) prostředků, kterou chcete monitorovat.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Povolení diagnostiky skupiny zabezpečení sítě Azure na portálu

1. Na webu Azure Portal přejděte k prostředku skupiny zabezpečení sítě k monitorování
2. Vyberte *diagnostické protokoly* otevřete následující stránku

   ![Obrázek prostředku, skupiny zabezpečení sítě Azure](media/log-analytics-azure-networking-analytics/log-analytics-nsg-enable-diagnostics01.png)
3. Klikněte na tlačítko *zapnout diagnostiku* otevřete následující stránku

   ![Obrázek prostředku, skupiny zabezpečení sítě Azure](media/log-analytics-azure-networking-analytics/log-analytics-nsg-enable-diagnostics02.png)
4. Chcete-li zapnout diagnostiku, klikněte na tlačítko *na* pod *stav*
5. Klikněte na zaškrtávací políčko pro *odesílat do Log Analytics*
6. Vyberte existující pracovní prostor Log Analytics nebo vytvořit pracovní prostor
7. Klikněte na zaškrtávací políčko **protokolu** pro každý typ protokolu pro shromažďování
8. Klikněte na tlačítko *Uložit* povolení protokolování diagnostiky ke službě Log Analytics

### <a name="enable-azure-network-diagnostics-using-powershell"></a>Povolení diagnostiky sítě Azure pomocí Powershellu

Následující skript Powershellu znázorňuje, jak povolit diagnostické protokolování pro skupiny zabezpečení sítě
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzureRmNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzureRmDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Analýzy používání skupin zabezpečení sítě Azure
Po klepnutí **analýzy skupin zabezpečení sítě Azure** dlaždici v přehledu, můžete zobrazení souhrnných informací o protokolů a pak přejdete k podrobnostem v těchto kategoriích:

* Blokované toky ve skupině zabezpečení sítě
  * Pravidla skupiny zabezpečení sítě s blokovanými toky
  * Adresy MAC s blokovanými toky
* Povolené toky ve skupině zabezpečení sítě
  * Pravidla skupiny zabezpečení sítě s povolenými toky
  * Adresy MAC s povolenými toky

![Obrázek řídicího panelu skupiny zabezpečení sítě Azure analytics](media/log-analytics-azure-networking-analytics/log-analytics-nsg01.png)

![Obrázek řídicího panelu skupiny zabezpečení sítě Azure analytics](media/log-analytics-azure-networking-analytics/log-analytics-nsg02.png)

Na **analýzy skupin zabezpečení sítě Azure** řídicí panel, zkontrolujte souhrnné informace u některého okna a klikněte na z nich se má zobrazit podrobné informace na stránce vyhledávání protokolu.

Na žádném z vyhledávací stránky protokolů můžete zobrazit výsledky podle času, podrobné výsledky a historii hledání protokolu. Můžete také filtrovat podle omezujících vlastností můžete zúžit výsledky.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Migrace ze starého řešení analýzy sítě
V lednu 2017 změnit podporovaným způsobem odesílání protokolů ze služby Azure Application Gateway a skupiny zabezpečení sítě Azure ke službě Log Analytics. Tyto změny poskytují následující výhody:
+ Protokoly se zapisují přímo do Log Analytics bez nutnosti používat účet úložiště
+ Nižší latence od okamžiku, kdy se generují protokoly na ně k dispozici ve službě Log Analytics
+ Méně kroků konfigurace
+ Běžný formát pro všechny typy diagnostiky Azure

Používat aktualizované řešení:

1. [Konfigurovat diagnostiku, která se pošle přímo ke službě Log Analytics z Azure Application Gateway](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [Konfigurovat diagnostiku, která se pošle přímo ke službě Log Analytics ze skupin zabezpečení sítě Azure](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. Povolit *Azure Application Gateway Analytics* a *analýzy skupin zabezpečení sítě Azure* řešení pomocí procesu popsaného v [přidání řešení Log Analytics z Galerie řešení](log-analytics-add-solutions.md)
3. Aktualizovat všechny uložené dotazy, řídicí panely nebo výstrahy k použití nového datového typu
  + Typ je AzureDiagnostics. Elementu ResourceType můžete použít k filtrování síťových protokolů Azure.

    | Namísto: | Použití: |
    | --- | --- |
    | NetworkApplicationgateways &#124; kde OperationName == "ApplicationGatewayAccess" | AzureDiagnostics &#124; kde element ResourceType = "APPLICATIONGATEWAYS" a OperationName == "ApplicationGatewayAccess" |
    | NetworkApplicationgateways &#124; kde OperationName == "ApplicationGatewayPerformance" | AzureDiagnostics &#124; kde element ResourceType == "APPLICATIONGATEWAYS" a OperationName = ApplicationGatewayPerformance |
    | NetworkSecuritygroups | AzureDiagnostics &#124; kde element ResourceType == "NETWORKSECURITYGROUPS" |

   + Pro všechna pole, který má příponu \_s, \_d, nebo \_g v názvu, změňte první znak na malá písmena
   + Pro všechna pole, který má příponu \_o název, data se dělí do jednotlivých polí na základě názvů vnořeného pole.
4. Odeberte *Azure Networking Analytics (zastaralé)* řešení.
  + Pokud používáte PowerShell, použijte `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

Data shromážděná před změna není viditelný v novém řešení. Můžete pokračovat k dotazování na tato data pomocí starého typu a názvy polí.

## <a name="troubleshooting"></a>Řešení potíží
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Další postup
* Použití [prohledávání protokolů v Log Analytics](log-analytics-log-searches.md) zobrazíte podrobné dat diagnostiky Azure.
