---
title: Konfigurace infrastruktury SAP HANA a operací v Azure | Dokumentace Microsoftu
description: Provozní příručka pro systémy SAP HANA, které jsou nasazeny na virtuálních počítačích Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
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
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: acccc553c5b63b2acd0f9793b0397b25145449dd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59699326"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Konfigurace infrastruktury SAP HANA a operace v Azure
Tento článek obsahuje pokyny o tom, jak nakonfigurovat infrastrukturu Azure a provozovat systémů SAP HANA, které jsou nasazené na nativních virtuálních počítačích Azure (VM). Tento článek také obsahuje informace o konfiguraci pro SAP HANA Škálováním pro skladovou Položku virtuálního počítače M128s. Tento článek není určena k nahrazení standardní dokumentaci k SAPU, který obsahuje následující obsah:

- [Příručka věnovaná SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Průvodců instalací SAPU](https://service.sap.com/instguides)
- [Poznámky SAP](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Požadavky
K použití tohoto průvodce potřebujete základní znalosti o následující komponenty Azure:

- [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Sítě Azure a virtuální sítě](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Další informace o SAP NetWeaver a jiných komponent SAP v Azure, najdete v článku [SAP v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) část [dokumentace ke službě Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Důležité informace o základní nastavení
Následující části popisují důležité informace o základní nastavení pro nasazení systému SAP HANA na virtuálních počítačích Azure.

### <a name="connect-to-azure-virtual-machines"></a>Připojení k Azure virtual machines
Jak je uvedeno v [virtuálních počítačů Azure Průvodce plánováním](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), dvě základní metody se používají k připojení k virtuálním počítačům Azure:

- Připojení přes internet a veřejných koncových bodů na virtuálním počítači Jumpboxu nebo na virtuálním počítači, na kterém běží SAP HANA.
- Připojení přes [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) nebo Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Připojení Site-to-site přes VPN nebo ExpressRoute je nezbytné pro produkční scénáře. Tento typ připojení je nutná pro nevýrobní scénáře, které informačního kanálu do produkčních scénářů použití softwaru SAP. Následující obrázek ukazuje příklad připojení webů:

![Připojení mezi weby](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Vyberte typy virtuálních počítačů Azure
Typy virtuálních počítačů Azure použít pro produkční scénáře jsou uvedeny v [dokumentaci k SAPU pro IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). U scénářů s neprovozním širší nativní typy virtuálních počítačů Azure je k dispozici.

>[!NOTE]
> Pro scénáře neprovozním použijte typy virtuálních počítačů, které jsou uvedeny v [SAP 1928533 # Poznámka](https://launchpad.support.sap.com/#/notes/1928533). Za použití virtuálních počítačů Azure pro produkční scénáře, zkontrolujte, zda virtuální počítače v systému SAP publikována s certifikací SAP HANA [certified seznamu platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Pokud chcete nasadit virtuální počítače v Azure, použijte:

- Na webu Azure portal.
- Rutiny Powershellu pro Azure.
- Azure CLI.

Také můžete nasadit kompletní instalované platformy SAP HANA ve službě Azure VM prostřednictvím [cloudové platformy SAPU](https://cal.sap.com/). Informace o procesu instalace najdete v tématu [nasadit řešení SAP S/4HANA nebo BW/4HANA v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Informace o automatizaci vydání, naleznete v tématu [tohoto článku Githubu věnovaném](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

### <a name="choose-an-azure-storage-type"></a>Zvolte typ služby Azure Storage
Azure nabízí dva typy úložiště, které jsou vhodné pro virtuální počítače Azure, který spouštění řešení SAP HANA:  

- Standardní pevné disky (HDD)
- Premium jednotky SSD (Solid-State Drive)

Další informace o těchto typech disků najdete v tématu [vyberte typ disku](../../windows/disks-types.md).

Azure nabízí dvě metody nasazení pro virtuální pevné disky na Azure storage úrovně standard a premium storage. Pokud celková scénář umožňuje, využít výhod [Azure spravovaný disk](https://azure.microsoft.com/services/managed-disks/) nasazení.

Seznam typů úložiště a jejich SLA propustnost vstupně-výstupních operací a úložiště najdete v tématu [dokumentace k Azure pro spravované disky](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="configure-the-storage-for-azure-virtual-machines"></a>Konfigurace úložiště pro virtuální počítače Azure

Budete pravděpodobně nikdy pečovat o vstupně-výstupních subsystémů a jejími funkcemi vzhledem k tomu, že si dali pozor, aby byly splněny požadavky na minimální úložiště pro SAP HANA na dodavatele zařízení. Při sestavování infrastruktury Azure sami, musíte mít na paměti některé z těchto požadavků. Také by měl pochopit požadavky na konfiguraci navržený v následujících částech. Pro případy, kde můžete konfigurovat virtuální počítače chcete SAP HANA po spuštění, možná budete muset:

- Povolte čtení a zápis svazek na /hana/log minimálně 250 MB/s pro velikosti vstupně-výstupních operací 1 MB.
- Povolte čtení aktivity minimálně 400 MB/s pro /hana/data pro vstupně-výstupní operace velikosti 16 MB a 64 MB.
- Povolte zápis aktivity alespoň 250 MB za sekundu pro /hana/data vstupně-výstupní operace větší 16 MB a 64 MB.

Úložiště s nízkou latencí je zásadní pro systémy DBMS, i když DBMS, jako je SAP HANA, zachová data v paměti. Kritická cesta v úložišti je obvykle kolem zápisy protokolu transakce systémů DBMS. Ale operací, jako je vytváření úložných bodů nebo načítají data v paměti se po obnovení při havárii také může být důležité. 

Použití Azure premium disků pro svazky /hana/data a /hana/log je povinné. K dosažení minimální propustnost /hana/log a /hana/data podle potřeby SAP, sestavení RAID 0 mdadm nebo správce pro logický svazků (LVM) prostřednictvím více disky Azure premium storage. Také použijte svazky RAID jako /hana/data a /hana/log svazky. Doporučujeme použít následující formáty stripe RAID 0:

- 64 KB nebo 128 KB pro/hana/dat
- 32 KB pro/hana/log

> [!NOTE]
> Nemusíte konfigurovat žádné úrovně redundance pomocí svazky RAID, protože premium a Azure storage úrovně standard zachovat tři Image virtuálního pevného disku. Použití svazek RAID je čistě konfigurace svazků, které poskytuje dostatečnou propustnost vstupně-výstupních operací.

Shromažďování počtu virtuálních pevných disků Azure pod RAID je kumulativní straně propustnost vstupně-výstupních operací a úložiště. Když vložíte RAID 0 přes 3 disky Azure premium storage x P30, poskytuje třikrát IOPS a třikrát propustnost úložiště jednoho Azure premium storage P30 disku.

Následující doporučení pro ukládání do mezipaměti se předpokládá vlastnosti vstupně-výstupních operací pro SAP HANA:

- Je téměř jakékoli další úlohy proti HANA datových souborů. Výjimky jsou po restartování instance HANA nebo data se načtou do HANA velkých a velkých vstupně-výstupních operací. Další možnost je z větší čtení že vstupně-výstupních operací s datovými soubory mohou být zálohy databáze HANA. V důsledku toho ukládání do mezipaměti pro čtení nedává smysl proto, že ve většině případů musí být všechny datové svazky souboru zcela načteny.
- Zápis proti datových souborů dochází v nárůsty zatížení na základě úložných bodů HANA a obnovení při havárii pro HANA. Vytváření úložných bodů je asynchronní a nevlastní žádné uživatelské transakce. Zápis dat během obnovení při havárii je výkon kritických zajistí systému reagovat rychle znovu. Obnovení při havárii by měl být raději výjimečné situace.
- Existují téměř všechny operace čtení ze souborů znovu HANA. Výjimky jsou velké vstupně-výstupních operací, když provádíte zálohování protokolů transakcí, obnovení při havárii nebo fázi restartování instance HANA.  
- Hlavní zátěž v SAP HANA znovu protokolu jsou zápisy. V závislosti na povaze zatížení může mít vstupně-výstupních operací jako 4 KB, nebo v jiných případech a vstupně-výstupních operací malá velikost nejméně 1 MB. Zapsat latence proti protokolu znovu SAP HANA je důležité výkonu.
- Všechny operace zápisu musí být trvale uloženého na disku spolehlivé způsobem.

V důsledku těchto zjištěné vzory vstupů/výstupů SAP Hana nastavení ukládání do mezipaměti pro různé svazky, které používají službu Azure premium storage do:

- **/hana/data**: Neexistující ukládání do mezipaměti.
- **/ hana/log**: Neexistující ukládání do mezipaměti, s výjimkou pro virtuální počítače řady M-series (viz další rozšíření později v tomto článku).
- **/ hana/sdílené**: Ukládání do mezipaměti pro čtení.


Také vzít v úvahu celkovou propustnost vstupně-výstupní operace virtuálního počítače při velikost nebo při rozhodování o virtuálním počítači. Celkovou propustnost úložiště virtuálního počítače jsou uvedené v [paměťově optimalizované velikosti virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

#### <a name="linux-io-scheduler-mode"></a>Režim Plánovač vstupně-výstupních operací na Linuxu
Linux má několik různých režimech plánování vstupně-výstupních operací. Ze systému Linux dodavateli a SAP doporučuje pro nastavení režimu Plánovač vstupně-výstupních operací pro diskové svazky klávesou **cfq** režimu, aby **noop** režimu. Další informace najdete v tématu [1984798 # Poznámka SAP](https://launchpad.support.sap.com/#/notes/1984787). 


#### <a name="storage-solution-with-write-accelerator-for-azure-m-series-virtual-machines"></a>Řešení úložiště s Write Accelerator pro Azure řady M-series virtuálních počítačů
 Zapsat že akcelerátor je funkce Azure, zavádí pro virtuální počítače Azure řady M-series výhradně. Účelem funkce je zlepšit latenci vstupu/výstupu zápisů ve službě Azure premium storage. Pro SAP HANA má použít u svazku /hana/log pouze akcelerátorem zápisu. Z tohoto důvodu musíte změnit konfigurace, pokud se zobrazí. Aby bylo možné používat s objemem /hana/log pouze akcelerátorem zápisu je hlavní Změna rozložení mezi /hana/data a /hana/log. 

> [!IMPORTANT]
> Certifikace SAP HANA pro Azure řady M-series virtuálních počítačů je výhradně pro Write Accelerator pro svazek s protokolem/hana /. V důsledku toho produkční scénář nasazení SAP HANA v Azure řady M-series se očekává, že virtuální počítače nakonfigurované s Write Accelerator pro/hana/protokolu svazku.

> [!NOTE]
> Pro produkční scénáře, zkontrolujte, zda určitého typu virtuálních počítačů, je podporován pro SAP HANA SAP v [dokumentaci k SAPU pro IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

V následující tabulce jsou uvedeny doporučené konfigurace.

| SKLADOVOU POLOŽKU VIRTUÁLNÍHO POČÍTAČE | Paměť RAM | Virtuální počítač maximální vstupně-výstupních operací<br /> Propustnost | / hana/dat | / hana/log | / hana/sdílené | / root svazku | / usr/sap | Hana/zálohování |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/sec | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/sec | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1 000 MB za sekundu | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1 000 GB | 1 000 MB za sekundu | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1,750 giB | 1 000 MB za sekundu | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2 000 GB | 2 000 MB za sekundu |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3,800 giB | 2 000 MB za sekundu | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

Zkontrolujte, zda splňuje propustnost úložiště pro různé svazky doporučené úlohy, které chcete spustit. Pokud úloha vyžaduje větší objemy /hana/data a /hana/log, zvyšte počet Azure premium storage virtuální pevné disky. Pro změnu velikosti svazku s více virtuálními pevnými disky než uvedené zvýšení IOPS a propustnost vstupně-výstupní operace v rámci omezení typu virtuálního počítače Azure.

Zápis akcelerátoru pouze funguje ve spojení s [Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Přinejmenším musí být disky Azure premium storage, které tvoří svazku /hana/log nasazený jako spravované disky.

Existují omezení pro virtuální pevné disky na virtuální počítač, který podporuje akcelerátor zápisu Azure premium storage. Aktuální omezení platí pro:

- 16 virtuálních pevných disků pro M128xx virtuálního počítače.
- 8 virtuálních pevných disků pro M64xx virtuálního počítače.
- 4 virtuální pevné disky pro M32xx virtuálního počítače.

Další informace o tom, jak povolit akcelerátorem zápisu najdete v tématu [akcelerátorem zápisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Další informace o a omezení pro akcelerátorem zápisu naleznete v dokumentaci stejné.


#### <a name="cost-conscious-azure-storage-configuration"></a>Konfigurace služby Azure Storage hledí na cenu
Následující tabulka ukazuje konfiguraci typy virtuálních počítačů, které zákazníci nejčastěji používají k hostiteli SAP HANA na virtuálních počítačích Azure. Můžou nastat některé typy virtuálních počítačů, které nesplňují minimální všechna kritéria pro SAP HANA. Může existovat i některé typy virtuálních počítačů, které nejsou oficiálně podporované se SAP HANA SAP. Tyto virtuální počítače zatím jste provedli správně pro nevýrobní scénáře. 

> [!NOTE]
> Pro produkční scénáře, zkontrolujte, zda určitého typu virtuálních počítačů, je podporován pro SAP HANA SAP v [dokumentaci k SAPU pro IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).


| SKLADOVOU POLOŽKU VIRTUÁLNÍHO POČÍTAČE | Paměť RAM | Virtuální počítač maximální vstupně-výstupních operací<br /> Propustnost | / hana/dat a/hana/log<br /> prokládané s LVM nebo mdadm | / hana/sdílené | / root svazku | / usr/sap | Hana/zálohování |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/sec | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 GiB | 384 MB/sec | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GiB | 768 MB/sec | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 432 GiB | 1 200 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GiB | 2 000 MB za sekundu | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M32ts | 192 GiB | 500 MB/sec | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M32ls | 256 GiB | 500 MB/sec | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M64ls | 512 GiB | 1 000 MB za sekundu | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 |1 x S30 |
| M64s | 1 000 GB | 1 000 MB za sekundu | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1,750 giB | 1 000 MB za sekundu | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2 000 GB | 2 000 MB za sekundu |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3,800 giB | 2 000 MB za sekundu | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


Disky, které se doporučují pro typy menších virtuálních počítačů s 3 x P20 oversize svazky ve vztahu místo doporučení, které jsou uvedeny v [dokument white paper SAP TDI úložiště](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Volba zobrazené v tabulce byl proveden poskytuje dostatečnou propustnost disku pro SAP HANA. Pokud potřebujete změnit **/hana/záloha** svazek, který byl přizpůsoben pro Udržovat zálohy, které představují dvakrát objem paměti, můžete bez obav s úpravami. 

Zkontrolujte, zda splňuje propustnost úložiště pro různé svazky doporučené úlohy, které chcete spustit. Pokud úloha vyžaduje větší objemy /hana/data a /hana/log, zvyšte počet Azure premium storage virtuální pevné disky. Pro změnu velikosti svazku s více virtuálními pevnými disky než uvedené zvýšení IOPS a propustnost vstupně-výstupní operace v rámci omezení typu virtuálního počítače Azure. 

> [!NOTE]
> Předchozí konfigurace není těžit z [jeden virtuální počítač Azure SLA k virtuálním počítačům](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) protože používá kombinaci služby Azure premium storage a Azure storage úrovně standard. Výběr byl zvolen pro zajištění optimalizace nákladů. Storage úrovně premium pro všechny disky vyberte v tabulce jsou uvedené jako úložiště Azure úrovně standard (Sxx), aby konfigurace virtuálního počítače kompatibilní s Azure SLA jednoho virtuálního počítače.


> [!NOTE]
> Doporučené konfigurace disku cílové minimální požadavky, které SAP poskytuje jeho infrastruktury poskytovatelů. Scénáře úloh a skutečné zákaznická nasazení tato doporučení neposkytli dostatečná možností v některých situacích. Například zákazník potřeboval rychlejší opětovné načtení dat po restartu HANA nebo konfigurací vyžaduje větší šířku pásma pro úložiště. Ostatní případy zahrnují /hana/log, kde nejsou dostatečná pro konkrétní úlohu 5 000 IOPS. Použijte je jako výchozí bod a přizpůsobit je na základě požadavků zatížení.
>  

### <a name="set-up-azure-virtual-networks"></a>Nastavení virtuální sítě Azure
Pokud máte připojení site-to-site k Azure přes síť VPN nebo Azure ExpressRoute, musíte mít aspoň jednu virtuální síť Azure, který je připojený prostřednictvím virtuální brány k okruhu ExpressRoute nebo VPN. V jednoduchých nasazení je možné nasadit virtuální brány do podsítě virtuální síť Azure, který je hostitelem instance systému SAP HANA příliš. 

Instalace SAP HANA, vytvořte dva další podsítě v rámci virtuální sítě Azure. Jednu podsíť hostuje virtuální počítače ke spuštění instance systému SAP HANA. Další podsítě spustí JumpBox nebo správu virtuálních počítačů na hostitele systému SAP HANA Studio, další software pro správu nebo aplikační software.

> [!IMPORTANT]
> Konfigurace [Azure síťových virtuálních zařízení](https://azure.microsoft.com/solutions/network-appliances/) v cestě komunikaci mezi aplikací SAP a DBMS vrstvy aplikace SAP NetWeaver - Hybris – nebo systém SAP S/4HANA – na základě není podporováno. Toto omezení je důvodů funkčnosti a výkonu. Komunikační trasa mezi aplikační vrstvě SAP a DBMS vrstva musí být přímá. Omezení neobsahuje [skupiny zabezpečení aplikací (ASG) a zabezpečení sítě (NSG) pravidla skupiny](https://docs.microsoft.com/azure/virtual-network/security-overview) Pokud pravidla ASG a skupině NSG povolit cestu přímá komunikace. 
>
> Další scénáře, ve kterém nejsou podporovány síťová virtuální zařízení jsou v: 
>
> - Komunikační trasy mezi virtuálními počítači Azure, které představují Linux Pacemaker clusteru uzly a SBD zařízení, jak je popsáno v [vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).
> - Komunikační trasy mezi virtuálními počítači Azure a souborového serveru systému Windows Server horizontální navýšení kapacity až, jak je popsáno v nastavení [instanci SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s využitím sdílené složky v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). 
>
> Síťová virtuální zařízení v komunikaci cesty můžete snadno double latence sítě mezi dvěma komunikace partnery. Také můžete omezují propustnost v kritické cesty mezi aplikační vrstvě SAP a vrstvu DBMS. V některých scénářích zákazníka může způsobit síťová virtuální zařízení Pacemaker Linuxové clustery selhání. Toto jsou případy, kdy komunikace mezi uzly clusteru Linux Pacemaker sdělit SBD zařízení přes síťové virtuální zařízení.  
> 

> [!IMPORTANT]
> Další návrh, který vaší *není* zodpovědnosti aplikační vrstvě SAP a vrstvu DBMS do různých virtuálních sítí Azure, které nejsou podporovány je [v partnerském vztahu](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) mezi sebou. Doporučujeme oddělit SAP aplikační vrstva a vrstva DBMS pomocí podsítí v rámci virtuální sítě Azure místo použití různých virtuálních sítích Azure. 
>
>Pokud se rozhodnete postupujte podle doporučení a místo toho oddělit dvě vrstvy do různých virtuálních sítí, musí být dvě virtuální sítě [v partnerském vztahu](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 
>
> Mějte na paměti, které síťový provoz mezi dvěma [v partnerském vztahu](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) virtuálním sítím Azure je v souladu s náklady na přenos. Obrovská datový svazek, který se skládá z po mnoho terabajtů se vyměňují mezi SAP aplikační vrstva a vrstva DBMS. Pokud SAP aplikační vrstva a vrstva DBMS jsou odděleny mezi dvěma partnerskými virtuálními sítěmi Azure, lze nashromáždit značné náklady. 

Při instalaci virtuálních počítačů pro spuštění SAP HANA, třeba virtuální počítače:

- Dvě virtuální síťové karty nainstalované. Jedním síťovým rozhraním se připojí k podsíti správy. Další síťová karta připojuje z místní sítě nebo jiné sítě do instance SAP HANA ve virtuálním počítači Azure.
- Statické privátní IP adresy, které jsou nasazené pro obě virtuální síťové karty.

> [!NOTE]
> Přiřazení statických IP adres prostřednictvím Azure znamená, že je přiřadit k jednotlivým virtuální síťové karty. Přiřadit statické IP adresy v rámci hostovaného operačního systému pro virtuální síťový adaptér. Některé služby Azure, jako je Azure Backup závisí na skutečnost, který na nejnižší primární virtuální síťové karty nastavená DHCP, ne statické IP adresy. Další informace najdete v tématu [Poradce při potížích s Azure se zálohováním virtuálních počítačů](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). K virtuálnímu počítači přiřadit více statických IP adres, virtuálnímu počítači přiřadíte víc virtuálních síťových karet.
>
>

Pro nasazení, které jsou enduring vytvořte virtuální datové centrum síťové architektury v Azure. Tato architektura se doporučuje oddělení brány virtuální sítě Azure, která se připojuje k místnímu do samostatné virtuální sítě Azure. Tento samostatný virtuální síť hostuje veškerý provoz, které se zasílají do místního nebo k Internetu. Pomocí tohoto přístupu můžete nasadit software na audit a protokolování provozu, který zadá virtuální datové centrum Azure v této virtuální síti centra samostatné. Díky tomu máte jednu virtuální síť, který je hostitelem veškerý software a konfigurace, které souvisí s přidaného a odchozí přenosy dat do nasazení vašeho řešení Azure.


Další informace o virtuální datové centrum a návrhu související virtuální síť Azure najdete v článku: 

- [Virtuální datové centrum Azure: Z hlediska sítě](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter).
- [Virtuální datové centrum Azure a rovina podnikového řízení](https://docs.microsoft.com/azure/architecture/vdc/).


>[!NOTE]
>Provoz, který probíhá mezi centrální virtuální síti a virtuální sítě paprsků pomocí [partnerský vztah virtuální sítě Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) podléhá Další [náklady](https://azure.microsoft.com/pricing/details/virtual-network/). Na základě těchto nákladů, možná budete muset provést ohrožení mezi systémem přísné síťové střed a paprsek návrhu a spouští několik [Azure ExpressRoute Gateway](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) připojení k paprsky aby bylo možné obejít partnerský vztah virtuální sítě. 
>
> Azure ExpressRoute Gateway zavést další [náklady](https://azure.microsoft.com/pricing/details/vpn-gateway/) příliš. Můžete také setkat další náklady na software jiných výrobců, které používáte k přihlášení, auditování a monitorování síťového provozu. Zvažte náklady pro výměny dat přes virtuální síť vytvoření partnerského vztahu a náklady na vytvořené další brány ExpressRoute a licencích k softwaru. Můžete se rozhodnout na mikrosegmentaci v rámci jedné virtuální sítě s použitím podsítě jako jednotky izolace místo virtuálních sítí.


Přehled různých metod, které můžete použít k přiřazování IP adres najdete v tématu [typy IP adres a metody přidělování v Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Pro virtuální počítače, na kterých běží SAP HANA práce se statickými IP adresami, které jsou přiřazeny. Důvodem je, že některé atributy konfigurace pro HANA odkazují na IP adresy.

[Skupiny zabezpečení sítě Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) se používají ke směrování provozu, který se směruje do instance SAP HANA nebo JumpBox. Skupiny zabezpečení sítě a nakonec [skupiny zabezpečení aplikací](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) jsou spojené s podsítí SAP HANA a podsítě pro správu.

Následující obrázek znázorňuje přehled hrubý nasazení schématu pro SAP HANA, která odpovídá architektuře střed a paprsek virtuální sítě:

![Hrubý nasazení schématu pro SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)

SAP HANA v Azure nasadit bez připojení site-to-site, stínit instance SAP HANA z veřejného Internetu a skrýt za dopředné proxy serverem. V tomto scénáři základní nasazení spoléhá na Azure integrované služby DNS k překladu názvů hostitelů. Ve složitějším nasazení, kde veřejně přístupnou IP adresy se používají jsou obzvláště důležité, Azure integrované služby DNS. Pomocí skupin zabezpečení sítě Azure a [Azure síťových virtuálních zařízení](https://azure.microsoft.com/solutions/network-appliances/) monitorování, směrování z Internetu do vaší virtuální síti Azure architektury v Azure. 

Následující obrázek ukazuje hrubý schéma pro nasazení SAP HANA bez připojení site-to-site v architektuře střed a paprsek virtuální sítě:
  
![Hrubý nasazení schématu pro SAP HANA bez připojení site-to-site](media/hana-vm-operations/hana-simple-networking2.PNG)
 

Další popis toho, jak používat Azure síťových virtuálních zařízení pro sledování a řízení přístupu z Internetu bez architekturu střed a paprsek virtuální sítě najdete v tématu [nasazení vysoce dostupných síťových virtuálních zařízení](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configure-azure-infrastructure-for-sap-hana-scale-out"></a>Konfigurace infrastruktury Azure pro horizontální navýšení kapacity SAP HANA
Microsoft má jeden SKU virtuálních počítačů řady M-series, s certifikací SAP HANA konfigurace škálování na víc systémů. Typ virtuálního počítače M128s má certifikaci pro škálování až 16 uzlů. Změny v certifikace škálování na více instancí SAP HANA na virtuálních počítačích Azure, najdete v článku [certified seznamu platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Minimální verze operačního systému, použít k nasazení horizontální navýšení kapacity konfigurací ve virtuálních počítačích Azure jsou:

- SUSE Linux 12 SP3.
- Red Hat Linux 7.4.

Horizontální navýšení kapacity certifikaci 16 uzly:

- Hlavní uzel je jeden uzel.
- Maximální počet 15 uzlů se pracovní uzly.

>[!NOTE]
>V nasazení škálování na více instancí virtuálních počítačů Azure není možné použít pohotovostní uzel.
>

Existují dva důvody, proč nelze nakonfigurovat pohotovostní uzel:

- Azure má v tuto chvíli žádné nativní služby systému souborů NFS. V důsledku toho musí být nakonfigurované sdílených složek NFS pomocí funkce třetích stran.
- Žádná konfigurace systému souborů NFS třetích stran nesplňuje kritéria latence úložiště pro SAP HANA s jejich řešení nasazená v Azure.

V důsledku toho není možné sdílet /hana/data a /hana/log svazky. Nesdílí tyto svazky jednotlivé uzly brání použití pohotovostní uzel SAP HANA v konfiguraci horizontální navýšení kapacity.

Následující obrázek ukazuje základní návrhu pro jeden uzel v konfiguraci horizontální navýšení kapacity:

![Základní informace o škálování na víc systémů z jediného uzlu](media/hana-vm-operations/scale-out-basics.PNG)

Základní konfigurace uzlu virtuálního počítače pro horizontální navýšení kapacity SAP HANA vypadá takto:

- Pro /hana/shared sestavujete clusteru pro systém souborů NFS podle operačního systému SUSE Linux 12 SP3 s vysokou dostupností. Tento cluster hostitelem sdílené složky NFS /hana/shared horizontální navýšení kapacity konfigurací a SAP NetWeaver nebo BW/4HANA centrální služby. Informace o tom, jak tyto konfigurace sestavení naleznete v tématu [vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
- Všechny ostatní diskové svazky nejsou sdíleny mezi různými uzly a nejsou založené na systému souborů NFS. Konfigurace instalace a kroky pro horizontální navýšení kapacity HANA instalace s nesdílené /hana/data a /hana/log jsou uvedené dále v tomto článku.

>[!NOTE]
>S vysokou dostupností clusteru systému souborů NFS se zobrazuje na obrázcích, pokud se podporuje s operačním systémem SUSE Linux pouze. Vysoce dostupné řešení systému souborů NFS založeného na Red Hat budete informováni, později.

Změna velikosti svazků pro uzly je stejná jako vertikálně navýšit kapacitu, s výjimkou /hana/shared. V následující tabulce jsou uvedeny doporučené velikosti a typy pro skladovou Položku virtuálního počítače M128s.

| SKLADOVOU POLOŽKU VIRTUÁLNÍHO POČÍTAČE | Paměť RAM | Virtuální počítač maximální vstupně-výstupních operací<br /> Propustnost | / hana/dat | / hana/log | / root svazku | / usr/sap | Hana/zálohování |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M128s | 2 000 GB | 2,000 MB/s |3 x P30 | 2 x P20 | 1 x P6 | 1 x P6 | 2 x P40 |


Zkontrolujte, zda splňuje propustnost úložiště pro různé svazky doporučené úlohy, které chcete spustit. Pokud úloha vyžaduje větší objemy /hana/data a /hana/log, zvyšte počet Azure premium storage virtuální pevné disky. Pro změnu velikosti svazku s více virtuálními pevnými disky než uvedené zvýšení IOPS a propustnost vstupně-výstupní operace v rámci omezení typu virtuálního počítače Azure. Akcelerátorem zápisu platí také pro disky, které tvoří /hana/log svazku.
 

Vzorec, který definuje velikost svazku/hana/sdílené pro horizontální navýšení kapacity jako velikost paměti z jednoho pracovního uzlu na čtyři uzly pracovního procesu, naleznete v tématu [požadavky na úložiště SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

Za předpokladu, že budete postupovat SAP HANA horizontální navýšení kapacity certifikovaných M128s virtuálního počítače Azure s přibližně 2 TB paměti, SAP doporučení jsou shrnuté jako:

- Pro jeden hlavní uzel a až čtyři uzly pracovního procesu je velikost svazku /hana/shared 2 TB.
- Pro jeden hlavní uzel a pět a 8 pracovních uzlů je velikost svazku /hana/shared 4 TB.
- Pro jeden hlavní uzel a 9 až 12 pracovních uzlů je velikost svazku /hana/shared 6 TB.
- Pro jeden hlavní uzel a 12 až 15 pracovních uzlů je velikost svazku /hana/shared 8 TB.

Další důležité návrh, který se zobrazí v grafice konfiguraci jednoho uzlu virtuálního počítače s horizontálním navýšením SAP HANA je virtuální síť s použitím konfigurace podsítě. SAP důrazně doporučuje oddělit provoz směřující na klienta a aplikace z komunikace mezi uzly HANA. 

K dosažení tohoto cíle, připojte dva různé virtuální síťové adaptéry k virtuálnímu počítači, jak je znázorněno na obrázcích. Oba virtuální síťové adaptéry jsou v různých podsítích a mají dvou různých IP adresách. Pak řídit tok provozu s využitím pravidel směrování pomocí skupin zabezpečení sítě a trasy definované uživatelem.

Zejména v Azure neexistují žádné prostředky a metody k vynucení kvalitu služby a kvóty na konkrétní virtuální síťové karty. V důsledku toho oddělení klienta a aplikace zákazníky i meziuzlové komunikaci neotevře příležitosti k určení priority jednoho datového proudu provoz z nich. Místo toho oddělení zůstane míru zabezpečení v stínění meziuzlové komunikaci horizontální navýšení kapacity konfigurací.  

>[!IMPORTANT]
>SAP důrazně doporučuje, že můžete oddělit síťový provoz klienta a aplikace na straně meziuzlové provoz a jak je popsáno v tomto článku. Doporučujeme umístit architekturu na místě, jak je znázorněno v předchozí grafiky.
>

Následující obrázek ukazuje minimální požadovaná síťová architektury z hlediska sítě:

![Základní informace o škálování na víc systémů z jediného uzlu](media/hana-vm-operations/scale-out-networking-overview.PNG)

Omezení zatím nepodporuje jsou 15 pracovní uzly kromě jeden hlavní uzel.

Následující obrázek ukazuje architekturu úložiště z hlediska úložiště:


![Základní informace o škálování na víc systémů z jediného uzlu](media/hana-vm-operations/scale-out-storage-overview.PNG)

/Hana/shared svazek umístěn na vysoce dostupné konfiguraci sdílené složky systému souborů NFS. Všechny jednotky jsou místně připojené k jednotlivým virtuálním počítačům. 

### <a name="highly-available-nfs-share"></a>Vysoce dostupné sdílené složky systému souborů NFS
Zatím s vysokou dostupností clusteru systému souborů NFS ve spolupráci s operačním systémem SUSE Linux pouze. Informace o nastavení, najdete v části [vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs). Pokud clusteru systému souborů NFS nesdílejte s další konfigurace HANA mimo virtuální síť Azure, na kterém běží instance systému SAP HANA, můžete jej nainstalujte ve stejné virtuální síti. Nainstalujte ve vlastní podsíti a ujistěte se, že ne všechny libovolného provozu přístup k podsíť. Místo toho omezení provozu směřujícího do této podsítě na IP adresy virtuálního počítače, které jsou spouštěny provoz do /hana/shared svazku.

Související s virtuální síťové karty virtuálního počítače HANA horizontální navýšení kapacity, který směruje provoz /hana/shared, doporučení jsou:

- Vzhledem k tomu, že provoz do /hana/shared střední, směrujte přes virtuální síťové karty, která je přiřazena k síti klienta v minimální požadavky na konfiguraci.
- Nakonec pro provoz do/hana/sdílené, nasaďte třetí podsíti ve virtuální síti, kde nasadíte horizontální navýšení kapacity konfigurací SAP HANA. Přiřaďte třetí virtuální síťové adaptéry, která je hostována v této podsíti. Pomocí třetí virtuální síťovou kartu a přidružené IP adresy pro provoz do sdílené složky systému souborů NFS. Pak můžete použít samostatné přístup a pravidla směrování.

>[!IMPORTANT]
>Síťový provoz mezi virtuální počítače, které mají SAP HANA způsobem horizontální navýšení kapacity, nasazení a vysoce dostupný systém souborů NFS za žádných okolností může směrovat přes [síťové virtuální zařízení](https://azure.microsoft.com/solutions/network-appliances/) nebo podobné virtuální zařízení. Skupiny zabezpečení sítě Azure se nevyžaduje těchto zařízení. Zkontrolujte, že vaše pravidla směrování, abyste měli jistotu, že síťová virtuální zařízení nebo podobné virtuální zařízení jsou zkrácen při přístupu k systému souborů NFS s vysokou dostupností sdílet z virtuálních počítačů, na kterých běží SAP HANA.
> 

Pokud chcete sdílet s vysokou dostupností clusteru systému souborů NFS mezi konfiguracemi SAP HANA, přesuňte všechny tyto konfigurace HANA do stejné virtuální síti. 
 

### <a name="install-an-sap-hana-scale-out-in-azure"></a>Instalace SAP HANA horizontální navýšení kapacity v Azure
Instalace konfigurace SAP škálování na víc systémů, postupujte podle následujících obecných kroků:

- Nasazení nové nebo upravovat nové infrastruktury virtuální sítě Azure.
- Nasazení nových virtuálních počítačů pomocí svazků úložiště spravované v Azure úrovně premium.
- Nasadit novou nebo upravit existující cluster s vysokou dostupností systému souborů NFS.
- Přizpůsobit, abyste měli jistotu, že, například meziuzlové komunikaci mezi virtuálními počítači není směrován přes směrování sítě [síťové virtuální zařízení](https://azure.microsoft.com/solutions/network-appliances/). Totéž platí pro přenosy mezi virtuálními počítači a s vysokou dostupností clusteru systému souborů NFS.
- Instalace hlavního uzlu SAP HANA.
- Přizpůsobení parametrů konfigurace hlavního uzlu SAP HANA.
- Před pokračováním instalace SAP HANA pracovních uzlů.

#### <a name="install-sap-hana-in-a-scale-out-configuration"></a>Instalace SAP HANA v konfiguraci horizontální navýšení kapacity
Nasazení infrastruktury virtuálních počítačů Azure a dalším krokům hotovi. Nyní abyste mohli nainstalovat horizontální navýšení kapacity konfigurací SAP HANA, postupujte takto:

- Instalace SAP HANA hlavní uzel podle dokumentace společnosti SAP.
- *Po instalaci změnit global.ini soubor a přidejte parametr "basepath_shared = ne ' pro global.ini*. Tento parametr umožňuje SAP HANA po spuštění v Škálováním bez sdíleného /hana/data a svazky /hana/log mezi uzly. Další informace najdete v tématu [2080991 # Poznámka SAP](https://launchpad.support.sap.com/#/notes/2080991).
- Po změně parametr global.ini Restartujte instanci SAP HANA.
- Přidáte pracovní uzly. Další informace najdete v tématu [příručka věnovaná SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html). Zadejte interní síť pro SAP HANA komunikace v rámci uzlu během instalace nebo později, například místní hdblcm. Další informace najdete v tématu [2183363 # Poznámka SAP](https://launchpad.support.sap.com/#/notes/2183363). 

Následující rutina toto nastavení používá konfiguraci horizontální navýšení kapacity, kterou jste nainstalovali nesdílené disků ke spouštění /hana/data a /hana/log. / Hana/sdílený svazek umístěn na vysoce dostupné sdílené složky systému souborů NFS.


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>SAP HANA dynamické vrstvení 2.0 pro virtuální počítače Azure

Kromě certifikací SAP HANA na virtuálních počítačích Azure řady M-series SAP HANA dynamické vrstvení 2.0 (DT 2.0) také podporuje v Microsoft Azure. Odkazy na dokumentaci pro dynamické vrstvení SAP HANA naleznete v části "Odkazuje na DT 2.0 dokumentaci" dále v tomto článku. Není žádný rozdíl v instalaci produktu nebo provoz, například prostřednictvím řídicího panelu SAP HANA ve Virtuálním počítači Azure, ale některé důležité položky jsou povinné oficiální podporu k Azure. Tyto klíčové body jsou popsány v následujících částech. 

SAP HANA DT 2.0 nepodporuje SAP BW nebo S4HANA. Hlavní použití jsou teď nativní aplikace HANA.


### <a name="overview"></a>Přehled

Na následujícím obrázku poskytuje přehled podpory DT 2.0 v Microsoft Azure. Postupujte podle těchto povinné požadavky pro dosažení souladu s oficiální certifikaci:

- DT 2.0 musí být nainstalován na vyhrazených virtuálních počítačích Azure. Nebude fungovat na stejném virtuálním počítači, kde běží SAP HANA.
- SAP HANA a virtuální počítače DT 2.0 musí být nasazeny v rámci stejné virtuální síti Azure.
- SAP HANA a virtuální počítače DT 2.0 musí být nasazeny s akcelerovaných síťových služeb Azure povolen.
- Typ úložiště pro virtuální počítače DT 2.0 musí být služby Azure premium storage.
- Více disků v Azure musí být připojené k virtuálnímu počítači DT 2.0.
- Vytváření softwaru diskového pole RAID nebo prokládaných svazků, přes LVM nebo mdadm, je požadován, pomocí prokládání disků Azure.

Následující části obsahují podrobnější vysvětlení.

![Přehled architektury SAP HANA DT 2.0](media/hana-vm-operations/hana-dt-20.PNG)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>Vyhrazený virtuální počítač Azure pro SAP HANA DT 2.0

Na Azure IaaS DT 2.0 je podporována pouze na vyhrazený virtuální počítač. DT 2.0 není smí spouštět na stejném virtuálním počítači Azure se spuštěným systémem HANA instance. Na začátku dva typy virtuálních počítačů lze použít ke spuštění SAP HANA DT 2.0:

- M64-32ms 
- E32sv3 

Popisy typů virtuálních počítačů, naleznete v tématu [paměťově optimalizované velikosti virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

Zadaný základní myšlenka DT 2.0, což je o snižování zátěže teplé data pro úsporu nákladů, je vhodné použít odpovídající velikosti virtuálních počítačů. Neexistuje žádné striktní pravidlo pro všechny možné kombinace. To závisí na pracovním vytížení konkrétního zákazníka.

V následující tabulce jsou uvedeny doporučené konfigurace.

| Typ virtuálního počítače SAP HANA | Typ virtuálního počítače DT 2.0 |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


Všechny kombinace s certifikací SAP HANA virtuální počítače řady M-series s podporovaných DT 2.0 virtuálních počítačů, jako je například M64 32ms a E32sv3, je možné.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Sítě Azure a SAP HANA DT 2.0

Instalace DT 2.0 na vyhrazený virtuální počítač vyžaduje propustnost sítě mezi tímto DT 2.0 a virtuální počítač SAP HANA s minimálně 10 GB. V důsledku toho je nutné umístit všechny virtuální počítače ve stejné virtuální síti Azure a povolení akcelerovaných síťových služeb Azure.

Další informace o akcelerovaných síťových služeb Azure najdete v tématu [vytvořit virtuální počítač s Linuxem s Akcelerovanými síťovými službami](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).

### <a name="vm-storage-for-sap-hana-dt-20"></a>Úložiště virtuálního počítače pro SAP HANA DT 2.0

Podle pokynů DT 2.0 osvědčené postupy minimální propustnost vstupně-výstupních operací disku je 50 MB/s na fyzické jádro. Prohlížení specifikace pro dva typy virtuálních počítačů Azure, které jsou podporovány pro DT 2.0, je disk maximální limit propustnosti vstupně-výstupních operací pro virtuální počítač:

- **E32sv3**:   768 MB/s, bez mezipaměti, což znamená, že poměr 48 MB za sekundu podle počtu fyzických jader
- **M64-32ms**:  1 000 MB za sekundu, bez mezipaměti, což znamená, že poměr 62,5 MB za sekundu podle počtu fyzických jader

Vyžaduje se připojení k virtuálnímu počítači 2.0 DT více disků v Azure a vytvoření s použitím prokládáním na úrovni operačního systému k dosažení maximální propustnost disků jednotlivých virtuálních počítačů softwarového pole RAID. Jeden disk Azure neposkytuje propustnost pro dosažení maximálního limitu virtuálních počítačů. Azure premium storage je povinné pro spuštění DT 2.0. 

- Další informace o typech disků k dispozici Azure najdete v tématu [vyberte typ disku pro virtuální počítače Azure IaaS Windows](../../windows/disks-types.md).
- Další informace o tom, jak vytvořit softwarového pole RAID prostřednictvím mdadm najdete v tématu [konfigurace softwarového pole RAID v Linuxu](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid).
- Další informace o tom, jak konfigurace LVM vytvořit prokládané svazky pro maximální propustnost, naleznete v tématu [konfigurace LVM na virtuální počítač s Linuxem v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm).

V závislosti na požadavcích velikost existují různé možnosti pro dosažení maximální propustnost virtuální počítač. Tady je konfigurace disků objem dat pro každý typ virtuálního počítače DT 2.0 k dosažení horní limit propustnosti virtuálního počítače. Virtuální počítač E32sv3 se považuje za úroveň položka pro úlohy menší. Pokud není dostatečně rychle, může být potřeba změnit velikost virtuálního počítače do M64 32ms.

Vzhledem k tomu, že má virtuální počítač M64 32ms velké množství paměti, nemusí dosáhnout zatížení vstupně-výstupních operací tento limit, zejména pro úlohy náročné na čtení. V závislosti na zatížení zákaznického můžou stačit méně disky v sadě stripe. Aby bylo v bezpečí, byly vybrány následující konfigurace disku zajistit maximální propustnost.


| SKLADOVOU POLOŽKU VIRTUÁLNÍHO POČÍTAČE | Konfigurace disku 1 | Konfigurace disku 2 | Konfigurace disku 3 | Konfigurace disku 4 | Konfigurace disku 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3,5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2,5 TB | 6 x P15 -> 1,5 TB | 


Zejména v případě, že pracovní zatížení je náročné na čtení, zapnout v nastavení "jen pro čtení" mezipaměti hostitele Azure podle doporučení pro datové svazky databázový software může zvýšit výkon vstupně-výstupních operací. Transakční protokol mezipaměti disku Azure hostitele musí být "none". 

Výchozí bod, který doporučujeme pro velikost svazku protokolu je 15 procent velikosti dat heuristické metody. Pokud chcete vytvořit svazek s protokolem, použijte jiný disk Azure typy v závislosti na požadavcích náklady a propustnost. Pro svazek s protokolem se vyžaduje Vysoká propustnost vstupně-výstupních operací. 

Pokud používáte virtuální počítač zadejte M64 32ms, doporučujeme, abyste povolili [akcelerátorem zápisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). Akcelerátor zápisu je funkce služby Azure, která poskytuje latence zápisu u optimální disku pro protokol transakcí. Je dostupná pouze pro řadu M. Existují některé položky, které byste měli zvážit, jako je například maximální počet disků podle typu virtuálního počítače. Další informace o akcelerátorem zápisu, naleznete v tématu [povolení akcelerátoru zápisu](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


Následující tabulka ukazuje několik příkladů můžete upravit velikost svazku protokolu.

| Zadejte velikost svazku dat a disku | svazek s protokolem a disk zadejte konfigurace 1 | svazek s protokolem a disk zadejte config 2 |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2,5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1,5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


Podobně jako horizontální navýšení kapacity SAP HANA, adresáři /hana/shared musí být sdílena mezi SAP HANA a virtuálním Počítačem 2.0 DT. Doporučujeme použít stejnou architekturu jako horizontální navýšení kapacity SAP HANA pomocí vyhrazených virtuálních počítačích, které fungují jako vysoce dostupný server pro systém souborů NFS. Pokud chcete poskytnout zálohování sdíleného svazku, použijte stejné návrhu. Ale záleží jen na vás, pokud vysoké dostupnosti je nezbytné, nebo pokud stačí použít vyhrazený virtuální počítač s dostatečnou kapacitou úložiště tak, aby fungoval jako záložní server.



### <a name="links-to-dt-20-documentation"></a>Odkazy na dokumentaci DT 2.0 

- [Průvodce instalací pro dynamické vrstvení SAP HANA a aktualizací](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [SAP HANA dynamické vrstvení výukové kurzy a materiály](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [SAP HANA dynamické vrstvení testování konceptu](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
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
Pokud máte připojení site-to-site mezi místními umístěními a Azure a komponenty SAP používáte, pravděpodobně již používáte SAProuter. Vzdálená podpora v tomto případě těchto kroků:

- Udržujte privátní a statickou IP adresu virtuálního počítače, který je hostitelem SAP HANA v konfiguraci SAProuter.
- Nakonfigurujte skupiny zabezpečení sítě, který je hostitelem virtuálního počítače HANA povolený provoz přes TCP/IP port 3299 podsítě.

Pokud nemáte směrovač SAP pro virtuální počítač se SAP HANA připojíte k Azure prostřednictvím Internetu, nainstalujte součást. Nainstalujte SAProuter v samostatných virtuálních počítačů v podsíti správy. 

Hrubý schéma pro nasazení SAP HANA, bez připojení site-to-site a s SAProuter naleznete na následujícím obrázku:

![Bez připojení site-to-site a SAProuter surové nasazení schématu pro SAP HANA](media/hana-vm-operations/hana-simple-networking3.PNG)

Nezapomeňte nainstalovat SAProuter na samostatném virtuálním počítači a ne ve virtuálním počítači Jumpboxu. Samostatný virtuální počítač musí mít statickou IP adresu. Pro připojení vašich SAProuter k SAProuter, který je hostitelem SAP, obraťte se na SAP pro IP adresu. SAProuter, který je hostitelem SAP je protějškem SAProuter instance, který nainstalujete na virtuální počítač. Umožňuje nakonfigurovat instanci SAProuter adresu IP ze SAP. V nastavení konfigurace pouze nezbytné port je TCP port 3299.

Další informace o tom, jak nastavit a spravovat připojení vzdálenou podporu prostřednictvím SAProuter najdete v tématu [dokumentaci k SAPU](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Vysoká dostupnost se SAP HANA na nativních virtuálních počítačích Azure
Při spuštění operačního systému SUSE Linux Enterprise Server pro SAP aplikace 12 SP1 nebo novější, můžete vytvořit Pacemaker cluster s využitím techniky STONITH zařízení. Použijte zařízení k nastavení konfigurace služby SAP HANA, která používá synchronní replikace s systémové replikace HANA a automatické převzetí služeb při selhání. Další informace o postupu instalace najdete v tématu [Příručka pro vysokou dostupnost SAP HANA pro Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
