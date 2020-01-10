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
ms.openlocfilehash: 7d7b9f847cdcc4ab4b1ff065425eebe07fb4d888
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775209"
---
1. Na stránce **vytvořit partnerský vztah** na kartě **Konfigurace** vyplňte pole, jak vidíte níže.

    > [!div class="mx-imgBorder"]
    > Konfigurace partnerského vztahu ![– Exchange](../media/setup-exchange-conf-tab.png)

    * Jako **typ partnerského vztahu**vyberte *Exchange*.
    * Vyberte **SKU** jako *základní zdarma*.
    * Vyberte umístění **Metro** , pro které chcete převést partnerský vztah na prostředek Azure. Pokud máte partnerské vztahy s Microsoftem ve vybraném umístění **Metro** , které se nepřevádějí do prostředku Azure, pak budou tato připojení uvedená v části **připojení partnerských vztahů** , jak je znázorněno níže. Nyní můžete tato připojení partnerských vztahů převést na prostředek Azure.

        > [!div class="mx-imgBorder"]
        > Konfigurace partnerského vztahu ![– připojení starší verze Exchange](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > Nemůžete změnit nastavení pro starší připojení partnerských vztahů. Pokud chcete přidat další připojení partnerského vztahu se společností Microsoft ve vybraném umístění služby **Metro** , klikněte na tlačítko **vytvořit nové** . Další informace najdete v tématu [Vytvoření nebo úprava partnerského vztahu Exchange pomocí portálu](../howto-exchange-portal.md) .
        >

1. Klikněte na **zkontrolovat + vytvořit**. Všimněte si, že na portálu běží základní ověření informací, které jste zadali. Zobrazuje se na pásu karet nahoře a *spouští se konečné ověření...* .

    > [!div class="mx-imgBorder"]
    > Karta ověření partnerského vztahu ![](../media/setup-direct-review-tab-validation.png)

1. Po *úspěšném ověření*ověřte informace a odešlete žádost kliknutím na **vytvořit**. Pokud potřebujete žádost upravit, klikněte na **Předchozí** a opakujte postup výše.

    > [!div class="mx-imgBorder"]
    > ![odesílání partnerských vztahů](../media/setup-exchange-review-tab-submit.png)

1. Po odeslání žádosti počkejte, než se dokončí nasazení. Pokud nasazení neproběhne úspěšně, kontaktujte [partnerský vztah Microsoftu](mailto:peering@microsoft.com). Úspěšné nasazení se zobrazí jako níže.

    > [!div class="mx-imgBorder"]
    > Úspěšnost partnerského vztahu ![](../media/setup-direct-success.png)
