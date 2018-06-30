---
title: SAP HANA operace v Azure | Microsoft Docs
description: Provozní příručka pro SAP HANA systémy, které jsou nasazeny na virtuálních počítačích Azure.
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
ms.openlocfilehash: 59adc0bccd523818aad1feca2d0cf260f45d899c
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110363"
---
# <a name="sap-hana-on-azure-operations-guide"></a>SAP HANA na Azure provozní příručky
Tento dokument obsahuje pokyny pro operační systémy SAP HANA, které jsou nasazeny na Azure nativní virtuální počítače (VM). Tento dokument není určen k nahrazení standardní SAP dokumentace, která zahrnuje následující obsah:

- [Příručka pro správu SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Průvodce instalací SAP](https://service.sap.com/instguides)
- [Poznámky k SAP](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Požadavky
Chcete-li tohoto průvodce použijte, je třeba základní znalosti o Azure následující součásti:

- [Virtuální počítače Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure sítě a virtuální sítě](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Další informace o SAP NetWeaver a další součásti SAP v Azure, najdete v článku [SAP v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) části [dokumentace k Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Důležité informace o základní nastavení
Následující části popisují základní nastavení aspekty pro nasazení SAP HANA systémy na virtuálních počítačích Azure.

### <a name="connect-into-azure-virtual-machines"></a>Připojení do virtuálních počítačů Azure
Jak je uvedeno v [virtuální počítače Azure Průvodce plánováním](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), existují dvě základní metody pro připojení do virtuálních počítačích Azure:

- Připojení přes internet a veřejné koncové body na virtuálním počítači přejít, nebo na virtuální počítač se systémem SAP HANA.
- Připojení přes [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) nebo Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Připojení Site-to-site prostřednictvím sítě VPN nebo ExpressRoute je potřebný u produkčních scénářích. Tento typ připojení je také potřeba pro nevýrobní prostředí scénáře, které informačního kanálu do produkčních scénářích, kde se používá SAP softwaru. Následující obrázek ukazuje příklad připojení webů:

![Připojení webů](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Vyberte typy virtuálních počítačů Azure
Virtuální počítač Azure typy, které lze použít pro produkčních scénářích jsou uvedeny v [SAP dokumentaci IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Pro scénáře nevýrobní prostředí je k dispozici širší rozsah nativní typy virtuálního počítače Azure.

>[!NOTE]
> Mimo produkční scénářů použití typy virtuálních počítačů, které jsou uvedeny v [Poznámka SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Pro použití virtuálních počítačů Azure produkčních scénářích, zkontrolujte SAP HANA certifikované virtuálních počítačů v SAP publikovaná [certifikované platformy IaaS seznamu](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Nasazení virtuálních počítačů v Azure pomocí:

- Portál Azure.
- Rutiny Azure PowerShell.
- Azure CLI.

Také můžete nasadit kompletní nainstalované SAP HANA platformy v rámci služeb virtuálního počítače Azure pomocí [SAP Cloudová platforma](https://cal.sap.com/). Proces instalace je popsán v [nasazení SAP S nebo 4HANA nebo BW/4HANA v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) nebo s automatizace vydané [zde](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

### <a name="choose-azure-storage-type"></a>Zvolte typ úložiště Azure
Azure nabízí dva typy úložiště, které jsou vhodné pro virtuální počítače Azure, který běží SAP HANA:

- [Úložiště Azure úrovně Standard](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure nabízí dvě metody nasazení pro virtuální pevné disky na Azure Standard a Premium Storage. Pokud to umožňují celkové scénář, využít výhod [Azure spravované disku](https://azure.microsoft.com/services/managed-disks/) nasazení.

Seznam typů úložišť a jejich SLA IOPS a úložiště propustnost, zkontrolujte [dokumentace k Azure pro spravované disky](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="configuring-the-storage-for-azure-virtual-machines"></a>Konfigurace úložiště pro virtuální počítače Azure

Pokud to i pokud jste si zakoupili SAP HANA zařízení pro místní, jste museli nikdy zajímají subsystémy vstupně-výstupních operací a jeho funkce, protože dodavatele zařízení musí být jistý, že jsou splněny požadavky na minimální úložiště pro SAP HANA. Jak infrastrukturu Azure vytvoříte sami, můžete také měli vědět některé z těchto požadavků také pochopit požadavky na konfiguraci, které doporučujeme v následujících částech. Nebo pro případy, kde konfigurujete virtuální počítače chcete spustili SAP HANA. Některé vlastnosti, které se zobrazí výzva jsou výsledkem je potřeba:

- Povolit čtení a zápis svazek na /hana/log 250 MB za sekundu minimálně s velikostí vstupně-výstupních operací 1 MB
- Povolit čtení aktivitu alespoň 400MB/s pro /hana/data 16 MB a 64 MB velikostí vstupně-výstupních operací
- Povolit zápis aktivitu alespoň 250MB/s pro /hana/data s 16 MB a 64 MB velikostí vstupně-výstupních operací

Zadaný této nízkou úložiště latence je velmi důležitá pro systémy databázového systému i jako ty, jako je SAP HANA ponechat data v paměti. Kritické cestu, do úložiště je většinou kolem zápisů protokolu transakcí systémů databázového systému. Ale také jako operace zápisu úložných bodů nebo po obnovení při havárii může být rozhodující načítání dat v paměti. Proto je nutné k /hana/data a /hana/log svazky využít Azure prémiové disky. Pro dosažení minimální propustnost/hana/protokolů a datových/hana/požadovanou SAP, které potřebujete k vytvoření RAID 0 pomocí MDADM nebo LVM přes víc disků Storage úrovně Premium a používat jako/hana/data a svazky protokolu/hana/svazky RAID. Stripe velikostí pro RAID 0 doporučení je použít:

- 64 tisíc nebo 128 kB pro/hana/data
- 32 kB pro/hana/protokolu

> [!NOTE]
> Nemusíte konfigurovat žádné úrovně redundance pomocí svazky RAID, protože Azure Premium a Standard storage zachovat tři bitové kopie virtuálního pevného disku. Použití svazek RAID je čistě konfigurace svazky, které poskytují dostatečná propustnost vstupu/výstupu.

Shromažďování počet virtuálních pevných disků Azure pod RAID, je kumulativní ze strany propustnost IOPS a úložiště. Ano když vložíte přes 3 x P30 Azure Premium Storage disky RAID 0, ho měl dát třikrát IOPS a třikrát propustnost úložiště jednoho disku Azure Premium Storage P30.

Nemusíte konfigurovat ukládání do mezipaměti na discích používaných pro /hana/data a /hana/log Storage úrovně Premium. Všechny disky vytváření těchto svazků by měl mít ukládání do mezipaměti těchto disků, nastavte hodnotu 'None'.

Mějte celkovou propustnost vstupně-výstupních operací na virtuální počítač při změně velikosti nebo rozhodování pro virtuální počítač. Celková propustnost úložiště virtuálního počítače je popsána v článku [paměťově optimalizované velikosti virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

#### <a name="cost-conscious-azure-storage-configuration"></a>Cenově vědomá toho konfigurace úložiště Azure
Následující tabulka znázorňuje konfiguraci typů virtuálních počítačů, které zákazníci běžně používají k hostiteli SAP HANA na virtuálních počítačích Azure. Může být některé typy virtuálních počítačů, které nemusí splňovat všechna minimální kritéria pro SAP HANA. Ale dosavadní vynechali těchto virtuálních počítačů bez problémů provést pro scénáře nevýrobní prostředí. 

> [!NOTE]
> Pro produkční scénáře, zkontrolujte, zda určitý typ virtuálního počítače je podporován pro SAP HANA SAP v [SAP dokumentaci IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).


| VIRTUÁLNÍ POČÍTAČ SKU | Paměť RAM | Max. VIRTUÁLNÍ POČÍTAČ VSTUPNĚ-VÝSTUPNÍCH OPERACÍ<br /> Propustnost | / hana/protokolu a/hana/data<br /> rozdělená s LVM nebo MDADM | / hana/sdílené | / root svazku | / usr/sap | Hana/zálohování |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 GiB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 GiB | 1200 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M32ts | 192 giB | 500 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 |1 x S30 |
| M64s | 1000 giB | 1000 MB/s | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2000 giB | 2000 MB/s |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


Disky doporučeno pro typy menší virtuální počítač s 3 x P20 oversize svazky týkající se podle doporučení místa [SAP TDI úložiště dokument White Paper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Ale volba, zobrazovat v tabulce byl proveden zajistit dostatek propustnost disku pro SAP HANA. Pokud budete potřebovat změny /hana/backup svazek, který byl dimenzované pro zálohování, které představují dvakrát svazku paměti, nebojte se, zda je upravit.   
Zjistil, zda bude propustnost úložiště pro jiné navrhované svazky se zatížením, které chcete spustit. Pokud zatížení vyžaduje vyšší svazky pro /hana/data a /hana/log, je potřeba zvýšit počet Azure Premium Storage virtuální pevné disky. Změna velikosti svazku s více virtuálních pevných disků než uvedené zvýší propustnost IOPS a vstupně-výstupní operace v rámci typ virtuálního počítače Azure. 

> [!NOTE]
> Konfigurace výše by nijak přínosné [jeden virtuální počítač Azure SLA k Virtuálním počítačům](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) vzhledem k tomu, že používá kombinaci Storage úrovně Premium a Azure Standard Storage. Výběr však jste vybrali k optimalizaci nákladů.


#### <a name="azure-storage-configuration-to-benefit-for-meeting-single-vm-sla"></a>Konfigurace Azure úložiště, abyste mohli využívat pro splnění jedné SLA k Virtuálním počítačům
Pokud chcete využívat [jeden virtuální počítač Azure SLA k Virtuálním počítačům](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), budete muset použít výhradně Azure Premium Storage virtuální pevné disky.

> [!NOTE]
> Pro produkční scénáře, zkontrolujte, zda určitý typ virtuálního počítače je podporován pro SAP HANA SAP v [SAP dokumentaci IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

| VIRTUÁLNÍ POČÍTAČ SKU | Paměť RAM | Max. VIRTUÁLNÍ POČÍTAČ VSTUPNĚ-VÝSTUPNÍCH OPERACÍ<br /> Propustnost | / hana/protokolu a/hana/data<br /> rozdělená s LVM nebo MDADM | / hana/sdílené | / root svazku | / usr/sap | Hana/zálohování |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 GiB | 768 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| E64v3 | 443 GiB | 1200 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| M32ts | 192 giB | 500 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| M64s | 1000 giB | 1000 MB/s | 2 x P30 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 giB | 2000 MB/s |3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |


Disky doporučeno pro typy menší virtuální počítač s 3 x P20 oversize svazky týkající se podle doporučení místa [SAP TDI úložiště dokument White Paper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Ale volba, zobrazovat v tabulce byl proveden zajistit dostatek propustnost disku pro SAP HANA. Pokud budete potřebovat změny /hana/backup svazek, který byl dimenzované pro zálohování, které představují dvakrát svazku paměti, nebojte se, zda je upravit.  
Zjistil, zda bude propustnost úložiště pro jiné navrhované svazky se zatížením, které chcete spustit. Pokud zatížení vyžaduje vyšší svazky pro /hana/data a /hana/log, je potřeba zvýšit počet Azure Premium Storage virtuální pevné disky. Změna velikosti svazku s více virtuálních pevných disků než uvedené zvýší propustnost IOPS a vstupně-výstupní operace v rámci typ virtuálního počítače Azure. 



#### <a name="storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Řešení úložiště s Azure zápisu akcelerátoru pro virtuální počítače Azure M-Series
Azure akcelerátoru zápisu je funkce, které je získávání vrácena výhradně pro virtuální počítače M-Series. Jako název stavy, je účelem funkce vstupně-výstupních operací latenci zápisů Azure Premium Storage. Pro SAP HANA zápisu akcelerátoru by měl použít u pouze /hana/log svazku. Konfigurace zobrazí, pokud je proto nutné změnit. Hlavní změna rozpadu mezi /hana/data a /hana/log je-li používat Azure zápisu akcelerátoru pouze /hana/log svazek. 

> [!IMPORTANT]
> SAP HANA certifikační pro virtuální počítače Azure M-Series je výhradně s Azure zápisu akcelerátoru /hana/log svazku. V důsledku toho se očekává nasazení SAP HANA scénář v produkčním prostředí na virtuálních počítačích Azure M-Series konfigurovat pomocí Azure zápisu akcelerátoru /hana/log svazku.  

> [!NOTE]
> Pro produkční scénáře, zkontrolujte, zda určitý typ virtuálního počítače je podporován pro SAP HANA SAP v [SAP dokumentaci IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Doporučené konfigurace vypadat podobně jako:

| VIRTUÁLNÍ POČÍTAČ SKU | Paměť RAM | Max. VIRTUÁLNÍ POČÍTAČ VSTUPNĚ-VÝSTUPNÍCH OPERACÍ<br /> Propustnost | / hana/dat | / hana/protokolu | / hana/sdílené | / root svazku | / usr/sap | Hana/zálohování |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 giB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 giB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 giB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

Zjistil, zda bude propustnost úložiště pro jiné navrhované svazky se zatížením, které chcete spustit. Pokud zatížení vyžaduje vyšší svazky pro /hana/data a /hana/log, je potřeba zvýšit počet Azure Premium Storage virtuální pevné disky. Změna velikosti svazku s více virtuálních pevných disků než uvedené zvýší propustnost IOPS a vstupně-výstupní operace v rámci typ virtuálního počítače Azure.

Azure akcelerátoru zapisovat pouze funguje ve spojení s [discích spravovaných pomocí Azure](https://azure.microsoft.com/services/managed-disks/). Proto alespoň Azure Premium Storage disky tvořící /hana/log svazek je nutné nasadit jako spravované disky.

Existují omezení Azure Premium Storage VHD na virtuální počítač, který může podporovat Azure zápisu akcelerátoru. Aktuální omezení jsou:

- 16 virtuálních pevných disků pro M128xx virtuálních počítačů
- 8 virtuální pevné disky pro M64xx virtuálních počítačů
- 4 virtuální pevné disky pro M32xx virtuálních počítačů

Podrobnější pokyny o tom, jak povolit zápis akcelerátoru Azure najdete v článku [zápisu akcelerátoru](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Podrobnosti a omezení pro zápis akcelerátoru Azure naleznete v dokumentaci stejné.


### <a name="set-up-azure-virtual-networks"></a>Nastavení virtuální sítě Azure
Pokud máte připojení site-to-site do Azure přes VPN nebo ExpressRoute, musíte mít alespoň jeden [virtuální síť Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) který je připojený prostřednictvím brány virtuální sítě VPN nebo ExpressRoute s okruhem. Brána virtuální žije v podsíť ve virtuální síti Azure. Chcete-li nainstalovat SAP HANA, vytvořte dva další podsítě v rámci virtuální sítě. Jednu podsíť hostuje virtuální počítače ke spuštění instance SAP HANA. Další podsítě spustí Jumpbox nebo správu virtuálních počítačů pro hostování SAP HANA Studio nebo jiného softwaru pro správu.

Když instalujete virtuální počítače spustit SAP HANA, třeba virtuální počítače:

- Dva virtuální síťové karty nainstalované: jednu síťovou kartu pro připojení k podsíti správy a jeden síťový adaptér a z místní sítě nebo jiné sítě, se připojit k instanci SAP HANA ve virtuálním počítači Azure.
- Statickou privátní IP adresy, které jsou nasazeny pro obě virtuálních síťových karet.

Přehled různých metod pro přiřazení IP adres najdete v tématu [IP adres, typy a metody přidělování v Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Pro virtuální počítače spuštěné SAP HANA by měla fungovat s přiřadit statické IP adresy. Důvodem je, že některé konfigurační atributy pro HANA odkazují IP adresy.

[Azure skupiny zabezpečení sítě (Nsg)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) jsou používána ke směrování provozu, který se směruje na instanci SAP HANA nebo Jumpbox. Jsou přidružené k SAP HANA podsíť a podsíť správy skupin Nsg.

Následující obrázek ukazuje přehled hrubý nasazení schématu pro SAP HANA:

![Hrubý nasazení schématu pro SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)


K nasazení SAP HANA v Azure bez připojení site-to-site, přístup k SAP HANA instance, když veřejnou IP adresu. Virtuální počítač Azure, který běží Jumpbox virtuálního počítače musí být přidělit IP adresu. V tomto scénáři základní nasazení spoléhá na Azure vestavěné služby DNS přeložit názvy hostitelů. V složitější nasazení, kde se používají veřejné IP adresy jsou zvlášť důležité, Azure vestavěné služby DNS. Pomocí skupin Nsg Azure můžete omezit otevřené porty nebo rozsahy IP adres, které se můžou připojit do Azure podsítí s prostředky, které mají veřejné IP adresy. Následující obrázek znázorňuje hrubé schématu pro nasazení SAP HANA bez připojení site-to-site:
  
![Hrubý nasazení schématu pro SAP HANA bez připojení site-to-site](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Operace pro nasazení SAP HANA na virtuálních počítačích Azure
Následující části popisují některé operace související s nasazováním systémů SAP HANA na virtuálních počítačích Azure.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Zálohování a obnovení operací na virtuálních počítačích Azure
Tyto dokumenty popisují, jak zálohovat a obnovovat nasazení SAP HANA:

- [Přehled zálohování SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Zálohování na úrovni souborů SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Srovnávací test snímku úložiště SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Spuštění a restartování virtuálních počítačů, které obsahují SAP HANA
Hlavní funkce veřejného cloudu Azure je, že se vám účtovat pouze pro vaše výpočetní minut. Například při vypnutí virtuálního počítače se systémem SAP HANA se fakturuje pouze náklady na úložiště během této doby. Další funkce je dostupná, když zadáte statické IP adresy pro virtuální počítače v původním nasazení. Po restartování virtuálního počítače, který má SAP HANA restartuje virtuální počítač s jeho předchozí IP adresy. 


### <a name="use-saprouter-for-sap-remote-support"></a>Použijte SAProuter podporu vzdálené SAP
Pokud máte připojení site-to-site mezi vaší místní umístění a Azure, a pak pravděpodobně již používáte SAProuter používáte SAP součásti. V takovém případě dokončete následující položky pro vzdálené podpory:

- Udržujte privátní a statickou IP adresu virtuálního počítače, který je hostitelem SAP HANA v konfiguraci SAProuter.
- Nakonfigurujte podsítě, který je hostitelem virtuálního počítače HANA umožňující přenos prostřednictvím portu TCP/IP 3299 NSG.

Pokud se připojujete k Azure přes internet, a pro virtuální počítač s SAP HANA nemáte směrovač SAP, budete muset nainstalovat součást. Nainstalujte SAProuter v samostatných virtuálních počítačů v podsíti správy. Následující obrázek znázorňuje hrubé schématu pro nasazení SAP HANA bez připojení site-to-site a s SAProuter:

![Bez připojení site-to-site a SAProuter extenzivních nasazení schématu pro SAP HANA](media/hana-vm-operations/hana-simple-networking3.PNG)

Ujistěte se, že nainstalujete SAProuter v samostatných virtuálních počítačů a není v Jumpbox virtuálního počítače. Samostatný virtuální počítač musí mít statickou IP adresu. Pokud chcete připojit vaše SAProuter k SAProuter, který je hostitelem SAP, obraťte se na SAP pro IP adresu. (SAProuter, který je hostitelem SAP je protějškem SAProuter instance, kterou si nainstalujete do virtuálního počítače.) Slouží ke konfiguraci vaší instance SAProuter IP adresu z SAP. V nastavení konfigurace a jenom nezbytné port je TCP port 3299.

Další informace o tom, jak nastavit a spravovat podporu vzdáleného připojení prostřednictvím SAProuter najdete v tématu [SAP dokumentaci](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Vysoká dostupnost s SAP HANA na virtuálních počítačích Azure nativní
Pokud používáte systém SUSE Linux 12 SP1 nebo novější, můžete vytvořit cluster kardiostimulátor s STONITH zařízení. Zařízení můžete použít k nastavení konfigurace SAP HANA, který používá synchronní replikaci s HANA systému replikace a automatické převzetí služeb při selhání. Další informace o postupu instalace najdete v tématu [Průvodce SAP HANA vysoké dostupnosti pro virtuální počítače Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
