---
title: Nejčastější dotazy (nejčastější dotazy) týkající se azure network watcheru | Dokumenty společnosti Microsoft
description: Tento článek odpovídá na nejčastější dotazy týkající se služby Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2019
ms.author: damendo
ms.openlocfilehash: b48aab918b477f5c689a50ca476b0b1336642f0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471852"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Nejčastější dotazy (nejčastější dotazy) týkající se Azure Network Watcher
Služba [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) poskytuje sadu nástrojů pro monitorování, diagnostiku, zobrazení metrik a povolení nebo zakázání protokolů pro prostředky ve virtuální síti Azure. Tento článek odpovídá na běžné otázky týkající se služby.

## <a name="general"></a>Obecné

### <a name="what-is-network-watcher"></a>Co je Network Watcher?
Sledování sítě je navrženo tak, aby monitorovalo a opravovalo stav sítě součástí IaaS (Infrastructure-as-a-Service), které zahrnují virtuální počítače, virtuální sítě, aplikační brány, nástroje pro vyrovnávání zatížení a další prostředky ve virtuální síti Azure. Není to řešení pro monitorování infrastruktury PaaS (Platform-as-a-Service) nebo získání webové / mobilní analýzy.

### <a name="what-tools-does-network-watcher-provide"></a>Jaké nástroje poskytuje sledování sítě?
Network Watcher poskytuje tři hlavní sady funkcí
* Monitorování
  * [Zobrazení topologie](https://docs.microsoft.com/azure/network-watcher/view-network-topology) zobrazuje prostředky ve virtuální síti a vztahy mezi nimi.
  * [Sledování připojení](https://docs.microsoft.com/azure/network-watcher/connection-monitor) umožňuje sledovat připojení a latenci mezi virtuálním virtuálním serverem a jiným síťovým prostředkem.
  * [Sledování výkonu sítě](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor) umožňuje monitorovat připojení a latence napříč hybridními síťovými architekturami, okruhy Expressroute a koncovými body služby/aplikace.  
* Diagnostika
  * [Ověření toku IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) umožňuje zjistit problémy s filtrováním provozu na úrovni virtuálního počítačů.
  * [Další směrování](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) pomáhá ověřit dopravní trasy a zjistit problémy se směrováním.
  * [Poradce při potížích s připojením](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal) umožňuje jednorázovou kontrolu připojení a latence mezi virtuálním virtuálním serverem a jiným síťovým prostředkem.
  * [Snímání paketů](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) umožňuje zachytit veškerý provoz na virtuálním počítači ve vaší virtuální síti.
  * [Řešení potíží s VPN](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview) spouští několik diagnostických kontrol na vašich branách VPN a připojeních, které pomáhají ladit problémy.
* protokolování
  * [Protokoly toku nsg](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) umožňuje protokolovat veškerý provoz ve [skupinách zabezpečení sítě (NSGs)](https://docs.microsoft.com/azure/virtual-network/security-overview)
  * [Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) zpracovává vaše data nsg flow log, která vám umožní vizualizovat, dotazovat, analyzovat a pochopit váš síťový provoz.


Podrobnější informace naleznete na [stránce přehled u sledování sítě](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).


### <a name="how-does-network-watcher-pricing-work"></a>Jak fungují ceny sledování sítě?
Navštivte [stránku Ceny](https://azure.microsoft.com/pricing/details/network-watcher/) pro součásti Sledování sítě a jejich ceny.

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>Ve kterých oblastech je sledovací modul sítě podporován/k dispozici?
Nejnovější dostupnost v regionu si můžete zobrazit na [stránce dostupnosti služby Azure.](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)

### <a name="which-permissions-are-needed-to-use-network-watcher"></a>Jaká oprávnění jsou potřebná k použití sledovacího programu sítě?
Podívejte se na seznam [oprávnění RBAC potřebných k použití sledovacího programu sítě](https://docs.microsoft.com/azure/network-watcher/required-rbac-permissions). Pro nasazení prostředků potřebujete oprávnění přispěvatele k NetworkWatcherRG (viz níže).

### <a name="how-do-i-enable-network-watcher"></a>Jak povolit službu Network Watcher?
Služba Sledování sítě je [povolena automaticky](https://azure.microsoft.com/updates/azure-network-watcher-will-be-enabled-by-default-for-subscriptions-containing-virtual-networks/) pro každé předplatné.

### <a name="what-is-the-network-watcher-deployment-model"></a>Co je model nasazení sledování sítě?
Nadřazený prostředek Sledování sítě je nasazen s jedinečnou instancí v každé oblasti. Formát pojmenování: NetworkWatcher_RegionName. Příklad: NetworkWatcher_centralus je prostředek sledování sítě pro oblast "Centrální USA".

### <a name="what-is-the-networkwatcherrg"></a>Co je NetworkWatcherRG?
Prostředky Network Watcher jsou umístěny ve skryté skupině prostředků **NetworkWatcherRG,** která je vytvořena automaticky. Například prostředek nsg toku protokoly je podřízený prostředek sledování sítě a je povolena v NetworkWatcherRG.

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>Proč je nutné nainstalovat rozšíření Sledování sítě? 
Rozšíření Sledování sítě je vyžadováno pro všechny funkce, které potřebuje generovat nebo zachytit provoz z virtuálního soudu. 

### <a name="which-features-require-the-network-watcher-extension"></a>Které funkce vyžadují rozšíření Sledování sítě?
Funkce Zachytávání paketů, Poradce při potížích s připojením a Sledování připojení potřebují k dispozici rozšíření Sledování sítě.

### <a name="what-are-resource-limits-on-network-watcher"></a>Co jsou omezení prostředků pro sledování sítě?
Všechny limity najdete na stránce [Omezení služeb.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#network-watcher-limits)  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>Proč je povolena pouze jedna instance sledovacího programu sítě pro oblast? 
Network Watcher stačí být povolena jednou pro předplatné pro jeho funkce do práce, to není limit služby.

### <a name="how-can-i-manage-the-network-watcher-resource"></a>Jak lze spravovat prostředek sledování sítě? 
Prostředek sledování sítě představuje back-endovou službu pro sledování sítě a je plně spravován Azure. Zákazníci ji nemusí spravovat. Operace, jako je přesunutí nejsou podporovány na prostředek. Prostředek však [lze odstranit](https://docs.microsoft.com/azure/network-watcher/network-watcher-create#delete-a-network-watcher-in-the-portal). 

## <a name="nsg-flow-logs"></a>Protokoly toku nsg

### <a name="what-does-nsg-flow-logs-do"></a>K čemu se dělají protokoly toku NSG?
Síťové prostředky Azure lze kombinovat a spravovat prostřednictvím [skupin zabezpečení sítě (NSGs).](https://docs.microsoft.com/azure/virtual-network/security-overview) Protokoly toku nsg umožňují protokolovat 5 n-tice toku toku informace o veškerý provoz prostřednictvím nsG. Nezpracovaná tok protokoly jsou zapsány do účtu úložiště Azure, odkud je lze dále zpracovat, analyzovat, dotazovat nebo exportovat podle potřeby.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-firewall"></a>Jak se používají protokoly toku NSG s účtem úložiště za bránou firewall?

Chcete-li použít účet úložiště za bránou firewall, musíte pro přístup k účtu úložiště poskytnout služby Trusted Microsoft Services:

* Přejděte na účet úložiště zadáním názvu účtu úložiště do globálního vyhledávání na portálu nebo na [stránce Účty úložiště.](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)
* V části **NASTAVENÍ** vyberte **Brány firewall a virtuální sítě**.
* V povolit přístup z vyberte **Vybrané sítě**. Potom v části **Výjimky**zaškrtněte políčko vedle **možnosti Povolit důvěryhodným službám Microsoftu přístup k tomuto účtu úložiště.** 
* Pokud je tato možnost již vybraná, není potřeba provádět žádné změny.  
* Vyhledejte cílovou skupinu nsg na [stránce přehledu protokolů toku skupiny NSG](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) a povolte protokoly toku skupiny NSG s vybraným výše uvedeným účtem úložiště.

Po několika minutách můžete zkontrolovat protokoly úložiště, ve kterých by se mělo zobrazit aktualizované časové razítko nebo nově vytvořený soubor JSON.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-service-endpoint"></a>Jak se používají protokoly toku NSG s účtem úložiště za koncovým bodem služby?

Protokoly toku nsg jsou kompatibilní s koncovými body služby bez nutnosti jakékoli další konfigurace. Přečtěte si [kurz o povolení koncových bodů služby](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint) ve vaší virtuální síti.


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>Jaký je rozdíl mezi protokoly toku verze 1 & 2?
Protokoly toku verze 2 zavádí koncept *stavu toku* & ukládá informace o přenesených bajtů a paketů. [Přečtěte si další informace](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file).

## <a name="next-steps"></a>Další kroky
 - Přejdete na naši [stránku s přehledem dokumentace](https://docs.microsoft.com/azure/network-watcher/) pro některé výukové programy, které vám pomohou začít s Programem Sledování sítě.
