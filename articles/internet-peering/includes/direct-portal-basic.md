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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774637"
---
1. Klikněte na **vytvořit prostředek** > **Zobrazit vše**.

    > [!div class="mx-imgBorder"]
    > ![hledání partnerských vztahů](../media/setup-seeall.png)

1. Do vyhledávacího pole vyhledejte *partnerský vztah* a stiskněte *ENTER* na klávesnici. Z výsledků klikněte na prostředek **partnerského vztahu** .

    > [!div class="mx-imgBorder"]
    > ![spuštění partnerského vztahu](../media/setup-launch.png)

1. Po spuštění **partnerského vztahu** si Projděte stránku, abyste pochopili podrobnosti. Až budete připraveni, klikněte na **vytvořit**.

    > [!div class="mx-imgBorder"]
    > ![vytvoření partnerského vztahu](../media/setup-create.png)

1. Na stránce **vytvořit partnerský vztah** na kartě **základy** vyplňte pole, jak vidíte níže.

    > [!div class="mx-imgBorder"]
    > Základy partnerského vztahu ![](../media/setup-basics-tab.png)

    * Vyberte své **předplatné**Azure.
    * V případě **skupiny prostředků**můžete buď zvolit existující skupinu prostředků z rozevíracího seznamu, nebo vytvořit novou skupinu kliknutím na **vytvořit novou**. V tomto příkladu vytvoříme novou skupinu prostředků.
    * **Název** odpovídá názvu prostředku a může to být cokoli, co si zvolíte.
    * Pokud jste v předchozím kroku zvolili existující skupinu prostředků, automaticky se vybere **oblast** . Pokud se rozhodnete vytvořit novou skupinu prostředků, musíte taky zvolit oblast Azure, ve které chcete prostředek umístit. USA – východ

        > [!NOTE]
        > Oblast, kde se nachází skupina prostředků, je nezávislá na umístění, kde chcete vytvořit partnerský vztah s Microsoftem. Je ale osvědčeným postupem organizovat prostředky partnerského vztahu v rámci skupin prostředků, které jsou umístěné v nejbližších oblastech Azure. Např. pro partnerské vztahy v Ashburn můžete vytvořit skupinu prostředků v *východní USA* nebo *východní USA 2*

    * Vyberte číslo ASN v poli **partnerské číslo ASN** .

        > [!IMPORTANT]
        > * Před odesláním žádosti o partnerský vztah můžete zvolit jenom číslo ASN s ValidationState jako schválenou. Pokud jste právě odeslali žádost PeerAsn, počkejte 12 hodin, nebo tak, aby bylo přidružení ASN "schválené". Pokud vybrané číslo ASN čeká na ověření, zobrazí se chybová zpráva. 
        > * Pokud nevidíte ASN, které potřebujete vybrat, zkontrolujte, jestli jste vybrali správné předplatné. Pokud ano, ověřte, jestli jste už vytvořili PeerAsn pomocí [přidružení partnerského čísla ASN k předplatnému Azure](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > Základ ![ho partnerského vztahu vyplněný](../media/setup-direct-basics-filled-tab.png)

    * Pokračujte kliknutím na tlačítko **Další: > konfigurace** .
