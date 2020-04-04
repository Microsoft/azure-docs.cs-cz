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
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80628430"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>Regionální dostupnost
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>Omezení

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>Portál


1. Přihlaste se k [portálu Azure](https://portal.azure.com/) a přejděte na disk, který chcete snímek.
1. Na disku vyberte **Vytvořit snímek.**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="Screenshot. Okno disku se zvýrazněným **+Create snapshot**, protože to je to, co musíte vybrat.":::

1. Vyberte skupinu prostředků, kterou chcete použít, a zadejte název.
1. Vyberte **Přírůstkové** a vyberte **Zkontrolovat + Vytvořit**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="Screenshot. Vytvořte snímek, vyplňte název a vyberte přírůstkové a vytvořte snímek.":::

1. Vybrat **vytvořit**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="Screenshot. Ověřovací stránka snímku, potvrďte výběry a vytvořte snímek.":::

## <a name="next-steps"></a>Další kroky

Pokud chcete zobrazit ukázkový kód demonstrující rozdílové schopnosti přírůstkových snímků pomocí rozhraní .NET, přečtěte si část [Kopírování záloh spravovaných disků Azure do jiné oblasti s rozdílovou schopností přírůstkových snímků](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
