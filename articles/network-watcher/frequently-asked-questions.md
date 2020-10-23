---
title: Nejčastější dotazy týkající se Azure Network Watcher | Microsoft Docs
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se služby Azure Network Watcher.
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
ms.openlocfilehash: 4ba35d91d286cb43a763887d104e21ae0d537c8e
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424123"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Nejčastější dotazy týkající se Azure Network Watcher
Služba [azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) poskytuje sadu nástrojů pro monitorování, diagnostiku, zobrazení metrik a povolení nebo zakázání protokolů pro prostředky ve službě Azure Virtual Network. Tento článek obsahuje odpovědi na běžné dotazy týkající se služby.

## <a name="general"></a>Obecné

### <a name="what-is-network-watcher"></a>Co je Network Watcher?
Network Watcher je navržený tak, aby sledoval a opravil stav sítě pro součásti IaaS (infrastruktura jako služba), které zahrnují Virtual Machines, virtuální sítě, aplikační brány, nástroje pro vyrovnávání zatížení a další prostředky ve službě Azure Virtual Network. Nejedná se o řešení pro monitorování infrastruktury PaaS (platforma jako služba) nebo k získávání webových a mobilních analýz.

### <a name="what-tools-does-network-watcher-provide"></a>Jaké nástroje Network Watcher poskytují?
Network Watcher poskytuje tři hlavní sady funkcí
* Monitorování
  * [Zobrazení topologie](https://docs.microsoft.com/azure/network-watcher/view-network-topology) zobrazuje prostředky ve virtuální síti a vztahy mezi nimi.
  * [Monitor připojení](https://docs.microsoft.com/azure/network-watcher/connection-monitor) umožňuje monitorovat připojení a latenci mezi virtuálním počítačem a jiným síťovým prostředkem.
  * [Nástroj Sledování výkonu sítě](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor) umožňuje monitorovat připojení a latence napříč hybridními síťovými architekturami, okruhy ExpressRoute a koncovými body služby nebo aplikace.  
* Diagnostika
  * [Ověření toku protokolu IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) umožňuje detekovat problémy s filtrováním provozu na úrovni virtuálního počítače.
  * [Další segment směrování](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) vám pomůže ověřit trasy provozu a zjišťovat problémy s směrováním.
  * [Řešení potíží s připojením](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal) umožňuje jednorázové připojení a kontrolu latence mezi virtuálním počítačem a jiným síťovým prostředkem.
  * [Zachytávání paketů](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) umožňuje zachytit veškerý provoz virtuálního počítače ve vaší virtuální síti.
  * [Řešení potíží s VPN](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview) spouští více diagnostických kontrol bran a připojení VPN, které vám pomůžou s laděním.
* Protokolování
  * [Protokoly toku NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) vám umožňují protokolovat veškerý provoz ve [skupinách zabezpečení sítě (skupin zabezpečení sítě)](https://docs.microsoft.com/azure/virtual-network/security-overview) .
  * [Analýza provozu](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) zpracovává data protokolu toku NSG, což umožňuje vizualizovat, dotazovat, analyzovat a pochopit síťový provoz.


Podrobnější informace najdete na stránce s [přehledem Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).


### <a name="how-does-network-watcher-pricing-work"></a>Jak funguje Network Watcher ceny?
Na [stránce s cenami](https://azure.microsoft.com/pricing/details/network-watcher/) najdete Network Watcher komponenty a jejich ceny.

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>Které oblasti jsou Network Watcher podporovány/k dispozici v?
Nejnovější regionální dostupnost najdete na [stránce dostupnosti služby Azure](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher) .

### <a name="which-permissions-are-needed-to-use-network-watcher"></a>Která oprávnění jsou nutná k použití Network Watcher?
Podívejte se na seznam [oprávnění Azure RBAC potřebných k použití Network Watcher](https://docs.microsoft.com/azure/network-watcher/required-rbac-permissions). Pro nasazení prostředků potřebujete oprávnění přispěvatele k NetworkWatcherRG (viz níže).

### <a name="how-do-i-enable-network-watcher"></a>Jak povolit službu Network Watcher?
Služba Network Watcher je [automaticky povolená](https://azure.microsoft.com/updates/azure-network-watcher-will-be-enabled-by-default-for-subscriptions-containing-virtual-networks/) pro každé předplatné.

### <a name="what-is-the-network-watcher-deployment-model"></a>Co je model nasazení Network Watcher?
Nadřazený prostředek Network Watcher je nasazený s jedinečnou instancí v každé oblasti. Formát názvů: NetworkWatcher_RegionName. Příklad: NetworkWatcher_centralus je Network Watcher prostředek pro oblast "Střed USA".

### <a name="what-is-the-networkwatcherrg"></a>Co je NetworkWatcherRG?
Prostředky Network Watcher se nacházejí ve skryté skupině prostředků **NetworkWatcherRG** , která se vytváří automaticky. Například prostředek NSG Flow log je podřízeným prostředkem Network Watcher a je povolen v NetworkWatcherRG.

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>Proč potřebuji nainstalovat rozšíření Network Watcher? 
Pro všechny funkce, které potřebují vygenerovat nebo zachytit provoz z virtuálního počítače, se vyžaduje rozšíření Network Watcher. 

### <a name="which-features-require-the-network-watcher-extension"></a>Které funkce vyžadují rozšíření Network Watcher?
Funkce zachytávání paketů, řešení potíží s připojením a monitorování připojení vyžadují, aby bylo k dispozici rozšíření Network Watcher.

### <a name="what-are-resource-limits-on-network-watcher"></a>Co jsou omezení prostředků u Network Watcher?
Všechna omezení najdete na stránce [omezení služby](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#network-watcher-limits) .  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>Proč je pro jednotlivé oblasti povolena pouze jedna instance Network Watcher? 
Network Watcher pro předplatné, který funguje, je třeba povolit jenom jednou, nejedná se o limit služby.

### <a name="how-can-i-manage-the-network-watcher-resource"></a>Jak můžu spravovat prostředek Network Watcher? 
Prostředek Network Watcher představuje back-end službu pro Network Watcher a plně spravuje Azure. Zákazníci je nepotřebují spravovat. Operace, jako je přesun, nejsou u prostředku podporovány. Prostředek je ale [možné odstranit](https://docs.microsoft.com/azure/network-watcher/network-watcher-create#delete-a-network-watcher-in-the-portal). 

## <a name="service-availability-and-redundancy"></a>Dostupnost služby a redundance 

### <a name="is-the-network-watcher-service-zone-resilient"></a>Je zóna služby Network Watcher odolná proti chybám? 
Ano. Služba Network Watcher je ve výchozím nastavení odolná proti zónám. 

### <a name="how-do-i-configure-the-network-watcher-service-to-be-zone-resilient"></a>Návody nakonfigurovat službu Network Watcher, aby byla odolná proti zóně? 
Pro povolení odolnosti zóny není nutná žádná konfigurace zákazníka. V zóně – odolnost pro Network Watcher prostředky je ve výchozím nastavení dostupná a spravovaná samotnými službami. 

## <a name="nsg-flow-logs"></a>Protokoly toku NSG

### <a name="what-does-nsg-flow-logs-do"></a>Co dělají protokoly toku NSG?
Síťové prostředky Azure je možné kombinovat a spravovat prostřednictvím [skupin zabezpečení sítě (skupin zabezpečení sítě)](https://docs.microsoft.com/azure/virtual-network/security-overview). Protokoly toku NSG umožňují protokolovat informace o toku 5-řazené kolekce členů o všech přenosech prostřednictvím služby skupin zabezpečení sítě. Protokoly nezpracovaných toků se zapisují na účet Azure Storage, ze kterého se dají dál zpracovávat, analyzovat, dotazovat nebo exportovat podle potřeby.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-firewall"></a>Návody používat protokoly toku NSG s účtem úložiště za bránou firewall?

Pokud chcete použít účet úložiště za bránou firewall, musíte poskytnout výjimku pro důvěryhodné služby Microsoftu pro přístup k vašemu účtu úložiště:

* Přejděte do účtu úložiště zadáním názvu účtu úložiště do globálního vyhledávání na portálu nebo na [stránce účty úložiště](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts) .
* V části **NASTAVENÍ** vyberte **Brány firewall a virtuální sítě**.
* V části "povolení přístupu z" vyberte **vybrané sítě**. Pak v části **výjimky**zaškrtněte políčko u možnosti **"pro přístup k tomuto účtu úložiště pro důvěryhodné služby Microsoftu"** . 
* Pokud je tato možnost již vybraná, není potřeba provádět žádné změny.  
* Na [stránce Přehled protokolů toků NSG](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) vyhledejte cílovou NSG a povolte protokoly toku NSG s vybraným účtem úložiště.

Po několika minutách můžete zkontrolovat protokoly úložiště, ve kterých by se mělo zobrazit aktualizované časové razítko nebo nově vytvořený soubor JSON.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-service-endpoint"></a>Návody používat protokoly toku NSG s účtem úložiště za koncovým bodem služby?

Protokoly toku NSG jsou kompatibilní s koncovými body služby bez nutnosti jakékoli další konfigurace. Přečtěte si [kurz povolení koncových bodů služby](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint) ve vaší virtuální síti.


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>Jaký je rozdíl mezi protokoly toku verze 1 & 2?
Protokoly Flow verze 2 zavádí koncept *stavu toku* , & ukládá informace o odeslaných bajtech a paketech. [Další informace](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file).

## <a name="next-steps"></a>Další kroky
 - Přejděte na stránku s [přehledem](https://docs.microsoft.com/azure/network-watcher/) o naší dokumentaci, kde najdete některé kurzy, které vám pomohou začít s Network Watcher.
