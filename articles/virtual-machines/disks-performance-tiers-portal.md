---
title: Změna výkonu služby Azure Managed disks pomocí Azure Portal
description: Naučte se, jak změnit úrovně výkonu pro nové a existující spravované disky pomocí Azure Portal.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 01/05/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 625fb1e3dd0b433da6b60f995aa6b380c23ec9ce
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901013"
---
# <a name="change-your-performance-tier-using-the-azure-portal"></a>Změňte úroveň výkonu pomocí Azure Portal

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>Omezení

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="getting-started"></a>Začínáme

### <a name="new-disks"></a>Nové disky

Následující kroky ukazují, jak změnit úroveň výkonu disku při prvním vytvoření disku:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. Přejděte k virtuálnímu počítači, pro který chcete vytvořit nový disk.
1. Při výběru nového disku nejdřív zvolte velikost disku, který potřebujete.
1. Po výběru velikosti vyberte jinou úroveň výkonu, abyste mohli změnit její výkon.
1. Vyberte **OK** a vytvořte disk.

:::image type="content" source="media/disks-performance-tiers-portal/new-disk-change-performance-tier.png" alt-text="Snímek obrazovky okna pro vytvoření disku, disk je zvýrazněn a zvýrazní se rozevírací nabídka úroveň výkonu." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::


## <a name="existing-disks"></a>Existující disky

Následující kroky popisují, jak změnit úroveň výkonu existujícího disku:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. Přejděte k virtuálnímu počítači obsahujícímu disk, který chcete změnit.
1. Buď zrušte přidělení virtuálního počítače, nebo odpojte disk.
1. Výběr disku
1. Vyberte **Velikost + výkon**.
1. V rozevíracím seznamu **úroveň výkonu** vyberte jinou úroveň než aktuální úroveň výkonu disku.
1. Vyberte **Změnit velikost**.

:::image type="content" source="media/disks-performance-tiers-portal/change-tier-existing-disk.png" alt-text="Snímek obrazovky okna velikost + výkon, úroveň výkonu je zvýrazněný." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::

## <a name="next-steps"></a>Další kroky

Pokud potřebujete změnit velikost disku, abyste mohli využít vyšší úrovně výkonu, přečtěte si tyto články:

- [Rozbalení virtuálních pevných disků na virtuálním počítači se systémem Linux pomocí Azure CLI](linux/expand-disks.md)
- [Rozšíření spravovaného disku připojeného k virtuálnímu počítači s Windows](windows/expand-os-disk.md)
