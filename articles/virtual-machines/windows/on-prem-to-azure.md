---
title: Migrace z AWS a dalších platforem na spravované disky v Azure
description: Vytvářejte virtuální počítače v Azure pomocí virtuálních pevných disků nahraných z jiných cloudů, jako je AWS nebo jiné virtualizační platformy, a využijte spravované disky Azure.
author: roygara
manager: twooley
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dc283d1845926b79fb541d8ccb011fe853f50484
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870282"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Migrace z Amazon Web Services (AWS) a dalších platforem na spravované disky v Azure

Soubory Virtuálního pevného disku můžete nahrát z AWS nebo místních virtualizačních řešení do Azure a vytvářet virtuální počítače, které využívají spravované disky. Spravované disky Azure odebere potřebu spravovat účty úložiště pro virtuální počítače Azure IaaS. Musíte zadat pouze typ (Premium nebo Standard) a velikost disku, který potřebujete, a Azure vytvoří a spravuje disk za vás. 

Můžete nahrát buď generalizované a specializované VD. 
- **Generalizované VHD** - byly odstraněny všechny informace o vašem osobním účtu pomocí sysprepu. 
- **Specializované Virtuální pevné rozlišení** – udržuje uživatelské účty, aplikace a další stavová data z původního virtuálního počítače. 

> [!IMPORTANT]
> Než nahrajete jakýkoli virtuální pevný disk do Azure, měli byste postupovat podle [možnosti Příprava virtuálního pevného disku nebo virtuálního pevného disku VHDX pro nahrání do Azure.](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>


| Scénář                                                                                                                         | Dokumentace                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Máte existující instance AWS EC2, které chcete migrovat na virtuální počítače Azure pomocí spravovaných disků                              | [Přesunutí virtuálního počítače z Amazon Web Services (AWS) do Azure](aws-to-azure.md)                           |
| Máte virtuální počítač z jiné virtualizační platformy, kterou chcete použít jako image k vytvoření více virtuálních počítačů Azure. | [Nahrání generalizovaného virtuálního pevného disku a jeho použití k vytvoření nového virtuálního počítače v Azure](upload-generalized-managed.md) |
| Máte jedinečně přizpůsobený virtuální počítač, který chcete znovu vytvořit v Azure.                                                      | [Nahrání specializovaného virtuálního pevného disku do Azure a vytvoření nového virtuálního počítače](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Přehled spravovaných disků

Spravované disky Azure zjednodušují správu virtuálních počítačů odebráním nutnosti spravovat účty úložiště. Spravované disky také těžit z lepší spolehlivostvirtuálních počítačů v sadě dostupnosti. Zajišťuje, že disky různých virtuálních počítačů v sadě dostupnosti jsou dostatečně izolované od sebe navzájem, aby se zabránilo jeden bod selhání. Automaticky umísťuje disky různých virtuálních počítačů do sady dostupnosti v různých jednotkách měřítka úložiště (razítkách), což omezuje dopad selhání jedné jednotky škálování úložiště způsobené selháním hardwaru a softwaru.
Na základě vašich potřeb si můžete vybrat ze čtyř typů možností úložiště. Další informace o dostupných typech disků naleznete v článku [Vyberte typ disku](disks-types.md).

## <a name="plan-for-the-migration-to-managed-disks"></a>Plánování migrace na spravované disky

Tato část vám pomůže učinit nejlepší rozhodnutí o virtuálních počítači a typy disků.

Pokud plánujete migraci z nespravovaných disků na spravované disky, měli byste si být vědomi toho, že uživatelé s rolí [Přispěvatel virtuálního počítače](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) nebudou moci změnit velikost virtuálního počítače (jak by mohli předpřevodem). Důvodem je, že virtuální počítače se spravovanými disky vyžadují, aby uživatel měl oprávnění Microsoft.Compute/disks/write na discích operačního systému.

### <a name="location"></a>Umístění

Vyberte umístění, kde jsou k dispozici spravované disky Azure. Pokud migrujete na spravované disky Premium, ujistěte se také, že úložiště Premium je k dispozici v oblasti, do které plánujete migrovat. Aktuální informace o dostupných umístěních najdete [v tématu Služby Azure podle oblastí.](https://azure.microsoft.com/regions/#services)

### <a name="vm-sizes"></a>Velikost virtuálních počítačů

Pokud migrujete na spravované disky premium, budete muset aktualizovat velikost virtuálního počítače na velikost úložiště Premium storage, která je k dispozici v oblasti, kde se virtuální počítače nachází. Zkontrolujte velikosti virtuálních zařízení, které jsou schopné úložiště Premium. Specifikace velikosti virtuálního počítače Azure jsou uvedeny v [části Velikosti pro virtuální počítače](sizes.md).
Zkontrolujte charakteristiky výkonu virtuálních počítačů, které fungují s úložištěm Premium, a zvolte nejvhodnější velikost virtuálních počítačů, která nejlépe vyhovuje vaší pracovní zátěži. Ujistěte se, že je k dispozici dostatečná šířka pásma na vašem virtuálním počítači řídit provoz na disku.

### <a name="disk-sizes"></a>Velikosti disků

**Prémiové spravované disky**

Existuje sedm typů spravovaných disků s prémií, které lze použít s virtuálním počítačem a každý má konkrétní iOP a omezení propustnost. Vezměte v úvahu tato omezení při výběru typu disku Premium pro váš virtuální počítač na základě potřeb vaší aplikace z hlediska kapacity, výkonu, škálovatelnosti a zatížení ve špičce.

| Typ prémiových disků  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Velikost disku           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| Vstupně-výstupní operace za sekundu / disk       | 120   | 240   | 500   | 1100  |2300              | 5000              | 7 500              | 7 500              | 
| Propustnost / disk | 25 MB za sekundu  | 50 MB za sekundu  | 100 MB za sekundu | 125 MB za sekundu |150 MB za sekundu | 200 MB za sekundu | 250 MB za sekundu | 250 MB za sekundu |

**Standardní spravované disky**

Existuje sedm typů standardních spravovaných disků, které lze použít s virtuálním počítačem. Každý z nich má jinou kapacitu, ale mají stejné vigb a omezení propustnosti. Zvolte typ disků Se standardní spravovanou na základě potřeb kapacity vaší aplikace.

| Disk typu Standard  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Velikost disku           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1024 GB (1 TB)   | 2048 GB (2 TB)    | 4095 GB (4 TB)   | 
| Vstupně-výstupní operace za sekundu / disk       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Propustnost / disk | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu |60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 

### <a name="disk-caching-policy"></a>Zásady ukládání disku do mezipaměti 

**Prémiové spravované disky**

Ve výchozím nastavení je zásada ukládání disku do mezipaměti *jen pro čtení* pro všechny datové disky Premium a pro čtení a *zápis* pro disk operačního systému Premium připojený k virtuálnímu počítače. Toto nastavení konfigurace se doporučuje k dosažení optimálního výkonu pro vos vaší aplikace. Pro datové disky náročné na zápis nebo jen pro zápis (například soubory protokolu serveru SQL Server) zakažte ukládání disků do mezipaměti, abyste mohli dosáhnout lepšího výkonu aplikací.

### <a name="pricing"></a>Ceny

Zkontrolujte [ceny pro spravované disky](https://azure.microsoft.com/pricing/details/managed-disks/). Ceny prémiových spravovaných disků jsou stejné jako u disků Premium Unmanaged Disks. Ceny za standardní spravované disky se však liší od standardních nespravovaných disků.


## <a name="next-steps"></a>Další kroky

- Než nahrajete jakýkoli virtuální pevný disk do Azure, měli byste postupovat podle [možnosti Příprava virtuálního pevného disku nebo virtuálního pevného disku VHDX pro nahrání do Azure.](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
