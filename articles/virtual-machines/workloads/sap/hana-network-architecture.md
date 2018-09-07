---
title: Síťová architektura SAP Hana v Azure (velké instance) | Dokumentace Microsoftu
description: Architektura sítě nasazení SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c2ab917f701ebcb78ae01a4ed97915858e5b95db
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44028040"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Síťová architektura SAP HANA (velké instance)

Architektura služby Azure network je klíčovou komponentou úspěšné nasazení aplikace SAP v HANA velkou instanci. SAP HANA v Azure (velké instance) nasazení obvykle mají větší prostředí SAP s několika různých řešení SAP s různou velikostí databází, využití prostředků procesoru a využití paměti v aplikaci. Je pravděpodobné, že ne všechny tyto systémy SAP jsou založené na systému SAP HANA. Prostředí SAP je pravděpodobně hybridní, který používá:

- Nasazení SAP systémy místní. Z důvodu jejich velikosti tyto systémy momentálně nemůže být hostovaná v Azure. Příkladem je produkční systém SAP ERP, který běží na SQL serveru (jako je databáze) a vyžaduje další prostředky procesoru nebo paměti než virtuální počítače můžete zadat.
- Na základě SAP HANA SAP systémy místně nasadili.
- Nasazené systémy SAP na virtuálních počítačích. Tyto systémy můžou být vývoj, testování, izolovaného prostoru, nebo produkční instance pro některé aplikace založené na systému SAP NetWeaver, které můžete úspěšně nasazovat v Azure (na virtuálních počítačích), na základě poptávky spotřeby a paměti prostředků. Tyto systémy také může být založen na databáze, jako jsou SQL Server. Další informace najdete v tématu [SAP Support Poznámka #1928533 – aplikace SAP v Azure: podporované produkty a typy virtuálních počítačů Azure](https://launchpad.support.sap.com/#/notes/1928533/E). A tyto systémy můžou být založené na databáze, jako jsou SAP HANA. Další informace najdete v tématu [platformách IaaS s certifikací SAP HANA](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).
- Nasazený aplikační servery SAP v Azure (na virtuálních počítačích), které využívají SAP HANA v Azure (velké instance) v razítka velkých instancí Azure.

Vytvoření hybridního prostředí SAP se čtyřmi nebo více různých scénářů nasazení je obvyklé. Existují také většinou zákazníka kompletní prostředí SAP, na kterých běží v Azure. Virtuální počítače budou výkonnější, roste počet zákazníků, které přesunout všechny svoje řešení SAP v Azure.

Sítě v rámci systémů SAP v Azure nasadit Azure není složitá. Je založen na následující zásady:

- Virtuální sítě Azure musí být připojen k okruhu ExpressRoute, který se připojuje k místní síti.
- Okruh ExpressRoute, která se obvykle připojuje místní do Azure by měl mít 1 GB/s nebo vyšší šířku pásma. Tato minimální šířku pásma umožňuje vyhrazena adekvátní šířka pásma pro přenos dat mezi místními systémy a systémy, které běží na virtuálních počítačích. Umožňuje také vyhrazena adekvátní šířka pásma pro připojení k systémům Azure z místních uživatelů.
- Všechny systémy SAP v Azure musí být nastavení ve virtuálních sítích komunikovat mezi sebou.
- Místně hostované služby Active Directory a DNS jsou v místním rozšíří do Azure přes ExpressRoute.


> [!NOTE] 
> Z fakturační pohledu pouze jedno předplatné Azure může být propojený jenom k jednomu tenantovi velká Instance razítka v konkrétní oblasti Azure. Naopak jednoho tenanta razítko velká Instance může být propojený jenom jedno předplatné Azure. Tento požadavek je konzistentní s jinými fakturovatelné objekty v Azure.

Pokud se SAP HANA v Azure (velké instance) se nasazuje v několika různých oblastech Azure, samostatného tenanta se nasadí v razítku velkou instanci. Můžete spustit i v rámci stejného předplatného Azure, pokud jsou tyto instance součástí stejné prostředí SAP. 

> [!IMPORTANT] 
> Se SAP HANA v Azure (velké instance) se podporuje pouze nasazení Azure Resource Manageru.

 

## <a name="additional-virtual-network-information"></a>Informace o další virtuální sítě

Pokud chcete připojení virtuální sítě pro ExpressRoute, musíte vytvořit brány Azure. Další informace najdete v tématu [o branách virtuálních sítí pro ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Služba Azure gateway je možné s využitím ExpressRoute k infrastruktuře mimo Azure nebo razítku velkých instancí Azure. Služba Azure gateway lze použít také pro připojení mezi virtuálními sítěmi. Další informace najdete v tématu [konfigurace síťové připojení pomocí prostředí PowerShell pro Resource Manager](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Služba Azure gateway na maximálně čtyři různá připojení ExpressRoute můžete připojit tak dlouho, dokud tato připojení pocházejí z různých hraničním směrovačům Microsoft enterprise. Další informace najdete v tématu [infrastrukturu SAP HANA (velké instance) a možnosti připojení v Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Propustnost, kterou poskytuje služba Azure gateway se liší pro oba případy použití. Další informace najdete v tématu [informace o službě VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Maximální propustnost, které můžete dosáhnout s bránou virtuální sítě je 10 GB/s s použitím připojení typu ExpressRoute. Kopírování souborů mezi virtuálním Počítačem, který se nachází ve virtuální síti a systém v místním (jako datový proud jedna kopie) nelze dosáhnout úplné propustnosti různé skladové položky brány. Využívat šířku pásma kompletní brány virtuální sítě pomocí různých datových proudů. Nebo je nutné zkopírovat různé soubory v paralelní datové proudy jednoho souboru.


## <a name="networking-architecture-for-hana-large-instance"></a>Architektura sítě pro velké Instance HANA
Síťové architektury pro velké Instance HANA lze rozdělit do čtyř různých částí:

- Místní sítě a připojení ExpressRoute k Azure. Tato část je doména zákazníka a je připojená k Azure přes ExpressRoute. Podívejte se vpravo dole na následujícím obrázku.
- Síť Azure services, jak jsme uvedli, s virtuálními sítěmi, které znovu mají brány. Tato část je oblast, kde potřebujete najít vhodné návrhů pro požadavky na aplikace, zabezpečení a požadavky na dodržování předpisů. Ať už používáte velká Instance HANA je jiné vzít v úvahu z hlediska počtu virtuálních sítí a Azure SKU brány lze vybírat. Najdete v pravém horním rohu na obrázku.
- Připojení velká Instance HANA přes ExpressRoute technologie do Azure. Tato část nasazení a postará Microsoft. Všechno, co je potřeba je zadat několik rozsahů IP adres po nasazení vaše prostředky v velká Instance HANA okruh ExpressRoute připojit k virtuálním sítím. Další informace najdete v tématu [infrastrukturu SAP HANA (velké instance) a možnosti připojení v Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- Použití sítě ve velké Instance HANA, což je většinou transparentní za vás.

![Virtuální sítě připojené k SAP HANA v Azure (velké instance) a místní](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Požadavek na vaše místní prostředky nutné se připojit přes ExpressRoute do Azure není změnit, protože používáte velká Instance HANA. Požadavek na jeden nebo více virtuálními sítěmi, na kterých běží virtuální počítače, které jsou hostiteli aplikační vrstvu, která se připojuje k HANA instance hostované v jednotkách velká Instance HANA, také nemění. 

Rozdíly pro nasazení SAP v Azure jsou následující:

- Velká Instance HANA jednotek vašeho tenanta zákazníka jsou připojené prostřednictvím jiného okruhu ExpressRoute do virtuální sítě. K oddělení podmínek zátěže, místní virtuální sítě ExpressRoute odkazů a odkazů mezi virtuálními sítěmi a velké Instance HANA Nesdílejte stejné směrovače.
- Profil zatížení mezi aplikační vrstvě SAP a velké Instance HANA je jiný druh, s mnoha malých požadavků a nárůstem třeba přenos dat (sad výsledků) ze SAP HANA na aplikační vrstvu.
- Architektura aplikací SAP je citlivější na latenci sítě než typické scénáře, kde data se přemisťují mezi místními a Azure.
- Brána virtuální sítě má aspoň dvě připojení ExpressRoute. Obě připojení sdílet maximální šířka pásma pro příchozí data brány virtuální sítě.

Latenci sítě mezi virtuálními počítači a velké Instance HANA jednotky může být vyšší než typické latence přenosu sítě virtuálních počítačů VM. Závisí na oblasti Azure, můžete hodnoty měří překročit round-trip latence 0,7 ms jsou klasifikovány jako podprůměrná v [SAP Poznámka #1100926 – nejčastější dotazy: výkon sítě](https://launchpad.support.sap.com/#/notes/1100926/E). Nicméně zákazníci nasadit na základě SAP HANA produkční aplikace SAP úspěšně na velké Instance SAP HANA. Zákazníci, kteří nasadí skvělé vylepšené sestavy spuštěním svých aplikací SAP na systému SAP HANA pomocí jednotek velká Instance HANA. Zajistěte, aby že důkladně otestovat vašich obchodních procesů ve velké Instance Azure HANA.
 
Zajištění deterministické sítích s latencí mezi virtuálními počítači a velké Instance HANA brány virtuální sítě SKU je nezbytné. Na rozdíl od vzorů provozu mezi místními a virtuálními počítači můžete vyvíjet vzor provoz mezi virtuálními počítači a velké Instance HANA malou, avšak vysokou nárůstům požadavků a datové svazky přenášet. Pro zpracování takových nárůstem dobře, důrazně doporučujeme používat SKU brány UltraPerformance. Pro třídu typu II SKU velké Instance HANA je povinné použití SKU brány UltraPerformance jako bránu virtuální sítě.

> [!IMPORTANT] 
> Zadaný celkový provoz sítě mezi aplikací SAP a databázi vrstvy, pouze HighPerformance nebo SKU brány UltraPerformance pro virtuální sítě jsou podporovány pro připojení k SAP HANA v Azure (velké instance). Pro HANA velké Instance SKU typu II je podporován pouze SKU brány UltraPerformance jako bránu virtuální sítě.



## <a name="single-sap-system"></a>Jeden systému SAP

V místní infrastruktuře bylo dříve uvedeno je připojené prostřednictvím ExpressRoute do Azure. Okruh ExpressRoute se připojuje do hraniční směrovač podniku. Další informace najdete v tématu [technický přehled ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Po navázání trasy připojí do páteřní síť Azure a všech oblastech Azure jsou dostupné.

> [!NOTE] 
> Pokud chcete spustit prostředí SAP v Azure, připojte k hraniční směrovač podniku nejbližší oblasti Azure v prostředí SAP. Azure velká Instance razítka jsou propojené prostřednictvím vyhrazené podnikové hraniční směrovače zařízení kvůli minimalizaci latence sítě mezi virtuálními počítači v Azure IaaS a velké Instance razítka.

Brána virtuální sítě pro virtuální počítače, které hostují instance aplikace SAP je připojen k okruhu ExpressRoute. Stejné virtuální síti je připojený k hraniční směrovač vyhrazený pro připojení k velké Instance razítka samostatné podniku.

Tento systém je jednoduchý příklad jednoho systému SAP. Aplikační vrstvě SAP je hostované v Azure. Databáze SAP HANA běží na systému SAP HANA v Azure (velké instance). Předpokladem je, že kritický bod nepředstavuje šířky pásma brány virtuální sítě, propustnost 2 GB/s nebo 10 GB/s.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Několik systémů SAP vyhrazené nebo rozsáhlých systémů SAP

Pokud pro připojení k SAP HANA v Azure (velké instance) jsou nasazeny více systémů SAP vyhrazené nebo rozsáhlých systémů SAP, propustnosti brány virtuální sítě může být kritickým bodem. V takovém případě rozdělení vrstvy aplikace na více virtuálních sítí. Můžete také vytvořit zvláštní virtuální síť, která se připojuje k velká Instance HANA pro případy, například:

- Provádění zálohování přímo z instance HANA ve velké Instance HANA VM v Azure, který je hostitelem sdílené složky NFS.
- Kopírování velkých zálohy nebo jiné soubory z jednotky velká Instance HANA do místa na disku spravované v Azure.

Použijte samostatné virtuální sítě pro hostitele virtuálních počítačů, které Správa služby storage. Toto uspořádání se vyhnete účinky velkých souborů nebo přenos dat ze velká Instance HANA do Azure na bráně virtuální sítě, který poskytuje virtuální počítače, na kterých běží SAP aplikační vrstvu. 

Pro větší škálovatelnost síťové architektury:

- Využívejte více virtuálních sítí pro jeden, větší SAP aplikační vrstvu.
- Nasazení jedné samostatné virtuální sítě pro každý systém SAP nasazení, ve srovnání se sloučením tyto systémy SAP do samostatných podsítí v rámci stejné virtuální síti.

 Větší škálovatelnost sítě architektura pro SAP HANA v Azure (velké instance):

![Nasazení aplikační vrstvě SAP přes více virtuálních sítí](./media/hana-overview-architecture/image4-networking-architecture.png)

Obrázek ukazuje aplikační vrstvě SAP nebo komponenty, které jsou nasazené v několika virtuálními sítěmi. Tato konfigurace zavedené nevyhnutelné latence režijní náklady, ke které došlo při komunikaci mezi aplikací hostovaných v těchto virtuálních sítích. Ve výchozím nastavení, síťový provoz mezi virtuálními počítači v různých virtuálních sítích směrovat přes hraniční směrovače organizace v této konfiguraci. Vytvoření partnerského vztahu virtuálních sítí ve stejné oblasti je možné optimalizovat a snížení latence při komunikaci mezi dvěma virtuálními sítěmi. Tato metoda se dá použít i v případě, že jsou tyto virtuální sítě v různých předplatných. Partnerský vztah virtuální sítě, komunikace mezi virtuálními počítači ve dvou různých virtuálních sítích pomocí páteřní síť Azure přímo komunikovat mezi sebou. Latence ukazuje, jak v případě, že jsou virtuální počítače ve stejné virtuální síti. Provoz, který adresuje rozsahy IP adres, které jsou připojené přes bránu virtuální sítě Azure je směrován přes bránu virtuální sítě jednotlivé virtuální sítě. 

Další informace o partnerských vztahů virtuálních sítí najdete v tématu [partnerský vztah virtuálních sítí](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


## <a name="routing-in-azure"></a>Směrování v Azure

Tři aspekty směrování sítě jsou důležité pro SAP HANA v Azure (velké instance):

* SAP HANA v Azure (velké instance) můžete přistupovat pouze prostřednictvím virtuálních počítačů a vyhrazené připojení ExpressRoute, ne přímo z místní. Přímý přístup z místní jednotky velká Instance HANA dodaných společností Microsoft, není možné okamžitě. Přenositelné směrování omezení jsou způsobeny aktuální architektury sítě Azure používané pro velké Instance SAP HANA. Někteří klienti správy a všechny aplikace, které potřebují přímý přístup, jako je SAP správci řešení spuštěné v místním nemůže připojit k databázi SAP HANA.

* Pokud máte velké Instance HANA jednotky nasazené ve dvou různých oblastech Azure pro zotavení po havárii, omezení přechodné směrování použita. Jinými slovy IP adresy velká Instance HANA jednotka v jedné oblasti (například USA – západ) nejsou směrovány na jednotku velká Instance HANA nasadit v jiné oblasti (například USA – východ). Toto omezení je nezávislé na použití síť Azure, partnerský vztah napříč oblastmi nebo křížové připojení okruhy ExpressRoute, které se připojují velká Instance HANA jednotek do virtuálních sítí. Grafické znázornění viz obrázek v části "použití velká Instance HANA ve více oblastech. jednotek" Toto omezení, který se dodává s nasazenou architekturou zakazuje okamžité použijte HANA System Replication funkce zotavení po havárii.

* SAP HANA v Azure (velké instance) jednotky mají přiřazenou IP adresu z rozsah adres fondu IP serveru, který jste odeslali. Další informace najdete v tématu [infrastrukturu SAP HANA (velké instance) a možnosti připojení v Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Tato IP adresa je přístupné prostřednictvím ExpressRoute, který se připojuje k HANA v Azure (velké instance) virtuální sítě a předplatných Azure. Přidělit IP adresu z celkového počtu, rozsah adres fondu IP serveru přímo přiřazená jednotky hardwaru. Má *není* přiřazeném prostřednictvím překladu adres už nepotřebujeme, stejně jako v případě v prvním nasazení tohoto řešení. 

> [!NOTE] 
> K překonání omezení v přechodné směrování, jak je vysvětleno v prvních dvou seznam položek, použijte další komponenty pro směrování. Součásti, které slouží k překonání tohoto omezení může být:

> * Reverzních proxy serverů pro směrování dat do a z. Například F5 BIG-IP, server NGINX pomocí Traffic Manageru nasazené v Azure jako virtuální brána firewall/přenosů směrování řešení.
> * Pomocí [pravidla iptables tak](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) v virtuálního počítače s Linuxem umožňující směrování mezi místními umístěními a velké Instance HANA jednotky, nebo mezi jednotkami velká Instance HANA v různých oblastech.

> Mějte na paměti, že implementaci a podporu pro vlastní řešení zahrnující třetích stran síťových zařízení nebo IPTables není k dispozici společností Microsoft. Podpora musí poskytnout dodavatel komponentu používanou nebo integrátor. 

## <a name="internet-connectivity-of-hana-large-instance"></a>Připojení k Internetu velké instance HANA
Velká Instance HANA neodpovídá *není* mít přímé připojení k Internetu. Jako příklad toto omezení může omezit možnost zaregistrovat image operačního systému přímo s operačním systémem. Můžete potřebovat pro práci s vaším místním operačním systémem SUSE Linux Enterprise Server předplatné Management Tool serveru nebo správce předplatného Red Hat Enterprise Linux.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Šifrování dat mezi virtuálními počítači a velké Instance HANA
Data přenášená mezi velká Instance HANA a virtuální počítače nejsou šifrována. Výhradně pro server exchange mezi HANA DBMS na straně a aplikace založené na JDBC/ODBC, ale můžete povolit šifrování přenosů. Další informace najdete v tématu [této dokumentace SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Použití jednotek velká Instance HANA ve více oblastech

Bude pravděpodobně z důvodů pro nasazení SAP HANA v Azure (velké instance) v několika oblastech Azure kromě pro zotavení po havárii. Možná budete chtít přístup ze všech virtuálních počítačů nasazených v různých virtuálních sítích v oblastech velká Instance HANA. IP adresy přiřazené k jiné velké Instance HANA jednotek nejsou šířeny nad rámec virtuální sítě, které jsou přímo připojené prostřednictvím jejich brány do instancí. V důsledku toho se používá i drobnou změnu na návrh virtuální sítě. Bránu virtuální sítě může zpracovávat čtyři různé okruhy ExpressRoute, mimo jiné podnikové hraniční směrovače. Každá virtuální síť, který je připojený k jednomu z razítka velká Instance může být připojen k velké Instance razítka v jiné oblasti Azure.

![Virtuální sítě připojené k razítkům velkých instancí Azure v různých oblastech Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

Obrázek ukazuje, jak různých virtuálních sítí v obou oblastech jsou připojené dva různé okruhy ExpressRoute, které se používají k připojení k SAP HANA v Azure (velké instance) v obou oblastech Azure. Nově zavedená připojení jsou obdélníkové červené čáry. Tato připojení z virtuální sítě virtuální počítače spuštěné v jednom z těchto virtuálních sítí můžete přístup k každý z různých jednotek velká Instance HANA nasazené ve dvou oblastech. Jak ukazuje obrázek, se předpokládá, že máte dvě spojení ExpressRoute z místního na dvou oblastech Azure. Toto uspořádání se doporučuje z důvodů pro zotavení po havárii.

> [!IMPORTANT] 
> Pokud jste použili víc okruhů ExpressRoute, předřazení AS Path a nastavení místní Preference protokolu BGP by měla sloužit k zajištění správné směrování provozu.

**Další kroky**
- Přečtěte si [architektura úložiště SAP HANA (velké instance)](hana-storage-architecture.md)