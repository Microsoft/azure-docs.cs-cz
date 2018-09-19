---
title: Osvědčené postupy zabezpečení sítě Azure | Dokumentace Microsoftu
description: Tento článek obsahuje sadu osvědčené postupy při používání zabezpečení sítě vytvořené v možnosti Azure.
services: security
documentationcenter: na
author: TomShinder
manager: mbaldwin
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/17/2018
ms.author: TomSh
ms.openlocfilehash: d89972ff0f7e3035fa20f8d9ee2863b68fa52e9f
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124061"
---
# <a name="azure-network-security-best-practices"></a>Osvědčené postupy zabezpečení sítě Azure
Můžete se připojit [Azure virtuální počítače (VM)](https://azure.microsoft.com/services/virtual-machines/) a zařízení tak, aby jejich umístěním na jiná síťová zařízení [virtuálním sítím Azure](https://azure.microsoft.com/documentation/services/virtual-network/). To znamená můžete připojit adaptéry virtuální sítě k virtuální síti a povolit založené na TCP/IP komunikaci mezi síťové zařízení. Virtuální počítače připojené ke službě Azure virtual network můžete připojit k zařízení ve stejné virtuální síti, různých virtuálních sítích, Internetu nebo vaše vlastní místní sítě.

Tento článek popisuje kolekci osvědčené postupy zabezpečení sítě Azure. Tyto osvědčené postupy, které jsou odvozeny z našich zkušenostech s Azure networking a prostředí zákazníků, jako je sami.

Pro každý osvědčený postup Tento článek vysvětluje:

* Jaký je doporučený postup
* Proč chcete povolit tento osvědčený postup
* Pokud chcete povolit osvědčený postup, co mohou být způsobeny
* Je to možné alternativy doporučené postupy
* Jak lze zjistíte, jak povolit osvědčený postup

Tento článek osvědčené postupy zabezpečení sítě Azure je založen na stanovisko shody a možnostech platformy Azure a sady funkcí, protože existují v okamžiku, kdy byla zapsána v tomto článku. Názory a technologie v průběhu času měnit a tento článek bude aktualizován v pravidelných intervalech, aby tyto změny projevily.

Následující části popisují doporučené postupy pro zabezpečení sítě.

## <a name="logically-segment-subnets"></a>Logicky segmentu podsítě
Virtuální sítě Azure jsou podobné sítě LAN ve vaší místní síti. Myšlenku za služby Azure virtual network je, že vytvoříte jeden privátní síť IP adres na základě místa ve kterém můžete umístit všechny virtuální počítače Azure. Privátní adresní prostory IP adres k dispozici jsou ve třídě A (10.0.0.0/8), třídy B (172.16.0.0/12), a rozsahy adres třídy C (192.168.0.0/16).

Osvědčené postupy k segmentaci logicky podsítě patří:

**Osvědčený postup**: segmentovat do podsítí větší adresní prostor.   
**Podrobnosti o**: použití [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)– na základě podsítě zásady k vytvoření podsítě.

**Osvědčený postup**: vytvořit řízení přístupu k síti mezi podsítěmi. Směrování mezi podsítěmi dojde automaticky a není nutné ručně nakonfigurovat směrovací tabulky. Ve výchozím nastavení nejsou žádné řízení přístupu k síti mezi podsítěmi, které vytvoříte ve službě Azure virtual network.   
**Podrobnosti o**: použití [skupinu zabezpečení sítě](../virtual-network/virtual-networks-nsg.md) (NSG). Skupiny Nsg se dají snadno, zařízení kontrolu paketů, které používají 5-řazené kolekce členů (Zdrojová IP adresa, zdrojový port, cílová IP adresa, cílový port a protokol vrstvy 4) přístup k vytváření pravidel povolení/zákazu pro síťový provoz. Povolí nebo zakážou provoz do a z jednu IP adresu, na a z více IP adres, nebo do a z celé podsítě.

Při použití skupin zabezpečení sítě pro řízení přístupu k síti mezi podsítěmi, stačí vložit prostředky, které patří do stejné zóně zabezpečení nebo role v jejich vlastní podsítě.

## <a name="control-routing-behavior"></a>Řízení chování směrování
Při umístění virtuálního počítače ve službě Azure virtual network virtuální počítač může připojit k jinému virtuálnímu počítači ve stejné virtuální síti, i když jsou ostatní virtuální počítače v různých podsítích. To je možné, protože tento typ komunikace umožňuje shromažďování systémových tras ve výchozím nastavení povolená. Tyto výchozí trasy mohly virtuální počítače ve stejné virtuální síti k zahájení připojení mezi sebou a s Internetem (pro odchozí komunikaci na Internetu).

I když výchozí systémové trasy, které jsou užitečné pro řadu scénářů nasazení, jsou časy, kdy budete chtít přizpůsobit konfigurace směrování pro vaše nasazení. Můžete nakonfigurovat adresu dalšího směrování k dosažení určitým příjemcům.

Doporučujeme, abyste nakonfigurovali [trasy definované uživatelem](../virtual-network/virtual-networks-udr-overview.md) při nasazování zařízení pro zabezpečení virtuální sítě. Mluvíme o to v pozdější části s názvem [zabezpečit vaše důležité prostředky služeb Azure pro vaše virtuální sítě](azure-security-network-security-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks).

> [!NOTE]
> Trasy definované uživatelem nejsou nutné a výchozí systémové trasy, které obvykle pracovat.
>
>

## <a name="enable-forced-tunneling"></a>Povolit vynucené tunelové propojení
Abyste lépe pochopili, vynucené tunelování, je užitečné vědět, jaké "rozdělit tunelové propojení" je. Nejběžnějším příkladem dělené tunelové propojení se používá u připojení virtuální privátní sítě (VPN). Představte si navázat připojení k síti VPN z vaší pokoje v hotelu k vaší podnikové síti. Toto připojení umožňuje přístup k firemním prostředkům. Přejděte veškerá komunikace s vaší podnikové síti pomocí tunelového připojení sítě VPN.

Co se stane, když chcete připojit k prostředkům na Internetu? Pokud je povoleno dělené tunelové propojení, tato připojení přejít přímo k Internetu a ne prostřednictvím tunelového připojení sítě VPN. Odborníci na zabezpečení, zvažte toto představuje potenciální riziko. Se doporučuje zakázat dělené tunelové propojení a ujistěte se, že všechna připojení, jsou určené k Internetu a jsou určené pro podnikové prostředky, přejděte pomocí tunelového připojení sítě VPN. Výhodou zakázat dělené tunelové propojení je, že jsou připojení k Internetu, potom nuceně prostřednictvím zabezpečení zařízení k podnikové síti. Pokud klient VPN připojený k Internetu mimo tunelového připojení sítě VPN, který by se tento případ.

Nyní Pojďme vrácení tomto k virtuálním počítačům ve virtuální síti Azure. Výchozí trasy pro služby Azure virtual network mohly virtuální počítače iniciovat provoz do Internetu. To moc může představovat bezpečnostní riziko, protože tato odchozí připojení může zvýšit prostor pro útoky virtuálního počítače a používají útočníci. Z tohoto důvodu doporučujeme, aby vám [povolit vynucené tunelování](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) na vašich virtuálních počítačů, když máte připojení mezi místními sítěmi mezi virtuální sítí Azure a vaší místní síti. Mluvíme o připojení mezi místními sítěmi později v síti osvědčené postupy.

Pokud nemáte připojení mezi různými místy, ujistěte se, abyste mohli využívat skupin zabezpečení sítě (vysvětlení výše) nebo Azure virtual network zabezpečovací zařízení (věnujeme se jí) aby odchozí připojení k Internetu z virtuálních počítačů Azure.

## <a name="use-virtual-network-appliances"></a>Použijte virtuální síťová zařízení
Skupiny zabezpečení sítě a směrování definovaného uživatelem můžete zadat míru zabezpečení sítě na síť a přenosové vrstvy [modelu OSI](https://en.wikipedia.org/wiki/OSI_model). Ale v některých situacích chcete nebo potřebujete povolit zabezpečení na vysoké úrovni zásobníku. V takových situacích doporučujeme, že nasazujete virtuální síťová zabezpečovací zařízení partnery Azure k dispozici.

Síť Azure zabezpečovací zařízení může poskytovat lepší zabezpečení než co poskytuje ovládací prvky nejvyšší úrovně sítě. Možnosti zabezpečení síťového zařízení zabezpečení virtuální sítě patří:


* Funkce brány firewall
* Neoprávněného vniknutí zjišťování/narušení ochrany před únikem informací
* Správa ohrožení zabezpečení
* Řízení aplikací
* Detekce anomálií založená na síti
* Filtrování webů
* Antivirová ochrana v programu
* Ochrana Botnetu

K dispozici virtuální síť Azure zabezpečovací zařízení, najdete [Azure Marketplace](https://azure.microsoft.com/marketplace/) a vyhledejte položku "zabezpečení" a "zabezpečení sítě."

## <a name="deploy-perimeter-networks-for-security-zones"></a>Nasadit hraniční sítí pro zóny zabezpečení
A [hraniční síti](https://docs.microsoft.com/azure/best-practices-network-security) (označované také jako DMZ) je segment fyzické nebo logické sítě, která poskytuje další vrstvu zabezpečení mezi vašimi prostředky a Internetem. Specializované síťová zařízení řízení přístupu na okraji hraniční síti povolit jenom požadované přenosy do vaší virtuální sítě.

Hraniční sítě jsou užitečné, protože správu řízení přístupu vaší sítě vám umožní soustředit se monitorování, protokolování a vytváření sestav na zařízení na hranici vaší virtuální sítí Azure. Tady je obvykle povolit distribuované s cílem odepření služeb (DDoS) ochrany před únikem informací, vniknutí zjišťování/narušení ochrany před únikem informací systémy (IDS/IPS), pravidla brány firewall a zásad, filtrování webů, antimalwaru sítě a dalších. Síťová zařízení zabezpečení nacházejí mezi Internetem a virtuální sítí Azure a rozhraní v obou sítích.

I když se jedná o základní koncepci hraniční síti, existuje mnoho různých návrzích, jako je například back-to-back troj adresami a s více adresami.

Pro všechna nasazení vysoké zabezpečení doporučujeme, zvažte použití hraniční síti k vylepšení úrovně zabezpečení sítě pro vaše prostředky Azure.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Vyhněte se na Internetu s vyhrazené linky WAN
Mnoho organizací zvolili hybridní IT trasy. V hybridním IT některé informace ve firmě jsou v Azure, ostatní budou i nadále místní. V mnoha případech některé součásti služby provozovaná v Azure v místním i nadále další komponenty.

V hybridním scénáři IT je obvykle nějaký typ připojení mezi místními sítěmi. Připojení mezi místními sítěmi umožňuje společnosti připojení svých místních sítí k virtuálním sítím Azure. K dispozici jsou dvě řešení pro připojení mezi různými místy:

* **Site-to-site VPN**: je důvěryhodné, spolehlivé a navážete technologie, ale připojení probíhá přes internet. Šířka pásma je omezen na maximálně o 200 MB/s. Site-to-site VPN je žádoucí možností v některých scénářích a je popsána dále v části [RDP/SSH zakázat přístup k virtuálním počítačům](#disable-rdpssh-access-to-virtual-machines).
* **Azure ExpressRoute**: doporučujeme používat [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) pro vaše připojení mezi místními sítěmi. ExpressRoute je vyhrazené sítě WAN propojení mezi místním umístěním nebo poskytovatele hostingu serveru Exchange. Protože telco připojení vašich dat nebude procházet přes internet a proto není vystavený potenciální rizika internetové komunikace.

## <a name="optimize-uptime-and-performance"></a>Optimalizovat výkon a dostupnost
Pokud služba je vypnutý, není přístupná informace. Pokud je tak, že data nepoužitelné nízký výkon, můžete zvážit data, která mají být nepřístupné. Z hlediska zabezpečení je potřeba cokoli, co můžete vaše služby mít optimální provozuschopnost a výkon.

Oblíbené a efektivní způsob pro zlepšení dostupnosti a výkonu je Vyrovnávání zatížení. Vyrovnávání zatížení je způsob distribuce síťového provozu mezi servery, které jsou součástí služby. Například pokud máte front-end webové servery jako součást služby, můžete použít vyrovnávání zatížení a distribuuje provoz mezi několik front-endové webové servery.

Toto rozdělení provozu, zvýšíte dostupnost, protože pokud jeden z webových serverů přestane být dostupný, nástroj pro vyrovnávání zatížení zastaví odesílání provozu k danému serveru a přesměruje na servery, které jsou stále online. Vyrovnávání zatížení vám také pomůže výkon, protože režie procesoru, síť a paměť obsluze žádostí se distribuuje mezi všechny servery s vyrovnáváním zatížení.

Doporučujeme vám, že nepoužijete Vyrovnávání zatížení, kdykoli je to možné a v závislosti na vašich služeb. Následují scénáře na všech úrovních virtuální sítě Azure a na globální úrovni, spolu s možností služby Vyrovnávání zatížení pro každý.

**Scénář**: když máte aplikaci, která:

- Vyžaduje ze stejné uživatelské/klientské relace přenosu žádostí do stejného virtuálního počítače back-end. Příklady jsou aplikace nákupních košíků a servery webové pošty.
- Přijímá pouze zabezpečené připojení, takže nešifrovaná komunikace se serverem není nepřijatelnou možností.
- Vyžaduje více požadavků protokolu HTTP na stejné připojení TCP dlouhotrvající směrovat nebo načíst balanced na různé back-end servery.

**Vyrovnávání zatížení možnost**: použití [Azure Application Gateway](../application-gateway/application-gateway-introduction.md), protokolu HTTP webové provoz nástroje pro vyrovnávání zatížení. Služba Application Gateway podporuje šifrování SSL začátku do konce a [ukončení protokolu SSL](../application-gateway/application-gateway-introduction.md) v bráně. Webové servery může potom dešifrováním šifrování a dešifrování režii a provoz dešifrovat do back-end serverů.

**Scénář**: je potřeba načíst vyrovnávat příchozí připojení z Internetu mezi servery umístěné ve službě Azure virtual network. Jsou scénáře, kdy jste:

- Máte bezstavové aplikace, které přijímají příchozí požadavky z Internetu.
- Nevyžadují rychlé relace a přesměrování zpracování SSL. Rychlé relace je metoda využívají k dosažení serveru spřažení s vyrovnáváním zatížení aplikace.

**Vyrovnávání zatížení možnost**: pomocí webu Azure portal k [vytvořit externím vyrovnáváním zatížení](../load-balancer/quickstart-create-basic-load-balancer-portal.md) se šíří příchozí požadavky na víc virtuálních počítačů pro zajištění vyšší úrovně dostupnosti.

**Scénář**: je potřeba načíst zůstatek připojení z virtuálních počítačů, které nejsou na Internetu. Ve většině případů se inicializuje připojení, která jsou přijati pro vyrovnávání zatížení zařízení ve virtuální síti Azure, jako je instance systému SQL Server nebo interní webové servery.   
**Vyrovnávání zatížení možnost**: pomocí webu Azure portal k [vytvořit interní nástroj](../load-balancer/quickstart-create-basic-load-balancer-powershell.md) se šíří příchozí požadavky na víc virtuálních počítačů pro zajištění vyšší úrovně dostupnosti.

**Scénář**: musíte globální zátěže, protože jste:

- Máte cloudové řešení, která je hromadně distribuují napříč několika oblastmi a vyžaduje nejvyšší úroveň dostupnosti (dostupnost) je to možné.
- Třeba nejvyšší úroveň dostupnosti je to možné, aby se zajistilo, že je vaše služba dostupná i v případě, že celé datové centrum přestane být k dispozici.

**Vyrovnávání zatížení možnost**: použijte Azure Traffic Manageru. Traffic Manager umožňuje načíst zůstatek připojení k vašim službám na základě umístění uživatele.

Například pokud uživatel odešle požadavek do služby z EU, připojení se směřuje do vaší služby umístěné v datovém centru EU. Tato část Traffic Manageru globální načíst vyrovnávání pomáhá zlepšit výkon, protože připojení k nejbližší datové centrum je rychlejší než připojení k datovým centrům, která jsou daleko.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>Zakázat přístup RDP/SSH k virtuálním počítačům
Je možné dosáhnout virtuální počítače Azure s použitím [Remote Desktop Protocol](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) a [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) protokol (SSH). Tyto protokoly umožňuje správu virtuálních počítačů ze vzdálených umístění a jsou standardní ve výpočetním prostředí datacenter.

Potenciální problém zabezpečení s použitím těchto protokolů přes internet je, že útočníci můžou používat [útok hrubou silou](https://en.wikipedia.org/wiki/Brute-force_attack) techniky k získání přístupu k Azure virtual machines. Po útočníci získají přístup, mohou použít váš virtuální počítač jako bod spuštění pro ohrožení ostatní počítače ve vaší virtuální síti nebo dokonce napadení síťovými zařízeními mimo Azure.

Doporučujeme zakázat přímý přístup pomocí protokolu RDP a SSH pro virtuální počítače Azure z Internetu. Po zakázání je přímý přístup pomocí protokolu RDP a SSH z Internetu, máte další možnosti, které můžete použít pro přístup k těmto virtuálním počítačům pro vzdálenou správu.

**Scénář**: Povolit u jednoho uživatele pro připojení ke službě Azure virtual network přes internet.   
**Možnost**: [Point-to-site VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md) je jiný termín pro připojení k síti VPN klienta nebo serveru pro vzdálený přístup. Po navázání připojení point-to-site slouží uživatele pomocí protokolu RDP nebo SSH pro připojení k žádné virtuální počítače na virtuální síť Azure, které se uživatel připojil přes point-to-site VPN. Předpokladem je, že je uživatel oprávnění k dosažení ty virtuální počítače.

Point-to-site VPN je bezpečnější než přímé spojení přes protokol RDP nebo SSH, protože uživatel nemá k ověření dvakrát před připojením k virtuálnímu počítači. Nejprve, uživatel musí ověřit (a autorizovat) k navázání připojení VPN typu point-to-site. Za druhé, uživatel musí ověřit (a autorizovat) k vytvoření relace protokolu RDP nebo SSH.

**Scénář**: Povolit uživatelům ve vaší místní síti připojit k virtuálním počítačům ve vaší virtuální sítí Azure.   
**Možnost**: A [site-to-site VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) celá síť se připojuje k jiné síti přes internet. Síť site-to-site VPN můžete použít k připojení místní sítě ke službě Azure virtual network. Uživatelé ve vaší místní síti připojit pomocí protokolu RDP nebo SSH přes připojení VPN typu site-to-site. Není nutné povolit přímý přístup protokolu RDP nebo SSH přes internet.

**Scénář**: poskytuje funkce podobné site-to-site VPN pomocí vyhrazené propojení WAN.   
**Možnost**: použití [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/). Poskytuje funkce podobné k síti VPN site-to-site. Hlavními rozdíly jsou:

- Vyhrazená propojení WAN nebude procházení Internetu.
- Vyhrazené linky WAN jsou obvykle více stabilní a poskytují větší výkon.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>Zabezpečte vaše důležité prostředky služeb Azure pro vaše virtuální sítě
Pomocí koncových bodů služby virtuální sítě rozšiřují privátní adresní prostor vaší virtuální sítě a její identitu vaší virtuální sítě do služeb Azure přes přímé připojení. Koncové body umožňují svázat vaše důležité prostředky služeb Azure pouze s vašimi virtuálními sítěmi. Provoz z vaší virtuální sítě do služby Azure vždy zůstává v páteřní síti Microsoft Azure.

Koncové body služby poskytují následující výhody:

- **Zlepšení zabezpečení prostředků služeb Azure:** Pomocí koncových bodů služby je možné svázat prostředky služeb Azure s vaší virtuální sítí. Svázání prostředků služeb virtual network poskytuje lepší zabezpečení díky úplnému odebrání veřejného internetového přístupu k prostředkům a povolení provozu pouze z vaší virtuální sítě.
- **Optimální směrování provozu služeb Azure z vaší virtuální sítě**: všechny trasy ve virtuální síti, které vynucují internetový provoz přes místní nebo virtuální zařízení, což se označuje jako vynucené tunelování, také vynutit provoz služeb Azure má provést stejného postupu jako internetový provoz. Koncové body služby poskytují optimální směrování provozu Azure.

  Koncové body vždy směrují provoz služby přímo z vaší virtuální sítě do služby v páteřní síti Azure. Udržování provozu na páteřní síti Azure umožňuje pokračovat v auditování a monitorování odchozího internetového provozu z vašich virtuálních sítí prostřednictvím vynuceného tunelování, aniž by to ovlivnilo provoz služeb. Další informace o [trasy definované uživatelem a vynuceném tunelování](../virtual-network/virtual-networks-udr-overview.md).

- **Snadné nastavení menším režie na správu**: už nepotřebujete vyhrazené veřejné IP adresy ve vaší virtuální sítě k zabezpečení prostředků Azure prostřednictvím brány firewall protokolu IP. K nastavení koncových bodů služby se nevyžaduje překlad adres ani zařízení brány. Koncové body služby se konfigurují pouhým kliknutím na podsíť. Není spojena žádná další režie udržovat koncových bodů.

Další informace o koncových bodů služby a služby Azure a oblasti, které jsou k dispozici pro koncové body služby, najdete v článku [koncové body služeb virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="next-step"></a>Další krok
Zobrazit [osvědčené postupy zabezpečení Azure a vzory](security-best-practices-and-patterns.md) pro další doporučené postupy zabezpečení, mají použít, když jste návrhu, nasazení a správa cloudových řešení pomocí služby Azure.
