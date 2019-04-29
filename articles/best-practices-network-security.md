---
title: Osvědčené postupy zabezpečení sítě Azure | Dokumentace Microsoftu
description: Seznamte se s některými z klíčových funkcí dostupných v Azure k usnadnění vytváření zabezpečených síťových prostředí
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: d169387a-1243-4867-a602-01d6f2d8a2a1
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: 74a46c7788b0a0dc729ab977489ed6d97a387a6e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60619380"
---
# <a name="microsoft-cloud-services-and-network-security"></a>Zabezpečení cloudových služeb a sítě společnosti Microsoft
Cloudovým službám Microsoftu poskytovat vysoce škálovatelné služby a infrastruktury, možnosti na podnikové úrovni a spoustu možností pro hybridní připojení. Zákazníci si mohou vybrat pro přístup k těmto službám prostřednictvím Internetu nebo s využitím Azure ExpressRoute, které poskytuje připojení privátní sítě. Platforma Microsoft Azure umožňuje zákazníkům bezproblémově rozšířit svoji infrastrukturu do cloudu a vytvářet vícevrstvé architektury. Kromě toho můžete povolit třetím stranám poznat široké možnosti tím, že nabízí zabezpečení služeb a virtuálních zařízení. Tento dokument white paper poskytuje přehled o zabezpečení a architektury problémy, které by měli zákazníci zvážit při používání cloudových služeb Microsoftu, získat přístup přes ExpressRoute. Věnuje se také vytváření bezpečnější služeb ve virtuálních sítích Azure.

## <a name="fast-start"></a>Rychlý start
Následující graf logiky můžete směrovat na konkrétní příklad mnoho postupů zabezpečení, která je k dispozici s platformou Azure. Stručná referenční příručka najdete příklad, který nejlépe vyhovuje vašemu případu. Rozšířené vysvětlení pokračujte ve čtení prostřednictvím papíru.
[![0]][0]

[Příklad 1: Vytvářejte hraniční síti (označované také jako DMZ, demilitarizovaná zóna nebo monitorovaná podsíť) k ochraně aplikací s použitím skupin zabezpečení sítě (Nsg).](#example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs)</br>
[Příklad 2: Vytvořte hraniční síť k ochraně aplikací s bránou firewall a skupin zabezpečení sítě.](#example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs)</br>
[Příklad 3: Vytvářejte hraniční síti můžete chránit sítím pomocí brány firewall, trasy definované uživatelem (UDR) a skupiny zabezpečení sítě.](#example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg)</br>
[Příklad 4: Přidáte hybridní připojení pomocí virtuální privátní sítě site-to-site a virtuální zařízení (VPN).](#example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br>
[Příklad 5: Přidáte hybridní připojení site-to-site, brána Azure VPN.](#example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway)</br>
[Příklad 6: Přidáte hybridní připojení s ExpressRoute.](#example-6-add-a-hybrid-connection-with-expressroute)</br>
Příklady pro přidání připojení mezi virtuálními sítěmi, vysokou dostupnost a řetězení služeb se přidá k tomuto dokumentu průběhu několika následujících měsíců.

## <a name="microsoft-compliance-and-infrastructure-protection"></a>Ochrana Microsoft dodržování předpisů a infrastruktury
Pomáhají organizacím, které jsou v souladu s národní, regionální a průmyslové požadavky, kterými se řídí shromažďování a používání dat jednotlivců, Microsoft nabízí víc než 40 certifikací a atestací. Nejkomplexnější sada všech poskytovatelů cloudových služeb.

Další informace najdete v tématu informace o dodržování předpisů na [Microsoft Trust Center][TrustCenter].

Microsoft nabízí komplexní přístup k ochraně potřebných ke spuštění služeb global services vysoce škálovatelné cloudové infrastruktury. Cloudová infrastruktura Microsoftu zahrnuje hardware, software, sítě a pro správu a provozní personál, kromě fyzických datových center.

![2]

Tento přístup poskytuje více bezpečný základ pro zákazníky, kteří k nasazení svých služeb v cloudu Microsoftu. Dalším krokem je pro zákazníky, kteří pro návrh a tvorbu architektury zabezpečení k ochraně těchto služeb.

## <a name="traditional-security-architectures-and-perimeter-networks"></a>Architektury zabezpečení a hraniční sítě
I když společnost Microsoft se investuje silně při ochraně cloudové infrastruktury, musí zákazníci taky chránit své cloudové služby a skupiny prostředků. S více vrstvami přístup k zabezpečení poskytuje nejlepší ochranu. Zóny zabezpečení hraniční sítě chrání prostředkům v interní síti z nedůvěryhodné sítě. Hraniční síť odkazuje na okraje nebo části sítě, které se nacházejí mezi Internetem a chráněné podnikové infrastruktury IT.

V typických podnikových sítích se základní infrastruktury silně přidá na perimetry, s víc vrstvami zabezpečení zařízení. Hranice každou vrstvu se skládá ze zařízení a body vynucení zásad. Každá vrstva může obsahovat kombinaci následujících síťových zařízení zabezpečení: brány firewall, ochrany před únikem informací s cílem odepření služby (DoS), zjišťování neoprávněných vniknutí nebo systémy ochrany (IDS/IPS) a zařízení VPN. Vynucení zásad můžou mít podobu zásady brány firewall, seznamy řízení přístupu (ACL) nebo konkrétní směrování. První linie obrany v síti, přímo přijímat příchozí provoz z Internetu, je kombinací těchto mechanismů k zabránění útokům a škodlivý provoz zároveň vám umožní dál oprávněné požadavky na síť. Tento provoz se směruje přímo k prostředkům v hraniční síti. Tento prostředek může potom "mluvit" k prostředkům v síti, přechodem mezi protokoly další hranice pro ověření hlubší první. Nejkrajnější vrstvy se nazývá hraniční síti, protože tuto část sítě je přístupný z Internetu, obvykle s určitou formu ochrany na obou stranách. Následující obrázek znázorňuje příklad hraniční síti jedinou podsítí v podnikové síti, s dvěma hranice zabezpečení.

![3]

Existuje mnoho architektur používaný k implementaci hraniční síti. Tyto architektury může být v rozsahu od nástroj pro vyrovnávání zatížení jednoduché do více podsítích hraniční sítě různých mechanismů na každou hranici bloku provoz a chránit hlubší vrstvy v podnikové síti. Jak je sestaven hraniční sítě závisí na konkrétním potřebám organizace a jeho celkového odolnost vůči rizikům.

Jak zákazníci přesunout své úlohy do veřejných cloudů, je důležité pro podporu podobnými funkcemi pro architekturu hraniční sítě v Azure a splňovat požadavky na zabezpečení a dodržování předpisů. Tento dokument obsahuje pokyny o tom, jak zákazníci mohou vytvářet prostředí zabezpečené sítě v Azure. Se zaměřuje na hraniční síti, ale také zahrnuje komplexní diskuzi o aspektech zabezpečení sítě. Na následující otázky informovat této diskuse:

* Jak lze sestavit do hraniční sítě v Azure?
* Jaké jsou některé funkce Azure, které umožňují vytvářet hraniční síti?
* Jak se dají chránit back endové úlohy?
* Jak se řídí internetové komunikace do úlohy v Azure?
* Jak místní sítě se dají chránit z nasazení v Azure?
* Kdy je vhodné funkce nativní zabezpečení Azure používat versus zařízení třetích stran nebo služby?

Následující diagram znázorňuje různé vrstvy zabezpečení, které Azure poskytuje zákazníkům. Tyto vrstvy jsou nativní v samotné platformy Azure a uživatelsky definované funkce:

![4]

Příchozí provoz z Internetu, DDoS Azure pomáhá chránit před útoky ve velkém měřítku na Azure. Další vrstva je uživatelem definovaný veřejné IP adresy (koncových bodů), které se používají k určení, jaký provoz můžete předat prostřednictvím cloudové služby k virtuální síti. Nativní Azure virtual izolace sítě zajistí, že naprosté izolaci od všech ostatních sítí a že provoz pouze prochází nakonfigurovaná uživatelem cesty a metody. Tyto cesty a metody jsou další vrstvy, kde skupin zabezpečení sítě, směrování definovaného uživatelem a síťových virtuálních zařízení slouží k vytvoření hranic zabezpečení k ochraně nasazení aplikací do chráněné síti.

Další část obsahuje základní informace o virtuálních sítích Azure. Tyto virtuální sítě jsou vytvořeny pomocí zákazníků a jsou co jejich nasazených úloh jsou připojené k. Virtuální sítě jsou základem všem síťovým funkcím zabezpečení vyžadované k vytvoření hraniční síti k ochraně zákaznických nasazení v Azure.

## <a name="overview-of-azure-virtual-networks"></a>Přehled služby Azure virtual networks
Předtím, než k virtuálním sítím Azure můžete získat přenosy z Internetu, existují dvě vrstvy zabezpečení platformy Azure přináší:

1.    **Služba DDoS protection**: Služba DDoS protection je vrstva Azure fyzické sítě, které chrání samotné platformy Azure ve velkém měřítku útoky z Internetu. Tyto útoky pomocí více uzlů "bot" při pokusu o zahlcovat služby sítě Internet. Azure nabízí robustní síť ochrany před útoky DDoS na všech připojení příchozí, odchozí a Azure různé oblasti. Tato vrstva před útoky DDoS protection nemá žádné atributy konfigurovat uživatele a není přístupná pro zákazníka. Vrstva před útoky DDoS protection chrání Azure jako platformu pro velké objemy před útoky, také sleduje odesílací vázané provozu a provoz mezi Azure oblasti. Pomocí síťových virtuálních zařízení ve virtuální síti, je možné nakonfigurovat další úrovně odolnosti zákazník proti menší škálování útoku, který nemá dojít platformy úroveň ochrany. Příklad před útoky DDoS v akci; Pokud internetový IP adresa byla napadených ve velkém měřítku útoky DDoS, Azure by zjišťovat zdroje útoky a procházení problematický provozu předtím, než je dosaženo konečného určené. V téměř všech případech napadené koncového bodu není ovlivněn útoku. Ve výjimečných případech, že je koncový bod vliv na žádný provoz má vliv na ostatní koncové body, pouze na napadený koncový bod. Proto další zákazníky a služby zobrazoval žádný dopad z tohoto útoku. Je důležité si uvědomit, že Azure DDoS pouze hledá ve velkém měřítku útoky. Je možné, že konkrétní služby může být přetížena před překročení prahové hodnoty platformy úroveň ochrany. Například webu na jednom serveru služby IIS A0, může být převedeno do režimu offline podle s útoky DDoS předtím, než úroveň platformy Azure DDoS protection registrována hrozbu.

2.  **Veřejné IP adresy**: Veřejné IP adresy (povolené prostřednictvím koncových bodů služby, veřejné IP adresy, Application Gateway a další funkce Azure, které se veřejné IP adresy do Internetu směruje na váš prostředek) umožňují cloudové služby nebo skupiny prostředků s veřejnou IP adresu Internet adresy a porty, které jsou vystaveny. Koncový bod používá překládání adres (NAT) Pokud chcete směrovat provoz na interní adresu a port ve službě Azure virtual network. Tato cesta je hlavní způsob, jak pro externí přenos dat do virtuální sítě. Veřejné IP adresy se dají konfigurovat určit, jaký provoz je předáno a jak a kde je přeložen do virtuální sítě.

Po přenosy přicházejí virtuální sítě, je mnoho funkcí, které souvisejí. Virtuální sítě Azure jsou základem pro zákazníky, kteří připojovat své úlohy a kdy se použije základní zabezpečení na úrovni sítě. Privátní sítě (virtuální sítě překrytí) v Azure je pro zákazníky s následující funkce a vlastnosti:

* **Izolace provozu**: Virtuální síť je hranice izolace provozu na platformě Azure. Virtuální počítače (VM) v jedné virtuální síti nemůže komunikovat přímo do virtuálních počítačů v jiné virtuální síti, i když obě virtuální sítě jsou vytvořeny tentýž zákazník. Izolace je důležité vlastnost, která zajišťuje virtuální počítače zákazníků a komunikace zůstávají privátní virtuální sítě.

>[!NOTE]
>Izolace provozu odkazuje pouze na provoz *příchozí* k virtuální síti. Ve výchozím nastavení odchozího provozu z virtuální sítě do Internetu je povoleno, ale můžete zabránit v případě potřeby pomocí skupin zabezpečení sítě.
>
>

* **Vícevrstvá topologie**: Virtuální sítě umožňují zákazníkům definovat vícevrstvé topologie přidělením podsítě a určování samostatné adresních prostorů pro různé prvky nebo "vrstvy" jejich úloh. Tyto logické seskupení topologie zákazníkům umožnit, aby definoval zásady různý přístup na základě typů úloh a také řídit tok mezi vrstvami.
* **Připojení mezi místními sítěmi**: Zákazníci můžou navázat připojení mezi místními sítěmi mezi virtuální sítí a několika místními lokalitami nebo jiným virtuálním sítím v Azure. Zákazníci mohou používat k vytvoření připojení typu VNet Peering, Azure VPN Gateway, síťová virtuální zařízení třetích stran nebo ExpressRoute. Azure podporuje standardní protokoly IPsec/IKE a privátní připojení ExpressRoute sítě site-to-site (S2S) VPN.
* **Skupina zabezpečení sítě** umožňuje zákazníkům vytvářet pravidla (ACL) na požadované úrovni členitosti: Síťová rozhraní, jednotlivým virtuálním počítačům nebo virtuální podsítě. Zákazníci můžou řídit přístup povolením nebo odepřením komunikace mezi úlohami v rámci virtuální sítě, ze systémů v sítích zákazníků prostřednictvím připojení mezi místními sítěmi, nebo nasměrovat internetovou komunikaci.
* **Uživatelem definovaná TRASA** a **předávání IP adres** umožňují zákazníkům definovat komunikační trasy mezi různé vrstvy v rámci virtuální sítě. Zákazníky můžete nasadit bránu firewall, IDS/IPS a dalších virtuálních zařízení a směrovat síťový provoz přes tyto zabezpečovací zařízení k vynucení zásad hranice zabezpečení, auditování a kontroly.
* **Síťová virtuální zařízení** na webu Azure Marketplace: Zabezpečovací zařízení, jako jsou brány firewall, nástroje pro vyrovnávání zatížení a IDS/IPS jsou dostupné v Tržišti Azure Marketplace a Galerie Imagí virtuálních počítačů. Zákazníci mohou nasadit tato zařízení do svých virtuálních sítí a konkrétně v jejich hranice zabezpečení (včetně podsítě hraniční síť) k dokončení prostředí vícevrstevného zabezpečení sítě.

Pomocí těchto funkcí a možností je jeden příklad, jak může zkonstruovat hraniční síťové architektury v Azure v následujícím diagramu:

![5]

## <a name="perimeter-network-characteristics-and-requirements"></a>Hraniční síť charakteristiky a požadavky
Hraniční síť je front-endu sítě přímo propojení komunikaci z Internetu. Příchozí pakety jakým způsobem se předávají prostřednictvím zabezpečovací zařízení, jako jsou brány firewall, ID a IP adresy, před dosažením back-end serverů. Pakety směřující na Internet z úlohy může také probíhat přes zabezpečovací zařízení v hraniční síti pro vynucení zásad, kontrolu a auditování, před opuštěním sítě. Kromě toho hraniční síti může být hostitelem bran VPN mezi různými místy mezi místní sítí a virtuálních sítích zákazníků.

### <a name="perimeter-network-characteristics"></a>Vlastnosti hraniční sítě
Odkazování na předchozím obrázku, jsou některé z vlastností dobré hraniční síti následujícím způsobem:

* Přístupem k Internetu:
  * Hraniční síť v samotné podsítě je přístup k Internetu, přímo komunikovat s Internetem.
  * Veřejné IP adresy, virtuální IP adresy a/nebo koncové body služby předat internetový provoz do front-endové sítě a zařízení.
  * Příchozí provoz z Internetu prochází zabezpečení zařízení před dalším prostředkům ve front-endové síti.
  * Pokud je povolené Výstupní zabezpečení, provoz prochází přes zařízení zabezpečení, jako poslední krok před předáním k Internetu.
* Chráněná síťová:
  * Neexistuje žádná cesta s přímým přístupem z Internetu do základní infrastruktury.
  * Kanály a základní infrastruktury musí procházet přes zařízení zabezpečení, jako jsou skupiny zabezpečení sítě, brány firewall nebo zařízení VPN.
  * Jiná zařízení nesmí přemostili Internet a základní infrastruktury.
  * Zabezpečení zařízení na straně Internetu a chráněné síti, kterým čelí hranice hraniční síti (například dvě brány firewall ikony je vidět na předchozím obrázku) ve skutečnosti může být jeden virtuální zařízení s pravidly rozlišené nebo rozhraní Každá hranice. Například jedno fyzické zařízení, logicky oddělené, zpracovávání zatížení pro obě hranice hraniční síti.
* Další běžné postupy a omezení:
  * Úlohy nesmí uložit důležité obchodní informace.
  * Přístup a aktualizace konfigurace hraniční sítě a nasazení jsou omezené jenom oprávněným správcům.

### <a name="perimeter-network-requirements"></a>Požadavky na hraniční síť
Pokud chcete povolit tyto charakteristiky, postupujte podle následujících pokynů na požadavky na virtuální síť k implementaci úspěšné hraniční síti:

* **Architektura podsítě:** Zadejte virtuální síť tak, aby celé podsítě je vyhrazená jako hraniční síti, která je oddělená od jiných podsítí ve stejné virtuální síti. Toto oddělení zajišťuje přenos dat mezi hraniční sítí a dalších toků úrovně interní nebo privátní podsítě přes bránu firewall či IDS/IPS virtuálního zařízení.  Trasy definované uživatelem na hranici podsítě je potřeba předávat provoz do virtuálního zařízení.
* **NSG:** Samotné podsítě hraniční sítě by měl být otevřený, aby umožňoval komunikaci s Internetem, ale to neznamená, že zákazníci by měl být obcházení skupin zabezpečení sítě. Použijte běžné postupy zabezpečení pro minimalizaci povrchy síť přístupný z Internetu. Zamknout rozsahy vzdálených adres povolený přístup k nasazení nebo konkrétní aplikační protokoly a porty, které jsou otevřené. Mohou nastat okolnosti, ale, ve kterých není možné kompletní uzamčení. Například pokud zákazníci mají externí web v Azure, hraniční síti by měl povolit příchozí webové žádosti z jakékoli veřejné IP adresy, ale měli otvírat jenom webovými porty aplikace: TCP na portu 80 a/nebo TCP na portu 443.
* **Směrovací tabulky:** Samotné podsítě hraniční sítě by měl být schopný komunikovat přímo k Internetu, ale by nemělo umožňovat přímá komunikace do a z back end nebo místní sítě bez nutnosti kontaktovat zařízení brány firewall nebo zabezpečení.
* **Konfigurace zabezpečení zařízení:** Aby se směrovaly a kontrolu paketů mezi hraniční sítí a zbytek chráněné sítě, zařízení zabezpečení, jako jsou brány firewall, ID a IP adresy může být zařízení s více adresami. Mohou mít oddělených síťových adaptérů pro hraniční síť a back endové podsítě. Síťové adaptéry v hraniční síti komunikovat přímo do a z Internetu, s odpovídající skupiny zabezpečení sítě a směrovací tabulky hraniční sítě. Připojení k back endové podsítě síťové karty mají více omezený skupin zabezpečení sítě a směrovací tabulky odpovídající back endové podsítě.
* **Zabezpečení funkce zařízení:** Zabezpečovací zařízení nasazený v hraniční síti obvykle provádět následující funkce:
  * Brána firewall: Vynucení pravidel brány firewall nebo zásady řízení přístupu pro příchozí požadavky.
  * Detekce hrozeb a ochrany před únikem informací: Zjištění a minimalizace rizik útoků z Internetu.
  * Auditování a protokolování: Zachování podrobné protokoly auditování a analýz.
  * Reverzní proxy server: Přesměrování příchozích požadavků na odpovídající back-end servery. Toto přesměrování zahrnuje mapování a překladu cílové adresy na front-endu zařízení obvykle brány firewall, adres back endového serveru.
  * Dopředné proxy: Poskytuje NAT a provádění auditování pro komunikace iniciovaná v rámci virtuální sítě do Internetu.
  * Směrovač: Předávání mezi podsítěmi a příchozí provoz ve virtuální síti.
  * Zařízení VPN: Funguje jako brány VPN mezi různými místy pro připojení VPN mezi různými místy mezi zákazníka místních sítí a virtuální sítě Azure.
  * Server sítě VPN: Přijímá žádosti klientů VPN připojení k virtuálním sítím Azure.

> [!TIP]
> Oddělovat následujících dvou skupin: jednotlivcům oprávnění k přístupu k zařízení zabezpečení hraniční síť i jednotlivce oprávnění jako správci vývoje, nasazení nebo provozu aplikací. Pokud oddělíte tyto skupiny umožňuje oddělení povinností a zabrání jedné osobě obcházení zabezpečení aplikací a kontrolní mechanismy zabezpečení sítě.
>
>

### <a name="questions-to-be-asked-when-building-network-boundaries"></a>Otázky, které se zobrazí výzva při vytváření ohraničení sítě
V této části Pokud není výslovně uvedeno, pojem "sítě" představuje privátní Azure virtual networks vytvořené správcem předplatného. Výraz neodkazuje na základní fyzické sítě v rámci Azure.

Virtuální sítě Azure se navíc často používají k rozšíření tradičních místních sítí. Je možné začlenit site-to-site nebo ExpressRoute hybridní sítě řešení s využitím architektury hraniční sítě. Tento odkaz hybridní je důležitým aspektem, který při vytváření hranic zabezpečení sítě.

Následující tři otázky jsou důležité pro odpověď při vytváření sítě s hraniční sítí a několika hranice zabezpečení.

#### <a name="1-how-many-boundaries-are-needed"></a>(1) kolik hranice jsou potřeba?
První rozhodnutí je rozhodnout, kolik hranice zabezpečení jsou potřeba v daném scénáři:

* Jedna hranice: Jeden na front-endu hraniční síti, mezi virtuální sítí a Internetem.
* Dva hranice: Jeden na straně Internetu do hraniční sítě a další mezi hraniční podsíti a back endové podsítě ve virtuálních sítích Azure.
* Tři hranice: Na straně Internetu do hraniční sítě mezi hraniční sítí a back endové podsítě a mezi back endové podsítě a v místní síti.
* Hranice N: Proměnné číslo. V závislosti na požadavcích zabezpečení neexistuje žádné omezení počtu hranice zabezpečení, které mohou být použity v dané síti.

Počet a typ hranice potřeba se liší podle vaší společnosti odolnosti vůči rizikům a konkrétní scénář se implementuje. Toto rozhodnutí je často proveden společně s několika skupinami v rámci organizace, včetně často tým rizik a dodržování předpisů, síť a tým platformy a vývojovým týmem aplikace. Uživatelé se znalostí v oblasti zabezpečení, zahrnutých dat a technologie, které se používají by měl mít údajem v tomto rozhodnutí zajistit postoj zabezpečení pro každou implementaci.

> [!TIP]
> Použijte nejmenší počet hranic, které splňují požadavky na zabezpečení pro dané situaci. Více hranic, operací a řešení problémů může být obtížnější, a také součástí správy více hranic zásad časem režie na správu. Však nedostatečné hranice zvýšit rizika. Jak najít rovnováhu je velmi důležité.
>
>

![6]

Předchozí obrázek ukazuje široký přehled o tři zabezpečení hraniční síti. Hranice jsou mezi hraniční síti a Internetu, Azure front-endu a back-end privátní podsítě a Azure back endové podsítě a místní podnikové sítě.

#### <a name="2-where-are-the-boundaries-located"></a>2), kde se nacházejí hranicích?
Jakmile se rozhodla počet hranic, where pro jejich implementaci je dalším kritériem. Existují obecně tři možnosti:

* Pomocí internetové zprostředkující služby (například cloudové firewallu webových aplikací, které není probírané v tomto dokumentu)
* Používat nativní funkce a/nebo síťových virtuálních zařízení v Azure
* V místní síti pomocí fyzických zařízení

Možnosti v sítích čistě Azure, jsou nativní funkce Azure (například Azure nástroje pro vyrovnávání zatížení) nebo virtuální síťová zařízení od rozsáhlý partnerský ekosystém Azure (například brány firewall Check Point).

Pro potřeby hranici mezi Azure a v místní síti zařízení zabezpečení může být na obou stranách připojení (nebo obě strany). Proto musí rozhodnutí provedených na umístění umístit zabezpečení zařízení.

Na předchozím obrázku Internetu do hraniční sítě a hranice front-to-back-end jsou zcela obsažen v rámci Azure a musí být nativní funkce Azure nebo síťových virtuálních zařízení. Zabezpečení zařízení na hranici mezi Azure (back endové podsítě) a v podnikové síti může být buď na straně Azure nebo na straně v místním nebo dokonce kombinaci zařízení na obou stranách. Může být významné výhody a nevýhody jednu z možností, které musíte vzít v úvahu vážně.

Například pomocí existující ozubeného kola fyzického zabezpečení na straně místní sítě má výhodu, že je potřeba žádné nové zařízení. Potřebuje pouze změny konfigurace. Nevýhodou, ale je, že veškerý provoz musí vrátit z Azure do místní sítě, která má být zobrazeno zabezpečení zařízení. Proto provoz Azure do Azure může způsobit velkou latenci a vliv na výkon aplikace a uživatelské prostředí, pokud bylo nuceno zpět do místní sítě pro vynucení zásad zabezpečení.

#### <a name="3-how-are-the-boundaries-implemented"></a>(3), jak se implementují hranice?
Každá hranice zabezpečení bude mít pravděpodobně požadavků na různé možnosti (například ID a pravidla brány firewall na straně Internetu hraniční síti, ale pouze na seznamy ACL mezi hraniční sítí a back endové podsítě). Rozhodování o tom, na kterém zařízení (nebo kolik zařízení) použijte závisí na požadavcích scénáře a zabezpečení. Příklady 1, 2 a 3 v následující části popisují některé možnosti, které by bylo možné použít. Kontrola funkce nativní síť Azure a k dispozici v Azure z partnerský ekosystém zařízení zobrazuje řadu možností pro téměř jakýkoli scénář vyřešit.

Dalším kritériem při implementaci klíče je postup pro připojení k místní síti pomocí Azure. Můžete použít Azure virtuální bránou nebo síťové virtuální zařízení? Tyto možnosti jsou popsány podrobněji v následující části (příklady 4, 5 a 6).

Kromě toho může být potřeba provozu mezi virtuálními sítěmi v rámci Azure. Tyto scénáře se přidají v budoucnu.

Jakmile budete znát odpovědi na otázky předchozí [rychlý Start](#fast-start) části můžete identifikovat, které příklady jsou pro daný scénář nejvhodnější.

## <a name="examples-building-security-boundaries-with-azure-virtual-networks"></a>Příklady: Vytvoření hranic zabezpečení s virtuálními sítěmi Azure
### <a name="example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs"></a>Příklad 1, sestavení hraniční síti do zvýšit ochranu aplikací pomocí skupin zabezpečení sítě
[Zpět na rychlý start](#fast-start) | [podrobné pokyny v tomto příkladu pro sestavení][Example1]

[![7]][7]

#### <a name="environment-description"></a>Popis prostředí
V tomto příkladu je předplatné, které obsahuje následující prostředky:

- Jedna skupina prostředků
- Virtuální síť se dvěma podsítěmi: "FrontEnd" a "Back-end"
- Skupina zabezpečení sítě, které platí pro obě podsítě
- Windows server, který představuje aplikační server web (dále jen "IIS01")
- Dva servery Windows, které představují servery back endové aplikace ("AppVM01", "AppVM02")
- Windows server, který představuje server DNS ("DNS01")
- Veřejné IP adresy přidružené k serveru webové aplikace

Skripty a šablony Azure Resource Manageru, najdete v článku [podrobné pokyny pro sestavení][Example1].

#### <a name="nsg-description"></a>Popis skupiny zabezpečení sítě
V tomto příkladu je skupinu NSG vytvořené a pak načíst šest pravidlům.

> [!TIP]
> Obecně platí by měl vytvářet specifická pravidla "Povolit" nejprve, za nímž následuje obecnější pravidla "Zakázat". Uvedenou prioritou určí, která pravidla se vyhodnocují první. Po nalezení provozu použít na příslušné pravidlo, žádná další pravidla se vyhodnocují. Pravidla skupiny zabezpečení sítě můžete použít v buď příchozí nebo odchozí směr (z hlediska podsíť).
>
>

Deklarativní byly sestaveny pro příchozí provoz následující pravidla:

1. Interní DNS provoz (port 53) je povolený.
2. Je povolený provoz protokolu RDP (portu 3389) z Internetu do libovolného virtuálního počítače.
3. Je povolený provoz HTTP (port 80) z Internetu webový server (IIS01).
4. Je povolen veškerý provoz (všechny porty) z IIS01 k AppVM1.
5. Veškerý provoz (všechny porty) z Internetu do celé virtuální sítě (obě podsítě) byl odepřen.
6. Veškerý provoz (všechny porty) z front-endové podsítě do back endové podsítě je odepřen.

Pomocí těchto pravidel vázán ke každé podsíti, pokud požadavek HTTP byl příchozí z Internetu webový server, jak pravidla 3 (Povolit) a 5 (zabránění) se vztahují. Ale vzhledem k tomu, že pravidlo 3 má vyšší prioritu, pouze by použít, a pravidla 5 by souvisejí. Požadavek HTTP se proto by bylo možné webový server. Pokud tento stejný provoz se snažil to spojit se serverem DNS01, pravidlo 5 (zabránění) by se použít jako první, a provoz nebude možné předat serveru. Pravidla 6 (zabránění) blokuje front-endové podsítě z komunikujících do back endové podsítě (s výjimkou povolený provoz v pravidlech 1 a 4). Tato sada pravidel chrání síť back-end, v případě, že útočník zneužije webovou aplikaci na front-endu. Útočník by mít omezený přístup k back endovou síť "chráněné" (pouze pro prostředky zveřejněné na AppVM01 server).

Neexistuje výchozí odchozí pravidlo, které umožňuje provozu do Internetu. V tomto příkladu jsme povoluje odchozí provoz a místo abyste upravili žádná odchozí pravidla. Chcete-li zamezit provoz v obou směrech, směrování definovaného uživatelem je potřeba (viz příklad 3).

#### <a name="conclusion"></a>Závěr
V tomto příkladu je poměrně jednoduché a nekomplikované způsob izolace back endové podsítě z příchozí provoz. Další informace najdete v tématu [podrobné pokyny pro sestavení][Example1]. Tyto pokyny zahrnují:

* Jak vytvořit tento hraniční síti pomocí klasického skriptů prostředí PowerShell.
* Postup pro sestavení tato hraniční síť pomocí šablony Azure Resource Manageru.
* Podrobný popis jednotlivých příkazů skupiny zabezpečení sítě.
* Scénáře toků podrobné provoz znázorňující, jak je provoz povolený nebo zakázaný v každé vrstvě.


### <a name="example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs"></a>Příklad 2 vytvořit hraniční síť k ochraně aplikací s bránou firewall a skupin zabezpečení sítě
[Zpět na rychlý start](#fast-start) | [podrobné pokyny v tomto příkladu pro sestavení][Example2]

[![8]][8]

#### <a name="environment-description"></a>Popis prostředí
V tomto příkladu je předplatné, které obsahuje následující prostředky:

* Jedna skupina prostředků
* Virtuální síť se dvěma podsítěmi: "FrontEnd" a "Back-end"
* Skupina zabezpečení sítě, které platí pro obě podsítě
* Síťové virtuální zařízení, v tomto případě a brány firewall, připojení k front-endové podsítě
* Windows server, který představuje aplikační server web (dále jen "IIS01")
* Dva servery Windows, které představují servery back endové aplikace ("AppVM01", "AppVM02")
* Windows server, který představuje server DNS ("DNS01")

Skripty a šablony Azure Resource Manageru, najdete v článku [podrobné pokyny pro sestavení][Example2].

#### <a name="nsg-description"></a>Popis skupiny zabezpečení sítě
V tomto příkladu je skupinu NSG vytvořené a pak načíst šest pravidlům.

> [!TIP]
> Obecně platí by měl vytvářet specifická pravidla "Povolit" nejprve, za nímž následuje obecnější pravidla "Zakázat". Uvedenou prioritou určí, která pravidla se vyhodnocují první. Po nalezení provozu použít na příslušné pravidlo, žádná další pravidla se vyhodnocují. Pravidla skupiny zabezpečení sítě můžete použít v buď příchozí nebo odchozí směr (z hlediska podsíť).
>
>

Deklarativní byly sestaveny pro příchozí provoz následující pravidla:

1. Interní DNS provoz (port 53) je povolený.
2. Je povolený provoz protokolu RDP (portu 3389) z Internetu do libovolného virtuálního počítače.
3. Všechny přenosy z Internetu (všechny porty) na virtuální síťové zařízení (Brána firewall) je povolený.
4. Je povolen veškerý provoz (všechny porty) z IIS01 k AppVM1.
5. Veškerý provoz (všechny porty) z Internetu do celé virtuální sítě (obě podsítě) byl odepřen.
6. Veškerý provoz (všechny porty) z front-endové podsítě do back endové podsítě je odepřen.

Pomocí těchto pravidel vázán ke každé podsíti, pokud požadavek HTTP byl příchozí z Internetu do brány firewall, jak pravidla 3 (Povolit) a 5 (zabránění) se vztahují. Ale vzhledem k tomu, že pravidlo 3 má vyšší prioritu, pouze by použít, a pravidla 5 by souvisejí. Požadavek HTTP se proto by bylo možné bránu firewall. Pokud tento stejný provoz se snažil to spojit se serverem IIS01, i když je na front-endové podsítě, pravidlo 5 (zabránění) by se použilo, a provoz nebude možné předat serveru. Pravidla 6 (zabránění) blokuje front-endové podsítě z komunikujících do back endové podsítě (s výjimkou povolený provoz v pravidlech 1 a 4). Tato sada pravidel chrání síť back-end, v případě, že útočník zneužije webovou aplikaci na front-endu. Útočník by mít omezený přístup k back endovou síť "chráněné" (pouze pro prostředky zveřejněné na AppVM01 server).

Neexistuje výchozí odchozí pravidlo, které umožňuje provozu do Internetu. V tomto příkladu jsme povoluje odchozí provoz a místo abyste upravili žádná odchozí pravidla. Chcete-li zamezit provoz v obou směrech, směrování definovaného uživatelem je potřeba (viz příklad 3).

#### <a name="firewall-rule-description"></a>Popis pravidla brány firewall
V bráně firewall musí být vytvořené pravidly pro předávání. Pravidlo je potřeba, protože v tomto příkladu pouze směruje přenosy z Internetu ve vázané na bránu firewall a potom na webový server pouze jeden předávání sítě překlad adres (NAT).

Pravidlo pro předávání přijímá všechny vstupní zdrojové adresy, která vyšle brány firewall došlo k pokusu o přístup protokolu HTTP (port 80 nebo 443 pro protokol HTTPS). Má odesílány mimo místní brány firewall na rozhraní a přesměruje na webový server s adresou IP adresa 10.0.1.5.

#### <a name="conclusion"></a>Závěr
V tomto příkladu je poměrně jednoduchý způsob, jak ochraně vaší aplikace s bránou firewall a izolaci back endové podsítě z příchozí provoz. Další informace najdete v tématu [podrobné pokyny pro sestavení][Example2]. Tyto pokyny zahrnují:

* Jak vytvořit tento hraniční síti pomocí klasického skriptů prostředí PowerShell.
* Postup pro sestavení v tomto příkladu pomocí šablony Azure Resource Manageru.
* Podrobný popis každého příkazu a brány firewall pravidla skupiny zabezpečení sítě.
* Scénáře toků podrobné provoz znázorňující, jak je provoz povolený nebo zakázaný v každé vrstvě.

### <a name="example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg"></a>Příklad 3 sestavení hraniční síti můžete chránit sítím pomocí brány firewall a UDR a skupiny zabezpečení sítě
[Zpět na rychlý start](#fast-start) | [podrobné pokyny v tomto příkladu pro sestavení][Example3]

[![9]][9]

#### <a name="environment-description"></a>Popis prostředí
V tomto příkladu je předplatné, které obsahuje následující prostředky:

* Jedna skupina prostředků
* Virtuální síť se třemi podsítěmi: "SecNet", "FrontEnd" a "Back-end"
* Síťové virtuální zařízení, v tomto případě a brány firewall, připojený k podsíti SecNet
* Windows server, který představuje aplikační server web (dále jen "IIS01")
* Dva servery Windows, které představují servery back endové aplikace ("AppVM01", "AppVM02")
* Windows server, který představuje server DNS ("DNS01")

Skripty a šablony Azure Resource Manageru, najdete v článku [podrobné pokyny pro sestavení][Example3].

#### <a name="udr-description"></a>Uživatelem definovaná TRASA popis
Ve výchozím nastavení následující systémové trasy, které jsou definovány jako:

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

VNETLocal je vždy jeden nebo více definovaných předpony, které tvoří virtuální sítě pro danou konkrétní síť (to znamená, ho změní z virtuální sítě k virtuální síti, v závislosti na tom, jak definuje každá konkrétní virtuální síť). Zbývající systémové trasy, které jsou statické a výchozí jak je uvedeno v tabulce.

V tomto příkladu dvě směrovací tabulky vytvářejí, jednu pro front-end a back endové podsítě. Každá tabulka je načtena statické trasy, které jsou vhodné pro dané podsíti. V tomto příkladu každá tabulka má tři trasy, které směrovat všechen provoz (0.0.0.0/0) přes bránu firewall (dalšího směrování virtuální zařízení IP adresa =):

1. Provozu místních podsítí s další směrování definované pro povolení provozu na místní podsíti obejít bránu firewall.
2. Provoz virtuální sítě se další směrování definované jako brána firewall. Toto přesměrování přepíše výchozí pravidlo, které umožňuje místní přenosy virtuální sítě pro směrování přímo.
3. Všechny zbývající provoz (0/0) se další směrování definované jako brána firewall.

> [!TIP]
> Při komunikaci místní podsíti konce uživatelem definovaná TRASA, které nemají položka místní podsíti.
>
> * V našem příkladu je velmi důležité 10.0.1.0/24 odkazující na VNETLocal! Bez něho paketů, které byste museli opustit Webový Server (10.0.1.4) směřující do jiného místního serveru (například) 10.0.1.25 selže, protože se odešlou do síťového virtuálního zařízení. Síťové virtuální zařízení bude odesílat do podsítě a podsíť ji odešlete do NVA v nekonečné smyčce.
> * Pravděpodobnost smyčka směrování jsou obvykle vyšší na zařízení s několika síťovými kartami, které jsou připojené do oddělené podsítě, což je často tradiční, v místním zařízení.
>
>

Po vytvoření směrovací tabulky, musí být vázán na podsítě. Směrovací tabulky front-endové podsítě jednou vytvořit a vázaný k podsíti, by vypadalo podobně jako tento výstup:

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

> [!NOTE]
> Uživatelem definovaná TRASA teď může používat pro podsíť brány, na kterém je připojen okruh ExpressRoute.
>
> Příklady toho, jak povolit hraniční sítě s využitím ExpressRoute nebo sítě site-to-site jsou uvedeny v příkladech 3 a 4.
>
>

#### <a name="ip-forwarding-description"></a>Popis předávání IP
Předávání IP adres je doprovodný funkce uživatelem definovaná TRASA. Předávání IP adres se nastavení na virtuální zařízení, které umožňuje přijímání dat adresovaných konkrétně zkoumaly zařízení a potom je předejte tento provoz do konečného ultimate.

Například by uživatelem definovaná TRASA Pokud AppVM01 odešle požadavek na DNS01 server, směrovat provoz do brány firewall. Povolené předávání IP adres provoz pro cíl DNS01 (10.0.2.4) se přijal zařízení (10.0.0.4) a pak se předávají do konečného ultimate (10.0.2.4). Bez předávání IP adres v bráně firewall povolen nebude se provoz přijímat zařízení i v případě, že směrovací tabulka má brána firewall jako další segment směrování. Použití virtuálního zařízení, je důležité si pamatovat, abyste mu povolili předávání IP spolu s uživatelem definovaná TRASA.

#### <a name="nsg-description"></a>Popis skupiny zabezpečení sítě
V tomto příkladu je skupině NSG vytvořené a pak načíst s jedním pravidlem. Tato skupina je pak vázat pouze na front-endovými a back endové podsítě (ne SecNet). Deklarativně se vytváří následující pravidlo:

* Veškerý provoz (všechny porty) z Internetu do celé virtuální sítě (všechny podsítě) byl odepřen.

I když v tomto příkladu se používají skupiny zabezpečení sítě, jeho hlavním účelem je jako sekundární vrstvy ochrany proti chybě ruční konfigurace. Cílem je blokovat veškerý příchozí provoz z Internetu do části front end a back endové podsítě. Provoz by měl směrovat jenom prostřednictvím SecNet podsítě do brány firewall (a v případě potřeby k části front end a back endové podsítě). Navíc s pravidly směrování definovaného uživatelem na místě, jakýkoli přenos, který provedl ji do části front end a back endové podsítě by přesměrováni navýšení kapacity na bránu firewall (díky směrování definovaného uživatelem). Brána firewall jako asymetrický tok uvidí tento provoz a by vyřadit odchozí provoz. Proto se tři vrstvy zabezpečení, ochraně podsítě:

* Žádné veřejné IP adresy na front-endu nebo síťových karet back-endu.
* Skupiny Nsg odepření provozu z Internetu.
* Odstranění brány firewall asymetrického provoz.

Jeden bod zajímavé týkající se skupiny zabezpečení sítě v tomto příkladu je, že obsahuje pouze jeden pravidlo, které je zamítnutí přenosy z Internetu do celé virtuální sítě, včetně zabezpečení podsítě. Ale vzhledem k tomu, že skupina zabezpečení sítě je vázaný jenom na front-endovými a back endové podsítě, pravidlo není zpracován za přenos příchozích zabezpečení podsítě. V důsledku toho proudí do podsítě zabezpečení.

#### <a name="firewall-rules"></a>Pravidla brány firewall
V bráně firewall musí být vytvořené pravidly pro předávání. Vzhledem k tomu, že brána firewall je blokování nebo předávání všech příchozích, odchozích a přenosy uvnitř virtuální sítě, jsou potřeba více pravidel brány firewall. Také veškerý příchozí provoz narazí na veřejnou IP adresu služby zabezpečení (na jiném portu), zpracování bránou firewall. Osvědčeným postupem je diagram logické toky před nastavením podsítí a pravidla brány firewall, aby se zabránilo Přepracujte později. Na následujícím obrázku je logické zobrazení pravidla brány firewall v tomto příkladu:

![10]

> [!NOTE]
> Podle síťového virtuálního zařízení používá, portům pro správu liší. V tomto příkladu je odkazována Barracuda NextGen Firewall, který používá porty 22, 801 a 807. Najdete v dokumentaci výrobce zařízení najít přesnou portů používá ke správě zařízení používá.
>
>

#### <a name="firewall-rules-description"></a>Popis pravidla brány firewall
V předchozím Logický diagram zabezpečení podsítě neuvádíme, protože brána firewall je jediný zdroj v příslušné podsíti. Diagram zobrazuje pravidla brány firewall a jak jsou logicky povolují nebo odpírají přenosové toky, nikoli skutečnou cestou směrované. Navíc externích portů vybrané pro provoz protokolu RDP jsou vyšší rozsah portů (8014 – 8026) a nebyly vybrány pro volně bylo v souladu s poslední dva oktety místní IP adresa, aby byly čitelnější (například adresa serveru místní 10.0.1.4 je přidružený externí port 8014). Žádné vyšší porty nekonfliktní, ale může použít.

V tomto příkladu budeme potřebovat sedm typů pravidel:

* Externí pravidla (pro příchozí provoz):
  1. Pravidlo brány firewall správy: Toto pravidlo přesměrování aplikace umožňuje provoz procházel k portům pro správu síťového virtuálního zařízení.
  2. Pravidla protokolu RDP (pro každý server Windows): Tyto čtyři pravidla (jeden pro každý server) povolit správu jednotlivých serverů pomocí protokolu RDP. Čtyři pravidla protokolu RDP může také sbaleny do jednoho pravidla, v závislosti na možnosti síťového virtuálního zařízení používá.
  3. Pravidla pro provoz aplikace: Existují dva z těchto pravidel první pro front-endový webový provoz a druhé pro back-end provozu (třeba webový server na datové vrstvě). Konfigurace pravidla závisí na síťovou architekturu (kde jsou umístěny servery) a provoz toků (směr přenosové toky a porty, které se používají).
     * První pravidlo povoluje provoz aplikace skutečný přístup k serveru aplikace. Ostatní pravidla povolit pro správu a zabezpečení, jsou pravidla pro provoz aplikace co umožňují externí uživatele nebo služby, pro přístup k aplikacím. V tomto příkladu je jednom webovém serveru na portu 80. Jediné aplikace pravidlo firewallu tak přesměruje příchozí provoz na externí IP adresu, na webové servery interní IP adresu. Relace přesměrované přenosy by byl přeložen prostřednictvím překladu adres do interního serveru.
     * Druhé pravidlo je back endové pravidlo pro povolení webového serveru ke komunikaci s AppVM01 serveru (ale ne AppVM02) přes libovolný port.
* Vnitřní pravidla (pro přenosy uvnitř virtuální sítě.)
  1. Odchozí internetové pravidlo: Toto pravidlo povoluje provoz z libovolné sítě k předání do vybraných sítí. Toto pravidlo je obvykle výchozí pravidlo již v bráně firewall, ale v zakázaném stavu. Toto pravidlo musí být povolené pro účely tohoto příkladu.
  2. Pravidlo DNS: Toto pravidlo umožní provoz jenom DNS (port 53) k předání do serveru DNS. Pro toto prostředí je blokován většiny provozu z front-endu do back-endu. Toto pravidlo umožňuje konkrétně DNS z jakékoli místní podsítě.
  3. Podsítě pro podsíť pravidlo: Toto pravidlo je povolit všechny servery v back endové podsítě pro připojení k libovolnému serveru na front-endové podsítě (ale ne naopak).
* Odolné vůči selhání pravidla (pro provoz, který nesplňuje některé z předchozích):
  1. Všechny přenosy pravidlo odepřít: Toto pravidlo Odepřít by vždycky měla být konečné pravidlo (z hlediska prioritou), a proto pokud se shodují s některým z předchozích pravidel nezdaří tok přenosů přestane se tímto pravidlem. Toto pravidlo je pravidlo výchozí a obvykle na místě a aktivní. Tohoto pravidla jsou obvykle potřeba provádět žádné změny.

> [!TIP]
> Na druhé pravidlo provozu aplikace a pro zjednodušení tento příklad smí jiný port. V reálné situaci nejspecifičtější port a rozsahy adres by měla sloužit k omezení možností útoku tohoto pravidla.
>
>

Po vytvoření předchozí pravidla je potřeba zkontrolovat prioritu každé pravidlo, aby provoz je povolený nebo zakázaný podle potřeby. V tomto příkladu jsou pravidla v pořadí podle priority.

#### <a name="conclusion"></a>Závěr
V tomto příkladu je složitější ale dokončení způsob ochrany a izolaci sítě, než v předchozích příkladech. (Příklad 2 chrání jenom aplikace a příklad 1 pouze izoluje podsítě). Tento návrh umožňuje monitorování provozu v obou směrech a chrání nejen příchozí aplikační server, ale vynucuje zásady zabezpečení sítě pro všechny servery v této síti. Také v závislosti na zařízení používá, úplné provoz auditování a sledování můžete dosáhnout. Další informace najdete v tématu [podrobné pokyny pro sestavení][Example3]. Tyto pokyny zahrnují:

* Jak vytvořit tento příklad hraniční síti pomocí klasického skriptů prostředí PowerShell.
* Postup pro sestavení v tomto příkladu pomocí šablony Azure Resource Manageru.
* Podrobné popisy jednotlivých UDR NSG příkazu a pravidla brány firewall.
* Scénáře toků podrobné provoz znázorňující, jak je provoz povolený nebo zakázaný v každé vrstvě.

### <a name="example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn"></a>Příklad 4 přidat hybridní připojení site-to-site a virtuální zařízení sítě VPN
[Zpět na rychlý start](#fast-start) | Podrobné pokyny pro sestavení k dispozici brzy

[![11]][11]

#### <a name="environment-description"></a>Popis prostředí
Hybridní sítě pomocí virtuální síťové zařízení (NVA) lze přidat na některý z typů hraniční sítě, je popsáno v příkladu 1, 2 nebo 3.

Jak je znázorněno na předchozím obrázku, připojení VPN prostřednictvím Internetu (site-to-site) slouží k připojení místní sítě k virtuální síti Azure přes síťové virtuální zařízení.

> [!NOTE]
> Pokud používáte ExpressRoute s povolenou možností veřejné partnerské vztahy Azure, by měl vytvořit statickou trasu. Tato statická trasa by měl směrovat na síťové virtuální zařízení VPN IP adresu mimo podnikovou síť intranet a nikoli prostřednictvím připojení ExpressRoute. NAT vyžaduje na možnost Azure veřejný partnerský vztah ExpressRoute může dojít k narušení relace VPN.
>
>

Jakmile se síť VPN na místě, síťové virtuální zařízení stane centrální rozbočovač pro všechny sítě a podsítě. Předávání pravidla brány firewall určit, které přenosové toky jsou povoleny, jsou přeloženy prostřednictvím překladu adres, se přesměrují nebo jsou vynechány (i pro toky přenosů mezi místní sítí a Azure).

Přenosové toky potřeba pečlivě zvážit, jak se dá optimalizovat nebo snížena v tomto vzoru návrhu v závislosti na konkrétním případu použití.

Pomocí prostředí vytvořené v příkladu 3 a následným přidáním připojení sítě site-to-site VPN hybridní, vytvoří následující:

[![12]][12]

Místní směrovač nebo jiné síťové zařízení, která je kompatibilní s vaší síťové virtuální zařízení pro síť VPN, bude klient VPN. Fyzické zařízení bude zodpovídat za spouštění a Správa připojení k síti VPN s vaší síťové virtuální zařízení.

Logicky k síťové virtuální zařízení, sítě vypadá čtyři samostatné "zóny zabezpečení" s pravidly na síťové virtuální zařízení se primární ředitel provozu mezi tyto zóny:

![13]

#### <a name="conclusion"></a>Závěr
Přidání sítě site-to-site VPN hybridní připojení ke službě Azure virtual network můžete rozšířit místní síť do Azure bezpečným způsobem. Pomocí připojení VPN, provoz je šifrovaný a směrovat přes Internet. Síťové virtuální zařízení v tomto příkladu poskytuje centrální umístění pro vynucení a spravovat zásady zabezpečení. Další informace najdete v pokynech podrobné sestavení (řádně doloženo). Tyto pokyny zahrnují:

* Postup pro sestavení hraniční síti tento příklad se skripty Powershellu.
* Postup pro sestavení v tomto příkladu pomocí šablony Azure Resource Manageru.
* Scénáře toků podrobné provoz zobrazující tok provozu v rámci tohoto návrhu.

### <a name="example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway"></a>Příklad 5 přidat hybridní připojení s bránou Azure VPN site-to-site,
[Zpět na rychlý start](#fast-start) | Podrobné pokyny pro sestavení k dispozici brzy

[![14]][14]

#### <a name="environment-description"></a>Popis prostředí
Hybridní sítě pomocí služby Azure VPN gateway mohou být přidány do obou hraniční sítě typu je popsáno v příkladu 1 nebo 2.

Jak je znázorněno na předchozím obrázku, připojení VPN prostřednictvím Internetu (site-to-site) slouží k připojení místní sítě k virtuální síti Azure přes Azure VPN gateway.

> [!NOTE]
> Pokud používáte ExpressRoute s povolenou možností veřejné partnerské vztahy Azure, by měl vytvořit statickou trasu. Tato statická trasa by měl směrovat na síťové virtuální zařízení VPN IP adresu mimo podnikovou síť intranet a nikoli prostřednictvím sítě WAN ExpressRoute. NAT vyžaduje na možnost Azure veřejný partnerský vztah ExpressRoute může dojít k narušení relace VPN.
>
>

Následující obrázek znázorňuje dvě sítě hrany v tomto příkladu. Na první edge řízení síťového virtuálního zařízení a skupin zabezpečení sítě přenosové toky k přenosům uvnitř Azure sítích a mezi Azure a Internetem. Druhý edge je Azure VPN gateway, která je hraniční samostatné a izolované sítě mezi místními a Azure.

Přenosové toky potřeba pečlivě zvážit, jak se dá optimalizovat nebo snížena v tomto vzoru návrhu v závislosti na konkrétním případu použití.

Pomocí prostředí vytvořené v příkladu 1 a následným přidáním připojení sítě site-to-site VPN hybridní, vytvoří následující:

[![15]][15]

#### <a name="conclusion"></a>Závěr
Přidání sítě site-to-site VPN hybridní připojení ke službě Azure virtual network můžete rozšířit místní síť do Azure bezpečným způsobem. Pomocí nativní brány Azure VPN, provoz se šifrovanými v protokolu IPSec a směruje se přes Internet. Pomocí Azure VPN gateway také můžete zadat nižšími náklady možnost (žádné další licence nákladů stejně jako u síťových virtuálních zařízení jiných výrobců). Tato možnost je nejekonomičtější v příkladu 1, kde se používá žádné síťové virtuální zařízení. Další informace najdete v pokynech podrobné sestavení (řádně doloženo). Tyto pokyny zahrnují:

* Postup pro sestavení hraniční síti tento příklad se skripty Powershellu.
* Postup pro sestavení v tomto příkladu pomocí šablony Azure Resource Manageru.
* Scénáře toků podrobné provoz zobrazující tok provozu v rámci tohoto návrhu.

### <a name="example-6-add-a-hybrid-connection-with-expressroute"></a>Příklad 6 přidat hybridní připojení s ExpressRoute
[Zpět na rychlý start](#fast-start) | Podrobné pokyny pro sestavení k dispozici brzy

[![16]][16]

#### <a name="environment-description"></a>Popis prostředí
Hybridních sítí pomocí připojení privátní partnerský vztah ExpressRoute můžete přidat buď hraniční sítě typu je popsáno v příkladu 1 nebo 2.

Jak je znázorněno na předchozím obrázku, soukromý partnerský vztah ExpressRoute poskytuje přímé připojení mezi vaší místní sítí a virtuální síť Azure. Provoz transits pouze síti poskytovatele služeb a sítí Microsoft Azure, nikdy klepnou na Internetu.

> [!TIP]
> Pomocí ExpressRoute udržuje podnikové síťové přenosy z Internetu. Umožňuje také pro smlouvy o úrovni služeb poskytovatelem ExpressRoute. Azure Gateway můžete předat až 10 GB/s s využitím ExpressRoute, zatímco maximální propustnosti brány Azure VPN typu site-to-site, je 200 MB/s.
>
>

Jak je znázorněno v následujícím diagramu, s touto možností prostředí teď má dvě sítě hran. Síťové virtuální zařízení a skupiny zabezpečení sítě řídit přenosové toky k přenosům uvnitř Azure sítích a mezi Azure a Internetem, zatímco brána je hranici samostatné a izolované sítě mezi místními a Azure.

Přenosové toky potřeba pečlivě zvážit, jak se dá optimalizovat nebo snížena v tomto vzoru návrhu v závislosti na konkrétním případu použití.

Pomocí prostředí vytvořené v příkladu 1 a následným přidáním připojení ExpressRoute hybridní síť, vytvoří následující:

[![17]][17]

#### <a name="conclusion"></a>Závěr
Přidání připojení k síti ExpressRoute soukromého partnerského vztahu můžete rozšířit místní síť do Azure v zabezpečené a nižší latenci a vyšším výkonem způsobem. Navíc pomocí nativní Azure Gateway jako v následujícím příkladu poskytuje možnost (žádné další licence stejně jako u dodavatelů síťových virtuálních zařízení) a nižšími náklady. Další informace najdete v pokynech podrobné sestavení (řádně doloženo). Tyto pokyny zahrnují:

* Postup pro sestavení hraniční síti tento příklad se skripty Powershellu.
* Postup pro sestavení v tomto příkladu pomocí šablony Azure Resource Manageru.
* Scénáře toků podrobné provoz zobrazující tok provozu v rámci tohoto návrhu.

## <a name="references"></a>Odkazy
### <a name="helpful-websites-and-documentation"></a>Užitečné webů a dokumentace
* Přístup k Azure pomocí Azure Resource Manageru:
* Přístup k Azure pomocí Powershellu: [https://docs.microsoft.com/powershell/azureps-cmdlets-docs/](/powershell/azure/overview)
* Virtuální sítě dokumentace: [https://docs.microsoft.com/azure/virtual-network/](https://docs.microsoft.com/azure/virtual-network/)
* Dokumentace skupiny zabezpečení sítě: [https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg](virtual-network/security-overview.md)
* Uživatelem definované směrování dokumentace: [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview](virtual-network/virtual-networks-udr-overview.md)
* Virtuální brány Azure: [https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway/)
* Site-to-Site VPN: [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell](vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* Dokumentace ke službě ExpressRoute (Nezapomeňte najdete v částech "Začínáme" a "Jak na"): [https://docs.microsoft.com/azure/expressroute/](https://docs.microsoft.com/azure/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "vývojový diagram možnosti zabezpečení"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "funkce zabezpečení azure"
[3]: ./media/best-practices-network-security/dmzcorporate.png "A DMZ v podnikové síti"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "architektury zabezpečení azure"
[5]: ./media/best-practices-network-security/dmzazure.png "DMZ v Azure Virtual Network"
[6]: ./media/best-practices-network-security/dmzhybrid.png "hybridní síť s tři hranice zabezpečení"
[7]: ./media/best-practices-network-security/example1design.png "příchozí DMZ pomocí skupiny zabezpečení sítě"
[8]: ./media/best-practices-network-security/example2design.png "příchozí DMZ pomocí síťového virtuálního zařízení a skupiny zabezpečení sítě"
[9]: ./media/best-practices-network-security/example3design.png "obousměrné DMZ pomocí síťového virtuálního zařízení, skupiny zabezpečení sítě a směrování definovaného uživatelem"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "logické zobrazení pravidel brány Firewall"
[11]: ./media/best-practices-network-security/example3designoptions.png "připojené hybridní síť DMZ pomocí síťového virtuálního zařízení"
[12]: ./media/best-practices-network-security/example4designs2s.png "DMZ pomocí síťového virtuálního zařízení připojeno pomocí sítě Site-to-Site VPN"
[13]: ./media/best-practices-network-security/example4networklogical.png "logické sítě z hlediska síťového virtuálního zařízení"
[14]: ./media/best-practices-network-security/example5designoptions.png "připojení Site-to-Site hybridní síť DMZ pomocí brány Azure"
[15]: ./media/best-practices-network-security/example5designs2s.png "DMZ pomocí brány Azure prostřednictvím sítě Site-to-Site VPN"
[16]: ./media/best-practices-network-security/example6designoptions.png "DMZ pomocí brány Azure připojené sítě ExpressRoute hybridní"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "DMZ pomocí brány Azure pomocí připojení ExpressRoute"

<!--Link References-->
[TrustCenter]: https://azure.microsoft.com/support/trust-center/compliance/
[Example1]: ./virtual-network/virtual-networks-dmz-nsg.md
[Example2]: ./virtual-network/virtual-networks-dmz-nsg-fw-asm.md
[Example3]: ./virtual-network/virtual-networks-dmz-nsg-fw-udr-asm.md
[Example4]: ./virtual-network/virtual-networks-hybrid-s2s-nva-asm.md
[Example5]: ./virtual-network/virtual-networks-hybrid-s2s-agw-asm.md
[Example6]: ./virtual-network/virtual-networks-hybrid-expressroute-asm.md
[Example7]: ./virtual-network/virtual-networks-vnet2vnet-direct-asm.md
[Example8]: ./virtual-network/virtual-networks-vnet2vnet-transit-asm.md
