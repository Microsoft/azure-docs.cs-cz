---
title: Osvědčené postupy zabezpečení sítě Azure | Microsoft Docs
description: Další některé klíčové funkce, které jsou k dispozici v Azure k usnadnění vytváření zabezpečené sítě v prostředích
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
ms.openlocfilehash: cf015f4857a22b755813d0be1af5a55a8b7b6535
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34360468"
---
# <a name="microsoft-cloud-services-and-network-security"></a>Zabezpečení služeb a sítě cloudu Microsoft
Cloudové služby společnosti Microsoft poskytování služeb flexibilně škálovatelné a infrastruktury, podnikové úrovni funkce a mnoho možností pro hybridní připojení. Tato volba se přístup k těmto službám prostřednictvím Internetu nebo s Azure ExpressRoute, které poskytuje připojení k privátní síti. Platforma Microsoft Azure umožňuje zákazníkům bezproblémově rozšířit jejich infrastrukturu do cloudu a vytvářet vícevrstvé architektury. Kromě toho můžete povolit třetím stranám rozšířené možnosti prostřednictvím nabídky zabezpečení služeb a virtuálních zařízení. Tento dokument poskytuje přehled o zabezpečení a architektury problémy, které zákazníci by měli zvážit při používání cloudové služby společnosti Microsoft získat přístup přes ExpressRoute. Také vysvětluje vytváření bezpečnější služeb ve virtuálních sítí Azure.

## <a name="fast-start"></a>Rychlý start
Následující graf logiky můžete směrovat vám konkrétní příklad mnoho postupů zabezpečení, která je k dispozici s platformou Azure. Stručná referenční příručka najít příklad, který nejlépe vyhovuje váš případ. Pro rozšířené vysvětlení pokračujte v načítání prostřednictvím papíru.
[![0]][0]

[Příklad 1: Vytvoření hraniční síti (označované také jako DMZ, demilitarizovaná zóna nebo monitorovaná podsíť) k ochraně aplikací pomocí skupin zabezpečení sítě (Nsg).](#example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs)</br>
[Příklad 2: Vytvoření hraniční síti k ochraně aplikací s bránou firewall a skupin Nsg.](#example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs)</br>
[Příklad 3: Vytvoření hraniční síti k ochraně sítě s bránu firewall, trasy definované uživatelem (UDR) a skupina NSG.](#example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg)</br>
[Příklad 4: Přidejte hybridní připojení site-to-site, virtuální zařízení virtuální privátní síti (VPN).](#example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br>
[Příklad 5: Přidejte hybridní připojení site-to-site, brána Azure VPN.](#example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway)</br>
[Příklad 6: Přidejte hybridní připojení prostřednictvím ExpressRoute.](#example-6-add-a-hybrid-connection-with-expressroute)</br>
Příklady pro přidání připojení mezi virtuálními sítěmi, vysokou dostupnost a řetězení služby přidá k tomuto dokumentu za další několik měsíců.

## <a name="microsoft-compliance-and-infrastructure-protection"></a>Ochrana Microsoft dodržování předpisů a infrastruktury
Pomoci organizacím, které vyhovují požadavkům national, místní a průmyslové řídících shromažďování a používání dat se jednotlivce, společnost Microsoft nabízí víc než 40 certifikace a atestace podle. Většina komplexní sadu všechny poskytovatele cloudové služby.

Další informace najdete v tématu informace o dodržování předpisů na [Microsoft Trust Center][TrustCenter].

Společnost Microsoft nemá komplexní přístup k ochraně cloudové infrastruktury potřebné ke spuštění velkého rozsahu služeb global services. Infrastruktura cloudu Microsoft zahrnuje hardware, software, sítě a správu a provozní personál, kromě fyzické datových centrech.

![2]

Tento přístup poskytuje bezpečnější základ pro zákazníky k nasazení služeb v cloudu Microsoft. Dalším krokem je pro zákazníky návrh a vytvoření Architektura zabezpečení k ochraně těchto služeb.

## <a name="traditional-security-architectures-and-perimeter-networks"></a>Tradiční zabezpečení architektury a hraniční sítě
I když Microsoft investují výraznou při ochraně infrastruktury cloudu, musí zákazníkům taky chránit jejich cloudové služby a skupiny prostředků. S více vrstvami přístup k zabezpečení poskytuje nejlepší obranou. Zóny zabezpečení hraniční sítě chrání prostředkům v interní síti z nedůvěryhodné sítě. Hraniční síti odkazuje okraje nebo části sítě, které se nacházejí mezi Internetu a chráněné podnikové infrastruktury IT.

V typické podnikové sítě se základní infrastruktury výraznou přidá v okruhu, s více vrstvami zabezpečení zařízení. Hranice každé vrstvě se skládá z zařízení a body vynucení zásad. Jednotlivé úrovně může obsahovat kombinaci následující zařízení zabezpečení sítě: brány firewall, odmítnutí služby (DoS) prevence, zjišťování neoprávněných vniknutí nebo systémy ochrany (ID nebo IP adresy) a zařízení VPN. Vynucení zásad mohou mít formu zásady brány firewall, seznamy řízení přístupu (ACL) nebo konkrétní směrování. První linií obrany v síti, přímo přijímat příchozí provoz z Internetu, je kombinací tyto mechanismy pro zabránění útokům a škodlivých přenosů současně další oprávněné požadavky do sítě. Tento provoz se směruje přímo k prostředkům v hraniční síti. Tento prostředek může pak "komunikovat" k prostředkům v síti, tranzitní další hranice pro ověření hlubší první. Vnější vrstva se nazývá hraniční síti, protože tuto část sítě je přístup k Internetu, obvykle s určitou formu ochrany na obou stranách. Následující obrázek znázorňuje příklad hraniční síti jedné podsíti v podnikové síti, s dvěma hranice zabezpečení.

![3]

Existuje mnoho architektur použít k implementaci hraniční síti. Tyto architektury můžete v rozsahu od služby Vyrovnávání zatížení jednoduché do více podsítěmi hraniční síti s různým mechanismy na každá hranice bloku provoz a chránit hlubší vrstvy podnikové sítě. Jak je integrována do hraniční sítě závisí na konkrétním požadavkům organizace a jeho celkový odolnost vůči rizikům.

Jak zákazníci přesouvat zatížení do veřejných cloudů, je velmi důležité podporují podobné možnosti pro architekturu hraniční sítě v Azure a splňovat požadavky na zabezpečení a dodržování předpisů. Tento dokument obsahuje pokyny na tom, jak zákazníci mohou vytvářet prostředí zabezpečené sítě v Azure. Zaměřuje se na hraniční síti, ale také zahrnuje komplexní diskuzi o mnoho aspektů zabezpečení sítě. Na následující otázky informovat toto pojednání:

* Jak se dají vytvářet do hraniční sítě v Azure?
* Jaké jsou některé funkce Azure, které je k dispozici pro sestavení hraniční síti?
* Jak lze chránit úlohy back-end?
* Jak jsou ovládaná internetové komunikace pro úlohy v Azure?
* Tom, jak může být místní sítě chráněný od nasazení v Azure?
* Pokud má být funkce nativní zabezpečení Azure použita versus zařízení třetích stran nebo služby?

Následující diagram znázorňuje různých úrovní zabezpečení, které Azure poskytuje zákazníkům. Tyto vrstvy jsou nativní v samotné platformě Azure a uživatelsky definované funkce:

![4]

Příchozí z Internetu, DDoS Azure pomáhá chránit proti rozsáhlé útoky na Azure. Další vrstva je zákazník definovaný veřejné IP adresy (koncových bodů), které se používají k určení, které přenos dat přes cloudovou službu do virtuální sítě. Nativní Azure virtuální izolace sítě zajišťuje úplný izolaci od všech ostatních sítí a že jenom přenosy dat prostřednictvím uživateli nakonfigurovanému cesty a metody. Tyto cesty a metody jsou další vrstva, kde skupiny Nsg, UDR a síťových virtuálních zařízení slouží k vytvoření hranice zabezpečení k ochraně nasazení aplikací v síti chráněné.

Následující část obsahuje přehled virtuálních sítí Azure. Tyto virtuální sítě jsou vytvořené pomocí zákazníků a jsou co jejich nasazených úloh jsou připojené k. Virtuální sítě jsou základ pro všechny funkce sítě zabezpečení, která je nutný k vytvoření hraniční síti k ochraně zákaznických nasazení v Azure.

## <a name="overview-of-azure-virtual-networks"></a>Přehled virtuálních sítí Azure
Předtím, než k virtuálním sítím Azure můžete získat přenosy z Internetu, existují dvě vrstvy zabezpečení vyplývajících na platformu Azure:

1.    **Ochrana proti útoku DDoS**: ochrana proti útoku DDoS je vrstva Azure fyzické síti, která chrání Azure k samotné platformě nástroje z rozsáhlé útoky založenými na Internetu. Tyto útoky použít více uzly "robota" ve snaze zahlcovat služby sítě Internet. Azure má robustní mřížku DDoS ochrany na všech příchozích, odchozích a mezi Azure oblast připojení. Tuto vrstvu ochrany DDoS nemá žádné atributy konfigurovat uživatele a není dostupný pro zákazníka. Ochrannou vrstvu DDoS chrání Azure jako platformu před útoky ve velkém měřítku, také sleduje odesílací vázané provozu a provoz mezi Azure oblast. Na virtuální sítě pomocí virtuální síťová zařízení, můžete nakonfigurovat další vrstvy odolnost proti zákazník proti menší útoku škálování, který nemá dojít platformy úroveň ochrany. Příklad DDoS v akci; Pokud pomocí rozsáhlé útoku DDoS byl napaden internetovou IP adresu, by Azure zjišťovat zdrojích útoků a procházet nástroje problematické provoz, než bylo dosaženo plánovaného místa určení. Ve většině případů attacked koncový bod není ovlivněn útoku. Ve výjimečných případech, že koncový bod ovlivňuje žádný provoz má vliv na ostatní koncové body, pouze attacked koncový bod. Další zákazníků a služby, proto by viz žádný vliv z tohoto útoku. Je důležité si uvědomit, že Azure DDoS pouze hledá rozsáhlé útoky. Je možné, že může být specifické služby přetížena předtím, než se překročí mezní hodnoty úroveň ochrany platformy. Například webu na jednom serveru A0 IIS, může do režimu offline pomocí útoku DDoS předtím, než úroveň platformy Azure ochrana proti útoku DDoS registrována hrozbou.

2.  **Veřejné IP adresy**: veřejné IP adresy (povolené prostřednictvím koncových bodů služby, veřejné IP adresy, aplikační brána a dalších Azure funkcí, které prezentovat Internetu směrovat na prostředku veřejné IP adresy) umožňují cloudové služby nebo prostředek skupiny tak, aby měl veřejný Internet IP adres a portů. Koncový bod používá překládání adres (NAT) pro směrování provozu na interní adresu a port na virtuální síť Azure. Tato cesta je primární způsob pro externí přenos dat do virtuální sítě. Veřejné IP adresy lze konfigurovat, a určit, jaký provoz, je předaná a jak a kde je přeložená k virtuální síti.

Provoz dosáhne virtuální sítě, budete řadu funkcí, které je možné uplatnit. Virtuální sítě Azure jsou základem pro zákazníky připojit jejich úlohy a kde platí základní zabezpečení na úrovni sítě. Privátní sítě (virtuální sítě překrytí) je v Azure pro zákazníky s následující funkce a vlastnosti:

* **Izolace provozu**: virtuální sítě je hranice izolace přenosů na platformě Azure. Virtuální počítače (VM) v jednu virtuální síť nemůže komunikovat přímo do virtuálních počítačů v jinou virtuální síť, i v případě, že jsou obě virtuální sítě vytvořené pomocí tentýž zákazník. Izolace je kritické vlastnosti, které zajišťuje, aby virtuální počítače zákazníka a komunikace zůstane privátní virtuální sítě.

>[!NOTE]
>Izolace přenosů dat se vztahuje pouze na přenosy *příchozí* do virtuální sítě. Ve výchozím nastavení se odchozí provoz z virtuální sítě k Internetu je povoleno, ale je možné zabránit v případě potřeby pomocí skupin Nsg.
>
>

* **Vícevrstvá topologie**: Povolit virtuální sítě zákazníků k definování vícevrstvé topologie přidělením podsítě a určení samostatné adresních prostorů pro různé prvky nebo "vrstvy" jejich úloh. Tyto logické seskupení a topologie zákazníkům umožňují definovat zásady různý přístup na základě typů úlohy a také řídit tok přenosů dat mezi vrstvami.
* **Připojení mezi různými místy**: Zákazníci mohou vytvořit připojení mezi různými místy mezi virtuální síť a více místními servery nebo jiných virtuálních sítí v Azure. K vytvoření připojení, zákazníci mohou používat virtuální síť partnerský vztah Azure VPN Gateway, síťových virtuálních zařízení nebo ExpressRoute. Azure podporuje sítě site-to-site (S2S) VPN pomocí standardních protokolů protokolu IPsec/IKE a privátní připojení ExpressRoute.
* **Skupina NSG** umožňuje zákazníkům vytvářet pravidla (ACL) na požadované úrovni členitosti: síťového rozhraní, jednotlivé virtuální počítače nebo virtuální podsítě. Zákazníci můžete řídit přístup k povolení nebo odepření komunikace mezi zatížení v rámci virtuální sítě, ze systémů v sítích zákazníka prostřednictvím připojení mezi různými místy, nebo přímé komunikaci v síti Internet.
* **UDR** a **předávání IP adres** povolit zákazníkům definovat cesty komunikace mezi různých vrstev v rámci virtuální sítě. Zákazníci můžete nasadit bránu firewall, ID nebo IP adresy a jiné virtuální zařízení a směrovat síťový provoz přes tyto zabezpečovací zařízení pro vynucení hranic zásad zabezpečení, auditování a kontroly.
* **Síťových virtuálních zařízení** v Azure Marketplace: zabezpečovací zařízení, jako jsou brány firewall, nástroje pro vyrovnávání zatížení a ID nebo IP adresy jsou k dispozici v Azure Marketplace a galerii bitové kopie virtuálního počítače. Zákazníci můžou nasazovat těchto zařízení do svých virtuálních sítích a konkrétně v jejich hranice zabezpečení (včetně podsítě hraniční síť) k dokončení víceúrovňových zabezpečeném síťovém prostředí.

Tyto funkce a možnosti je jeden příklad, jak může v Azure zkonstruovat síťovou architekturu hraniční následující diagram:

![5]

## <a name="perimeter-network-characteristics-and-requirements"></a>Hraniční síť charakteristiky a požadavky
Hraniční síť je front-endu sítě, přímo propojení komunikaci z Internetu. Příchozí pakety musí procházet skrz zabezpečovací zařízení, jako jsou brány firewall, identifikátory a IP adresy, dříve, než dorazila back-end serverů. Internetový pakety z úloh můžete také procházet skrz zabezpečovací zařízení v hraniční síti pro vynucení zásad, kontrolu a auditování, před opuštěním sítě. Kromě toho hraniční síti, může hostovat brány VPN mezi různými místy mezi virtuální sítě zákazníků a místní sítě.

### <a name="perimeter-network-characteristics"></a>Hraniční síť charakteristiky
Odkazování na předchozím obrázku, některé charakteristiky dobrý hraniční síti jsou následující:

* Internetový:
  * Samotné podsítě hraniční sítě je přístupný z Internetu, přímo komunikaci s Internetem.
  * Veřejné IP adresy, virtuální IP adresy a koncové body služby předat internetový provoz klientské sítě a zařízení.
  * Příchozí provoz z Internetu projdou zabezpečovací zařízení před dalším prostředkům na síť front-end.
  * Pokud je povoleno odchozí zabezpečení, provoz prochází přes zabezpečovací zařízení, jako poslední krok před předávání k Internetu.
* Chráněná síťová:
  * Neexistuje žádné přímé cestu k základní infrastruktury z Internetu.
  * Kanály pro základní infrastruktury musí procházet přes zabezpečovací zařízení, jako jsou skupiny Nsg, brány firewall nebo zařízení VPN.
  * Jiná zařízení nesmí přemostění Internetu a základní infrastruktury.
  * Zabezpečovací zařízení na straně Internetu a chráněná síťová čelí hranice hraniční síti (například dvě brány firewall ikony vidět na předchozím obrázku) může ve skutečnosti se jeden virtuální zařízení se odlišné pravidla nebo s rozhraními pro každou hranici. Například jeden fyzického zařízení logicky oddělené, zpracování zatížení pro obě hranice hraniční síti.
* Dalších běžných postupů a omezení:
  * Úlohy nesmí ukládání důležitých informací business.
  * Přístup a aktualizace hraniční sítě konfigurací a nasazení jsou omezeny na pouze oprávnění správci.

### <a name="perimeter-network-requirements"></a>Požadavky na hraniční síť
Pokud chcete povolit tyto charakteristiky, postupujte podle následujících pokynů na požadavky na virtuální síť k implementaci úspěšné hraniční sítě:

* **Architektura podsítě:** zadejte virtuální síť tak, aby celou podsíť je vyhrazený jako hraniční síti, oddělený od jiných podsítí ve stejné virtuální síti. Toto oddělení zajišťuje provoz mezi hraniční sítí a jinými interní nebo privátní podsítě vrstvy toky přes bránu firewall či virtuální zařízení ID nebo IP adresy.  Trasy definované uživatelem v podsítích hranic jsou požadované pro tento přenos dat do tohoto virtuálního zařízení.
* **Skupina NSG:** samotné podsítě hraniční síť by měla být otevřené pro komunikaci s Internetem, ale to neznamená, zákazníci měli obcházení skupiny Nsg. Dodržujte běžné postupy zabezpečení, chcete-li minimalizovat povrchy síťový přístup k Internetu. Zamknout rozsahy Vzdálená adresa povolen přístup k nasazení nebo konkrétní aplikaci protokoly a porty, které jsou otevřené. Může být okolností, ale, ve kterých není možné dokončení uzamčení. Například pokud zákazníci externí web ve službě Azure, hraniční sítě by měl povolit příchozí webové žádosti z všechny veřejné IP adresy, ale měli jenom otevřít porty webové aplikace: TCP na portu 80 nebo TCP na portu 443.
* **Směrovací tabulky:** samotné podsítě hraniční sítě by měl být schopen komunikovat přímo k Internetu, ale neměli povolit přímé komunikaci s back end a místní sítí bez průchodu přes bránu firewall nebo zabezpečení zařízení.
* **Konfigurace zabezpečení zařízení:** ke směrování a kontrola paketů mezi hraniční sítí a zbytek chráněné sítě, zabezpečovací zařízení, jako je například Brána firewall, ID, a IP adresy zařízení může být více adresami. Můžou mít oddělených síťových adaptérů pro hraniční sítí a podsítí back-end. Síťové adaptéry v hraniční síti komunikovat přímo do a z Internetu, s odpovídající skupiny Nsg a do hraniční sítě směrovací tabulky. Síťové adaptéry, připojení k podsítím back-end víc omezit skupiny Nsg a směrovacích tabulek odpovídající podsítě back-end.
* **Funkce zabezpečení zařízení:** zabezpečovací zařízení nasazený v hraniční síti obvykle provádět následující funkce:
  * Brány firewall: Vynucování pravidel brány firewall nebo zásady řízení přístupu pro příchozí požadavky.
  * Hrozby odhalování a prevence: zjišťování a zmírnění nebezpečné útoky z Internetu.
  * Auditování a protokolování: zachování podrobné protokoly auditování a analýz.
  * Reverse proxy: přesměrování příchozích požadavků na odpovídající back-end serverů. Toto přesměrování zahrnuje mapování a překladu cílové adresy na front-endu zařízení obvykle brána firewall, na serveru back-end adresy.
  * Předávání proxy: poskytnutí NAT a provádění auditování pro komunikace iniciované ze v rámci virtuální sítě k Internetu.
  * Směrovač: Předávání mezi podsítěmi a příchozí provoz ve virtuální síti.
  * Zařízení VPN: funguje jako brány sítě VPN mezi různými místy pro připojení VPN mezi různými místy mezi místními sítěmi zákazníka a virtuálních sítí Azure.
  * Server sítě VPN: přijímání klienti VPN připojení k virtuální sítě Azure.

> [!TIP]
> Oddělit následujících dvou skupin: osobám oprávnění k přístupu k zařízení hraniční sítě zabezpečení a jednotlivce oprávnění jako správci vývoj, nasazení nebo provozu aplikace. Tyto skupiny oddělíte umožňuje oddělení povinností a zabrání jedna osoba obcházení zabezpečení aplikací a ovládací prvky zabezpečení sítě.
>
>

### <a name="questions-to-be-asked-when-building-network-boundaries"></a>Otázky, které se zobrazí výzva, při vytváření sítě hranice
V této části Pokud není výslovně uvedeno, termín "sítě" odkazuje privátní virtuální sítě Azure vytvořené správce předplatného. Termín neodkazuje na základní fyzické sítě v rámci Azure.

Navíc virtuálních sítí Azure se často používají k rozšíření tradičních místních sítí. Je možné začlenit site-to-site nebo ExpressRoute hybridní sítě řešení pomocí architektury hraniční sítě. Tento odkaz hybridní je důležitý faktor při vytváření hranic zabezpečení sítě.

Tyto tři otázky jsou důležité k zodpovězení když sestavujete síť s hraniční sítí a více hranic zabezpečení.

#### <a name="1-how-many-boundaries-are-needed"></a>1) hranice kolik je potřeba?
První rozhodnutí bod je k rozhodování o tom, kolik hranice zabezpečení jsou potřeba v této situaci:

* Jednoho hraničního: jednu na front-endu hraniční síti, mezi virtuální sítí a Internetem.
* Dva hranice: jeden na straně Internetu do hraniční sítě a jiné mezi podsíť hraniční sítě a podsítě back-end v Azure virtuální sítě.
* Tři hranice: jeden na straně Internetu do hraniční sítě, jednu mezi hraniční sítí a podsítí back-end a jeden mezi podsítěmi back-end a místní sítě.
* Hranice N: Číslo proměnné. V závislosti na požadavcích zabezpečení neexistuje žádné omezení počtu hranice zabezpečení, které mohou být použity v dané síti.

Počet a typ hranice potřeby lišit podle odolnost vůči rizikům společnosti a konkrétní scénář se implementuje. Toto rozhodnutí se často provádí společně s více skupin v rámci organizace, často včetně team rizika a dodržování předpisů, sítě a platformy týmu a vývojovým týmem aplikace. Osoby s znalosti o zabezpečení, zahrnutých dat a používá technologie by měl mít indikované v tomto rozhodnutí zajistit příslušná bezpečnostní potřebujete pro každou implementaci.

> [!TIP]
> Použijte nejmenší počet hranic, které splňují požadavky na zabezpečení pro dané situaci. S další hranice, operace a řešení problémů může být obtížnější, a také režie na správu zabývajících se správa víc zásad hranic v čase. Nedostatečná hranice však zvýšit tak riziko. Hledání rovnováhu mezi je velmi důležité.
>
>

![6]

Předchozí obrázek zobrazuje souhrnné zobrazení tři zabezpečení hraniční sítě. Hranice jsou mezi hraniční síti a Internetu, Azure front-end a back-end privátní podsítě a v podsíti Azure back-end a místní podnikové síti.

#### <a name="2-where-are-the-boundaries-located"></a>2), kde se nachází hranice?
Jakmile se rozhodli, počet hranic, kde pro jejich implementaci je další bod rozhodnutí. Existují obecně tři možnosti:

* Pomocí internetové zprostředkující služby (například cloudové brány firewall webových aplikací, který není popisovaný v tomto dokumentu)
* Pomocí nativní funkce nebo síťových virtuálních zařízení v Azure
* Pomocí fyzických zařízení v místní síti

Na výhradně Azure sítě jsou možnosti nativní funkce Azure (například Azure nástroje pro vyrovnávání zatížení) nebo sítě virtuálních zařízení z ekosystému bohaté partnera Azure (například kontrolní bod brány firewall).

Pro potřeby hranice mezi Azure a místní sítí zabezpečovací zařízení mohou být uloženy na obou stranách připojení (nebo na obou stranách). Proto musí být přijato rozhodnutí o umístění umístit zabezpečení zařízení.

Na předchozím obrázku Internet hraniční síti a hranice přední na back-end jsou zcela obsažen v rámci Azure a musí být nativní funkce Azure nebo sítě virtuálních zařízení. Zabezpečovací zařízení na hranici mezi Azure (back-end podsítě) a podnikové síti může být buď na straně Azure nebo na straně místní nebo i kombinaci zařízení na obou stranách. Může být významné výhody a nevýhody obou možností, které musí být vážně považovat za.

Například pomocí stávajících zařízeních fyzického zabezpečení na straně místní sítě má výhodu, že je potřeba žádné nové zařízení. Potřebuje pouze změny konfigurace. Nevýhodou, je ale, že veškerý provoz musí vraťte z Azure do místní sítě, který má být zobrazeno zabezpečení zařízení. Proto Azure do Azure provoz může způsobit významné latence a mohla ovlivnit výkon aplikace a uživatelské prostředí, pokud bylo nuceno zpět do místní sítě pro vynucení zásad zabezpečení.

#### <a name="3-how-are-the-boundaries-implemented"></a>3), jak jsou implementované hranice?
Každá hranice zabezpečení bude pravděpodobně mít požadavků na různé možnosti (například ID a pravidla brány firewall na straně Internetu hraniční síti, ale jenom na seznamy ACL mezi hraniční sítě a podsítě back-end). Rozhodování, kdy zařízení (nebo kolik zařízení) používat závisí na scénáři a bezpečnostní požadavky. Příklady 1, 2 a 3 v následující části popisují některé možnosti, které by bylo možné použít. Kontrola funkce nativní síť Azure a zařízení v Azure k dispozici z ekosystém partnerů ukazuje řadu možnosti řešení téměř každý scénář.

Dalším kritériem při rozhodování klíče implementace je, jak připojit místní sítě s Azure. Můžete použít bránu Azure virtuální nebo virtuální síťové zařízení? Tyto možnosti jsou popsány podrobněji v následující části (příklady 4, 5 a 6).

Kromě toho může být potřeba provoz mezi virtuálními sítěmi v rámci Azure. Tyto scénáře se přidají v budoucnu.

Jakmile musíte znát odpovědi na otázky předchozí [rychlého spuštění](#fast-start) část vám pomůže zjistit, které příkladů nejlépe vyhovuje danému scénáři.

## <a name="examples-building-security-boundaries-with-azure-virtual-networks"></a>Příklady: Vytváření hranic zabezpečení s virtuální sítě Azure
### <a name="example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs"></a>Příklad 1 sestavení hraniční síti k ochraně aplikací pomocí skupin Nsg
[Zpět na rychlý start](#fast-start) | [podrobné pokyny v tomto příkladu pro sestavení][Example1]

[![7]][7]

#### <a name="environment-description"></a>Popis prostředí
V tomto příkladu je odběr, který obsahuje následující prostředky:

- Jedna skupina prostředků
- Virtuální síť se dvěma podsítěmi: "FrontEnd" a "Back-end"
- Skupinu zabezpečení sítě, který se použije pro obě podsítě
- Windows server, který představuje server webových aplikací ("IIS01")
- Dva servery Windows, které představují servery back-end aplikace ("AppVM01", "AppVM02")
- Windows server, který představuje server DNS ("DNS01")
- Veřejné IP adresy přidružené k serveru webové aplikace

Skripty a šablonu Azure Resource Manager najdete v tématu [podrobné pokyny pro sestavení][Example1].

#### <a name="nsg-description"></a>Popis skupiny NSG
V tomto příkladu je skupinu NSG vytvořené a pak načtená šesti pravidla.

> [!TIP]
> Obecně řečeno by měl vytvářet specifická pravidla "Povolit" první, a potom více obecná pravidla "Deny". Zadaná priorita Určuje, která pravidla se vyhodnocují jako první. Jakmile provoz nenajde Pokud chcete použít pro konkrétní pravidlo, jsou vyhodnotit žádná další pravidla. Pravidla NSG můžete použít v obou příchozí nebo odchozí směr (z hlediska podsítě).
>
>

Následující pravidla deklarativně, se budou vytvářeny pro příchozí provoz:

1. Interní DNS provoz (port 53) je povolený.
2. Provoz protokolu RDP (portu 3389) z Internetu do jakéhokoli virtuálního počítače je povolena.
3. Je povolen přenos HTTP (port 80) z Internetu webový server (IIS01).
4. Všechny přenosy (všechny porty) z IIS01 na AppVM1 je povolen.
5. Přenosy dat (všechny porty) z Internetu do celý virtuální sítě (obě podsítě) byl odepřen.
6. Přenosy dat (všechny porty) z podsítě front-endu do podsítě back-end byl odepřen.

Pomocí těchto pravidel vázána na každou podsíť, pokud požadavek HTTP byl příchozí z Internetu webový server, jak pravidla 3 (Povolit) a 5 (Odepřít) by použít. Ale vzhledem k tomu, že pravidlo 3 má vyšší prioritu, pouze by použít, a pravidla 5 nebude možné uplatnit. Proto bude mít možnost požadavku HTTP k webovému serveru. Pokud tento stejný provoz se pokusil připojit k serveru DNS01, pravidla 5 (Odepřít) bude první použití, a provoz nebude možné předat serveru. Pravidlo 6 (Odepřít) blokuje front-end podsíť z rozhovoru s back-end podsítě (s výjimkou povolené přenosy v pravidlech 1 a 4). Této sady pravidel chrání síť back-end v případě, že útočník ohrožuje webové aplikace front-endu. Útočník by mít omezený přístup k síti "chráněné" back-end (pouze pro prostředky zveřejněné na serveru AppVM01).

Je výchozí odchozí pravidlo, které umožňuje přenos se k Internetu. V tomto příkladu jsme se umožňuje odchozí provoz a úprava není žádná odchozí pravidla. Zamknout provoz v obou směrech, uživatelem definované směrování je požadovaná (viz příklad 3).

#### <a name="conclusion"></a>Závěr
V tomto příkladu je relativně jednoduché a snadný způsob izolace back-end podsíť z příchozí provoz. Další informace najdete v tématu [podrobné pokyny pro sestavení][Example1]. Tyto pokyny obsahují:

* Jak vytvořit tuto hraniční síť pomocí klasického skriptů prostředí PowerShell.
* Jak vytvořit tuto hraniční síť pomocí šablony Azure Resource Manager.
* Podrobný popis každého příkazu NSG.
* Scénáře podrobné provoz tok zobrazuje jak povolené nebo zakázané v každé vrstvě přenosy.


### <a name="example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs"></a>Příklad 2 sestavení hraniční síti k ochraně aplikací s bránou firewall a skupiny Nsg
[Zpět na rychlý start](#fast-start) | [podrobné pokyny v tomto příkladu pro sestavení][Example2]

[![8]][8]

#### <a name="environment-description"></a>Popis prostředí
V tomto příkladu je odběr, který obsahuje následující prostředky:

* Jedna skupina prostředků
* Virtuální síť se dvěma podsítěmi: "FrontEnd" a "Back-end"
* Skupinu zabezpečení sítě, který se použije pro obě podsítě
* Virtuální zařízení sítě, v tomto případě a brány firewall, připojený k podsíti front-endu
* Windows server, který představuje server webových aplikací ("IIS01")
* Dva servery Windows, které představují servery back-end aplikace ("AppVM01", "AppVM02")
* Windows server, který představuje server DNS ("DNS01")

Skripty a šablonu Azure Resource Manager najdete v tématu [podrobné pokyny pro sestavení][Example2].

#### <a name="nsg-description"></a>Popis skupiny NSG
V tomto příkladu je skupinu NSG vytvořené a pak načtená šesti pravidla.

> [!TIP]
> Obecně řečeno by měl vytvářet specifická pravidla "Povolit" první, a potom více obecná pravidla "Deny". Zadaná priorita Určuje, která pravidla se vyhodnocují jako první. Jakmile provoz nenajde Pokud chcete použít pro konkrétní pravidlo, jsou vyhodnotit žádná další pravidla. Pravidla NSG můžete použít v obou příchozí nebo odchozí směr (z hlediska podsítě).
>
>

Následující pravidla deklarativně, se budou vytvářeny pro příchozí provoz:

1. Interní DNS provoz (port 53) je povolený.
2. Provoz protokolu RDP (portu 3389) z Internetu do jakéhokoli virtuálního počítače je povolena.
3. Všechny přenosy z Internetu (všechny porty) pro virtuální síťové zařízení (bránu firewall) je povolen.
4. Všechny přenosy (všechny porty) z IIS01 na AppVM1 je povolen.
5. Přenosy dat (všechny porty) z Internetu do celý virtuální sítě (obě podsítě) byl odepřen.
6. Přenosy dat (všechny porty) z podsítě front-endu do podsítě back-end byl odepřen.

Pomocí těchto pravidel vázána na každou podsíť, pokud požadavek HTTP byl příchozí z Internetu do brány firewall, jak pravidla 3 (Povolit) a 5 (Odepřít) by použít. Ale vzhledem k tomu, že pravidlo 3 má vyšší prioritu, pouze by použít, a pravidla 5 nebude možné uplatnit. Proto bude mít možnost požadavek HTTP do brány firewall. Pokud tento stejný provoz pokoušel připojit k serveru IIS01, i když je počítač ve front-endu podsíti, pravidla 5 (Odepřít) by použít, a provoz nebude možné předat serveru. Pravidlo 6 (Odepřít) blokuje front-end podsíť z rozhovoru s back-end podsítě (s výjimkou povolené přenosy v pravidlech 1 a 4). Této sady pravidel chrání síť back-end v případě, že útočník ohrožuje webové aplikace front-endu. Útočník by mít omezený přístup k síti "chráněné" back-end (pouze pro prostředky zveřejněné na serveru AppVM01).

Je výchozí odchozí pravidlo, které umožňuje přenos se k Internetu. V tomto příkladu jsme se umožňuje odchozí provoz a úprava není žádná odchozí pravidla. Zamknout provoz v obou směrech, uživatelem definované směrování je požadovaná (viz příklad 3).

#### <a name="firewall-rule-description"></a>Popis pravidla brány firewall
V bráně firewall by se vytvořit pravidla předávání. Pravidlo je potřeba, protože v tomto příkladu pouze trasy internetové přenosy v vázané do brány firewall a potom na webový server pouze jeden přesměrování síťový překlad adres (NAT).

Pravidlo předávání přijme všechny příchozí zdroj adresu, která dotkne bránu firewall, při pokusu o přístup protokolu HTTP (port 80 nebo 443 pro protokol HTTPS). Má odeslat mimo bránu firewall místního rozhraní a přesměruje na webový server s IP adresou 10.0.1.5.

#### <a name="conclusion"></a>Závěr
V tomto příkladu je poměrně snadný způsob, jak ochraně vaší aplikace s bránou firewall a izolace back-end podsíť z příchozí přenosy. Další informace najdete v tématu [podrobné pokyny pro sestavení][Example2]. Tyto pokyny obsahují:

* Jak vytvořit tuto hraniční síť pomocí klasického skriptů prostředí PowerShell.
* Jak vytvořit v tomto příkladu pomocí šablony Azure Resource Manager.
* Podrobný popis každého příkazu a brány firewall pravidla NSG.
* Scénáře podrobné provoz tok zobrazuje jak povolené nebo zakázané v každé vrstvě přenosy.

### <a name="example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg"></a>Příklad 3 sestavení hraniční síti k ochraně sítí s použitím brány firewall a UDR a NSG
[Zpět na rychlý start](#fast-start) | [podrobné pokyny v tomto příkladu pro sestavení][Example3]

[![9]][9]

#### <a name="environment-description"></a>Popis prostředí
V tomto příkladu je odběr, který obsahuje následující prostředky:

* Jedna skupina prostředků
* Virtuální síť s tři podsítě: "SecNet", "FrontEnd" a "Back-end"
* Virtuální zařízení sítě, v tomto případě a brány firewall, připojený k podsíti SecNet
* Windows server, který představuje server webových aplikací ("IIS01")
* Dva servery Windows, které představují servery back-end aplikace ("AppVM01", "AppVM02")
* Windows server, který představuje server DNS ("DNS01")

Skripty a šablonu Azure Resource Manager najdete v tématu [podrobné pokyny pro sestavení][Example3].

#### <a name="udr-description"></a>Popis UDR
Ve výchozím nastavení následující systémové trasy, které jsou definované jako:

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

VNETLocal je vždy jeden nebo více definovaných adresních předpon, které tvoří virtuální sítě pro tuto konkrétní síť (to znamená, změní z virtuální sítě k virtuální síti, v závislosti na tom, jak je definována každou konkrétní virtuální síť). Zbývající systémové trasy statické a výchozí jako uvedené v tabulce.

V tomto příkladu dvou směrovacích tabulek je vytvořen, jeden pro podsítě front-end a back-end. Každá tabulka je načtena s statické trasy, které jsou vhodné pro dané podsíti. V tomto příkladu každá tabulka měla tři tras, které směrovat všechen provoz (0.0.0.0/0) přes bránu firewall (dalšího směrování = virtuální zařízení IP adresa):

1. Provozu místních podsítí s žádné další směrování definované umožňující provozu místních podsítí obejít bránu firewall.
2. Virtuální síťový provoz s další směrování definovat jako bránu firewall. Tato další segment přepisuje výchozí pravidlo, které umožňuje místní virtuální síťový provoz směrovat přímo.
3. Všechny zbývající provoz (0/0) se na další směrování definovat jako bránu firewall.

> [!TIP]
> V místní podsíti komunikace zalomení UDR nemá položku místní podsíti.
>
> * V našem příkladu je důležité 10.0.1.0/24 odkazující na VNETLocal! Bez toho paketů a Webový Server (10.0.1.4) určené na jiný server pro místní 10.0.1.25 (například) selžou, protože se odešlou do hodnocení chyb zabezpečení. Hodnocení chyb zabezpečení bude odesílat do podsítě a podsíť ji odešlete do hodnocení chyb zabezpečení v nekonečné smyčce.
> * Pravděpodobnost smyčka směrování jsou obvykle vyšší na zařízení s více síťovými kartami, které jsou připojené k oddělení podsítí, což je často tradiční, místní zařízení.
>
>

Po vytvoření směrovacích tabulek jsou musí být vázána na podsítě. Směrovací tabulky front-end podsíť po vytvoření a vázaný k podsíti, bude vypadat podobně jako tento výstup:

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

> [!NOTE]
> UDR můžete teď použijí na podsíť brány, na který je připojen okruh ExpressRoute.
>
> Příklady jak povolit hraniční síti s ExpressRoute nebo sítě site-to-site jsou uvedeny v příkladech 3 a 4.
>
>

#### <a name="ip-forwarding-description"></a>Popis předávání IP
Předávání IP adres je doprovodný funkce, která UDR. Předávání IP adres je nastavení na virtuální zařízení, který umožní přijímání dat adresovaných není konkrétně pro zařízení a pak tento přenosu do konečného ultimate.

Například pokud AppVM01 provede požadavek na server DNS01, že UDR by tento provoz trasy do brány firewall. U povoleno předávání IP je provoz pro cíl DNS01 (10.0.2.4) akceptovat zařízení (10.0.0.4) a potom předána do cílového ultimate (10.0.2.4). Bez předávání IP zapnuta brána firewall nebude možné provoz přijímat zařízení Přestože směrovací tabulka má bránu firewall jako další segment. Pokud chcete používat virtuální zařízení, je důležité si pamatovat, abyste povolili předávání IP spolu s UDR.

#### <a name="nsg-description"></a>Popis skupiny NSG
Skupinu NSG v tomto příkladu je vytvořen a pak načten s jedním pravidlem. Tato skupina je pak vázána pouze k podsítím front-end a back-end (ne SecNet). Deklarativně se sestavuje následující pravidlo:

* Přenosy dat (všechny porty) z Internetu do celý virtuální sítě (všechny podsítě) byl odepřen.

I když v tomto příkladu se používají skupiny Nsg, jeho hlavním účelem je jako vrstva sekundární ochranu proti ruční chybné konfigurace. Cílem je blokovat veškerý příchozí provoz z Internetu do front-end nebo back-end podsítí. Provoz by měl pouze procházet skrz SecNet podsítě do brány firewall (a pak v případě potřeby k podsítě front-end nebo back-end). Plus s pravidly UDR v místě, jakýkoli přenos, který zkontrolujte do podsítí front-end nebo back-end by přesměrováni se do brány firewall (díky UDR). Brány firewall by se zobrazit tento provoz asymetrické toku a by vyřadit odchozí přenosy. Proto jsou tři vrstvy zabezpečení, ochraně podsítě:

* Žádné veřejné IP adresy na všech front-end nebo back-end síťové adaptéry.
* Skupiny Nsg odepření přenosy z Internetu.
* Vyřazování brány firewall asymetrické provoz.

Jeden bod zajímavé týkající se NSG v tomto příkladu je, že obsahuje pouze jeden pravidlo, které je tak, aby odepřel internetové přenosy na celý virtuální síť včetně podsítě zabezpečení. Ale vzhledem k tomu, že NSG je vázaný jenom na podsítě front-end a back-end, pravidlo není zpracován na provoz příchozí na podsíť. možnosti zabezpečení. V důsledku toho přenosy na podsíť. možnosti zabezpečení.

#### <a name="firewall-rules"></a>Pravidla brány firewall
V bráně firewall by se vytvořit pravidla předávání. Vzhledem k tomu, že brána firewall je blokování nebo předávání všechny vstupní, výstupní a přenosy uvnitř virtuální sítě, je potřeba řada pravidla brány firewall. Navíc veškerý příchozí provoz dotkne služby zabezpečení veřejnou IP adresu (na jiné porty), mají být zpracovány bránou firewall. Osvědčeným postupem je diagram logické toky před nastavením podsítě a pravidla brány firewall, aby se zabránilo přepracování později. Na následujícím obrázku je logický zobrazení pravidla brány firewall v tomto příkladu:

![10]

> [!NOTE]
> Založená na síti virtuální zařízení používá, správu porty liší. V tomto příkladu je odkazováno Barracuda NextGen Firewall, porty 22, 801 a 807, který používá. Najdete v dokumentaci výrobce zařízení najít přesnou porty používané ke správě zařízení používá.
>
>

#### <a name="firewall-rules-description"></a>Popis pravidla brány firewall
Na předchozím obrázku logické podsítě zabezpečení není zobrazen, protože brána firewall je pouze prostředků na této podsíti. Diagram se zobrazuje pravidla brány firewall a jak se logicky povolit nebo odepřít tok přenosů dat, nikoli skutečné směrované cestu. Také externí porty, vybraný pro provoz protokolu RDP jsou vyšší pohyboval porty (8014 – 8026) a nebyly vybrány volně vyrovnání v posledních dvou oktety místní IP adresu pro snazší čitelnost (například místní server adresu 10.0.1.4 přidružen port externí 8014). Žádné vyšší porty konfliktní, ale může použít.

V tomto příkladu potřebujeme sedm typů pravidel:

* Externí pravidla (pro příchozí provoz):
  1. Pravidlo brány firewall správy: Tato aplikace přesměrování pravidlo umožňuje přenos dat na porty správy zařízení virtuální sítě.
  2. Pravidla protokolu RDP (pro každý server systému Windows): tyto čtyři pravidla (jeden pro každý server), povolit správu jednotlivých serverů prostřednictvím protokolu RDP. Čtyři pravidla RDP může také sbaleny do jednoho pravidla, v závislosti na možnosti síťového virtuální zařízení používá.
  3. Pravidla pro provoz aplikace: existují dvě pravidla, první pro front-end webové přenosy a druhý pro přenosy back-end (například webový server na datové vrstvě). Konfigurace pravidla závisí na síťovou architekturu (kde jsou umístěné vaše servery) a provoz toků (směru tok přenosů dat a který porty se používají).
     * První pravidlo umožňuje přenos skutečné aplikaci připojit k serveru aplikace. Zatímco ostatní pravidla povolit pro zabezpečení a správu, jsou pravidla pro provoz aplikace, co povolit externí uživatele nebo službám přístup k aplikacím. V tomto příkladu je jednom webovém serveru na portu 80. Proto jediné aplikace pravidlo firewallu přesměrování příchozí provoz do externí IP adresy, na webové servery interní IP adresu. Relace přesměrovaného provoz by možné přeložit prostřednictvím NAT interního serveru.
     * Druhé pravidlo je pravidlo back-end umožňuje webový server, aby komunikoval s AppVM01 serveru (ale ne AppVM02) prostřednictvím libovolný port.
* Vnitřní pravidla (pro přenosy uvnitř virtuální sítě.)
  1. Odchozí do internetové pravidlo: Toto pravidlo umožňuje přenosy z jakékoli sítě mají být předána do vybraných sítí. Toto pravidlo je obvykle výchozí pravidlo už v bráně firewall, ale v zakázaném stavu. Toto pravidlo by měly být povoleny v tomto příkladu.
  2. Pravidlo DNS: Toto pravidlo umožňuje předat serveru DNS pouze provoz DNS (port 53). Pro toto prostředí je blokována většina provoz z front-endu na back-end. Toto pravidlo umožňuje konkrétně DNS z jakékoli místní podsítě.
  3. Podsítě, která se pravidlo podsítě: Toto pravidlo se má povolit všechny servery v podsíti back-end připojení k libovolnému serveru na front-endu podsítě (ale nikoli naopak).
* Pohotovostního pravidlo (pro provoz, který nesplňuje některé z předchozí):
  1. Všechny přenosy pravidlo odepřít: Toto pravidlo Odepřít by měla být vždy poslední pravidlo (z hlediska priorita), a jako takový přenosový tok selže-li odpovídat žádné z předchozí pravidla je vyřazeno tímto pravidlem. Toto pravidlo je výchozí pravidlo a obvykle na místě a aktivní. K tomuto pravidlu obvykle je nutné provést žádné změny.

> [!TIP]
> Na druhé pravidlo provoz aplikace ke zjednodušení tento příklad jakéhokoli portu je povolen. Ve scénáři skutečné nejvíce určitý port a rozsahy adres slouží ke snížení prostor pro útoky toto pravidlo.
>
>

Po vytvoření předchozí pravidel je důležité zkontrolovat prioritu každé pravidlo zajistit provoz povolený nebo zakázaný podle potřeby. V tomto příkladu jsou pravidla v pořadí podle priority.

#### <a name="conclusion"></a>Závěr
V tomto příkladu je složitější ale dokončit způsob ochrany a izolace sítě než v předchozích příkladech. (Příklad 2 chrání pouze aplikace, a příklad 1 právě izoluje podsítě). Tento návrh umožňuje monitorování provoz v obou směrech a chrání nejen příchozí aplikační server, ale vynucuje zásady zabezpečení sítě pro všechny servery v této síti. Také v závislosti na zařízení používá, sledování a auditování úplný provoz se dá dosáhnout. Další informace najdete v tématu [podrobné pokyny pro sestavení][Example3]. Tyto pokyny obsahují:

* Jak vytvořit tento příklad hraniční síti pomocí klasického skriptů prostředí PowerShell.
* Jak vytvořit v tomto příkladu pomocí šablony Azure Resource Manager.
* Podrobný popis jednotlivých UDR NSG příkaz a pravidla brány firewall.
* Scénáře podrobné provoz tok zobrazuje jak povolené nebo zakázané v každé vrstvě přenosy.

### <a name="example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn"></a>Příklad 4 přidat hybridní připojení s site-to-site, virtuální zařízení VPN
[Zpět na rychlý start](#fast-start) | Podrobné pokyny sestavení k dispozici co nejdříve

[![11]][11]

#### <a name="environment-description"></a>Popis prostředí
Hybridní sítě pomocí virtuální síťové zařízení (hodnocení chyb zabezpečení) mohou být přidány do hraniční sítě typy popsané v příkladech 1, 2 nebo 3.

Jak je vidět na předchozím obrázku, připojení k síti VPN přes Internet (site-to-site) slouží k připojení k místní síti pro virtuální síť Azure prostřednictvím hodnocení chyb zabezpečení.

> [!NOTE]
> Pokud používáte ExpressRoute s povolenou možností veřejný partnerský vztah Azure, by se vytvořit statickou trasu. Tato statického směrování by měl směrovat na hodnocení chyb zabezpečení sítě VPN IP adresu mimo podnikovou síť intranet a nikoli prostřednictvím připojení ExpressRoute. NAT požadované možnosti ExpressRoute Azure veřejného partnerského vztahu můžete rozdělit relace VPN.
>
>

Po v místní síti VPN změní hodnocení chyb zabezpečení centrální rozbočovač pro všechny sítě a podsítě. Předávání pravidla brány firewall určit, které přenosové toky jsou povoleny, jsou převedeny přes NAT, zprávy jsou přesměrovány nebo zahozených (i pro toky přenosů dat mezi místní sítí a Azure).

Přenosové toky považovat za pečlivě, jak lze optimalizovat a snížení podle tohoto vzoru návrhu v závislosti na konkrétní případ použití.

Použití prostředí součástí příklad 3 a následným přidáním připojení site-to-site VPN hybridní sítě vytváří následující návrhu:

[![12]][12]

Místní směrovač nebo jiné zařízení sítě, který je kompatibilní s vaší hodnocení chyb zabezpečení sítě pro síť VPN, bude klient VPN. Toto fyzické zařízení zodpovídá za inicializaci a udržování připojení k síti VPN se vaše hodnocení chyb zabezpečení.

Logicky k hodnocení chyb zabezpečení, sítě vypadá čtyři samostatné "zóny zabezpečení" s pravidly na hodnocení chyb zabezpečení se primární ředitel provozu mezi tyto zóny:

![13]

#### <a name="conclusion"></a>Závěr
Přidání sítě site-to-site VPN hybridní připojení k virtuální síti Azure můžete rozšířit do místní sítě do Azure zabezpečeným způsobem. Pomocí připojení k síti VPN, provozu je šifrovaný a směruje přes Internet. Hodnocení chyb zabezpečení sítě v tomto příkladu poskytuje centrální umístění pro vynucení a spravovat zásady zabezpečení. Další informace najdete v tématu sestavení podrobné pokyny (řádně doloženo). Tyto pokyny obsahují:

* Jak vytvořit tento příklad hraniční síti pomocí skriptů prostředí PowerShell.
* Jak vytvořit v tomto příkladu pomocí šablony Azure Resource Manager.
* Scénáře podrobné provoz tok zobrazuje tok provozu prostřednictvím tohoto návrhu.

### <a name="example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway"></a>Příklad 5 přidat hybridní připojení s brány Azure VPN site-to-site,
[Zpět na rychlý start](#fast-start) | Podrobné pokyny sestavení k dispozici co nejdříve

[![14]][14]

#### <a name="environment-description"></a>Popis prostředí
Hybridní sítě pomocí služby Azure VPN gateway mohou být přidány do libovolného hraniční sítě popsané v příkladech 1 nebo 2.

Jak je vidět na předchozím obrázku, připojení k síti VPN přes Internet (site-to-site) slouží k připojení k místní síti pro virtuální síť Azure přes bránu VPN Azure VPN.

> [!NOTE]
> Pokud používáte ExpressRoute s povolenou možností veřejný partnerský vztah Azure, by se vytvořit statickou trasu. Tato statického směrování by měl směrovat na hodnocení chyb zabezpečení sítě VPN IP adresu mimo podnikovou síť intranet a nikoli prostřednictvím sítě WAN ExpressRoute. NAT požadované možnosti ExpressRoute Azure veřejného partnerského vztahu můžete rozdělit relace VPN.
>
>

Následující obrázek ukazuje dva okraje sítě v tomto příkladu. Na první okraji a Nsg a hodnocení chyb zabezpečení řídit tok přenosů dat uvnitř Azure sítích a mezi Azure a Internet. Druhý okraj je brána Azure VPN, který je samostatný izolovanou síť okraje mezi místními a Azure.

Přenosové toky považovat za pečlivě, jak lze optimalizovat a snížení podle tohoto vzoru návrhu v závislosti na konkrétní případ použití.

Použití prostředí vytvořené v příkladu 1 a následným přidáním připojení site-to-site VPN hybridní sítě vytváří následující návrhu:

[![15]][15]

#### <a name="conclusion"></a>Závěr
Přidání sítě site-to-site VPN hybridní připojení k virtuální síti Azure můžete rozšířit do místní sítě do Azure zabezpečeným způsobem. Provozu pomocí nativní brány Azure VPN, je šifrovanými v protokolu IPSec a směruje se přes Internet. Pomocí služby Azure VPN gateway také můžete poskytnout nižší náklady možnost (žádné další licence náklady stejně jako u jiných výrobců NVAs). Tato možnost je nejekonomičtější v příkladu 1, kde se používá žádné hodnocení chyb zabezpečení. Další informace najdete v tématu sestavení podrobné pokyny (řádně doloženo). Tyto pokyny obsahují:

* Jak vytvořit tento příklad hraniční síti pomocí skriptů prostředí PowerShell.
* Jak vytvořit v tomto příkladu pomocí šablony Azure Resource Manager.
* Scénáře podrobné provoz tok zobrazuje tok provozu prostřednictvím tohoto návrhu.

### <a name="example-6-add-a-hybrid-connection-with-expressroute"></a>Příklad 6 přidat hybridní připojení s ExpressRoute
[Zpět na rychlý start](#fast-start) | Podrobné pokyny sestavení k dispozici co nejdříve

[![16]][16]

#### <a name="environment-description"></a>Popis prostředí
Hybridní sítě pomocí připojení soukromého partnerského vztahu ExpressRoute můžete přidat buď hraniční sítě typu popsané v příkladech 1 nebo 2.

Jak je vidět na předchozím obrázku, soukromý partnerský vztah ExpressRoute poskytuje přímé připojení mezi místní sítí a virtuální síť Azure. Provoz transits pouze síti poskytovatele služeb a sítě pro Microsoft Azure, nikdy klepnou na Internetu.

> [!TIP]
> Vypnout Internet pomocí služby ExpressRoute udržuje podnikové síťové přenosy. Umožňuje také pro smlouvy o úrovni služeb od poskytovatele ExpressRoute. Bránu Azure můžete předat až 10 GB/s s ExpressRoute, zatímco maximální propustnost brány Azure VPN site-to-site, je 200 MB/s.
>
>

Jak je vidět v následujícím diagramu, pomocí této možnosti prostředí teď má dva okraje sítě. Hodnocení chyb zabezpečení a NSG řídit tok přenosů dat uvnitř Azure sítích a mezi Azure a Internet, při brány je okraj samostatné izolovanou síť mezi místními a Azure.

Přenosové toky považovat za pečlivě, jak lze optimalizovat a snížení podle tohoto vzoru návrhu v závislosti na konkrétní případ použití.

Použití prostředí vytvořené v příkladu 1 a následným přidáním ExpressRoute hybridní síťového připojení, vytváří následující návrhu:

[![17]][17]

#### <a name="conclusion"></a>Závěr
Přidání síťovým připojením ExpressRoute privátní partnerský vztah můžete rozšířit do místní sítě do Azure v zabezpečené, nižší latenci a vyšší provádění způsobem. Také pomocí nativní Azure Gateway jako v následujícím příkladě nabízí nižší náklady možnost (žádné další licencování stejně jako u jiných výrobců NVAs). Další informace najdete v tématu sestavení podrobné pokyny (řádně doloženo). Tyto pokyny obsahují:

* Jak vytvořit tento příklad hraniční síti pomocí skriptů prostředí PowerShell.
* Jak vytvořit v tomto příkladu pomocí šablony Azure Resource Manager.
* Scénáře podrobné provoz tok zobrazuje tok provozu prostřednictvím tohoto návrhu.

## <a name="references"></a>Odkazy
### <a name="helpful-websites-and-documentation"></a>Užitečné weby a dokumentace
* Přístup k Azure s Azure Resource Manager:
* Přístup k Azure pomocí prostředí PowerShell: [https://docs.microsoft.com/powershell/azureps-cmdlets-docs/](/powershell/azure/overview)
* Virtuální sítě dokumentaci: [https://docs.microsoft.com/azure/virtual-network/](https://docs.microsoft.com/azure/virtual-network/)
* Dokumentaci skupiny zabezpečení sítě: [https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg](virtual-network/security-overview.md)
* Uživatelem definované směrování dokumentaci: [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview](virtual-network/virtual-networks-udr-overview.md)
* Azure virtuální brány: [https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway/)
* Site-to-Site VPN: [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell](vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* Dokumentace ExpressRoute (Nezapomeňte najdete v částech "Začínáme" a "Jak na"): [https://docs.microsoft.com/azure/expressroute/](https://docs.microsoft.com/azure/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "vývojový diagram možnosti zabezpečení"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "funkce zabezpečení azure"
[3]: ./media/best-practices-network-security/dmzcorporate.png "DMZ A v podnikové síti"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "Architektura zabezpečení azure"
[5]: ./media/best-practices-network-security/dmzazure.png "hraniční sítě v Azure virtuální sítě"
[6]: ./media/best-practices-network-security/dmzhybrid.png "hybridní sítě s hranicemi tři zabezpečení"
[7]: ./media/best-practices-network-security/example1design.png "příchozí DMZ s NSG"
[8]: ./media/best-practices-network-security/example2design.png "příchozí DMZ s hodnocení chyb zabezpečení a NSG"
[9]: ./media/best-practices-network-security/example3design.png "DMZ obousměrně s hodnocení chyb zabezpečení, NSG a UDR"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "logickém zobrazení pravidla brány Firewall"
[11]: ./media/best-practices-network-security/example3designoptions.png "DMZ s hodnocení chyb zabezpečení propojená síť hybridní"
[12]: ./media/best-practices-network-security/example4designs2s.png "DMZ s hodnocení chyb zabezpečení sítě, které jsou připojené pomocí sítě Site-to-Site VPN"
[13]: ./media/best-practices-network-security/example4networklogical.png "logické sítě z hlediska hodnocení chyb zabezpečení"
[14]: ./media/best-practices-network-security/example5designoptions.png "DMZ se služba Azure Gateway propojená síť Site-to-Site hybridní"
[15]: ./media/best-practices-network-security/example5designs2s.png "DMZ s Azure bránu pomocí sítě Site-to-Site VPN"
[16]: ./media/best-practices-network-security/example6designoptions.png "DMZ se služba Azure Gateway propojená síť hybridní ExpressRoute"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "DMZ s Azure bránu pomocí připojení ExpressRoute"

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
