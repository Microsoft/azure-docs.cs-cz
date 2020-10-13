---
title: Shlukování spravovaných disků
description: Další informace o shlukování disků pro virtuální počítače Azure a jejich rozšíření na disk
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 547834542b605b226ebffd68e05296ee847dc6de
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974324"
---
# <a name="disk-bursting"></a>Zvládání výkonových špiček disků
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Shlukování na úrovni virtuálních počítačů
Podpora rozšíření na úrovni virtuálních počítačů je povolená ve všech oblastech ve veřejném cloudu v těchto podporovaných velikostech: 
- [Řada Lsv2](../lsv2-series.md)

V Středozápadní USA je k dispozici také rozšíření na úrovni virtuálního počítače pro následující podporované velikosti:
- [Dsv3-series](../dv3-dsv3-series.md)
- [Esv3-Series](../ev3-esv3-series.md)

Pro virtuální počítače, které ji podporují, je shluking povolen ve výchozím nastavení.

## <a name="disk-level-bursting"></a>Roztržení na úrovni disku
K dispozici je také SSD na úrovni [Premium](../disks-types.md#premium-ssd) pro velikosti disků P20 a menší ve všech oblastech. Při nových nasazení velikosti disků, která ho podporují, je rozšíření disku ve výchozím nastavení povolené. Stávající velikosti disků, pokud podporují shlukování disku, mohou umožňovat roztržení pomocí jedné z následujících metod: 
- **Restartování virtuálního počítače** 
- **Odpojení a opětovné připojení disku**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]