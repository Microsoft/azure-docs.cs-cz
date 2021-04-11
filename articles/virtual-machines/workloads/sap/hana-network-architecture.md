---
title: Architektura sítě SAP HANA v Azure (velké instance) | Microsoft Docs
description: Architektura sítě, jak nasadit SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 92cfa5b2e399811754b57bda64569753ccfa6be8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101668754"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Síťová architektura SAP HANA (velké instance)

Architektura síťových služeb Azure je klíčovou součástí úspěšného nasazení aplikací SAP ve velké instanci HANA. Nasazení na platformě Azure (velké instance) je SAP HANA obvykle větší než SAP na šířku s několika různými řešeními SAP s různou velikostí databází, spotřebou prostředků procesoru a využitím paměti. Je nejspíš to, že ne všechny systémy IT jsou umístěné v Azure už. Vaše technologie SAP na šířku je často hybridní i z bodu DBMS a bod aplikace SAP, který využívá kombinaci NetWeaver a S/4HANA a SAP HANA a dalších systémů DBMS. Azure nabízí různé služby, které umožňují spouštět různé systémy DBMS, NetWeaver a S/4HANA v Azure. Azure také nabízí síťovou technologii, aby Azure vypadal jako virtuální datové centrum pro vaše místní nasazení softwaru.

Pokud vaše kompletní systémy IT nejsou hostované v Azure. Síťové funkce Azure slouží k připojení místního světa k assetům Azure, aby Azure vypadaly jako virtuální datacentrum vašeho vlastního nastavení. Použitá funkce sítě Azure je: 

- Virtuální sítě Azure jsou připojené k okruhu [ExpressRoute](https://azure.microsoft.com/services/expressroute/) , který se připojuje k vašim prostředkům v místní síti.
- Okruh ExpressRoute, který se připojuje místně k Azure, by měl mít minimální šířku pásma [1 GB/s nebo vyšší](https://azure.microsoft.com/pricing/details/expressroute/). Tato minimální šířka pásma umožňuje dostatečnou šířku pásma pro přenos dat mezi místními systémy a systémy, které běží na virtuálních počítačích. Umožňuje taky adekvátní šířku pásma pro připojení k systémům Azure od místních uživatelů.
- Všechny systémy SAP v Azure jsou nastavené ve virtuálních sítích ke vzájemné komunikaci.
- Služba Active Directory a služba DNS hostované místně se rozšiřují do Azure prostřednictvím ExpressRoute z místního prostředí nebo běží v Azure jako dokončené.

Pro konkrétní případ integrace velkých instancí HANA do prostředků infrastruktury sítě Azure Data Center se používá technologie Azure ExpressRoute i


> [!NOTE] 
> Pouze jedno předplatné Azure může být propojeno pouze s jedním klientem v rámci razítka velké instance v konkrétní oblasti Azure. Naopak jeden tenant s razítkem rozsáhlých instancí HANA může být propojený jenom s jedním předplatným Azure. Tento požadavek je konzistentní s jinými Fakturovatelné objekty v Azure.

Pokud je SAP HANA v Azure (velké instance) nasazené v několika různých oblastech Azure, v razítku velké instance HANA se nasadí samostatný tenant. Můžete spustit oba v rámci stejného předplatného Azure, pokud jsou tyto instance součástí stejné technologie SAP na šířku. 

> [!IMPORTANT] 
> Pro SAP HANA v Azure (velké instance) se podporuje jenom Metoda nasazení Azure Resource Manager.

 

## <a name="additional-virtual-network-information"></a>Další informace o virtuální síti

Aby bylo možné připojit virtuální síť k ExpressRoute, je třeba vytvořit bránu Azure ExpressRoute. Další informace najdete v tématu [informace o branách ExpressRoute pro ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md). 

Brána Azure ExpressRoute se používá s ExpressRoute k infrastruktuře mimo Azure nebo k razítku velké instance Azure. Bránu Azure ExpressRoute můžete připojit k maximálně čtyřem různým ExpressRoute okruhům, pokud tato připojení pocházejí z různých hraničních směrovačů Microsoft Enterprise. Další informace najdete v tématu [SAP Hana (velké instance) infrastruktury a připojení v Azure](hana-overview-infrastructure-connectivity.md). 

> [!NOTE] 
> Maximální propustnost, kterou můžete dosáhnout pomocí brány ExpressRoute, je 10 GB/s s použitím připojení ExpressRoute. Kopírování souborů mezi virtuálním počítačem, který se nachází ve virtuální síti, a místním systému (jako jeden kopírovací datový proud) nedosáhne celé propustnosti různých SKU brány. K využití celé šířky pásma brány ExpressRoute použijte více datových proudů. Nebo je nutné zkopírovat různé soubory v paralelních datových proudech jednoho souboru.


## <a name="networking-architecture-for-hana-large-instance"></a>Architektura sítě pro velkou instanci HANA
Architektura sítě pro rozsáhlou instanci HANA může být rozdělená do čtyř různých částí:

- Místní sítě a ExpressRoute připojení k Azure. Tato část je doména zákazníka a je připojená k Azure prostřednictvím ExpressRoute. Tento okruh ExpressRoute se vám plně hradí jako zákazník. Šířka pásma by měla být dostatečně velká, aby bylo možné zpracovávat síťový provoz mezi místními prostředky a oblastí Azure, ke které se připojujete. Viz dole vpravo na následujícím obrázku.
- Síťové služby Azure, jak je popsáno výše, s virtuálními sítěmi, které znovu vyžadují přidané brány ExpressRoute. Tato část je oblast, kde potřebujete najít vhodné návrhy pro požadavky na aplikace, zabezpečení a požadavky na dodržování předpisů. Bez ohledu na to, jestli používáte rozsáhlou instanci HANA, je další bod, který je třeba vzít v úvahu jako počet virtuálních sítí a SKU brány Azure na výběr z. Podívejte se na obrázek vpravo nahoře.
- Připojení velké instance HANA prostřednictvím technologie ExpressRoute do Azure Tato část je nasazena a zpracována společností Microsoft. Vše, co potřebujete udělat, je poskytnout některé rozsahy IP adres po nasazení vašich assetů ve velké instanci HANA připojte okruh ExpressRoute k virtuálním sítím. Další informace najdete v tématu [SAP Hana (velké instance) infrastruktury a připojení v Azure](hana-overview-infrastructure-connectivity.md). Jako zákazník pro připojení mezi síťovými prostředky infrastruktury a velkými instancemi služby Azure Data Center se za vás neúčtují žádné další poplatky.
- Síť v rámci razítka velké instance HANA, která je pro vás hlavně transparentní.

![Virtuální síť připojená k SAP HANA v Azure (velké instance) a místní](./media/hana-overview-architecture/image1-architecture.png)

Požadavek, aby se vaše místní prostředky musely připojit prostřednictvím ExpressRoute k Azure, se nemění, protože používáte velkou instanci HANA. Požadavek na jednu nebo více virtuálních sítí, na kterých běží virtuální počítače, který je hostitelem aplikační vrstvy, která se připojuje k instancím HANA hostovaným v jednotkách velkých instancí HANA, se ani nezmění. 

Rozdíly v nasazení SAP v Azure jsou:

- Jednotky velkých instancí služby HANA vašeho tenanta zákazníka jsou propojeny prostřednictvím jiného okruhu ExpressRoute do vašich virtuálních sítí. Aby bylo možné oddělit podmínky zatížení, nesdílejí stejné směrovače na ExpressRoute okruhy virtuální sítě Azure Virtual Network a okruhy mezi virtuálními sítěmi Azure a velkými instancemi HANA.
- Profil úloh mezi aplikační vrstvou SAP a rozsáhlou instancí HANA má jinou povahu, ale mnoho malých požadavků a shluků, jako jsou přenosy dat (sady výsledků), od SAP HANA do aplikační vrstvy.
- Architektura aplikace SAP je citlivá na latenci sítě než typické scénáře, kdy se data mezi místními a Azure mění.
- Brána Azure ExpressRoute má minimálně dvě připojení ExpressRoute. Jeden okruh, který je připojený z místního prostředí a druhý, který je připojený k velkým instancím HANA. To znamená, že pro připojení k bráně ExpressRoute je místo pro další dva další okruhy z různých směrovači msee. Toto omezení nezávisí na použití rychlé cesty ExpressRoute. Všechny připojené okruhy sdílejí maximální šířku pásma pro příchozí data brány ExpressRoute.

S revizí 3 dlouhých razítek instancí HANA může být latence sítě mezi virtuálními počítači a velkými jednotkami instancí vyšší než typická latence sítě typu virtuální počítač k virtuálnímu počítači. V závislosti na oblasti Azure mohou měřené hodnoty překročit 0,7-MS latenci odezvy v SAP, který je klasifikován jako nižší průměr v tématu [SAP Note #1100926-Nejčastější dotazy: výkon sítě](https://launchpad.support.sap.com/#/notes/1100926/E). V závislosti na oblasti a nástroji Azure měření latence síťové odezvy mezi virtuálním počítačem Azure a jednotkou velké instance je měřená latence může být až do 2 MS. Zákazníci se však úspěšně nasazují SAP HANA aplikace SAP na základě SAP HANA velké instance. Ujistěte se, že vaše obchodní procesy důkladně otestujete ve velké instanci Azure HANA. Nová funkce označovaná jako rychlá cesta ExpressRoute dokáže snížit latenci sítě mezi velkými instancemi HANA a virtuálními počítači aplikační vrstvy v Azure v podstatě (viz níže). 

S revizí 4 velkými objemy instancí HANA se může latence sítě mezi virtuálními počítači Azure nasazenými v blízkosti razítka velké instance naplnit průměrnou nebo vyšší než průměrná klasifikace, jak je uvedeno v tématu [SAP Note #1100926-Nejčastější dotazy: výkon sítě](https://launchpad.support.sap.com/#/notes/1100926/E) , pokud je nakonfigurovaná rychlá cesta Azure ExpressRoute (viz níže). Aby bylo možné nasadit virtuální počítače Azure v těsné blízkosti pro jednotky s velkým počtem instancí revize 4, je nutné využívat [skupiny umístění v blízkosti Azure](../../co-location.md). Způsob, jakým se dají skupiny umístění blízkosti použít k vyhledání vrstvy aplikace SAP ve stejném datovém centru Azure jako revize 4 jednotky velkých instancí HANA, jsou popsané v tématu [skupiny umístění pro zajištění optimální latence sítě s aplikacemi SAP](sap-proximity-placement-scenarios.md).

Aby se zajistila deterministické latence sítě mezi virtuálními počítači a rozsáhlou instancí HANA, je nutné vybrat SKU brány ExpressRoute. Na rozdíl od vzorců přenosů mezi místními a virtuálními počítači může model přenosů mezi virtuálními počítači a rozsáhlou instancí HANA vyvíjet malé, ale i vysoké shluky požadavků a datových svazků, které se mají přenést. Abychom mohli tyto shluky zvládnout správně, důrazně doporučujeme použití SKU brány UltraPerformance. Pro třídu Type II třídy SKU velkých instancí služby HANA je použití SKU brány UltraPerformance jako brány ExpressRoute povinné.

> [!IMPORTANT] 
> Vzhledem k celkovému zatížení sítě mezi aplikací SAP a vrstvami databáze jsou podporovány pouze skladové položky brány HighPerformance nebo UltraPerformance pro virtuální sítě, aby se připojovaly k SAP HANA v Azure (velké instance). U velkých SKU typu instance II pro HANA se jako brána ExpressRoute podporuje jenom SKU brány UltraPerformance. Výjimky se použijí při použití rychlé cesty ExpressRoute (viz níže).

### <a name="expressroute-fast-path"></a>Rychlá cesta k ExpressRoute
Aby se snížila latence, ExpressRoute rychlá cesta byla zavedena a vydána v květnu 2019 pro konkrétní připojení velkých instancí HANA k virtuálním sítím Azure, které hostují virtuální počítače aplikace SAP. Hlavním rozdílem v řešení, které jsme doposud učinili, je to, že datové toky mezi virtuálními počítači a velkými instancemi HANA nejsou již směrovány prostřednictvím brány ExpressRoute. Místo toho virtuální počítače přiřazené v podsítích virtuální sítě Azure přímo komunikují s vyhrazeným hraničním směrovačem rozlehlé sítě. 

> [!IMPORTANT] 
> Funkce rychlé cesty ExpressRoute vyžaduje, aby podsítě, na kterých běží aplikace SAP, byly ve stejné virtuální síti Azure, která je připojená k velkým instancím HANA. Virtuální počítače umístěné ve virtuálních sítích Azure, které jsou v partnerském vztahu s virtuální sítí Azure připojené přímo k jednotkám velkých instancí HANA, nevyužívají ExpressRoute rychlou cestu. V důsledku toho, že se v důsledku vytváření partnerských okruhů připojovat k virtuální síti rozbočovače a k virtuálním sítím, které obsahují vrstvu aplikace SAP (paprsky), se při získávání partnerského vztahu ExpressRoute rychlá cesta nefunkčností ExpressRoute. V kromě Služba ExpressRoute rychlá cesta nepodporuje pravidla směrování definovaná uživatelem (UDR) ještě dnes. Další informace najdete v tématu [Brána virtuální sítě ExpressRoute a FastPath](../../../expressroute/expressroute-about-virtual-network-gateways.md). 


Další podrobnosti o tom, jak nakonfigurovat rychlou cestu ExpressRoute, najdete v dokumentu [připojení virtuální sítě k rozsáhlým instancím Hana](./hana-connect-vnet-express-route.md).    

> [!NOTE]
> Brána UltraPerformance ExpressRoute je nutná k fungování rychlé cesty ExpressRoute.


## <a name="single-sap-system"></a>Jeden systém SAP

Místní infrastruktura, která se dřív zobrazila, je připojená prostřednictvím ExpressRoute do Azure. Okruh ExpressRoute se připojuje k hraničnímu směrovači Microsoft Enterprise (MSEE). Další informace najdete v tématu [ExpressRoute Technical Overview](../../../expressroute/expressroute-introduction.md). Po navázání trasy se připojí k páteřní síti Azure.

> [!NOTE] 
> Pokud chcete spustit SAP krajina v Azure, připojte se k hraničnímu směrovači Enterprise, který je nejblíže oblasti Azure na platformě SAP na šířku. K minimalizaci latence sítě mezi virtuálními počítači v rámci razítek velkých instancí v Azure IaaS a HANA jsou připojená razítka pro velká instance HANA přes vyhrazená zařízení podnikového hraničního směrovače.

Brána ExpressRoute pro virtuální počítače, které jsou hostiteli instancí aplikace SAP, je připojená k jednomu okruhu ExpressRoute, který se připojuje k místnímu. Stejná virtuální síť je připojená k samostatnému hraničnímu směrovači podniku vyhrazenému pro připojení k velkým razítkům instancí. Pomocí rychlé cesty ExpressRoute se tok dat z HANA velkých instancí do virtuálních počítačů aplikační vrstvy SAP nesměrují prostřednictvím brány ExpressRoute a s tím snižují latenci při zpoždění sítě.

Tento systém je jednoduchým příkladem jednoho systému SAP. Aplikační vrstva SAP je hostována v Azure. Databáze SAP HANA běží na SAP HANA v Azure (velké instance). Předpokladem je, že šířka pásma ExpressRoute brány v propustnosti 2 GB/s nebo 10 GB/s nepředstavuje kritický bod.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Více systémů SAP nebo velkých systémů SAP

Pokud se pro připojení k SAP HANA v Azure (velké instance) nasadí víc systémů SAP nebo velkých systémů SAP, může se propustnost brány ExpressRoute stát kritickým bodem. Nebo chcete izolovat produkční a neprodukční systémy v různých virtuálních sítích Azure. V takovém případě rozdělte vrstvy aplikace do více virtuálních sítí. Také můžete vytvořit speciální virtuální síť, která se připojí k velké instanci HANA pro případy, jako například:

- Provádění zálohování přímo z instancí HANA ve velké instanci HANA na virtuální počítač v Azure, který hostuje sdílené složky systému souborů NFS.
- Kopírování velkých záloh nebo jiných souborů z velkých jednotek instance HANA na místo na disku spravované v Azure.

Použijte samostatnou virtuální síť pro hostování virtuálních počítačů, které spravují úložiště pro hromadný přenos dat mezi velkými instancemi HANA a Azure. Toto uspořádání brání vlivům velkých souborů nebo přenosů dat z velké instance HANA do Azure v bráně ExpressRoute, která slouží k virtuálním počítačům, na kterých běží aplikační vrstva SAP. 

Pro škálovatelnou síťovou architekturu:

- Využijte více virtuálních sítí pro jednu, větší aplikační vrstvu SAP.
- Nasaďte jednu samostatnou virtuální síť pro každý nasazený systém SAP a v porovnání s kombinací těchto systémů SAP v samostatných podsítích ve stejné virtuální síti.

  Škálovatelná Síťová architektura pro SAP HANA v Azure (velké instance):

![Nasazení aplikační vrstvy SAP přes více virtuálních sítí](./media/hana-overview-architecture/image4-networking-architecture.png)

V závislosti na pravidlech a omezeních, které chcete použít mezi různými virtuálními sítěmi hostujícími virtuální počítače s různými systémy SAP, byste měli tyto virtuální sítě navázat. Další informace o partnerském vztahu virtuálních sítí najdete v tématu věnovaném [partnerským vztahům virtuální sítě](../../../virtual-network/virtual-network-peering-overview.md).


## <a name="routing-in-azure"></a>Směrování v Azure

Ve výchozím nastavení je pro SAP HANA v Azure (velké instance) důležité tři požadavky na síťové směrování:

* K SAP HANA v Azure (velké instance) se dá získat pøístup jenom prostřednictvím virtuálních počítačů Azure a vyhrazeného připojení ExpressRoute, ne přímo z místního prostředí. Přímý přístup z místního prostředí k jednotkám velkých instancí HANA, jak ho společnost Microsoft doručí vám, není hned možná. Omezení přenosného směrování je způsobeno aktuální síťovou architekturou Azure, která se používá pro SAP HANA velké instance. Někteří klienti pro správu a všechny aplikace, které potřebují přímý přístup, jako je například Správa řešení SAP místně spuštěné v místním prostředí, se nemohou připojit k databázi SAP HANA. U výjimek se podívejte na oddíl přímé směrování na velké instance HANA.

* Pokud máte velké jednotky instancí HANA nasazené ve dvou různých oblastech Azure pro zotavení po havárii, platí stejná přechodná omezení směrování v minulosti. Jinými slovy, IP adresy velké instance jednotky HANA v jedné oblasti (například USA – západ) nebyly směrovány na jednotku velkých instancí HANA nasazenou v jiné oblasti (například USA – východ). Toto omezení bylo nezávislé na použití partnerského vztahu sítě Azure v různých oblastech nebo vzájemném propojení okruhů ExpressRoute, které spojují jednotky velkých instancí HANA s virtuálními sítěmi. V případě grafické reprezentace si přečtěte obrázek v části "použití jednotek velkých instancí HANA ve více oblastech." Toto omezení, které bylo dodáno s nasazenou architekturou, zakázalo okamžité použití replikace systému HANA jako funkce zotavení po havárii. V případě nedávných změn vyhledejte oddíl ' použít velké instance služby HANA v několika oblastech '. 

* Jednotky s SAP HANA v Azure (velké instance) mají přiřazenou IP adresu z rozsahu adres fondu IP adres serveru, který jste odeslali při žádosti o nasazení velké instance HANA. Další informace najdete v tématu [SAP Hana (velké instance) infrastruktury a připojení v Azure](hana-overview-infrastructure-connectivity.md). Tato IP adresa je přístupná prostřednictvím předplatných Azure a okruhu, který propojuje virtuální sítě Azure s velkými instancemi HANA. IP adresa, která je přiřazená z tohoto rozsahu adres fondu IP adres serveru, je přímo přiřazená k hardwarové jednotce. V tomto *případě se už nepřiřazuje prostřednictvím* překladu adres (NAT), jako by to bylo v prvním nasazení tohoto řešení. 

### <a name="direct-routing-to-hana-large-instances"></a>Přímé směrování na velké instance HANA

Ve výchozím nastavení nefunguje přenosná směrování v těchto scénářích:

* Mezi jednotkami velkých instancí HANA a místním nasazením.

* Mezi směrováním velkých instancí HANA nasazenými ve dvou různých oblastech.

Existují tři způsoby, jak povolit tranzitivní směrování v těchto scénářích:

- Reverzní proxy server, který bude směrovat data do a z. Například F5 BIG-IP NGINX s Traffic Manager nasazenými ve službě Azure Virtual Network, která se připojuje k rozsáhlým instancím HANA a místní jako řešení směrování virtuální brány firewall/provozu.
- Použití pravidel softwaru iptables na virtuálním počítači Linux k povolení směrování mezi místními umístěními a velkými jednotkami instancí nebo mezi jednotkami velkých instancí HANA v různých oblastech. Virtuální počítač, na kterém běží softwaru iptables, musí být nasazený ve virtuální síti Azure, která se připojuje k velkým instancím HANA a místně. Virtuální počítač musí mít odpovídající velikost, takže je propustnost sítě virtuálního počítače dostačující pro očekávaný síťový provoz. Podrobnosti o šířce pásma sítě virtuálních počítačů najdete v článku [velikosti virtuálních počítačů se systémem Linux v Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- [Azure firewall](https://azure.microsoft.com/services/azure-firewall/) by bylo jiné řešení umožňující přímý provoz mezi místními jednotkami a velkými instancemi instancí Hana. 

Všechna přenos těchto řešení by se směrovala přes virtuální síť Azure a takové přenosy by taky mohly být omezené pomocí používaných softwarových zařízení nebo skupin zabezpečení sítě Azure, takže se některé IP adresy nebo rozsahy IP adres z místního prostředí můžou zablokovat nebo výslovně povolit přístup k velkým instancím HANA. 

> [!NOTE]  
> Mějte na paměti, že Microsoft neposkytuje implementaci a podporu pro vlastní řešení zahrnující síťová zařízení třetích stran nebo softwaru iptables. Podporu musí poskytnout dodavatel používané komponenty nebo integrátoru. 

#### <a name="express-route-global-reach"></a>Global Reach Express Route
Společnost Microsoft představila nové funkce s názvem [ExpressRoute Global REACH](../../../expressroute/expressroute-global-reach.md). Global Reach lze použít pro velké instance HANA ve dvou scénářích:

- Umožněte přímý přístup z místního prostředí k jednotkám velkých instancí HANA nasazeným v různých oblastech.
- Povolit přímou komunikaci mezi jednotkami velkých instancí HANA nasazenými v různých oblastech


##### <a name="direct-access-from-on-premises"></a>Přímý přístup z místního prostředí
V oblastech Azure, kde je nabízena Global Reach, můžete požádat o povolení funkcí Global Reach pro okruh ExpressRoute, který připojuje vaši místní síť k virtuální síti Azure, která se připojuje k jednotkám velkých instancí HANA. Na místní straně okruhu ExpressRoute se účtují nějaké náklady. Pro ceny si prohlédněte ceny za [Global REACH doplněk](https://azure.microsoft.com/pricing/details/expressroute/). V souvislosti s okruhem, který spojuje jednotky velkých instancí HANA s Azure, se neúčtují žádné další poplatky. 

> [!IMPORTANT]  
> V případě použití Global Reach pro povolení přímého přístupu mezi jednotkami velkých instancí HANA a místními prostředky není síťová data a tok řízení **směrována přes virtuální sítě Azure**, ale přímo mezi směrovači Microsoft Enterprise Exchange. V důsledku toho se žádná pravidla NSG nebo ASG nebo jakýkoli typ brány firewall, síťové virtuální zařízení nebo proxy, který jste nasadili ve službě Azure Virtual Network, nedotkne. **Pokud k povolení přímého přístupu z místního prostředí do systému HANA Velká jednotka instancí použijete ExpressRoute Global Reach a oprávnění pro přístup k jednotkám velkých instancí HANA je potřeba definovat v bránách firewall na místní straně.** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Propojení velkých instancí HANA v různých oblastech Azure
Stejným způsobem se dá ExpressRoute Global Reach použít k připojení k jednotkám velkých instancí mezi místními a HANA, dá se použít k propojení dvou tenantů s velkými instancemi HANA nasazených pro vás ve dvou různých oblastech. Izolací jsou okruhy ExpressRoute, které klienti ve velkých instancích HANA používají pro připojení k Azure v obou oblastech. Za propojení dvou tenantů rozsáhlých instancí HANA, které jsou nasazené ve dvou různých oblastech, se neúčtují žádné další poplatky. 

> [!IMPORTANT]  
> Tok dat a tok řízení síťového provozu mezi různými klienty s velkým počtem instancí HANA se nebudou směrovat přes sítě Azure. V důsledku toho nemůžete použít funkce Azure ani síťová virtuální zařízení k vykonání omezení komunikace mezi dvěma klienty rozsáhlých instancí HANA. 

Pokud chcete získat další informace o tom, jak ExpressRoute Global Reach povolit, přečtěte si dokument [připojit virtuální síť k rozsáhlým instancím Hana](./hana-connect-vnet-express-route.md).


## <a name="internet-connectivity-of-hana-large-instance"></a>Připojení k Internetu velké instance HANA
Velká instance *Hana nemá přímé* připojení k Internetu. Toto omezení může například omezit schopnost registrovat bitovou kopii operačního systému přímo dodavateli operačního systému. Možná budete muset pracovat se serverem nástroje pro správu předplatného SUSE Linux Enterprise Server nebo Red Hat Enterprise Linux správce předplatného.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Šifrování dat mezi virtuálními počítači a rozsáhlou instancí HANA
Data přenášená mezi velkou instancí a virtuálními počítači HANA nejsou šifrovaná. Nicméně čistě pro výměnu mezi aplikací založenými na platformě a JDBC/rozhraním ODBC pro HANA, můžete povolit šifrování provozu. Další informace najdete v [této dokumentaci pomocí SAP](https://help.sap.com/viewer/102d9916bf77407ea3942fef93a47da8/1.0.11/en-US/dbd3d887bb571014bf05ca887f897b99.html).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Použít velké jednotky instancí HANA ve více oblastech

Aby bylo možné realizovat nastavení zotavení po havárii, musíte mít SHANA jednotky velkých instancí ve více oblastech Azure. I při použití Azure [Global VNet peering] nefunguje ve výchozím nastavení přenosné směrování mezi klienty ve velkých instancích v HANA ve dvou různých oblastech. Global Reach ale otevře komunikační cestu mezi jednotkami velkých instancí HANA, které jste zřídili ve dvou různých oblastech. Tento scénář použití ExpressRoute Global Reach umožňuje:

 - Replikace systému HANA bez dalších proxy a bran firewall
 - Kopírování záloh mezi jednotkami velkých instancí HANA ve dvou různých oblastech, aby se prováděly systémové kopie nebo aktualizace systému


![Virtuální síť připojená k razítkům rozsáhlých instancí Azure v různých oblastech Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

Obrázek ukazuje, jak jsou různé virtuální sítě v obou oblastech připojené ke dvěma různým okruhům ExpressRoute, které se používají pro připojení k SAP HANA v Azure (velké instance) v obou oblastech Azure (šedé řádky). Důvodem pro tyto dvě vzájemné připojení je ochrana před výpadkem směrovači msee na obou stranách. Tok komunikace mezi dvěma virtuálními sítěmi ve dvou oblastech Azure by se měl zpracovat v rámci [globálního partnerského vztahu](/archive/blogs/azureedu/how-to-setup-global-vnet-peering-in-azure) dvou virtuálních sítí ve dvou různých oblastech (modrých teček na řádku). Silná červená čára popisuje ExpressRoute Global Reach připojení, které umožňuje, aby jednotky pro velké instance klientů ve dvou různých oblastech komunikovaly. 

> [!IMPORTANT] 
> Pokud jste použili více okruhů ExpressRoute, je třeba pro zajištění správného směrování provozu použít nastavení protokolu BGP pro nedokončenou cestu a místní preference.

**Další kroky**
- Informace o [architektuře úložiště SAP Hana (velké instance)](hana-storage-architecture.md)