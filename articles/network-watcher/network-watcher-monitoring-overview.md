---
title: Úvod do sledovací proces sítě Azure | Microsoft Docs
description: Tato stránka obsahuje přehled služby sledovací proces sítě pro monitorování a detekční síť připojená prostředky ve službě Azure
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: 14bc2266-99e3-42a2-8d19-bd7257fec35e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: jdial
ms.openlocfilehash: a546296749ba9373355cfe2b857b83d8af94d5a1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="azure-network-monitoring-overview"></a>Přehled monitorování sítě Azure

Zákazníci vytvářet síť začátku do konce v Azure tak, že orchestruje a skládání různé jednotlivých síťovým prostředkům, například virtuální síť, ExpressRoute, aplikační bránu, nástroje pro vyrovnávání zatížení a další. Monitorování je k dispozici na všech síťových prostředků. Označujeme jako sledování na úrovni prostředků toto monitorování.

Koncová sítě může mít komplexní konfigurace a interakce mezi prostředky, vytváření komplexní scénáře, které je třeba na základě scénáře monitorování prostřednictvím sledovací proces sítě.

Tento článek popisuje scénář a sledování na úrovni prostředků. Monitorování sítě v Azure je komplexní a zahrnuje dvě rozsáhlé kategorie:

* [**Sledovací proces sítě** ](#network-watcher) – na základě scénáře monitorování se poskytuje s funkcí v sledovací proces sítě. Tato služba obsahuje zachytáváním paketů, další směrování, IP tok ověření, zobrazení skupiny zabezpečení, tok protokolů NSG. Scénář úrovně monitorování poskytuje pohledu koncové síťových prostředků, na rozdíl od monitorování jednotlivých síťových prostředků.
* [**Sledování prostředků** ](#network-resource-level-monitoring) -sledování na úrovni prostředků se skládá ze čtyř funkce, diagnostické protokoly, metriky, řešení potíží s a stav prostředků. Všechny tyto funkce jsou postavené na úrovni prostředků sítě.

## <a name="network-watcher"></a>Network Watcher

Sledovací proces sítě je místní služba, která umožňuje sledovat a diagnostikovat podmínky na úrovni scénář sítě, do a z Azure. Diagnostika sítě a k dispozici sledovací proces sítě vizualizace nástroje vám pomůžou pochopit, diagnostikovat a získáte přehled o k síti v Azure.

Sledovací proces sítě aktuálně má následující možnosti:

* **[Topologie](network-watcher-topology-overview.md)**  -poskytuje zobrazení úrovně sítě zobrazuje různé propojení a přidružení mezi síťovým prostředkům ve skupině prostředků.
* **[Proměnné zachytáváním paketů](network-watcher-packet-capture-overview.md)**  -zaznamená data paketů do/z virtuálního počítače. Pokročilé možnosti filtrování a podrobně nastavit ovládací prvky, jako je například moct nastavit čas a velikost omezení poskytují univerzálnost. Paketu data mohou být uložena v úložišti objektů blob nebo na místní disk ve formátu CAP.
* **[Ověřte IP toku](network-watcher-ip-flow-verify-overview.md)**  – ověří, zda je paket povolený nebo zakázaný na základě toku informace 5 řazené kolekce členů paketu parametrů (cílovou IP adresu, zdrojové IP adresy, cílový Port, zdrojový Port a protokol). Pokud paketu je zakázané skupiny zabezpečení, je vrácena pravidlo a skupiny, který odepřen paketu.
* **[Další směrování](network-watcher-next-hop-overview.md)**  -určí další segment pro pakety směrovány v prostředcích infrastruktury sítě Azure umožňuje diagnostikovat žádné špatně nakonfigurovaný trasy definované uživatelem.
* **[Zobrazení skupiny zabezpečení](network-watcher-security-group-view-overview.md)**  -získá zabezpečení efektivní a použitých pravidel, která se použijí na virtuálním počítači.
* **[Protokolování toku NSG](network-watcher-nsg-flow-logging-overview.md)**  -toku protokoly pro skupinu zabezpečení sítě umožňují zaznamenat protokoly související s přenosy, které jsou povolené nebo zakázané pravidla zabezpečení ve skupině. Tok je definována informací o 5-n-tice – zdrojové adresy IP, cílovou IP adresu, zdrojový Port, cílový Port a protokol.
* **[Brána virtuální sítě a řešení potíží s připojení](network-watcher-troubleshoot-manage-rest.md)**  -poskytuje možnost Poradce při potížích brány virtuální sítě a připojení.
* **[Sítě limity předplatného](#network-subscription-limits)**  -umožňuje zobrazit využití prostředků sítě proti omezení.
* **[Konfigurace protokolu diagnostiky](#diagnostic-logs)**  – poskytuje k povolení nebo zakázání diagnostické protokoly pro síťové prostředky ve skupině prostředků.
* **[Poradce při potížích připojení](network-watcher-connectivity-overview.md)**  -ověří možnost pro přímé připojení TCP z virtuálního počítače do daného rozšíření s Azure kontext koncového bodu.
* **[Monitorování připojení](connection-monitor.md)**  -sledovat problémy latence a konfiguraci mezi virtuální počítač Azure a IP adresu, pomocí zdrojové a cílové IP adresy a portu.

### <a name="role-based-access-control-rbac-in-network-watcher"></a>Řízení přístupu na základě role (RBAC) v sledovací proces sítě

Sítě používá sledovacích procesů [model řízení řízení přístupu (RBAC)](../role-based-access-control/overview.md). Sledovací proces sítě vyžadují následující oprávnění. Je důležité se ujistit, že role pro inicializaci rozhraní API sledovací proces sítě nebo pomocí sledovací proces sítě z portálu nemá požadovaný přístup.

|Prostředek| Oprávnění|
|---|---| 
|Microsoft.Storage/ |Čtení|
|Microsoft.Authorization/| Čtení| 
|Microsoft.Resources/subscriptions/resourceGroups/| Čtení|
|Microsoft.Storage/storageAccounts/listServiceSas/ | Akce|
|Microsoft.Storage/storageAccounts/listAccountSas/ |Akce|
|Microsoft.Storage/storageAccounts/listKeys/ | Akce|
|Microsoft.Compute/virtualMachines/ |Čtení|
|Microsoft.Compute/virtualMachines/ |Zápis|
|Microsoft.Compute/virtualMachineScaleSets/ |Čtení|
|Microsoft.Compute/virtualMachineScaleSets/ |Zápis|
|Microsoft.Network/networkWatchers/packetCaptures/ |Čtení|
|Microsoft.Network/networkWatchers/packetCaptures/| Zápis|
|Microsoft.Network/networkWatchers/packetCaptures/| Odstranění|
|Microsoft.Network/networkWatchers/ |Zápis |
|Microsoft.Network/networkWatchers/| Čtení |
|Microsoft.Insights/alertRules/ |*|
|Microsoft.Support/ | *|

### <a name="network-subscription-limits"></a>Limity předplatného sítě

Limity předplatného sítě poskytují podrobnosti o využití jednotlivých síťových prostředků v předplatném v oblasti pro maximální počet prostředků, které jsou k dispozici.

![limitu předplatného pro sítě][nsl]

## <a name="network-resource-level-monitoring"></a>Úrovně monitorování sítě prostředků

Následující funkce jsou k dispozici pro monitorování na úrovni prostředků:

### <a name="audit-log"></a>Protokol auditu

Operace provedené v rámci konfigurace sítí přihlášeni. Tyto protokoly můžete zobrazit na portálu Azure nebo pomocí nástroje Microsoft, jako je Power BI nebo nástroje třetích stran. Protokoly auditu jsou k dispozici prostřednictvím portálu, prostředí PowerShell, rozhraní příkazového řádku a Rest API. Další informace o protokolů auditu najdete v tématu [auditovat operace s Resource Managerem](../resource-group-audit.md)

Protokoly auditu jsou k dispozici pro operace udělat na všechny síťové prostředky.

### <a name="metrics"></a>Metriky

Metriky jsou měření výkonu a čítače, které jsou shromážděny prostřednictvím v časovém intervalu. Metriky jsou aktuálně dostupné pro službu Application Gateway. Metriky lze použít k aktivaci výstrahy podle prahovou hodnotu. V tématu [Application Diagnostics brány](../application-gateway/application-gateway-diagnostics.md) zobrazíte použití metriky pro vytvoření výstrahy.

![zobrazení metriky][metrics]

### <a name="diagnostic-logs"></a>Diagnostické protokoly

Pravidelné a spontánních události jsou vytvořené síťové prostředky a protokolovány v účtech úložiště, odeslané do centra událostí, nebo analýzy protokolů. Tyto protokoly poskytují přehled o stavu prostředku. Tyto protokoly můžete zobrazit v nástrojů, jako je Power BI a analýzy protokolů. Další postup zobrazení diagnostických protokolů najdete [analýzy protokolů](../log-analytics/log-analytics-azure-networking-analytics.md).

Diagnostické protokoly jsou k dispozici pro [nástroj pro vyrovnávání zatížení](../load-balancer/load-balancer-monitor-log.md), [skupin zabezpečení sítě](../virtual-network/virtual-network-nsg-manage-log.md), trasy a [Application Gateway](../application-gateway/application-gateway-diagnostics.md).

Sledovací proces sítě poskytuje že diagnostické protokoly zobrazení. Toto zobrazení obsahuje všechny síťové prostředky, které podporují protokolování diagnostiky. Z tohoto hlediska můžete povolit nebo zakázat síťových prostředků, snadno a rychle.

![Protokoly][logs]

### <a name="troubleshooting"></a>Řešení potíží

Řešení potíží okna, prostředí na portálu, je k dispozici na síťové prostředky dnes diagnostikovat běžné problémy související s jednotlivé zdroje. Toto prostředí je k dispozici pro následující prostředky sítě - ExpressRoute, brána sítě VPN, aplikační bránu, protokoly zabezpečení sítě, trasy, DNS, nástroj pro vyrovnávání zatížení a Traffic Manager. Další informace o odstraňování potíží prostředků úrovně s, najdete [Diagnostikujte a vyřešte problémy s řešení potíží s Azure](https://azure.microsoft.com/blog/azure-troubleshoot-diagonse-resolve-issues/)

![informace o řešení potíží][TS]

### <a name="resource-health"></a>Stav prostředků

Stav síťového prostředku je k dispozici v pravidelných intervalech. Tyto prostředky zahrnují VPN Gateway a tunelového připojení sítě VPN. Stav prostředku je dostupné na portálu Azure. Další informace o stavu prostředků, navštivte [přehled stavu prostředků](../resource-health/resource-health-overview.md)

## <a name="next-steps"></a>Další postup

Po získání informací o sledovací proces sítě, můžete další informace pro:

Provést zachytáváním paketů na vašem virtuálním počítači tak, že navštívíte [zachytáváním paketů proměnné na portálu Azure](network-watcher-packet-capture-manage-portal.md)

Provádění Proaktivní monitorování a diagnostiku pomocí [výstraha zachytáváním paketů](network-watcher-alert-triggered-packet-capture.md).

Zjištění chyb zabezpečení s [analýza zachytáváním paketů s Wireshark](network-watcher-deep-packet-inspection.md), pomocí nástroje s otevřeným zdrojem.

Informace o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.

<!--Image references-->
[TS]: ./media/network-watcher-monitoring-overview/troubleshooting.png
[logs]: ./media/network-watcher-monitoring-overview/logs.png
[metrics]: ./media/network-watcher-monitoring-overview/metrics.png
[nsl]: ./media/network-watcher-monitoring-overview/nsl.png











