---
title: Osvědčené postupy zabezpečení sítě Azure | Microsoft Docs
description: Tento článek obsahuje sadu osvědčené postupy pro použití zabezpečení síťových součástí možnosti Azure.
services: security
documentationcenter: na
author: TomShinder
manager: swadhwa
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: d6d723f40cdc0382fa41a51eb32e7b59f0798627
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="azure-network-security-best-practices"></a>Osvědčené postupy zabezpečení sítě Azure
Microsoft Azure umožňuje připojit virtuální počítače a zařízení k další síťová zařízení tím, že je na virtuálních sítí Azure. Virtuální síť Azure je konstrukce, která umožňuje připojit virtuální síťové karty k virtuální síti a povolit založené na protokolu TCP komunikaci mezi síťových zařízení. Virtuální počítače Azure připojené k virtuální síti Azure může připojit k zařízení na stejné Azure virtuální síti, jinou virtuální sítí Azure, na Internetu nebo dokonce na vlastní místní sítě.

Tento článek popisuje kolekci osvědčené postupy zabezpečení sítě Azure. Tyto doporučené postupy jsou odvozeny od našich zkušeností s prací v síti Azure a prostředí zákazníků, jako sami.

Pro každý osvědčený postup Tento článek vysvětluje:

* Co je osvědčeným postupem
* Proč chcete povolit tento osvědčený postup
* Pokud se nepodaří povolit osvědčený postup, co může být výsledkem
* Možné alternativy doporučené postupy
* Jak můžete informace o povolení osvědčený postup

Tento článek osvědčené postupy zabezpečení sítě Azure je založen na konsenzu stanovisko a možnostech platformy Azure a sady funkcí, které jsou v době, kdy byla zapsána v tomto článku. Názory a technologie v průběhu času mění a v tomto článku budeme je aktualizovat v pravidelných intervalech, aby odrážela tyto změny.

Azure sítě osvědčené postupy zabezpečení popsané v tomto článku patří:

* Logicky segment podsítě
* Řízení chování směrování
* Povolení vynuceného tunelování
* Pomocí virtuálních síťových zařízení
* Nasazení zóny DMZ pro rozdělení na zóny zabezpečení
* Vyhněte se ohrožení k Internetu pomocí vyhrazené linky WAN
* Optimalizovat výkon a provozuschopnost
* Použít globální zátěže
* Zakázat přístup protokolu RDP pro virtuální počítače Azure
* Povolit Azure Security Center
* Rozšíření vašeho datového centra do Azure

## <a name="logically-segment-subnets"></a>Logicky segment podsítě
[Virtuální sítě Azure](https://azure.microsoft.com/documentation/services/virtual-network/) jsou podobné k místní síti ve vaší místní síti. Cílem virtuální síť Azure je vytvoření jedné privátní IP adresy na základě místa sítě ve kterém můžete umístit všechny vaše [virtuální počítače Azure](https://azure.microsoft.com/services/virtual-machines/). Privátní adresní prostory IP adres k dispozici jsou ve A třídy (10.0.0.0/8), třídy B (172.16.0.0/12), a rozsahy adres třídy C (192.168.0.0/16).

Podobně jako do místní, větší adresní prostor by měl segmentovat do podsítí. Můžete použít [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) na základě zásady podsítí k vytvoření podsítě.

Směrování mezi podsítěmi dojde automaticky a není nutné ručně nakonfigurovat směrovacích tabulek. Výchozí nastavení je ale, že neexistují žádná opatření přístup k síti mezi podsítěmi, které vytvoříte ve virtuální síti Azure. Chcete-li vytvořit ovládací prvky pro přístup k síti mezi podsítěmi, budete muset uvést něco mezi podsítěmi.

Jednou z věcí, můžete použít k provedení této úlohy je [skupinu zabezpečení sítě](../virtual-network/virtual-networks-nsg.md) (NSG). Skupiny Nsg jsou jednoduché stavové paketu kontroly zařízení, která používají 5-n-tice (zdrojové IP adresy, zdrojového portu, cílové adresy IP, cílový port a protokol vrstvy 4) přístup k vytvoření, povolit nebo odepřít pravidla pro provoz v síti. Můžete povolit nebo odepřít provoz do a z jednu IP adresu, na a z více IP adres nebo i do a z celé podsítě.

Pomocí skupin Nsg pro řízení přístupu k síti mezi podsítěmi umožňuje umístění prostředky, které patří do stejné zóny zabezpečení nebo role ve svých vlastních podsítích. Například vezměte v úvahu jednoduché 3vrstvé aplikace, které má webovou vrstvu, aplikační vrstvy logiky a databázové vrstvy. Můžete uvést virtuální počítače, které patří do každé z těchto úrovní do svých vlastních podsítích. Pak použijete skupiny Nsg k řízení provozu mezi podsítí:

* Virtuální počítače vrstvy webové můžou jenom iniciovat připojení k počítačům logiku aplikace a můžete akceptovat jenom připojení z Internetu
* Aplikace logiky virtuálních počítačů může spustit pouze připojení s databázové vrstvy a můžete akceptovat jenom připojení z webová vrstva
* Virtuální počítače vrstvy databáze nelze navázat spojení s nic mimo vlastní podsítě a můžete pouze přijmou připojení z vrstvy logiku aplikace

Další informace o skupinách zabezpečení sítě a jak je můžete využít logicky segmentovat virtuálních sítí Azure, najdete v části [co je skupina zabezpečení sítě](../virtual-network/virtual-networks-nsg.md) (NSG).

## <a name="control-routing-behavior"></a>Řízení chování směrování
Při umístění virtuálního počítače na virtuální síť Azure, můžete si všimnout, že se virtuální počítač může připojit k žádným jiným virtuálním počítačem ve stejné virtuální síti Azure, i když jsou ostatní virtuální počítače v různých podsítích. To je možné, protože je kolekce tras systému, které jsou ve výchozím nastavení povolené, umožňujících tento typ komunikace. Tyto výchozí trasy měly virtuální počítače ve stejné virtuální síti Azure k zahájení připojení mezi sebou a s Internetem (pro odchozí komunikace k Internetu jenom).

Výchozí systémové trasy, které jsou užitečné pro mnoho scénářů nasazení, jsou časy, kdy chcete přizpůsobit konfigurace směrování pro vaše nasazení. Toto vlastní nastavení vám umožní nakonfigurovat adresa dalšího směrování k dosažení určitým příjemcům.

Doporučujeme nakonfigurovat trasy definované uživatelem, když nasazujete virtuální síťové zařízení zabezpečení, která je popsána v novější osvědčený postup.

> [!NOTE]
> Trasy definované uživatelem nejsou vyžadovány, a ve většině případů lze použít výchozí systémové trasy.
>
>

Další informace o uživatele definovaná trasy a způsob jejich konfigurace přečíst v článku [co jsou trasy definované uživatelem a předávání IP](../virtual-network/virtual-networks-udr-overview.md).

## <a name="enable-forced-tunneling"></a>Povolení vynuceného tunelování
Abyste lépe pochopili, vynucené tunelování, je vhodné pochopit, jaké "rozdělení tunelování" je.
Nejběžnější příkladu dělené tunelové propojení se seznámili s připojeními VPN typu. Představte si vytvořit připojení VPN z vaší místnosti hotelů k podnikové síti. Toto připojení umožní, že vám přístup k podnikovým prostředkům a veškerá komunikace se k podnikové síti přejděte prostřednictvím tunelu VPN.

Co se stane, když se chcete připojit k prostředkům na Internetu? Pokud je povoleno dělené tunelové propojení, tato připojení přejděte přímo k Internetu a ne prostřednictvím tunelového připojení sítě VPN. Některé odborníky zabezpečení zvažte to představuje potenciální riziko a proto doporučujeme, aby bylo zakázané dělené tunelové propojení a všechna připojení. Připojení, které jsou určené pro připojení, které jsou určené pro podnikové prostředky a Internetu by měl projít tunelového připojení sítě VPN. Výhodou to je, že připojení k Internetu jsou pak vynutit prostřednictvím zabezpečovací zařízení podnikové sítě, které by být v případě, pokud klient VPN není připojený k Internetu mimo tunelového připojení sítě VPN.

Nyní Pojďme navrácení tomto pro virtuální počítače na virtuální síti Azure. Výchozí trasy pro virtuální síť Azure měly virtuální počítače zahájíte provoz do Internetu. To příliš může představovat bezpečnostní riziko, jak tyto odchozí připojení může zvýšit prostor pro útoky virtuálního počítače a útočníci využít.
Z tohoto důvodu doporučujeme, abyste povolili vynucené tunelování na virtuálních počítačích, když máte připojení mezi různými místy mezi vaší virtuální sítě Azure a v místní síti. Mezi místní připojení popsané dál v tomto Azure sítě osvědčené postupy dokumentu.

Pokud nemáte připojení mezi více místy, ujistěte se, můžete využít výhod skupin zabezpečení sítě (popsané výše) nebo Azure virtuální sítě zabezpečovací zařízení (popsáno vedle) aby odchozí připojení k Internetu z vaší virtuální Azure Počítače.

Další informace o vynucené tunelování a jak ji povolit, najdete v části [nakonfigurujte vynucené tunelování prostřednictvím Powershellu a Azure Resource Manager](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md).

## <a name="use-virtual-network-appliances"></a>Pomocí virtuálních síťových zařízení
Při skupiny zabezpečení sítě a směrování definované uživatelem může poskytnout míru zabezpečení sítě v síti a přenosu vrstvy [OSI model](https://en.wikipedia.org/wiki/OSI_model), budou existovat být situace, kdy budete má nebo třeba povolit zabezpečení na vysoké úrovni zásobníku. V takových situacích doporučujeme nasadit virtuální sítě zabezpečovací zařízení poskytovaných Azure partnery.

Síť Azure zabezpečovací zařízení doručovat rozšířené úrovně zabezpečení přes zajišťuje řízení na úrovni sítě. Mezi možnosti zabezpečení sítě poskytované virtuální sítě zabezpečovací zařízení patří:

* Fungující brána firewall může
* Zjišťování neoprávněných vniknutí/narušení prevence
* Ohrožení zabezpečení správy
* Řízení aplikace
* Detekce anomálií založený na síti
* Filtrování webových
* Antivirové
* Botnet ochrany

Pokud potřebujete vyšší úroveň zabezpečení sítě, než lze získat pomocí řízení přístupu na úrovni sítě, pak doporučujeme prozkoumat a nasadit virtuální síť Azure zabezpečovací zařízení.

Další informace o jaké virtuální síť Azure zabezpečovací zařízení jsou k dispozici a o jejich možnosti naleznete [Azure Marketplace](https://azure.microsoft.com/marketplace/) a vyhledejte "zabezpečení" a "zabezpečení sítě."

## <a name="deploy-dmzs-for-security-zoning"></a>Nasazení zóny DMZ pro rozdělení na zóny zabezpečení
DMZ, nebo "hraniční sítě" segment fyzické nebo logické sítě, který poskytuje další vrstvu zabezpečení mezi vaše prostředky a Internetem. Účelem hraniční síti je umístit specializované síťová zařízení řízení přístupu na okraji sítě hraniční sítě tak, aby pouze požadované provoz je povolený za zařízení pro zabezpečení sítě a do virtuální sítě Azure.

Zóny DMZ jsou užitečné, protože sledování, protokolování a vytváření sestav v zařízeních na hranici službě Azure Virtual Network se můžou zaměřit správu řízení přístupu vaší sítě. Tady by obvykle povolit DDoS prevence, zjišťování/narušení prevence vniknutí systémy (ID nebo IP adresy), pravidla brány firewall a zásady, filtrování webových, antimalwarových sítě a další. Zařízení zabezpečení sítě nacházejí mezi Internetu a virtuální sítí Azure a rozhraní na obě sítě.

I když jde o základní koncepci DMZ, existuje mnoho různých návrzích DMZ, například typu back-to-back tři adresami, více adresami a další.

Doporučujeme pro všechny vysoce zabezpečených nasazeních zvažte nasazení DMZ k vylepšení úrovně zabezpečení sítě pro vaše prostředky Azure.

Další informace o zóny DMZ a jak je nasadit v Azure najdete v tématu [cloudové služby Microsoftu a zabezpečení sítě](../best-practices-network-security.md).

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Vyhněte se ohrožení k Internetu pomocí vyhrazené linky WAN
Mnoho organizací rozhodli hybridního IT trasy. V hybridní IT některé prostředky informace společnosti jsou v Azure, zatímco ostatní zůstanou místně. V mnoha případech,, některé součásti služby bude používat v Azure při další součásti zůstanou místní.

V hybridním scénáři IT je obvykle nějaký typ připojení mezi různými místy. To mezi různými místy, připojení umožňuje společnosti své místní sítě připojit k virtuálním sítím Azure. Nejsou k dispozici dvě řešení připojení mezi různými místy:

* Site-to-site VPN
* ExpressRoute

[Site-to-site VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) představuje virtuální privátní připojení mezi místní sítí a virtuální síti Azure. Toto připojení probíhá přes Internet a umožňuje informace "tunel" uvnitř šifrované odkaz mezi vaší sítí a Azure. Site-to-site VPN je zabezpečený, Vyspělá technologie, která byla nasazena podniky všech velikostí pro dekád. Tunelové propojení šifrování se provádí pomocí [režimu tunelového připojení IPsec](https://technet.microsoft.com/library/cc786385.aspx).

Při site-to-site VPN je důvěryhodné, spolehlivým a zavedených technologie, provoz v rámci tunelu procházení Internetu. Kromě toho šířka pásma je relativně omezená na maximálně přibližně 200 MB/s.

Pokud budete potřebovat výjimečných úroveň zabezpečení a výkonu pro připojení mezi různými místy, doporučujeme použít Azure ExpressRoute pro připojení mezi různými místy. ExpressRoute je vyhrazené sítě WAN propojení mezi místní umístění nebo poskytovatele hostingu serveru Exchange. Protože se jedná o telco připojení, vaše data není přenášet přes Internet a proto není viditelné na potenciální rizika spojená s internetovou komunikaci.

Další informace o tom, jak funguje Azure ExpressRoute a postup nasazení najdete v tématu [technický přehled ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="optimize-uptime-and-performance"></a>Optimalizovat výkon a provozuschopnost
Utajení, integrita a dostupnost (c oddílu) tvoří chaloupka dnešní nejvíce míry modelu zabezpečení. Důvěrnost je o šifrování a ochrany osobních údajů, integritu je o tom, jak se, že data se nezmění neoprávněným uživatelem a dostupnosti je o tom, jak se, že jsou autorizované jednotlivce mít přístup k informacím, které mají oprávnění k přístupu. Selhání v některém z těchto oblastí představuje případný průnik v zabezpečení.

Dostupnost můžete představit jako o provozu a výkonu. Pokud služba je vypnutý, není přístupná informace. Pokud je tak nízký, nepoužitelnost data výkonu, můžete považujeme za data, která mají být nedostupné. Proto z hlediska zabezpečení budeme muset udělat ať můžeme zajistěte, aby našich služeb optimální dostupnost a výkon.
Ke zvýšení dostupnosti a výkonu použít metodu oblíbených a efektivní je použití služby Vyrovnávání zatížení. Vyrovnávání zatížení je metoda distribucí síťový provoz mezi servery, které jsou součástí služby. Například pokud máte front-end webové servery jako součást služby, můžete Vyrovnávání zatížení pro distribuci provoz napříč více front-endu webových serverů.

Této distribuce přenosů zvýšíte dostupnost, protože jeden z webových serverů přestane být dostupný, nástroje pro vyrovnávání zatížení zastaví odesílání provozu k danému serveru a přesměruje na servery, které jsou stále online. Vyrovnávání zatížení taky pomáhá výkonu, protože procesor, sítě a paměti, že nároků na obsluhovat požadavky je distribuován do všech zatížení vyrovnáváním servery.

Doporučujeme vám, že nepoužijete Vyrovnávání zatížení, kdykoli je to možné a podle potřeby pro vaše služby. Budete nevyřešíme vhodnost v následujících částech: na úrovni virtuální síť Azure, Azure poskytuje k tři primární možnostech Vyrovnávání zatížení:

* Vyrovnávání zatížení založené na protokolu HTTP
* Externí Vyrovnávání zatížení
* Interní vyrovnávání zatížení

## <a name="http-based-load-balancing"></a>Vyrovnávání zatížení založené na protokolu HTTP
Vyrovnávání zatížení založené na protokolu HTTP základny rozhodnutí o jaké server k odeslání připojení pomocí vlastnosti protokolu HTTP. Azure má Vyrovnávání zatížení HTTP, který prochází podle názvu aplikační brány.

Doporučujeme použít Azure Application Gateway při:

* Aplikace, které vyžadují, aby se požadavky ze stejné uživatelské/klientské relace pokaždé dostaly ke stejnému back-endovému virtuálnímu počítači. Příklady tohoto by nákupního košíku aplikace a webové servery e-mailu.
* Aplikace, které chcete uvolnit farmy webových serverů z ukončení protokolu SSL režie využitím Application Gateway [přesměrování zpracování SSL](https://f5.com/glossary/ssl-offloading) funkce.
* Aplikace, jako je například síť pro doručování obsahu, které vyžadují, aby bylo během jednoho dlouhotrvajícího připojení TCP více požadavků HTTP směrováno nebo směrováno s vyrovnáváním zatížení na různé back-endové servery.

Další informace o tom, jak funguje Azure Application Gateway a jak můžete použít v nasazeních, najdete v části [Přehled brány aplikace](../application-gateway/application-gateway-introduction.md).

## <a name="external-load-balancing"></a>Externí Vyrovnávání zatížení
Vyrovnávání zatížení externí probíhá při příchozí připojení z Internetu jsou mezi vaše servery umístěné v Azure Virtual Network skupinu s vyrovnáváním zatížení. Nástroje pro vyrovnávání zatížení Azure externí můžete zadat, tato funkce a doporučujeme vám, že slouží jako nevyžadují trvalé relace nebo snižování zátěže protokolu SSL.

Vyrovnávání zatížení založené na protokolu HTTP, na rozdíl od externí Vyrovnávání zatížení používá informace v síti a přenosu vrstvy sítě model OSI rozhoduje o jaké server vyrovnávání připojení k načtení.

Doporučujeme použít vyrovnávání zatížení externí vždy, když máte [bezstavové aplikace](http://whatis.techtarget.com/definition/stateless-app) přijímá příchozí žádosti z Internetu.

Další informace o tom, jak funguje Azure externí nástroj pro vyrovnávání zatížení a jak je můžete nasadit, najdete v části [začínáte s vytvářením k Internetu čelí pro vyrovnávání zatížení ve Správci prostředků pomocí prostředí PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="internal-load-balancing"></a>Interní vyrovnávání zatížení
Vyrovnávání zatížení interní je podobná externí zátěže a používá stejný mechanismus načíst vyrovnávání připojení k serverům za ně. Jediným rozdílem je, že nástroj pro vyrovnávání zatížení v tomto případě přijímá připojení z virtuálních počítačů, které nejsou na Internetu. Ve většině případů připojení, která jsou podmínky přijaty ve Vyrovnávání zatížení zahájili zařízení v Azure Virtual Network.

Doporučujeme vám, že používáte interní Vyrovnávání zatížení pro scénáře využívající tato funkce, například když potřebujete načíst vyrovnávání připojení k SQL serverům nebo interní webové servery.

Další informace o tom, jak funguje Azure interní Vyrovnávání zatížení a jak ji nasadit, najdete v části [začínáte s vytvářením interní pro vyrovnávání zatížení pomocí prostředí PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md).

## <a name="use-global-load-balancing"></a>Použít globální zátěže
Díky výpočetní veřejného cloudu, je možné nasadit globálně distribuované aplikace, které mají součásti, které jsou umístěné v datových centrech po celém světě. To je možné v Microsoft Azure z důvodu přítomnosti globální datového centra Azure. Na rozdíl od již bylo zmíněno dříve technologie Vyrovnávání zatížení globální Vyrovnávání zatížení umožňuje zpřístupnit služeb i v případě, že celá datová centra může být k dispozici.

Tento typ globální Vyrovnávání zatížení v Azure a využívají můžete získat [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/). Díky Traffic Manager je možné načíst vyrovnávání připojení k vašim službám podle umístění uživatele.

Například pokud uživatel je vytváření požadavku služby z Evropské unie, připojení se přesměruje k vašim službám umístěný v datacentrum Evropa. Tato část služby Traffic Manager globální načíst vyrovnávání pomáhá zlepšit výkon, protože připojení k nejbližší datového centra je rychlejší než připojení k datových center, které jsou daleko.

Na straně dostupnosti Vyrovnávání zatížení globální zajišťuje, že vaše služba je k dispozici i v případě, že by měl k dispozici celého datového centra.

Například pokud datovém centru Azure by měl k dispozici z prostředí důvodů nebo kvůli výpadku (například selhání k místní síti), připojení k službě by přesměrována na nejbližší online datacenter. Tato služba Vyrovnávání zatížení globální dosahuje využitím DNS zásady, které můžete vytvořit v Traffic Manageru.

Doporučujeme, abyste používali Traffic Manager pro řešení cloudu, které vyvíjíte a které má obor široce distribuované nad několika oblastmi a vyžaduje nejvyšší úroveň provozu možná.

Další informace o Azure Traffic Manager a jak se dá nasadit, najdete v části [co je Traffic Manager](../traffic-manager/traffic-manager-overview.md).

## <a name="disable-rdpssh-access-to-azure-virtual-machines"></a>Zakázat přístup RDP/SSH pro virtuální počítače Azure
Je možné připojit virtuální počítače Azure pomocí [Remote Desktop Protocol](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) a [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) protokoly (SSH). Tyto protokoly umožňují spravovat virtuální počítače ze vzdálených umístění a jsou v datovém centru computing standardní.

Potenciální problém zabezpečení s použitím těchto protokolů přes Internet se útočníci mohou používat různé [hrubou silou](https://en.wikipedia.org/wiki/Brute-force_attack) techniky k získání přístupu k Azure Virtual Machines. Jakmile útočníci získají přístup, mohou použít virtuální počítač jako bod spuštění pro ostatní počítače v Azure Virtual Network narušení nebo i útokům síťová zařízení mimo Azure.

Z toho důvodu doporučujeme zakázat přímý přístup RDP a SSH pro virtuální počítače Azure z Internetu. Po zakázání je přímý přístup RDP a SSH z Internetu, máte další možnosti, které můžete použít pro přístup k těchto virtuálních počítačů pro vzdálenou správu:

* Point-to-site VPN
* Site-to-site VPN
* ExpressRoute

[Point-to-site VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md) je jiný termín pro připojení klienta nebo serveru sítě VPN pro vzdálený přístup. Síť VPN point-to-site umožňuje jenom jednoho konkrétního uživatele pro připojení k virtuální síti Azure přes Internet. Po navázání připojení point-to-site, bude uživatel moci připojit k žádné virtuální počítače nachází ve virtuální síti Azure, které uživatel připojené prostřednictvím point-to-site VPN pomocí protokolu RDP nebo SSH. Předpokladem je, že je uživatel autorizovaný k dosažení těchto virtuálních počítačů.

Point-to-site VPN je bezpečnější než přímé připojení protokolu RDP nebo SSH, protože uživatel nemá k ověření dvakrát před připojením k virtuálnímu počítači. První, uživatel musí ověřit (a autorizaci) k navázání připojení VPN typu point-to-site; druhý, uživatel musí ověřit (a autorizaci) k vytvoření relace protokolu RDP nebo SSH.

A [site-to-site VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) celá síť se připojuje k jiné síti přes Internet. Můžete se připojit k virtuální síti Azure do místní sítě site-to-site VPN. Pokud nasazujete site-to-site VPN, uživatelé na vaší místní síti bude moct připojit k virtuálním počítačům ve vaší virtuální síti Azure pomocí protokolu RDP nebo SSH přes připojení VPN typu site-to-site a nevyžaduje povolit přímý přístup protokolu RDP nebo SSH přes Internet.

Můžete taky vyhrazené propojení WAN nakonfigurovánu podobná site-to-site VPN. Hlavní rozdíly jsou 1. vyhrazené propojení WAN není procházení Internetu a 2. vyhrazené linky WAN jsou obvykle další stabilní a původce. Azure poskytuje řešení vyhrazené propojení WAN ve formě [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="enable-azure-security-center"></a>Povolit Azure Security Center
Azure Security Center pomáhá zabránit, zjistit a reagovat na hrozby a poskytuje že vyšší přehled a kontrolu nad, zabezpečení vašich prostředků Azure. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

Azure Security Center pomáhá optimalizovat a monitorování zabezpečení sítě podle:

* Poskytuje doporučení zabezpečení sítě
* Monitorování stavu konfigurace zabezpečení sítě
* Výstrahy upozorňující na základě hrozeb pro síť i na úrovni koncového bodu a sítě

Důrazně doporučujeme, abyste povolili pro všechna vaše nasazení Azure Azure Security Center.

Další informace o službě Azure Security Center a jak ji povolit pro vaše nasazení najdete v tématu [Úvod do Azure Security Center](../security-center/security-center-intro.md).

## <a name="securely-extend-your-datacenter-into-azure"></a>Bezpečně rozšířit vašeho datového centra do Azure
Mnoho podnikových IT organizace jsou chtějí rozšířit do cloudu místo narůstají jejich místní datacentra. Toto rozšíření představuje rozšíření stávající infrastruktury IT do veřejného cloudu. Využitím mezi různými místy možnosti připojení, je možné považovat za jakékoli jiné podsítě v síťové infrastruktuře místní virtuálních sítí Azure.

Existují však plánování a návrhu problémy, které je třeba vzít v úvahu nejprve. To je obzvláště důležité v oblasti zabezpečení sítě. Jedním z nejlepší způsoby, abyste pochopili, jak postupovat takový návrh je příklad.

Společnost Microsoft vytvořila [Diagram architektury odkaz rozšíření Datacenter](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84#content) a podpůrné vedlejších vám pomohou pochopit, co bude vypadat takové příponu datového centra. To poskytuje příklad implementace odkaz, můžete použít k plánování a návrh příponu zabezpečené enterprise datacentra do cloudu. Doporučujeme, abyste si prošli tohoto dokumentu představu o klíčové komponenty bezpečné řešení.

Další informace o tom, jak bezpečně rozšířit vašeho datového centra do Azure, zobrazit video [rozšíření vašeho Datacentra do Microsoft Azure](https://www.youtube.com/watch?v=Th1oQQCb2KA).
