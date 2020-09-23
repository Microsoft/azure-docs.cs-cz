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
ms.openlocfilehash: 107b1cbde35ef639883f86153859679a834735a3
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90891845"
---
# <a name="managed-disk-bursting"></a>Shlukování spravovaných disků
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Shlukování na úrovni virtuálních počítačů
Podpora rozšíření na úrovni virtuálních počítačů je povolená ve všech oblastech ve veřejném cloudu v těchto podporovaných velikostech: 
- [Řada Lsv2](../lsv2-series.md)

V Středozápadní USA je k dispozici také rozšíření na úrovni virtuálního počítače pro následující podporované velikosti:
- [Dsv3-series](../dv3-dsv3-series.md)
- [Esv3-Series](../ev3-esv3-series.md)

Pro virtuální počítače, které ji podporují, je shluking povolen ve výchozím nastavení.

## <a name="disk-level-bursting"></a>Roztržení na úrovni disku
K dispozici je taky naše [Premium SSD](disks-types.md#premium-ssd) pro velikosti disků P20 a menší ve všech oblastech v cloudech veřejných, vládních a čínských Azure. Pro všechna nová a stávající nasazení velikostí disků, která to podporují, je rozšíření disku ve výchozím nastavení povolené. 

[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
