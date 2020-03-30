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
ms.openlocfilehash: 8f2e2081ee31e2ce7b21677bd261ed7eda705334
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79486044"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>Regionální dostupnost
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>Omezení

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>Portál

Chcete-li vytvořit a nakonfigurovat přírůstkové snímky na globálním [https://aka.ms/incrementalsnapshots](https://aka.ms/incrementalsnapshots)portálu Azure, musíte použít následující odkaz: . Přírůstkové vytváření snímků ještě není k dispozici na globálním portálu Azure.

1. Přihlaste se k [portálu Azure](https://aka.ms/incrementalsnapshots) pomocí poskytnutého odkazu a přejděte na disk, který chcete snímek.
1. Na disku vyberte **Vytvořit snímek.**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="Screenshot. Okno disku se zvýrazněným **+Create snapshot**, protože to je to, co musíte vybrat.":::

1. Vyberte skupinu prostředků, kterou chcete použít, a zadejte název.
1. Vyberte **Přírůstkové** a vyberte **Zkontrolovat + Vytvořit**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="Screenshot. Vytvořte snímek, vyplňte název a vyberte přírůstkové a vytvořte snímek.":::

1. Vybrat **vytvořit**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="Screenshot. Ověřovací stránka snímku, potvrďte výběry a vytvořte snímek.":::

## <a name="next-steps"></a>Další kroky

Pokud chcete zobrazit ukázkový kód demonstrující rozdílové schopnosti přírůstkových snímků pomocí rozhraní .NET, přečtěte si část [Kopírování záloh spravovaných disků Azure do jiné oblasti s rozdílovou schopností přírůstkových snímků](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
