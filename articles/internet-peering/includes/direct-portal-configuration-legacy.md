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
ms.openlocfilehash: 632490498b8dd13414657edb9518cd543ac07af6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "81678830"
---
1. Na stránce **vytvořit partnerský vztah** na kartě **Konfigurace** vyplňte pole, jak je znázorněno zde.

    > [!div class="mx-imgBorder"]
    > ![Vytvořit partnerský vztah na stránce konfigurace](../media/setup-direct-conf-tab.png)

    * V případě **typu partnerského vztahu** vyberte možnost **Direct**.
    * V případě **sítě Microsoft** vyberte **AS8075**. Nevybírejte číslo ASN 8069. Je vyhrazený pro speciální aplikace a používá se jenom pro [partnerské vztahy Microsoftu](mailto:peering@microsoft.com).
    * Vyberte **SKU** jako **základní zdarma**. Nevybírejte možnost Premium zdarma, protože je vyhrazená pro speciální aplikace.
    * Vyberte umístění **Metro** , kde chcete převést partnerský vztah na prostředek Azure. Pokud máte připojení s partnerským vztahem k Microsoftu ve vybraném umístění **Metro** , které se nepřevede na prostředek Azure, budou tato připojení uvedená v části **připojení partnerských vztahů** , jak je znázorněno na obrázku. Nyní můžete tato připojení partnerských vztahů převést na prostředek Azure.

        > [!div class="mx-imgBorder"]
        > ![Seznam připojení partnerských vztahů](../media/setup-directlegacy-conf-tab.png)

1. Pokud potřebujete šířku pásma aktualizovat, vyberte tlačítko Upravit pro řádek, abyste mohli upravit nastavení připojení.

    > [!div class="mx-imgBorder"]
    > ![Tlačítko Upravit](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Pokud chcete přidat další připojení partnerského vztahu se společností Microsoft ve vybraném umístění **Metro** , vyberte **vytvořit novou**. Další informace najdete v tématu [Vytvoření nebo úprava přímého partnerského vztahu pomocí portálu](../howto-direct-portal.md).
    >

1. Vyberte **Zkontrolovat a vytvořit**. Všimněte si, že portál spouští základní ověření informací, které jste zadali. Pás karet v horní části zobrazuje zprávu *s finálním ověřením...*.

    > [!div class="mx-imgBorder"]
    > ![Karta ověření partnerského vztahu](../media/setup-direct-review-tab-validation.png)

1. Po změně zprávy na *prošlé ověření* ověřte své informace. Žádost odešlete tak, že vyberete **vytvořit**. Pokud chcete žádost upravit, vyberte **Předchozí** a opakujte postup.

    > [!div class="mx-imgBorder"]
    > ![Odesílání partnerských vztahů](../media/setup-direct-review-tab-submit.png)

1. Po odeslání žádosti počkejte, než se nasazení dokončí. Pokud nasazení neproběhne úspěšně, kontaktujte [partnerský vztah Microsoftu](mailto:peering@microsoft.com). Úspěšné nasazení se zobrazí, jak je znázorněno zde.

    > [!div class="mx-imgBorder"]
    > ![Úspěšné vytvoření partnerského vztahu](../media/setup-direct-success.png)
