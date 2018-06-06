---
title: Přehled a architektura SAP HANA v Azure (velké instance) | Microsoft Docs
description: Přehled architektury nasazení SAP HANA v Azure (velké instance).
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
ms.date: 06/04/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bfcab5a84d9e8b0bf164c666162636ede2e1b06f
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763778"
---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>Přehled SAP HANA (velké instance) a architektura v Azure

## <a name="what-is-sap-hana-on-azure-large-instances"></a>Co je SAP HANA v Azure (velké instance)?

SAP HANA v Azure (velké instance) je jedinečné řešení do Azure. Kromě zajištění virtuálních počítačů pro nasazení a spuštění SAP HANA, Azure nabízí možnost spuštění a nasazení SAP HANA na úplné obnovení serverů, které jsou vyhrazené pro vás. SAP HANA v Azure (velké instance) řešení je založena na nesdílené serveru nebo úplné obnovení hardware, který je přiřazen. Hardware serveru je součástí větší razítek, které obsahují výpočty a serveru, sítě a infrastruktury úložiště. V kombinaci je HANA přizpůsobit integrace center dat (TDI) certifikaci. SAP HANA v Azure (velké instance) nabízí SKU jiný server nebo velikosti. Jednotky mají 72 procesory a 768 GB paměti a přejděte do jednotky, které mají 960 procesory a 20 TB paměti.

Izolace zákazníka v rámci infrastruktury razítko se provádí v klientech, který vypadá takto:

- **Sítě**: izolace zákazníků v rámci infrastruktury zásobníku prostřednictvím virtuální sítě na zákazníka přiřadit klienta. Klient je přiřazen jednoho zákazníka. Zákazník může mít několik klientů. Izolace sítě klientů, které zakazuje síťovou komunikaci mezi klienty v úrovni razítka infrastruktury, i v případě, že klienti patří do stejného zákazníka.
- **Součástí úložišť**: izolaci prostřednictvím úložiště virtuálních počítačů, které mají svazky úložiště, které jsou jim přiřazeny. Svazky úložiště lze přiřadit pouze jeden úložiště virtuálnímu počítači. Úložiště virtuálního počítače je přiřazen výhradně jeden jednoho klienta v zásobníku certifikovaných infrastruktury SAP HANA TDI. V důsledku toho je přístupná v jedné konkrétní a související klientovi pouze svazky úložiště, které jsou přiřazeny k virtuálnímu počítači úložiště. Nejsou viditelné mezi různými klienty nasazené.
- **Server nebo hostitele**: jednotka serveru nebo hostitele není sdílena mezi zákazníky nebo klientů. Server nebo hostitele nasazený na zákazníka, je atomic holých výpočetní jednotka, přiřazený jeden jednoho klienta. *Ne* dělení hardwaru nebo logicky dělení se má použít, může vést k tomu můžete sdílení hostitel nebo server s jinou zákazníka. Úložné svazky, které jsou přiřazeny k virtuálnímu počítači úložiště konkrétní klienta jsou připojené k takové serveru. Klient může mít jeden do mnoha serveru jednotek různé identifikátory SKU výhradně přiřazen.
- V rámci SAP HANA s časovým razítkem infrastrukturu Azure (velké instance) mnoha různými klienty jsou nasazené a izolované proti sobě prostřednictvím klienta koncepty na sítě, úložiště a výpočetní úroveň. 


Tyto jednotky úplné obnovení serveru jsou podporovány pouze spustit SAP HANA. Vrstvy aplikace SAP nebo zatížení střední VMware běží ve virtuálních počítačích. Razítka infrastruktury se systémem SAP HANA na jednotkách Azure (velké instance) jsou připojené k páteřním služby síť Azure. Tímto způsobem se poskytuje s nízkou latencí připojení mezi SAP HANA na jednotkách Azure (velké instance) a virtuální počítače.

Tento dokument je jedním z několika dokumentů, které se týkají SAP HANA v Azure (velké instance). Tento dokument uvádí základní architekturu, zodpovědnostech a služeb poskytovaných řešení. Nejdůležitější funkce řešení jsou popsány i. Pro většinu dalších oblastech, jako je vytváření sítí a připojení se věnují čtyři jiné dokumenty podrobnosti a zobrazit informace. V dokumentaci SAP HANA v Azure (velké instance) nezahrnuje aspekty instalace SAP NetWeaver nebo nasazení SAP NetWeaver ve virtuálních počítačích. SAP NetWeaver v Azure je popsaná v samostatné dokumenty najít ve stejném kontejneru dokumentace k Azure. 


Různé dokumenty velké Instance HANA materiálů zahrnovat tyto oblasti:

- [Přehled SAP HANA (velké instance) a architektura v Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infrastruktura SAP HANA (velké instance) a připojení v Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Instalace a konfigurace SAP HANA (velké instance) na Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (velké instance) vysoké dostupnosti a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Řešení potíží s SAP HANA (velké instance) a sledování v Azure](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Vysoká dostupnost nastavit v SUSE pomocí STONITH](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [Operační systém zálohování a obnovení pro typ II SKU](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

## <a name="definitions"></a>Definice

Několik společné definice se často používá v architektuře a technické příručce nasazení. Všimněte si následujících podmínek a jejich významů:

- **IaaS**: infrastruktura jako služba.
- **PaaS**: platforma jako služba.
- **SaaS**: Software jako služba.
- **Součást SAP**: jednotlivých aplikací SAP, například ERP centrální součást (ECC), obchodní skladu (BW), správce řešení nebo podnikového portálu (EP). SAP součástí může být založen na tradičních technologií ABAP nebo Java nebo jiných NetWeaver na základě aplikaci, například obchodních objektů.
- **Prostředí SAP**: jeden nebo více součástí SAP logicky seskupeny provést obchodní funkci, jako je třeba vývoje, zajištění kvality, školení, zotavení po havárii nebo výroby.
- **Na šířku SAP**: odkazuje na celý SAP prostředky ve vaší IT na šířku. Na šířku SAP zahrnuje všechny produkční a mimo provozní prostředí.
- **SAP systému**: kombinace databázového systému vrstvy a aplikaci vrstvu, například vývoj systému SAP ERP, testovacím systému SAP BW a produkční systém SAP CRM. Azure nasazení nepodporují dělení tyto dvě vrstvy mezi místními a Azure. Systému SAP je nasadit místně nebo jeho nasazené v Azure. Můžete nasadit různých systémech šířku SAP do Azure nebo místní. Například můžete nasadit SAP CRM vývoj a testování systémy v Azure při nasazování SAP CRM produkční systému místní. Pro SAP HANA v Azure (velké instance) je určena hostujete aplikační vrstvu SAP SAP systémů ve virtuálních počítačích a související instance SAP HANA na jednotce v SAP HANA s časovým razítkem Azure (velké instance).
- **Velké Instance razítka**: zásobník infrastruktury hardwaru, certifikovaným SAP HANA TDI a vyhrazených ke spuštění SAP HANA instancí v rámci Azure.
- **SAP HANA v Azure (velké instance):** oficiální název nabídky v Azure ke spuštění HANA instancí v na hardwaru certifikovaném SAP HANA TDI, která je nasazena v velké Instance razítka v různých oblastech Azure. Související termín *velké Instance HANA* je zkratka pro *SAP HANA v Azure (velké instance)* a se často používá v této příručce technického nasazení.
- **Mezi různými místy**: popisuje scénář, kde jsou nasazené virtuální počítače k předplatnému Azure, který má site-to-site, více lokalit nebo Azure ExpressRoute připojení mezi místní datových center a Azure. Dokumentace k společné Azure, tyto typy nasazení jsou také popsány jako mezi různými místy scénáře. Z důvodu pro připojení je rozšířit místní domény, místní Azure Active Directory nebo OpenLDAP a místní DNS do Azure. Povahu místní je rozšířeno na Azure majetek předplatná Azure. S touto příponou virtuálních počítačů může být součástí místní domény. 

   Uživatelé domény místní domény, můžete přístup k serverům a spouštění služeb na těchto virtuálních počítačích (například služby databázového systému). Komunikace a název rozlišení mezi virtuálními počítači nasazen místní a virtuální počítače nasazené Azure je možné. Tento scénář je typické pro způsob, ve které jsou nasazeny většina SAP prostředky. Další informace najdete v tématu [plánování a návrhu pro Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a [vytvoření virtuální sítě pomocí připojení site-to-site pomocí portálu Azure](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Klienta**: získá do izolované zákazník nasazené v HANA velké Instance razítka *klienta.* Klient je izolované sítě, úložiště a výpočetní vrstvy od ostatních klientů. Úložiště a výpočetní jednotky přiřazené různými klienty nelze zobrazit navzájem ani vzájemně komunikovat na úrovni HANA velké Instance razítka. Zákazník může rozhodnout pro nasazení do jiných klientů. Dokonce i pak není žádná komunikace mezi klienty na úrovni HANA velké Instance razítka.
- **Kategorie SKU**: pro HANA velké Instance, nabízí tyto dvě kategorie SKU:
    - **Typ I třídy**: S72, S72m, S144, S144m, S192, S192m a S192xm
    - **Zadejte třídu II**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm a S960m


Jsou k dispozici na tom, jak nasadit SAP úlohy v cloudu různé další prostředky. Pokud máte v plánu provést nasazení SAP HANA v Azure, budete muset být zkušenosti s a s ohledem na zásady služby Azure IaaS a nasazení SAP zatížení v Azure IaaS. Než budete pokračovat, najdete v části [použití SAP řešení na virtuálních počítačích Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Další informace. 

## <a name="certification"></a>Certifikace

Kromě toho na certifikaci NetWeaver SAP vyžaduje speciální certifikační pro SAP HANA pro podporu SAP HANA na určité infrastruktuře, jako je například Azure IaaS.

Jádro Poznámka SAP na NetWeaver a pro SAP HANA stupeň certifikační [SAP Poznámka #1928533 – SAP aplikace v Azure: podporované produkty a virtuální počítač Azure typy](https://launchpad.support.sap.com/#/notes/1928533).

[SAP Poznámka #2316233 - SAP HANA v Microsoft Azure (velké instance)](https://launchpad.support.sap.com/#/notes/2316233/E) je také důležité. Vysvětluje řešení popsané v tomto průvodci. Kromě toho jsou podporovány ke spuštění SAP HANA v typu GS5 virtuálních počítačů Azure. Informace o tomto případě je publikována na [webu SAP](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).

SAP HANA na řešení Azure (velké instance) uvedené v 2316233 # Poznámka SAP poskytuje zákazníkům společnosti Microsoft a SAP schopnost nasadit velké SAP Business Suite SAP BW, S/4 HANA BW/4HANA nebo jiných SAP HANA úlohy v Azure. Řešení je založena na značce SAP HANA certifikovaných vyhrazený hardware ([SAP HANA přizpůsobit integrace center dat – TDI](https://scn.sap.com/docs/DOC-63140)). Pokud spustíte SAP HANA TDI nakonfigurované řešení, všechny aplikace na základě SAP HANA (například SAP Business Suite na SAP HANA, SAP BW SAP HANA, S4/HANA a BW4/HANA) funguje na hardware infrastruktury.

Porovnání se spouštěním SAP HANA ve virtuálních počítačích, toto řešení obsahuje výhody. Poskytuje pro mnohem větší objemy paměti. Chcete-li povolit toto řešení, je potřeba pochopit následující klíčové aspekty:

- SAP aplikace vrstvu a bez SAP aplikace spustit ve virtuálních počítačích, které jsou hostované v razítka obvykle Azure hardwaru.
- Zákazník místní infrastrukturu, datových center a nasazení aplikací, které jsou připojené k Cloudová platforma prostřednictvím ExpressRoute (doporučeno) nebo virtuální privátní sítě (VPN). Služba Active Directory a DNS také jsou rozšířené do Azure.
- Instance databáze SAP HANA pro pracovní vytížení HANA běží na SAP HANA v Azure (velké instance). Razítko velké Instance je připojený do sítě Azure, takže software na virtuální počítače mohou komunikovat s HANA instance spuštěné v instanci velké HANA.
- Hardware SAP HANA v Azure (velké instance) je vyhrazený hardware, které jsou součástí IaaS s SUSE Linux Enterprise Server nebo Red Hat Enterprise Linux předinstalována. Stejně jako u virtuálních počítačů, další aktualizace a údržba pro operační systém je vaší povinností.
- Instalace HANA nebo žádné další součásti, které jsou nezbytné ke spuštění na jednotkách HANA velké instance SAP HANA je vaší povinností. Všechny příslušné probíhající operace a správa SAP HANA v Azure jsou také vaší povinností.
- Kromě pomocí řešení popisovaných v tomto poli můžete nainstalovat další součásti ve vašem předplatném Azure, která se připojuje k SAP HANA v Azure (velké instance). Příklady jsou komponenty, které umožňují komunikaci s, nebo přímo k SAP HANA databázové, jako jsou třeba servery Přechod protokolu RDP servery, SAP HANA Studio, SAP Data Services pro SAP BI scénáře, nebo sítě řešení monitorování.
- Jako v Azure velké Instance HANA nabízí podporu pro vysokou dostupnost a funkci obnovení po havárii.

## <a name="architecture"></a>Architektura

SAP HANA na řešení Azure (velké instance) na vysoké úrovni, má aplikační vrstvu SAP, které se nacházejí ve virtuálních počítačích. V databázové vrstvě se nachází na hardwaru SAP TDI nakonfigurované v razítku velké Instance ve stejné oblasti Azure, která je připojena k Azure IaaS.

> [!NOTE]
> Nasazení SAP aplikační vrstvu ve stejné oblasti Azure jako vrstva databázového systému SAP. Toto pravidlo je dobře zdokumentovat v publikované informace o SAP úlohy v Azure. 

Přehled architektury SAP HANA v Azure (velké instance) poskytuje konfigurace hardwaru certifikovaném SAP TDI, což je nevirtuálním, úplného operačního systému, vysoce výkonné server pro databázi SAP HANA. Poskytuje taky možnost a flexibilitu Azure k prostředkům škálování pro aplikační vrstvy SAP podle svých potřeb.

![Přehled architektury systému SAP HANA v Azure (velké instance)](./media/hana-overview-architecture/image1-architecture.png)

Architektura vidět je rozdělené do tří částí:

- **Pravé**: ukazuje na místní infrastrukturu, který spouští různé aplikace v datech centra, aby koncoví uživatelé přístup obchodních aplikací, jako je například SAP. V ideálním případě by to místní infrastruktury je připojen do Azure s [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Center**: ukazuje Azure IaaS a virtuálních počítačů. v takovém případě použijte k hostování SAP nebo jiné aplikace, které používají SAP HANA jako systém databázového systému. Menší HANA instancí, které funkce s pamětí, které poskytují virtuálních počítačů nasazených ve virtuálních počítačích spolu s jejich aplikační vrstvu. Další informace o virtuálních počítačích najdete v tématu [virtuální počítače](https://azure.microsoft.com/services/virtual-machines/).

   Síť Azure služby slouží k seskupení systémy SAP společně s jiných aplikací do virtuální sítě. Tyto virtuální sítě připojit k místním systémům i, SAP HANA v Azure (velké instance).

   SAP NetWeaver aplikace a databáze, které jsou podporována pro spuštění v Azure najdete v tématu [SAP podporu Poznámka #1928533 – SAP aplikace v Azure: podporované produkty a virtuální počítač Azure typy](https://launchpad.support.sap.com/#/notes/1928533). Dokumentaci k nasazení SAP řešení v Azure najdete v tématu:

  -  [Použití SAP na virtuálních počítačích s Windows](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Použití SAP řešení na virtuálních počítačích Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Levé**: zobrazí hardware certifikovaný SAP HANA TDI v razítku velké instanci Azure. Instance HANA velké jednotky jsou připojené k virtuálním sítím předplatného pomocí stejnou technologii jako připojení z místního do Azure.

Razítko velké instanci Azure samotné kombinuje následující součásti:

- **Computing**: servery, které jsou založeny na procesory Intel Xeon E7-8890v3 nebo Intel Xeon E7-8890v4, s nezbytné výpočetní schopnosti, které jsou certifikované SAP HANA.
- **Sítě**: A unified vysokorychlostní síťové fabric, která propojení výpočty, úložiště a LAN součásti.
- **Úložiště**: infrastruktury úložiště, které je přístupné přes jednotné síťových prostředcích infrastruktury. Konkrétní úložnou kapacitu, která je k dispozici, závisí na konkrétní SAP HANA v konfiguraci Azure (velké instance), která je nasazena. Větší kapacitu úložiště je k dispozici v dalších měsíčních nákladů.

V infrastruktuře víceklientské velké Instance razítka jsou zákazníci nasadit jako izolované klienty. V nasazení klienta název v rámci Azure registraci předplatného Azure. Toto předplatné je ten, který velké Instance HANA se fakturuje proti. Tyto klienty mít vztah 1:1 k předplatnému Azure. Pro síť je možné získat přístup k jednotce velké Instance HANA nasazené v jednoho klienta v jedné oblasti Azure z různých virtuálních sítích, které patří do různých předplatných Azure. Těchto předplatných Azure musí patřit do stejné Azure registrace. 

Stejně jako u virtuálních počítačů, SAP HANA v Azure (velké instance) je k dispozici v několika oblastmi Azure. Nabídka Možnosti obnovení po havárii, můžete vyjádřit výslovný souhlas. Různé velké Instance razítka v rámci jedné politickým geografické oblasti jsou připojené k sobě navzájem. Například HANA velké Instance razítka v oblasti USA – západ a oblasti USA – východ jsou připojené prostřednictvím propojení s vyhrazenou síť pro replikaci obnovení po havárii. 

Stejně jako, můžete si vybrat mezi různé typy virtuálních počítačů s virtuálními počítači Azure, můžete z různých SKU z HANA velké instanci, která jsou vytvořena speciálně pro různé zatížení typy SAP HANA. SAP platí poměr paměti na procesor soketu pro různou zátěží podle generace procesor Intel. Následující tabulka uvádí typy SKU nabízí.

Od července 2017 je k dispozici v několika konfigurace v oblasti USA – západ USA – východ, Austrálie – východ, Austrálie – jihovýchod, západní Evropa, a Severní Evropa SAP HANA v Azure (velké instance).

| Řešení SAP | Procesor | Memory (Paměť) | Úložiště | Dostupnost |
| --- | --- | --- | --- | --- |
| Optimalizovaná pro OLAP: SAP BW BW/4HANA<br /> nebo SAP HANA pro obecné zatížení OLAP | Na Azure S72 SAP HANA<br /> – 2 procesor Intel Xeon® x E7 8890 v3<br /> 36 jader procesoru a 72 procesoru vláken |  768 GB |  3 TB | K dispozici. |
| --- | Na Azure S144 SAP HANA<br /> – Procesor Intel Xeon® x 4 E7 8890 v3<br /> 72 jader procesoru a 144 procesoru vláken |  1,5 TB |  6 TB | Nenabízí se už |
| --- | Na Azure S192 SAP HANA<br /> – Procesor Intel Xeon® x 4 E7 8890 v4<br /> 96 jader procesoru a 192 procesoru vláken |  2.0 TB |  8 TB | K dispozici. |
| --- | Na Azure S384 SAP HANA<br /> – 8 procesor Intel Xeon® x E7 8890 v4<br /> 192 jader procesoru a 384 procesoru vláken |  4.0 TB |  16 TB | K dispozici. |
| Optimalizovaná pro OLTP: SAP Business Suite<br /> na SAP HANA nebo S nebo 4HANA (OLTP)<br /> Obecné OLTP | Na Azure S72m SAP HANA<br /> – 2 procesor Intel Xeon® x E7 8890 v3<br /> 36 jader procesoru a 72 procesoru vláken |  1,5 TB |  6 TB | K dispozici. |
|---| Na Azure S144m SAP HANA<br /> – Procesor Intel Xeon® x 4 E7 8890 v3<br /> 72 jader procesoru a 144 procesoru vláken |  3.0 TB |  12 TB | Nenabízí se už |
|---| Na Azure S192m SAP HANA<br /> – Procesor Intel Xeon® x 4 E7 8890 v4<br /> 96 jader procesoru a 192 procesoru vláken  |  4.0 TB |  16 TB | K dispozici. |
|---| Na Azure S384m SAP HANA<br /> – 8 procesor Intel Xeon® x E7 8890 v4<br /> 192 jader procesoru a 384 procesoru vláken |  6.0 TB |  18 TB | K dispozici. |
|---| Na Azure S384xm SAP HANA<br /> – 8 procesor Intel Xeon® x E7 8890 v4<br /> 192 jader procesoru a 384 procesoru vláken |  8.0 TB |  22 TB |  K dispozici. |
|---| Na Azure S576m SAP HANA<br /> – Procesor Intel Xeon® x 12 E7 8890 v4<br /> 288 jader procesoru a 576 procesoru vláken |  12.0 TB |  28 TB | K dispozici. |
|---| Na Azure S768m SAP HANA<br /> – Procesor Intel Xeon® x 16 E7 8890 v4<br /> 384 jader procesoru a 768 procesoru vláken |  16.0 TB |  36 TB | K dispozici. |
|---| Na Azure S960m SAP HANA<br /> – 20 procesor Intel Xeon® x E7 8890 v4<br /> 480 jader procesoru a 960 procesoru vláken |  20.0 TB |  46 TB | K dispozici. |
| Optimalizovaná pro OLTP **TDIv5**: SAP Business Suite<br /> na SAP HANA nebo S nebo 4HANA (OLTP)<br /> Obecné OLTP | Na Azure S192xm SAP HANA<br /> – Procesor Intel Xeon® x 4 E7 8890 v4<br /> 96 jader procesoru a 192 procesoru vláken |  6.0 TB |  16 TB | K dispozici. |
|---| Na Azure S384xxm SAP HANA<br /> – 8 procesor Intel Xeon® x E7 8890 v4<br /> 192 jader procesoru a 384 procesoru vláken |  12.0 TB |  28 TB | K dispozici. |
|---| Na Azure S576xm SAP HANA<br /> – Procesor Intel Xeon® x 12 E7 8890 v4<br /> 288 jader procesoru a 576 procesoru vláken |  18.0 TB |  41 TB | K dispozici. |
|---| Na Azure S768xm SAP HANA<br /> – Procesor Intel Xeon® x 16 E7 8890 v4<br /> 384 jader procesoru a 768 procesoru vláken |  24,0 TB |  56 TB | K dispozici. |
- Jader procesoru = součet jiný-technologie hyper-threaded jader procesoru součtu procesorů jednotky serverů.
- Vláken procesoru = součet výpočetní vláken poskytované technologie hyper-threaded jader procesoru součtu procesorů jednotky serverů. Všechny jednotky jsou nakonfigurované ve výchozím nastavení používají technologii Hyper-Threading.


Konkrétní konfigurace vybrali jsou závislé na zatížení, prostředky procesoru a požadovanou paměť. Je možné pro pracovní vytížení OLTP používat SKU, které jsou optimalizované pro pracovní vytížení OLAP. 

Všechny nabídky základní hardware jsou certifikované SAP HANA TDI. Dvěma různými třídami hardwaru dělí SKU do:

- S72 S72m, S144, S144m, S192, S192m a S192xm, které jsou označovány jako "Typ I třídy" z jednotky SKU.
- S384, S384m, S384xm, S384xxm, S576m, S576xm S768m, S768xm a S960m, které se označují jako "Typ třídy II" z jednotky SKU.

Dokončení HANA velké Instance razítka není přidělen výhradně pro jednoho zákazníka&#39;s použití. Tuto skutečnost se vztahuje na stojany s výpočetní a úložnou kapacitu připojené prostřednictvím síťových prostředcích infrastruktury také nasazené v Azure. Velké Instance HANA infrastruktury, jako je například Azure, nasadí různých zákazníků &quot;klienty&quot; , které jsou izolované od sebe navzájem v následující tři úrovně:

- **Sítě**: izolaci pomocí virtuální sítě v rámci HANA velké Instance razítka.
- **Úložiště**: izolaci prostřednictvím úložiště virtuálních počítačů, které mají přiřazené svazky úložiště a izolovat svazky úložiště mezi klienty.
- **Výpočetní**: vyhrazený přiřazení serveru jednotek pro jednoho klienta. Ne pevného nebo soft oddíly jednotky serverů. Bez sdílení jedné jednotky serveru nebo hostitele mezi klienty. 

Nasazení velkých Instance HANA jednotky mezi různými klienty nejsou viditelné pro sebe navzájem. Instance HANA velké jednotky nasazené v jiných klientů nemůže komunikovat přímo s jinými na úrovni HANA velké Instance razítka. Pouze Instance HANA velké jednotky v rámci jednoho klienta může na úrovni HANA velké Instance razítka vzájemně komunikovat.

Nasazené klienta v razítko velké Instance je přiřazený k jedno předplatné pro účely fakturace. Pro síť můžete přistupovat z virtuální sítě z jiných předplatných Azure v rámci stejné Azure registrace. Pokud nasadíte pomocí jiného předplatného Azure ve stejné oblasti Azure, také můžete požádat o klienta oddělené HANA velké Instance.

Existují významné rozdíly mezi systémem SAP HANA na velké instanci HANA a SAP HANA běžící na virtuálních počítačích, které jsou nasazené v Azure:

- Neexistuje žádné vrstva virtualizace pro SAP HANA v Azure (velké instance). Zobrazí výkon základní hardware úplné obnovení.
- Na rozdíl od Azure jsou vyhrazené SAP HANA na serveru Azure (velké instance) na konkrétního zákazníka. Není možné, že server jednotky nebo hostitele pevného nebo soft rozdělena na oddíly. V důsledku toho jednotce HANA velké Instance slouží jako přiřadit jako celek do klienta a který pro vás. Restartování nebo vypnutí serveru nebude automaticky vést k operačního systému a SAP HANA nasazován na jiném serveru. (Pro typ I třídy SKU, jedinou výjimkou je, pokud server narazí problémy a opětovné nasazení je nutné provést na jiném serveru.)
- Na rozdíl od Azure, kde jsou vybrané typy procesoru hostitele pro nejlepší poměr ceny a výkonu, typy procesoru zvolené pro SAP HANA v Azure (velké instance) jsou nejvyšší provádění řádku procesor Intel E7v3 a E7v4.


### <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Spustit víc instancí SAP HANA na jednu jednotku velké Instance HANA
Je možné k hostování více než jeden aktivní instance SAP HANA na jednotkách HANA velké Instance. Pokud chcete zadat možnosti úložiště snímků a zotavení po havárii, taková konfigurace vyžaduje svazku nastavit na instanci. V současné době velké Instance HANA jednotky lze dále rozdělit takto:

- **S72, S72m, S144, S192**: V přírůstcích po 256 GB s 256 GB nejmenší počáteční jednotky. Maximum paměti jednotky lze spojovat různých intervalech například 256 GB a 512 GB.
- **S144m a S192m**: V přírůstcích po 256 GB s 512 GB nejmenší jednotky. Různé násobky například 512 GB a 768 lze spojovat na maximum paměti jednotky.
- **Zadejte třídu II**: V přírůstcích po 512 GB s nejmenší počáteční jednotky, 2 TB. Maximum paměti jednotky lze spojovat různých intervalech například 512 GB, 1 TB a 1,5 TB.

Některé příklady spuštěním několika instancí SAP HANA může vypadat následovně.

| Skladová jednotka (SKU) | Velikost paměti | Velikost úložiště | Velikosti s více databází |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | instance HANA 1 × 768 GB<br /> nebo instance 1 × 512 GB + 1 × 256 GB instance<br /> nebo instance 3 x 256 GB | 
| S72m | 1,5 TB | 6 TB | instance 3x512GB HANA<br />nebo instance 1 × 512 GB + 1 × 1 TB instance<br />nebo instance 6 x 256 GB<br />nebo instance 1x1.5 TB | 
| S192m | 4 TB | 16 TB | instance 8 x 512 GB<br />nebo instance 4 x 1 TB<br />nebo instancí 4 x 512 GB + 2 × 1 TB instancí<br />nebo instancí 4 x 768 GB + instancí 2 x 512 GB<br />nebo instance 1 × 4 TB |
| S384xm | 8 TB | 22 TB | instance 4 x 2 TB<br />nebo instancí 2 x 4 TB<br />nebo instancí 2 × 3 TB + 1 × 2 TB instancí<br />nebo instancí 2x2.5 TB + 1 × 3 TB instancí<br />nebo instance. 1 x 8 TB |


Existují také další variace. 

### <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Použít uzly vrstvení a rozšíření dat SAP HANA
SAP podporuje vrstvení datový model pro SAP BW různé verze SAP NetWeaver a SAP BW/4HANA. Další informace o vrstvení datový model, naleznete v dokumentu SAP [SAP BW/4HANA a SAP BW na HANA s uzly rozšíření SAP HANA](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
S HANA velké instancí můžete použít možnost 1 konfigurace uzlů rozšíření SAP HANA jak je popsáno v blogu dokumenty – nejčastější dotazy a SAP. Možnost 2 konfigurace se dá nastavit pomocí následujících HANA velké Instance SKU: S72m, S192, S192m, S384 a S384m. 

Když se podíváte na dokumentaci, výhoda se nemusí zobrazovat okamžitě. Ale když se podíváte na pokyny pro nastavení velikosti SAP, uvidíte několik výhod pomocí možnosti-1 a 2 možnost rozšíření uzly SAP HANA. Zde jsou příklady:

- Pokyny k nastavení velikosti SAP HANA obvykle vyžadují dvojnásobek datový svazek jako paměť. Při spuštění vaší instance SAP HANA s aktivní data, máte jenom 50 procent nebo méně paměti, naplní se data. Zbývající paměti je v ideálním případě uchovávat pro SAP HANA provádění své práce.
- Znamená, že v jednotce HANA velké Instance S192 s 2 TB paměti, systémem databázi SAP BW máte pouze 1 TB jako datový svazek.
- Pokud používáte dalšího uzlu Rozšíření SAP HANA možnost-1, i S192 HANA velké Instance SKU, nabízí další kapacitu 2 TB pro datový svazek. V konfiguraci možnost 2 získáte další 4 TB pro záložním datový svazek. Ve srovnání s aktivního uzlu, kapacita paměti úplné "záložním" rozšíření uzlu lze použít pro ukládání dat pro možnost-1. Double paměti lze použít pro datový svazek v konfiguraci uzlu Rozšíření SAP HANA možnost-2.
- Skončili se s kapacitou 3 TB pro vaše data a hot záložním poměr 1:2 pro možnost-1. Máte 5 TB dat a 1:4 poměr konfigurace uzlu Rozšíření možnost-2.

Čím datový svazek ve srovnání s paměť, tím vyšší pravděpodobnost jsou uložení záložním data, která se žádostí o na místo na disku.


## <a name="operations-model-and-responsibilities"></a>Operace modelu a odpovědnosti

Služba součástí SAP HANA v Azure (velké instance) je zarovnán služby Azure IaaS. Zobrazí instance velké instance HANA s nainstalovaným operačním systémem, která je optimalizovaná pro SAP HANA. Jako u virtuálních počítačů Azure IaaS většinu úloh posílení zabezpečení operačního systému, instalace dalšího softwaru, instalaci HANA, operační operačního systému a HANA a aktualizaci operačního systému a HANA je vaší povinností. Microsoft nebude v můžete vynutit aktualizace operačního systému, nebo HANA.

![Odpovědnosti SAP HANA v Azure (velké instance)](./media/hana-overview-architecture/image2-responsibilities.png)

Jak je vidět v diagramu, SAP HANA v Azure (velké instance) je více klientů, které nabízejí IaaS. Ve většině případů je dělení zodpovědnosti na hranici infrastruktury operačního systému. Microsoft je zodpovědná za všechny aspekty služby pod čarou operačního systému. Jste zodpovědní za všechny aspekty služby výše na řádku. Operačního systému je vaší povinností. Můžete použít nejaktuálnější místní metod, kterou může využít pro dodržování předpisů, zabezpečení, správy aplikací, základ a operačního systému správy. V systémech se zobrazí jako, pokud jsou ve vaší síti všechny namapoval.

Tato služba je optimalizovaná pro SAP HANA, existují oblasti, které potřebujete k práci s společnost Microsoft k použití možnosti základní infrastruktury pro dosažení co nejlepších výsledků.

Následující seznam uvádí další podrobnosti o vrstvy a vaše odpovědnosti:

**Sítě**: všechny interní sítě pro razítko velké Instance systémem SAP HANA. Vaše odpovědnosti patří přístup k úložišti, připojení mezi instancemi (pro Škálováním na více systémů a další funkce), připojení k povahu a připojení k Azure je hostitelem aplikační vrstvu SAP ve virtuálních počítačích. Zahrnuje také připojení k síti WAN mezi datovými centry Azure pro replikaci pro účely obnovení po havárii. Všechny sítě jsou oddíly prostřednictvím klienta a mít kvalitu služby, které jsou použity.

**Úložiště**: virtualizovaného oddíly úložiště pro všechny svazky, které jsou potřebné SAP HANA servery, a také pro snímky. 

**Servery**: vyhrazených fyzických serverů ke spuštění databáze SAP HANA, přiřazen klientům. Servery typu I třídy SKU jsou abstrahované hardwaru. Konfigurace serveru s těmito typy serverů se shromažďují a udržuje na profily, které můžou přesouvat z jednoho fyzického hardwaru na jiný fyzický hardware. Takové (ruční) přesunu profilu operacemi je možné charakterizovat trochu opravy služby Azure. Servery SKU – třída typu II nemáte nabízí takové funkce.

**SDDC**: software pro správu, který se používá ke správě dat soustředí jako softwarově definované entity. Ho fond zdrojů pro škálování, dostupnosti a z důvodů výkonu umožňuje společnosti Microsoft.

**Podporované OS**: zvolíte (SUSE Linux nebo Red Hat Linux) operačního systému na serverech se systémem. Bitové kopie operačního systému, který se dodává s byly poskytnutý dodavatelem pro jednotlivé Linux společnosti Microsoft pro spuštění SAP HANA. Musíte mít předplatné s dodavatelem Linux pro tento konkrétní obrázek optimalizované SAP HANA. Jste zodpovědní za registraci bitové kopie operačního systému dodavatele. 

Z bodu předání společností Microsoft a jste zodpovědní za jakékoli další opravy operačního systému Linux. Tato opravy zahrnuje další balíčky, které může být nezbytné pro úspěšnou instalaci SAP HANA a které nejsou zahrnuté dodavatelem konkrétní Linux v jejich SAP HANA optimalizované bitové kopie operačního systému. (Další informace najdete v na SAP HANA instalace dokumentace a poznámky SAP.) 

Jste zodpovědní za použití dílčích oprav operačního systému kvůli selhání nebo optimalizace operačního systému a jeho ovladače relativně ke konkrétní serverový hardware. Také jste zodpovědní za zabezpečení nebo funkční opravy operačního systému. 

Vaše odpovědnosti také zahrnuje monitorování a plánování kapacity:

- Využití prostředků procesoru.
- Využití paměti.
- Diskové svazky související s volného místa, IOPS a latenci.
- Svazek síťový provoz mezi HANA velké Instance a aplikační vrstvu SAP.

Základní infrastruktura velké instance HANA poskytuje funkce pro zálohování a obnovení svazku operačního systému. Pomocí této funkce je také vaší povinností.

**Middleware**: SAP HANA především Instance. Správu, operace a monitorování jsou vaše zodpovědnosti. Zadaná funkce můžete použít snímky úložiště pro zálohování a obnovení a po havárii pro účely obnovení. Tyto možnosti jsou poskytovány infrastruktury. Vaše odpovědnosti také zahrnovat navrhování vysoké dostupnosti nebo zotavení po havárii s těmito možnostmi monitorování a jejich využití k určení, zda snímky úložiště byl úspěšně proveden.

**Data**: vaše data spravovaná přes SAP HANA a další data, jako je zálohování sdílí soubory umístěné na svazcích nebo souboru. Vaše odpovědnosti zahrnují monitorování volného místa na disku a správu obsahu na svazcích. Také jste zodpovědní za monitorování úspěšné provedení zálohy diskové svazky a úložiště snímků. Úspěšné provedení replikaci dat do lokalit pro zotavení po havárii zodpovídá Microsoft.

**Aplikace:** instancí aplikace SAP nebo v případě jiných SAP aplikace, aplikační vrstvu těchto aplikací. Vaše odpovědnosti zahrnují nasazení, správu, operace a monitorování těchto aplikací. Jste zodpovědní za plánování kapacity využití prostředků procesoru, využití paměti, využívání Azure Storage a spotřeba šířky pásma sítě v rámci virtuální sítě. Také jste zodpovědní za plánování kapacity pro spotřeby prostředků z virtuální sítě k SAP HANA v Azure (velké instance).

**WAN**: připojení je vytvořit z místního nasazení Azure pro úlohy. Všem zákazníkům s instancí velké HANA použijte Azure ExpressRoute pro připojení k síti. Toto připojení není součástí SAP HANA na řešení Azure (velké instance). Jste zodpovědní za nastavení pro toto připojení.

**Archiv**: Chcete archivovat kopií dat pomocí vlastních metod v účtech úložiště. Archivace vyžaduje správu, dodržování předpisů, náklady a operace. Jste zodpovědní za generování archivu kopií a záloh v Azure a uložit je do kompatibilní způsobem.

Najdete v článku [SLA pro SAP HANA v Azure (velké instance)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/).

## <a name="sizing"></a>Velikost

Nastavení velikosti pro velké instanci HANA je nejsou jiné než dimenzování pro HANA obecně. U stávajících a nasadit systémy, které chcete přesunout z jiných RDBMS do HANA, SAP poskytuje řadu sestav, které běží na vaše stávající systémy SAP. Pokud databázi přesunete do HANA, tyto sestavy zkontrolujte data a výpočet požadavků na paměť pro instanci HANA. Další informace o tom, jak spustit tyto sestavy a získat jejich nejnovější opravy nebo verze přečtěte si následující poznámky k SAP:

- [Poznámka SAP #1793345 - dimenzování pro sadě SAP HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [Poznámka: #1872170 - Suite na velikosti sestavy HANA a S/4 HANA SAP](https://launchpad.support.sap.com/#/notes/1872170)
- [Poznámka SAP #2121330 – nejčastější dotazy: SAP BW na HANA Změna velikosti sestav](https://launchpad.support.sap.com/#/notes/2121330)
- [Poznámka: #1736976 - sestavu nastavení velikosti pro BW HANA SAP](https://launchpad.support.sap.com/#/notes/1736976)
- [Poznámka: #2296290 - dimenzování nové sestavy pro BW na HANA SAP](https://launchpad.support.sap.com/#/notes/2296290)

Pro implementace zelená pole je k dispozici vypočítat požadavky na paměť implementace SAP softwaru nad HANA SAP rychlé přizpůsobení velikosti symbolů.

Požadavky na paměť pro HANA zvýšit s růstem datový svazek. Uvědomte si vaše aktuální využití paměti při předpovědi, co se má být v budoucnosti. Podle požadavky na paměť, potom můžete namapovat vaše vyžádání do jedné Instance SKU velké HANA.

## <a name="requirements"></a>Požadavky

Tento seznam sestaví požadavky na spuštění SAP HANA v Azure (větší instance).

**Microsoft Azure**

- Předplatné Azure, který může být propojený SAP HANA v Azure (velké instance).
- Kontrakt Microsoft Premier support. Konkrétní informace související se spouštěním SAP v Azure najdete v tématu [SAP podporu Poznámka #2015553 – SAP v Microsoft Azure: podporovat požadavky](https://launchpad.support.sap.com/#/notes/2015553). Pokud použijete instanci HANA velké jednotky s 384 a více procesorů, musíte taky rozšířit prvotřídní podporu kontrakt zahrnout rychlé odpovědi Azure.
- Sledování HANA velké Instance SKU je nutné po provedení nastavení velikosti cvičení s SAP.

**Připojení k síti**

- ExpressRoute mezi místním nasazením a Azure: připojení místní datové centrum Azure, nezapomeňte uspořádat alespoň 1 GB/s připojení od vašeho poskytovatele internetových služeb. 

**Operační systém**

- Licence pro SUSE Linux Enterprise Server 12 pro SAP aplikace.

   > [!NOTE] 
   > Operační systém dodaných společností Microsoft není registrován u SUSE. Není připojen k instanci nástroj pro správu předplatného.

- SUSE Linux předplatné nástroj pro správu nasazené v Azure na virtuálním počítači. Tento nástroj poskytuje možnost pro SAP HANA v Azure (velké instance) pro zápis a SUSE aktualizován v uvedeném pořadí. (Není žádný přístup k Internetu v rámci datového centra velké Instance HANA.) 
- Licence pro Red Hat Enterprise Linux 6.7 nebo 7.2 pro SAP HANA.

   > [!NOTE]
   > Operační systém dodaných společností Microsoft není registrován u Red Hat. Není připojen k instanci Red Hat odběr Manager.

- Red Hat odběr Manager nasazené v Azure na virtuálním počítači. Správce předplatného Red Hat poskytuje možnost pro SAP HANA v Azure (velké instance) pro zápis a Red Hat aktualizován v uvedeném pořadí. (Není žádný přímý přístup k Internetu z v rámci klienta nasazené na značce velké instanci Azure.)
- SAP vyžaduje, abyste měli podporu smlouvy s poskytovatelem Linux. Tento požadavek se neodebere řešení pro velká Instance HANA nebo fakt spuštění Linux v Azure. Na rozdíl od pomocí některé z Galerie obrázků Linux Azure, služby poplatek je *není* součástí nabídku řešení HANA velké instance. Je vaší povinností splnit požadavky SAP o smlouvách, podporu s distributorem Linux. 
   - Pro systémy SUSE Linux vyhledat požadavky na podporu smluv, [SAP Poznámka #1984787 - SUSE Linux Enterprise Server 12: poznámky k instalaci](https://launchpad.support.sap.com/#/notes/1984787) a [SAP Poznámka #1056161 - SUSE priority nepodporují pro aplikace SAP](https://launchpad.support.sap.com/#/notes/1056161).
   - Red Hat Linux potřebujete úrovněmi správné předplatné, které zahrnují podporu a služby aktualizace pro operační systémy velkých instance HANA. Red Hat [SAP řešení] doporučuje Red Hat Enterprise Linux (https://access.redhat.com/solutions/3082481 předplatné. 

Na matici podpory různých verzí SAP HANA s různými verzemi systému Linux, najdete v části [2235581 # Poznámka SAP](https://launchpad.support.sap.com/#/notes/2235581).

Matice kompatibility operačního systému a verze firmwaru a ovladače HLI, najdete v části [Upgrade operačního systému pro HLI](os-upgrade-hana-large-instance.md).


**Database**

- Licence a součásti instalace softwaru pro SAP HANA (platforma nebo enterprise edition).

**Aplikace**

- Licence a softwarové součásti od instalace pro všechny aplikace SAP, která se připojují k SAP HANA a související SAP podporovat smluv.
- Licence a softwarové součásti od instalace pro všechny aplikace bez SAP používá ve vztahu k SAP HANA v prostředí Azure (velké instance) a související smlouvy o podpoře.

**Dovednosti**

- Zkušenosti s a znalosti o Azure IaaS a jeho komponenty.
- Zkušenosti s a znalosti o tom, jak nasadit SAP zatížení v Azure.
- Instalace SAP HANA certifikované pracovníky.
- SAP architekti dovednosti pro návrh vysoké dostupnosti a zotavení po havárii kolem SAP HANA.

**SAP**

- Očekává se, že jste SAP zákazníka a mají podporu kontrakt s SAP.
- Zejména pro implementace od třídy typu II HANA velké Instance SKU poraďte se s SAP ve verzích systému SAP HANA a závěrečné konfigurace na velkých a velkých hardwaru škálování.


## <a name="storage"></a>Úložiště

Rozložení úložiště pro SAP HANA v Azure (velké instance) ke konfiguraci pomocí SAP HANA v modelu nasazení classic prostřednictvím SAP Doporučené pokyny. Pokyny jsou dokumentovány v článku [požadavky na úložiště SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) dokumentu white paper.

HANA velké instanci typu I třídy se dodává s čtyřikrát paměti svazek jako svazek úložiště. Pro třídu II typ jednotek HANA velké Instance není čtyřikrát další úložiště. Jednotky se dodávají s svazku, který je určený pro ukládání HANA zálohování transakčního protokolu. Další informace najdete v tématu [nainstalovat a nakonfigurovat SAP HANA (velké instance) na Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Viz následující tabulka uvádí přidělení úložiště. V tabulce jsou uvedeny hrubý kapacity pro jiné svazky zadaná u jiné Instance velké HANA jednotek.

| HANA velké Instance SKU | Hana nebo dat | Hana a protokolování | / sdílené Hana | Hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3,328 GB | 768 GB |1,280 GB | 768 GB |
| S192 | 4 608 GB | 1024 GB | 1 536 GB | 1024 GB |
| S192m | 11,520 GB | 1 536 GB | 1,792 GB | 1 536 GB |
| S192xm |  12 000 GB |  2 050 GB |  2 050 GB |  2.040 GB |
| S384 | 11,520 GB | 1 536 GB | 1,792 GB | 1 536 GB |
| S384m | 12 000 GB | 2 050 GB | 2 050 GB | 2,040 GB |
| S384xm | 16 000 GB | 2 050 GB | 2 050 GB | 2,040 GB |
| S384xxm |  20 000 GB | 3,100 GB | 2 050 GB | 3,100 GB |
| S576m | 20 000 GB | 3,100 GB | 2 050 GB | 3,100 GB |
| S576xm | 31,744 GB | 4 096 GB | HODNOTU 2 048 GB | 4 096 GB |
| S768m | 28,000 GB | 3,100 GB | 2 050 GB | 3,100 GB |
| S768xm | 40 960 GB | 6,144 GB | 4 096 GB | 6,144 GB |
| S960m | 36,000 GB | 4,100 GB | 2 050 GB | 4,100 GB |


Skutečné nasazené svazky se může lišit v závislosti na nasazení a nástroje, který se používá k zobrazení velikosti svazků.

Pokud jste rozdělit HANA velké Instance SKU, může vypadat několik příkladů možné dělení částí:

| Oddíl paměti v GB | Hana nebo dat | Hana a protokolování | / sdílené Hana | Hana/log/zálohování |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1,792 GB | 640 GB | 1024 GB | 640 GB |
| 1 536 | 3,328 GB | 768 GB | 1,280 GB | 768 GB |


Tyto velikosti jsou čísla hrubý svazku, která mírně založené na nasazení a nástroje používané ke podívejte se na svazky se může lišit. Existují také další velikostí oddílu, jako je například 2,5 TB. Velikosti úložiště se počítá pomocí vzorce podobné použita pro předchozí oddíly. Termín "oddíly" neznamená, že operační systém, paměť nebo prostředky procesoru se žádným způsobem rozdělena na oddíly. Označuje oddílů pro úložiště pro různé HANA instance, které chcete nasadit na jedna jednotka HANA velké Instance. 

Můžete potřebovat další úložiště. Úložiště můžete přidat nákupu dalšího úložiště v jednotkách 1 TB. Toto dodatečné úložiště se dá přidat jako další svazek. Také může sloužit k rozšíření jeden nebo více existujících svazcích. Není možné snížit velikost svazků původně nasazení a většinou zdokumentovat, předchozích tabulkách. Také není možné změnit názvy svazků nebo se připojte názvy. Svazky úložiště výše popsané jsou připojené k instanci HANA velké jednotky jako NFS4 svazky.

Úložiště snímků můžete použít pro zálohování a obnovení a po havárii pro účely obnovení. Další informace najdete v tématu [SAP HANA (velké instance) vysoké dostupnosti a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="encryption-of-data-at-rest"></a>Šifrování neaktivních uložených dat
Úložiště použitého pro velké Instance HANA umožňuje transparentní šifrování dat, který je uložen na discích. Při nasazení Instance HANA velké jednotky, můžete povolit tento druh šifrování. Také můžete změnit na svazky šifrované po nasazení probíhá. Přesunutí ze bez šifrování šifrovaných svazcích je transparentní a nevyžaduje výpadek. 

S typem I třídy z SKU, je na svazku spouštěcí logická jednotka je uložený na šifrování. Pro třídu typu II instance SKU z HANA velké budete muset šifrování spouštění logické jednotky s metody operačního systému. Další informace kontaktujte tým Microsoft Service Management.


## <a name="networking"></a>Sítě

Architektura služby síť Azure je klíčovou součástí úspěšné nasazení aplikace SAP HANA velké instance. SAP HANA na nasazení Azure (velké instance) obvykle mají větší šířku SAP v několika různých řešeních pro SAP s různou velikost databáze, využití prostředků procesoru a využití paměti. Je pravděpodobné, že ne všechny tyto systémy SAP jsou založeny na SAP HANA. Vaše šířku SAP je pravděpodobně hybridní, který používá:

- Nasazení SAP systémy místně. Z důvodu jeho velikosti tyto systémy aktuálně nemůže být hostovaná v Azure. Příkladem je provozní systému SAP ERP, který běží v systému SQL Server (jako databáze) a vyžaduje další prostředky procesoru nebo paměti než může poskytnout virtuálních počítačů.
- Nasazení na základě SAP HANA SAP systémy místně.
- Nasazené SAP systémy ve virtuálních počítačích. Tyto systémy může být vývoj, testování, izolovaného prostoru, nebo produkční instance pro všechny aplikace na základě SAP NetWeaver, které můžete úspěšně nasazovat v Azure (na virtuálních počítačích), na základě poptávky prostředků využívání a paměti. Tyto systémy můžou být taky založené na databáze, jako je SQL Server. Další informace najdete v tématu [SAP podporu Poznámka #1928533 – SAP aplikace v Azure: podporované produkty a virtuální počítač Azure typy](https://launchpad.support.sap.com/#/notes/1928533/E). A tyto systémy může být založen na databáze například SAP HANA. Další informace najdete v tématu [SAP HANA certifikované platformy IaaS](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).
- Nasazení SAP aplikační servery v Azure (na virtuálních počítačích), které využívají SAP HANA v Azure (velké instance) v Azure velké Instance razítka.

Hybridní SAP šířku se čtyřmi nebo více různých scénářů nasazení je typické. Existují také mnoho zákazníků případů dokončení krajiny SAP, které běží v Azure. Virtuální počítače jsou účinnější, zvyšuje počet zákazníků, které přepínají jejich SAP řešení v Azure.

Azure sítě v kontextu systému SAP nasazené v Azure není složité. Je založena na následující zásady:

- Virtuální sítě Azure musí být připojen k okruhu ExpressRoute, která se připojuje k místní síti.
- Okruh ExpressRoute, který se připojuje místní obvykle do Azure by měl mít šířky pásma s 1 GB/s nebo vyšší. Tato minimální šířku pásma umožňuje odpovídající šířku pásma pro přenos dat mezi místní a systémy, které běží na virtuálních počítačích. Umožňuje také odpovídající šířku pásma pro připojení k systémům Azure z místních uživatelů.
- Všechny systémy SAP v Azure musí být nastavené v virtuální sítě pro komunikaci mezi sebou.
- Active Directory a DNS hostované na místě jsou rozšířené do Azure prostřednictvím ExpressRoute z místní.


> [!NOTE] 
> Z fakturace hlediska může být propojený jenom jedno předplatné pouze jeden klienta v velké Instance razítka v určité oblasti Azure. Naopak jednoho klienta razítko velké Instance může být propojený jenom jedno předplatné Azure. Tento požadavek je konzistentní s jinými fakturovatelný objekty v Azure.

Pokud nasazení SAP HANA v Azure (velké instance) v několika různých oblastech Azure samostatné klienta je nasazena v velké Instance razítka. Můžete spustit i v rámci stejného předplatného Azure, dokud tyto instance jsou součástí stejné šířku SAP. 

> [!IMPORTANT] 
> SAP HANA v Azure (velké instance) podporuje pouze nasazení Azure Resource Manager.

 

### <a name="additional-virtual-network-information"></a>Informace o další virtuální síti

Chcete-li připojit virtuální síť expressroute, musí být vytvořeny bránu Azure. Další informace najdete v tématu [o brány virtuální sítě pro ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). 

Služba Azure gateway lze použít s ExpressRoute infrastruktury mimo Azure nebo s Azure velké Instance razítka. Služba Azure gateway můžete také použít pro připojení mezi virtuálními sítěmi. Další informace najdete v tématu [konfigurace pro správce prostředků do síťového připojení pomocí prostředí PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Tak dlouho, dokud tato připojení pocházejí z různých Microsoft enterprise hraniční směrovače se můžete připojit bránu Azure až na čtyři různá připojení ExpressRoute. Další informace najdete v tématu [infrastruktury SAP HANA (velké instance) a připojení v Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Propustnost, které poskytuje služba Azure gateway se liší pro oba případy použití. Další informace najdete v tématu [o službě VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Maximální propustnost, kterou můžete dosáhnout s bránou virtuální sítě je 10 GB/s pomocí připojení ExpressRoute. Kopírování souborů mezi virtuální počítač, který se nachází ve virtuální síti a systému místní (jako jedna kopie datový proud) není dosáhnout úplnou propustnost různých SKU brány. Chcete-li využívat šířku pásma dokončení bránu virtuální sítě, použijte víc datových proudů. Nebo musíte zkopírovat různých souborů v paralelní datové proudy jednoho souboru.


### <a name="networking-architecture-for-hana-large-instance"></a>Architektura sítě pro instanci velké HANA
Sítě architekturu pro velké instanci HANA lze rozdělit do čtyř různých částí:

- Místní sítě a připojení ExpressRoute do Azure. Tato část je zákazníka doména, která je připojena k Azure prostřednictvím ExpressRoute. Najdete v pravém dolním na následujícím obrázku.
- Azure síťových služeb, dřív popsané s virtuálními sítěmi, které jsou znovu brány. Tato část je oblast, kde je potřeba najít odpovídající návrhy pro vaše požadavky na aplikace, zabezpečení a požadavky na dodržování předpisů. Jestli používáte velké instanci HANA je jiný vzít v úvahu z hlediska počtu virtuálních sítí a Azure SKU brány lze vybírat. Najdete v pravém horním rohu na obrázku.
- Připojení velké Instance HANA prostřednictvím ExpressRoute technologie do Azure. Tato část nasazení a postará Microsoft. Všechny, které je potřeba je zadat některé rozsahy IP adres po nasazení vaše prostředky v instanci velké HANA připojit k virtuálním sítím okruh ExpressRoute. Další informace najdete v tématu [infrastruktury SAP HANA (velké instance) a připojení v Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- Sítě v HANA velké Instance, který je ve většině případů transparentní pro vás.

![Virtuální síť připojená k SAP HANA na Azure (velké instance) a na místě](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Požadavek na vaše místní prostředky musí připojit prostřednictvím ExpressRoute do Azure nepodporuje změnit, protože používáte HANA velké instanci. Požadavek na jednu nebo více virtuálních sítí, spuštěné virtuální počítače, které jsou hostiteli aplikační vrstvu, která se připojuje k instancím HANA hostovaná v instanci HANA velké jednotky, máte také nemění. 

Rozdíly pro nasazení SAP v Azure jsou:

- Instance HANA velké jednotky vašeho zákazníka klienta jsou připojené prostřednictvím jiného okruhu ExpressRoute do virtuálních sítí. K oddělení zatížení, Nesdílejte místní do virtuální sítě ExpressRoute a odkazy mezi virtuálními sítěmi a HANA velké Instance stejné směrovače.
- Profil zatížení mezi SAP aplikační vrstvu a velké Instance HANA je i jiná, s mnoho malých požadavků a shluky jako přenosu dat (sady výsledků) z SAP HANA do aplikační vrstvu.
- Architektura aplikace SAP je více citlivá na latenci sítě než typické scénáře, kde data se vyměňují mezi místními a Azure.
- Brána virtuální sítě má alespoň dvě připojení ExpressRoute. Obě připojení sdílet maximální šířka pásma pro příchozí data bránu virtuální sítě.

Latence sítě došlo mezi virtuálními počítači a velký Instance HANA jednotky může být vyšší než typické latence operace round-trip sítě virtuálních počítačů VM. Závisí na oblast Azure, hodnoty měřenou může překročit odezvy latence 0,7 ms jsou klasifikovány jako níže průměr v [SAP Poznámka #1100926 – nejčastější dotazy: výkon sítě](https://launchpad.support.sap.com/#/notes/1100926/E). Nicméně zákazníci nasadit na základě SAP HANA výrobní aplikace SAP úspěšně na velké Instance SAP HANA. Zákazníci, kteří nasazenou skvělé vylepšení sestavy spuštěním svých aplikací SAP na SAP HANA pomocí Instance HANA velké jednotky. Zajistěte, aby že důkladně otestovat své obchodní procesy v Azure HANA velké Instance.
 
Zajistit latence deterministický sítě mezi virtuálními počítači a velký Instance HANA volba bránu virtuální sítě SKU je nezbytné. Na rozdíl od vzory přenosů dat mezi místní a virtuální počítače můžete vyvíjet vzoru provoz mezi virtuálními počítači a velký Instance HANA malé ale vysoké shluky požadavky a datové svazky přenášet. Zpracovat takové shluky dobře, důrazně doporučujeme použít UltraPerformance SKU brány. Pro třídu typu II HANA velké Instance SKU použití skladová položka brány UltraPerformance jako brána virtuální sítě je povinné.

> [!IMPORTANT] 
> Zadaný celkový provoz sítě mezi databáze vrstvy a aplikaci SAP, HighPerformance nebo UltraPerformance SKU brány pro virtuální sítě jsou podporovány pro připojení k SAP HANA v Azure (velké instance). Pro HANA velké Instance typu II SKU je podporováno pouze UltraPerformance SKU brány jako brána virtuální sítě.



### <a name="single-sap-system"></a>Jednoho systému SAP

Místní infrastruktura dříve vidět je připojené prostřednictvím ExpressRoute do Azure. Okruh ExpressRoute připojí do hraniční směrovač podniku. Další informace najdete v tématu [technický přehled ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Po navázání trasy, která se připojuje do páteřní strukturu Azure a jsou dostupné všechny oblasti Azure.

> [!NOTE] 
> SAP krajiny spustit v Azure, připojí k hraniční směrovač podniku nejbližší oblast Azure z povahu SAP. Azure velké Instance razítka jsou propojeny pomocí vyhrazené podnikové hraniční směrovač zařízení k minimalizaci latence sítě mezi virtuálními počítači v Azure IaaS a velké Instance razítka.

Brána virtuální sítě pro virtuální počítače, které hostují instance aplikace SAP je připojená k okruhu ExpressRoute. Stejné virtuální síti je připojený k hraniční směrovač vyhrazený pro připojení k velké Instance razítka samostatné podniku.

Tento systém je jednoduchý příklad jednoho systému SAP. Aplikační vrstvu SAP je hostován v Azure. Databázi SAP HANA běží na SAP HANA v Azure (velké instance). Předpokladem je, že šířku pásma brány virtuální sítě 2 GB/s nebo 10 GB/s propustností nepředstavuje úzkým místem.

### <a name="multiple-sap-systems-or-large-sap-systems"></a>Více systémů SAP nebo velké systémy SAP

Pokud pro připojení k SAP HANA v Azure (velké instance) se nasazuje víc systémů SAP nebo velké systémy SAP, propustnost brány virtuální sítě může stát úzkým místem. V takovém případě rozdělte aplikace vrstvy na více virtuálních sítí. Můžete také vytvořit speciální virtuální síť, která se připojí k instanci velké HANA případech, jako například:

- Provádění zálohování přímo z instancí HANA v instanci velké HANA k virtuálnímu počítači v Azure, který je hostitelem sdílených složek NFS.
- Kopírování velkých zálohy nebo další soubory z Instance HANA velké jednotky na spravovat v Azure místo na disku.

Použijte samostatné virtuální sítě pro hostitele virtuálních počítačů, které správu úložiště. Toto uspořádání zabraňuje účinky velkých souborů nebo přenos dat z velké Instance HANA do Azure na bráně virtuální sítě, která má virtuální počítače, které spustit aplikační vrstvu SAP. 

Pro více škálovatelná architektura sítě:

- Využívejte více virtuálních sítí pro jeden, větší SAP aplikační vrstvu.
- Nasaďte jeden samostatný virtuální sítě u každého systému SAP nasazení ve srovnání s kombinace těchto systémů SAP v oddělené podsítě v rámci stejné virtuální síti.

 Větší škálovatelnost sítě architektura pro SAP HANA v Azure (velké instance):

![Nasazení SAP aplikační vrstvu přes více virtuálních sítí](./media/hana-overview-architecture/image4-networking-architecture.png)

Obrázek zobrazuje SAP aplikační vrstvu nebo součásti, které jsou nasazené v více virtuálních sítí. Tato konfigurace se zavedl nevyhnutelné latence režie, které došlo při komunikaci mezi aplikacemi hostované v těchto virtuálních sítí. Ve výchozím nastavení síťový provoz mezi virtuálními počítači, které jsou umístěné v různých virtuálních sítích směrovat přes hraniční směrovače enterprise v této konfiguraci. Od září 2016 se dají optimalizovat tento směrování. 

Způsob, jak optimalizovat a omezit latence při komunikaci mezi dvěma virtuálními sítěmi je partnerského vztahu virtuálních sítí ve stejné oblasti. Tato metoda funguje, i když tyto virtuální sítě jsou v různých předplatných. Partnerský vztah virtuální sítě, komunikace mezi virtuálními počítači ve dvou různých virtuálních sítích pomocí páteřní síti Azure přímou komunikaci mezi sebou. Latence ukazuje, jak je, pokud jsou virtuální počítače ve stejné virtuální síti. Provoz, který řeší rozsahy IP adres, které jsou připojené přes bránu virtuální sítě Azure je směrován přes bránu virtuální sítě jednotlivé virtuální sítě. 

Další informace o partnerském vztahu virtuální sítě najdete v tématu [partnerský vztah virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


### <a name="routing-in-azure"></a>Směrování v Azure

Tři aspekty směrování sítě jsou důležité pro SAP HANA v Azure (velké instance):

* SAP HANA v Azure (velké instance) lze přistupovat pouze prostřednictvím virtuálních počítačů a vyhrazené připojení ExpressRoute, ne přímo z místní. Přímý přístup z místního s jednotkami velké Instance HANA dodaných společností Microsoft, není možné okamžitě. Přenositelné směrování omezení jsou vzhledem k aktuální architektuře síť Azure pro velké Instance SAP HANA. Někteří klienti správy a všechny aplikace, které potřebují přímý přístup, jako je například SAP řešení správce spuštěného na místě, se nemůže připojit k databázi SAP HANA.

* Pokud máte velké Instance HANA jednotky nasazené ve dvou různých oblastech Azure pro zotavení po havárii, platí stejné omezení směrování přechodný. Jinými slovy IP adresy Instance HANA velké jednotky v jedné oblasti (například USA – západ) nejsou směrovány ke velké Instance HANA jednotka nasazení v jiné oblasti (například USA – východ). Toto omezení je nezávislé na použití sítě Azure partnerský vztah v oblastech nebo křížové připojení okruhy ExpressRoute, které se připojují k virtuálním sítím Instance HANA velké jednotky. Pro grafické znázornění podívejte se na obrázek v části "Použití HANA velké Instance jednotek v několika oblastech." Toto omezení, která se dodává s nasazené architekturu, zakáže okamžitou použití replikace systému HANA jako funkci obnovení po havárii.

* SAP HANA na jednotkách Azure (velké instance) mají přiřazenou IP adresu z rozsahu adres fondu IP serveru, který jste odeslali. Další informace najdete v tématu [infrastruktury SAP HANA (velké instance) a připojení v Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Tato IP adresa je přístupná prostřednictvím předplatných Azure a ExpressRoute, která se připojuje virtuální sítě k HANA v Azure (velké instance). Přidělit IP adresu mimo, je rozsah adres fondu IP serveru přímo přiřazena k jednotce hardwaru. Má *není* přiřazené prostřednictvím NAT už, stejně jako v případě v prvním nasazení tohoto řešení. 

> [!NOTE] 
> K překonání omezení v přechodný směrování, jak je popsáno v první dva seznam položek, použijte další součásti pro směrování. Součásti, které lze použít k překonání omezení může být:

> * Zpětného-proxy server pro směrování dat do a z. Například F5 BIG-IP, NGINX s Traffic Manager nasazené v Azure jako virtuální brány firewall nebo provoz směrování řešení.
> * Pomocí [IPTables pravidla](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) v virtuálního počítače s Linuxem umožňující směrování mezi místními umístěními a Instance HANA velké jednotky, nebo mezi jednotky HANA velké Instance v různých oblastech.

> Upozorňujeme, že implementace a podporu pro vlastní řešení zahrnující třetích stran síťových zařízení nebo IPTables není poskytované společností Microsoft. Podpora musí být poskytnuta dodavatele komponenta, kterou používá nebo integrátor. 

### <a name="internet-connectivity-of-hana-large-instance"></a>Připojení k Internetu velké instance HANA
Velké Instance HANA nemá *není* mít přímé připojení k Internetu. Jako příklad může toto omezení omezit na možnost registrovat bitovou kopii operačního systému přímo s dodavatelem operačního systému. Možná budete muset spolupracovat s vaší místní server SUSE Linux Enterprise předplatné nástroj pro správu serveru nebo Red Hat Enterprise Linux odběr Manager.

### <a name="data-encryption-between-vms-and-hana-large-instance"></a>Šifrování dat mezi virtuálními počítači a velký Instance HANA
Data přenesená mezi HANA velké Instance a virtuální počítače nejsou šifrována. Můžete však výhradně pro server exchange mezi straně HANA databázového systému a aplikací založeného na JDBC nebo rozhraní ODBC, povolit šifrování přenosů. Další informace najdete v tématu [této dokumentace podle SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

### <a name="use-hana-large-instance-units-in-multiple-regions"></a>Používat jednotky HANA velké Instance v několika oblastech

Můžete mít důvodů, proč nasazení SAP HANA v Azure (velké instance) v několika oblastech Azure jiný než pro zotavení po havárii. Možná budete chtít přístup HANA velké instanci ze všech virtuálních počítačích nasazených v různých virtuálních sítích v oblastech. IP adresy přiřazené k jiné jednotky velké Instance HANA nebudou rozšířeny nad rámec virtuální sítě, které jsou přímo připojené prostřednictvím jejich brány na instance. V důsledku toho byla zavedená mírné změnu na návrh virtuální sítě. Brána virtuální sítě může zpracovávat čtyři jiné okruhy ExpressRoute mimo jiné podnikové hraniční směrovače. Každý virtuální síť, která je připojena k jednomu z velké Instance razítka může být připojen k velké Instance razítka v jiné oblasti Azure.

![Virtuální síť připojená k Azure velké Instance razítka v různých oblastech Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

Obrázek ukazuje, jak různých virtuálních sítí v obou oblastí jsou připojené dva různé okruhy ExpressRoute, které se používají pro připojení k SAP HANA v Azure (velké instance) v obou oblastech Azure. Nově přináší připojení jsou obdélníková červené čáry. Tato připojení z virtuální sítě virtuální počítače spuštěné v jednom z těchto virtuálních sítí mohou mít přístup ke každé jiné jednotky velké Instance HANA nasazené ve dvou oblastech. Jak ukazuje obrázek, předpokládá se, že máte dvě připojení ExpressRoute z místního dvou oblastech Azure. Toto uspořádání se doporučuje důvodů obnovení po havárii.

> [!IMPORTANT] 
> Pokud jste použili více okruhů ExpressRoute, předřazení AS Path a místní BGP předvoleb nastavení slouží k zajištění správné směrování provozu.


