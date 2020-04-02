---
title: Doporučené postupy pro zabezpečení sítě – Microsoft Azure
description: Tento článek obsahuje sadu osvědčených postupů pro zabezpečení sítě pomocí integrovaných funkcí Azure.
services: security
author: TerryLanfear
manager: barbkess
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2019
ms.author: TomSh
ms.openlocfilehash: 5e155758d19b45d977fcd087bff0ceb85898f8f8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548306"
---
# <a name="azure-best-practices-for-network-security"></a>Doporučené postupy Azure pro zabezpečení sítě
Tento článek popisuje kolekci doporučených postupů Azure pro zvýšení zabezpečení sítě. Tyto osvědčené postupy jsou odvozeny z našich zkušeností se sítí Azure a zkušeností zákazníků, jako jste vy.

Pro každý osvědčený postup tento článek vysvětluje:

* Jaký je osvědčený postup
* Proč chcete povolit tento osvědčený postup
* Jaký by mohl být výsledek, pokud nepovolíte osvědčené postupy
* Možné alternativy k osvědčeným postupům
* Jak se můžete naučit povolit osvědčené postupy

Tyto osvědčené postupy jsou založeny na konsensu stanovisko a možnosti platformy Azure a sady funkcí, jak existují v době, kdy byl napsán tento článek. Názory a technologie se v průběhu času mění a tento článek bude pravidelně aktualizován tak, aby tyto změny odrážel.

## <a name="use-strong-network-controls"></a>Použití silných síťových ovládacích prvků
[Virtuální počítače (VM)](https://azure.microsoft.com/services/virtual-machines/) a zařízení Azure můžete připojit k jiným síťovým zařízením jejich umístěním do [virtuálních sítí Azure](../../virtual-network/index.yml). To znamená, že můžete připojit karty virtuálního síťového rozhraní k virtuální síti a povolit komunikaci založenou na protokolu TCP/IP mezi zařízeními s podporou sítě. Virtuální počítače připojené k virtuální síti Azure se můžou připojovat k zařízením ve stejné virtuální síti, různých virtuálních sítích, internetu nebo vlastních místních sítích.

Při plánování sítě a zabezpečení sítě doporučujeme centralizovat:

- Správa funkcí hlavní sítě, jako je ExpressRoute, zřizování virtuální sítě a podsítě a adresování IP.
- Zásady zabezpečení sítě prvků, jako jsou funkce virtuálních síťových zařízení, jako je ExpressRoute, virtuální sítě a zřizování podsítě a ip adresování.

Pokud ke sledování sítě a zabezpečení sítě používáte společnou sadu nástrojů pro správu, získáte jasnou viditelnost obou. Jednoduchá a jednotná strategie zabezpečení snižuje počet chyb, protože zvyšuje lidské porozumění a spolehlivost automatizace.

## <a name="logically-segment-subnets"></a>Logicky segmentové podsítě
Virtuální sítě Azure jsou podobné sítím LAN v místní síti. Myšlenka virtuální sítě Azure spoána v tom, že vytvoříte síť založenou na jediném privátním adresním prostoru IP, na který můžete umístit všechny virtuální počítače Azure. Privátní IP adresní prostory jsou k dispozici v rozsahu třídy A (10.0.0.0/8), třídy B (172.16.0.0/12) a třídy C (192.168.0.0/16).

Mezi osvědčené postupy pro logické segmentaci podsítí patří:

**Osvědčený postup**: Nepřiřazujte pravidla povolit s širokým rozsahem (například povolit 0.0.0.0 až 255.255.255.255).  
**Podrobnosti**: Zajistěte, aby postupy odstraňování problémů odrazovaly od nastavení těchto typů pravidel nebo je nezakazovaly. Tato pravidla umožňují, aby pravidla vedla k falešnému pocitu bezpečí a jsou často nalezena a využívána červenými týmy.

**Osvědčený postup**: Segmentujte větší adresní prostor do podsítí.   
**Detail**: K vytvoření podsítí použijte principy podsíťování založené na [ROZHRANÍ CIDR.](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

**Osvědčený postup**: Vytvoření ovládacích prvků přístupu k síti mezi podsítěmi. Směrování mezi podsítěmi probíhá automaticky a není nutné ručně konfigurovat směrovací tabulky. Ve výchozím nastavení neexistují žádné ovládací prvky přístupu k síti mezi podsítěmi, které vytvoříte ve virtuální síti Azure.   
**Podrobnosti**: Pomocí [skupiny zabezpečení sítě](/azure/virtual-network/virtual-networks-nsg) chránit před nevyžádaným provozem do podsítí Azure. Skupiny zabezpečení sítě jsou jednoduchá, stavová zařízení pro kontrolu paketů, která používají přístup 5 n-tice (zdrojová IP adresa, zdrojový port, cílová ADRESA IP, cílový port a protokol vrstvy 4) k vytvoření pravidel povolení/zamítnutí síťového provozu. Povolujete nebo zamítáte provoz na jednu adresu IP a z ní, na více adres IP nebo z celých podsítí.

Používáte-li skupiny zabezpečení sítě pro řízení přístupu k síti mezi podsítěmi, můžete prostředky, které patří do stejné zóny zabezpečení nebo role, umístit do vlastních podsítí.

**Osvědčený postup:** Vyhněte se malým virtuálním sítím a podsítím, abyste zajistili jednoduchost a flexibilitu.   
**Podrobnosti**: Většina organizací přidá více prostředků, než bylo původně plánováno, a opětovné přidělení adres je náročné na práci. Použití malých podsítí přidá omezenou hodnotu zabezpečení a mapování skupiny zabezpečení sítě do každé podsítě přidá režii. Definujte podsítě široce, abyste zajistili flexibilitu pro růst.

**Osvědčený postup:** Zjednodušte správu pravidel skupiny zabezpečení sítě definováním [skupin zabezpečení aplikací](https://azure.microsoft.com/blog/applicationsecuritygroups/).  
**Podrobnosti**: Definujte skupinu zabezpečení aplikací pro seznamy adres IP, o kterých se domníváte, že se mohou v budoucnu změnit nebo které budou použity v mnoha skupinách zabezpečení sítě. Ujistěte se, že název skupiny zabezpečení aplikací jasně, takže ostatní mohou pochopit jejich obsah a účel.

## <a name="adopt-a-zero-trust-approach"></a>Přijetí přístupu nulové důvěry
Sítě založené na obvodu pracují za předpokladu, že všechny systémy v rámci sítě mohou být důvěryhodné. Dnešní zaměstnanci však přistupují k prostředkům své organizace odkudkoli na různých zařízeních a aplikacích, což činí ovládací prvky zabezpečení perimetru irelevantními. Zásady řízení přístupu, které se zaměřují pouze na to, kdo má přístup k prostředku, nestačí. Aby správci zabezpečení zvládli rovnováhu mezi zabezpečením a produktivitou, musí také zohlednit *způsob* přístupu k prostředku.

Sítě se musí vyvíjet z tradiční obrany, protože sítě mohou být ohroženy porušením: útočník může ohrozit jeden koncový bod v rámci důvěryhodné hranice a pak rychle rozšířit základnu v celé síti. [Sítě nulové důvěryhodnosti](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/) eliminují koncept důvěryhodnosti na základě umístění v síti v rámci obvodu. Místo toho architektury zero trust používají deklarace důvěryhodnosti zařízení a uživatelů k přístupu k datům a prostředkům organizace. Pro nové iniciativy přijměte zero trust přístupy, které ověřují důvěryhodnost v době přístupu.

Osvědčené postupy jsou:

**Osvědčený postup:** Udělte prostředky podmíněného přístupu na základě zařízení, identity, záruky, umístění v síti a dalších.  
**Podrobnosti**: [Podmíněný přístup Azure AD](/azure/active-directory/conditional-access/overview) umožňuje použít správné řízení přístupu implementací rozhodnutí automatizovaného řízení přístupu na základě požadovaných podmínek. Další informace najdete [v tématu Správa přístupu ke správě Azure pomocí podmíněného přístupu](../../role-based-access-control/conditional-access-azure-management.md).

**Osvědčený postup:** Povolit přístup k portu pouze po schválení pracovního postupu.  
**Podrobnosti**: Přístup [k virtuálním počítači v Azure v Centru zabezpečení just-in-time](../../security-center/security-center-just-in-time.md) můžete použít k uzamčení příchozího provozu na virtuálních počítačích Azure, snížení vystavení útokům a zároveň poskytujete snadný přístup k virtuálním počítačům v případě potřeby.

**Osvědčený postup**: Udělte dočasná oprávnění k provádění privilegovaných úloh, která brání škodlivým nebo neoprávněným uživatelům v získání přístupu po vypršení platnosti oprávnění. Přístup je udělen pouze v případě, že jej uživatelé potřebují.  
**Podrobnosti**: Použití přístupu just-in-time ve správě privilegovaných identit Azure AD nebo v řešení jiného výrobce udělit oprávnění k provádění privilegovaných úloh.

Zero Trust je další vývoj v oblasti zabezpečení sítě. Stav kybernetických útoků pohání organizace, aby přijaly myšlení "assume breach", ale tento přístup by neměl být omezující. Sítě Zero Trust chrání podniková data a zdroje a zároveň zajišťují, aby organizace mohly budovat moderní pracoviště pomocí technologií, které zaměstnancům umožňují být produktivní kdykoli, kdekoli a jakýmkoli způsobem.

## <a name="control-routing-behavior"></a>Řízení chování směrování
Když vložíte virtuální počítač do virtuální sítě Azure, virtuální počítač se může připojit k libovolnému jinému virtuálnímu počítači ve stejné virtuální síti, i když ostatní virtuální počítače jsou v různých podsítích. To je možné, protože kolekce systémových tras povolena ve výchozím nastavení umožňuje tento typ komunikace. Tyto výchozí trasy umožňují virtuálním počítače ve stejné virtuální síti zahájit připojení mezi sebou navzájem a s internetem (pouze pro odchozí komunikaci k internetu).

Přestože výchozí systémové trasy jsou užitečné pro mnoho scénářů nasazení, existují časy, kdy chcete přizpůsobit konfiguraci směrování pro vaše nasazení. Adresu dalšího směrování můžete nakonfigurovat tak, aby se dostala na konkrétní cíle.

Doporučujeme nakonfigurovat [uživatelem definované trasy](../../virtual-network/virtual-networks-udr-overview.md) při nasazení zařízení zabezpečení pro virtuální síť. Mluvíme o tom v pozdější části s názvem [Zabezpečit vaše kritické prostředky služby Azure pouze do virtuálních sítí](network-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks).

> [!NOTE]
> Uživatelem definované trasy nejsou vyžadovány a výchozí systémové trasy obvykle fungují.
>
>

## <a name="use-virtual-network-appliances"></a>Použití virtuálních síťových zařízení
Skupiny zabezpečení sítě a uživatelem definované směrování mohou poskytnout určitou míru zabezpečení sítě v síťových a transportních vrstvách [modelu OSI](https://en.wikipedia.org/wiki/OSI_model). Ale v některých situacích chcete nebo potřebujete povolit zabezpečení na vysokých úrovních zásobníku. V takových situacích doporučujeme nasadit zařízení pro zabezpečení virtuální sítě poskytovaná partnery Azure.

Zařízení zabezpečení sítě Azure můžou poskytovat lepší zabezpečení, než jaké poskytují ovládací prvky na úrovni sítě. Možnosti zabezpečení sítě zařízení pro zabezpečení virtuální sítě zahrnují:


* Brána firewall
* Detekce vniknutí/prevence vniknutí
* Správa zranitelnosti
* Řízení aplikace
* Detekce anomálií v síti
* Filtrování webů
* Antivirus
* Ochrana botnetu

Pokud chcete najít dostupná zařízení pro zabezpečení virtuální sítě Azure, přejděte na [Azure Marketplace](https://azure.microsoft.com/marketplace/) a vyhledejte "zabezpečení" a "zabezpečení sítě".

## <a name="deploy-perimeter-networks-for-security-zones"></a>Nasazení hraničních sítí pro zóny zabezpečení
[Hraniční síť](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) (označovaná také jako DMZ) je fyzický nebo logický segment sítě, který poskytuje další vrstvu zabezpečení mezi prostředky a internetem. Specializovaná zařízení pro řízení přístupu k síti na okraji hraniční sítě umožňují pouze požadovaný provoz do vaší virtuální sítě.

Hraniční sítě jsou užitečné, protože můžete zaměřit správu řízení přístupu k síti, monitorování, protokolování a vytváření sestav na zařízeních na okraji virtuální sítě Azure. Hraniční síť je místo, kde obvykle povolujete distribuované zabezpečení odmítnutí služby (DDoS), systémy detekce vniknutí/prevence vniknutí (IDS/IPS), pravidla a zásady brány firewall, filtrování webů, síťový antimalware a další. Zařízení zabezpečení sítě jsou mezi internetem a virtuální sítí Azure a mají rozhraní v obou sítích.

I když se jedná o základní návrh obvodové sítě, existuje mnoho různých vzorů, jako back-to-back, tri-homed, a multi-homed.

Na základě výše uvedeného konceptu Zero Trust doporučujeme zvážit použití hraniční sítě pro všechna nasazení s vysokým zabezpečením, abyste zvýšili úroveň zabezpečení sítě a řízení přístupu pro vaše prostředky Azure. Azure nebo řešení jiného výrobce můžete použít k zajištění další vrstvy zabezpečení mezi prostředky a internetem:

- Nativní ovládací prvky Azure. [Azure Firewall](/azure/firewall/overview) a [brána firewall webových aplikací v application gateway](../../application-gateway/features.md#web-application-firewall) nabízejí základní zabezpečení s plně stavovou bránou firewall jako službou, integrovanou vysokou dostupností, neomezenou škálovatelností cloudu, filtrováním plně kvalifikovaných znalostí celého rozsahu, podporou základních sad pravidel Protokolu OWASP a jednoduchým nastavením a konfigurací.
- Nabídky třetích stran. Vyhledejte na [Azure Marketplace](https://azuremarketplace.microsoft.com/) bránu firewall nové generace (NGFW) a další nabídky třetích stran, které poskytují známé nástroje zabezpečení a výrazně zvýšily úrovně zabezpečení sítě. Konfigurace může být složitější, ale nabídka třetí strany vám může umožnit používat stávající funkce a dovednosti.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Vyhněte se vystavení internetu pomocí vyhrazených odkazů WAN
Mnoho organizací zvolilo hybridní it trasu. S hybridním IT, některé z informačních prostředků společnosti jsou v Azure a jiné zůstávají místní. V mnoha případech některé součásti služby běží v Azure, zatímco jiné součásti zůstávají místní.

V hybridním scénáři IT je obvykle nějaký typ připojení mezi místními prostory. Mezimístní připojení umožňuje společnosti připojit místní sítě k virtuálním sítím Azure. K dispozici jsou dvě řešení připojení mezi místními prostory:

* [Vpn site-to-site](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md). Je to důvěryhodná, spolehlivá a zavedená technologie, ale připojení probíhá přes internet. Šířka pásma je omezena na maximálně 1,25 Gb/s. Síť VPN site-to-site je v některých scénářích žádoucí volbou.
* **Azure ExpressRoute**. Doporučujeme používat [ExpressRoute](../../expressroute/expressroute-introduction.md) pro připojení mezi místními prostory. ExpressRoute umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes soukromé připojení zajišťované poskytovatelem připojení. Pomocí ExpressRoute můžete navázat připojení ke cloudovým službám Microsoftu, jako je Azure, Office 365 a Dynamics 365. ExpressRoute je vyhrazené propojení SÍTĚ WAN mezi místním umístěním nebo poskytovatelem hostingu microsoft exchange. Vzhledem k tomu, že se jedná o telekomunikační připojení, vaše data necestují přes internet, takže nejsou vystavena potenciálním rizikům internetové komunikace.

Umístění připojení ExpressRoute může ovlivnit kapacitu brány firewall, škálovatelnost, spolehlivost a viditelnost síťového provozu. Budete muset určit, kde ukončit ExpressRoute v existujících (místních) sítích. Můžete:

- Ukončit mimo bránu firewall (paradigma hraniční sítě), pokud potřebujete přehled o provozu, pokud potřebujete pokračovat v existující praxi izolace datových center, nebo pokud jste výhradně uvedení extranet prostředky v Azure.
- Ukončit uvnitř firewallu (paradigma rozšíření sítě). Toto je výchozí doporučení. Ve všech ostatních případech doporučujeme zacházet s Azure jako s n-tém datovým centrem.

## <a name="optimize-uptime-and-performance"></a>Optimalizace doby a výkonu
Pokud je služba vypnutá, nelze získat přístup k informacím. Pokud je výkon tak nízký, že data jsou nepoužitelná, můžete data považovat za nepřístupná. Z hlediska zabezpečení musíte udělat vše, co je v vašich silách, abyste se ujistili, že vaše služby mají optimální dobu obsluhy a výkon.

Oblíbenou a účinnou metodou pro zvýšení dostupnosti a výkonu je vyrovnávání zatížení. Vyrovnávání zatížení je metoda distribuce síťového provozu mezi servery, které jsou součástí služby. Pokud máte například jako součást služby webové servery front-end, můžete použít vyrovnávání zatížení k distribuci provozu mezi více webovými servery front-endu.

Toto rozdělení provozu zvyšuje dostupnost, protože pokud jeden z webových serverů přestane být k dispozici, nástroj pro vyrovnávání zatížení přestane odesílat přenosy na tento server a přesměruje jej na servery, které jsou stále online. Vyrovnávání zatížení také pomáhá výkon, protože procesor, síť a nároky na paměť pro obsluhování požadavků je distribuován napříč všemi servery s vyrovnáváním zatížení.

Doporučujeme, abyste kdykoli používali vyrovnávání zatížení, kdykoli je to možné, a podle potřeby pro vaše služby. Následují scénáře na úrovni virtuální sítě Azure i globální úrovni, spolu s možnostmi vyrovnávání zatížení pro každou z nich.

**Scénář**: Máte aplikaci, která:

- Vyžaduje požadavky ze stejné relace uživatele nebo klienta k dosažení stejného back-endvirtuálního počítače. Příkladem jsou aplikace nákupního košíku a servery webové pošty.
- Přijímá pouze zabezpečené připojení, takže nešifrovaná komunikace se serverem není přijatelnou možností.
- Vyžaduje více požadavků HTTP na stejné dlouhotrvající připojení TCP, které mají být směrovány nebo vyrovnávání zatížení na různé servery back-end.

**Možnost vyrovnávání zatížení:** Použijte [Azure Application Gateway](/azure/application-gateway/application-gateway-introduction), nástroj pro vyrovnávání zatížení webového provozu HTTP. Aplikační brána podporuje komplexní šifrování TLS a [ukončení TLS](/azure/application-gateway/application-gateway-introduction) v bráně. Webové servery pak mohou být unburdened z šifrování a dešifrování režie a provoz tekoucí nešifrované na back-end servery.

**Scénář**: Je třeba vyvážit příchozí připojení z internetu mezi servery umístěnými ve virtuální síti Azure. Scénáře jsou, když:

- Mají bezstavové aplikace, které přijímají příchozí požadavky z internetu.
- Nevyžadují rychlé relace nebo tls vyložení. Rychlé relace je metoda používaná s vyrovnáváním zatížení aplikace k dosažení spřažení serveru.

**Možnost vyrovnávání zatížení**: Pomocí portálu Azure [vytvořte externí vyrovnávání zatížení,](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) které rozprostře příchozí požadavky na více virtuálních počítačích, aby zajistilo vyšší úroveň dostupnosti.

**Scénář**: Je třeba vyrovnávání zatížení připojení z virtuálních počítačů, které nejsou na internetu. Ve většině případů jsou připojení přijatá pro vyrovnávání zatížení iniciována zařízeními ve virtuální síti Azure, jako jsou instance SERVERU SQL Server nebo interní webové servery.   
**Možnost vyrovnávání zatížení**: Pomocí portálu Azure [vytvořte interní systém vyrovnávání zatížení,](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) který rozprostře příchozí požadavky na více virtuálních počítačích, aby zajistil vyšší úroveň dostupnosti.

**Scénář**: Potřebujete globální vyrovnávání zatížení, protože:

- Mějme cloudové řešení, které je široce distribuováno ve více oblastech a vyžaduje nejvyšší možnou úroveň dostupnosti (dostupnosti).
- Potřebujete nejvyšší možnou úroveň provozu, abyste se ujistili, že vaše služba je k dispozici i v případě, že nebude k dispozici celé datové centrum.

**Možnost vyrovnávání zatížení:** Použijte Azure Traffic Manager. Traffic Manager umožňuje vyrovnávání zatížení připojení k vašim službám na základě umístění uživatele.

Pokud například uživatel požádá o vaši službu z EU, bude připojení směrováno k vašim službám umístěným v datovém centru EU. Tato část globálního vyrovnávání zatížení Traffic Manageru pomáhá zlepšit výkon, protože připojení k nejbližšímu datovému centru je rychlejší než připojení k datovým centrům, která jsou daleko.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>Zakázání přístupu RDP/SSH k virtuálním počítačům
Virtuální počítače Azure je možné oslovit pomocí [protokolu RDP (Remote Desktop Protocol)](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) a protokolu [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH). Tyto protokoly umožňují správu virtuálních počítačů ze vzdálených umístění a jsou standardem v datacentrovém computingu.

Potenciální problém zabezpečení s použitím těchto protokolů přes internet je, že útočníci můžete použít [techniky hrubou silou](https://en.wikipedia.org/wiki/Brute-force_attack) získat přístup k virtuálním počítačům Azure. Potom co útočníci získají přístup, můžou použít váš virtuální počítač jako spouštěcí bod pro narušení dalších počítačů ve virtuální síti, nebo dokonce zaútočit na síťová zařízení mimo Azure.

Doporučujeme zakázat přímý přístup RDP a SSH k virtuálním počítačům Azure z internetu. Po přímé RDP a SSH přístup z internetu je zakázáno, máte další možnosti, které můžete použít pro přístup k těmto virtuálním mům pro vzdálenou správu.

**Scénář**: Povolte jednomu uživateli připojení k virtuální síti Azure přes internet.   
**Možnost:** [Point-to-site VPN](/azure/vpn-gateway/vpn-gateway-point-to-site-create) je jiný termín pro připojení klient/server VPN vzdáleného přístupu. Po navázání připojení bodu k webu může uživatel pomocí protokolu RDP nebo SSH připojit k libovolným virtuálním virtuálním mům umístěným ve virtuální síti Azure, ke kterým se uživatel připojil prostřednictvím sítě VPN bodu na místo. To předpokládá, že uživatel je oprávněn k dosažení těchto virtuálních stránek.

Point-to-site VPN je bezpečnější než přímé připojení RDP nebo SSH, protože uživatel musí ověřit dvakrát před připojením k virtuálnímu počítači. Nejprve musí uživatel ověřit (a být autorizován) k navázání připojení VPN bodu k webu. Za druhé, uživatel musí ověřit (a být autorizován) k vytvoření relace RDP nebo SSH.

**Scénář**: Povolte uživatelům v místní síti připojení k virtuálním počítačům ve vaší virtuální síti Azure.   
**Možnost:** Síť [VPN site-to-site](/azure/vpn-gateway/vpn-gateway-site-to-site-create) spojuje celou síť s jinou sítí přes internet. K připojení místní sítě k virtuální síti Azure můžete použít síť VPN mezi lokalitami. Uživatelé v místní síti se připojují pomocí protokolu RDP nebo SSH přes připojení VPN mezi lokalitami. Nemusíte povolit přímý přístup k RDP nebo SSH přes internet.

**Scénář**: Pomocí vyhrazeného odkazu WAN můžete poskytovat funkce podobné síti VPN mezi lokalitami.   
**Možnost:** Použijte [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/). Poskytuje funkce podobné síti VPN site-to-site. Hlavními rozdíly jsou:

- Vyhrazené propojení WAN neprochází internetem.
- Vyhrazené wan odkazy jsou obvykle stabilnější a lepší výkon.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>Zabezpečení důležitých prostředků služeb Azure pouze do virtuálních sítí
Koncové body služby virtuální sítě slouží k rozšíření privátního adresního prostoru virtuální sítě a identity vaší virtuální sítě na služby Azure prostředně prostředně přímého připojení. Koncové body umožňují svázat vaše důležité prostředky služeb Azure pouze s vašimi virtuálními sítěmi. Provoz z vaší virtuální sítě do služby Azure vždy zůstane v páteřní síti Microsoft Azure.

Koncové body služby poskytují následující výhody:

- **Zlepšení zabezpečení prostředků služeb Azure:** Pomocí koncových bodů služby je možné svázat prostředky služeb Azure s vaší virtuální sítí. Svázání prostředků služeb s virtuální sítí poskytuje lepší zabezpečení, protože zcela eliminuje přístup k prostředkům z veřejného internetu a povoluje jen provoz z vaší virtuální sítě.
- **Optimální směrování pro provoz služeb Azure z vaší virtuální sítě**: Všechny trasy ve vaší virtuální síti, které vynucují internetový provoz do místních a/nebo virtuálních zařízení, označované jako vynucené tunelování, také nutí provoz služeb Azure, aby se vydal stejnou cestou jako internetový provoz. Koncové body služby poskytují optimální směrování provozu Azure.

  Koncové body vždy přebírají provoz služeb přímo z vaší virtuální sítě do služby v páteřní síti Azure. Udržování provozu v páteřní síti Azure umožňuje pokračovat v auditování a monitorování odchozího internetového provozu z vašich virtuálních sítí prostřednictvím vynuceného tunelového propojení, aniž by to ovlivnilo provoz služeb. Další informace o [uživatelem definovaných trasách a vynuceném tunelovém propojení](../../virtual-network/virtual-networks-udr-overview.md).

- **Jednoduché nastavení s nižší režií na správu**: K zabezpečení prostředků Azure prostřednictvím brány firewall IP už nepotřebujete vyhrazené veřejné IP adresy ve virtuálních sítích. K nastavení koncových bodů služby se nevyžaduje překlad adres ani zařízení brány. Koncové body služby se konfigurují pouhým kliknutím na podsíť. Neexistuje žádná další režie pro údržbu koncových bodů.

Další informace o koncových bodech služby a službách Azure a oblastech, pro které jsou koncové body služby k dispozici, najdete v [tématu Koncové body služby virtuální sítě](../../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="next-steps"></a>Další kroky
Podívejte se na [osvědčené postupy a vzory zabezpečení Azure, kde](best-practices-and-patterns.md) najdete další doporučené postupy zabezpečení, které se používají při navrhování, nasazování a správě cloudových řešení pomocí Azure.
