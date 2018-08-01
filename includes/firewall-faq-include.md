---
title: zahrnout soubor
description: zahrnout soubor
services: firewall
author: vhorne
ms.service: ''
ms.topic: include
ms.date: 7/30/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: e23579479c61810d651bebae7b486b53aaaf0d42
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39361385"
---
### <a name="what-is-azure-firewall"></a>Co je brána Azure Firewall?

Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure. Jedná se o plně stavové brány firewall na-jako službu s integrovanou vysokou dostupnost a škálovatelnost cloudu neomezený. Můžete centrálně vytvářet, vynucovat a protokolovat zásady připojení k aplikacím a sítím napříč různými předplatnými a virtuálními sítěmi. Brány Firewall na Azure je aktuálně ve verzi public preview.

### <a name="what-capabilities-are-supported-in-the-azure-firewall-public-preview-release"></a>Jaké funkce jsou podporované ve verzi public preview Brána Firewall služby Azure?  

* Stavová brána firewall jako služba
* Integrovaná vysoká dostupnost s neomezenou škálovatelností cloudu
* Filtrování FQDN 
* Pravidla filtrování síťového provozu
* Podpora pro odchozí SNAT
* Centrálně vytvoření, vynucení a protokolovat zásady aplikace a síťové připojení mezi virtuálními sítěmi a předplatných Azure
* Plná integrace se službou Azure Monitor zajišťující protokolování a analýzy 

### <a name="how-can-i-join-the-azure-firewall-public-preview"></a>Jak může připojit k Azure bránu Firewall verzi Public Preview?

Brány Firewall na Azure je aktuálně spravované veřejné verzi preview, které se můžete připojit pomocí příkazu Register-AzureRmProviderFeature Powershellu, jak je vysvětleno v dokumentaci veřejné verzi Preview Azure brány Firewall.

### <a name="what-is-the-pricing-for-azure-firewall"></a>Jaké jsou ceny za Firewall služby Azure?

Azure Brána Firewall nemá hradit fixní částku + náklady na proměnnou. Ceny jsou níže, a tyto jsou další snížené o 50 % ve verzi public preview.

* Fixní poplatek: $1.25/firewall/hour
* Proměnlivá částka: $0.03/ GB zpracovaných brány firewall (příchozí nebo odchozí).

### <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Co je typické nasazení modelu pro Brána Firewall služby Azure?

I když je možné nasadit na všechny virtuální sítě bránou Firewall služby Azure, Zákazníci by obvykle nasazení Brána Firewall služby Azure v centrální virtuální síti a vytvořit partnerský vztah jiných virtuálních sítí k němu v modelu Centrum & paprsku. Pak můžete nastavit výchozí trasu z partnerské virtuální sítě tak, aby odkazoval na tento centrální virtuální síti Brána Firewall.

### <a name="how-can-i-install-the-azure-firewall"></a>Jak nainstalovat Azure bránu Firewall?

Azure brány Firewall můžete nastavit prostřednictvím webu Azure portal, Powershellu, rozhraní REST API nebo šablony. Zobrazit [kurz: nasazení a konfiguraci brány Firewall Azure pomocí webu Azure portal](../articles/firewall/tutorial-firewall-deploy-portal.md) podrobné pokyny.

### <a name="what-are-some-azure-firewall-concepts"></a>Jaké jsou některé pojmy brány Firewall Azure?

Azure Brána Firewall podporuje pravidel a kolekcí pravidel. Kolekce pravidel je sada pravidel, která sdílí stejné pořadí a priority. Kolekce pravidel jsou provedeny v pořadí podle jejich priority, kolekcí pravidel sítě mají vyšší prioritu než kolekcí pravidel aplikace, všechna pravidla se ukončuje.
Existují dva typy kolekcí pravidel:

* Pravidla aplikace: můžete nakonfigurovat plně kvalifikované názvy domény (FQDN), které mohou být přístupné z podsítě. 
* Pravidla síťových: umožňuje konfigurovat pravidla obsahující zdrojové adresy, protokolu, cílový port a cílovou adresu. 

### <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Brána Firewall služby Azure podporuje filtrování příchozího provozu?

Azure brány Firewall ve verzi public preview podporuje jenom odchozí filtrování. Příchozí ochranu pro protokoly než HTTP/S (ex: protokol RDP, SSH, FTP) nezávazně plánujeme přidat obecná dostupnost Azure bránu Firewall  
 
### <a name="what-logginganalytics-is-supported-by-the-azure-firewall"></a>Jaké protokolování a analýzy podporuje Azure bránu Firewall?

Brány Firewall na Azure je integrovaná se službou Azure Monitor pro zobrazení a analýza protokolů brány Firewall. Protokoly můžete odeslat do Log Analytics, Azure Storage nebo centra událostí. Mohou být analyzovány v Log Analytics, nebo prostřednictvím různých nástrojů, jako je Excel a Power BI. Naleznete v tématu [kurz: Brána Firewall služby Azure Monitor protokoly](../articles/firewall/tutorial-diagnostics.md) další podrobnosti.

### <a name="how-does-azure-firewall-work-relative-to-existing-like-nvas-in-the-marketplace"></a>Jak funguje brána Firewall služby Azure vzhledem ke stávající jako síťová virtuální zařízení na webu Marketplace?

Brány Firewall na Azure je služba základní brána firewall, která může vyřešit určitých scénářů zákazníků. Očekává se, zda bude mít kombinaci síťových virtuálních zařízení a Brána Firewall služby Azure třetích stran. Lepší spolupráci se základní prioritou.
 
### <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Jaký je rozdíl mezi waf služby Application Gateway a Brána Firewall služby Azure?

Firewall webových aplikací (WAF) je funkce služby Application Gateway poskytující centralizovanou ochranu příchozí webových aplikací před běžným zneužitím a ohrožení zabezpečení. Brána Firewall Azure poskytuje odchozí síťové úroveň ochrany pro všechny porty a protokoly a úroveň ochrany aplikací pro odchozí HTTP/S. Příchozí ochranu pro protokoly než HTTP/S (například RDP, SSH, FTP) je vyhledán nezávazně plánované pro zavedení všeobecné dostupnosti Brána Firewall služby Azure

### <a name="what-is-the-difference-between-network-security-groups-nsg-and-azure-firewall"></a>Jaký je rozdíl mezi skupiny zabezpečení sítě (NSG) a Brána Firewall služby Azure?

Brána Firewall služby Azure service doplňuje funkce Skupina zabezpečení sítě a současně poskytuje lepší zabezpečení sítě v obrany. Skupiny zabezpečení sítě poskytují distribuované síťový provoz vrstvy filtrování pro omezení provozu směřujícího do prostředků v rámci virtuálních sítí v každém předplatném.  Azure bránu Firewall je plně stavové a centralizované síťové brány firewall jako služba, poskytuje úroveň ochrany sítě a aplikace v různých předplatných a virtuální sítí (VNets). 

### <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Jak nastavit Brána Firewall služby Azure se Moje koncové body služby?

Zabezpečený přístup ke službám PaaS doporučujeme koncových bodů služby. Zákazníci Azure brány Firewall můžete povolit koncové body služby v podsíti brány Firewall na Azure a zakázat v propojených virtuálních sítích paprsků pro využívání obě funkce – zabezpečení koncového bodu služby a centrálního protokolování pro veškerý provoz.

### <a name="what-are-the-known-service-limits"></a>Jaká jsou omezení služeb?

* Azure brána firewall nemá doporučeného limitu pro 1000 TB/brána firewall/měsíc. 
* Azure bránu firewall, na kterém běží v centrální virtuální síti se vztahují omezení partnerských vztahů virtuálních sítí: maximální velikost 50 virtuálních sítí paprsků.  
* Brány Firewall Azure nefunguje s globální partnerský vztah, takže byste měli mít minimálně jedno nasazení brány firewall v jedné oblasti.
* Pravidla brány firewall Azure podporuje 10 tisíc aplikací a 10 tisíc pravidel sítě.