---
title: zahrnout soubor
titleSuffix: Azure
description: zahrnout soubor
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 5/22/2020
ms.author: derekol
ms.openlocfilehash: e2804c8f9b1af89ac0ea86ec14136df66d900060
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "83846118"
---
1. Vyberte **vytvořit prostředek**  >  **Zobrazit vše**.

    > [!div class="mx-imgBorder"]
    > ![Hledat partnerský vztah](../media/setup-seeall.png)

1. Ve vyhledávacím poli vyhledejte **partnerský vztah** a na své klávesnici vyberte **ENTER** . Z výsledků vyberte prostředek **partnerského vztahu** .

    > [!div class="mx-imgBorder"]
    > ![Spustit partnerský vztah](../media/setup-launch.png)

1. Po spuštění **partnerského vztahu** si Projděte stránku, abyste porozuměli podrobnostem. Až budete připraveni, vyberte **vytvořit**.

    > [!div class="mx-imgBorder"]
    > ![Vytvořit partnerský vztah](../media/setup-create.png)

1. Na stránce **vytvořit partnerský vztah** na kartě **základy** vyplňte pole, jak je znázorněno zde.

    > [!div class="mx-imgBorder"]
    > ![Karta základy partnerského vztahu](../media/setup-basics-tab.png)

    * Vyberte své **předplatné** Azure.
    * V případě **skupiny prostředků** můžete buď zvolit existující skupinu prostředků z rozevíracího seznamu, nebo vytvořit novou skupinu výběrem možnosti **vytvořit nový**. V tomto příkladu vytvoříme novou skupinu prostředků.
    * **Název** odpovídá názvu prostředku a může to být cokoli, co si zvolíte.
    * Pokud jste zvolili existující skupinu prostředků, je vybrána možnost vybrat **oblast** . Pokud se rozhodnete vytvořit novou skupinu prostředků, musíte taky zvolit oblast Azure, ve které chcete prostředek umístit.

        > [!NOTE]
        > Oblast, ve které se nachází skupina prostředků, je nezávislá na umístění, kde chcete vytvořit partnerský vztah s Microsoftem. Je ale osvědčeným postupem uspořádání prostředků partnerského vztahu do skupin prostředků, které se nacházejí v nejbližších oblastech Azure. Například pro partnerské vztahy v Ashburn můžete vytvořit skupinu prostředků v Východní USA nebo východní USA 2.

    * V poli **partnerské číslo ASN** vyberte své ASN.

        > [!IMPORTANT]
        > * Před odesláním žádosti o vytvoření partnerského vztahu můžete zvolit jenom číslo ASN s ValidationState jako schválenou. Pokud jste právě odeslali žádost PeerAsn, počkejte 12 hodin, nebo tak, aby bylo možné schválit přidružení ASN. Pokud vybrané číslo ASN čeká na ověření, zobrazí se chybová zpráva. 
        > * Pokud nevidíte ASN, které potřebujete vybrat, zkontrolujte, jestli jste vybrali správné předplatné. Pokud ano, ověřte, jestli jste už vytvořili PeerAsn pomocí [přidružení partnerského čísla ASN k předplatnému Azure](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > ![Vyplněné základy partnerských vztahů](../media/setup-direct-basics-filled-tab.png)

    * Chcete-li pokračovat, vyberte možnost **Další: >konfigurace** .
