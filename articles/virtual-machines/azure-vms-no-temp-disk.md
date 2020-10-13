---
title: Nejčastější dotazy týkající se velikosti virtuálních počítačů Azure bez místního dočasného disku
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Microsoft Azure velikostí virtuálních počítačů, které nemají místní dočasný disk.
author: brbell
ms.service: virtual-machines
ms.topic: conceptual
ms.author: brbell
ms.reviewer: mimckitt
ms.date: 06/15/2020
ms.openlocfilehash: 30587fac7d7be37d7595a78502b7999adee9a30f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665306"
---
# <a name="azure-vm-sizes-with-no-local-temporary-disk"></a>Velikosti virtuálních počítačů Azure bez místního dočasného disku 
Tento článek obsahuje odpovědi na nejčastější dotazy týkající se velikostí virtuálních počítačů Azure, které nemají místní dočasný disk (tj. žádný místní dočasný disk). Další informace o těchto velikostech virtuálních počítačů najdete v tématu [specifikace pro dv4 a Dsv4-Series (pro obecné účely úlohy)](dv4-dsv4-series.md) nebo [specifikace pro Ev4 a Esv4-Series (paměťově optimalizované úlohy)](ev4-esv4-series.md).

## <a name="what-does-no-local-temp-disk-mean"></a>Co neznamená žádný místní dočasný disk? 
Tradičně jsme měli velikost virtuálních počítačů (např. Standard_D2s_v3, Standard_E48_v3), které zahrnují malý místní disk (tj. a D: Drive). Teď s těmito novými velikostmi virtuálních počítačů už neexistuje tento malý místní disk. Můžete však stále připojit HDD úrovně Standard, SSD úrovně Premium nebo SSD úrovně Ultra.

## <a name="what-if-i-still-want-a-local-temp-disk"></a>Co když stále chci místní dočasný disk?
Pokud vaše úloha vyžaduje místní dočasný disk, máme také k dispozici nové velikosti virtuálních počítačů [s Ddv4 a Ddsv4](ddv4-ddsv4-series.md) nebo [Edv4 a Edsv4](edv4-edsv4-series.md) . Tyto velikosti nabízejí 50% větší dočasný disk v porovnání s předchozími velikostmi v3.

> [!NOTE]
> Místní dočasný disk není trvalý. Pokud chcete zajistit, aby vaše data byla trvalá, použijte prosím HDD úrovně Standard, SSD úrovně Premium nebo SSD úrovně Ultra možnosti. 

## <a name="what-are-the-differences-between-these-new-vm-sizes-and-the-general-purpose-dv3dsv3-or-the-memory-optimized-ev3esv3-vm-sizes-that-i-am-used-to"></a>Jaké jsou rozdíly mezi těmito novými velikostmi virtuálních počítačů a Pro obecné účely Dv3/Dsv3 nebo paměťově optimalizovanou velikostí virtuálních počítačů Ev3/Esv3, které jsem Používám? 
| rodiny virtuálních počítačů v3 s místním dočasným diskem   | Nové rodiny V4 s místním dočasným diskem | Nové rodiny v4 bez místního dočasného disku |
|---|---|---|
| Dv3   | Ddv4 | Dv4 |
| Dsv3 | Ddsv4  | Dsv4 |
| Ev3   | Edv4  | Ev4 |
| Esv3 | Edsv4 |    Esv4 | 

## <a name="can-i-resize-a-vm-size-that-has-a-local-temp-disk-to-a-vm-size-with-no-local-temp-disk"></a>Můžu změnit velikost virtuálního počítače, která má místní dočasný disk na velikost virtuálního počítače bez místního dočasného disku?  
Ne. Pro změnu velikosti jsou povolené jenom tyto kombinace: 

1. Virtuální počítač (s místním dočasným diskem) – > virtuální počítač (s místním dočasným diskem); ani 
2. Virtuální počítač (bez místního dočasného disku) – > virtuální počítač (bez místního dočasného disku). 

> [!NOTE]
> Pokud bitová kopie závisí na disku prostředků nebo na místním dočasném disku existuje stránkovací soubor nebo swapfile, image bez disků nebudou fungovat – místo toho použijte alternativu "s diskem". 

## <a name="do-these-vm-sizes-support-both-linux-and-windows-operating-systems-os"></a>Podporují tyto velikosti virtuálních počítačů operační systémy Linux i Windows (OS)?
Ano.

## <a name="will-this-break-my-custom-scripts-custom-images-or-os-images-that-have-scratch-files-or-page-files-on-a-local-temp-disk"></a>Budou se tato přerušení vlastní skripty, vlastní image nebo image operačních systémů, které mají soubory nebo soubory stránky na místním dočasném disku?
Pokud vlastní image operačního systému odkazuje na místní dočasný disk, bitová kopie nemusí pracovat správně s touto bezdiskovou velikostí.

## <a name="have-questions-or-feedback"></a>Máte otázky nebo připomínky?
Vyplňte [formulář zpětné vazby]( https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRUMzdCQkw0OVVRTldFUUtXSTlLQVBPUkVHSy4u). 

## <a name="next-steps"></a>Další kroky 
V tomto dokumentu jste se dozvěděli víc o nejčastějších dotazech souvisejících s virtuálními počítači Azure bez místního dočasného disku. Další informace o těchto velikostech virtuálních počítačů najdete v následujících článcích:

- [Specifikace pro dv4 a Dsv4-Series (Pro obecné účely úlohy)](dv4-dsv4-series.md)
- [Specifikace pro Ev4 a Esv4-Series (paměťově optimalizovaná zatížení)](ev4-esv4-series.md)
