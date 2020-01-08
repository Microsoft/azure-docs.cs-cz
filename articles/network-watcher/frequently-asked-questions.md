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
ms.openlocfilehash: 97fcd3241be6dac81adfa8e17999d92d84abaa19
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647284"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Nejčastější dotazy týkající se Azure Network Watcher
Služba [azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) poskytuje sadu nástrojů pro monitorování, diagnostiku, zobrazení metrik a povolení nebo zakázání protokolů pro prostředky ve službě Azure Virtual Network. Tento článek obsahuje odpovědi na běžné dotazy týkající se služby.

## <a name="general"></a>Obecné

### <a name="what-is-network-watcher"></a>Co je Network Watcher?
Network Watcher je navržený tak, aby sledoval a opravil stav sítě pro součásti IaaS (infrastruktura jako služba), které zahrnují Virtual Machines, virtuální sítě, aplikační brány, nástroje pro vyrovnávání zatížení a další prostředky ve službě Azure Virtual Network. Nejedná se o řešení pro monitorování infrastruktury PaaS (platforma jako služba) nebo k získávání webových a mobilních analýz.

### <a name="what-tools-does-network-watcher-provide"></a>Jaké nástroje Network Watcher poskytují?
Network Watcher poskytuje tři hlavní sady funkcí
* Sledování
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

### <a name="what-are-resource-limits-on-network-watcher"></a>Co jsou omezení prostředků u Network Watcher?
Všechna omezení najdete na stránce [omezení služby](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#network-watcher-limits) .  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>Proč je pro jednotlivé oblasti povolena pouze jedna instance Network Watcher?
Network Watcher pro předplatné, který funguje, je třeba povolit jenom jednou, nejedná se o limit služby.

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>Proč potřebuji nainstalovat rozšíření Network Watcher? 
Pro všechny funkce, které potřebují vygenerovat nebo zachytit provoz z virtuálního počítače, se vyžaduje rozšíření Network Watcher. 

### <a name="which-features-require-the-network-watcher-extension"></a>Které funkce vyžadují rozšíření Network Watcher?
K dispozici je Network Watcher rozšíření pouze zachycení paketů, řešení potíží s připojením a monitorování připojení.

## <a name="nsg-flow-logs"></a>Protokoly toku NSG

### <a name="what-does-nsg-flow-logs-do"></a>Co dělají protokoly toku NSG?
Síťové prostředky Azure je možné kombinovat a spravovat prostřednictvím [skupin zabezpečení sítě (skupin zabezpečení sítě)](https://docs.microsoft.com/azure/virtual-network/security-overview). Protokoly toku NSG umožňují protokolovat informace o toku 5-řazené kolekce členů o všech přenosech prostřednictvím služby skupin zabezpečení sítě. Protokoly nezpracovaných toků se zapisují na účet Azure Storage, ze kterého se dají dál zpracovávat, analyzovat, dotazovat nebo exportovat podle potřeby.

### <a name="are-there-any-caveats-to-using-nsg-flow-logs"></a>Existují nějaká upozornění k používání protokolů toku NSG?
Pro používání protokolů toku NSG nejsou žádné požadavky. Existují však dvě omezení.
- **Ve vaší virtuální síti nesmí být k dispozici koncové body služby**: protokoly toku NSG se generují z agentů ve vašich virtuálních počítačích do účtů úložiště. V současné době ale můžete protokoly generovat jenom přímo do účtů úložiště a nemůžete do vaší virtuální sítě přidat koncový bod služby.

- **Účet úložiště nesmí být branou firewall**: z důvodu interních omezení musí být účty úložiště přístupné prostřednictvím veřejného Internetu, aby mohly s nimi pracovat protokoly NSG Flow. Provoz se pořád směruje přes Azure interně a nebudete mít za sebou poplatky za další výstup.

Pokyny, jak tyto problémy obejít, najdete v dalších dvou otázkách. Obě tato omezení by se měla vyřešit pomocí ledna 2020.

### <a name="how-do-i-use-nsg-flow-logs-with-service-endpoints"></a>Návody používat protokoly toku NSG s koncovými body služby?

*Možnost 1: překonfigurujte protokoly toku NSG k vygenerování pro účet Azure Storage bez koncových bodů virtuální sítě.*

* Vyhledání podsítí s koncovými body:

    - V globálním vyhledávání v horní části webu Azure Portal vyhledejte **Skupiny prostředků**.
    - Přejděte do skupiny prostředků obsahující skupinu zabezpečení sítě, se kterou pracujete.
    - Pomocí druhého rozevíracího seznamu můžete filtrovat podle typu a vybrat **virtuální sítě** .
    - Klikněte na virtuální síť obsahující koncové body služby.
    - V **Nastavení** v levém podokně vyberte **Koncové body služby**.
    - Poznamenejte si podsítě, ve kterých je povolená služba **Microsoft.Storage**.

* Zakázat koncové body služby:

    - Pokračujte tam, kde jste přestali, a v **Nastavení** v levém podokně vyberte **Podsítě**.
    * Klikněte na podsíť obsahující koncové body služby.
    - V části **Koncové body služby** > **Služby** zrušte zaškrtnutí u služby **Microsoft.Storage**.

Po několika minutách můžete zkontrolovat protokoly úložiště, ve kterých by se mělo zobrazit aktualizované časové razítko nebo nově vytvořený soubor JSON.

*Možnost 2: zakázání protokolů toku NSG*

Pokud koncové body služby Microsoft.Storage nezbytně potřebujete, budete muset zakázat protokoly toku NSG.

### <a name="how-do-i-disable-the--firewall-on-my-storage-account"></a>Návody zakázat bránu firewall v mém účtu úložiště?

Tento problém se vyřeší tím, že pro přístup k účtu úložiště povolíte všechny sítě:

* Na [stránce s přehledem protokolů toku NSG](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) vyhledejte příslušnou skupinu zabezpečení sítě a zjistěte název účtu úložiště.
* Zadejte název účtu úložiště do globálního vyhledávání na portálu a přejděte do účtu úložiště.
* V části **NASTAVENÍ** vyberte **Brány firewall a virtuální sítě**.
* Vyberte **Všechny sítě** a uložte nastavení. Pokud je tato možnost již vybraná, není potřeba provádět žádné změny.  

### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>Jaký je rozdíl mezi protokoly toku verze 1 & 2?
Protokoly Flow verze 2 zavádí koncept *stavu toku* , & ukládá informace o odeslaných bajtech a paketech. [Přečtěte si další informace](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file).

## <a name="next-steps"></a>Další kroky
 - Přejděte na stránku s [přehledem](https://docs.microsoft.com/azure/network-watcher/) o naší dokumentaci, kde najdete některé kurzy, které vám pomohou začít s Network Watcher.
