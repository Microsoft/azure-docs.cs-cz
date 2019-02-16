---
title: Migrace z AWS a dalších platforem na Managed Disks v Azure | Dokumentace Microsoftu
description: Vytvoření virtuálních počítačů v Azure pomocí virtuální pevné disky nahrávány z ostatních cloudů, jako je AWS nebo jiných virtualizačních platforem a začněte využívat Azure Managed Disks.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 05e687ab31b6c19193076033e1350952549d26e0
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56330745"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Migrace z Amazon Web Services (AWS) a dalších platforem na Managed Disks v Azure

Můžete nahrát soubory virtuálního pevného disku z AWS nebo v místním řešení virtualizace do Azure k vytvoření virtuálních počítačů, které budou využívat služby Managed Disks. Služba Azure Managed Disks odpadá nutnost spravovat účty úložiště pro virtuální počítače Azure IaaS. Je nutné jenom zadat typ (Premium nebo Standard) a velikost disku je nutné, a Azure vytvoří a spravuje disk za vás. 

Můžete nahrát zobecněný a specializovaných virtuálních pevných disků. 
- **Zobecněný virtuální pevný disk** -zaznamenala všechny informace vašeho osobního účtu odebrat, pomocí nástroje Sysprep. 
- **Specializované virtuální pevný disk** – zachovává uživatelské účty, aplikace a další data o stavu z původního virtuálního počítače. 

> [!IMPORTANT]
> Před odesláním jakéhokoli virtuálního pevného disku do Azure, měli byste postupovat podle [Příprava Windows VHD nebo VHDX, který chcete nahrát do Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>


| Scénář                                                                                                                         | Dokumentace                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Máte existující instancí EC2 služby AWS, které chcete migrovat virtuální počítače Azure s použitím spravovaných disků                              | [Přesun virtuálního počítače z Amazon Web Services (AWS) do Azure](aws-to-azure.md)                           |
| Máte virtuální počítač z jiné virtualizační platforma, která chcete použít jako image k vytvoření několika virtuálních počítačů Azure. | [Nahrání generalizovaného virtuálního pevného disku a použít ho k vytvoření nového virtuálního počítače v Azure](upload-generalized-managed.md) |
| Máte jednoznačně vlastní virtuální počítač, který chcete znovu vytvořit v Azure.                                                      | [Nahrání specializovaného virtuálního pevného disku do Azure a vytvoření nového virtuálního počítače](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Přehled služby Managed Disks

Azure Managed Disks zjednodušuje správu virtuálních počítačů tím, že si nemusíte spravovat účty úložiště. Managed Disks také výhody z vyšší spolehlivost virtuálních počítačů ve skupině dostupnosti. Zajišťuje, že disky různých virtuálních počítačů ve skupině dostupnosti jsou dostatečně izolované od sebe, aby jediný bod selhání. Automaticky umístí disky různých virtuálních počítačů ve skupině dostupnosti v různých Uložišť jednotek škálování (razítka) který omezuje dopad selhání škálovací jednotky jednoho úložiště se situace způsobena vlivem hardwaru a softwaru selhání.
Podle svých potřeb, můžete vybrat z čtyři typy úložišť. Další informace o typech disků k dispozici, najdete v našem článku [vyberte typ disku](disks-types.md).

## <a name="plan-for-the-migration-to-managed-disks"></a>Plánování migrace do služby Managed Disks

V této části vám umožní nejlepší rozhodnutí o typech virtuálních počítačů a disk.

Pokud plánujete migraci z nespravovaných disků na managed disks, byste měli vědět, které uživatelé s [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) role nebude možné změnit velikost virtuálního počítače (jak se může před převod). Je to proto, že virtuální počítače se spravovanými disky vyžadují, aby uživatel měl oprávnění Microsoft.Compute/disks/write pro disky s operačním systémem.

### <a name="location"></a>Umístění

Vyberte umístění, kde jsou k dispozici Azure Managed Disks. Pokud provádíte migraci do služby Managed Disks úrovně Premium, ujistěte se také, že Premium storage je dostupné v oblasti, kde máte v úmyslu migrovat do. Zobrazit [služeb Azure podle oblasti](https://azure.microsoft.com/regions/#services) aktuální informace o dostupných umístění.

### <a name="vm-sizes"></a>Velikost virtuálních počítačů

Pokud provádíte migraci do služby Managed Disks úrovně Premium, budete muset aktualizovat velikost virtuálního počítače do Premium Storage podporuje velikost dostupná v oblasti, kde je umístěn virtuální počítač. Kontrola velikosti virtuálních počítačů, které jsou schopné Premium Storage. Specifikace velikosti virtuálního počítače Azure jsou uvedeny v [velikosti virtuálních počítačů](sizes.md).
Zkontrolujte charakteristiky výkonu virtuálních počítačů, které pracují se službou Premium Storage a zvolte nejvhodnější velikosti virtuálního počítače, který nejlépe vyhovuje vaší úlohy. Ujistěte se, že je dostatečná šířka pásma dostupné na virtuálním počítači Centrum umožňující prosazovat diskové přenosy.

### <a name="disk-sizes"></a>Velikost disků

**Premium Managed Disks**

Existuje sedm typů spravovaných disků úrovně premium se dají s virtuálním Počítačem a obsahují konkrétní IOPs a propustnost omezení. Vzít v úvahu tato omezení, pokud vyberete disk typu Premium pro virtuální počítač podle potřeb vaší aplikace z hlediska kapacity, výkonu, škálovatelnosti a načte ve špičce.

| Typ disky Premium  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Velikost disku           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| Vstupně-výstupní operace za sekundu / disk       | 120   | 240   | 500   | 1100  |2300              | 5000              | 7500              | 7500              | 
| Propustnost / disk | 25 MB za sekundu  | 50 MB za sekundu  | 100 MB za sekundu | 125 MB za sekundu |150 MB za sekundu | 200 MB za sekundu | 250 MB za sekundu | 250 MB za sekundu |

**Spravované disky úrovně Standard**

Existuje sedm typů spravované disky úrovně standard, které lze použít s virtuálním Počítačem. Každý z nich mít jiné kapacitě, ale mají stejné vstupně-výstupních operací a omezení propustnosti. Zvolte typ služby Standard Managed disks na základě potřeb kapacity vaší aplikace.

| Disk typu Standard  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Velikost disku           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1024 GB (1 TB)   | 2048 GB (2TB)    | 4095 GB (4 TB)   | 
| Vstupně-výstupní operace za sekundu / disk       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Propustnost / disk | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu |60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 

### <a name="disk-caching-policy"></a>Zásady ukládání do mezipaměti na disku 

**Premium Managed Disks**

Ve výchozím nastavení, disk zásady ukládání do mezipaměti je *jen pro čtení* pro všechny úrovně Premium datové disky, a *čtení a zápis* pro disk s operačním systémem Premium připojené k virtuálnímu počítači. Toto nastavení konfigurace se doporučuje pro zajištění optimálního výkonu pro vaše aplikace IOs. Náročné na zápis nebo jen pro zápis datové disky (jako jsou například soubory protokolu serveru SQL Server) Zakázání používání mezipaměti disku, takže můžete dosáhnout lepší výkon aplikace.

### <a name="pricing"></a>Ceny

Zkontrolujte [ceny pro Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/). Ceny služby Managed Disks úrovně Premium je stejná jako nespravované disky Premium. Ale ceny pro Standard Managed Disks se liší od Unmanaged Disks úrovně Standard.


## <a name="next-steps"></a>Další kroky

- Před odesláním jakéhokoli virtuálního pevného disku do Azure, měli byste postupovat podle [Příprava Windows VHD nebo VHDX, který chcete nahrát do Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
