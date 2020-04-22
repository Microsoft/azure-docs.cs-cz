---
title: zahrnout soubor
titleSuffix: Azure
description: zahrnout soubor
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 00c24212706555667ad4680c086ece24f15ca59a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678589"
---
1. Vyberte **Vytvořit prostředek** > **Zobrazit vše**.

    > [!div class="mx-imgBorder"]
    > ![Hledat partnerský vztah](../media/setup-seeall.png)

1. Ve vyhledávacím poli **vyhledejte peering** a na klávesnici vyberte **Enter.** Z výsledků vyberte zdroj **partnerského vztahu.**

    > [!div class="mx-imgBorder"]
    > ![Spuštění partnerského vztahu](../media/setup-launch.png)

1. Po spuštění **peeringu** zkontrolujte stránku, abyste pochopili podrobnosti. Až budete připraveni, vyberte **Vytvořit**.

    > [!div class="mx-imgBorder"]
    > ![Vytvořit partnerský vztah](../media/setup-create.png)

1. Na stránce **Vytvořit partnerský vztah vyplňte** na kartě **Základy** pole, jak je znázorněno tady.

    > [!div class="mx-imgBorder"]
    > ![Karta Základy partnerského vztahu](../media/setup-basics-tab.png)

    * Vyberte **předplatné**Azure .
    * Ve **skupině Prostředků**můžete buď vybrat existující skupinu prostředků z rozevíracího seznamu, nebo vytvořit novou skupinu výběrem **možnosti Vytvořit nový**. Vytvoříme novou skupinu prostředků pro tento příklad.
    * **Název** odpovídá názvu prostředku a může být cokoli, co si vyberete.
    * **Oblast** je automaticky vybraná, pokud jste zvolili existující skupinu prostředků. Pokud jste se rozhodli vytvořit novou skupinu prostředků, musíte také zvolit oblast Azure, kde se má prostředek naskládat.

        > [!NOTE]
        > Oblast, ve které se nachází skupina prostředků, je nezávislá na umístění, kde chcete vytvořit partnerský vztah se společností Microsoft. Ale je to osvědčený postup pro uspořádání prostředků partnerského vztahu v rámci skupin prostředků, které jsou umístěny v nejbližších oblastech Azure. Například pro vzájemné vyrovnání v Ashburnu můžete vytvořit skupinu prostředků v USA – východ nebo východ USA2.

    * V poli Peer ASN vyberte svůj **ASN.**

        > [!IMPORTANT]
        > * Před odesláním žádosti o partnerský vztah můžete zvolit pouze asn s validačním státem jako schválený. Pokud jste právě odeslali žádost PeerAsn, počkejte asi 12 hodin, než bude přidružení ASN schváleno. Pokud asn, které vyberete, čeká na ověření, zobrazí se chybová zpráva. 
        > * Pokud nevidíte ASN, které potřebujete zvolit, zkontrolujte, že jste vybrali správné předplatné. Pokud ano, zkontrolujte, jestli jste už vytvořili PeerAsn pomocí [přidruženého partnera ASN k předplatnému Azure](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > ![Vyplněné základy partnerského vztahu](../media/setup-direct-basics-filled-tab.png)

    * Chcete-li pokračovat, vyberte **možnost Další : Konfigurace >.**
