---
title: Shlukování spravovaných disků
description: Další informace o shlukování disků pro virtuální počítače Azure a jejich rozšíření na disk
author: albecker1
ms.author: albecker
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: f92fae38d49c51dfe87a68b023ba779e89b0e0bc
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/02/2020
ms.locfileid: "84295452"
---
# <a name="disk-bursting"></a>Zvládání výkonových špiček disků
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Shlukování na úrovni virtuálních počítačů
Podpora rozšíření na úrovni virtuálních počítačů je povolená ve všech oblastech ve veřejném cloudu v těchto podporovaných velikostech: 
- [Řada Lsv2](../lsv2-series.md)

Pro virtuální počítače, které ji podporují, je shluking povolen ve výchozím nastavení.

## <a name="disk-level-bursting"></a>Roztržení na úrovni disku
K dispozici je taky naše [Premium SSD](disks-types.md#premium-ssd) pro velikosti disků P20 a menší ve všech oblastech v cloudech veřejných, vládních a čínských Azure. Pro všechna nová a stávající nasazení velikostí disků, která to podporují, je rozšíření disku ve výchozím nastavení povolené. 

[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
