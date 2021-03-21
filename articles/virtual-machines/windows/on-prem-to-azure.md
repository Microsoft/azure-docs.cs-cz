---
title: Migrace z AWS a dalších platforem na Managed Disks v Azure
description: Vytvářejte virtuální počítače v Azure pomocí VHD odeslaných z jiných cloudů, jako je AWS nebo jiných virtualizačních platforem, a využijte výhod Azure Managed Disks.
author: roygara
manager: twooley
ms.service: virtual-machines
ms.subervice: disks
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c817528ff26dd10112eaf69c7ad20f8fb5813695
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550395"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Migrace z Amazon Web Services (AWS) a dalších platforem na Managed Disks v Azure

Soubory VHD můžete nahrát z AWS nebo místních virtualizačních řešení do Azure a vytvořit tak virtuální počítače, které využívají Managed Disks. Azure Managed Disks odstraňuje nutnost spravovat účty úložiště pro virtuální počítače Azure s IaaS. Musíte zadat jenom typ (Premium nebo Standard) a velikost disku, kterou potřebujete, a Azure tento disk vytvoří a bude ho spravovat. 

Můžete nahrát generalizované a specializované virtuální pevné disky. 
- **Zobecněný virtuální pevný disk** – všechny informace o svém osobním účtu se odebraly pomocí nástroje Sysprep. 
- **Specializované virtuální** pevné disky – udržují uživatelské účty, aplikace a data dalších stavů z původního virtuálního počítače. 

> [!IMPORTANT]
> Před nahráním libovolného virtuálního pevného disku do Azure byste měli postupovat po [přípravě virtuálního pevného disku (VHD) Windows nebo VHDX k nahrání do Azure](prepare-for-upload-vhd-image.md)
>
>


| Scenario                                                                                                                         | Dokumentace                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Máte existující instance EC2 AWS, které byste chtěli migrovat na virtuální počítače Azure pomocí spravovaných disků.                              | [Přesunutí virtuálního počítače z Amazon Web Services (AWS) do Azure](aws-to-azure.md)                           |
| Máte virtuální počítač z jiné virtualizační platformy, kterou byste chtěli použít jako image k vytvoření několika virtuálních počítačů Azure. | [Nahrajte zobecněný virtuální pevný disk a použijte ho k vytvoření nového virtuálního počítače v Azure.](upload-generalized-managed.md) |
| Máte jedinečně přizpůsobený virtuální počítač, který byste chtěli znovu vytvořit v Azure.                                                      | [Nahrání specializovaného virtuálního pevného disku do Azure a vytvoření nového virtuálního počítače](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Přehled Managed Disks

Azure Managed Disks zjednodušuje správu virtuálních počítačů tím, že odstraňuje nutnost spravovat účty úložiště. Managed Disks taky využívat lepší spolehlivost virtuálních počítačů ve skupině dostupnosti. Zajišťuje, aby byly disky různých virtuálních počítačů ve skupině dostupnosti dostatečně izolované, aby nedocházelo k jednomu bodu selhání. Automaticky umístí disky různých virtuálních počítačů do skupiny dostupnosti v různých jednotkách škálování úložiště (razítka), které omezují dopad selhání jedné jednotky úložiště kvůli selháním hardwaru a softwaru.
Podle svých potřeb můžete vybírat ze čtyř typů úložišť. Další informace o dostupných typech disků najdete v našem článku [Výběr typu disku](../disks-types.md).

## <a name="plan-for-the-migration-to-managed-disks"></a>Plánování migrace na Managed Disks

Tato část vám pomůže udělat si nejlepší rozhodnutí o typech virtuálních počítačů a disků.

Pokud plánujete migrovat z nespravovaných disků na Managed disks, měli byste si být vědomi, že uživatelé s rolí [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) nebudou moct měnit velikost virtuálního počítače (protože by to bylo možné před převodem). Důvodem je to, že virtuální počítače se spravovanými disky vyžadují, aby měl uživatel na discích s operačním systémem oprávnění Microsoft. COMPUTE/disks/Write.

### <a name="location"></a>Umístění

Vyberte umístění, kde jsou k dispozici Azure Managed Disks. Pokud migrujete na Premium Managed Disks, zajistěte, aby služba Premium Storage byla dostupná v oblasti, na kterou plánujete migrovat. Aktuální informace o dostupných umístěních najdete v tématu [služby Azure podle oblasti](https://azure.microsoft.com/regions/#services) .

### <a name="vm-sizes"></a>Velikost virtuálních počítačů

Pokud migrujete na Premium Managed Disks, je nutné aktualizovat velikost virtuálního počítače na Premium Storage dostupné velikosti v oblasti, kde se nachází virtuální počítač. Zkontrolujte velikosti virtuálních počítačů, které jsou Premium Storage podporuje. Specifikace velikosti virtuálního počítače Azure jsou uvedené v části [velikosti pro virtuální počítače](../sizes.md).
Přečtěte si výkonnostní charakteristiky virtuálních počítačů, které pracují se Premium Storage a vyberte nejvhodnější velikost virtuálního počítače, která nejlépe vyhovuje vašim úlohám. Ujistěte se, že je ve vašem VIRTUÁLNÍm počítači k dispozici dostatečná šířka pásma, aby bylo možné disk provozovat.

### <a name="disk-sizes"></a>Velikosti disků

**Managed Disks úrovně Premium**

K virtuálnímu počítači se dá použít sedm typů disků úrovně Premium, které se dají používat s vaším virtuálním počítačem, přičemž každý z nich má konkrétní omezení IOPs a propustnost Vezměte v úvahu tato omezení při výběru typu disku Premium pro váš virtuální počítač na základě potřeb vaší aplikace z důvodu kapacity, výkonu, škálovatelnosti a zatížení ve špičce.

| Typ disků úrovně Premium  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Velikost disku           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| Vstupně-výstupní operace za sekundu / disk       | 120   | 240   | 500   | 1100  |2300              | 5000              | 7 500              | 7 500              | 
| Propustnost / disk | 25 MB za sekundu  | 50 MB za sekundu  | 100 MB za sekundu | 125 MB za sekundu |150 MB za sekundu | 200 MB za sekundu | 250 MB za sekundu | 250 MB za sekundu |

**Standardní Managed Disks**

Existuje sedm typů standardních spravovaných disků, které se dají používat s vaším VIRTUÁLNÍm počítačem. Každá z nich má různou kapacitu, ale má stejný počet vstupně-výstupních operací a omezení propustnosti. Vyberte typ Standard Managed disks založený na potřebách kapacity vaší aplikace.

| Disk typu Standard  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Velikost disku           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1024 GB (1 TB)   | 2048 GB (2 TB)    | 4095 GB (4 TB)   | 
| Vstupně-výstupní operace za sekundu / disk       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Propustnost / disk | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu |60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 

### <a name="disk-caching-policy"></a>Zásady ukládání do mezipaměti disku 

**Managed Disks úrovně Premium**

Ve výchozím nastavení jsou zásady ukládání do mezipaměti disku jen pro *čtení* pro všechny disky Premium data a *ke čtení i zápisu* pro disk s operačním systémem Premium připojené k virtuálnímu počítači. Toto nastavení konfigurace se doporučuje pro dosažení optimálního výkonu pro IOs vaší aplikace. U datových disků, které jsou výhradně pro zápis nebo pouze pro zápis (například souborů protokolu SQL Server), zakažte ukládání do mezipaměti disku, abyste dosáhli lepšího výkonu aplikace.

### <a name="pricing"></a>Ceny

Projděte si [ceny Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/). Ceny Managed Disks Premium jsou stejné jako nespravované disky Premium. Ceny pro standardní Managed Disks se ale liší od standardních nespravovaných disků.


## <a name="next-steps"></a>Další kroky

- Před nahráním libovolného virtuálního pevného disku do Azure byste měli postupovat po [přípravě virtuálního pevného disku (VHD) Windows nebo VHDX k nahrání do Azure](prepare-for-upload-vhd-image.md)