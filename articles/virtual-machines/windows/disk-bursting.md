---
title: Shlukování spravovaných disků
description: Další informace o shlukování disků pro virtuální počítače Azure a jejich rozšíření na disk
author: albecker1
ms.author: albecker
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 25aa9fc166e831acd2ed0389bbbe4d2dc7e04b19
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "82594385"
---
# <a name="disk-bursting"></a>Zvládání výkonových špiček disků
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Shlukování na úrovni virtuálních počítačů
Podpora rozšíření na úrovni virtuálních počítačů je povolená ve všech oblastech ve veřejném cloudu v těchto podporovaných velikostech: 
- [Řada Lsv2](../lsv2-series.md)

Pro virtuální počítače, které ji podporují, je shluking povolen ve výchozím nastavení.

## <a name="disk-level-bursting"></a>Roztržení na úrovni disku
K dispozici je také SSD na úrovni [Premium](disks-types.md#premium-ssd) pro velikosti disků P20 a menší ve všech oblastech. Při nových nasazení velikosti disků, která ho podporují, je rozšíření disku ve výchozím nastavení povolené. Stávající velikosti disků, pokud podporují shlukování disku, mohou umožňovat roztržení pomocí jedné z následujících metod: 
- **Restartujte virtuální počítač.** 
- **Odpojení a opětovné připojení disku**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
