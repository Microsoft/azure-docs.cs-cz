---
title: Přehled a architektura SAP Hana v Azure (velké instance) | Dokumentace Microsoftu
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
ms.date: 08/27/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9d28b6ea5612a3db539c51d2603c3f12282ca519
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43090412"
---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>SAP HANA (velké instance) přehled a architektura v Azure

## <a name="what-is-sap-hana-on-azure-large-instances"></a>Co je SAP HANA v Azure (velké instance)?

SAP HANA v Azure (velké instance) je jedinečné řešení na Azure. Kromě toho, že virtuální počítače pro nasazování a spouštění SAP HANA, Azure vám nabízí možnost spouštět a nasazovat SAP HANA na holých serverů, které jsou vyhrazené. SAP HANA v Azure (velké instance) řešení je založena na nesdílený hostitele nebo server holého hardwaru, který je vám přiřazena. Hardware serveru je součástí větší razítek, které obsahují výpočty/server, sítí a infrastruktury úložiště. Jako kombinace je HANA přizpůsobené integrace System center data (TDI) certified. SAP HANA v Azure (velké instance) nabízí jiný server SKU nebo velikosti. Jednotky může mít 36 Intel Procesorových jader a o velikosti 768 GB paměti a přejděte do jednotky, které mají až 480 Intel Procesorových jader a až 24 TB paměti.

Izolace zákazníka v rámci razítka infrastruktury se provádí v tenantech, který bude vypadat takto:

- **Sítě**: izolace zákazníků v rámci zásobníku infrastruktury prostřednictvím virtuální sítě na tenantovi zákazníka, které jsou přiřazeny. Tenant se přiřadí pro jediného zákazníka. Zákazník může mít více tenantů. Izolace sítě tenantů zakazuje síťové komunikace mezi klienty na úrovni razítka infrastruktury i v případě, že klienti patřit do stejné zákazníka.
- **Úložiště komponent**: izolace prostřednictvím úložiště virtuálních počítačů, které mají svazky úložiště, které jsou jim přiřazeny. Svazky úložiště je přiřadit pouze jeden úložiště virtuálního počítače. Úložiště virtuálního počítače je exkluzivně přiřazená jedna jednoho tenanta v zásobníku certifikovaných infrastrukturu SAP HANA TDI. V důsledku toho svazky úložiště přiřazené k virtuálnímu počítači úložiště přístupné v jedné konkrétní a související tenanta pouze. Nejsou viditelné mezi různé nasazené tenanty.
- **Server nebo hostitele**: jednotka serveru nebo hostitele není sdílena mezi zákazníky nebo tenantů. Server nebo hostitele nasazený pro zákazníka, je jednotka atomic výpočetní úplné obnovení systému, který je přiřazen do jednoho jednoho tenanta. *Ne* dělení hardwarové nebo softwarové dělení se používá, který může způsobit sdílíte s odběratele jiného hostitele nebo server. Úložné svazky, které jsou přiřazeny k z konkrétního tenanta úložiště virtuálního počítače jsou připojené taková na server. Klient může mít jeden na mnoho server jednotek z různých skladových položkách exkluzivně přiřazená.
- V rámci SAP HANA na razítku infrastruktury Azure (velké instance) mnoho různých tenantů se nasazují a izolované proti sobě základními pojmy tenanta na sítě, úložiště a výpočetní úrovně. 


Tyto jednotky úplné obnovení systému server jsou podporovány pouze spuštění SAP HANA. Aplikační vrstvě SAP nebo úloh VMware střední vrstvy běží na virtuálních počítačích. Razítka infrastruktury, na kterých běží SAP HANA v Azure (velké instance) jednotky jsou připojené k páteřních služeb sítě Azure. Tímto způsobem je k dispozici připojení s nízkou latencí mezi virtuálními počítači a SAP HANA v Azure (velké instance) jednotky.

Tento dokument je jednou z několika dokumenty, které se týkají SAP HANA v Azure (velké instance). Toto téma představuje základní architekturu, zodpovědnostech a služby poskytované řešení. Základní možnosti řešení jsou popsány také. Většinu ostatních oblastech, jako je například sítě a připojení čtyři další dokumenty, které pokrývají podrobnosti a informace o procházení. Dokumentace k systému SAP HANA v Azure (velké instance) nezahrnuje aspekty instalace SAP NetWeaver nebo nasazení SAP NetWeaver na virtuálních počítačích. SAP NetWeaver v Azure je zahrnutých v samostatných dokumentech nalezen ve stejném kontejneru dokumentace k Azure. 


Jiné dokumenty velká Instance HANA pokyny zahrnují následující oblasti:

- [SAP HANA (velké instance) přehled a architektura v Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infrastrukturu SAP HANA (velké instance) a možnosti připojení v Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Instalace a konfigurace SAP HANA (velké instance) v Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (velké instance) vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Řešení potíží s SAP HANA (velké instance) a monitorování v Azure](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Vysoká dostupnost v SUSE lze nastavit pomocí využitím techniky STONITH](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [Operační systém zálohování a obnovení pro SKU typu II](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

## <a name="definitions"></a>Definice

Několik běžných definice se běžně používají v architektury a technické příručce pro nasazení. Mějte na paměti následující termíny a jejich význam:

- **IaaS**: infrastruktura jako služba.
- **PaaS**: platforma jako služba.
- **SaaS**: Software jako služba.
- **Komponenta SAP**: jednotlivé aplikace SAP, jako je ERP ústřední součást (ECC), Business Warehouse (BW), správci řešení nebo Enterprise Portal (RP). Komponenty SAP může být založen na tradičních technologií ABAP a Java nebo jiných NetWeaver na základě aplikace, například pro obchodní objekty.
- **Prostředí SAP**: jedna nebo více součástí SAP logicky seskupeny provádět obchodní funkce, jako je vývoj, kontrola kvality, školení, zotavení po havárii nebo produkčního prostředí.
- **Prostředí SAP**: odkazuje na celý prostředky SAP v prostředí vašeho IT. Prostředí SAP zahrnuje všechny produkčního prostředí a neprodukční prostředí.
- **Systém SAP**: kombinace DBMS vrstvy a aplikační vrstvě například vývoj systému SAP ERP, systém SAP BW testovací a produkční systém SAP CRM. Nasazení v Azure nepodporují dělení tyto dvě vrstvy mezi místními a Azure. Systém SAP je nasazená místně nebo je nasazené v Azure. Můžete nasadit různé systémy prostředí SAP do Azure nebo místně. Například můžete nasadit vývoj SAP CRM a systémů v Azure můžete testovat při nasazování SAP CRM produkční systém místní. Pro SAP HANA v Azure (velké instance) je určena hostujete aplikační vrstvě SAP systémů SAP na virtuálních počítačích a související instance SAP HANA na jednotce v SAP HANA v Azure (velké instance) razítko.
- **Velké Instance razítka**: zásobníku hardwarové infrastruktury, který je certifikací pro SAP HANA TDI a vyhrazených ke spuštění instance systému SAP HANA v Azure.
- **SAP HANA v Azure (velké instance):** oficiální název nabídky v Azure ke spuštění HANA instance na certifikací pro SAP HANA TDI hardwaru, který je nasazený v velká Instance razítka v různých oblastech Azure. Související výraz *velká Instance HANA* je zkratka pro *SAP HANA v Azure (velké instance)* a se běžně používá v této příručce technického nasazení.
- **Mezi různými místy**: popisuje scénář, ve které jsou nasazené virtuální počítače s předplatným Azure, který má připojení Azure ExpressRoute mezi místní datová centra a Azure, site-to-site a multi-Site. Dokumentace v běžných Azure a tyto druhy nasazení jsou také popsány jako scénáře mezi různými místy. Důvod pro připojení je k rozšíření místních domén, místní Azure Active Directory/OpenLDAP a místní DNS do Azure. V místním prostředí je rozšířit na prostředky Azure z předplatných Azure. U tohoto rozšíření virtuální počítač může být součástí místní domény. 

   Domény uživatele z místní domény můžete přístup k serverům a spustit služby na těchto virtuálních počítačů (například systému DBMS služby). Místně nasadili komunikace a název řešení mezi virtuálními počítači a virtuálních počítačů nasazených Azure je možné. Tento scénář je typický pro způsob, ve které jsou nasazené prostředky většina SAP. Další informace najdete v tématu [plánování a návrh pro Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a [vytvoření virtuální sítě s připojením site-to-site pomocí webu Azure portal](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Tenant**: zákazník nasazené v razítku velká Instance HANA načte samostatný do *tenanta.* Tenant je izolovaný v oblasti sítí, úložiště a výpočetní vrstvy od ostatních tenantů. Úložiště a výpočetní jednotky přiřazená různých tenantech nelze zobrazit mezi sebou ani vzájemně komunikovat na úrovni razítko velká Instance HANA. Zákazník se může rozhodnout pro nasazení do různých tenantech. Dokonce i pak neexistuje žádná komunikace mezi klienty na úrovni razítko velká Instance HANA.
- **Kategorie SKU**: pro velké Instance HANA, jsou k dispozici tyto dvě kategorie skladové položky:
    - **Typ třídy I**: S72 S72m, S144, S144m, S192, S192m a S192xm
    - **Zadejte třídu II**: S384 S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm a S960m


Různé další zdroje informací jsou k dispozici pro nasazení úloh SAP v cloudu. Pokud budete chtít provést nasazení SAP Hana v Azure, musíte být zkušenosti s a nezná zásady Azure IaaS a nasazení úloh SAP v Azure IaaS. Než budete pokračovat, naleznete v tématu [řešení použití SAP na virtuálních počítačích Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Další informace. 

## <a name="certification"></a>Certifikace

Kromě certifikační NetWeaver SAP vyžaduje speciální certifikaci pro SAP HANA a SAP HANA podporují určité infrastruktury, jako je Azure IaaS.

Je základní Poznámka SAP na systému NetWeaver a SAP HANA stupně certifikace [SAP Poznámka #1928533 – aplikace SAP v Azure: podporované produkty a typy virtuálních počítačů Azure](https://launchpad.support.sap.com/#/notes/1928533).

Záznamy certifikaci pro SAP HANA na jednotky Azure (velké instance) najdete v [platformách IaaS s certifikací SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) lokality. 

SAP HANA v Azure (velké instance) typů uvedených v SAP HANA s certifikací platformy IaaS lokality poskytuje Microsoft a SAP zákazníkům možnost nasazení velké SAP Business Suite, SAP BW, s/4 HANA, BW/4HANA nebo jiné úlohy SAP HANA v Azure. Toto řešení je založeno na razítku certifikovaných vyhrazený hardware SAP HANA ([SAP HANA přizpůsobená data center integrace – TDI](https://scn.sap.com/docs/DOC-63140)). Pokud spouštíte řešení SAP HANA TDI nakonfigurované, všechny aplikace pro systém SAP HANA (jako je SAP Business Suite v SAP HANA, SAP BW on SAP HANA S4/HANA a BW4/HANA) funguje na hardwarové infrastruktury.

Porovnání se spouštěním SAP HANA ve virtuálních počítačích, toto řešení má výhodu. Poskytuje větší objemy paměti. Pokud chcete povolit toto řešení, je potřeba pochopit následující klíčové aspekty:

- SAP aplikací vrstvy a mimo systém SAP aplikace běží ve virtuálních počítačích, které jsou hostované v razítka obvykle hardware Azure.
- Zákazník v místní infrastruktuře, datových center, a nasazení aplikací jsou připojené k platformě cloud prostřednictvím ExpressRoute (doporučeno) nebo virtuální privátní sítě (VPN). Služba Active Directory a DNS také se rozšíří do Azure.
- Instance databáze SAP HANA pro pracovní vytížení HANA běží na systému SAP HANA v Azure (velké instance). Velká Instance razítka je připojen do sítě Azure, aby software spuštěný na virtuálních počítačích můžete pracovat s instance HANA používané velká Instance HANA.
- Hardware systému SAP HANA v Azure (velké instance) je k dispozici v IaaS s operačním systémem SUSE Linux Enterprise Server nebo Red Hat Enterprise Linux a předinstalovaným vyhrazený hardware. Stejně jako u virtuálních počítačů, další aktualizace a údržba pro operační systém je na vás.
- Instalace HANA nebo jakékoli další komponenty potřebné ke spuštění SAP HANA na jednotkách velká Instance HANA je vaší povinností. Všechny příslušné probíhající operace a správa SAP HANA v Azure jsou také vaší povinností.
- Kromě řešení je zde popsáno můžete nainstalovat další komponenty ve vašem předplatném Azure, která se připojuje k SAP HANA v Azure (velké instance). Příklady jsou komponenty, které umožňují komunikaci s, nebo přímo do systému SAP HANA databáze, jako jsou třeba jump servery, servery protokolu RDP, SAP HANA Studio, SAP Data Services pro SAP BI scénáře, nebo řešení pro monitorování sítě.
- Velká Instance HANA stejně jako v Azure, nabízí podporu pro vysokou dostupnost a funkce pro zotavení po havárii.

## <a name="architecture"></a>Architektura

Na vysoké úrovni SAP HANA v Azure (velké instance) řešení má aplikační vrstvě SAP, které se nacházejí ve virtuálních počítačích. Databázové vrstvě se nachází na hardwaru SAP TDI nakonfigurované v razítku velké Instance ve stejné oblasti Azure, která je připojená k Azure IaaS.

> [!NOTE]
> Nasaďte aplikační vrstvě SAP ve stejné oblasti Azure jako vrstvě SAP DBMS. Toto pravidlo je dobře zdokumentovaná v publikované informace o úloh SAP v Azure. 

Celková architektura SAP HANA v Azure (velké instance) poskytuje certifikací SAP TDI hardwarová konfigurace, což je nevirtualizovaných, holých počítačů, vysoce výkonné server pro databázi SAP HANA. Poskytuje také schopnosti a flexibilitě Azure pro škálování prostředků pro aplikační vrstvě SAP podle svých potřeb.

![Přehled architektury systému SAP HANA v Azure (velké instance)](./media/hana-overview-architecture/image1-architecture.png)

Architektuře, která je rozdělen na tři části:

- **Pravé**: ukazuje na místní infrastrukturu, na kterém běží jiné aplikace v datech centra tak, aby koncoví uživatelé můžou ke obchodní aplikace, jako je SAP. V ideálním případě by tento místní infrastruktury je připojen do Azure s využitím [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **System Center**: Zobrazí Azure IaaS a virtuálních počítačů v tomto případě použijte k hostování SAP nebo k jiným aplikacím, které používají SAP HANA jako systém DBMS. Menší instance HANA, které fungují s pamětí, které poskytují virtuálních počítačů nasazených ve virtuálních počítačích spolu s jejich aplikační vrstvu. Další informace o virtuálních počítačích najdete v tématu [virtuálních počítačů](https://azure.microsoft.com/services/virtual-machines/).

   Síť Azure služby slouží k seskupení systémů SAP vyhrazené společně s další aplikace do virtuální sítě. Tyto virtuální sítě připojit k místním systémům a jde o SAP HANA v Azure (velké instance).

   Aplikace SAP NetWeaver a databází, které podporují spouštění v Azure najdete v tématu [SAP Support Poznámka #1928533 – aplikace SAP v Azure: podporované produkty a typy virtuálních počítačů Azure](https://launchpad.support.sap.com/#/notes/1928533). Dokumentaci o tom, jak nasadit řešení SAP v Azure najdete v tématu:

  -  [Použití SAP na virtuálních počítačích Windows](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Pomocí řešení SAP na virtuálních počítačích Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Vlevo**: zobrazí hardware s certifikací SAP HANA TDI v razítku velkých instancí Azure. Velká Instance HANA jednotky jsou připojené k virtuálním sítím předplatného s využitím stejnou technologii jako připojení z místního do Azure.

Razítko velkých instancí Azure samotné kombinují následující komponenty:

- **Výpočetní**: servery, které jsou založeny na procesorech Intel Xeon E7-8890v3 nebo Intel Xeon E7-8890v4, které poskytují nezbytné výpočetní funkce a jsou certifikací SAP HANA.
- **Síť**: A sjednocené vysokorychlostní síťové infrastruktury, která propojuje výpočty, úložiště a sítě LAN součásti.
- **Úložiště**: infrastrukturu úložiště, který je přístupný prostřednictvím sjednocené síťové infrastruktury. Konkrétní úložnou kapacitu, která je k dispozici, závisí na konkrétní SAP HANA v Azure (velké instance) konfigurace, která je nasazena. Větší kapacitu úložiště je k dispozici na měsíční poplatek.

V infrastruktuře víceklientské velká Instance razítka jsou zákazníci nasadit jako izolovaných tenantů. Při nasazení klienta název předplatného Azure v rámci vaší registrace v Azure. Toto předplatné Azure je ten, který je velká Instance HANA účtovat na vrub. Tyto klienti mají vztah 1:1 k předplatnému Azure. Pro síť je možné pro přístup k celku velká Instance HANA nasazené do jednoho tenanta v jedné oblasti Azure v různých virtuálních sítích, které patří do různých předplatných Azure. Předplatná Azure, musíte patřit do stejné registrace v Azure. 

Stejně jako u virtuálních počítačů, SAP HANA v Azure (velké instance) se nabízí v několika oblastech Azure. Nabízí možnosti zotavení po havárii, můžete vyjádřit výslovný souhlas. Jiné velké Instance razítka v rámci jedné politické geografické oblasti jsou připojené k sobě navzájem. Například HANA velké Instance razítka v oblasti USA – západ a USA – východ jsou propojeny pomocí propojení vyhrazenou síť pro replikaci pro zotavení po havárii. 

Stejně jako, můžete si vybrat mezi různé typy virtuálních počítačů s Azure Virtual Machines, můžete vybrat z různých skladových položek z velká Instance HANA, které jsou přizpůsobené pro typy různých úloh SAP Hana. SAP platí poměry paměti k procesoru soket pro různé úlohy založené na generace procesorů Intel. V následující tabulce jsou uvedeny typy SKU, které nabízejí.

SAP HANA v Azure (velké instance) služba je k dispozici v několika konfigurací v oblasti Azure USA – západ a USA – východ, Austrálie – východ, Austrálie – jihovýchod, západní Evropa, Severní Evropa, Japonsko – východ a Japonsko – západ.

[Velké instance HANA SKU s certifikací SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) seznamu jako:

| Řešení SAP | Procesor | Memory (Paměť) | Úložiště | Dostupnost |
| --- | --- | --- | --- | --- |
| Optimalizováno pro OLAP: SAP BW, BW/4HANA<br /> nebo SAP HANA pro obecné úlohy OLAP | SAP HANA v Azure S72<br /> – 2 x procesor Intel® Xeon® E7-8890 v3<br /> 36 Procesorových jader a 72 vlákna CPU |  768 GB |  3 TB | K dispozici. |
| --- | SAP HANA v Azure S144<br /> – 4 x procesor Intel® Xeon® E7-8890 v3<br /> 72 jader procesoru a 144 vlákna CPU |  1,5 TB |  6 TB | Nenabízí se už |
| --- | SAP HANA v Azure S192<br /> – 4 x procesor Intel® Xeon® E7-8890 v4<br /> 96 jader procesoru a 192 vlákna CPU |  2.0 TB |  8 TB | K dispozici. |
| --- | SAP HANA v Azure S384<br /> –. 8 x procesor Intel® Xeon® E7-8890 v4<br /> 192 Procesorových jader a 384 vlákna CPU |  4.0 TB |  16 TB | K dispozici. |
| Optimalizováno pro OLTP: SAP Business Suite<br /> v SAP HANA nebo S/4HANA (OLTP)<br /> Obecné OLTP | SAP HANA v Azure S72m<br /> – 2 x procesor Intel® Xeon® E7-8890 v3<br /> 36 Procesorových jader a 72 vlákna CPU |  1,5 TB |  6 TB | K dispozici. |
|---| SAP HANA v Azure S144m<br /> – 4 x procesor Intel® Xeon® E7-8890 v3<br /> 72 jader procesoru a 144 vlákna CPU |  3.0 TB |  12 TB | Nenabízí se už |
|---| SAP HANA v Azure S192m<br /> – 4 x procesor Intel® Xeon® E7-8890 v4<br /> 96 jader procesoru a 192 vlákna CPU  |  4.0 TB |  16 TB | K dispozici. |
|---| SAP HANA v Azure S384m<br /> –. 8 x procesor Intel® Xeon® E7-8890 v4<br /> 192 Procesorových jader a 384 vlákna CPU |  6.0 TB |  18 TB | K dispozici. |
|---| SAP HANA v Azure S384xm<br /> –. 8 x procesor Intel® Xeon® E7-8890 v4<br /> 192 Procesorových jader a 384 vlákna CPU |  8.0 TB |  22 TB |  K dispozici. |
|---| SAP HANA v Azure S576m<br /> – 12 x procesor Intel® Xeon® E7-8890 v4<br /> 288 Procesorových jader a 576 vlákna CPU |  12.0 TB |  28 TB | K dispozici. |
|---| SAP HANA v Azure S768m<br /> až 16 x procesor Intel® Xeon® E7-8890 v4<br /> 384 Procesorových jader a 768 vlákna CPU |  16.0 TB |  36 TB | K dispozici. |
|---| SAP HANA v Azure S960m<br /> – 20 x procesor Intel® Xeon® E7-8890 v4<br /> 480 Procesorových jader a 960 vlákna CPU |  20.0 TB |  46 TB | K dispozici. |


V části TDIv5 SAP HANA SAP umožňuje zákaznického velikosti a zákaznické projekty, které by mohly vést k konfigurace serveru, které nejsou uvedeny jako certifikuje:

- [SAP HANA certifikovaných zařízení](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [Platformy IaaS s certifikací SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

V mnoha případech tyto konfigurace specifické pro zákazníka server provádět více paměti, než je server jednotky certifikace SAP. Při práci s řešením SAP, zákazníci mají možnost získat podpory SAP a certifikace pro jejich konfigurace specifické pro zákazníka velikosti serveru. Následující standardní SKU velká Instance HANA v Azure jsou k dispozici a v Microsoftu ceník pro tyto projekty TDIv5 zákaznického velikosti.


| Původní skladovou Položku, kterou může být <br /> Rozšířené v paměti | Procesor | Memory (Paměť) | Úložiště | Dostupnost |
| --- | --- | --- | --- | --- |
| S192m je možné rozšířit na | SAP HANA v Azure S192xm<br /> – 4 x procesor Intel® Xeon® E7-8890 v4<br /> 96 jader procesoru a 192 vlákna CPU |  6.0 TB |  16 TB | K dispozici. |
| S384xm je možné rozšířit na | SAP HANA v Azure S384xxm<br /> –. 8 x procesor Intel® Xeon® E7-8890 v4<br /> 192 Procesorových jader a 384 vlákna CPU |  12.0 TB |  28 TB | K dispozici. |
| S576m je možné rozšířit na | SAP HANA v Azure S576xm<br /> – 12 x procesor Intel® Xeon® E7-8890 v4<br /> 288 Procesorových jader a 576 vlákna CPU |  18.0 TB |  41 TB | K dispozici. |
| S768m je možné rozšířit na | SAP HANA v Azure S768xm<br /> až 16 x procesor Intel® Xeon® E7-8890 v4<br /> 384 Procesorových jader a 768 vlákna CPU |  24,0 TB |  56 TB | K dispozici. |

- Jádra procesoru = SUMA bez hyper-threaded Procesorových jader součtu procesory jednotky serverů.
- Vlákna CPU = SUMA výpočetní vláken poskytuje hyper-threaded Procesorových jader součtu procesory jednotky serverů. Většina jednotky jsou nakonfigurované ve výchozím nastavení používají technologii Hyper-Threading.
- Podle doporučení dodavatele S768m, S768xm a S960m nejsou nakonfigurovaná pro použití technologie Hyper-Threading pro spuštění SAP HANA.


Jaké konkrétní konfigurace zvolené jsou závislé na úlohy, prostředky procesoru a požadované paměti. Je možné používat SKU, které jsou optimalizované pro úlohy OLAP úlohy OLTP. 

U nabídek, s výjimkou jednotky pro nastavení velikosti zákaznické projekty základní hardware jsou certifikací SAP HANA TDI. Dvě různé třídy hardwaru Rozdělit skladové jednotky do:

- S72 S72m, S144, S144m, S192, S192m a S192xm, které jsou označovány jako "Typ můžu třídy" skladových položek.
- S384, S384m, S384xm, S384xxm, S576m, S576xm S768m, S768xm a S960m, což se označuje jako "Typ II třídy" skladových položek.

Kompletní velká Instance HANA razítko není přidělená výhradně pro jediného zákazníka&#39;použijte s. Tato skutečnost platí do stojanů úložnou a výpočetní prostředky připojenými prostřednictvím sítě fabric nasazené v Azure i. Velká Instance HANA infrastruktury, jako je Azure, nasadí různých zákazníků &quot;tenantů&quot; , které jsou izolované od sebe v následujících třech úrovních:

- **Síť**: izolace prostřednictvím virtuálními sítěmi v rámci razítka velká Instance HANA.
- **Úložiště**: izolace prostřednictvím úložiště virtuálních počítačů, které mají přiřazené svazky úložiště a izolovat svazky úložiště mezi tenanty.
- **COMPUTE**: vyhrazené přiřazení jednotek serverů do jednoho tenanta. Žádné pevné nebo obnovitelné dělení server jednotek. Bez sdílení serveru nebo hostitele celek mezi tenanty. 

Nasazení jednotky velká Instance HANA mezi různých tenantech nejsou navzájem viditelné. Velká Instance HANA jednotky nasazených v různých tenantech nemůže komunikovat s jinými na úrovni razítko velká Instance HANA. Pouze velká Instance HANA jednotky v rámci jednoho tenanta může komunikovat mezi sebou na úrovni razítko velká Instance HANA.

Nasazené tenanta v razítku velkou instanci se přiřadí na jedno předplatné Azure pro účely fakturace. Pro síť můžete přistupovat z virtuální sítě z jiných předplatných Azure v rámci stejné registrace v Azure. Pokud provádíte nasazení s jiným předplatným Azure ve stejné oblasti Azure, také můžete požádat o oddělené velká Instance HANA tenanta.

Existují významné rozdíly mezi službou SAP HANA na velká Instance HANA a SAP HANA běžící na virtuálních počítačích nasazených v Azure:

- Neexistuje žádná vrstva virtualizace pro SAP HANA v Azure (velké instance). Získáte výkonu základního hardwaru úplné obnovení systému.
- Na rozdíl od Azure SAP HANA v Azure (velké instance) server je vyhrazen pro konkrétního zákazníka. Není možné, že jednotka serveru nebo hostitele je pevně nebo obnovitelné dělit na oddíly. V důsledku toho jednotka velká Instance HANA se používá jako přiřazená jako celek na tenanta a s ním pro vás. Restartování nebo vypnutí serveru nebude automaticky vést k operačního systému a SAP HANA se nasazuje na jiný server. (Pro typ můžu třídy SKU, jedinou výjimkou je, pokud server zaznamená problémy a opětovné nasazení je třeba provést na jiném serveru.)
- Na rozdíl od Azure, kde typy procesoru hostitele jsou vybrány pro nejlepší poměr cena/výkon, jsou tyto typy procesoru, zvolená pro SAP HANA v Azure (velké instance), nejvyšší provádění řádku procesor Intel E7v3 a E7v4.


### <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Spusťte několik instancí SAP HANA na jednu jednotku velká Instance HANA
Je možné k hostování více než jeden aktivní instanci SAP HANA na jednotkách velká Instance HANA. Poskytují možnosti snímků úložiště a zotavení po havárii, taková konfigurace vyžaduje svazku nastavit na jednu instanci. V současné době velká Instance HANA jednotky lze dále rozdělit následujícím způsobem:

- **S72 S72m, S144, S192**: Dokupuje se násobek 256 GB, 256 GB od nejmenších po počáteční jednotky. Různé přírůstky například 256 GB až 512 GB, mohou být kombinovány pro maximální paměť jednotku.
- **S144m a S192m**: Dokupuje se násobek 256 GB, 512 GB nejmenší jednotka. Různé přírůstky například 512 GB a o velikosti 768 GB mohou být kombinovány pro maximální paměť jednotku.
- **Zadejte třídu II**: V přírůstcích po 512 GB, od nejmenších po počáteční jednotku 2 TB. Různé přírůstky například 512 GB, 1 TB a 1,5 TB mohou být kombinovány pro maximální paměť jednotku.

Některé příklady spouštění více instancí SAP HANA může vypadat nějak takto.

| Skladová jednotka (SKU) | Velikost paměti | Velikost úložiště | Velikosti více databází |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1 x 768 GB instance HANA<br /> nebo instance 1 x 512 GB + 1 x 256 GB instance<br /> nebo 3 x 256 GB instance | 
| S72m | 1,5 TB | 6 TB | instance HANA 3x512GB<br />nebo instance 1 x 512 GB + 1 x 1 TB instance<br />nebo instance 6 x 256 GB<br />nebo instance 1x1.5 TB | 
| S192m | 4 TB | 16 TB | instance 8 x 512 GB<br />nebo instancích o velikosti 4 x 1 TB<br />nebo instance 4 x 512 GB + instancích o velikosti 2 x 1 TB<br />nebo instance 4 x 768 GB + 2 x 512 GB instance<br />nebo instance 1 × 4 TB |
| S384xm | 8 TB | 22 TB | instancích o velikosti 4 x 2 TB<br />nebo instancích o velikosti 2 x 4 TB<br />nebo instancích o velikosti 2 x 3 TB + instancích o velikosti 1 × 2 TB<br />nebo 2x2.5 TB instancích a instancích o velikosti 1 × 3 TB<br />nebo instance 1 × 8 TB |


Existují i další varianty konfigurací. 

### <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Použití rozšíření a ovládání datových vrstev na data uzlů SAP HANA
SAP podporuje datový model vrstev pro SAP BW dvě různé verze SAP Netweaveru a SAP BW/4HANA. Další informace o modelu vrstev dat, naleznete v dokumentu SAP [SAP BW/4HANA a SAP BW on HANA se SAP HANA rozšíření uzly](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
S velká Instance HANA můžete použít možnost 1 konfiguraci uzlů rozšíření SAP HANA, jak je popsáno v dokumentech blogu – nejčastější dotazy a SAP. Možnost 2 konfigurace můžete nastavit s následující SKU velké Instance HANA: S72m S192, S192m, S384 a S384m. 

Při pohledu na dokumentaci výhodu v podobě nemusí zobrazovat okamžitě. Ale když se podíváte na pokyny pro nastavení velikosti SAP, můžete zobrazit výhodu pomocí možnosti-1 a 2 možnost rozšíření uzly SAP HANA. Tady jsou příklady:

- Pokyny pro nastavení velikosti SAP HANA obvykle vyžadují dvojnásobek objem dat jako paměť. Při spuštění vaší instance SAP HANA s výměně dat máte pouze 50 % nebo méně paměti naplněný daty. Zbývající část paměti se nachází v ideálním případě pro SAP HANA dělat svou práci.
- To znamená v jednotce S192 velké Instance HANA 2 TB paměti, s databázi SAP BW máte pouze 1 TB jako datový svazek.
- Pokud používáte další uzel výrazu SAP HANA možnost-1, také S192 HANA velké Instance SKU, poskytuje dodatečnou kapacitu 2 TB pro datový svazek. V konfiguraci možnost 2 získáte další 4 TB pro teplé datový svazek. Ve srovnání s aktivní uzel, kapacita paměti úplné "teplé" rozšíření uzlu je možné pro ukládání dat pro možnost-1. Double paměti lze použít pro datový svazek v konfiguraci uzlu Rozšíření možnost 2 SAP HANA.
- Můžete skončit s kapacitou 3 TB pro vaše data a horkou záložním poměr 1:2 pro možnost-1. Máte 5 TB dat a poměru 1:4 s konfigurací uzlu rozšíření pro možnost-2.

Čím vyšší objem dat ve srovnání s pamětí, vyšší pravděpodobnost jsou uložená data warm vyžadujete pro pro službu disk storage.


## <a name="operations-model-and-responsibilities"></a>Provozní model a zodpovědnosti

Služby, opatřeného SAP HANA v Azure (velké instance) je v souladu s služby Azure IaaS. Můžete získat instanci velká Instance HANA s nainstalovaným operačním systémem, která je optimalizovaná pro SAP HANA. Stejně jako u virtuálních počítačů Azure IaaS, většinu úloh, posílení zabezpečení operačního systému, instalace dalšího softwaru, instalaci HANA, provozní operačního systému a HANA a aktualizaci operačního systému a HANA je vaší odpovědností. Microsoft nebude vynucení aktualizací operačního systému nebo HANA na vás.

![Odpovědnosti SAP Hana v Azure (velké instance)](./media/hana-overview-architecture/image2-responsibilities.png)

Jak je znázorněno na diagramu, SAP HANA v Azure (velké instance) je více tenanty, které nabízejí IaaS. Ve většině případů je dělení zodpovědnosti na hranici infrastruktury operačního systému. Microsoft je zodpovědná za všechny aspekty služby pod řádkem operačního systému. Zodpovídáte za všechny aspekty služby nad řádku. Operační systém je na vás. Můžete nadále používat nejaktuálnější místních metod, které může využívat pro dodržování předpisů, zabezpečení, Správa aplikací, základ a operačního systému správy. V systémech zobrazují, jakoby se ve vaší síti všechny vztahující.

Tato služba je optimalizovaná pro SAP HANA, existují oblasti, kde potřebujete pro práci s Microsoft používat základní funkce infrastruktury pro dosažení co nejlepších výsledků.

Následující seznam obsahuje další podrobnosti na jednotlivých vrstev a vaše odpovědnosti:

**Sítě**: interní sítě pro razítko velká Instance spouštějí SAP HANA. Vaší zodpovědností zahrnuje přístup k úložišti, spojení mezi instancí (pro horizontální navýšení kapacity a další funkce), připojení na šířku a připojení k Azure, kde se hostuje aplikační vrstvě SAP na virtuálních počítačích. Zahrnuje také sítě WAN připojení mezi datovými centry Azure pro replikaci pro účely zotavení po havárii. Všechny sítě dělí podle tenanta a mít kvality služeb, které jsou použity.

**Úložiště**: virtualizovaného rozdělit na oddíly úložiště pro všechny svazky, které jsou vyžadované servery SAP HANA, a také pro snímky. 

**Servery**: vyhrazených fyzických serverů ke spuštění databáze SAP HANA, která je přiřazena klientům. Servery sady s typem třídy můžu skladových položek jsou abstrahované hardwaru. Konfigurace serveru s těmito typy serverů se shromažďují a udržuje na profilů, které lze přesunout z jednoho fyzického hardwaru do jiného fyzického hardwaru. Takové (ruční) přesun profilu operacemi je možné porovnat trochu s opravou služeb Azure. Servery třídy SKU typu II nenabízí takovou schopnost.

**SDDC**: software pro správu, který se používá pro správu datového centra jako softwarově definovaných entit. Umožňuje Microsoftu do fondu zdrojů pro škálování, dostupnost a kvůli výkonu.

**Zakáže**: operační systém zvolíte (SUSE Linux nebo Red Hat Linux), na kterém běží na serverech. Pro spuštění SAP HANA bylo zadáno Image operačních systémů, které jsou součástí jednotlivých dodavatel Linuxových společnosti Microsoft. Musíte mít předplatné s dodavatelem Linux pro konkrétní image optimalizovaných pro SAP HANA. Zodpovídáte za registraci bitové kopie operačního systému dodavatele. 

Z bodu předání společností Microsoft zodpovídáte za všechny další opravy operačního systému Linux. Tato oprava obsahuje další balíčky, který může být nezbytné pro úspěšnou instalaci SAP HANA, která nejsou zahrnuta konkrétní dodavatel Linuxových v jejich SAP HANA optimalizované bitové kopie operačního systému. (Další informace najdete v tématu SAP HANA dokumentaci k instalaci a poznámkách.) 

Zodpovídáte za opravy operačního systému z důvodu selhání nebo optimalizace operačního systému a jeho ovladače vzhledem ke konkrétní serverového hardwaru. Můžete také zodpovídají za zabezpečení nebo funkční opravy operačního systému. 

Vaše odpovědnosti také zahrnuje monitorování a plánování kapacity:

- Spotřeba prostředků procesoru.
- Využití paměti.
- Správa pro diskové svazky související s volného místa, vstupně-výstupních operací a latenci.
- Objem síťového provozu mezi velká Instance HANA a aplikační vrstvě SAP.

Základní infrastruktury velká Instance HANA poskytuje funkce pro zálohování a obnovení svazek s operačním systémem. Pomocí této funkce je také vaší povinností.

**Middleware**: Instance SAP HANA, především. Správa, operace a monitorování jsou vaší odpovědností. Zadaná funkce můžete používat úložiště snímků pro účely obnovení zálohování a obnovení a po havárii. Tyto možnosti jsou k dispozici v infrastruktuře. Vaše odpovědnosti také zahrnovat návrh vysoké dostupnosti nebo zotavení po havárii s těmito možnostmi monitorování a jejich využitím k určení, zda snímků úložiště byl úspěšně proveden.

**Data**: vaše data spravovaná přes SAP HANA a další data, jako je zálohování sdílených složek soubory jsou umístěné v svazků nebo souborů. Vaše odpovědnosti patří monitorování volného místa na disku a správu obsahu ve svazcích. Také zodpovídáte za monitorování úspěšné provedení zálohy diskové svazky a snímků úložiště. Úspěšné spuštění replikace dat do lokality pro zotavení po havárii zodpovídá Microsoft.

**Aplikace:** instancí aplikace SAP nebo v případě aplikací mimo systém SAP, aplikační vrstvu těchto aplikací. Vaše odpovědnosti patří nasazení, správu, operací a monitorování těchto aplikací. Zodpovídáte za spotřebu prostředků procesoru, využití paměti, spotřebu úložiště Azure a využití šířky pásma sítě v rámci virtuálních sítí plánování kapacity. Můžete také zodpovídají za plánování kapacity pro spotřebu prostředků z virtuálních sítí k SAP HANA v Azure (velké instance).

**Sítě WAN**: připojení, je vytvořit z místního na nasazení Azure pro úlohy. Všichni zákazníci s velká Instance HANA pomocí Azure ExpressRoute pro připojení. Toto připojení, které nejsou součástí SAP Hana v Azure (velké instance) řešení. Zodpovídáte za nastavení tohoto připojení.

**Archiv**: Chcete archivovat kopie dat pomocí vlastní metody v účtech úložiště. Archivace vyžaduje správu, dodržování předpisů, náklady a provoz. Zodpovídáte za generování archivu kopií a záloh v Azure a ukládat je na vyhovující způsobem.

Zobrazit [smlouva SLA pro SAP HANA v Azure (velké instance)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/).

## <a name="sizing"></a>Velikost

Nastavení velikosti pro velké Instance HANA se nijak neliší od změny velikosti pro HANA obecně. U stávajících a nasadit systémy, které chcete přesunout z jiných relační databázový systém k HANA, SAP poskytuje řadu sestav, které běží na vašich stávajících systémů SAP. Pokud databáze byla převedena do platformy HANA, tyto sestavy zkontrolujte data a výpočet požadavků na paměť pro instanci HANA. Další informace o tom, jak spustit tyto sestavy a získat jeho nejnovější opravy nebo verze si přečtěte následující poznámky SAP:

- [Poznámka SAP #1793345 - změny velikosti pro SAP Suite on HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [Poznámka: #1872170 - Suite on HANA a s/4 HANA zpráva o velikosti SAP](https://launchpad.support.sap.com/#/notes/1872170)
- [Poznámka SAP #2121330 – nejčastější dotazy: SAP BW on HANA velikosti sestav](https://launchpad.support.sap.com/#/notes/2121330)
- [Poznámka: #1736976 – Sestava nastavení velikosti pro BW on HANA SAP](https://launchpad.support.sap.com/#/notes/1736976)
- [Poznámka: #2296290 – nové velikosti sestavy pro BW on HANA SAP](https://launchpad.support.sap.com/#/notes/2296290)

Pro implementace zelené louce je možné vypočítat požadavky na paměť implementace SAP software nad HANA SAP rychlé přizpůsobení velikosti symbolů.

Požadavky na paměť pro HANA zvýšit objem dat roste. Mějte na paměti z aktuální využití paměti můžete odhadnout, co se děje bude v budoucnu. Podle požadavků na vysoce paměťově, pak můžete mapovat vaši vyžadují do jedné skladové položky velké Instance HANA.

## <a name="requirements"></a>Požadavky

Tento seznam sestaví požadavky pro spuštění SAP HANA v Azure (větší instance).

**Microsoft Azure**

- Předplatné Azure, které lze propojit k SAP HANA v Azure (velké instance).
- Smlouvu o podpoře Microsoft Premier. Konkrétní informace související se systémem SAP v Azure najdete v tématu [SAP Support Poznámka #2015553 – SAP v Microsoft Azure: požadavky pro podporu](https://launchpad.support.sap.com/#/notes/2015553). Pokud používáte jednotky velká Instance HANA s 384 a více procesorů, potřebujete také rozšířit na Premier Azure Rapid Response zahrnout smlouvu o podpoře.
- Sledování HANA velké Instance SKU je nutné po provedení nastavení velikosti cvičení s řešením SAP.

**Připojení k síti**

- ExpressRoute mezi místní do Azure: pro připojení k Azure v místním datovém centru, ujistěte se, že k seřazení alespoň 1 GB/s připojení od svého poskytovatele internetových služeb. 

**Operační systém**

- Licence pro SUSE Linux Enterprise Server 12 pro aplikace SAP.

   > [!NOTE] 
   > Operační systém od společnosti Microsoft není zaregistrován SUSE. Není připojen k instanci nástroje pro správu předplatného.

- SUSE Linux předplatné nástroj pro správu nasazených v Azure na virtuálním počítači. Tento nástroj poskytuje možnosti pro SAP HANA v Azure (velké instance) zaregistrované a v uvedeném pořadí aktualizoval SUSE. (Není v rámci datového centra velká Instance HANA bez připojení k Internetu.) 
- Licence pro Red Hat Enterprise Linux 6.7 nebo 7.x pro SAP HANA.

   > [!NOTE]
   > Operační systém od společnosti Microsoft není zaregistrován Red Hat. Není připojen k instanci správce předplatného Red Hat.

- Red Hat správce předplatného nasazené v Azure na virtuálním počítači. Správce předplatného Red Hat poskytuje možnosti pro SAP HANA v Azure (velké instance) zaregistrované a aktualizovat v uvedeném pořadí by Red Hat. (Neexistuje žádný přímý přístup k Internetu z v rámci tenanta nasazené na razítku velkých instancí Azure.)
- SAP vyžaduje podporu kontrakt s poskytovatele Linuxu. Tento požadavek se neodebere řešení pro velké Instance HANA nebo skutečnost, že spouštíte Linux v Azure. Na rozdíl od některých z Galerie imagí Azure pro Linux, poplatky za službu se *není* součástí nabídky řešení velké instance HANA. Je vaší odpovědností pro splnění požadavků SAP ohledně smlouvy o podpoře s distributora Linuxu. 
   - SUSE Linux, vyhledejte požadavky Smlouvy o podpoře v [SAP Poznámka #1984787 - operačním systémem SUSE Linux Enterprise Server 12: poznámky k instalaci](https://launchpad.support.sap.com/#/notes/1984787) a [SAP Poznámka #1056161 – SUSE prioritní podporu pro aplikace SAP](https://launchpad.support.sap.com/#/notes/1056161).
   - Pro Red Hat Linux musíte mít správné předplatné úrovně, které zahrnují podporu a služby aktualizace pro operační systémy velká Instance HANA. Red Hat doporučuje Red Hat Enterprise Linux pro [řešení SAP] (https://access.redhat.com/solutions/3082481 předplatného. 

Matice podpory různých verzí SAP HANA s různými verzemi systému Linux, najdete v části [2235581 # Poznámka SAP](https://launchpad.support.sap.com/#/notes/2235581).

Matice kompatibility operačního systému a verze firmwaru a ovladače HLI, najdete v tématu [Upgrade operačního systému pro HLI](os-upgrade-hana-large-instance.md).


> [!IMPORTANT] 
> Typ II jednotek pouze SLES 12 SP2 operačního systému verze se v tuto chvíli nepodporuje. 


**Database**

- Licence a software instalační komponenty pro SAP HANA (platformy nebo enterprise edition).

**Aplikace**

- Licence a software instalační komponenty pro všechny aplikace SAP, které se připojují k SAP HANA a SAP související podporu kontraktů.
- Licence a software instalační komponenty pro všechny aplikace mimo systém SAP používá ve vztahu k SAP HANA v prostředí Azure (velké instance) a související smlouvy o podpoře.

**Dovednosti**

- Zkušenosti s a znalosti Azure IaaS a jeho součástí.
- Prostředí s a znalosti o tom, jak nasadit úloh SAP v Azure.
- Instalace SAP HANA s certifikací pracovníky.
- SAP architekt dovednosti: pište návrh vysoké dostupnosti a zotavení po havárii kolem SAP HANA.

**SAP**

- Očekává se, že jste je zákazníkem v systému SAP a mají podporu kontraktu s řešením SAP.
- Zejména pro implementace třídy typu II SKU velké Instance HANA konzultujte s řešením SAP verze SAP HANA a konečný výsledek konfigurace na velkých a velkých vertikálně navýšit kapacitu hardwaru.


## <a name="storage"></a>Úložiště

Rozložení úložiště pro SAP HANA v Azure (velké instance) je nakonfigurována ve SAP HANA v modelu nasazení classic prostřednictvím SAP Doporučené pokyny. Pokyny jsou dokumentovány v článku [požadavky na úložiště SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) dokument white paper.

Velká Instance HANA typu můžu třídy součástí čtyřikrát paměti svazek jako svazek úložiště. Pro třídu typu II velká Instance HANA jednotek není čtyřnásobnou úložiště. Jednotky se dodávají s svazku, který je určený pro ukládání záloh protokolů transakcí HANA. Další informace najdete v tématu [nainstalujte a nakonfigurujte SAP HANA (velké instance) v Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

V následující tabulce, jde o přidělení úložiště. Tabulka uvádí přibližnou kapacitu pro různé svazky s různými jednotkami velká Instance HANA k dispozici.

| Velká Instance HANA SKU | data systému Hana / | Hana/log | Hana/shared | Hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3,328 GB | 768 GB |1,280 GB | 768 GB |
| S192 | 4 608 GB | 1 024 GB | 1 536 GB | 1 024 GB |
| S192m | 11,520 GB | 1 536 GB | 1,792 GB | 1 536 GB |
| S192xm |  11,520 GB |  1 536 GB |  1,792 GB |  1 536 GB |
| S384 | 11,520 GB | 1 536 GB | 1,792 GB | 1 536 GB |
| S384m | 12 000 GB | 2 050 GB | 2 050 GB | 2 040 GB |
| S384xm | 16 000 GB | 2 050 GB | 2 050 GB | 2 040 GB |
| S384xxm |  20 000 GB | 3,100 GB | 2 050 GB | 3,100 GB |
| S576m | 20 000 GB | 3,100 GB | 2 050 GB | 3,100 GB |
| S576xm | 31,744 GB | 4 096 GB | 2 048 GB | 4 096 GB |
| S768m | 28,000 GB | 3,100 GB | 2 050 GB | 3,100 GB |
| S768xm | 40 960 GB | 6,144 GB | 4 096 GB | 6,144 GB |
| S960m | 36,000 GB | 4,100 GB | 2 050 GB | 4,100 GB |


Skutečné nasazené svazky může lišit v závislosti na nasazení a nástroje, který se používá k zobrazení velikostí svazku.

Pokud rozdělíte SKU velké Instance HANA, několik příkladů kusů možné dělení může vypadat:

| Oddíl paměti v GB | data systému Hana / | Hana/log | Hana/shared | Hana/log/zálohování |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1,792 GB | 640 GB | 1 024 GB | 640 GB |
| 1 536 | 3,328 GB | 768 GB | 1,280 GB | 768 GB |


Tyto velikosti jsou čísla hrubý svazku, která mírně nasazení a nástroje používané ke podívejte se na svazky se může lišit. Existují také jiné velikosti oddílu, jako je například 2,5 TB. Tyto velikosti úložiště se vypočítávat pomocí vzorce podobný použitému pro předchozí oddíly. Termín "oddíly", neznamená, že operační systém, paměť nebo prostředky procesoru se nijak rozdělit na oddíly. Znamená to oddílů pro úložiště pro různé instance HANA, které můžete chtít nasazení v jedné jednotce velká Instance HANA. 

Potřebujete další úložiště. Přidání úložiště koupit další úložiště v jednotkách 1 TB. Toto dodatečné úložiště lze přidat jako další svazek. To lze také rozšířit nejméně jeden z existující svazky. Není možné snížit velikost svazků původně nasazené a většinou popsané v předchozích tabulkách. Také není možné změnit názvy svazků nebo připojit názvy. Výše popsaný svazky úložiště jsou připojeny k velká Instance HANA jednotky jako NFS4 svazky.

Snímky úložiště můžete používat pro účely obnovení zálohování a obnovení a po havárii. Další informace najdete v tématu [SAP HANA (velké instance) vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Přečtěte si [HLI Podporované scénáře](hana-supported-scenario.md) podrobnosti rozložení úložiště pro váš scénář.

### <a name="encryption-of-data-at-rest"></a>Šifrování neaktivních uložených dat
Úložiště pro velké Instance HANA umožňuje transparentní šifrování dat se ukládají na discích. Při nasazení jednotka velká Instance HANA, můžete povolit tento typ šifrování. Také můžete změnit na šifrovaných svazcích po nasazení se provádí. Přechod z nešifrované na šifrovaných svazcích je transparentní a nevyžaduje výpadek. 

S typem můžu třídy SKU, se šifrují spouštěcí logická jednotka je uložený na svazku. Pro třídu typu II skladové položky z velké instance HANA je potřeba šifrovat spouštěcí logické jednotky s operačním systémem metody. Další informace obraťte se na tým Microsoft Service Management.


## <a name="networking"></a>Sítě

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

 

### <a name="additional-virtual-network-information"></a>Informace o další virtuální sítě

Pokud chcete připojení virtuální sítě pro ExpressRoute, musíte vytvořit brány Azure. Další informace najdete v tématu [o branách virtuálních sítí pro ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). 

Služba Azure gateway je možné s využitím ExpressRoute k infrastruktuře mimo Azure nebo razítku velkých instancí Azure. Služba Azure gateway lze použít také pro připojení mezi virtuálními sítěmi. Další informace najdete v tématu [konfigurace síťové připojení pomocí prostředí PowerShell pro Resource Manager](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Služba Azure gateway na maximálně čtyři různá připojení ExpressRoute můžete připojit tak dlouho, dokud tato připojení pocházejí z různých hraničním směrovačům Microsoft enterprise. Další informace najdete v tématu [infrastrukturu SAP HANA (velké instance) a možnosti připojení v Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Propustnost, kterou poskytuje služba Azure gateway se liší pro oba případy použití. Další informace najdete v tématu [informace o službě VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Maximální propustnost, které můžete dosáhnout s bránou virtuální sítě je 10 GB/s s použitím připojení typu ExpressRoute. Kopírování souborů mezi virtuálním Počítačem, který se nachází ve virtuální síti a systém v místním (jako datový proud jedna kopie) nelze dosáhnout úplné propustnosti různé skladové položky brány. Využívat šířku pásma kompletní brány virtuální sítě pomocí různých datových proudů. Nebo je nutné zkopírovat různé soubory v paralelní datové proudy jednoho souboru.


### <a name="networking-architecture-for-hana-large-instance"></a>Architektura sítě pro velké Instance HANA
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



### <a name="single-sap-system"></a>Jeden systému SAP

V místní infrastruktuře bylo dříve uvedeno je připojené prostřednictvím ExpressRoute do Azure. Okruh ExpressRoute se připojuje do hraniční směrovač podniku. Další informace najdete v tématu [technický přehled ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Po navázání trasy připojí do páteřní síť Azure a všech oblastech Azure jsou dostupné.

> [!NOTE] 
> Pokud chcete spustit prostředí SAP v Azure, připojte k hraniční směrovač podniku nejbližší oblasti Azure v prostředí SAP. Azure velká Instance razítka jsou propojené prostřednictvím vyhrazené podnikové hraniční směrovače zařízení kvůli minimalizaci latence sítě mezi virtuálními počítači v Azure IaaS a velké Instance razítka.

Brána virtuální sítě pro virtuální počítače, které hostují instance aplikace SAP je připojen k okruhu ExpressRoute. Stejné virtuální síti je připojený k hraniční směrovač vyhrazený pro připojení k velké Instance razítka samostatné podniku.

Tento systém je jednoduchý příklad jednoho systému SAP. Aplikační vrstvě SAP je hostované v Azure. Databáze SAP HANA běží na systému SAP HANA v Azure (velké instance). Předpokladem je, že kritický bod nepředstavuje šířky pásma brány virtuální sítě, propustnost 2 GB/s nebo 10 GB/s.

### <a name="multiple-sap-systems-or-large-sap-systems"></a>Několik systémů SAP vyhrazené nebo rozsáhlých systémů SAP

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


### <a name="routing-in-azure"></a>Směrování v Azure

Tři aspekty směrování sítě jsou důležité pro SAP HANA v Azure (velké instance):

* SAP HANA v Azure (velké instance) můžete přistupovat pouze prostřednictvím virtuálních počítačů a vyhrazené připojení ExpressRoute, ne přímo z místní. Přímý přístup z místní jednotky velká Instance HANA dodaných společností Microsoft, není možné okamžitě. Přenositelné směrování omezení jsou způsobeny aktuální architektury sítě Azure používané pro velké Instance SAP HANA. Někteří klienti správy a všechny aplikace, které potřebují přímý přístup, jako je SAP správci řešení spuštěné v místním nemůže připojit k databázi SAP HANA.

* Pokud máte velké Instance HANA jednotky nasazené ve dvou různých oblastech Azure pro zotavení po havárii, omezení přechodné směrování použita. Jinými slovy IP adresy velká Instance HANA jednotka v jedné oblasti (například USA – západ) nejsou směrovány na jednotku velká Instance HANA nasadit v jiné oblasti (například USA – východ). Toto omezení je nezávislé na použití síť Azure, partnerský vztah napříč oblastmi nebo křížové připojení okruhy ExpressRoute, které se připojují velká Instance HANA jednotek do virtuálních sítí. Grafické znázornění viz obrázek v části "použití velká Instance HANA ve více oblastech. jednotek" Toto omezení, který se dodává s nasazenou architekturou zakazuje okamžité použijte HANA System Replication funkce zotavení po havárii.

* SAP HANA v Azure (velké instance) jednotky mají přiřazenou IP adresu z rozsah adres fondu IP serveru, který jste odeslali. Další informace najdete v tématu [infrastrukturu SAP HANA (velké instance) a možnosti připojení v Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Tato IP adresa je přístupné prostřednictvím ExpressRoute, který se připojuje k HANA v Azure (velké instance) virtuální sítě a předplatných Azure. Přidělit IP adresu z celkového počtu, rozsah adres fondu IP serveru přímo přiřazená jednotky hardwaru. Má *není* přiřazeném prostřednictvím překladu adres už nepotřebujeme, stejně jako v případě v prvním nasazení tohoto řešení. 

> [!NOTE] 
> K překonání omezení v přechodné směrování, jak je vysvětleno v prvních dvou seznam položek, použijte další komponenty pro směrování. Součásti, které slouží k překonání tohoto omezení může být:

> * Reverzních proxy serverů pro směrování dat do a z. Například F5 BIG-IP, server NGINX pomocí Traffic Manageru nasazené v Azure jako virtuální brána firewall/přenosů směrování řešení.
> * Pomocí [pravidla iptables tak](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) v virtuálního počítače s Linuxem umožňující směrování mezi místními umístěními a velké Instance HANA jednotky, nebo mezi jednotkami velká Instance HANA v různých oblastech.

> Mějte na paměti, že implementaci a podporu pro vlastní řešení zahrnující třetích stran síťových zařízení nebo IPTables není k dispozici společností Microsoft. Podpora musí poskytnout dodavatel komponentu používanou nebo integrátor. 

### <a name="internet-connectivity-of-hana-large-instance"></a>Připojení k Internetu velké instance HANA
Velká Instance HANA neodpovídá *není* mít přímé připojení k Internetu. Jako příklad toto omezení může omezit možnost zaregistrovat image operačního systému přímo s operačním systémem. Můžete potřebovat pro práci s vaším místním operačním systémem SUSE Linux Enterprise Server předplatné Management Tool serveru nebo správce předplatného Red Hat Enterprise Linux.

### <a name="data-encryption-between-vms-and-hana-large-instance"></a>Šifrování dat mezi virtuálními počítači a velké Instance HANA
Data přenášená mezi velká Instance HANA a virtuální počítače nejsou šifrována. Výhradně pro server exchange mezi HANA DBMS na straně a aplikace založené na JDBC/ODBC, ale můžete povolit šifrování přenosů. Další informace najdete v tématu [této dokumentace SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

### <a name="use-hana-large-instance-units-in-multiple-regions"></a>Použití jednotek velká Instance HANA ve více oblastech

Bude pravděpodobně z důvodů pro nasazení SAP HANA v Azure (velké instance) v několika oblastech Azure kromě pro zotavení po havárii. Možná budete chtít přístup ze všech virtuálních počítačů nasazených v různých virtuálních sítích v oblastech velká Instance HANA. IP adresy přiřazené k jiné velké Instance HANA jednotek nejsou šířeny nad rámec virtuální sítě, které jsou přímo připojené prostřednictvím jejich brány do instancí. V důsledku toho se používá i drobnou změnu na návrh virtuální sítě. Bránu virtuální sítě může zpracovávat čtyři různé okruhy ExpressRoute, mimo jiné podnikové hraniční směrovače. Každá virtuální síť, který je připojený k jednomu z razítka velká Instance může být připojen k velké Instance razítka v jiné oblasti Azure.

![Virtuální sítě připojené k razítkům velkých instancí Azure v různých oblastech Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

Obrázek ukazuje, jak různých virtuálních sítí v obou oblastech jsou připojené dva různé okruhy ExpressRoute, které se používají k připojení k SAP HANA v Azure (velké instance) v obou oblastech Azure. Nově zavedená připojení jsou obdélníkové červené čáry. Tato připojení z virtuální sítě virtuální počítače spuštěné v jednom z těchto virtuálních sítí můžete přístup k každý z různých jednotek velká Instance HANA nasazené ve dvou oblastech. Jak ukazuje obrázek, se předpokládá, že máte dvě spojení ExpressRoute z místního na dvou oblastech Azure. Toto uspořádání se doporučuje z důvodů pro zotavení po havárii.

> [!IMPORTANT] 
> Pokud jste použili víc okruhů ExpressRoute, předřazení AS Path a nastavení místní Preference protokolu BGP by měla sloužit k zajištění správné směrování provozu.


