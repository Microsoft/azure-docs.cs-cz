---
title: Konfigurace předvolby směrování sítě
titleSuffix: Azure Storage
description: Nakonfigurujte předvolby směrování sítě pro váš účet úložiště Azure, abyste určili, jak se bude směrovat síťový provoz na váš účet od klientů přes Internet.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 02/21/2020
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: fb427de170764e5cd1fca57f9fb2d1410829e521
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700809"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Nakonfigurujte předvolby síťového směrování pro Azure Storage

Tento článek popisuje, jak můžete nakonfigurovat pro svůj účet úložiště předvolby směrování sítě a koncové body specifické pro směrování. 

Priorita síťového směrování určuje, jak se síťový provoz směruje na váš účet od klientů přes Internet. Koncové body specifické pro trasu jsou nové koncové body, které Azure Storage vytvoří pro váš účet úložiště. Tyto koncové body směrují provoz přes požadovanou cestu beze změny výchozí předvolby směrování. Další informace najdete v tématu [Předvolby síťového směrování pro Azure Storage](network-routing-preference.md).

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>Konfigurace předvolby směrování pro výchozí veřejný koncový bod

Ve výchozím nastavení je předvolby směrování pro veřejný koncový bod účtu úložiště nastavená na globální síť Microsoft. Jako výchozí předvolby směrování pro veřejný koncový bod vašeho účtu úložiště si můžete vybrat mezi globálním a internetovým směrováním od Microsoftu. Další informace o rozdílu mezi těmito dvěma typy směrování najdete v tématu [Předvolby síťového směrování pro Azure Storage](network-routing-preference.md). 

Změna předvolby směrování na Internet:

1. Na portálu přejděte na svůj účet úložiště.

2. V části **Nastavení** vyberte **sítě**.

    > [!div class="mx-imgBorder"]
    > ![Možnost nabídky síť](./media/configure-network-routing-preference/networking-option.png)

3.  Na kartě **brány firewall a virtuální sítě** v části **síťové směrování** změňte nastavení **Předvolby směrování** na **Internet směrování**.

4.  Klikněte na **Uložit**.

    > [!div class="mx-imgBorder"]
    > ![možnost směrování v Internetu](./media/configure-network-routing-preference/internet-routing-option.png)

## <a name="configure-a-route-specific-endpoint"></a>Konfigurace koncového bodu specifického pro trasu

Můžete také nakonfigurovat koncový bod specifický pro trasu. Můžete třeba nastavit předvolby směrování pro výchozí koncový bod na *Směrování v Internetu* a pak publikovat koncový bod specifický pro trasu, který umožňuje směrování provozu mezi klienty na internetu a vaším účtem úložiště prostřednictvím globální sítě Microsoftu.

1.  Na portálu přejděte na svůj účet úložiště.

2.  V části **Nastavení** vyberte **sítě**.

3.  Na kartě **brány firewall a virtuální sítě** v části **publikovat koncové body specifické pro trasu** zvolte Předvolby směrování pro koncový bod specifický pro trasu a potom klikněte na **Uložit**. Tato preference má vliv jenom na koncový bod konkrétní trasy. Tato předvolba nemá vliv na výchozí předvolby směrování.  

    Na následujícím obrázku vidíte výběr možnosti **Směrování sítě Microsoft** .

    > [!div class="mx-imgBorder"]
    > ![Možnost směrování sítě Microsoft](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>Najít název koncového bodu pro koncový bod specifický pro trasu

Pokud jste nakonfigurovali koncový bod specifický pro trasu, můžete koncový bod najít ve vlastnostech účtu úložiště.

1.  V části **Nastavení** vyberte **vlastnosti**.

    > [!div class="mx-imgBorder"]
    > ![možnost nabídky vlastnosti](./media/configure-network-routing-preference/properties.png)

2.  Pro každou službu, která podporuje předvolby směrování, se zobrazí koncový bod **Směrování sítě Microsoft** . Tento obrázek ukazuje koncový bod pro objekty BLOB a souborové služby.

    > [!div class="mx-imgBorder"]
    > ![Možnost směrování sítě Microsoft pro koncové body specifické pro trasu](./media/configure-network-routing-preference/routing-url.png)


## <a name="see-also"></a>Viz také

- [Priorita síťového směrování](network-routing-preference.md)
- [Konfigurace bran firewall Azure Storage a virtuálních sítí](storage-network-security.md)
- [Doporučení zabezpečení pro úložiště objektů BLOB](../blobs/security-recommendations.md)
