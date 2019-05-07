---
title: Osvědčené postupy pro zabezpečení sítě – Microsoft Azure
description: Tento článek obsahuje sadu osvědčené postupy při používání zabezpečení sítě vytvořené v možnosti Azure.
services: security
author: TerryLanfear
manager: barbkess
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/05/2019
ms.author: TomSh
ms.openlocfilehash: 78402d3e388f08eae6652859a71c93ff408a5b0d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65152980"
---
# <a name="azure-best-practices-for-network-security"></a>Osvědčené postupy pro zabezpečení sítě Azure
Tento článek popisuje kolekci osvědčené postupy Azure pro zvýšení zabezpečení sítě. Tyto osvědčené postupy, které jsou odvozeny z našich zkušenostech s Azure networking a prostředí zákazníků, jako je sami.

Pro každý osvědčený postup Tento článek vysvětluje:

* Jaký je doporučený postup
* Proč chcete povolit tento osvědčený postup
* Pokud chcete povolit osvědčený postup, co mohou být způsobeny
* Je to možné alternativy doporučené postupy
* Jak lze zjistíte, jak povolit osvědčený postup

Tyto osvědčené postupy jsou založené na stanovisko shody a možnostech platformy Azure a sady funkcí, protože existují v okamžiku, kdy byla zapsána v tomto článku. Názory a technologie v průběhu času měnit a tento článek bude aktualizován v pravidelných intervalech, aby tyto změny projevily.

## <a name="use-strong-network-controls"></a>Použití ovládacích prvků silné sítě
Můžete se připojit [Azure virtuální počítače (VM)](https://azure.microsoft.com/services/virtual-machines/) a zařízení tak, aby jejich umístěním na jiná síťová zařízení [virtuálním sítím Azure](https://docs.microsoft.com/azure/virtual-network/). To znamená můžete připojit adaptéry virtuální sítě k virtuální síti a povolit založené na TCP/IP komunikaci mezi síťové zařízení. Virtuální počítače připojené ke službě Azure virtual network můžete připojit k zařízení ve stejné virtuální síti, různých virtuálních sítích, Internetu nebo vaše vlastní místní sítě.

Při plánování sítě a zabezpečení vaší sítě, doporučujeme vám, že centralizovat:

- Správu základních síťových funkcí, jako jsou ExpressRoute, virtuální síť a podsíť zřizování a přidělování IP adres.
- Zásady správného řízení síťových prvků zabezpečení, jako je například síťové virtuální zařízení funkce jako ExpressRoute, virtuální sítě a podsítě zřizování nebo přidělování IP adres.

Pokud používáte společnou sadu nástrojů pro správu pro monitorování vaší sítě a zabezpečení vaší sítě, získáte jasné viditelnost do obou. Strategie zabezpečení jednoduché a jednotné snižuje chyby, protože jeho hodnota se zvyšuje lidské porozumění a spolehlivost služby automation.

## <a name="logically-segment-subnets"></a>Logicky segmentu podsítě
Virtuálním sítím Azure jsou podobné ve vaší místní síti LAN. Myšlenku za služby Azure virtual network je, že vytvoříte síť podle jeden privátní adresní prostor IP adres, na kterém můžete umístit všechny virtuální počítače Azure. Privátní adresní prostory IP adres k dispozici jsou ve třídě A (10.0.0.0/8), třídy B (172.16.0.0/12), a rozsahy adres třídy C (192.168.0.0/16).

Osvědčené postupy k segmentaci logicky podsítě patří:

**Osvědčený postup**: Nepřiřazovat pravidla s širokou rozsahy povolit (třeba povolit 0.0.0.0 prostřednictvím 255.255.255.255).  
**Podrobnosti o**: Zajistěte, aby postupy pro řešení potíží bránit nebo zakázat nastavení těchto typů pravidel. Tyto povolit pravidla zájemce o zabezpečení a jsou často nalezen a zneužít red týmy.

**Osvědčený postup**: Větší adresní prostor segmentovat do podsítí.   
**Podrobnosti o**: Použití [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)– na základě podsítě zásady k vytvoření podsítě.

**Osvědčený postup**: Vytvořte řízení přístupu k síti mezi podsítěmi. Směrování mezi podsítěmi dojde automaticky a není nutné ručně nakonfigurovat směrovací tabulky. Ve výchozím nastavení nejsou žádné řízení přístupu k síti mezi podsítěmi, které vytvoříte ve službě Azure virtual network.   
**Podrobnosti o**: Použití [skupinu zabezpečení sítě](../virtual-network/virtual-networks-nsg.md) pro ochranu před nevyžádaný provoz do podsítí Azure. Skupiny zabezpečení sítě jsou jednoduché a stavových paketů kontroly zařízení, která používají přístup 5 řazené kolekce členů (Zdrojová IP adresa, zdrojový port, cílová IP adresa, cílový port a protokol vrstvy 4) k vytvoření pravidel povolení/zákazu pro síťový provoz. Povolí nebo zakážou provoz do a z jednu IP adresu, na a z více IP adres, nebo do a z celé podsítě.

Při použití skupin zabezpečení sítě pro řízení přístupu k síti mezi podsítěmi, stačí vložit prostředky, které patří do stejné zóně zabezpečení nebo role v jejich vlastní podsítě.

**Osvědčený postup**: Vyhněte se malý virtuální sítě a podsítě, aby jednoduchosti a flexibility.   
**Podrobnosti o**: Většina organizací přidat víc prostředků, než zpočátku naplánovat a znovu přidělení adresy je práce náročné. Pomocí malé podsítě přidá hodnotu omezené zabezpečení a mapování skupinu zabezpečení sítě ke každé podsíti přidá režie. Definujte podsítě široce, abyste měli jistotu, že máte flexibilitu pro růst.

**Osvědčený postup**: Zjednodušte správu pravidlo skupiny zabezpečení sítě tak, že definujete [skupiny zabezpečení aplikací](https://docs.microsoft.com/rest/api/virtualnetwork/applicationsecuritygroups).  
**Podrobnosti o**: Definujte skupinu zabezpečení aplikace pro seznam IP adres, které si myslíte, že může v budoucnu změnit nebo použít v rámci víc skupin zabezpečení sítě. Ujistěte se, název aplikace zabezpečení skupiny jasně tak ostatní můžete pochopit jejich obsah a účel.

## <a name="adopt-a-zero-trust-approach"></a>Přijmout přístup založený na nule důvěřovat
Na základě hraniční sítě pracovat na předpokladu, že všechny systémy v rámci sítě může považovat za důvěryhodné. Dnešní zaměstnanci odkudkoli přístup k prostředkům ve vaší organizaci, ale na celé řadě zařízení a aplikací, takže se dají hraniční kontrolních mechanismů pro zabezpečení relevantní. Zásady řízení přístupu, které se soustředí pouze na a přístup k prostředku nejsou dostatečná. Do hlavní větve rovnováhu mezi zabezpečením a produktivitu, správci zabezpečení muset zvážit *jak* přístupu k prostředku.

Sítě musí přejděte od tradiční ochrana, protože sítě můžou být zranitelné vůči porušením: útočník může ohrozit jeden koncový bod v rámci důvěryhodné hranice a rychle proniknutí rozbalte v celé síti. [Nula důvěryhodnosti](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/) eliminovat koncept vztah důvěryhodnosti podle umístění v síti v rámci hraniční sítě. Místo toho použijte nula důvěryhodnosti architektury zařízení a uživatelský vztah důvěryhodnosti deklarace identity budou chránit přístup k organizačním datům a prostředkům. Pro nové iniciativy přijmout přístupy důvěryhodnosti nula, které byly ověřeny vztah důvěryhodnosti v době přístup.

Osvědčené postupy jsou:

**Osvědčený postup**: Poskytnout podmíněný přístup k prostředkům v závislosti na zařízení, identity, assurance, umístění v síti a další.  
**Podrobnosti o**: [Podmíněný přístup Azure AD](../active-directory/conditional-access/overview.md) umožňuje použít ovládací prvky přístupu implementací automatizované rozhodovat o přístupu na základě požadovaných podmínek. Další informace najdete v tématu [spravovat přístup pro správu Azure pomocí podmíněného přístupu](../role-based-access-control/conditional-access-azure-management.md).

**Osvědčený postup**: Povolte přístup k portu až po schválení pracovního postupu.  
**Podrobnosti o**: Můžete použít [just-in-time přístup k virtuálním počítačům v Azure Security Center](../security-center/security-center-just-in-time.md) do uzamknutí příchozí provoz na virtuální počítače Azure, tím omezit vystavení útokům při poskytování snadného přístupu pro připojení k virtuálním počítačům v případě potřeby.

**Osvědčený postup**: Udělit dočasný oprávnění k provedení privilegovaných úlohy, která zabraňuje škodlivém nebo neautorizovaném uživatelé získají přístup po vypršení platnosti oprávnění. Přístup je udělen jenom v případě, že ji uživatelé potřebovat.  
**Podrobnosti o**: Použití just-in-time přístupu k udělení oprávnění k provedení privilegovaných úlohy v Azure AD Privileged Identity Management nebo řešení třetí strany.

Další vývojový stupeň zabezpečení sítě se žádný vztah důvěryhodnosti. Stav kyberútokům jednotky organizacích způsob myšlení "předpokládej chybu zabezpečení", ale tento postup by neměl být omezující. Žádný vztah důvěryhodnosti sítě chránit firemní data a prostředky přitom zajistit, že organizace můžete vytvářet moderní firemní síti pomocí technologie, aby zaměstnanci mohli produktivně pracovat kdykoli, kdekoli a žádným způsobem dostat.

## <a name="control-routing-behavior"></a>Řízení chování směrování
Při umístění virtuálního počítače ve službě Azure virtual network virtuální počítač může připojit k jinému virtuálnímu počítači ve stejné virtuální síti, i když jsou ostatní virtuální počítače v různých podsítích. To je možné, protože tento typ komunikace umožňuje shromažďování systémových tras ve výchozím nastavení povolená. Tyto výchozí trasy mohly virtuální počítače ve stejné virtuální síti k zahájení připojení mezi sebou a s Internetem (pro odchozí komunikaci na Internetu).

I když výchozí systémové trasy, které jsou užitečné pro řadu scénářů nasazení, jsou časy, kdy budete chtít přizpůsobit konfigurace směrování pro vaše nasazení. Můžete nakonfigurovat adresu dalšího směrování k dosažení určitým příjemcům.

Doporučujeme, abyste nakonfigurovali [trasy definované uživatelem](../virtual-network/virtual-networks-udr-overview.md) při nasazování zařízení pro zabezpečení virtuální sítě. Mluvíme o to v pozdější části s názvem [zabezpečit vaše důležité prostředky služeb Azure pro vaše virtuální sítě](azure-security-network-security-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks).

> [!NOTE]
> Trasy definované uživatelem nejsou nutné a výchozí systémové trasy, které obvykle pracovat.
>
>

## <a name="use-virtual-network-appliances"></a>Použijte virtuální síťová zařízení
Skupiny zabezpečení sítě a směrování definovaného uživatelem můžete zadat míru zabezpečení sítě na síť a přenosové vrstvy [modelu OSI](https://en.wikipedia.org/wiki/OSI_model). Ale v některých situacích chcete nebo potřebujete povolit zabezpečení na vysoké úrovni zásobníku. V takových situacích doporučujeme, že nasazujete virtuální síťová zabezpečovací zařízení partnery Azure k dispozici.

Síť Azure zabezpečovací zařízení může poskytovat lepší zabezpečení než co poskytuje ovládací prvky nejvyšší úrovně sítě. Možnosti zabezpečení síťového zařízení zabezpečení virtuální sítě patří:


* Funkce brány firewall
* Neoprávněného vniknutí zjišťování/narušení ochrany před únikem informací
* Správa ohrožení zabezpečení
* Řízení aplikací
* Detekce anomálií založená na síti
* Filtrování webů
* Antivirus
* Ochrana Botnetu

K dispozici virtuální síť Azure zabezpečovací zařízení, najdete [Azure Marketplace](https://azure.microsoft.com/marketplace/) a vyhledejte položku "zabezpečení" a "zabezpečení sítě."

## <a name="deploy-perimeter-networks-for-security-zones"></a>Nasadit hraniční sítí pro zóny zabezpečení
A [hraniční síti](https://docs.microsoft.com/azure/best-practices-network-security) (označované také jako DMZ) je segment fyzické nebo logické sítě, která poskytuje další vrstvu zabezpečení mezi vašimi prostředky a Internetem. Specializované síťová zařízení řízení přístupu na okraji hraniční síti povolit jenom požadované přenosy do vaší virtuální sítě.

Hraniční sítě jsou užitečné, protože správu řízení přístupu vaší sítě vám umožní soustředit se monitorování, protokolování a vytváření sestav na zařízení na hranici vaší virtuální sítí Azure. Hraniční síti je, kde je obvykle povolit distribuované s cílem odepření služeb (DDoS) ochrany před únikem informací, vniknutí zjišťování/narušení ochrany před únikem informací systémy (IDS/IPS), pravidla brány firewall a zásad, filtrování webů, antimalwaru sítě a dalších. Síťová zařízení zabezpečení nacházejí mezi Internetem a virtuální sítí Azure a rozhraní v obou sítích.

I když se jedná o základní koncepci hraniční síti, existuje mnoho různých návrzích jako back-to-back troj adresami a s více adresami.

Založený na nule důvěřovat konceptu již bylo zmíněno dříve, doporučujeme zvážit použití hraniční síti pro všechna nasazení vysoké zabezpečení pro zvýšení úrovně řízení přístupu a zabezpečení sítě pro vaše prostředky Azure. Poskytnout další vrstvu zabezpečení mezi vašimi prostředky a Internetu můžete použít Azure nebo řešení třetí strany:

- Azure nativní ovládací prvky. [Brány Firewall Azure](../firewall/overview.md) a [firewallu webových aplikací ve službě Application Gateway](../application-gateway/overview.md#web-application-firewall) nabízejí základní zabezpečení s plně stavová brána firewall jako služba, integrovanou vysokou dostupnost, škálovatelnost bez omezení cloud filtrování plně kvalifikovaný název domény , podporu pro základní sady pravidel OWASP a jednoduchá instalace a konfigurace.
- Nabídky třetí strany. Hledání [Azure Marketplace](https://azuremarketplace.microsoft.com/) pro firewall nové generace (NGFW) a dalších nabídek třetích stran, které poskytují zabezpečení známých nástrojů a výrazně rozšířené úrovně zabezpečení sítě. Konfigurace může být složitější, ale nabídky třetí strany může vám umožní použít stávající schopnosti a dovednosti.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Vyhněte se na Internetu s vyhrazené linky WAN
Mnoho organizací zvolili hybridní IT trasy. S hybridní IT některé informace ve firmě, jsou v Azure a jiné uchovávat místně. V mnoha případech některé součásti služby provozovaná v Azure v místním i nadále další komponenty.

V hybridním scénáři IT je obvykle nějaký typ připojení mezi místními sítěmi. Připojení mezi místními sítěmi umožňuje společnosti připojení svých místních sítí k virtuálním sítím Azure. K dispozici jsou dvě řešení pro připojení mezi různými místy:

* [Site-to-site VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md). Je důvěryhodné, spolehlivé a navážete technologie, ale připojení probíhá přes internet. Šířka pásma je omezen na maximálně o 200 MB/s. Site-to-site VPN je žádoucí možností v některých scénářích.
* **Azure ExpressRoute**. Doporučujeme, abyste použili [ExpressRoute](../expressroute/expressroute-introduction.md) pro vaše připojení mezi místními sítěmi. ExpressRoute umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes soukromé připojení zajišťované poskytovatelem připojení. Pomocí ExpressRoute může vytvořit připojení ke cloudovým službám Microsoftu, jako je Azure, Office 365 a Dynamics 365. ExpressRoute je vyhrazené sítě WAN propojení mezi místním umístěním nebo poskytovatele hostitelských služeb Microsoft Exchange. Vzhledem k tomu, že telco připojení vašich dat nebude přenosu přes internet, tak se nevystaví na potenciální rizika internetové komunikace.

Umístění připojení ExpressRoute může ovlivnit kapacitu brány firewall, škálovatelnost, spolehlivost a přehled o provozu sítě. Budete muset určit, kam chcete ukončit ExpressRoute v existující sítě (v místním prostředí). Můžete:

- Ukončete mimo bránu firewall (paradigma hraniční síti), pokud budete potřebovat přehled o provozu, pokud je potřeba pokračovat v existujících postupů izolace datových centrech, nebo pokud jste pouze umístění zdroje ve vnější síti v Azure.
- Ukončete za firewallem (paradigma rozšíření sítě). Toto je výchozí doporučení. Ve všech ostatních případech doporučujeme považovat za n-tý datového centra Azure.

## <a name="optimize-uptime-and-performance"></a>Optimalizovat výkon a dostupnost
Pokud služba je vypnutý, není přístupná informace. Pokud je tak, že data nepoužitelné nízký výkon, můžete zvážit data, která mají být nepřístupné. Z hlediska zabezpečení je potřeba cokoli, co můžete vaše služby mít optimální provozuschopnost a výkon.

Oblíbené a efektivní způsob pro zlepšení dostupnosti a výkonu je Vyrovnávání zatížení. Vyrovnávání zatížení je způsob distribuce síťového provozu mezi servery, které jsou součástí služby. Například pokud máte front-end webové servery jako součást služby, můžete použít vyrovnávání zatížení a distribuuje provoz mezi několik front-endové webové servery.

Toto rozdělení provozu, zvýšíte dostupnost, protože pokud jeden z webových serverů přestane být dostupný, nástroj pro vyrovnávání zatížení zastaví odesílání provozu k danému serveru a přesměruje na servery, které jsou stále online. Vyrovnávání zatížení vám také pomůže výkon, protože režie procesoru, síť a paměť obsluze žádostí se distribuuje mezi všechny servery s vyrovnáváním zatížení.

Doporučujeme vám, že nepoužijete Vyrovnávání zatížení, kdykoli je to možné a v závislosti na vašich služeb. Následují scénáře na všech úrovních virtuální sítě Azure a na globální úrovni, spolu s možností služby Vyrovnávání zatížení pro každý.

**Scénář**: Když máte aplikaci, která:

- Vyžaduje ze stejné uživatelské/klientské relace přenosu žádostí do stejného virtuálního počítače back-end. Příklady jsou aplikace nákupních košíků a servery webové pošty.
- Přijímá pouze zabezpečené připojení, takže nešifrovaná komunikace se serverem není nepřijatelnou možností.
- Vyžaduje více požadavků protokolu HTTP na stejné připojení TCP dlouhotrvající směrovat nebo načíst balanced na různé back-end servery.

**Vyrovnávání zatížení možnost**: Použití [Azure Application Gateway](../application-gateway/application-gateway-introduction.md), protokolu HTTP webové provoz nástroje pro vyrovnávání zatížení. Služba Application Gateway podporuje šifrování SSL začátku do konce a [ukončení protokolu SSL](../application-gateway/application-gateway-introduction.md) v bráně. Webové servery může potom dešifrováním šifrování a dešifrování režii a provoz dešifrovat do back-end serverů.

**Scénář**: Je potřeba načíst vyrovnávat příchozí připojení z Internetu mezi servery umístěné ve službě Azure virtual network. Jsou scénáře, kdy jste:

- Máte bezstavové aplikace, které přijímají příchozí požadavky z Internetu.
- Nevyžadují rychlé relace a přesměrování zpracování SSL. Rychlé relace je metoda využívají k dosažení serveru spřažení s vyrovnáváním zatížení aplikace.

**Vyrovnávání zatížení možnost**: Pomocí webu Azure portal k [vytvořit externím vyrovnáváním zatížení](../load-balancer/quickstart-create-basic-load-balancer-portal.md) se šíří příchozí požadavky na víc virtuálních počítačů pro zajištění vyšší úrovně dostupnosti.

**Scénář**: Je potřeba načíst zůstatek připojení z virtuálních počítačů, které nejsou na Internetu. Ve většině případů se inicializuje připojení, která jsou přijati pro vyrovnávání zatížení zařízení ve virtuální síti Azure, jako je instance systému SQL Server nebo interní webové servery.   
**Vyrovnávání zatížení možnost**: Pomocí webu Azure portal k [vytvořit interní nástroj](../load-balancer/quickstart-create-basic-load-balancer-powershell.md) se šíří příchozí požadavky na víc virtuálních počítačů pro zajištění vyšší úrovně dostupnosti.

**Scénář**: Musíte globální zátěže, protože jste:

- Máte cloudové řešení, která je hromadně distribuují napříč několika oblastmi a vyžaduje nejvyšší úroveň dostupnosti (dostupnost) je to možné.
- Třeba nejvyšší úroveň dostupnosti je to možné, aby se zajistilo, že je vaše služba dostupná i v případě, že celé datové centrum přestane být k dispozici.

**Vyrovnávání zatížení možnost**: Použití Azure Traffic Manageru. Traffic Manager umožňuje načíst zůstatek připojení k vašim službám na základě umístění uživatele.

Například pokud uživatel odešle požadavek do služby z EU, připojení se směřuje do vaší služby umístěné v datovém centru EU. Tato část Traffic Manageru globální načíst vyrovnávání pomáhá zlepšit výkon, protože připojení k nejbližší datové centrum je rychlejší než připojení k datovým centrům, která jsou daleko.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>Zakázat přístup RDP/SSH k virtuálním počítačům
Je možné dosáhnout virtuální počítače Azure s použitím [Remote Desktop Protocol](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) a [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) protokol (SSH). Tyto protokoly umožňuje správu virtuálních počítačů ze vzdálených umístění a jsou standardní ve výpočetním prostředí datacenter.

Potenciální problém zabezpečení s použitím těchto protokolů přes internet je, že útočníci můžou používat [útok hrubou silou](https://en.wikipedia.org/wiki/Brute-force_attack) techniky k získání přístupu k Azure virtual machines. Po útočníci získají přístup, mohou použít váš virtuální počítač jako bod spuštění pro ohrožení ostatní počítače ve vaší virtuální síti nebo dokonce napadení síťovými zařízeními mimo Azure.

Doporučujeme zakázat přímý přístup pomocí protokolu RDP a SSH pro virtuální počítače Azure z Internetu. Po zakázání je přímý přístup pomocí protokolu RDP a SSH z Internetu, máte další možnosti, které můžete použít pro přístup k těmto virtuálním počítačům pro vzdálenou správu.

**Scénář**: Povolte u jednoho uživatele pro připojení ke službě Azure virtual network přes internet.   
**Možnost**: [Point-to-site VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md) je jiný termín pro připojení k síti VPN klienta nebo serveru pro vzdálený přístup. Po navázání připojení point-to-site slouží uživatele pomocí protokolu RDP nebo SSH pro připojení k žádné virtuální počítače na virtuální síť Azure, které se uživatel připojil přes point-to-site VPN. Předpokladem je, že je uživatel oprávnění k dosažení ty virtuální počítače.

Point-to-site VPN je bezpečnější než přímé spojení přes protokol RDP nebo SSH, protože uživatel nemá k ověření dvakrát před připojením k virtuálnímu počítači. Nejprve, uživatel musí ověřit (a autorizovat) k navázání připojení VPN typu point-to-site. Za druhé, uživatel musí ověřit (a autorizovat) k vytvoření relace protokolu RDP nebo SSH.

**Scénář**: Povolte uživatelům ve vaší místní síti připojit k virtuálním počítačům ve vaší virtuální sítí Azure.   
**Možnost**: A [site-to-site VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) celá síť se připojuje k jiné síti přes internet. Síť site-to-site VPN můžete použít k připojení místní sítě ke službě Azure virtual network. Uživatelé ve vaší místní síti připojit pomocí protokolu RDP nebo SSH přes připojení VPN typu site-to-site. Není nutné povolit přímý přístup protokolu RDP nebo SSH přes internet.

**Scénář**: Poskytuje funkce podobné VPN typu site-to-site pomocí vyhrazené propojení WAN.   
**Možnost**: Použití [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/). Poskytuje funkce podobné k síti VPN site-to-site. Hlavními rozdíly jsou:

- Vyhrazená propojení WAN nebude procházení Internetu.
- Vyhrazené linky WAN jsou obvykle více stabilní a poskytují větší výkon.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>Zabezpečte vaše důležité prostředky služeb Azure pro vaše virtuální sítě
Pomocí koncových bodů služby virtuální sítě rozšiřují privátní adresní prostor vaší virtuální sítě a její identitu vaší virtuální sítě do služeb Azure přes přímé připojení. Koncové body umožňují svázat vaše důležité prostředky služeb Azure pouze s vašimi virtuálními sítěmi. Provoz z vaší virtuální sítě do služby Azure vždy zůstává v páteřní síti Microsoft Azure.

Koncové body služby poskytují následující výhody:

- **Zlepšení zabezpečení prostředků služeb Azure**: Pomocí koncových bodů služby je možné svázat prostředky služeb Azure s vaší virtuální sítí. Svázání prostředků služeb virtual network poskytuje lepší zabezpečení díky úplnému odebrání veřejného internetového přístupu k prostředkům a povolení provozu pouze z vaší virtuální sítě.
- **Optimální směrování provozu služeb Azure z vaší virtuální sítě**: Všechny trasy ve virtuální síti, které vynucují internetový provoz přes místní nebo virtuální zařízení, což se označuje jako vynucené tunelování, také vynutit provozu služeb Azure stejnou trasou jako internetový provoz. Koncové body služby poskytují optimální směrování provozu Azure.

  Koncové body vždy směrují provoz služby přímo z vaší virtuální sítě do služby v páteřní síti Azure. Udržování provozu na páteřní síti Azure umožňuje pokračovat v auditování a monitorování odchozího internetového provozu z vašich virtuálních sítí prostřednictvím vynuceného tunelování, aniž by to ovlivnilo provoz služeb. Další informace o [trasy definované uživatelem a vynuceném tunelování](../virtual-network/virtual-networks-udr-overview.md).

- **Snadné nastavení menším režie na správu**: Už nepotřebujete vyhrazené veřejné IP adresy ve vaší virtuální sítě k zabezpečení prostředků Azure prostřednictvím brány firewall protokolu IP. K nastavení koncových bodů služby se nevyžaduje překlad adres ani zařízení brány. Koncové body služby se konfigurují pouhým kliknutím na podsíť. Není spojena žádná další režie udržovat koncových bodů.

Další informace o koncových bodů služby a služby Azure a oblasti, které jsou k dispozici pro koncové body služby, najdete v článku [koncové body služeb virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="next-steps"></a>Další postup
Zobrazit [osvědčené postupy zabezpečení Azure a vzory](security-best-practices-and-patterns.md) pro další doporučené postupy zabezpečení, mají použít, když jste návrhu, nasazení a správa cloudových řešení pomocí služby Azure.
