---
title: Migrace z AWS a jiné platformy na spravované disky v Azure | Microsoft Docs
description: Vytvořit virtuální počítače v Azure pomocí virtuální pevné disky odeslán z ostatních cloudů jako AWS nebo jiných virtualizačních platforem a využijte výhod Azure spravované disků.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/07/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0440eccbdf76fc58fadf46de2508d362c0de6cc3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Migrace z Amazon Web Services (AWS) a jiné platformy na spravované disky v Azure

Můžete nahrát souborů VHD z AWS nebo místní virtualizačních řešeních Azure k vytvoření virtuálních počítačů, které využít výhod spravované disky. Disky systému Azure spravované eliminuje nutnost spravovat účty úložiště pro virtuální počítače Azure IaaS. Budete muset zadat pouze typ (Standard nebo Premium) a velikost disku je nutné, a Azure vytváří a spravuje disku za vás. 

Můžete nahrát zobecněný a specializované virtuálních pevných disků. 
- **Zobecněný virtuální pevný disk** -zaznamenala všechny vaše osobní účet informace odebrat pomocí nástroje Sysprep. 
- **Specializuje virtuálního pevného disku** -uchovává uživatelské účty, aplikace a další data o stavu z vašeho původního virtuálního počítače. 

> [!IMPORTANT]
> Před nahráním jakéhokoli virtuálního pevného disku do Azure, postupujte podle [Příprava Windows VHD nebo VHDX, který chcete nahrát do Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>


| Scénář                                                                                                                         | Dokumentace                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Máte existující instance AWS EC2, které chcete migrovat virtuální počítače Azure pomocí spravovaných disků                              | [Přesunout virtuální počítač z Amazon Web Services (AWS) do Azure](aws-to-azure.md)                           |
| Máte virtuální počítač z jiné virtualizační platformy, které chcete použít jako obrázek vytvořit víc virtuálních počítačů Azure. | [Nahrát zobecněný virtuální pevný disk a použít ho k vytvoření nového virtuálního počítače v Azure](upload-generalized-managed.md) |
| Musíte jednoznačně vlastní virtuální počítač, který chcete znovu vytvořit v Azure.                                                      | [Nahrání specializované virtuálního pevného disku do Azure a vytvoření nového virtuálního počítače](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Přehled spravovaných disků

Disky systému Azure spravované usnadňuje správu virtuálních počítačů odebráním potřeba spravovat účty úložiště. Spravovat disky také výhody z vyšší spolehlivost virtuálních počítačů ve skupině dostupnosti. Zajišťuje, zda jsou disky různých virtuálních počítačů ve skupině dostupnosti dostatečně od sebe navzájem oddělené předejdete jediný bod selhání. Automaticky umístí disky pro různé virtuální počítače do skupiny dostupnosti v různých jednotek škálování úložiště (razítka) omezující vliv jednoho úložiště škálování jednotky chyby způsobené hardwaru a chyby softwaru. V závislosti na vašich potřebách můžete vybrat ze dvou typů možností úložiště: 
 
- [Pro prémiové disky spravované](premium-storage.md) jsou plnou stavu jednotky SSD (Solid-State Drive) založené na úložná média, který poskytuje vysoký výkon, podporu disků s nízkou latencí pro virtuální počítače běžící I náročnými úlohy. Migrací prémiové disky spravované můžete využít výhod rychlosti a výkonu z těchto disků.  

- [Standardní disky spravované](standard-storage.md) používat jednotku pevného disku (HDD) na základě paměťových médií a jsou nejvhodnější pro vývoj/testování a další úlohy nepravidelným přístup, které jsou menší citlivá s ohledem na výkon proměnlivost.  

## <a name="plan-for-the-migration-to-managed-disks"></a>Plánování migrace na spravované disky

V této části můžete vytvořit nejlepší rozhodnutí o typech virtuálního počítače a disku.

Pokud plánujete o migraci z disků nespravované na spravované disky, byste měli vědět kterou můžou uživatelé s [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) role nebude možné změnit velikost virtuálního počítače (jak se může před převod). Je to proto, že virtuální počítače s spravované disky vyžadovat, aby uživatel má oprávnění Microsoft.Compute/disks/write na discích operačního systému.

### <a name="location"></a>Umístění

Vyberte umístění, kde Azure spravované disky jsou dostupné. Při migraci disků spravovaných Premium také zajistíte, že úložiště Premium je k dispozici v oblasti, kde máte v úmyslu migrovat. V tématu [služby Azure podle oblasti](https://azure.microsoft.com/regions/#services) aktuální informace o dostupných umístění.

### <a name="vm-sizes"></a>Velikost virtuálních počítačů

Pokud provádíte migraci na disky spravované Premium, budete muset aktualizovat velikost virtuálního počítače do Storage úrovně Premium podporující velikost dostupné v oblasti, kde je umístěn virtuální počítač. Zkontrolujte velikosti virtuálních počítačů, které jsou podporující Storage úrovně Premium. Specifikace velikosti virtuálního počítače Azure, jsou uvedeny v [velikosti virtuálních počítačů](sizes.md).
Zkontrolujte vlastnosti výkonu virtuálních počítačů, které pracovat Storage úrovně Premium a vyberete nejvhodnější velikost virtuálního počítače, který nejlépe vyhovuje vaší zatížení. Ujistěte se, že je dostatečnou šířku pásma dostupné na vašem virtuálním počítači k řízení provozu disku.

### <a name="disk-sizes"></a>Velikost disků

**Premium spravované disky**

Existují tři typy Premium spravované disky, které lze použít s vaší virtuálních počítačů a každá z nich má konkrétní IOPs a propustnost omezení. Vezměte v úvahu tyto limity při výběru typu Premium disku pro virtuální počítač na základě potřeb vaší aplikace z hlediska kapacity, výkon, škálovatelnost a načte ve špičce.

| Disky typu Premium  | P10               | P20               | P30               |
|---------------------|-------------------|-------------------|-------------------|
| Velikost disku           | 128 GB            | 512 GB            | 1024 GB (1 TB)    |
| Vstupně-výstupní operace za sekundu / disk       | 500               | 2300              | 5000              |
| Propustnost / disk | 100 MB za sekundu | 150 MB za sekundu | 200 MB za sekundu |

**Standardní disky spravované**

Existuje pět typů standardní spravované disků, které lze použít s virtuálního počítače. Každý z nich mají různé kapacity ale mít stejné IOPS a omezení propustnosti. Vyberte typ standardní spravovaných disků na základě potřeb kapacitu vaší aplikace.

| Disk typu Standard  | S4               | S6               | S10              | S20              | S30              |
|---------------------|------------------|------------------|------------------|------------------|------------------|
| Velikost disku           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1024 GB (1 TB)   |
| Vstupně-výstupní operace za sekundu / disk       | 500              | 500              | 500              | 500              | 500              |
| Propustnost / disk | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu |

### <a name="disk-caching-policy"></a>Zásady ukládání do mezipaměti na disku 

**Premium spravované disky**

Ve výchozím nastavení, je disk ukládání do mezipaměti zásad *jen pro čtení* pro všechny Premium datových disků, a *pro čtení a zápis* pro disk operačního systému Premium připojen k virtuálnímu počítači. Toto nastavení konfigurace se doporučuje pro dosažení optimálního výkonu pro vaše aplikace IOs. Těžký zápisu nebo pouze pro zápis datové disky (například soubory protokolu serveru SQL Server) zakažte ukládání do mezipaměti disku, takže můžete dosáhnout lepší výkon aplikace.

### <a name="pricing"></a>Ceny

Zkontrolujte [ceny pro spravované disky](https://azure.microsoft.com/pricing/details/managed-disks/). Ceny disků spravované Premium je stejný jako nespravované prémiové disky. Ale ceny pro standardní disky spravované se liší od standardní nespravované disky.


## <a name="next-steps"></a>Další kroky

- Před nahráním jakéhokoli virtuálního pevného disku do Azure, postupujte podle [Příprava Windows VHD nebo VHDX, který chcete nahrát do Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
