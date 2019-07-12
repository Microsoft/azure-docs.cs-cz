---
title: Síťová architektura SAP Hana v Azure (velké instance) | Dokumentace Microsoftu
description: Architektura sítě nasazení SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2359f1ea1d55b8ce153295cc71fdf78040e8e316
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707416"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Síťová architektura SAP HANA (velké instance)

Architektura služby Azure network je klíčovou komponentou úspěšné nasazení aplikace SAP v HANA velkou instanci. SAP HANA v Azure (velké instance) nasazení obvykle mají větší prostředí SAP s několika různých řešení SAP s různou velikostí databází, využití prostředků procesoru a využití paměti v aplikaci. Je pravděpodobné, že ne všechny systémy IT se již nacházejí v Azure. Prostředí SAP, často je hybridní i z bodu DBMS a SAP aplikace pohledu využíváte NetWeaver a S/4HANA, SAP HANA a dalších DBMS. Azure nabízí různé služby, které umožňují spouštění různých systémů DBMS, NetWeaver a S/4HANA v Azure. Azure také nabízí síťové technologie, aby oblast Azure, jako jsou virtuální datové centrum pro vaše nasazení místní software

Pokud vaše kompletní IT systémy jsou hostované v Azure. Azure síťové funkce slouží k připojení místní světa s vašimi prostředky Azure aby vypadat virtuálního datového centra vaší gigantické Azure. Azure síťové funkce, používá se: 

- Virtuální sítě Azure jsou připojené k [ExpressRoute](https://azure.microsoft.com/services/expressroute/) okruhu, který se připojuje k výpočetním prostředkům vaší místní síti.
- Okruh ExpressRoute, který se připojuje k Azure v místním musí mít minimální šířku pásma [1 GB/s nebo vyšší](https://azure.microsoft.com/pricing/details/expressroute/). Tato minimální šířku pásma umožňuje vyhrazena adekvátní šířka pásma pro přenos dat mezi místními systémy a systémy, které běží na virtuálních počítačích. Umožňuje také vyhrazena adekvátní šířka pásma pro připojení k systémům Azure z místních uživatelů.
- Všechny systémy SAP v Azure jsou nastavení ve virtuálních sítích komunikovat mezi sebou.
- Služba Active Directory a DNS hostované místně se rozšíří do Azure prostřednictvím ExpressRoute v místním nebo spouštěné dokončeno v Azure.

Pro zvláštní případ integrace velkých instancích HANA v prostředcích infrastruktury sítě centra Azure data se také používá technologie Azure ExpressRoute


> [!NOTE] 
> Pouze jedno předplatné Azure může být propojený jenom k jednomu tenantovi v razítku velká Instance HANA v konkrétní oblasti Azure. Naopak jednoho tenanta razítko velká Instance HANA může být propojený jenom jedno předplatné Azure. Tento požadavek je konzistentní s jinými fakturovatelné objekty v Azure.

Pokud se SAP HANA v Azure (velké instance) se nasazuje v několika různých oblastech Azure, samostatného tenanta se nasadí v razítku velká Instance HANA. Můžete spustit i v rámci stejného předplatného Azure, pokud jsou tyto instance součástí stejné prostředí SAP. 

> [!IMPORTANT] 
> Se SAP HANA v Azure (velké instance) se podporuje jenom způsob nasazení Azure Resource Manageru.

 

## <a name="additional-virtual-network-information"></a>Informace o další virtuální sítě

Pokud chcete připojení virtuální sítě pro ExpressRoute, musíte vytvořit bránu Azure ExpressRoute. Další informace najdete v tématu [informace o Expressroute Gateway pro ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Bránu Azure ExpressRoute se používá s ExpressRoute k infrastruktuře mimo Azure nebo razítku velkých instancí Azure. Brána Azure ExpressRoute maximálně čtyři různé okruhy ExpressRoute můžete připojit tak dlouho, dokud tato připojení pocházejí z různých hraničním směrovačům Microsoft enterprise. Další informace najdete v tématu [infrastrukturu SAP HANA (velké instance) a možnosti připojení v Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Maximální propustnost, které můžete dosáhnout s bránou ExpressRoute je 10 GB/s s použitím připojení typu ExpressRoute. Kopírování souborů mezi virtuálním Počítačem, který se nachází ve virtuální síti a systém v místním (jako datový proud jedna kopie) nelze dosáhnout úplné propustnosti různé skladové položky brány. Využívat kompletní šířky pásma ExpressRoute brány, pomocí různých datových proudů. Nebo je nutné zkopírovat různé soubory v paralelní datové proudy jednoho souboru.


## <a name="networking-architecture-for-hana-large-instance"></a>Architektura sítě pro velké Instance HANA
Síťové architektury pro velké Instance HANA lze rozdělit do čtyř různých částí:

- Místní sítě a připojení ExpressRoute k Azure. Tato část je doména zákazníka a je připojená k Azure přes ExpressRoute. Tento okruh Expressroute je plně placené vy jako zákazník. Šířka pásma by měl být dostatečně velký, aby síťový provoz mezi vaší místní prostředky a oblasti Azure, ke kterému se připojujete na zpracování. Podívejte se vpravo dole na následujícím obrázku.
- Azure síťové služby, jak jsme uvedli, s virtuálními sítěmi, které je třeba znovu přidat brány ExpressRoute. Tato část je oblast, kde potřebujete najít vhodné návrhů pro požadavky na aplikace, zabezpečení a požadavky na dodržování předpisů. Ať už používáte velká Instance HANA je jiné vzít v úvahu z hlediska počtu virtuálních sítí a Azure SKU brány lze vybírat. Najdete v pravém horním rohu na obrázku.
- Připojení velká Instance HANA přes ExpressRoute technologie do Azure. Tato část nasazení a postará Microsoft. Všechno, co je potřeba je zadat několik rozsahů IP adres po nasazení vaše prostředky v velká Instance HANA okruh ExpressRoute připojit k virtuálním sítím. Další informace najdete v tématu [infrastrukturu SAP HANA (velké instance) a možnosti připojení v Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Se neúčtují žádné další poplatky za vás jako odběratel pro připojení mezi prostředky infrastruktury sítě pro Azure data center a velké Instance HANA jednotky.
- Sítě v rámci razítka velká Instance HANA, což je většinou transparentní za vás.

![Virtuální sítě připojené k SAP HANA v Azure (velké instance) a místní](./media/hana-overview-architecture/image1-architecture.png)

Požadavek na vaše místní prostředky nutné se připojit přes ExpressRoute do Azure není změnit, protože používáte velká Instance HANA. Požadavek na jeden nebo více virtuálními sítěmi, na kterých běží virtuální počítače, které jsou hostiteli aplikační vrstvu, která se připojuje k HANA instance hostované v jednotkách velká Instance HANA, také nemění. 

Rozdíly pro nasazení SAP v Azure jsou následující:

- Velká Instance HANA jednotek vašeho tenanta zákazníka jsou připojené prostřednictvím jiného okruhu ExpressRoute do virtuální sítě. K oddělení podmínek zátěže, místní virtuální sítě Azure okruhy ExpressRoute a okruhy mezi virtuálními sítěmi Azure a velké instance HANA Nesdílejte stejné směrovače.
- Profil zatížení mezi aplikační vrstvě SAP a velké Instance HANA je jiný druh, s mnoha malých požadavků a nárůstem třeba přenos dat (sad výsledků) ze SAP HANA na aplikační vrstvu.
- Architektura aplikací SAP je citlivější na latenci sítě než typické scénáře, kde data se přemisťují mezi místními a Azure.
- Brána Azure ExpressRoute má aspoň dvě připojení ExpressRoute. Jeden okruh, který se připojuje z místního a ten, který se připojuje z velkých instancích HANA. Kvůli tomu pouze místo pro jiný dvou dalších okruhů z různých Msee připojení na bránu ExpressRoute. Toto omezení je nezávisle na využití ExpressRoute rychlé cesty. Všechny propojené okruhy sdílet maximální šířka pásma pro příchozí data bránu ExpressRoute.

Latenci sítě mezi virtuálními počítači a velké Instance HANA jednotky může být vyšší než typické latence přenosu sítě virtuálních počítačů VM. Závisí na oblasti Azure, můžete hodnoty měří překročit round-trip latence 0,7 ms jsou klasifikovány jako podprůměrná v [SAP Poznámka #1100926 – nejčastější dotazy: Výkon sítě](https://launchpad.support.sap.com/#/notes/1100926/E). Závisí na oblasti Azure a nástroje k přenosu latence sítě mezi virtuálním počítači Azure a velké Instance HANA jednotka měření, naměřenou latence může být až a kolem 2 milisekund. Nicméně zákazníci nasadit na základě SAP HANA produkční aplikace SAP úspěšně na velké Instance SAP HANA. Zajistěte, aby že důkladně otestovat vašich obchodních procesů ve velké Instance Azure HANA. Novou funkci, s názvem ExpressRoute rychlé cesty, se může podstatně snížit latenci sítě mezi velkých instancích HANA a aplikační vrstvu virtuálních počítačů v Azure (viz níže). 

K poskytování deterministické sítích s latencí mezi virtuálními počítači a velké Instance HANA, volba bránu ExpressRoute je základní SKU. Na rozdíl od vzorů provozu mezi místními a virtuálními počítači můžete vyvíjet vzor provoz mezi virtuálními počítači a velké Instance HANA malou, avšak vysokou nárůstům požadavků a datové svazky přenášet. Pro zpracování takových nárůstem dobře, důrazně doporučujeme používat SKU brány UltraPerformance. Pro třídu typu II SKU velké Instance HANA je povinné použití SKU brány UltraPerformance jako ExpressRotue brány.

> [!IMPORTANT] 
> Zadaný celkový provoz sítě mezi aplikací SAP a databázi vrstvy, pouze HighPerformance nebo SKU brány UltraPerformance pro virtuální sítě jsou podporovány pro připojení k SAP HANA v Azure (velké instance). Pro HANA velké Instance SKU typu II je podporován pouze SKU brány UltraPerformance jako bránu ExpressRoute. To neplatí při použití ExpressRoute rychlé cesty (viz níže)

### <a name="expressroute-fast-path"></a>Cesta k ExpressRoute Fast
Snížit latenci, ExpressRoute Rychlá cesta máte zavedená a součástí. května 2019 pro konkrétní připojení k virtuálním sítím Azure velkých instancích HANA, které jsou hostiteli virtuálních počítačů aplikace SAP. Hlavní rozdíl zatím zavádět řešení je, že toky dat mezi virtuálními počítači a velké instance HANA nejsou už směrována přes bránu ExpressRoute. Místo toho virtuální počítače přiřazené do podsítě virtuální sítě Azure komunikují přímo s hraniční směrovač podniku vyhrazené. 

> [!IMPORTANT] 
> Funkce Rychlá cesta ExpressRoute vyžaduje podsítí provádějících virtuálních počítačů aplikace SAP ve stejné virtuální síti Azure, která je teď připojený k velké instance HANA. Virtuální počítače ve virtuálních sítích Azure, které jsou v partnerském vztahu s virtuální sítí Azure, připojené přímo k velká Instance HANA jednotek nejsou analýzách ExpressRoute rychlé cesty. V důsledku typické hvězdicové virtuální sítě návrhy, kde okruhy ExpressRoute se připojuje proti centrální virtuální síti a jsou získávání partnerské virtuální sítě obsahující aplikační vrstvě SAP (centrálním uzlem), optimalizace tak rychle ExpressRoute Cesta nebude fungovat. Kromě služby ExpressRoute rychlé cesty nepodporuje pravidla směrování definované uživatelem (UDR) ještě dnes. Další informace najdete v tématu [síťovou ExpressRoute s virtuální bránou a FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways). 


Další informace o tom, jak nakonfigurovat ExpressRoute rychlé cesty, najdete dokumentu [připojení virtuální sítě pro velké instance HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).    

> [!NOTE]
> Bránu UltraPerformance ExpressRoute je nezbytný, aby pracovní ExpressRoute rychlé cesty


## <a name="single-sap-system"></a>Jeden systému SAP

V místní infrastruktuře bylo dříve uvedeno je připojené prostřednictvím ExpressRoute do Azure. Okruh ExpressRoute se připojuje do hraničního směrovače Microsoftu enterprise (MSEE). Další informace najdete v tématu [technický přehled ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Po navázání trasy, připojí se do páteřní síť Azure.

> [!NOTE] 
> Pokud chcete spustit prostředí SAP v Azure, připojte k hraniční směrovač podniku nejbližší oblasti Azure v prostředí SAP. Velká Instance HANA razítka jsou propojené prostřednictvím vyhrazené podnikové hraniční směrovače zařízení kvůli minimalizaci latence sítě mezi virtuálními počítači v Azure IaaS a velké Instance HANA razítka.

ExpressRoute gateway pro virtuální počítače, které hostují instance aplikace SAP je připojen na jeden okruh ExpressRoute, který se připojuje k místním. Stejné virtuální síti je připojený k hraniční směrovač vyhrazený pro připojení k velké Instance razítka samostatné podniku. Pomocí ExpressRoute rychlé cesty toku dat z velkých instancích HANA na aplikační vrstvě SAP virtuální počítače už nejsou směrovány přes bránu ExpressRoute a s ním snížení latence doby odezvy v síti.

Tento systém je jednoduchý příklad jednoho systému SAP. Aplikační vrstvě SAP je hostované v Azure. Databáze SAP HANA běží na systému SAP HANA v Azure (velké instance). Předpokladem je, že kritický bod nepředstavuje šířku pásma ExpressRoute gateway propustnost 2 GB/s nebo 10 GB/s.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Několik systémů SAP vyhrazené nebo rozsáhlých systémů SAP

Pokud pro připojení k SAP HANA v Azure (velké instance) jsou nasazeny více systémů SAP vyhrazené nebo rozsáhlých systémů SAP, propustnost brány ExpressRoute může být kritickým bodem. Nebo chcete izolovat produkční a neprodukční systémy v různých virtuálních sítích Azure. V takovém případě rozdělení vrstvy aplikace na více virtuálních sítí. Můžete také vytvořit zvláštní virtuální síť, která se připojuje k velká Instance HANA pro případy, například:

- Provádění zálohování přímo z instance HANA ve velké Instance HANA VM v Azure, který je hostitelem sdílené složky NFS.
- Kopírování velkých zálohy nebo jiné soubory z jednotky velká Instance HANA do místa na disku spravované v Azure.

Použijte samostatné virtuální sítě pro hostitele virtuálních počítačů, které Správa služby storage pro hromadné přenos dat mezi velkých instancích HANA a Azure. Toto uspořádání se vyhnete účinky velkých souborů nebo přenos dat z velká Instance HANA do Azure na bránu ExpressRoute, který poskytuje virtuální počítače, na kterých běží SAP aplikační vrstvu. 

Pro větší škálovatelnost síťové architektury:

- Využívejte více virtuálních sítí pro jeden, větší SAP aplikační vrstvu.
- Nasazení jedné samostatné virtuální sítě pro každý systém SAP nasazení, ve srovnání se sloučením tyto systémy SAP do samostatných podsítí v rámci stejné virtuální síti.

  Větší škálovatelnost sítě architektura pro SAP HANA v Azure (velké instance):

![Nasazení aplikační vrstvě SAP přes více virtuálních sítí](./media/hana-overview-architecture/image4-networking-architecture.png)

Závisí na pravidla a omezení, které chcete použít mezi různých virtuálních sítích hostování virtuálních počítačů různých systémů SAP, by měl vytvořit partnerský vztah těchto virtuálních sítí. Další informace o partnerských vztahů virtuálních sítí najdete v tématu [partnerský vztah virtuálních sítí](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


## <a name="routing-in-azure"></a>Směrování v Azure

Ve výchozím nastavení nasazení jsou důležité pro SAP HANA v Azure (velké instance) tři směrování důležité informace o síti:

* SAP HANA v Azure (velké instance) můžete přistupovat pouze prostřednictvím virtuálních počítačů Azure a vyhrazené připojení ExpressRoute, ne přímo z místní. Přímý přístup z místní jednotky velká Instance HANA dodaných společností Microsoft, není možné okamžitě. Přenositelné směrování omezení jsou způsobeny aktuální architektury sítě Azure používané pro velké Instance SAP HANA. Někteří klienti správy a všechny aplikace, které potřebují přímý přístup, jako je SAP správci řešení spuštěné v místním nemůže připojit k databázi SAP HANA. Výjimky najdete v části "Přímé směrování k velké instance HANA".

* Pokud máte velké Instance HANA jednotky nasazené ve dvou různých oblastech Azure pro zotavení po havárii, přechodné směrování omezení použít v minulosti. Jinými slovy IP adresy velká Instance HANA jednotka v jedné oblasti (například USA – západ) nebyly směrovat na jednotku velká Instance HANA nasadit v jiné oblasti (například USA – východ). Toto omezení byl nezávisle na použití síť Azure, partnerský vztah napříč oblastmi nebo křížové připojení okruhy ExpressRoute, které se připojují velká Instance HANA jednotek do virtuálních sítí. Grafické znázornění viz obrázek v části "použití velká Instance HANA ve více oblastech. jednotek" Toto omezení, které byly dodány s nasazenou architekturou, zakázáno okamžité použijte HANA System Replication funkce zotavení po havárii. Nedávné změny vyhledejte v části "Použití velká Instance HANA jednotky v několika oblastech". 

* SAP HANA v Azure (velké instance) jednotky mají přiřazenou IP adresu z rozsahu adres fondu IP serveru, odeslání požadavku nasazení velká Instance HANA. Další informace najdete v tématu [infrastrukturu SAP HANA (velké instance) a možnosti připojení v Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Tato IP adresa je přístupné prostřednictvím okruhu, který se připojuje virtuální sítě Azure pro velké instance HANA a předplatných Azure. Přidělit IP adresu z celkového počtu, rozsah adres fondu IP serveru přímo přiřazená jednotky hardwaru. Má *není* přiřazeném prostřednictvím překladu adres už nepotřebujeme, stejně jako v případě v prvním nasazení tohoto řešení. 

### <a name="direct-routing-to-hana-large-instances"></a>Přímé směrování pro velké instance HANA
Ve výchozím nastavení přenositelným směrováním mezi velká Instance HANA jednotky a místní nebo velká Instance HANA směrování, které jsou nasazené ve dvou různých oblastech nefunguje. Existuje několik možností, které chcete povolit takový přenositelným směrováním.

- Reverzních proxy serverů pro směrování dat do a z. Například F5 BIG-IP nebo NGINX s Traffic Managerem nasazené ve virtuální síti Azure, která se připojuje velké instance HANA a místní jako řešení směrování virtuální brána firewall/přenosů.
- Pomocí [pravidla iptables tak](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) v virtuálního počítače s Linuxem umožňující směrování mezi místními umístěními a velké Instance HANA jednotky, nebo mezi jednotkami velká Instance HANA v různých oblastech. Virtuální počítač provozovaný IPTables potřebuje k nasazení ve virtuální síť Azure, která se připojuje k velké instance HANA a na místní. Virtuální počítač musí být velikost nastavené odpovídajícím způsobem, tak, propustnost sítě pro virtuální počítač je dostatečné pro očekávané síťový provoz. Pro podrobnosti o virtuálním počítači šířka pásma sítě, zkontrolujte článek [velikosti Linuxových virtuálních počítačů v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json).
- [Brány Firewall Azure](https://azure.microsoft.com/services/azure-firewall/) by jiné řešení umožňuje směrovat přenos dat mezi místní a HANA velkých jednotek instance. 

Veškerý provoz z těchto řešení by se směroval do služby Azure virtual network a v důsledku může být kromě s omezeným přístupem softwarové zařízení používaná nebo Azure skupin zabezpečení sítě, proto, že určité IP adresy nebo rozsahy IP adres z provozu místní může blokované nebo explicitně povolen přístup k velké instance HANA. 

> [!NOTE]  
> Mějte na paměti, že implementaci a podporu pro vlastní řešení zahrnující třetích stran síťových zařízení nebo IPTables není k dispozici společností Microsoft. Podpora musí poskytnout dodavatel komponentu používanou nebo integrátor. 

#### <a name="express-route-global-reach"></a>Express Route globálním dosahem
Microsoft představil novou funkci s názvem [ExpressRoute globální dosah](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach). Globálním dosahem je možné pro velké instance HANA ve dvou scénářích:

- Povolit přímý přístup z místní jednotky vaší velká Instance HANA nasadit v různých oblastech
- Povolit přímou komunikaci mezi vaší jednotky velká Instance HANA nasadit v různých oblastech


##### <a name="direct-access-from-on-premise"></a>Přímý přístup z místní
V oblasti Azure, kde se nabízí globální dosah můžete požádat o povolení funkce globální dosah pro váš okruh ExpressRoute, který se připojuje vaše místní sítě pro virtuální síť Azure, která se připojuje k vašich velká Instance HANA jednotek. Existují některé důsledky náklady na straně místní váš okruh ExpressRoute. Ceny najdete ceny za [globální oslovit doplněk](https://azure.microsoft.com/pricing/details/expressroute/). Nejsou spojené žádné další náklady za vás související s okruhu, který se připojuje jednotek velká Instance HANA do Azure. 

> [!IMPORTANT]  
> V případě povolení přímého přístupu mezi velká Instance HANA jednotky a místní prostředky pomocí globální dosah, síťových tok dat a ovládací prvek je **nejsou směrovány prostřednictvím služby Azure virtual networks**, ale přímo mezi Microsoft organizace exchange směrovače. V důsledku nejsou získávání dotčená všechna pravidla NSG a ASG nebo libovolný typ brány firewall, síťové virtuální zařízení nebo proxy, které jste nasadili ve službě Azure virtual network. **Pokud používáte ExpressRoute globální dosah umožňující přímý přístup z místního k HANA velké instance jednotky omezení a oprávnění pro přístup k jednotky velké Instance HANA, musíte je definovat v brány firewall na straně místní** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Připojování velkých instancích HANA v různých oblastech Azure
Stejným způsobem jako globální dosah ExpressRoute můžete použít pro připojení místní jednotky velká Instance HANA ho lze použít pro připojení kabelu, které klienti velká Instance HANA, které jsou pro vás nasazeny ve dvou různých oblastech. Izolace je okruhy ExpressRoute, které klientům velká Instance HANA používají pro připojení k Azure v obou oblastech. Nejsou žádné další poplatky za připojení dva tenanty velká Instance HANA, které jsou nasazené ve dvou různých oblastech. 

> [!IMPORTANT]  
> Tok dat a řízení toku síťového provozu mezi různé HANA velké instance tenantů nebude ho směrovat pomocí sítě azure. V důsledku nelze použít funkce Azure nebo síťová virtuální zařízení k vynucení omezení komunikace mezi dvěma klienty velkých instancích HANA. 

Podrobné informace o tom, jak ExpressRoute globální dosah povoleno čtení dokumentu [připojení virtuální sítě pro velké instance HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).


## <a name="internet-connectivity-of-hana-large-instance"></a>Připojení k Internetu velké instance HANA
Velká Instance HANA neodpovídá *není* mít přímé připojení k Internetu. Jako příklad toto omezení může omezit možnost zaregistrovat image operačního systému přímo s operačním systémem. Můžete potřebovat pro práci s vaším místním operačním systémem SUSE Linux Enterprise Server předplatné Management Tool serveru nebo správce předplatného Red Hat Enterprise Linux.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Šifrování dat mezi virtuálními počítači a velké Instance HANA
Data přenášená mezi velká Instance HANA a virtuální počítače nejsou šifrována. Výhradně pro server exchange mezi HANA DBMS na straně a aplikace založené na JDBC/ODBC, ale můžete povolit šifrování přenosů. Další informace najdete v tématu [této dokumentace SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Použití jednotek velká Instance HANA ve více oblastech

Jak začít využívat ups sady pro zotavení po havárii, musíte pro velké Instance SHANA jednotek v několika oblastech Azure. Dokonce s používáním Azure [globální Vnet Peering], přenositelným směrováním ve výchozím nastavení nefunguje mezi tenanty velká Instance HANA ve dvou různých oblastech. Ale globální dosah otevře komunikační trasa mezi jednotkami velká Instance HANA, které máte zřízené ve dvou různých oblastech. Tento scénář využití globální dosah ExpressRoute umožňuje:

 - Systémové replikace HANA bez jakékoli další proxy nebo brány firewall
 - Kopírování záloh mezi jednotkami velká Instance HANA ve dvou různých oblastech provádět kopie systému nebo aktualizace systému


![Virtuální sítě připojené k razítkům velkých instancí Azure v různých oblastech Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

Obrázek znázorňuje připojení různých virtuálních sítí v obou oblastech na dva různé okruhy ExpressRoute, které se používají k připojení k SAP HANA v Azure (velké instance) v obou oblastech Azure (šedá řádky). Důvod pro tato dvě křížového spojení je ochrana po výpadku Msee na obou stranách. Tok komunikace mezi dvěma virtuálními sítěmi ve dvou oblastech Azure by měl být zpracován přes [globální partnerský vztah](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/) dvou virtuálních sítí ve dvou různých oblastech (modré tečkovaná čára). Tlustá čára red popisuje připojení ExpressRoute globální dosah, které povoluje jednotkách velká Instance HANA své tenanty ve dvou různých oblastech komunikovat mezi sebou. 

> [!IMPORTANT] 
> Pokud jste použili víc okruhů ExpressRoute, předřazení AS Path a nastavení místní Preference protokolu BGP by měla sloužit k zajištění správné směrování provozu.

**Další postup**
- Přečtěte si [architektura úložiště SAP HANA (velké instance)](hana-storage-architecture.md)