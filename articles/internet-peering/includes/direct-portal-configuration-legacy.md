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
ms.openlocfilehash: 16909e0a5d59b0ae019d91aad00e0168b0c5f433
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775066"
---
1. Na stránce **vytvořit partnerský vztah** na kartě **Konfigurace** vyplňte pole, jak vidíte níže.

    > [!div class="mx-imgBorder"]
    > Konfigurace partnerského vztahu ![– přímé](../media/setup-direct-conf-tab.png)

    * V případě **typu partnerského vztahu**vyberte možnost *Direct*.
    * V případě **sítě Microsoft**vyberte možnost *AS8075*. Nevybírejte číslo ASN 8069. Je vyhrazený pro speciální aplikace a používá se jenom pro [partnerské vztahy Microsoftu](mailto:peering@microsoft.com).
    * Vyberte **SKU** jako *základní zdarma*. Nevybírejte možnost *Premium zdarma* , protože je vyhrazená pro speciální aplikace.
    * Vyberte umístění **Metro** , pro které chcete převést partnerský vztah na prostředek Azure. Pokud máte partnerské vztahy s Microsoftem ve vybraném umístění **Metro** , které se nepřevádějí do prostředku Azure, pak budou tato připojení uvedená v části **připojení partnerských vztahů** , jak je znázorněno níže. Nyní můžete tato připojení partnerských vztahů převést na prostředek Azure.

        > [!div class="mx-imgBorder"]
        > Konfigurace partnerského vztahu ![konfigurace – přímá – starší](../media/setup-directlegacy-conf-tab.png)

1. Pokud potřebujete šířku pásma aktualizovat, klikněte na tlačítko Upravit pro řádek, jak je zvýrazněno níže, a upravte nastavení připojení.

    > [!div class="mx-imgBorder"]
    > Konfigurace partnerského vztahu ![– přímé úpravy](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Pokud chcete přidat další připojení partnerského vztahu se společností Microsoft ve vybraném umístění služby **Metro** , klikněte na tlačítko **vytvořit nové** . Další informace najdete v tématu [Vytvoření nebo úprava přímého partnerského vztahu pomocí portálu](../howto-direct-portal.md) .
    >

1. Klikněte na **zkontrolovat + vytvořit**. Všimněte si, že na portálu běží základní ověření informací, které jste zadali. Zobrazuje se na pásu karet nahoře a *spouští se konečné ověření...* .

    > [!div class="mx-imgBorder"]
    > Karta ověření partnerského vztahu ![](../media/setup-direct-review-tab-validation.png)

1. Po *úspěšném ověření*ověřte informace a odešlete žádost kliknutím na **vytvořit**. Pokud potřebujete žádost upravit, klikněte na **Předchozí** a opakujte postup výše.

    > [!div class="mx-imgBorder"]
    > ![odesílání partnerských vztahů](../media/setup-direct-review-tab-submit.png)

1. Po odeslání žádosti počkejte, než se dokončí nasazení. Pokud nasazení neproběhne úspěšně, kontaktujte [partnerský vztah Microsoftu](mailto:peering@microsoft.com). Úspěšné nasazení se zobrazí jako níže.

    > [!div class="mx-imgBorder"]
    > Úspěšnost partnerského vztahu ![](../media/setup-direct-success.png)
