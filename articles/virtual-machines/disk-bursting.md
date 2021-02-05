---
title: Shlukování spravovaných disků
description: Přečtěte si informace o shlukování disku pro disky Azure a virtuální počítače Azure.
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: dcdbf94e547581cb9ff885ac5896467abdf316ae
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576189"
---
# <a name="managed-disk-bursting"></a>Shlukování spravovaných disků
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Shlukování na úrovni virtuálních počítačů
Na následujících řadách virtuálních počítačů ve všech oblastech, ve kterých jsou podporované, se povolí rozpínání na úrovni virtuálních počítačů:
- [Řada Lsv2](lsv2-series.md)
- [Dsv3-series](dv3-dsv3-series.md)
- [Esv3-Series](ev3-esv3-series.md)

Pro virtuální počítače, které ji podporují, je shluking povolen ve výchozím nastavení.

## <a name="disk-level-bursting"></a>Roztržení na úrovni disku
K dispozici je taky naše [Premium SSD](disks-types.md#premium-ssd) pro velikosti disků P20 a menší ve všech oblastech v cloudech veřejných, vládních a čínských Azure. Pro všechna nová a stávající nasazení velikostí disků, která to podporují, je rozšíření disku ve výchozím nastavení povolené. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]
