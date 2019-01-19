---
title: Konfigurace infrastruktury SAP HANA a operací v Azure | Dokumentace Microsoftu
description: Provozní příručka pro systémy SAP HANA, které jsou nasazeny na virtuálních počítačích Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: juergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cede896e9a2a4c92a495a502fb6cf69805d755ee
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/18/2019
ms.locfileid: "54402128"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Konfigurace infrastruktury SAP HANA a operací v Azure
Tento dokument obsahuje pokyny pro konfiguraci infrastruktury Azure a operační systémy SAP HANA, které jsou nasazené na nativních virtuálních počítačích Azure (VM). Dokument obsahuje také informace o konfiguraci pro SAP HANA Škálováním pro skladovou Položku virtuálního počítače M128s. Tento dokument není určena k nahrazení standardní dokumentaci k SAPU, který obsahuje následující obsah:

- [Příručka věnovaná SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Průvodců instalací SAPU](https://service.sap.com/instguides)
- [Poznámky SAP](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Požadavky
K použití tohoto průvodce potřebujete základní znalosti o následující komponenty Azure:

- [Virtuální počítače Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Sítě Azure a virtuální sítě](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Další informace o SAP NetWeaver a jiných komponent SAP v Azure, najdete v článku [SAP v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) část [dokumentace ke službě Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Důležité informace o základní nastavení
Následující části popisují důležité informace o základní nastavení pro nasazení systému SAP HANA na virtuálních počítačích Azure.

### <a name="connect-into-azure-virtual-machines"></a>Připojení do virtuálních počítačů Azure
Jak je uvedeno v [virtuálních počítačů Azure Průvodce plánováním](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), existují dvě základní metody pro připojení do virtuálních počítačů Azure:

- Připojení přes internet a veřejných koncových bodů na virtuálním počítači s Jump nebo na virtuálním počítači, na kterém běží SAP HANA.
- Připojení přes [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) nebo Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Připojení Site-to-site přes VPN nebo ExpressRoute je nezbytné pro produkční scénáře. Tento typ připojení je nutná pro neprodukční scénáře, které informačního kanálu do produkčních scénářů, kde se používá softwaru SAP. Následující obrázek ukazuje příklad připojení webů:

![Připojení mezi weby](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Vyberte typy virtuálních počítačů Azure
Typy virtuálních počítačů Azure, které lze použít pro produkční scénáře jsou uvedeny v [dokumentaci k SAPU pro IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). U scénářů s testovacím širší nativní typy virtuálních počítačů Azure je k dispozici.

>[!NOTE]
> Li se o neprodukční scénářů použití typy virtuálních počítačů, které jsou uvedeny v [Poznámka SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Použití virtuálních počítačů Azure pro produkční scénáře, zkontrolujte, zda virtuální počítače v systému SAP publikována s certifikací SAP HANA [seznam certifikací platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Nasazení virtuálních počítačů v Azure s použitím:

- Na webu Azure portal.
- Rutiny Powershellu pro Azure.
- Azure CLI.

Také můžete nasadit kompletní instalované platformy SAP HANA ve službě Azure VM prostřednictvím [SAP Cloudovou platformu](https://cal.sap.com/). Proces instalace je popsána v [nasadit řešení SAP S/4HANA nebo BW/4HANA v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) nebo s automatizací vydané [tady](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

### <a name="choose-azure-storage-type"></a>Zvolte typ služby Azure Storage
Azure nabízí dva typy úložiště, které jsou vhodné pro virtuální počítače Azure, které se spouštějí SAP HANA:

- [Azure Standard Storage](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure nabízí dvě metody nasazení pro virtuální pevné disky na Azure na úrovni Standard a Premium Storage. Pokud celková scénář umožňuje, využít výhod [Azure spravovaný disk](https://azure.microsoft.com/services/managed-disks/) nasazení.

Seznam typů úložiště a jejich SLA propustnost vstupně-výstupních operací a úložiště, najdete v tématu [dokumentace k Azure pro spravované disky](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="configuring-the-storage-for-azure-virtual-machines"></a>Konfigurace úložiště pro virtuální počítače Azure

Zatím jste nikdy museli záleží vstupně-výstupních subsystémů a její možnosti. Důvodem bylo, že se na dodavatele zařízení potřeba zajistit, že jsou splněny minimální úložiště pro SAP HANA. Během vytváření infrastruktury Azure sami, byste také měli vědět některé z těchto požadavků. A také pochopit požadavky na konfiguraci navržený v následujících částech. Nebo pro případy, kde konfigurujete virtuální počítače spouštění řešení SAP HANA. Některé vlastnosti, které se zobrazí výzva jsou výsledkem je potřeba:

- Povolit čtení a zápis svazek na **/hana/log** z 250 MB/s minimálně s velikostí vstupně-výstupních operací 1 MB
- Povolit čtení aktivity minimálně 400 MB/s pro **/hana/dat** pro vstupně-výstupní operace velikosti 16 MB a 64 MB
- Povolit zápis aktivity alespoň 250 MB/s pro **/hana/dat** s 16 MB a 64 MB. velikost vstupně-výstupních operací

Úložiště s nízkou latencí je uvedený zásadní pro systémy DBMS, dokonce i DBMS, jako je SAP HANA, zachovat data v paměti. Kritická cesta v úložišti je obvykle kolem zápisy protokolu transakce systémů DBMS. Můžete ale také operace, jako je vytváření úložných bodů nebo načítají data v paměti se po obnovení při havárii může být důležité. Proto je nutné využívat disky Azure Premium pro **/hana/data** a **/hana/log** svazky. Abyste dosáhli minimální propustnost **/hana/log** a **/hana/dat** podle potřeby SAP, budete muset sestavit RAID 0 pomocí MDADM nebo LVM přes několik disků Azure Premium Storage. A používat svazky RAID jako **/hana/data** a **/hana/log** svazky. Podle velikosti stripe RAID 0 doporučení je použít:

- 64 KB nebo 128 KB pro   **/hana/dat**
- 32 KB pro   **/hana/log**

> [!NOTE]
> Nemusíte konfigurovat žádné úroveň redundance pomocí svazky RAID, protože Azure Premium a Standard storage udržují tři Image virtuálního pevného disku. Využití diskového pole RAID svazek je čistě konfigurace svazků, které poskytuje dostatečnou propustnost vstupně-výstupních operací.

Shromažďování počtu virtuálních pevných disků Azure pod RAID, je kumulativní straně propustnost vstupně-výstupních operací a úložiště. Proto když vložíte RAID 0 přes 3 x disky P30 Azure Premium Storage, se vám měl dát třikrát IOPS a třikrát propustnost úložiště jednoho disku Azure Premium Storage P30.

Ukládání do mezipaměti doporučení níže jsou za předpokladu, že vlastnosti vstupně-výstupních operací pro SAP HANA tento seznam jako:

- Existuje téměř jakékoli další úlohy proti HANA datových souborů. Výjimky jsou velké velikosti vstupně-výstupních operací po restartování instance HANA nebo data se načtou do HANA. Další možnost je z větší čtení že vstupně-výstupních operací s datovými soubory mohou být zálohy databáze HANA. V důsledku většinou ukládání do mezipaměti pro čtení nedává smysl, protože ve většině případů, všechny datové svazky souborů je potřeba načíst úplně.
- Zápis proti datových souborů dochází v nárůsty zatížení na základě úložných bodů HANA a obnovení při havárii pro HANA. Zápis úložných bodů je asynchronní a nejsou pojme jakékoli uživatelské transakce. Zápis dat během obnovení při havárii je výkon kritických zajistí systému reagovat rychle znovu. Nicméně obnovení při havárii by měl být raději výjimečné situace
- Existují téměř všechny operace čtení ze souborů znovu HANA. Výjimky jsou velkými vstupy a výstupy při provádění zálohy protokolu transakcí, obnovení při havárii, nebo ve fázi restartování instance HANA.  
- Hlavní zátěž v SAP HANA znovu protokolu jsou zápisy. Závislé na povaze úloh, můžete mít vstupně-výstupních operací malá jako 4 KB nebo v jiných případech vstupně-výstupní operace velikosti nejméně 1 MB. Zapsat latence proti protokolu znovu SAP HANA je důležité výkonu.
- Všechny operace zápisu musí být trvale uloženého na disku spolehlivé způsobem

V důsledku těchto zjištěné vzory vstupů/výstupů SAP Hana měla by být ukládání do mezipaměti pro jiné svazky za využití Azure Premium Storage nastavena jako:

- **/ hana/dat** -neexistující ukládání do mezipaměti
- **/ hana/log** – žádné ukládání do mezipaměti - výjimka pro řadu M-Series (viz dále v tomto dokumentu)
- **/ hana/sdílené** – pro čtení, ukládání do mezipaměti


Mějte také celkovou propustnost vstupně-výstupní operace virtuálního počítače při změně velikosti nebo rozhodování o tom, pro virtuální počítač. Celkovou propustnost úložiště virtuálního počítače najdete v článku [paměťově optimalizované velikosti virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

#### <a name="linux-io-scheduler-mode"></a>Režim Plánovač vstupně-výstupních operací systému Linux
Linux má několik různých režimech plánování vstupně-výstupních operací. Prostřednictvím Linux dodavateli a SAP doporučuje nastavit režim Plánovač vstupně-výstupních operací pro diskové svazky klávesou **cfq** režimu, aby **noop** režimu. Podrobnosti jsou odkazovány v [1984798 # Poznámka SAP](https://launchpad.support.sap.com/#/notes/1984787). 


#### <a name="storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Řešení úložiště pomocí Azure Write Accelerator pro virtuální počítače Azure řady M-Series
Akcelerátor zápisu Azure je funkce, které je Začínáme zavádět pro virtuální počítače Azure řady M-Series výhradně. Jak uvádí název účelem funkce je zlepšit latenci vstupu/výstupu zápisů ve službě Azure Storage úrovně Premium. Pro SAP HANA, by měla použít u akcelerátorem zápisu **/hana/log** pouze svazku. Konfigurace zobrazí, pokud je proto potřeba změnit. Hlavní změny je rozložení mezi **/hana/data** a **/hana/log** Chcete-li použít akcelerátor zápisu Azure proti **/hana/log** pouze svazku. 

> [!IMPORTANT]
> Certifikace SAP HANA pro virtuální počítače Azure řady M-Series je výhradně s Azure Write Accelerator pro **/hana/log** svazku. V důsledku toho se očekává mít nakonfigurovanou Azure Write Accelerator pro produkční scénáře nasazení SAP HANA na virtuálních počítačích Azure řady M-Series **/hana/log** svazku.  

> [!NOTE]
> Pro produkční scénáře, zkontrolujte, zda určitého typu virtuálních počítačů, je podporován pro SAP HANA SAP v [dokumentaci k SAPU pro IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Doporučená konfigurace vypadat takto:

| SKLADOVOU POLOŽKU VIRTUÁLNÍHO POČÍTAČE | Paměť RAM | Max. VM I/O<br /> Propustnost | / hana/dat | / hana/log | / hana/sdílené | / root svazku | / usr/sap | Hana/zálohování |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

Zkontrolujte, jestli bude vyhovovat propustnost úložiště pro různé svazky navrhované na zatížení, které chcete spustit. Pokud úloha vyžaduje větší svazky pro **/hana/data** a **/hana/log**, je potřeba zvýšit počet virtuálních pevných disků Storage úrovně Premium pro Azure. Pro změnu velikosti svazku s více virtuálními pevnými disky než uvedené zvýší propustnost vstupně-výstupních operací a vstupně-výstupní operace v rámci omezení typu virtuálního počítače Azure.

Azure Write Accelerator pouze funguje ve spojení s [Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Proto alespoň na disky Azure Premium Storage tvořící **/hana/log** svazků je nutné nasadit jako spravované disky.

Existují omezení Azure Premium Storage VHD na virtuální počítač, který může podporovat akcelerátor zápisu Azure. Aktuální omezení platí pro:

- 16 virtuálních pevných disků pro M128xx virtuálního počítače
- 8 virtuálních pevných disků pro M64xx virtuálního počítače
- 4 virtuální pevné disky pro M32xx virtuálního počítače

Podrobnější pokyny o tom, jak povolit akcelerátor zápisu Azure najdete v článku [akcelerátorem zápisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Podrobnosti a omezení pro akcelerátor zápisu Azure najdete v dokumentaci stejné.


#### <a name="cost-conscious-azure-storage-configuration"></a>Nákladů při provádění konfigurace služby Azure Storage
Následující tabulka ukazuje konfiguraci typy virtuálních počítačů, které zákazníci nejčastěji používají k hostiteli SAP HANA na virtuálních počítačích Azure. Můžou nastat některé typy virtuálních počítačů, které nemusí splňovat minimální všechna kritéria pro SAP HANA nebo nejsou oficiálně podporované se SAP HANA SAP. Ale zatím vypadal jako tyto virtuální počítače bez problémů provádět pro neprodukční prostředí. 

> [!NOTE]
> Pro produkční scénáře, zkontrolujte, zda určitého typu virtuálních počítačů, je podporován pro SAP HANA SAP v [dokumentaci k SAPU pro IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).


| SKLADOVOU POLOŽKU VIRTUÁLNÍHO POČÍTAČE | Paměť RAM | Max. VM I/O<br /> Propustnost | / hana/dat a/hana/log<br /> prokládané s LVM nebo MDADM | / hana/sdílené | / root svazku | / usr/sap | Hana/zálohování |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 432 GiB | 1200 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 |1 x S30 |
| M64s | 1000 GiB | 1000 MB/s | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


Disky, doporučuje se pro typy menších virtuálních počítačů s 3 x P20 oversize svazky týkajících se místa doporučení podle [SAP TDI úložiště dokument White Paper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Volba se zobrazuje v tabulce však byl proveden poskytuje dostatečnou propustnost disku pro SAP HANA. Pokud potřebujete změny **/hana/záloha** svazek, který se velikostí pro uchování zálohy, které představují dvakrát objem paměti, můžete upravit.   
Zkontrolujte, jestli bude vyhovovat propustnost úložiště pro různé svazky navrhované na zatížení, které chcete spustit. Pokud úloha vyžaduje větší svazky pro **/hana/data** a **/hana/log**, je potřeba zvýšit počet virtuálních pevných disků Storage úrovně Premium pro Azure. Pro změnu velikosti svazku s více virtuálními pevnými disky než uvedené zvýší propustnost vstupně-výstupních operací a vstupně-výstupní operace v rámci omezení typu virtuálního počítače Azure. 

> [!NOTE]
> Konfigurace výše by NIJAK přínosné [jeden virtuální počítač Azure SLA k virtuálním počítačům](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) protože používá kombinaci Azure Premium Storage a Azure Standard Storage. Výběr však byla vybrána k optimalizaci nákladů. Je třeba vybrat Premium Storage pro všechny disky výše uvedený jako úložiště Azure úrovně Standard (Sxx), aby konfigurace virtuálního počítače kompatibilní s Azure SLA jednoho virtuálního počítače.


> [!NOTE]
> Doporučené konfigurace disku uvedeno cílíte minimální požadavky, které SAP vyjadřuje směrem k příslušné infrastrukturu poskytovatele. V nasazení skutečných zákazníků a scénáře úloh situacích došlo k kde tato doporučení stále neposkytl dostatečná možnosti. Toto může nastat situace, kdy zákazník vyžaduje rychlejší opětovné načtení dat po restartu HANA nebo kde zálohování konfigurace vyžaduje větší šířku pásma pro úložiště. Jindy zahrnuté **/hana/log** kde 5000 vstupně-výstupních operací nejsou dostatečná pro konkrétní úlohu. Proto Využijte tato doporučení jako počáteční bod a přizpůsobení podle požadavků zatížení.
>  

### <a name="set-up-azure-virtual-networks"></a>Nastavení virtuální sítě Azure
Pokud máte připojení site-to-site do Azure přes VPN nebo ExpressRoute, musíte mít aspoň jednu virtuální síť Azure, který je připojený prostřednictvím virtuální brány k okruhu ExpressRoute nebo VPN. V jednoduchých nasazení je možné nasadit virtuální brány v podsítě virtuální sítě Azure (VNet), který je hostitelem instance systému SAP HANA i. Instalace SAP HANA, vytvořte dva další podsítě v rámci virtuální sítě Azure. Jednu podsíť hostuje virtuální počítače ke spuštění instance systému SAP HANA. Další podsítě spustí Jumpbox nebo virtuální počítače pro správu, k hostování SAP HANA Studio, další software pro správu nebo aplikační software.

> [!IMPORTANT]
> Mimo funkce, ale další důležité z důvodů výkonu, není možné konfigurovat [Azure síťových virtuálních zařízení](https://azure.microsoft.com/solutions/network-appliances/) v komunikační trasa mezi aplikací SAP a úroveň databázového systému SAP NetWeaver Hybris nebo S/4HANA na systému SAP. Komunikace mezi aplikační vrstvě SAP a vrstvu DBMS musí být s přímým přístupem. Omezení nezahrnuje [Azure ASG a skupiny zabezpečení sítě pravidla](https://docs.microsoft.com/azure/virtual-network/security-overview) tak dlouho, dokud tato pravidla ASG a skupiny zabezpečení sítě umožní přímé komunikaci. Další scénáře, ve kterém nejsou podporovány síťová virtuální zařízení jsou v komunikační trasy mezi virtuálními počítači Azure, které představují uzly clusteru Linux Pacemaker SBD zařízení a jak je popsáno v [vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse). Nebo v komunikaci se trasy mezi virtuálními počítači Azure a Windows Server SOFS nastavit až, jak je popsáno v [instanci SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s využitím sdílené složky v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). Síťová virtuální zařízení v komunikaci cesty může snadno dvakrát latence sítě mezi dva partneři komunikaci, můžete omezit propustnost v kritické cesty mezi aplikační vrstvě SAP a vrstvu DBMS. V některých scénářích zjištěnými se zákazníky může způsobit síťová virtuální zařízení Pacemaker Linuxové clustery selhání v případech, kdy je potřeba komunikace mezi uzly clusteru Linux Pacemaker sdělit své zařízení SBD přes síťové virtuální zařízení.  
> 

> [!IMPORTANT]
> Další návrh, který je **není** zodpovědnosti aplikační vrstvě SAP a vrstvu DBMS do různých virtuálních sítí Azure, které nejsou podporovány je [v partnerském vztahu](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) mezi sebou. Doporučujeme oddělit aplikační vrstvě SAP a DBMS vrstvy pomocí podsítí v rámci virtuální sítě Azure, namísto použití různých virtuálních sítích Azure. Pokud se rozhodnete postupujte podle doporučení a místo toho oddělit dvě vrstvy do jiné virtuální sítě, dvě virtuální sítě musí být [v partnerském vztahu](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Mějte na paměti, které síťový provoz mezi dvěma [v partnerském vztahu](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) virtuálním sítím Azure jsou předmětem náklady na přenos. S velkou datový svazek v po mnoho terabajtů, které se vyměňují mezi aplikační vrstvě SAP a DBMS vrstvu můžete sbírají značné náklady, pokud aplikační vrstvě SAP a DBMS vrstvy je rozdělen mezi dvěma partnerskými virtuálními sítěmi Azure. 

Při instalaci virtuálních počítačů pro spuštění SAP HANA, třeba virtuální počítače:

- Dvě virtuální síťové karty nainstalované: jednu síťovou kartu pro připojení k podsíti správy a jednu síťovou kartu pro připojení z místní sítě a jiných sítí, na instance SAP HANA ve virtuálním počítači Azure.
- Statické privátní IP adresy, které jsou nasazené pro obě virtuální síťové karty.

> [!NOTE]
> Byste měli přiřadit statické IP adresy pomocí Azure znamená, že na jednotlivé virtuální síťové adaptéry. Pro virtuální síťový adaptér by neměl přiřadit statické IP adresy v rámci hostovaného operačního systému. Některé služby Azure, jako je služba Azure Backup závisí na skutečnost, který na alespoň primárního virtuálního síťového adaptéru je nastavena, DHCP, ne statické IP adresy. Viz také dokument [Poradce při potížích s Azure se zálohováním virtuálních počítačů](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Pokud je potřeba přiřadit více statických IP adres k virtuálnímu počítači, musíte přiřadit víc karet Vnic k virtuálnímu počítači.
>
>

Ale pro nasazení, které jsou enduring, budete muset vytvořit virtuální datové centrum síťové architektury v Azure. Tato architektura se doporučuje oddělení brány virtuální sítě Azure, která se připojuje k místním do samostatné virtuální sítě Azure. Tento samostatný virtuální sítě by měl hostovat veškerý provoz, které se zasílají buď místní nebo k Internetu. Tento přístup umožňuje nasadit software pro auditování a protokolování provoz, který zadá virtuální datové centrum Azure v tento samostatný virtuální síti centra. Je proto nutné jednu virtuální síť, který je hostitelem veškerý software a konfigurace, které se týkají in - a odchozí provoz do nasazení vašeho řešení Azure.

Články [virtuální datové centrum Azure: Z hlediska sítě](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) a [virtuální datové centrum Azure a rovina podnikového řízení](https://docs.microsoft.com/azure/architecture/vdc/) poskytují další informace o virtuální datové centrum a související návrh virtuální sítě Azure.


>[!NOTE]
>Provoz, který probíhá mezi virtuální síti centra a pomocí virtuální sítě paprsků [Azure VNet peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) je předmětem další [náklady](https://azure.microsoft.com/pricing/details/virtual-network/). Na základě těchto nákladů, možná budete muset zvažte učinění ohrožení mezi systémem striktní návrh hvězdicové sítě a spouští několik [Azure ExpressRoute Gateway](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) připojit k 'paprsky' Pokud chcete vynechat, VNet peering. Však zavést další Azure ExpressRoute Gateway [náklady](https://azure.microsoft.com/pricing/details/vpn-gateway/) také. Můžete také setkat další náklady na software jiných výrobců, které používáte pro síťový provoz, protokolování, auditování a monitorování. Závisí na náklady pro výměnu dat prostřednictvím partnerského vztahu virtuálních sítí na jedné straně a náklady na vytvořené další brány Azure ExpressRoute a další softwarové licence, můžete se rozhodnout pro mikrosegmentaci v rámci jedné virtuální síti pomocí podsítí jako jednotka izolace místo virtuálních sítí.


Přehled různých metod pro přiřazení IP adres najdete v tématu [typy IP adres a metody přidělování v Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Pro virtuální počítače spouštějí SAP HANA byste měli spolupracovat s statické IP adresy přiřazené. Důvodem je, že některé atributy konfigurace pro HANA odkazují na IP adresy.

[Azure skupiny zabezpečení sítě (Nsg)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) se používají ke směrování provozu, který se směruje do instance SAP HANA nebo jumpbox. Skupiny zabezpečení sítě a nakonec [skupiny zabezpečení aplikací](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) jsou přidružené k podsíti SAP HANA a podsítě pro správu.

Následující obrázek znázorňuje přehled hrubý nasazení schématu pro SAP HANA po hvězdicové architektury virtuální sítě:

![Hrubý nasazení schématu pro SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)

SAP HANA v Azure nasadit bez připojení site-to-site, stále chcete stínit instance SAP HANA z veřejného Internetu a skrýt za dopředné proxy serverem. V tomto scénáři základní nasazení spoléhá na Azure integrované služby DNS přeložit názvy hostitelů. Ve složitějším nasazení, kde veřejně přístupnou IP adresy se používají jsou obzvláště důležité, Azure integrované služby DNS. Pomocí skupin zabezpečení sítě Azure a [síťových virtuálních zařízení Azure](https://azure.microsoft.com/solutions/network-appliances/) k řízení, monitorování, směrování z Internetu do vaší virtuální síti Azure architektury v Azure. Následující obrázek ukazuje hrubý schéma pro nasazení SAP HANA bez připojení site-to-site v rozbočovač a uvedenou architekturu virtuální sítě:
  
![Hrubý nasazení schématu pro SAP HANA bez připojení site-to-site](media/hana-vm-operations/hana-simple-networking2.PNG)
 

Další popis o tom, jak používat Azure síťová virtuální zařízení na sledování a řízení přístupu z Internetu bez centra a architekturu virtuální sítě paprsků najdete v článku [nasazení vysoce dostupných síťových virtuálních zařízení](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>Konfigurace infrastruktury Azure pro horizontální navýšení kapacity SAP HANA
Microsoft má jeden SKU virtuálních počítačů řady M-Series, s certifikací SAP HANA konfigurace škálování na víc systémů. Typ virtuálního počítače M128s je teď má certifikaci pro škálování až 16 uzlů. Změny v certifikace škálování na více instancí SAP HANA na virtuálních počítačích Azure, zkontrolujte [seznam certifikací platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Minimální verze operačního systému pro nasazení horizontální navýšení kapacity konfigurací ve virtuálních počítačích Azure jsou:

- SUSE Linux 12 SP3
- Red hat Linux 7.4

Horizontální navýšení kapacity certifikace 16 uzlů

- Hlavní uzel je jeden uzel
- Maximální počet 15 uzlů se pracovní uzly

>[!NOTE]
>Horizontální navýšení kapacity nasazení virtuálního počítače Azure není možné používat pohotovostní uzel
>

Důvod nebude moct konfigurovat pohotovostní uzel jsou dva účely:

- Azure má v tuto chvíli žádné nativní služby systému souborů NFS. V důsledku sdílených složek NFS je potřeba nakonfigurovat pomocí funkce třetích stran.
- Žádná konfigurace systému souborů NFS třetích stran můžou ke splnění kritérií latence úložiště pro SAP HANA s jejich řešení nasazená v Azure.

V důsledku toho **/hana/data** a **/hana/log** svazky nelze sdílet. Nesdílí tyto svazky jednotlivé uzly, zabraňuje využití pohotovostní uzel SAP HANA v konfiguraci horizontální navýšení kapacity.

V důsledku základním návrhu pro jeden uzel v konfiguraci horizontální navýšení kapacity bude vypadat takto:

![Základní informace o škálování na víc systémů z jediného uzlu](media/hana-vm-operations/scale-out-basics.PNG)

Základní konfigurace uzlu virtuálního počítače pro horizontální navýšení kapacity SAP HANA vypadá takto:

- Pro **/hana/sdílené**, sestavování clusteru pro systém souborů NFS podle operačního systému SUSE Linux 12 SP3 s vysokou dostupností. Tento cluster bude hostovat **/hana/sdílené** sdílených složek NFS horizontální navýšení kapacity konfigurací a SAP NetWeaver nebo BW/4HANA centrální služby. Dokumentace k sestavení taková konfigurace je k dispozici v článku [vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- Všechny ostatní diskové svazky jsou **není** sdílet mezi různými uzly a jsou **není** založené na systému souborů NFS. Konfigurace instalace a postupy pro horizontální navýšení kapacity HANA instalace s nesdílené **/hana/data** a **/hana/log** je k dispozici níže v tomto dokumentu.

>[!NOTE]
>S vysokou dostupností clusteru systému souborů NFS se zobrazuje na obrázcích, pokud se podporuje s operačním systémem SUSE Linux pouze. Vysoce dostupné řešení systému souborů NFS založeného na Red Hat budete informováni, později.

Změna velikosti svazků pro uzly s výjimkou je stejná jako vertikálně navýšit kapacitu, **/hana/sdílené**. Pro skladovou Položku virtuálního počítače M128s doporučené velikosti a typy vypadat:

| SKLADOVOU POLOŽKU VIRTUÁLNÍHO POČÍTAČE | Paměť RAM | Max. VM I/O<br /> Propustnost | / hana/dat | / hana/log | / root svazku | / usr/sap | Hana/zálohování |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P6 | 1 x P6 | 2 x P40 |


Zkontrolujte, jestli bude vyhovovat propustnost úložiště pro různé svazky navrhované na zatížení, které chcete spustit. Pokud úloha vyžaduje větší svazky pro **/hana/data** a **/hana/log**, je potřeba zvýšit počet virtuálních pevných disků Storage úrovně Premium pro Azure. Pro změnu velikosti svazku s více virtuálními pevnými disky než uvedené zvýší propustnost vstupně-výstupních operací a vstupně-výstupní operace v rámci omezení typu virtuálního počítače Azure. Platí také akcelerátor zápisu Azure pro disky, které tvoří **/hana/log** svazku.
 
V dokumentu [požadavky na úložiště pro SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), vzorec má název, který definuje velikost **/hana/sdílené** svazek pro horizontální navýšení kapacity jako velikost paměti z jednoho pracovního uzlu na čtyři uzly pracovního procesu.

Předpokládáme, že provedete SAP HANA horizontální navýšení kapacity certifikovaných M128s virtuálního počítače Azure s přibližně 2 TB paměti a doporučení SAP lze shrnout jako:

- Jeden hlavní uzel a až čtyři pracovní uzel, **/hana/sdílené** by musí být 2 TB velikosti svazku. 
- Jeden hlavní uzel a pět a 8 pracovních uzlů, velikost **/hana/sdílené** by měl být 4 TB. 
- Jeden hlavní uzel a 9 až 12 pracovních uzlů, velikost 6 TB pro **/hana/sdílené** by bylo zapotřebí. 
- Jeden hlavní uzel a pomocí 12 až 15 pracovních uzlů, které jsou nezbytné **/hana/sdílené** svazek, který je ve velikosti 8 TB.

Další důležité návrh, který se zobrazí v grafice konfiguraci jednoho uzlu virtuálního počítače s horizontálním navýšením SAP HANA je virtuální síť nebo lépe konfiguraci podsítě. SAP důrazně doporučuje oddělit internetový provoz klienta nebo aplikace z komunikace mezi uzly HANA. Jak je znázorněno na obrázcích, tento cíl se dosahuje tím, že dvě různé karet Vnic, které jsou připojené k virtuálnímu počítači. Oba virtuální síťové adaptéry jsou v různých podsítích, mají dvou různých IP adresách. Potom řízení toku provozu pomocí pravidel směrování pomocí skupin zabezpečení sítě nebo trasy definované uživatelem.

Zejména v Azure neexistují žádné prostředky a metody k vynucení kvalitu služby a kvóty na konkrétní virtuální síťové adaptéry. V důsledku toho oddělení zákazníky i meziuzlové komunikaci klienta nebo aplikace se neotevře příležitosti k určení priority jednoho datového proudu provoz z nich. Místo toho oddělení zůstane míru zabezpečení v stínění meziuzlové komunikaci horizontální navýšení kapacity konfigurací.  

>[!IMPORTANT]
>SAP důrazně doporučuje oddělení síťového provozu na straně klienta nebo aplikace a provoz uzlů, jak je popsáno v tomto dokumentu. Doporučujeme proto uvedení architekturu na místě, jak je znázorněno v poslední grafiky.
>

Z hlediska sítě minimální požadovaná síťová architektura vypadat nějak takto:

![Základní informace o škálování na víc systémů z jediného uzlu](media/hana-vm-operations/scale-out-networking-overview.PNG)

Omezení podporována, pokud jsou další jeden hlavní uzel 15 pracovního procesu.

Z hlediska úložiště architektura úložiště vypadat nějak takto:


![Základní informace o škálování na víc systémů z jediného uzlu](media/hana-vm-operations/scale-out-storage-overview.PNG)

**/Hana/sdílené** svazek umístěn na vysoce dostupné konfiguraci sdílené složky systému souborů NFS. Zatímco všechny ostatní disky jsou: místně připojené k jednotlivým virtuálním počítačům. 

### <a name="highly-available-nfs-share"></a>Vysoce dostupné sdílené složky systému souborů NFS
S vysokou dostupností clusteru systému souborů NFS zatím pracuje s operačním systémem SUSE Linux pouze. Dokument [vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) popisuje, jak ho nastavit. Pokud clusteru systému souborů NFS nesdílejte s další konfigurace HANA mimo virtuální síť azure, která se spouští instance systému SAP HANA, nainstalujte ji do stejné virtuální síti. Nainstalujte ho ve vlastní podsíti a ujistěte se, že ne všechny libovolného provozu přístup k podsíť. Místo toho byste měli omezit přenosy do této podsítě na IP adresy virtuálního počítače, spusťte provoz do **/hana/sdílené** svazku.

Související s virtuální síťový adaptér virtuálního počítače HANA horizontální navýšení kapacity, který by měl směrovat **/hana/sdílené** provoz, doporučení jsou:

- Od provozu do **/hana/sdílené** je střední, směrování, která je přiřazena k síti klienta v minimální požadavky na konfiguraci virtuálního síťového adaptéru
- Nakonec pro provoz do **/hana/sdílené**, nasaďte třetí podsíti ve virtuální síti nasadíte horizontální navýšení kapacity konfigurací SAP HANA a virtuálního síťového adaptéru přiřadit třetí, která je hostována v této podsíti. Pomocí třetího virtuálního síťového adaptéru a přidružené IP adresy pro provoz do sdílené složky systému souborů NFS. Pak můžete použít samostatné přístup a pravidla směrování.

>[!IMPORTANT]
>Síťový provoz mezi virtuální počítače, které mají SAP HANA způsobem horizontální navýšení kapacity, nasazení a systému souborů NFS s vysokou dostupností může za žádných okolností směrován přes [síťové virtuální zařízení](https://azure.microsoft.com/solutions/network-appliances/) nebo podobné virtuální zařízení. Zatímco skupiny zabezpečení sítě Azure se nevyžaduje těchto zařízení. Zkontrolujte vaše pravidla směrování, pokud chcete mít jistotu, že jsou zkrácen síťová virtuální zařízení nebo podobné virtuální zařízení při přístup k vysoce dostupné sdílené složky NFS z virtuálních počítačů spouštějí SAP HANA.
> 

Pokud chcete sdílet s vysokou dostupností clusteru systému souborů NFS mezi konfiguracemi SAP HANA, přesuňte všechny tyto konfigurace HANA do stejné virtuální síti. 
 

### <a name="installing-sap-hana-scale-out-n-azure"></a>Instalace Azure n škálování na více instancí SAP HANA
Instalace konfigurace SAP horizontální navýšení kapacity, musíte provést kroky k hrubé:

- Nasazování nových nebo úpravu nové infrastruktury virtuální sítě Azure
- Nasazení nových virtuálních počítačů pomocí Azure Storage úrovně Premium Managed svazky
- Nasazení nového nebo přizpůsobit existující cluster s vysokou dostupností systému souborů NFS
- Přizpůsobit, abyste měli jistotu, že, například meziuzlové komunikaci mezi virtuálními počítači není směrován přes směrováním v síti [síťové virtuální zařízení](https://azure.microsoft.com/solutions/network-appliances/). Totéž platí pro přenosy mezi virtuálními počítači a s vysokou dostupností clusteru systému souborů NFS.
- Instalace hlavního uzlu SAP HANA.
- Přizpůsobení parametrů konfigurace hlavního uzlu SAP HANA
- Pokračovat v instalaci pracovních uzlů SAP HANA

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>Instalace SAP HANA v konfiguraci horizontální navýšení kapacity
Nasazení infrastruktury virtuálních počítačů Azure a dalším krokům hotovi, budete muset nainstalovat horizontální navýšení kapacity konfigurací SAP HANA v těchto krocích:

- Instalace SAP HANA hlavní uzel podle dokumentace společnosti SAP.
- **Po instalaci budete muset změnit global.ini soubor a přidejte parametr "basepath_shared = ne ' pro global.ini**. Tento parametr povolí SAP HANA po spuštění v Škálováním bez "sdílené" **/hana/data** a **/hana/log** svazky mezi uzly. Podrobnosti jsou popsány v [2080991 # Poznámka SAP](https://launchpad.support.sap.com/#/notes/2080991).
- Po změně parametr global.ini, restartuje instanci SAP HANA
- Přidáte pracovní uzly. Viz také <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html>. Zadejte interní síť pro SAP HANA komunikace v rámci uzlu během instalace nebo později, například pomocí, místní hdblcm. Podrobnější dokumentaci najdete v článku také [2183363 # Poznámka SAP](https://launchpad.support.sap.com/#/notes/2183363). 

Následující rutina tento instalační program škálovatelných konfigurací, které jste nainstalovali bude používat-sdílené disky pro spuštění **/hana/data** a **/hana/log**. Vzhledem k tomu **/hana/sdílené** svazku se umístí na vysoce dostupných sdílených složek NFS.


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>SAP HANA dynamické ovládání datových vrstev na 2.0 pro virtuální počítače Azure

Kromě certifikací SAP HANA na virtuálních počítačích Azure řady M-series, SAP HANA dynamické ovládání datových vrstev na 2.0 také podporuje v Microsoft Azure (viz dokumentace k SAP HANA dynamické ovládání datových vrstev na odkazy níže). Neplatí žádné rozdíly v instalaci nebo ji operační, například přes SAP HANA řídicí panel ve virtuálním počítači Azure, existuje pár důležitých položek, které jsou povinné pro oficiální podporu k Azure. Tyto klíčové body jsou popsané níže. V celém článku – zkratka "DT 2.0" se použijí místo celé jméno dynamické ovládání datových vrstev na 2.0.

SAP HANA dynamické ovládání datových vrstev na 2.0 nepodporuje SAP BW nebo S4HANA. Případy použití hlavní teď jsou nativní aplikace HANA.


### <a name="overview"></a>Přehled

Následující obrázek poskytuje přehled ohledně podpory DT 2.0 v Microsoft Azure. Existuje sada povinné požadavky, který má být dodržována dodržovat oficiální certifikaci:

- DT 2.0 musí být nainstalován na vyhrazených virtuálních počítačích Azure. Nelze spustit na jednom virtuálním počítači, kde běží SAP HANA
- SAP HANA a virtuální počítače DT 2.0 musí být nasazeny v rámci stejné virtuální síti Azure
- SAP HANA a virtuální počítače DT 2.0 musí být nasazeny s povolenými akcelerovanými síťovými službami Azure
- Typ úložiště pro virtuální počítače DT 2.0 musí být Azure Storage úrovně Premium
- Více disků v Azure musí být připojené k virtuálnímu počítači DT 2.0
- Je potřeba vytvořit raid softwaru / prokládaných svazků (buď prostřednictvím lvm nebo mdadm) použitím prokládáním pro disky Azure

Další podrobnosti se vysvětlené v následujících částech.

![Přehled 2.0 architektury DT SAP HANA](media/hana-vm-operations/hana-dt-20.PNG)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>Vyhrazený virtuální počítač Azure pro SAP HANA DT 2.0

Na Azure IaaS DT 2.0 je podporována pouze na vyhrazený virtuální počítač. Není povoleno spustit DT 2.0 na stejném virtuálním počítači Azure se spuštěným systémem HANA instance. Zpočátku dva typy virtuálních počítačů lze použít ke spuštění SAP HANA DT 2.0:

- M64-32ms 
- E32sv3 

Zobrazit popis typu virtuálního počítače [zde](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Základní myšlenka DT 2.0, což je o snižování zátěže "teplé" data, aby bylo možné ušetřit uveden je vhodné použít odpovídající velikosti virtuálních počítačů. I když týkající se možných kombinací neexistuje žádné striktní pravidlo. To závisí na pracovním vytížení konkrétního zákazníka.

Doporučená konfigurace by byl:

| Typ virtuálního počítače SAP HANA | Typ virtuálního počítače DT 2.0 |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


Všechny kombinace s certifikací SAP HANA virtuální počítače řady M-series s podporovaných DT 2.0 virtuálních počítačů (M64 32ms a E32sv3) jsou možné.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Sítě Azure a SAP HANA DT 2.0

Propustnost sítě mezi tímto DT 2.0 a SAP HANA VM z 10 Gb minimální instalaci DT 2.0 na vyhrazený virtuální počítač vyžaduje. Proto je nutné umístit všechny virtuální počítače ve stejné virtuální síti Azure a povolit akcelerované síťové služby Azure.

Zobrazit další informace o akcelerovaných síťových služeb Azure [zde](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)

### <a name="vm-storage-for-sap-hana-dt-20"></a>Úložiště virtuálního počítače pro SAP HANA DT 2.0

Podle DT 2.0 nejlepších praktik propustnost vstupně-výstupních operací disku by měl být minimální 50 MB/s každé fyzické jádro. Prohlížení specifikace pro dva typy virtuálních počítačů Azure, které jsou podporovány pro DT 2.0, jeden disk maximální limit propustnosti vstupně-výstupních operací pro virtuální počítač zobrazí:

- E32sv3    :   768 MB/s (bez mezipaměti) to znamená, že poměr 48 MB za sekundu podle počtu fyzických jader
- M64-32ms  :  1 000 MB za sekundu (bez mezipaměti) to znamená, že poměr 62,5 MB za sekundu podle počtu fyzických jader

Je potřeba připojit k virtuálnímu počítači 2.0 DT více disků v Azure a vytvořit software raid (prokládáním) na úrovni operačního systému k dosažení maximálního limitu propustnost disků jednotlivých virtuálních počítačů. Jeden disk Azure nemůže poskytnout propustnost pro dosažení maximálního limitu virtuálních počítačů v této souvislosti. Azure Premium storage je povinné pro spuštění DT 2.0. 

- Najdete podrobnosti o typech disků k dispozici Azure [zde](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)
- Najdete podrobnosti o vytváření softwaru diskového pole raid prostřednictvím mdadm [zde](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
- Podrobnosti o konfiguraci LVM vytvořit prokládané svazku pro maximální propustnosti můžete najít [zde](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)

V závislosti na požadavcích velikost existují různé možnosti pro dosažení maximální propustnosti virtuálního počítače. Tady je konfigurace disků objem dat pro každý typ virtuálního počítače DT 2.0 k dosažení horní limit propustnosti virtuálního počítače. Virtuální počítač E32sv3 by měly být považovány za úroveň položka pro úlohy menší. V případě, že ji by měl ukázat, že není rychlé dostatečně může být potřeba změnit velikost virtuálního počítače do M64 32ms.
Jako virtuální počítač M64 32ms má velikost paměti, zatížení vstupně-výstupní operace nemusí dosáhnout omezení zejména pro úlohy náročné na čtení. Proto může být méně disky v sadě stripe dostatečná v závislosti na konkrétních úloh zákazníka. Ale bude v bezpečí disku se rozhodli zajistit maximální propustnost následujících konfigurací:


| SKLADOVOU POLOŽKU VIRTUÁLNÍHO POČÍTAČE | Konfigurace disku 1 | Konfigurace disku 2 | Konfigurace disku 3 | Konfigurace disku 4 | Konfigurace disku 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3,5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2,5 TB | 6 x P15 -> 1,5 TB | 


Zejména v případě zatížení intenzivní čtení může zvýšit vstupně-výstupním výkonem k zapnutí nastavení v mezipaměti pro Azure hostitele "jen pro čtení" podle doporučení pro datové svazky databázový software. Zatímco transakce protokolu mezipaměti disku Azure hostitele musí být "none". 

Doporučené výchozí bod týkající se velikost svazku protokolu je Heuristika 15 % velikosti dat. Vytvoření svazku protokolu lze provést pomocí typů jiný disk Azure v závislosti na požadavcích náklady a propustnost. U protokolu se vyžaduje Vysoká propustnost vstupně-výstupních operací svazku.  V případě použití virtuálního počítače zadejte M64 32ms, důrazně doporučujeme povolit [akcelerátorem zápisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). Azure Write Accelerator poskytuje latence zápisu u optimální disku pro protokol transakcí (dostupné pouze pro řadu M-series). Existují některé položky, které byste měli zvážit, když jako maximální počet disků podle typu virtuálního počítače. Najdete podrobnosti o akcelerátorem zápisu [zde](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)


Tady je pár příkladů o určení velikosti svazku protokolu:

| Zadejte velikost svazku dat a disku | svazek s protokolem a disk zadejte konfigurace 1 | svazek s protokolem a disk zadejte config 2 |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2,5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1,5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


Například pro SAP HANA horizontální navýšení kapacity, musí /hana/shared directory sdílet mezi SAP HANA a virtuálním Počítačem 2.0 DT. Stejnou architekturu jako SAP HANA horizontální navýšení kapacity pomocí vyhrazené virtuální počítače, která působí jako vysoce dostupný server pro systém souborů NFS se doporučuje. Negace sdílené záložní svazek je možné identické návrhu. Ale až zákazník Pokud by bylo nutné vysokou dostupnost, nebo pokud stačí jenom použít vyhrazený virtuální počítač s dostatečnou kapacitou úložiště tak, aby fungoval jako záložní server.



### <a name="links-to-dt-20-documentation"></a>Odkazy na dokumentaci DT 2.0 

- [SAP HANA dynamické ovládání datových vrstev na instalaci a aktualizaci Průvodce](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [SAP HANA dynamické ovládání datových vrstev na kurzy a materiály](https://www.sap.com/developer/topics/hana-dynamic-tiering.html)
- [SAP HANA dynamické vrstvení PoC](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [SAP HANA 2.0 aktualizace Service PACKU 02 dynamické vrstvení vylepšení](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Operace pro nasazení SAP HANA na virtuálních počítačích Azure
Následující části popisují některé operace související s nasazováním systémů SAP HANA na virtuálních počítačích Azure.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Zálohování a obnovení operací na virtuálních počítačích Azure
Tyto dokumenty popisují, jak zálohovat a obnovit vaše nasazení SAP HANA:

- [Přehled zálohování SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Zálohování na úrovni souboru SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Srovnávací test snímků úložiště SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Spuštění a restartování virtuálních počítačů, které obsahují SAP HANA
Viditelného funkcí služby veřejného cloudu Azure je, že vám budeme účtovat jenom výpočetní minut. Například při vypnutí virtuálního počítače, na kterém běží SAP HANA, bude se vám účtovat jenom za náklady na úložiště během této doby. Další funkcí je k dispozici, když zadat statické IP adresy pro virtuální počítače v původním nasazení. Po restartování virtuálního počítače, který má SAP HANA, virtuální počítač se restartuje s jeho předchozí IP adresy. 


### <a name="use-saprouter-for-sap-remote-support"></a>Vzdálená podpora SAP pomocí SAProuter
Pokud máte připojení site-to-site mezi místními umístěními a Azure a komponenty SAP spouštíte, máte pravděpodobně již spuštěnou SAProuter. V takovém případě dokončete následující položky pro podporu vzdáleného:

- Udržujte privátní a statickou IP adresu virtuálního počítače, který je hostitelem SAP HANA v konfiguraci SAProuter.
- Nakonfigurujte skupiny zabezpečení sítě, který je hostitelem virtuálního počítače HANA povolený provoz přes TCP/IP port 3299 podsítě.

Pokud se připojujete k Azure prostřednictvím Internetu, a není nutné směrovač SAP pro virtuální počítač se SAP HANA, musíte nainstalovat součást. Nainstalujte SAProuter v samostatných virtuálních počítačů v podsíti správy. Hrubý schéma pro nasazení SAP HANA, bez připojení site-to-site a s SAProuter naleznete na následujícím obrázku:

![Bez připojení site-to-site a SAProuter surové nasazení schématu pro SAP HANA](media/hana-vm-operations/hana-simple-networking3.PNG)

Nezapomeňte nainstalovat SAProuter na samostatném virtuálním počítači a ne ve virtuálním počítači Jumpboxu. Samostatný virtuální počítač musí mít statickou IP adresu. Pro připojení vašich SAProuter k SAProuter, který je hostitelem SAP, obraťte se na SAP pro IP adresu. (SAProuter, který je hostitelem SAP je protějškem SAProuter instance, který nainstalujete na virtuální počítač.) Umožňuje nakonfigurovat instanci SAProuter adresu IP ze SAP. V nastavení konfigurace pouze nezbytné port je TCP port 3299.

Další informace o tom, jak nastavit a spravovat připojení vzdálenou podporu prostřednictvím SAProuter najdete v tématu [dokumentaci k SAPU](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Vysoká dostupnost se SAP HANA na nativních virtuálních počítačích Azure
Pokud používáte systém SUSE Linux Enterprise Server pro SAP aplikace 12 SP1 nebo novější, můžete vytvořit Pacemaker cluster s využitím techniky STONITH zařízení. Zařízení můžete použít k nastavení konfigurace služby SAP HANA, která používá synchronní replikace s systémové replikace HANA a automatické převzetí služeb při selhání. Další informace o postupu instalace najdete v tématu [Příručka pro vysokou dostupnost SAP HANA pro Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
