---
title: Operace SAP HANA v Azure | Dokumentace Microsoftu
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
ms.date: 04/24/2018
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2480ad464f2fc716cf68672387a189aeb92f5737
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37918828"
---
# <a name="sap-hana-on-azure-operations-guide"></a>SAP HANA v Azure Provozní příručka
Tento dokument obsahuje pokyny pro operační systémy SAP HANA, které jsou nasazené na nativních virtuálních počítačích Azure (VM). Tento dokument není určena k nahrazení standardní dokumentaci k SAPU, který obsahuje následující obsah:

- [Příručka věnovaná SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
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
> Li se o neprodukční scénářů použití typy virtuálních počítačů, které jsou uvedeny v [Poznámka SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Použití virtuálních počítačů Azure pro produkční scénáře, zkontrolujte, zda virtuální počítače v systému SAP publikována s certifikací SAP HANA [seznam certifikací platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Nasazení virtuálních počítačů v Azure s použitím:

- Na webu Azure portal.
- Rutiny Powershellu pro Azure.
- Azure CLI.

Také můžete nasadit kompletní instalované platformy SAP HANA ve službě Azure VM prostřednictvím [SAP Cloudovou platformu](https://cal.sap.com/). Proces instalace je popsána v [nasadit řešení SAP S/4HANA nebo BW/4HANA v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) nebo s automatizací vydané [tady](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

### <a name="choose-azure-storage-type"></a>Zvolte typ služby Azure Storage
Azure nabízí dva typy úložiště, které jsou vhodné pro virtuální počítače Azure, které se spouštějí SAP HANA:

- [Azure Storage úrovně Standard](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure nabízí dvě metody nasazení pro virtuální pevné disky na Azure na úrovni Standard a Premium Storage. Pokud celková scénář umožňuje, využít výhod [Azure spravovaný disk](https://azure.microsoft.com/services/managed-disks/) nasazení.

Seznam typů úložiště a jejich SLA propustnost vstupně-výstupních operací a úložiště, najdete v tématu [dokumentace k Azure pro spravované disky](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="configuring-the-storage-for-azure-virtual-machines"></a>Konfigurace úložiště pro virtuální počítače Azure

Jde jste zakoupili SAP HANA zařízení pro místní, nikdy museli záleží vstupně-výstupních subsystémů a jeho funkce, protože se na dodavatele zařízení potřebuje, abyste měli jistotu, že jsou splněny minimální úložiště pro SAP HANA. Během vytváření infrastruktury Azure sami, můžete také měli být vědomi některé z těchto požadavků také pochopit požadavky na konfiguraci, které doporučujeme v následujících částech. Nebo pro případy, kde konfigurujete virtuální počítače spouštění řešení SAP HANA. Některé vlastnosti, které se zobrazí výzva jsou výsledkem je potřeba:

- Povolit čtení a zápis svazek na /hana/log 250 MB/s minimálně s velikostí vstupně-výstupních operací 1 MB
- Povolit čtení aktivity minimálně 400MB/s pro /hana/data pro vstupně-výstupní operace velikosti 16 MB a 64 MB
- Povolit zápis aktivity alespoň 250MB za sekundu pro /hana/data s 16 MB a 64 MB. velikost vstupně-výstupních operací

Úložiště s nízkou latencí je uvedený zásadní pro systémy DBMS, dokonce i těm, jako je SAP HANA, zachovat data v paměti. Kritická cesta v úložišti je obvykle kolem zápisy protokolu transakce systémů DBMS. Můžete ale také operace, jako je vytváření úložných bodů nebo načítají data v paměti se po obnovení při havárii může být důležité. Proto je nutné využívat disky Azure Premium pro /hana/data a /hana/log svazky. Aby bylo možné dosáhnout minimální propustnost/hana/log a/hana/dat podle potřeby SAP, budete muset sestavit RAID 0 pomocí MDADM nebo LVM přes několik disků Azure Premium Storage a použít jako/hana/data a svazky s protokoly/hana/svazky RAID. Podle velikosti stripe RAID 0 doporučení je použít:

- 64 kB nebo 128 kB pro/hana/dat
- 32 kB pro/hana/log

> [!NOTE]
> Nemusíte konfigurovat žádné úroveň redundance pomocí svazky RAID, protože Azure Premium a Standard storage udržují tři Image virtuálního pevného disku. Využití diskového pole RAID svazek je čistě konfigurace svazků, které poskytuje dostatečnou propustnost vstupně-výstupních operací.

Shromažďování počtu virtuálních pevných disků Azure pod RAID, je kumulativní straně propustnost vstupně-výstupních operací a úložiště. Proto když vložíte RAID 0 přes 3 x disky P30 Azure Premium Storage, se vám měl dát třikrát IOPS a třikrát propustnost úložiště jednoho disku Azure Premium Storage P30.

Ukládání do mezipaměti doporučení níže jsou za předpokladu, že vlastnosti vstupně-výstupních operací pro SAP HANA tento seznam jako:

- Existuje téměř jakékoli další úlohy proti HANA datových souborů. Výjimky jsou velké velikosti vstupně-výstupních operací po restartování HANA instance nebo restartování virtuálního počítače Azure, když data se načtou do HANA. Další možnost je z větší čtení že vstupně-výstupních operací s datovými soubory mohou být zálohy databáze HANA. V důsledku většinou ukládání do mezipaměti pro čtení nedává smysl, protože ve většině případů, všechny datové svazky souborů je potřeba načíst úplně.
- Zápis proti datových souborů dochází v nárůsty zatížení na základě úložných bodů HANA a obnovení při havárii pro HANA. Zápis úložných bodů je asynchronní a nejsou pojme jakékoli uživatelské transakce. Zápis dat během obnovení při havárii je výkon kritických zajistí systému reagovat rychle znovu. Nicméně obnovení při havárii by měl být raději výjimečné situace
- Existují téměř všechny operace čtení ze souborů znovu HANA. Výjimky jsou velkými vstupy a výstupy při provádění zálohy protokolu transakcí, obnovení při havárii, nebo ve fázi restartování instance HANA.  
- Hlavní zátěž v SAP HANA znovu protokolu jsou zápisy. Závislé na povaze úloh, můžete mít vstupně-výstupních operací malá jako 4 KB nebo v jiných případech vstupně-výstupní operace velikosti nejméně 1 MB. Zapsat latence proti protokolu znovu SAP HANA je důležité výkonu.
- Všechny operace zápisu musí být trvale uloženého na disku spolehlivé způsobem

V důsledku těchto zjištěné vzory vstupů/výstupů SAP Hana měla by být ukládání do mezipaměti pro jiné svazky za využití Azure Premium Storage nastavena jako:

- / hana/dat – bez ukládání do mezipaměti
- / hana/log - žádné ukládání do mezipaměti - výjimka pro řadu M-Series (viz dále v tomto dokumentu)
- čtení /Hana/Shared - ukládání do mezipaměti


Mějte také celkovou propustnost vstupně-výstupní operace virtuálního počítače při změně velikosti nebo rozhodování o tom, pro virtuální počítač. Celkovou propustnost úložiště virtuálního počítače najdete v článku [paměťově optimalizované velikosti virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

#### <a name="cost-conscious-azure-storage-configuration"></a>Nákladů při provádění konfigurace služby Azure Storage
Následující tabulka ukazuje konfiguraci typy virtuálních počítačů, které zákazníci nejčastěji používají k hostiteli SAP HANA na virtuálních počítačích Azure. Můžou nastat některé typy virtuálních počítačů, které nemusí splňovat minimální všechna kritéria pro SAP HANA. Ale zatím vypadal jako tyto virtuální počítače bez problémů provádět pro neprodukční prostředí. 

> [!NOTE]
> Pro produkční scénáře, zkontrolujte, zda určitého typu virtuálních počítačů, je podporován pro SAP HANA SAP v [dokumentaci k SAPU pro IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).


| SKLADOVOU POLOŽKU VIRTUÁLNÍHO POČÍTAČE | Paměť RAM | Max. VSTUPNĚ-VÝSTUPNÍ OPERACE VIRTUÁLNÍHO POČÍTAČE<br /> Propustnost | / hana/dat a/hana/log<br /> prokládané s LVM nebo MDADM | / hana/sdílené | / root svazku | / usr/sap | Hana/zálohování |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 GiB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 GiB | 1200 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M32ts | 192 giB | 500 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 |1 x S30 |
| M64s | 1 000 GB | 1000 MB/s | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2000 giB | 2000 MB/s |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


Disky, doporučuje se pro typy menších virtuálních počítačů s 3 x P20 oversize svazky týkajících se místa doporučení podle [SAP TDI úložiště dokument White Paper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Volba se zobrazuje v tabulce však byl proveden poskytuje dostatečnou propustnost disku pro SAP HANA. Pokud potřebujete změny /hana/backup svazku, které se velikostí pro uchování zálohy, které představují dvakrát objem paměti, můžete upravit.   
Zkontrolujte, jestli bude vyhovovat propustnost úložiště pro různé svazky navrhované na zatížení, které chcete spustit. Pokud úloha vyžaduje větší objemy /hana/data a /hana/log, potřebujete zvýšit počet virtuálních pevných disků Storage úrovně Premium pro Azure. Pro změnu velikosti svazku s více virtuálními pevnými disky než uvedené zvýší propustnost vstupně-výstupních operací a vstupně-výstupní operace v rámci omezení typu virtuálního počítače Azure. 

> [!NOTE]
> Konfigurace výše by NIJAK přínosné [jeden virtuální počítač Azure SLA k virtuálním počítačům](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) protože používá kombinaci Azure Premium Storage a Azure Standard Storage. Výběr však byla vybrána k optimalizaci nákladů.


#### <a name="azure-storage-configuration-to-benefit-for-meeting-single-vm-sla"></a>Výhoda pro splnění jedné SLA k virtuálním počítačům Azure konfigurace úložiště
Pokud chcete využívat [jeden virtuální počítač Azure SLA k virtuálním počítačům](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), budete muset výhradní používání virtuálních pevných disků Storage úrovně Premium pro Azure.

> [!NOTE]
> Pro produkční scénáře, zkontrolujte, zda určitého typu virtuálních počítačů, je podporován pro SAP HANA SAP v [dokumentaci k SAPU pro IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

| SKLADOVOU POLOŽKU VIRTUÁLNÍHO POČÍTAČE | Paměť RAM | Max. VSTUPNĚ-VÝSTUPNÍ OPERACE VIRTUÁLNÍHO POČÍTAČE<br /> Propustnost | / hana/dat a/hana/log<br /> prokládané s LVM nebo MDADM | / hana/sdílené | / root svazku | / usr/sap | Hana/zálohování |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 GiB | 768 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| E64v3 | 443 GiB | 1200 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| M32ts | 192 giB | 500 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| M64s | 1 000 GB | 1000 MB/s | 2 x P30 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 giB | 2000 MB/s |3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |


Disky, doporučuje se pro typy menších virtuálních počítačů s 3 x P20 oversize svazky týkajících se místa doporučení podle [SAP TDI úložiště dokument White Paper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Volba se zobrazuje v tabulce však byl proveden poskytuje dostatečnou propustnost disku pro SAP HANA. Pokud potřebujete změny /hana/backup svazku, které se velikostí pro uchování zálohy, které představují dvakrát objem paměti, můžete upravit.  
Zkontrolujte, jestli bude vyhovovat propustnost úložiště pro různé svazky navrhované na zatížení, které chcete spustit. Pokud úloha vyžaduje větší objemy /hana/data a /hana/log, potřebujete zvýšit počet virtuálních pevných disků Storage úrovně Premium pro Azure. Pro změnu velikosti svazku s více virtuálními pevnými disky než uvedené zvýší propustnost vstupně-výstupních operací a vstupně-výstupní operace v rámci omezení typu virtuálního počítače Azure. 



#### <a name="storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Řešení úložiště pomocí Azure Write Accelerator pro virtuální počítače Azure řady M-Series
Akcelerátor zápisu Azure je funkce, které je Začínáme zavádět pro virtuální počítače řady M-Series výhradně. Jak uvádí název účelem funkce je zlepšit latenci vstupu/výstupu zápisů ve službě Azure Storage úrovně Premium. Pro SAP HANA má použít u svazku /hana/log pouze akcelerátorem zápisu. Konfigurace zobrazí, pokud je proto potřeba změnit. Chcete-li použít akcelerátor zápisu Azure s objemem /hana/log pouze je hlavní Změna rozložení mezi /hana/data a /hana/log. 

> [!IMPORTANT]
> Certifikace SAP HANA pro virtuální počítače Azure řady M-Series je výhradně s Azure Write Accelerator pro /hana/log svazku. V důsledku toho produkční scénář nasazení SAP HANA na virtuálních počítačích Azure řady M-Series očekává se dá nakonfigurovat s Azure Write Accelerator pro /hana/log svazku.  

> [!NOTE]
> Pro produkční scénáře, zkontrolujte, zda určitého typu virtuálních počítačů, je podporován pro SAP HANA SAP v [dokumentaci k SAPU pro IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Doporučená konfigurace vypadat takto:

| SKLADOVOU POLOŽKU VIRTUÁLNÍHO POČÍTAČE | Paměť RAM | Max. VSTUPNĚ-VÝSTUPNÍ OPERACE VIRTUÁLNÍHO POČÍTAČE<br /> Propustnost | / hana/dat | / hana/log | / hana/sdílené | / root svazku | / usr/sap | Hana/zálohování |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 giB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1 000 GB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 giB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

Zkontrolujte, jestli bude vyhovovat propustnost úložiště pro různé svazky navrhované na zatížení, které chcete spustit. Pokud úloha vyžaduje větší objemy /hana/data a /hana/log, potřebujete zvýšit počet virtuálních pevných disků Storage úrovně Premium pro Azure. Pro změnu velikosti svazku s více virtuálními pevnými disky než uvedené zvýší propustnost vstupně-výstupních operací a vstupně-výstupní operace v rámci omezení typu virtuálního počítače Azure.

Azure Write Accelerator pouze funguje ve spojení s [Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Tak minimální Azure Premium Storage disky tvořící /hana/log svazek je nutné nasadit jako spravované disky.

Existují omezení Azure Premium Storage VHD na virtuální počítač, který může podporovat akcelerátor zápisu Azure. Aktuální omezení platí pro:

- 16 virtuálních pevných disků pro M128xx virtuálního počítače
- 8 virtuálních pevných disků pro M64xx virtuálního počítače
- 4 virtuální pevné disky pro M32xx virtuálního počítače

Podrobnější pokyny o tom, jak povolit akcelerátor zápisu Azure najdete v článku [akcelerátorem zápisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Podrobnosti a omezení pro akcelerátor zápisu Azure najdete v dokumentaci stejné.


### <a name="set-up-azure-virtual-networks"></a>Nastavení virtuální sítě Azure
Pokud máte připojení site-to-site do Azure přes VPN nebo ExpressRoute, musíte mít aspoň jeden [virtuální síť Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) , který je připojený prostřednictvím virtuální brány k okruhu ExpressRoute nebo VPN. Virtuální brány se nachází v podsíti ve virtuální síti Azure. Instalace SAP HANA, vytvořte dva další podsítě ve virtuální síti. Jednu podsíť hostuje virtuální počítače ke spuštění instance systému SAP HANA. Další podsítě spustí Jumpbox nebo virtuální počítače pro správu k SAP HANA Studio nebo jiného softwaru pro správu hostitele.

Při instalaci virtuálních počítačů pro spuštění SAP HANA, třeba virtuální počítače:

- Dvě virtuální síťové karty nainstalované: jednu síťovou kartu pro připojení k podsíti správy a jednu síťovou kartu pro připojení z místní sítě a jiných sítí, na instance SAP HANA ve virtuálním počítači Azure.
- Statické privátní IP adresy, které jsou nasazené pro obě virtuální síťové karty.

Přehled různých metod pro přiřazení IP adres najdete v tématu [typy IP adres a metody přidělování v Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Pro virtuální počítače spouštějí SAP HANA byste měli spolupracovat s statické IP adresy přiřazené. Důvodem je, že některé atributy konfigurace pro HANA odkazují na IP adresy.

[Azure skupiny zabezpečení sítě (Nsg)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) se používají ke směrování provozu, který se směruje do instance SAP HANA nebo Jumpbox. Skupiny zabezpečení sítě jsou přidružené k podsíti SAP HANA a podsítě pro správu.

Následující obrázek znázorňuje přehled hrubý nasazení schématu pro SAP HANA:

![Hrubý nasazení schématu pro SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)


SAP HANA v Azure nasadit bez připojení site-to-site, přístup k instance SAP HANA, i když veřejnou IP adresu. IP adresa musí přiřadí k virtuálnímu počítači Azure, na kterém běží virtuální počítač Jumpbox. V tomto scénáři základní nasazení spoléhá na Azure integrované služby DNS přeložit názvy hostitelů. Ve složitějším nasazení, kde veřejně přístupnou IP adresy se používají jsou obzvláště důležité, Azure integrované služby DNS. Pomocí skupin zabezpečení sítě Azure můžete omezit otevřených portů nebo rozsahů IP adres, se můžou připojit do Azure podsítí s prostředky, které mají veřejnou IP adresy. Hrubý schéma pro nasazení SAP HANA bez připojení site-to-site na následujícím obrázku:
  
![Hrubý nasazení schématu pro SAP HANA bez připojení site-to-site](media/hana-vm-operations/hana-simple-networking2.PNG)
 


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
Pokud používáte systém SUSE Linux 12 SP1 nebo novější, můžete vytvořit Pacemaker cluster s využitím techniky STONITH zařízení. Zařízení můžete použít k nastavení konfigurace služby SAP HANA, která používá synchronní replikace s systémové replikace HANA a automatické převzetí služeb při selhání. Další informace o postupu instalace najdete v tématu [Příručka pro vysokou dostupnost SAP HANA pro Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
