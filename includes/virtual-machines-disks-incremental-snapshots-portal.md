---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: de0cf260207747f4acb02a377819a13de8b8ba22
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002510"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>Regionální dostupnost
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>Omezení

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>Portál


1. Přihlaste se k [Azure Portal](https://portal.azure.com/) a přejděte na disk, na který chcete snímek.
1. Na disku vyberte **vytvořit snímek** .

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="–. Okno disku s * * + vytvořit snímek * * se zvýrazní, protože to je potřeba vybrat.":::

1. Vyberte skupinu prostředků, kterou chcete použít, a zadejte název.
1. Vyberte **přírůstkové** a vyberte **zkontrolovat + vytvořit** .

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="–. Vytvořte okno snímku, zadejte název a vyberte přírůstkové a vytvořte snímek.":::

1. Vyberte **Vytvořit**.

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="–. Ověřte stránku pro svůj snímek, potvrďte výběr a pak vytvořte snímek.":::

## <a name="next-steps"></a>Další kroky

Pokud se chcete podívat na vzorový kód prokazující rozdílovou schopnost přírůstkových snímků pomocí .NET, přečtěte si téma [kopírování záloh Azure Managed disks do jiné oblasti s rozdílovou schopností přírůstkových snímků](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
