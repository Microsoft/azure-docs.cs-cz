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
ms.openlocfilehash: 79cf4b2edd1a25df427cf15f9ee7f2f331ebd2df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774637"
---
1. Klikněte **na Vytvořit zdroj** > **Zobrazit vše**.

    > [!div class="mx-imgBorder"]
    > ![Hledat partnerský vztah](../media/setup-seeall.png)

1. Ve vyhledávacím poli *vyhledejte peering* a na klávesnici stiskněte *Enter.* Ve výsledcích klikněte na zdroj **partnerského vztahu.**

    > [!div class="mx-imgBorder"]
    > ![Spuštění partnerského vztahu](../media/setup-launch.png)

1. Po spuštění **peeringu** zkontrolujte stránku, abyste pochopili podrobnosti. Až budete připraveni, klikněte na **Vytvořit**.

    > [!div class="mx-imgBorder"]
    > ![Vytvořit partnerský vztah](../media/setup-create.png)

1. Na stránce **Vytvořit partnerský vztah** v části **Základy** vyplňte pole, jak je znázorněno níže.

    > [!div class="mx-imgBorder"]
    > ![Základy partnerského vztahu](../media/setup-basics-tab.png)

    * Zvolte **předplatné**Azure .
    * Ve **skupině Prostředků**můžete buď vybrat existující skupinu prostředků z rozevíracího přehledu, nebo vytvořit novou skupinu klepnutím na tlačítko Vytvořit **nový**. Vytvoříme novou skupinu prostředků pro tento příklad.
    * **Název** odpovídá názvu prostředku a může být cokoli, co si vyberete.
    * **Oblast** je automaticky vybraná, pokud jste ve výše uvedeném kroku zvolili existující skupinu prostředků. Pokud jste se rozhodli vytvořit novou skupinu prostředků, musíte také zvolit oblast Azure, kde se má prostředek naskládat. USA – východ

        > [!NOTE]
        > Oblast, kde se skupina prostředků nachází, je nezávislá na umístění, kde chcete vytvořit partnerský vztah se společností Microsoft. Ale je osvědčeným postupem uspořádat prostředky partnerského vztahu v rámci skupin prostředků, které jsou umístěny v nejbližších oblastech Azure. Např.: pro peerings v Ashburn, můžete vytvořit skupinu zdrojů ve *východní USA* nebo *východ US2*

    * V poli Peer **ASN** zvolte svůj asn.

        > [!IMPORTANT]
        > * Před odesláním požadavku partnerského vztahu můžete vybrat pouze asn s ValidationState jako "Schváleno". Pokud jste právě odeslali žádost PeerAsn, počkejte asi 12 hodin, než bude přidružení ASN "Schváleno". Pokud asn, které vyberete, čeká na ověření, zobrazí se chybová zpráva. 
        > * Pokud nevidíte ASN, které musíte zvolit, zkontrolujte, zda jste vybrali správné předplatné. Pokud ano, zkontrolujte, jestli jste už vytvořili PeerAsn pomocí [přidruženého partnera ASN k předplatnému Azure](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > ![Základy partnerského vztahu vyplněny](../media/setup-direct-basics-filled-tab.png)

    * Chcete-li pokračovat, klepněte na **tlačítko Další : Konfigurace >.**
