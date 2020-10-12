---
title: Osvědčené postupy zabezpečení sítě – Microsoft Azure
description: Tento článek poskytuje sadu osvědčených postupů pro zabezpečení sítě pomocí integrovaných funkcí Azure.
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
ms.openlocfilehash: 36673533fbbfc913f742a32bd20cde2b238e2143
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397228"
---
# <a name="azure-best-practices-for-network-security"></a>Osvědčené postupy Azure pro zabezpečení sítě
Tento článek popisuje shromažďování osvědčených postupů Azure pro vylepšení zabezpečení sítě. Tyto osvědčené postupy se odvozují z našich zkušeností s používáním sítí Azure a zkušeností zákazníků, jako je sami.

U každého osvědčeného postupu Tento článek vysvětluje:

* Co je nejlepší postup
* Proč je vhodné Tento osvědčený postup povolit
* To může být způsobeno tím, že nepovolíte osvědčené postupy
* Možné alternativy k osvědčeným postupům
* Postup, jak se naučit, jak povolit osvědčený postup

Tyto osvědčené postupy jsou založené na konsensu a funkcemi a sadách funkcí platformy Azure, jak existují v době, kdy byl tento článek napsán. Názory a technologie se v průběhu času mění a tento článek se pravidelně aktualizuje, aby odrážel tyto změny.

## <a name="use-strong-network-controls"></a>Použití silných síťových ovládacích prvků
[Virtuální počítače Azure](https://azure.microsoft.com/services/virtual-machines/) a zařízení můžete propojit s dalšími síťovými zařízeními tím, že je umístíte do služby [Azure Virtual Networks](../../virtual-network/index.yml). To znamená, že můžete připojit karty virtuální sítě k virtuální síti a povolit tak komunikaci založenou na protokolu TCP/IP mezi zařízeními s podporou sítě. Virtuální počítače připojené k virtuální síti Azure se můžou připojovat k zařízením ve stejné virtuální síti, různých virtuálních sítích, Internetu nebo vašich vlastních místních sítích.

Při plánování sítě a zabezpečení vaší sítě doporučujeme, abyste provedli centralizaci:

- Správa základních síťových funkcí, jako je ExpressRoute, virtuální síť a zřizování podsítí, a IP adresování.
- Řízení prvků zabezpečení sítě, jako je síťové virtuální zařízení, které funguje jako ExpressRoute, virtuální síť a zřizování podsítí a IP adresy.

Pokud používáte společnou sadu nástrojů pro správu pro monitorování sítě a zabezpečení sítě, získáte jasné možnosti zobrazení do obou. Jednoduchá a sjednocená strategie zabezpečení snižuje chyby, protože zvyšuje lidský význam a spolehlivost automatizace.

## <a name="logically-segment-subnets"></a>Logicky segmentovat podsítě
Virtuální sítě Azure jsou podobné sítím LAN v místní síti. Nápadem za Azure Virtual Network je vytvoření sítě založené na jednom privátním adresním prostoru IP adres, na kterém můžete umístit všechny virtuální počítače Azure. K dispozici jsou soukromé adresní prostory IP adres ve třídě A (10.0.0.0/8), třídě B (172.16.0.0/12) a v oblastech třídy C (192.168.0.0/16).

Mezi osvědčené postupy pro logické segmentování podsítí patří:

**Osvědčený postup**: nepřiřazujte pravidla povolení s širšími rozsahy (například povolit 0.0.0.0 až 255.255.255.255).  
**Podrobnosti**: Zajistěte, aby postupy řešení potíží zabránily nebo zakazují nastavení těchto typů pravidel. Tato pravidla povolují nepravdivému způsobu zabezpečení a jsou často zjištěna a zneužita v případě červeného týmu.

**Osvědčený postup**: segmentování většího adresního prostoru v podsítích.   
**Podrobnosti**: vytvoření podsítí pomocí principů podsítí založených na [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

**Osvědčený postup**: vytvoření řízení přístupu k síti mezi podsítěmi. Směrování mezi podsítěmi proběhne automaticky a nemusíte konfigurovat směrovací tabulky ručně. Ve výchozím nastavení neexistují žádná řízení přístupu k síti mezi podsítěmi, které vytvoříte ve službě Azure Virtual Network.   
**Podrobnosti**: k ochraně před nevyžádaným provozem do podsítí Azure použijte [skupinu zabezpečení sítě](/azure/virtual-network/virtual-networks-nsg) . Skupiny zabezpečení sítě jsou jednoduchá a stavová zařízení pro kontrolu paketů, která používají přístup 5-Tuple (zdrojová IP adresa, zdrojový port, cílová IP adresa, cílový port a protokol vrstvy 4) k vytvoření pravidel povolení a odepření provozu v síti. Povolíte nebo zakážete provoz do a z jedné IP adresy, do a z několika IP adres nebo do a z celých podsítí.

Pokud používáte skupiny zabezpečení sítě pro řízení přístupu k síti mezi podsítěmi, můžete do jejich vlastních podsítí umístit prostředky, které patří do stejné zóny nebo role zabezpečení.

**Osvědčený postup**: Vyhněte se malým virtuálním sítím a podsítím, abyste zajistili jednoduchost a flexibilitu.   
**Podrobnosti**: většina organizací přidávají více prostředků, než je původně plánováno, a opakované přidělování adres je náročné na práci. Použití malých podsítí přičítá hodnotu omezeného zabezpečení a mapování skupiny zabezpečení sítě na každou podsíť zvyšuje režii. Definujte v podstatě podsítě, abyste měli jistotu, že budete mít flexibilitu pro růst.

**Osvědčený postup**: zjednodušení správy pravidel skupiny zabezpečení sítě definováním [skupin zabezpečení aplikací](https://azure.microsoft.com/blog/applicationsecuritygroups/).  
**Podrobnosti**: Definujte skupinu zabezpečení aplikace pro seznamy IP adres, které si myslíte, že se v budoucnu mohou změnit nebo použít v mnoha skupinách zabezpečení sítě. Nezapomeňte pojmenovat skupiny zabezpečení aplikace jasně, aby ostatní mohli pochopit jejich obsah a účel.

## <a name="adopt-a-zero-trust-approach"></a>Přijetí přístupu s nulovým vztahem důvěryhodnosti
Hraniční sítě fungují na předpokladu, že všechny systémy v síti mohou být důvěryhodné. Ale dnešní zaměstnanci mají přístup k prostředkům organizace odkudkoli na nejrůznějších zařízeních a aplikacích, což usnadňuje řízení hraničního zabezpečení. Zásady řízení přístupu, které se zaměřují jenom na to, kdo má přístup k prostředku, nejsou dost. Aby správci zabezpečení mohli vyvážit rovnováhu mezi zabezpečením a produktivitou, musí také zohlednit *způsob, jakým* se k prostředku přistupoval.

Sítě se musí vyvíjet z tradičních obran, protože sítě mohou být zranitelné vůči narušení: útočník může ohrozit jeden koncový bod v rámci důvěryhodné hranice a pak rychle rozšířit dostane napříč celou sítí. [Nulové důvěryhodné](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/) sítě eliminují koncept vztahu důvěryhodnosti na základě síťového umístění v hraniční síti. Místo toho nulové architektury vztahu důvěryhodnosti používají deklarace identity zařízení a uživatele k přístupu do brány k datům a prostředkům organizace. V případě nových iniciativ zajistěte nulové přístupy k důvěryhodnosti, které ověřují důvěryhodnost v době přístupu.

Osvědčené postupy:

**Osvědčený postup**: Poskytněte podmíněný přístup k prostředkům na základě zařízení, identity, záruky, umístění v síti a dalších.  
**Podrobnosti**: [podmíněný přístup Azure AD](../../active-directory/conditional-access/overview.md) umožňuje použít správný ovládací prvky přístupu implementací automatického rozhodování o řízení přístupu na základě požadovaných podmínek. Další informace najdete v tématu [Správa přístupu ke správě Azure pomocí podmíněného přístupu](../../active-directory/conditional-access/howto-conditional-access-policy-azure-management.md).

**Osvědčený postup**: Povolte přístup k portu jenom po schválení pracovního postupu.  
**Podrobnosti**: k uzamknutí příchozího provozu do virtuálních počítačů Azure můžete použít [přístup k virtuálnímu počítači za běhu v Azure Security Center](../../security-center/security-center-just-in-time.md) . tím se sníží vystavení útokům a zároveň se vám umožní snadný přístup k virtuálním počítačům v případě potřeby.

**Osvědčený postup**: udělení dočasných oprávnění k provádění privilegovaných úloh, které zabrání škodlivým nebo neoprávněným uživatelům získat přístup po vypršení platnosti oprávnění. Přístup se udělí jenom v případě, že ho uživatelé potřebují.  
**Podrobnosti**: použití přístupu za běhu v Azure AD Privileged Identity Management nebo v řešení třetí strany pro udělení oprávnění k provádění privilegovaných úloh.

Nulový vztah důvěryhodnosti je dalším vývojem v zabezpečení sítě. Stav kyberútokůmch jednotek organizacím umožňuje vzít v úvahu místo "předpokládat porušení", ale tento přístup by neměl být omezen. Nulové důvěryhodné sítě chrání podniková data a prostředky při současném zajištění, že organizace můžou vytvářet moderní pracoviště pomocí technologií, které umožní jakýmkoli způsobem produktivní práci zaměstnanců kdykoli a kdekoli.

## <a name="control-routing-behavior"></a>Řízení chování směrování
Při umístění virtuálního počítače do virtuální sítě Azure se virtuální počítač může připojit k jinému virtuálnímu počítači ve stejné virtuální síti, a to i v případě, že jsou ostatní virtuální počítače v různých podsítích. To je možné, protože kolekce systémových tras povolených ve výchozím nastavení umožňuje tento typ komunikace. Tyto výchozí trasy umožňují virtuálním počítačům ve stejné virtuální síti iniciovat připojení navzájem a s internetem (pouze pro odchozí komunikaci na Internet).

I když jsou výchozí systémové trasy užitečné pro řadu scénářů nasazení, existují situace, kdy chcete přizpůsobit konfiguraci směrování pro vaše nasazení. Adresu dalšího směrování můžete nakonfigurovat tak, aby se dosáhlo konkrétního cíle.

Doporučujeme nakonfigurovat [uživatelsky definované trasy](../../virtual-network/virtual-networks-udr-overview.md) při nasazení zařízení zabezpečení pro virtuální síť. O tom se dozvíme v pozdější části s názvem [zabezpečení vašich důležitých prostředků služby Azure jenom na vaše virtuální sítě](network-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks).

> [!NOTE]
> Trasy definované uživatelem nejsou požadovány a standardní systémové trasy obvykle fungují.
>
>

## <a name="use-virtual-network-appliances"></a>Použití zařízení virtuální sítě
Skupiny zabezpečení sítě a uživatelem definované směrování můžou poskytovat určitou míru zabezpečení sítě v síťové a transportní vrstvě [modelu OSI](https://en.wikipedia.org/wiki/OSI_model). V některých situacích ale budete chtít nebo potřebovat povolit zabezpečení na vysoké úrovni zásobníku. V takových situacích doporučujeme nasadit zařízení zabezpečení virtuální sítě poskytovaná partnery Azure.

Zařízení zabezpečení sítě Azure můžou poskytovat lepší zabezpečení, než poskytují ovládací prvky na úrovni sítě. Mezi možnosti zabezpečení sítě u zařízení zabezpečení virtuální sítě patří:


* Brána firewall
* Detekce vniknutí/prevence vniknutí
* Správa ohrožení zabezpečení
* Řízení aplikace
* Detekce anomálií na základě sítě
* Filtrování webů
* Antivirus
* Ochrana botnetu

K vyhledání dostupných zařízení zabezpečení virtuální sítě Azure použijte [Azure Marketplace](https://azure.microsoft.com/marketplace/) a vyhledejte "zabezpečení" a "zabezpečení sítě".

## <a name="deploy-perimeter-networks-for-security-zones"></a>Nasazení hraničních sítí pro zóny zabezpečení
[Hraniční síť](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) (označovaná také jako DMZ) je fyzický nebo logický segment sítě, který poskytuje další vrstvu zabezpečení mezi prostředky a internetem. Specializovaná zařízení pro řízení přístupu k síti na hraniční síti hraniční sítě umožňují pouze požadovaný provoz do vaší virtuální sítě.

Hraniční sítě jsou užitečné, protože můžete zaměřit správu řízení přístupu k síti, monitorování, protokolování a vytváření sestav na zařízeních na hranici vaší virtuální sítě Azure. Hraniční síť je místo, kde obvykle povolíte možnost prevence distribuovaného odepření služeb (DDoS), zjišťování vniknutí/systémy prevence vniknutí (ID/IP adresy), pravidla a zásady brány firewall, filtrování webu, antimalwarový software a další. Zařízení zabezpečení sítě sedí mezi Internetem a vaší virtuální sítí Azure a mají rozhraní v obou sítích.

I když se jedná o základní návrh hraniční sítě, existuje mnoho různých návrhů, jako je back-back, Tri-Home a s více adresami.

Na základě výše zmíněného konceptu s nulovým vztahem důvěryhodnosti doporučujeme zvážit použití hraniční sítě pro všechna nasazení s vysokým zabezpečením a zvýšit úroveň zabezpečení sítě a řízení přístupu pro vaše prostředky Azure. K zajištění další úrovně zabezpečení mezi prostředky a internetem můžete použít Azure nebo řešení třetí strany:

- Nativní ovládací prvky Azure [Azure firewall](/azure/firewall/overview) a [Firewall webových aplikací v Application Gateway](../../application-gateway/features.md#web-application-firewall) nabízejí základní zabezpečení s plně stavovou bránou firewall jako službu, integrovanou vysokou dostupnost, neomezenou škálovatelnost cloudu, filtrování plně kvalifikovaného názvu domény, podporu pro základní sady pravidel OWASP a jednoduché nastavení a konfiguraci.
- Nabídky třetích stran. Vyhledejte [Azure Marketplace](https://azuremarketplace.microsoft.com/) firewallu nové generace (NGFW) a další nabídky třetích stran, které poskytují známé nástroje zabezpečení a výrazně vylepšené úrovně zabezpečení sítě. Konfigurace může být složitější, ale nabídka třetí strany vám může umožnit používání stávajících možností a dovednosti.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Vyhněte se expozici Internetu pomocí vyhrazených propojení WAN
Řada organizací zvolila hybridní trasu IT. V případě hybridního IT jsou některé informační prostředky společnosti v Azure a jiné zůstávají místně. V mnoha případech jsou některé součásti služby spuštěné v Azure, zatímco ostatní komponenty zůstanou místní.

Ve scénáři hybridního IT je obvykle nějaký typ připojení mezi různými místy. Připojení mezi místními sítěmi umožňuje společnosti připojit své místní sítě k virtuálním sítím Azure. K dispozici jsou dvě řešení pro připojení mezi různými místy:

* Síť [VPN typu Site-to-site](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md). Jedná se o důvěryhodnou, spolehlivou a navázanou technologii, ale připojení probíhá přes Internet. Šířka pásma je omezená na maximum přibližně 1,25 GB/s. SÍŤ Site-to-Site VPN je v některých případech žádoucí volbou.
* **ExpressRoute Azure**. Pro připojení mezi místními sítěmi doporučujeme použít [ExpressRoute](../../expressroute/expressroute-introduction.md) . ExpressRoute umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes soukromé připojení zajišťované poskytovatelem připojení. Pomocí ExpressRoute můžete navázat připojení ke cloudovým službám Microsoftu, jako je Azure, Microsoft 365 a Dynamics 365. ExpressRoute je vyhrazené propojení WAN mezi vaším místním umístěním nebo poskytovatelem hostingu Microsoft Exchange. Vzhledem k tomu, že se jedná o připojení výpovědi, vaše data necestují přes Internet, takže se nezveřejňují potenciální rizika internetové komunikace.

Umístění připojení ExpressRoute může ovlivnit viditelnost kapacity, škálovatelnosti, spolehlivosti a síťového provozu v bráně firewall. Budete muset určit, kde ukončit ExpressRoute v existujících sítích (místních). Můžete:

- Ukončete mimo bránu firewall (paradigma hraniční sítě), pokud potřebujete mít přehled o provozu, pokud budete potřebovat, abyste mohli i nadále používat stávající postupy izolování datových center, nebo pokud zadáváte výhradně prostředky pro extranet v Azure.
- Ukončete v bráně firewall (paradigma rozšíření sítě). Toto je výchozí doporučení. Ve všech ostatních případech doporučujeme, abyste Azure vytvářely jako n-tého datacentra.

## <a name="optimize-uptime-and-performance"></a>Optimalizace provozu a výkonu
Pokud služba nefunguje, k informacím nelze získat informace. Pokud je výkon nekvalitní, že data jsou nepoužitelná, můžete zvážit, že data nebudou přístupná. Z hlediska zabezpečení je potřeba, abyste měli jistotu, že vaše služby mají optimální dobu provozu a výkon.

Oblíbená a efektivní metoda pro zvýšení dostupnosti a výkonu je vyrovnávání zatížení. Vyrovnávání zatížení je metoda distribuce síťového provozu mezi servery, které jsou součástí služby. Pokud máte například webové servery front-end jako součást vaší služby, můžete použít vyrovnávání zatížení k distribuci provozu mezi více front-endové webové servery.

Tato distribuce provozu zvyšuje dostupnost, protože pokud některý z webových serverů přestane být k dispozici, nástroj pro vyrovnávání zatížení zastaví odesílání provozu na tento server a přesměruje jej na servery, které jsou stále online. Vyrovnávání zatížení také pomáhá s výkonem, protože režie procesoru, sítě a paměti pro obsluhu požadavků je distribuována napříč všemi servery s vyrovnáváním zatížení.

Vyrovnávání zatížení doporučujeme využívat, kdykoli budete a podle potřeby pro vaše služby. Níže jsou uvedené scénáře jak na úrovni virtuální sítě Azure, tak i na globální úrovni společně s možnostmi vyrovnávání zatížení.

**Scénář**: máte aplikaci, která:

- Vyžaduje, aby žádosti ze stejné relace uživatele nebo klienta dostaly stejný back-end virtuální počítač. Příkladem jsou aplikace nákupních košíků a servery webové pošty.
- Přijímá jenom zabezpečené připojení, takže nešifrovaná komunikace na server není přijatelnou možností.
- Vyžaduje směrování nebo vyrovnávání zatížení pro různé back-endové servery několika požadavky HTTP na stejné dlouhotrvající připojení TCP.

**Možnost Vyrovnávání zatížení**: použijte [Azure Application Gateway](/azure/application-gateway/application-gateway-introduction), nástroj pro vyrovnávání zatížení webového provozu http. Application Gateway podporuje komplexní šifrování TLS a [ukončení protokolu TLS](/azure/application-gateway/application-gateway-introduction) v bráně. Webové servery pak můžou být nenáročné ze zátěže šifrování a dešifrování a přenos dat do back-endové servery je nešifrovaný.

**Scénář**: musíte vyrovnávat zatížení příchozích připojení z Internetu mezi servery nacházející se ve službě Azure Virtual Network. Scénáře jsou případy, kdy:

- Mít bezstavové aplikace, které přijímají příchozí požadavky z Internetu.
- Nevyžadují rychlé relace nebo snižování zátěže TLS. Relace typu Sticky jsou metoda používaná s vyrovnáváním zatížení aplikací k zajištění spřažení serveru.

**Možnost Vyrovnávání zatížení**: pomocí Azure Portal [Vytvořte externí nástroj pro vyrovnávání zatížení](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) , který šíří příchozí požadavky napříč více virtuálními počítači, aby se zajistila vyšší úroveň dostupnosti.

**Scénář**: musíte vyrovnávat zatížení připojení z virtuálních počítačů, které nejsou na internetu. Ve většině případů jsou připojení přijatá pro vyrovnávání zatížení iniciována zařízeními ve službě Azure Virtual Network, například SQL Server instancemi nebo interními webovými servery.   
**Možnost Vyrovnávání zatížení**: pomocí Azure Portal [vytvořit interní nástroj pro vyrovnávání zatížení](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) , který šíří příchozí požadavky napříč více virtuálními počítači, aby se zajistila vyšší úroveň dostupnosti.

**Scénář**: budete potřebovat globální vyrovnávání zatížení, protože:

- Máte cloudové řešení, které je široce distribuované napříč několika oblastmi a vyžaduje nejvyšší možnou úroveň provozu (dostupnost).
- Potřebujete nejvyšší možnou úroveň dostupnosti, abyste se ujistili, že je vaše služba dostupná i v případě, že celé datové centrum nebude k dispozici.

**Možnost Vyrovnávání zatížení**: použijte Azure Traffic Manager. Traffic Manager umožňuje vyrovnávat zatížení připojení k vašim službám na základě umístění uživatele.

Například pokud uživatel odešle požadavek na službu z EU, připojení se přesměruje na vaše služby umístěné v datovém centru EU. Tato část Traffic Manager globálního vyrovnávání zatížení pomáhá zlepšit výkon, protože připojení k nejbližšímu datovému centru je rychlejší než připojení k datovým centrům, které jsou daleko pryč.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>Zakázat přístup RDP/SSH k virtuálním počítačům
Virtuální počítače Azure je možné kontaktovat pomocí [protokol RDP (Remote Desktop Protocol)](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) a protokolu [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH). Tyto protokoly umožňují správu virtuálních počítačů ze vzdálených umístění a jsou standardem v datacentrovém computingu.

Potenciální potíže se zabezpečením pomocí těchto protokolů přes Internet můžou útočníci využít techniky [hrubou silou](https://en.wikipedia.org/wiki/Brute-force_attack) k získání přístupu k virtuálním počítačům Azure. Potom co útočníci získají přístup, můžou použít váš virtuální počítač jako spouštěcí bod pro narušení dalších počítačů ve virtuální síti, nebo dokonce zaútočit na síťová zařízení mimo Azure.

Doporučujeme zakázat přímý přístup protokolu RDP a SSH k virtuálním počítačům Azure z Internetu. Po zakázání přímého přístupu RDP a SSH z internetu máte k dispozici další možnosti, které můžete použít pro přístup k těmto virtuálním počítačům pro vzdálenou správu.

**Scénář**: umožňuje jednomu uživateli připojit se k virtuální síti Azure přes Internet.   
**Možnost**: [síť VPN typu Point-to-site](/azure/vpn-gateway/vpn-gateway-point-to-site-create) je dalším termínem pro připojení klienta nebo serveru VPN pro vzdálený přístup. Po navázání připojení typu Point-to-site se uživatel může pomocí protokolu RDP nebo SSH připojit k jakýmkoli virtuálním počítačům umístěným ve virtuální síti Azure, ke které se uživatel připojil přes síť VPN typu Point-to-site. Tím se předpokládá, že uživatel má oprávnění k přístupu k těmto virtuálním počítačům.

SÍŤ VPN typu Point-to-site je bezpečnější než přímé připojení RDP nebo SSH, protože před připojením k virtuálnímu počítači musí uživatel ověřit dvakrát. Nejdřív musí uživatel ověřit (a být autorizován), aby navázal připojení VPN typu Point-to-site. Za druhé musí uživatel ověřit (a být autorizován) vytvořit relaci RDP nebo SSH.

**Scénář**: Umožněte uživatelům ve vaší místní síti připojení k virtuálním počítačům ve službě Azure Virtual Network.   
**Možnost**: síť [site-to-Site VPN](/azure/vpn-gateway/vpn-gateway-site-to-site-create) připojuje celou síť k jiné síti přes Internet. K připojení místní sítě k virtuální síti Azure můžete použít síť VPN typu Site-to-site. Uživatelé na místní síti se připojují pomocí protokolu RDP nebo SSH přes připojení VPN typu Site-to-site. Nemusíte umožňovat přímý přístup přes protokol RDP nebo SSH přes Internet.

**Scénář**: použijte vyhrazené propojení WAN k poskytnutí podobných funkcí jako síť VPN typu Site-to-site.   
**Možnost**: použijte [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/). Poskytuje funkce podobné síti VPN typu Site-to-site. Hlavními rozdíly jsou:

- Vyhrazené propojení WAN neprojde internetem.
- Vyhrazená propojení WAN jsou obvykle spolehlivější a fungují lépe.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>Zabezpečení důležitých prostředků služby Azure jenom na vaše virtuální sítě
Pomocí koncových bodů služby virtuální sítě můžete v rámci přímého připojení zvětšit privátní adresní prostor virtuální sítě a identitu vaší virtuální sítě do služeb Azure. Koncové body umožňují svázat vaše důležité prostředky služeb Azure pouze s vašimi virtuálními sítěmi. Provoz z vaší virtuální sítě do služby Azure vždycky zůstane v síti Microsoft Azure páteřní sítě.

Koncové body služby poskytují následující výhody:

- **Zlepšení zabezpečení prostředků služeb Azure:** Pomocí koncových bodů služby je možné svázat prostředky služeb Azure s vaší virtuální sítí. Svázání prostředků služeb s virtuální sítí poskytuje lepší zabezpečení, protože zcela eliminuje přístup k prostředkům z veřejného internetu a povoluje jen provoz z vaší virtuální sítě.
- **Optimální směrování provozu služeb Azure z vaší virtuální sítě**: jakékoli trasy ve vaší virtuální síti, která vynucuje internetový provoz na místních nebo virtuálních zařízeních, označovaná jako vynucené tunelování, taky vynutit, aby provoz služeb Azure převzal stejnou trasu jako internetový provoz. Koncové body služby poskytují optimální směrování provozu Azure.

  Koncové body vždy přebírají provoz služby přímo z vaší virtuální sítě do služby v páteřní síti Azure. Provoz v páteřní síti Azure vám umožní pokračovat v auditování a monitorování odchozího internetového provozu z vašich virtuálních sítí prostřednictvím vynuceného tunelování, aniž by to ovlivnilo provoz služeb. Přečtěte si další informace o [trasách definovaných uživatelem a vynuceném tunelování](../../virtual-network/virtual-networks-udr-overview.md).

- **Jednoduché nastavení s nižšími nároky na správu**: ve virtuálních sítích už nepotřebujete vyhrazené veřejné IP adresy pro zabezpečení prostředků Azure prostřednictvím brány firewall protokolu IP. K nastavení koncových bodů služby se nevyžaduje překlad adres ani zařízení brány. Koncové body služby se konfigurují pouhým kliknutím na podsíť. Neexistují žádné další nároky na údržbu koncových bodů.

Další informace o koncových bodech služby a službách a oblastech Azure, které jsou pro koncové body služby k dispozici pro, najdete v tématu [koncové body služby virtuální sítě](../../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="next-steps"></a>Další kroky
V článku [osvědčené postupy a vzory zabezpečení Azure](best-practices-and-patterns.md) najdete v tématu Doporučené postupy zabezpečení pro použití při navrhování, nasazování a správě cloudových řešení pomocí Azure.
