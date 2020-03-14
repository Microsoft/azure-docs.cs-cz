---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/12/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 64aa01995460837c820c90010d7c4e3f3d78e6a2
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79300229"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>Regionální dostupnost
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>Omezení

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>Portál

Chcete-li vytvořit a nakonfigurovat přírůstkové snímky v globálním Azure Portal, je nutné použít následující odkaz: [https://aka.ms/incrementalsnapshots](https://aka.ms/incrementalsnapshots). Přírůstkové vytváření snímku ještě není v globálním Azure Portal k dispozici.

1. Přihlaste se k [Azure Portal](https://aka.ms/incrementalsnapshots) pomocí zadaného odkazu a přejděte na disk, který chcete vytvořit snímek.
1. Na disku vyberte **vytvořit snímek** .

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text=" ":::

1. Vyberte skupinu prostředků, kterou chcete použít, a zadejte název.
1. Vyberte **přírůstkové** a vyberte **zkontrolovat + vytvořit** .

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text=" ":::

1. Vyberte **Vytvořit**.

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text=" ":::

## <a name="next-steps"></a>Další kroky

Pokud se chcete podívat na vzorový kód prokazující rozdílovou schopnost přírůstkových snímků pomocí .NET, přečtěte si téma [kopírování záloh Azure Managed disks do jiné oblasti s rozdílovou schopností přírůstkových snímků](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
