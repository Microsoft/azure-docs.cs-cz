---
title: Síťová architektura SAP HANA v Azure (velké instance) | Dokumenty společnosti Microsoft
description: Síťová architektura, jak nasadit SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 33684a6292d7e51c04f6bacc7c49ee5986dbec10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502397"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Architektura sítě SAP HANA (velké instance)

Architektura síťových služeb Azure je klíčovou součástí úspěšného nasazení aplikací SAP na velké instanci HANA. Nasazení SAP HANA v Azure (velké instance) mají obvykle větší prostředí SAP s několika různými řešeními SAP s různou velikostí databází, spotřebou prostředků procesoru a využitím paměti. Je pravděpodobné, že ne všechny IT systémy jsou už umístěné v Azure. Vaše prostředí SAP je často hybridní i z bodu DBMS a z hlediska aplikace SAP pomocí směsi NetWeaver a S/4HANA a SAP HANA a dalších DBMS. Azure nabízí různé služby, které vám umožní spouštět různé systémy DBMS, NetWeaver a S/4HANA v Azure. Azure vám také nabízí síťovou technologii, aby Azure vypadal jako virtuální datové centrum pro vaše místní nasazení softwaru.

Pokud vaše kompletní IT systémy nejsou hostované v Azure. Síťové funkce Azure se používají k propojení místního světa s vašimi prostředky Azure, aby Azure vypadal jako vaše virtuální datové centrum. Používaná funkce sítě Azure je: 

- Virtuální sítě Azure jsou připojené k okruhu [ExpressRoute,](https://azure.microsoft.com/services/expressroute/) který se připojuje k místním síťovým prostředkům.
- Okruh ExpressRoute, který se připojuje místně k Azure, by měl mít minimální šířku pásma [1 Gb/s nebo vyšší](https://azure.microsoft.com/pricing/details/expressroute/). Tato minimální šířka pásma umožňuje dostatečnou šířku pásma pro přenos dat mezi místními systémy a systémy, které běží na virtuálních počítačích. Umožňuje také dostatečnou šířku pásma pro připojení k systémům Azure od místních uživatelů.
- Všechny systémy SAP v Azure jsou nastaveny ve virtuálních sítích pro vzájemnou komunikaci.
- Služba Active Directory a místně hostovaná službou DNS se do Azure prostřednictvím ExpressRoute rozšiřují z místního prostředí nebo běží jako dokončené v Azure.

Pro konkrétní případ integrace velkých instancí HANA do síťové struktury datového centra Azure se používá také technologie Azure ExpressRoute.


> [!NOTE] 
> Jenom jedno předplatné Azure se dá propojit jenom s jedním tenantem v razítku velké instance HANA v určité oblasti Azure. Naopak jeden tenant razítko velké instance HANA lze propojit pouze s jedním předplatným Azure. Tento požadavek je konzistentní s ostatními fakturovatelnými objekty v Azure.

Pokud SAP HANA v Azure (velké instance) se nasadí ve více různých oblastech Azure, samostatný tenant se nasadí v razítku velké instance HANA. Můžete spustit oba pod stejné předplatné Azure tak dlouho, dokud tyto instance jsou součástí stejného prostředí SAP. 

> [!IMPORTANT] 
> Jenom metoda nasazení Azure Resource Manager je podporovaná s SAP HANA v Azure (velké instance).

 

## <a name="additional-virtual-network-information"></a>Další informace o virtuální síti

Chcete-li připojit virtuální síť k ExpressRoute, musí být vytvořena brána Azure ExpressRoute. Další informace naleznete v [tématu O branách Expressroute pro ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Brána Azure ExpressRoute se používá s ExpressRoute k infrastruktuře mimo Azure nebo na razítko Azure Large Instance. Bránu Azure ExpressRoute můžete připojit k maximálně čtyřem různým okruhům ExpressRoute, pokud tato připojení pocházejí z různých směrovačů Microsoft enterprise edge. Další informace najdete v [tématu SAP HANA (velké instance) infrastruktury a připojení v Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Maximální propustnost, které můžete dosáhnout pomocí brány ExpressRoute, je 10 Gb/s pomocí připojení ExpressRoute. Kopírování souborů mezi virtuálním počítačem, který se nachází ve virtuální síti, a místním systémem (jako datový proud jedné kopie) nedosahuje úplné propustnosti různých virtuálních zařízení brány. Chcete-li využít úplnou šířku pásma brány ExpressRoute, použijte více datových proudů. Nebo je nutné zkopírovat různé soubory v paralelních proudech jednoho souboru.


## <a name="networking-architecture-for-hana-large-instance"></a>Architektura sítě pro velkou instanci HANA
Síťovou architekturu pro velkou instanci HANA lze rozdělit do čtyř různých částí:

- Místní sítě a připojení ExpressRoute k Azure. Tato část je doménou zákazníka a je propojená s Azure přes ExpressRoute. Tento okruh Expressroute je plně placen vámi jako zákazníkem. Šířka pásma by měla být dostatečně velká pro zpracování síťového provozu mezi místními prostředky a oblastí Azure, proti které se připojujete. Viz vpravo dole na následujícím obrázku.
- Síťové služby Azure, jak jsme již dříve diskutovali, s virtuálními sítěmi, které opět potřebují přidat brány ExpressRoute. Tato část je oblast, kde je třeba najít vhodné návrhy pro vaše požadavky na aplikace, zabezpečení a požadavky na dodržování předpisů. Ať už používáte HANA Large Instance je jiný bod, který je třeba zvážit z hlediska počtu virtuálních sítí a soupoložky brány Azure, ze kterých si můžete vybrat. Viz vpravo nahoře na obrázku.
- Připojení velké instance HANA prostřednictvím technologie ExpressRoute do Azure. Tato část je nasazena a zpracována společností Microsoft. Vše, co musíte udělat, je poskytnout některé rozsahy IP adres po nasazení vašich prostředků v hana velké instance připojit ExpressRoute okruh u virtuálních sítí. Další informace najdete v [tématu SAP HANA (velké instance) infrastruktury a připojení v Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Za připojení mezi síťovým fabricem datového centra Azure a jednotkami velké instance HANA se neplatí žádný další poplatek.
- Vytváření sítí v rámci razítka hana velké instance, které je pro vás většinou transparentní.

![Virtuální síť připojená k SAP HANA v Azure (velké instance) a místně](./media/hana-overview-architecture/image1-architecture.png)

Požadavek, že vaše místní prostředky musí připojit prostřednictvím ExpressRoute do Azure se nezmění, protože používáte HANA velké instance. Požadavek mít jednu nebo více virtuálních sítí, které spouštějí virtuální počítače, které jsou hostitelem aplikační vrstvy, která se připojuje k instancíhana hostovaným v jednotkách velké instance HANA, se také nemění. 

Rozdíly oproti nasazením SAP v Azure jsou:

- Jednotky velké instance HANA vašeho klienta zákazníka jsou připojeny prostřednictvím jiného okruhu ExpressRoute do virtuálních sítí. Chcete-li oddělit podmínky načítání, místní virtuální sítě Azure ExpressRoute okruhy a okruhy mezi virtuálními sítěmi Azure a HANA velké instance nesdílejí stejné směrovače.
- Profil pracovního vytížení mezi aplikační vrstvou SAP a velkou instancí HANA má jinou povahu, s mnoha malými požadavky a shluky, jako jsou datové přenosy (sady výsledků) z SAP HANA do aplikační vrstvy.
- Architektura aplikace SAP je citlivější na latenci sítě než typické scénáře, kde se data vyměňují mezi místními a Azure.
- Brána Azure ExpressRoute má alespoň dvě připojení ExpressRoute. Jeden okruh, který je připojen z místního a jeden, který je připojen z velkých instancí HANA. To ponechává pouze prostor pro další dva další okruhy z různých MSEEs pro připojení na ExpressRoute Gateway. Toto omezení je nezávislé na použití ExpressRoute Rychlá cesta. Všechny připojené obvody sdílejí maximální šířku pásma pro příchozí data brány ExpressRoute.

S revizí 3 razítek velké instance HANA může být latence sítě mezi virtuálními virtuálními servery a jednotkami velké instance HANA vyšší než typická latence round-trip sítě virtuálního měn na virtuální ho. V závislosti na oblasti Azure mohou naměřené hodnoty překročit latenci odezvy 0,7 ms klasifikovanou jako podprůměrná v [SAP Note #1100926 – nejčastější dotazy: Výkon sítě](https://launchpad.support.sap.com/#/notes/1100926/E). V závislosti na oblasti Azure a nástroj pro měření latence síťového round-trip mezi azure vm a hana velké instance jednotky, naměřená latence může být až 2 milisekundy. Zákazníci však úspěšně nasazují produkční aplikace SAP založené na SAP na sap u velké instance SAP HANA. Ujistěte se, že důkladně otestujete vaše obchodní procesy v Azure HANA Large Instance. Nová funkce, nazvaná ExpressRoute Fast Path, je schopná výrazně snížit latenci sítě mezi velkými instancemi HANA a virtuálními počítači aplikační vrstvy v Azure (viz níže). 

S revize 4 hana velká instance razítka, latence sítě mezi virtuálními počítači Azure, které jsou nasazené v blízkosti razítko velké instance HANA, je zkušený ke splnění průměrné nebo lepší než průměrné klasifikace, jak je popsáno v [SAP Poznámka #1100926 - nejčastější dotazy: Výkon sítě,](https://launchpad.support.sap.com/#/notes/1100926/E) pokud Je nakonfigurováno Azure ExpressRoute Rychlá cesta (viz níže). Chcete-li nasadit virtuální počítače Azure v těsné blízkosti jednotek velké instance HANA revize 4, je třeba využít [skupiny umístění bezkontaktní komunikace Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location). Způsob, jakým lze skupiny umístění bezkontaktní komunikace použít k vyhledání aplikační vrstvy SAP ve stejném datovém centru Azure jako hostované jednotky hana velké instance revize 4, je popsán ve [skupinách umístění Azure Proximity Placement Pro optimální latenci sítě s aplikacemi SAP](sap-proximity-placement-scenarios.md).

Chcete-li poskytnout deterministickou latenci sítě mezi virtuálními počítači a velkou instancí HANA, je volba skladové položky brány ExpressRoute nezbytná. Na rozdíl od vzory provozu mezi místní a virtuální chod, vzor provozu mezi virtuálními počítači a HANA velké instance můžete vyvinout malé, ale vysoké shluky požadavků a datových svazků, které mají být přenášeny. Chcete-li tyto shluky dobře zpracovat, důrazně doporučujeme použít skladovou položku brány UltraPerformance. Pro třídu typu II velkých instancí HANA skladových položk je použití skladové položky brány UltraPerformance jako brány ExpressRoute povinné.

> [!IMPORTANT] 
> Vzhledem k celkovému síťovému provozu mezi aplikačními a databázovými vrstvami SAP jsou pro připojení k SAP HANA v Azure (velké instance) podporovány pouze jednotky SKU Pro virtuální sítě brány HighPerformance nebo UltraPerformance pro virtuální sítě. Pro objekty Skladové položky velké instance HANA typu II je jako brána ExpressRoute podporována pouze skladová položka brány brány UltraPerformance. Výjimky platí při použití ExpressRoute Rychlá cesta (viz níže)

### <a name="expressroute-fast-path"></a>Rychlá cesta ExpressRoute
Chcete-li snížit latenci, ExpressRoute Rychlá cesta se zavedla a vydala v květnu 2019 pro konkrétní připojení hana velkých instancí do virtuálních sítí Azure, které jsou hostitelem virtuálních počítačů aplikace SAP. Hlavní rozdíl řešení, které bylo zavedeno tak daleko, je, že toky dat mezi virtuálními počítači a velkými instancemi HANA již nejsou směrovány přes bránu ExpressRoute. Místo toho virtuální počítače přiřazené v podsítích virtuální sítě Azure přímo komunikují s vyhrazeným směrovačem enterprise edge. 

> [!IMPORTANT] 
> Funkce ExpressRoute Rychlá cesta vyžaduje, aby podsítě se spuštěnou virtuálními počítači aplikace SAP byly ve stejné virtuální síti Azure, která se připojila k velkým instancím HANA. Virtuální počítače umístěné ve virtuálních sítích Azure, které jsou partnerské společnosti s virtuální sítí Azure připojené přímo k jednotkám velké instance HANA, nemají prospěch z rychlé cesty ExpressRoute. V důsledku toho se získávají typické návrhy virtuálních sítí rozbočovače a paprsků, kde se obvody ExpressRoute připojují k centrální virtuální síti a virtuální sítě obsahující aplikační vrstvu SAP (paprsky) jsou peered, optimalizace ExpressRoute Fast Cesta nebude fungovat. V addtion ExpressRoute Rychlá cesta nepodporuje uživatelem definovaná pravidla směrování (UDR) dnes. Další informace naleznete v tématech [Brána virtuální sítě ExpressRoute a FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways). 


Další podrobnosti o konfiguraci rychlé cesty ExpressRoute načtěte v dokumentu [Připojení virtuální sítě k velkým instancím HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).    

> [!NOTE]
> Brána UltraPerformance ExpressRoute musí fungovat pro rychlou cestu ExpressRoute


## <a name="single-sap-system"></a>Jednotný systém SAP

Místní infrastruktura, která byla dříve zobrazena, je propojená přes ExpressRoute do Azure. Okruh ExpressRoute se připojuje k podnikovému hraničnímu směrovači (MSEE) společnosti Microsoft. Další informace naleznete v [technickém přehledu služby ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Po navázání trasy se připojí do páteřní sítě Azure.

> [!NOTE] 
> Chcete-li spustit prostředí SAP v Azure, připojte se k podnikovému hraničnímu směrovači, který je nejblíže oblasti Azure v krajině SAP. Razítka velké instance HANA jsou propojeny prostřednictvím vyhrazených zařízení směrovače enterprise edge, aby se minimalizovala latence sítě mezi virtuálními počítači v razítkách Azure IaaS a HANA Large Instance.

Brána ExpressRoute pro virtuální počítače, které hostují instance aplikací SAP, je připojena k jednomu okruhu ExpressRoute, který se připojuje k místnímu. Stejná virtuální síť je připojena k samostatnému podnikovému hraničnímu směrovači vyhrazenému pro připojení ke razítkům velké instance. Pomocí ExpressRoute Rychlá cesta, tok dat z HANA velké instance do aplikační vrstvy SAP virtuálnípočítače nejsou směrovány přes bránu ExpressRoute už a tím snížit latenci sítě round-trip.

Tento systém je přímočarým příkladem jednoho systému SAP. Aplikační vrstva SAP je hostovaná v Azure. Databáze SAP HANA běží na SAP HANA v Azure (velké instance). Předpokládá se, že šířka pásma brány ExpressRoute 2 Gb/s nebo 10 Gb/s nepředstavuje kritické místo.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Více systémů SAP nebo velkých systémů SAP

Pokud více systémů SAP nebo velké systémy SAP jsou nasazeny pro připojení k SAP HANA v Azure (velké instance), propustnost brány ExpressRoute může být kritickým bodem. Nebo chcete izolovat produkční a neprodukční systémy v různých virtuálních sítích Azure. V takovém případě rozdělte vrstvy aplikace do více virtuálních sítí. Můžete také vytvořit speciální virtuální síť, která se připojuje k velké instanci HANA v případech, jako jsou:

- Provádění záloh přímo z instancí HANA v hana velké instance na virtuální počítač v Azure, který hostuje sdílené složky nfs.
- Kopírování velkých záloh nebo jiných souborů z jednotek velké instance HANA na místo na disku spravované v Azure.

Použijte samostatnou virtuální síť k hostování virtuálních počítačů, které spravují úložiště pro hromadný přenos dat mezi velkými instancemi HANA a Azure. Toto uspořádání zabraňuje účinkům velkého přenosu souborů nebo dat z velké instance HANA do Azure na bráně ExpressRoute, která obsluhuje virtuální počítače, které spouštějí aplikační vrstvu SAP. 

Pro škálovatelnější síťovou architekturu:

- Využijte více virtuálních sítí pro jednu větší aplikační vrstvu SAP.
- Nasazení jedné samostatné virtuální sítě pro každý nasazený systém SAP ve srovnání s kombinováním těchto systémů SAP v samostatných podsítích v rámci stejné virtuální sítě.

  Škálovatelná síťová architektura pro SAP HANA v Azure (velké instance):

![Nasazení aplikační vrstvy SAP přes více virtuálních sítí](./media/hana-overview-architecture/image4-networking-architecture.png)

V závislosti na pravidlech a omezeních, chcete použít mezi různými virtuálními sítěmi hostujícími virtuální počítače různých systémů SAP, měli byste tyto virtuální sítě peerovat. Další informace o partnerskésíti virtuální sítě najdete v [tématu Partnerský vztah virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


## <a name="routing-in-azure"></a>Směrování v Azure

Ve výchozím nastavení nasazení jsou pro SAP HANA v Azure (velké instance důležité tři aspekty směrování v síti):

* SAP HANA v Azure (velké instance) je přístupný jenom prostřednictvím virtuálních počítačů Azure a vyhrazeného připojení ExpressRoute, nikoli přímo z místního prostředí. Přímý přístup z místního prostředí k jednotkám velké instance HANA, jak vám společnost Microsoft doručuje, není možný okamžitě. Omezení přenositého směrování jsou způsobena aktuální architekturou sítě Azure používanou pro velkou instanci SAP HANA. Některé klienty pro správu a všechny aplikace, které potřebují přímý přístup, jako je například SPRÁVCE řešení SAP spuštěné místně, se nemohou připojit k databázi SAP HANA. Výjimky naleznete v části Přímé směrování do velkých instancí HANA.

* Pokud máte jednotky velké instance HANA nasazené ve dvou různých oblastech Azure pro zotavení po havárii, stejná přechodná omezení směrování použitá v minulosti. Jinými slovy IP adresy jednotky velké instance HANA v jedné oblasti (například USA – západ) nebyly směrovány do jednotky velké instance HANA nasazené v jiné oblasti (například US East). Toto omezení bylo nezávislé na použití partnerského vztahu sítě Azure napříč oblastmi nebo na křížovém připojení okruhů ExpressRoute, které připojují jednotky velké instance HANA k virtuálním sítím. Grafické znázornění naleznete na obrázku v části "Použití jednotek velké instance HANA ve více oblastech". Toto omezení, které bylo součástí nasazené architektury, zakázalo okamžité použití hana systémové replikace jako funkce zotavení po havárii. Nejnovější změny najdete v části Použití jednotek velké instance HANA ve více oblastech. 

* Jednotky SAP HANA v Azure (velké instance) mají přiřazenou IP adresu z rozsahu adres fondu IP serveru, který jste odeslali při žádosti o nasazení velké instance HANA. Další informace najdete v [tématu SAP HANA (velké instance) infrastruktury a připojení v Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Tato IP adresa je přístupná prostřednictvím předplatných azure a okruhu, který spojuje virtuální sítě Azure s velkými instancemi HANA. Ip adresa přiřazená mimo rozsah adres fondu IP serverů je přiřazena přímo hardwarové jednotce. Již *není* přiřazenprostřednictvím nat, jako tomu bylo v případě prvních nasazení tohoto řešení. 

### <a name="direct-routing-to-hana-large-instances"></a>Přímé směrování na velké instance HANA

Ve výchozím nastavení nefunguje přenosité směrování v těchto scénářích:

* Mezi jednotkami velké instance HANA a místním nasazením.

* Mezi směrovánívelké instance HANA, které jsou nasazeny ve dvou různých oblastech.

V těchto scénářích lze povolit přenositelné směrování třemi způsoby:

- Reverzní proxy pro směrování dat, do a z. Například F5 BIG-IP, NGINX s Traffic Managerem nasazenýve virtuální síti Azure, která se připojuje k velkým instancím HANA a k místním jako řešení virtuálního směrování brány firewall a provozu.
- Použití [pravidel IPTables](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) ve virtuálním počítači s Operačním systémem Linux k povolení směrování mezi místními umístěními a jednotkami velké instance HANA nebo mezi jednotkami velké instance HANA v různých oblastech. Virtuální počítač se systémem IPTables musí být nasazen ve virtuální síti Azure, která se připojuje k velké instance HANA a k místnímu. Virtuální ho je možné odpovídajícím způsobem velikosti tak, aby propustnost sítě virtuálního připojení je dostatečná pro očekávaný síťový provoz. Podrobnosti o šířce pásma virtuálních počítačů najdete v článku [Velikosti virtuálních počítačů s Linuxem v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json).
- [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/) by bylo jiné řešení umožňující přímý provoz mezi místními a HANA velké instance jednotek. 

Veškerý provoz těchto řešení by byl směrován přes virtuální síť Azure a jako takový by mohl být navíc omezen měkkými zařízeními používanými nebo skupinami zabezpečení sítě Azure, takže se určité IP adresy nebo IP adresy pohybují od místní může být blokován nebo explicitně povolen přístup k velkým instancím HANA. 

> [!NOTE]  
> Uvědomte si, že implementace a podpora vlastních řešení zahrnujících síťová zařízení jiných výrobců nebo iptables není poskytována společností Microsoft. Podporu musí poskytovat dodavatel použité součásti nebo integrátor. 

#### <a name="express-route-global-reach"></a>Globální dosah expresní trasy
Společnost Microsoft představila novou funkci nazvanou [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach). Globální dosah lze použít pro velké instance HANA ve dvou scénářích:

- Povolit přímý přístup z místního prostředí k jednotkám velké instance HANA nasazeným v různých oblastech
- Povolit přímou komunikaci mezi jednotkami velké instance HANA nasazenými v různých oblastech


##### <a name="direct-access-from-on-premises"></a>Přímý přístup z místního prostředí
V oblastech Azure, kde je globální dosah nabízen, můžete požádat o povolení funkce Global Reach pro okruh ExpressRoute, která propojuje vaši místní síť s virtuální sítí Azure, která se připojuje i k jednotkám velké instance HANA. Existují některé náklady důsledky pro místní straně okruhu ExpressRoute. Ceny najdete v cenách [doplňku Global Reach Add-On](https://azure.microsoft.com/pricing/details/expressroute/). Neexistují žádné další náklady pro vás související s okruhem, který spojuje jednotky velké instance HANA s Azure. 

> [!IMPORTANT]  
> V případě použití globálního dosahu pro povolení přímého přístupu mezi jednotkami velké instance HANA a místními prostředky není síťový datový a řídicí tok **směrován přes virtuální sítě Azure**, ale přímo mezi směrovači podnikové výměny Microsoftu. V důsledku toho se nedotýkají žádných pravidel skupiny zabezpečení sítě nebo asg nebo jakéhokoli typu brány firewall, síťového virtuálního zařízení nebo serveru proxy, které jste nasadili ve virtuální síti Azure. **Pokud používáte ExpressRoute Global Reach k povolení přímého přístupu z místního na HANA Omezení velkých instancí jednotky a oprávnění pro přístup k jednotkám velkých instancí HANA je třeba definovat v bráně firewall na místní straně** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Připojení velkých instancí HANA v různých oblastech Azure
Stejným způsobem jako ExpressRoute Global Reach lze použít pro připojení místně k jednotkám velké instance HANA, lze jej použít k připojení dvou tenantů velké instance HANA, které jsou nasazeny pro vás ve dvou různých oblastech. Izolace je okruhy ExpressRoute, které vaši tenantové velké instance HANA používají pro připojení k Azure v obou oblastech. Neexistují žádné další poplatky za připojení dvou tenantů velké instance HANA, které jsou nasazeny ve dvou různých oblastech. 

> [!IMPORTANT]  
> Tok dat a tok řízení síťového provozu mezi různými tenanty velké instance HANA nebudou směrovány prostřednictvím sítí Azure. V důsledku toho nelze použít funkce Azure nebo neva vynutit omezení komunikace mezi dvěma tenanty velké instance HANA. 

Další podrobnosti o povolení globálního dosahu ExpressRoute načtěte v dokumentu [Připojení virtuální sítě k velkým instancím HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).


## <a name="internet-connectivity-of-hana-large-instance"></a>Připojení k Internetu velké instance HANA
Hana Large Instance *nemá* přímé připojení k internetu. Toto omezení může například omezit možnost registrace bitové kopie operačního systému přímo u dodavatele operačního systému. Možná budete muset pracovat s místním serverem SUSE Linux Enterprise Server Subscription Management Tool nebo S Red Hat Enterprise Linux Subscription Manager.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Šifrování dat mezi virtuálními aplikacemi a rozsáhlou instancí HANA
Data přenášená mezi rozsáhlou instancí HANA a virtuálními aplikacemi nejsou šifrovaná. Čistě pro výměnu mezi hana DBMS straně a JDBC/ODBC-založené aplikace, můžete však povolit šifrování provozu. Další informace naleznete v [této dokumentaci od společnosti SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Použití jednotek velké instance HANA ve více oblastech

Chcete-li realizovat nastavení zotavení po havárii, musíte mít jednotky shana velké instance ve více oblastech Azure. I s použitím Azure [Global Vnet Peering] přenosité směrování ve výchozím nastavení nefunguje mezi tenanty velké instance HANA ve dvou různých oblastech. Globální dosah však otevírá komunikační cestu mezi jednotkami velké instance HANA, které jste zřídili ve dvou různých oblastech. Tento scénář použití globálního dosahu ExpressRoute umožňuje:

 - Replikace systému HANA bez dalších proxy serverů nebo bran firewall
 - Kopírování záloh mezi jednotkami velké instance HANA ve dvou různých oblastech za účelem provedení systémových kopií nebo aktualizací systému


![Virtuální síť připojená k razítkům azure velké instance v různých oblastech Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

Obrázek ukazuje, jak jsou různé virtuální sítě v obou oblastech připojeny ke dvěma různým okruhům ExpressRoute, které se používají k připojení k SAP HANA v Azure (velké instance) v obou oblastech Azure (šedé čáry). Důvodem pro toto dvě křížová spojení je ochrana před výpadkem MSEEs na obou stranách. Tok komunikace mezi dvěma virtuálními sítěmi ve dvou oblastech Azure se má zpracovávat přes [globální partnerský vztah](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/) dvou virtuálních sítí ve dvou různých oblastech (modrá tečkovaná čára). Silná červená čára popisuje připojení ExpressRoute Global Reach, které umožňuje jednotkám velké instance HANA vašich klientů ve dvou různých oblastech vzájemně komunikovat. 

> [!IMPORTANT] 
> Pokud jste použili více okruhů ExpressRoute, měla by být k zajištění správného směrování provozu použita nastavení Protokolu BGP cesty AS a místní předvolby.

**Další kroky**
- Odkazovat na [architekturu úložiště SAP HANA (Velké instance)](hana-storage-architecture.md)
