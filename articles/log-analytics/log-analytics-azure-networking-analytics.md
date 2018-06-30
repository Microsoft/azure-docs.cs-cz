---
title: Řešení Azure sítě analýzy v Log Analytics | Microsoft Docs
description: Řešení Azure Analytics sítě můžete použít v analýzy protokolů ke kontrole protokolech skupiny zabezpečení sítě Azure a Azure Application Gateway.
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
ms.component: na
ms.openlocfilehash: 8a92bf7b031899ee75fbf2bb2fdfd7dced3bc1ad
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37127888"
---
# <a name="azure-networking-monitoring-solutions-in-log-analytics"></a>Monitorování řešení v analýzy protokolů Azure sítě

Analýzy protokolů nabízí následující řešení pro monitorování vaší sítí:
* Sledování výkonu sítě (NPM) na
 * Monitorování stavu sítě
* Azure Application Gateway analytics ke kontrole
 * Protokoly služby Azure Application Gateway
 * Metriky Azure Application Gateway
* Řešení ke sledování a audit síťové aktivity v síti cloudu
* [Analýza provozu](https://docs.microsoft.com/azure/networking/network-monitoring-overview#traffic-analytics) 
* Analýzy skupin zabezpečení sítě Azure

## <a name="network-performance-monitor-npm"></a>Sledování výkonu sítě (NPM)

[Sledování výkonu sítě](https://docs.microsoft.com/azure/networking/network-monitoring-overview) řešení správy je monitorování řešení, která sleduje stav, dostupnosti a dostupnosti sítě sítě.  Se používá k monitorování připojení mezi:

* veřejný cloud a místní
* datových center a umístění uživatele (firemních pobočkách)
* podsítě hostování různé úrovně víceúrovňových aplikací.

Další informace najdete v tématu [sledování výkonu sítě](https://docs.microsoft.com/azure/networking/network-monitoring-overview).

## <a name="azure-application-gateway-and-network-security-group-analytics"></a>Analýza Azure Application Gateway a skupinu zabezpečení sítě
Použití řešení:
1. Přidat do řešení pro správu k analýze protokolů a
2. Povolte diagnostiku pro přesměrování diagnostiku do pracovního prostoru analýzy protokolů. Není nutné zapsat protokoly do úložiště objektů Blob v Azure.

Diagnostika a odpovídající řešení můžete povolit pro jednoho nebo obou aplikační brány a skupiny zabezpečení sítě.

Pokud nepovolujte protokolování diagnostiky pro konkrétní typ prostředku, ale instalace řešení, jsou prázdné okna řídicí panel pro tento prostředek a zobrazí se chybová zpráva.

> [!NOTE]
> V lednu 2017 podporované způsob odesílání protokolů z brány aplikace a skupiny zabezpečení sítě k analýze protokolů změnit. Pokud se zobrazí **Azure sítě Analytics (nepoužívané)** řešení, najdete [migrace z původního řešení sítě analýzy](#migrating-from-the-old-networking-analytics-solution) kroky je nutné postupovat.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Zkontrolujte podrobnosti kolekce dat sítě Azure
Analýza Azure Application Gateway a řešením pro správu analytics skupinu zabezpečení sítě shromažďování protokolů diagnostiky přímo z Azure Application Gateway a skupiny zabezpečení sítě. Není nutné zapsat protokoly do úložiště objektů Blob v Azure a žádný agent je vyžadována pro shromažďování dat.

Následující tabulka uvádí metody shromažďování dat a další podrobnosti o tom, jak se data shromažďují pro Azure Application Gateway analýzy a analýzy skupinu zabezpečení sítě.

| Platforma | Přímé agenta | Agent systémy Center Operations Manager | Azure | Nástroj Operations Manager vyžaduje? | Dat agenta nástroje Operations Manager odeslána prostřednictvím skupiny pro správu | Četnost shromažďování dat |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |Při zaznamenání |


## <a name="azure-application-gateway-analytics-solution-in-log-analytics"></a>Analýza řešení služby Azure Application Gateway v analýzy protokolů

![Azure Application Gateway Analytics symbol](./media/log-analytics-azure-networking/azure-analytics-symbol.png)

Tyto protokoly jsou podporovány pro Application Gateway:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

Pro Application Gateway jsou podporovány následující metriky: znovu


* propustnost 5 minut

### <a name="install-and-configure-the-solution"></a>Instalace a konfigurace řešení
Použijte následující pokyny k instalaci a konfiguraci řešení analýzy Azure Application Gateway:

1. Povolit řešení Azure Application Gateway analýzy z [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureAppGatewayAnalyticsOMS?tab=Overview) nebo pomocí procesu popsaného v tématu [řešení přidat analýzy protokolů z Galerie řešení](log-analytics-add-solutions.md).
2. Povolte protokolování pro diagnostiku [Application Gateway](../application-gateway/application-gateway-diagnostics.md) chcete monitorovat.

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Povolte diagnostiku Azure Application Gateway na portálu

1. Na portálu Azure přejděte k prostředku aplikační brány k monitorování
2. Vyberte *protokolů diagnostiky* otevřete na následující stránce

   ![bitové kopie prostředku Azure Application Gateway](./media/log-analytics-azure-networking/log-analytics-appgateway-enable-diagnostics01.png)
3. Klikněte na tlačítko *zapněte diagnostiku* otevřete na následující stránce

   ![bitové kopie prostředku Azure Application Gateway](./media/log-analytics-azure-networking/log-analytics-appgateway-enable-diagnostics02.png)
4. Chcete-li zapněte diagnostiku, klikněte na tlačítko *na* pod *stav*
5. Klikněte na zaškrtávací políčko pro *poslat analýzy protokolů*
6. Vyberte existující pracovní prostor analýzy protokolů, nebo vytvořit pracovní prostor
7. Klikněte na zaškrtávací políčko v části **protokolu** pro každý typ protokolu ke shromažďování
8. Klikněte na tlačítko *Uložit* povolení protokolování diagnostiky k analýze protokolů

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>Povolte diagnostiku sítě Azure pomocí prostředí PowerShell

Následující skript prostředí PowerShell poskytuje příklad toho, jak povolit protokolování pro application Gateway diagnostiky.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzureRmApplicationGateway -Name 'ContosoGateway'

Set-AzureRmDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>Použití Azure Application Gateway analytics
![Obrázek analytics dlaždici Azure Application Gateway](./media/log-analytics-azure-networking/log-analytics-appgateway-tile.png)

Po kliknutí **Azure Application Gateway analytics** dlaždici v přehledu, můžete zobrazit souhrny souborů protokolu a přejít k podrobnostem podrobnostech v těchto kategoriích:

* Přístup k aplikaci brány protokolů
  * Chyby klienta a serveru pro službu Application Gateway přístup k protokolům
  * Počet požadavků za hodinu pro každý Application Gateway
  * Neúspěšné požadavky za hodinu pro každý Application Gateway
  * Chyby podle uživatelského agenta pro Application Gateway
* Výkon brány aplikace
  * Stav hostitele pro službu Application Gateway
  * Maximální a 95. percentil pro službu Application Gateway neúspěšné požadavky

![Obrázek panelu analýzy Azure Application Gateway](./media/log-analytics-azure-networking/log-analytics-appgateway01.png)

![Obrázek panelu analýzy Azure Application Gateway](./media/log-analytics-azure-networking/log-analytics-appgateway02.png)

Na **analytics Azure Application Gateway** řídicí panel, zkontrolujte souhrnné informace v jednom z okna a pak klikněte na jednu Chcete-li zobrazit podrobné informace na stránce vyhledávání protokolu.

Na všech stránkách vyhledávání protokolu můžete zobrazit výsledky čas, podrobné výsledky a historii hledání protokolu. Můžete také filtrovat podle omezující vlastnosti výsledky upřesněte.


## <a name="azure-network-security-group-analytics-solution-in-log-analytics"></a>Skupina zabezpečení sítě Azure analytics řešení v analýzy protokolů

![Skupina zabezpečení sítě Azure Analytics symbol](./media/log-analytics-azure-networking/azure-analytics-symbol.png)

> [!NOTE]
> Řešení analýzy skupinu zabezpečení sítě přechází na podpora komunity, protože jeho funkce nahradila [Analýza provozu](../network-watcher/traffic-analytics.md).
> - Řešení je k dispozici v [šablon Azure rychlý Start](https://azure.microsoft.com/resources/templates/oms-azurensg-solution/) a brzy bude k dispozici v Azure Marketplace.
> - Pro stávající zákazníky, kteří již přidán řešení do svého pracovního prostoru ji budou nadále fungovat bez úprav.
> - Microsoft bude podporovat odesílání diagnostických protokolů NSG do pracovního prostoru pomocí nastavení diagnostiky.

Tyto protokoly jsou podporovány pro skupiny zabezpečení sítě:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>Instalace a konfigurace řešení
Použijte následující pokyny k instalaci a konfiguraci řešení analýzy sítě Azure:

1. Povolit řešení analýzy skupinu zabezpečení sítě Azure z [Azure marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureNSGAnalyticsOMS?tab=Overview) nebo pomocí procesu popsaného v tématu [řešení přidat analýzy protokolů z Galerie řešení](log-analytics-add-solutions.md).
2. Povolte protokolování pro diagnostiku [skupinu zabezpečení sítě](../virtual-network/virtual-network-nsg-manage-log.md) prostředky, které chcete monitorovat.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Povolte diagnostiku skupiny zabezpečení sítě Azure na portálu

1. Na portálu Azure přejděte k prostředku skupinu zabezpečení sítě k monitorování
2. Vyberte *protokolů diagnostiky* otevřete na následující stránce

   ![bitové kopie prostředku skupinu zabezpečení sítě Azure](./media/log-analytics-azure-networking/log-analytics-nsg-enable-diagnostics01.png)
3. Klikněte na tlačítko *zapněte diagnostiku* otevřete na následující stránce

   ![bitové kopie prostředku skupinu zabezpečení sítě Azure](./media/log-analytics-azure-networking/log-analytics-nsg-enable-diagnostics02.png)
4. Chcete-li zapněte diagnostiku, klikněte na tlačítko *na* pod *stav*
5. Klikněte na zaškrtávací políčko pro *poslat analýzy protokolů*
6. Vyberte existující pracovní prostor analýzy protokolů, nebo vytvořit pracovní prostor
7. Klikněte na zaškrtávací políčko v části **protokolu** pro každý typ protokolu ke shromažďování
8. Klikněte na tlačítko *Uložit* povolení protokolování diagnostiky k analýze protokolů

### <a name="enable-azure-network-diagnostics-using-powershell"></a>Povolte diagnostiku sítě Azure pomocí prostředí PowerShell

Následující skript prostředí PowerShell představuje příklad, jak povolit protokolování pro skupiny zabezpečení sítě diagnostiky
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzureRmNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzureRmDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Použijte skupinu zabezpečení sítě Azure analytics
Po kliknutí **skupinu zabezpečení sítě Azure analytics** dlaždici v přehledu, můžete zobrazit souhrny souborů protokolu a přejít k podrobnostem podrobnostech v těchto kategoriích:

* Skupina zabezpečení sítě blokované toky
  * Pravidla skupiny zabezpečení sítě s blokované toky
  * Adresy MAC s blokované toky
* Skupina zabezpečení sítě povolené toky
  * Pravidla skupiny zabezpečení sítě s povolenou toky
  * Adresy MAC s povolenou toky

![Obrázek panelu analýzy skupinu zabezpečení sítě Azure](./media/log-analytics-azure-networking/log-analytics-nsg01.png)

![Obrázek panelu analýzy skupinu zabezpečení sítě Azure](./media/log-analytics-azure-networking/log-analytics-nsg02.png)

Na **skupinu zabezpečení sítě Azure analytics** řídicí panel, zkontrolujte souhrnné informace v jednom z okna a pak klikněte na jednu Chcete-li zobrazit podrobné informace na stránce vyhledávání protokolu.

Na všech stránkách vyhledávání protokolu můžete zobrazit výsledky čas, podrobné výsledky a historii hledání protokolu. Můžete také filtrovat podle omezující vlastnosti výsledky upřesněte.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Migrace z původního řešení sítě analýzy
V ledna 2017 podporované způsob odesílání protokolů z Azure Application Gateway a skupiny zabezpečení sítě Azure k Log Analytics změnil. Tyto změny poskytovat následující výhody:
+ Protokoly zapisují přímo k Log Analytics, aniž by bylo nutné použít účet úložiště
+ Menší latenci od času po vygenerování protokoly jim je k dispozici v analýzy protokolů
+ Méně kroků konfigurace
+ Běžný formát pro všechny typy Azure diagnostics

Použití aktualizované řešení:

1. [Konfiguraci diagnostiky k odeslání přímo k Log Analytics z Azure Application Gateway](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [Konfiguraci diagnostiky k odeslání přímo k Log Analytics ze skupin zabezpečení sítě Azure](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. Povolit *Azure Application Gateway Analytics* a *Analytics skupiny zabezpečení sítě Azure* řešení pomocí procesu popsaného v tématu [řešení přidat analýzy protokolů z Galerie řešení](log-analytics-add-solutions.md)
3. Aktualizovat žádné uložené dotazy, řídicí panely nebo výstrahy používat nový datový typ.
  + Typ je AzureDiagnostics. Příkaz ResourceType můžete filtrovat, aby Azure síťových protokolů.

    | Namísto: | Použití: |
    | --- | --- |
    | NetworkApplicationgateways &#124; kde OperationName == "ApplicationGatewayAccess" | AzureDiagnostics &#124; kde ResourceType = "APPLICATIONGATEWAYS" a OperationName == "ApplicationGatewayAccess" |
    | NetworkApplicationgateways &#124; kde OperationName == "ApplicationGatewayPerformance" | AzureDiagnostics &#124; kde ResourceType == "APPLICATIONGATEWAYS" a OperationName = ApplicationGatewayPerformance |
    | Skupin zabezpečení sítě | AzureDiagnostics &#124; kde ResourceType == "Skupin zabezpečení sítě" |

   + Pro každé pole, které má příponu \_s, \_d, nebo \_g v názvu, změna po prvním znaku na malá písmena
   + Pro každé pole, které má příponu \_o název, data je rozdělená do jednotlivých polí na základě názvů vnořená pole.
4. Odeberte *Analytics sítě Azure (nepoužívané)* řešení.
  + Pokud používáte prostředí PowerShell, použijte `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

Data jsou shromažďována předtím, než tato změna není zobrazená v nové řešení. Můžete pokračovat se dotázat na tato data pomocí starého typu a názvy polí.

## <a name="troubleshooting"></a>Řešení potíží
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Další postup
* Použití [přihlásit analýzy protokolů hledání](log-analytics-log-searches.md) zobrazíte podrobné Azure diagnostická data.
