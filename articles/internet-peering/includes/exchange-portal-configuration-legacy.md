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
ms.openlocfilehash: b8869e93a7156b24d61ac555c95b9ca7f850ae34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "81678566"
---
1. Na stránce **vytvořit partnerský vztah** na kartě **Konfigurace** vyplňte pole, jak je znázorněno zde.

    > [!div class="mx-imgBorder"]
    > ![Vytvoření partnerského vztahu typu stránky pro výměnu](../media/setup-exchange-conf-tab.png)

    * Jako **typ partnerského vztahu** vyberte **Exchange**.
    * Vyberte **SKU** jako **základní zdarma**.
    * Vyberte umístění **Metro** , kde chcete převést partnerský vztah na prostředek Azure. Pokud máte připojení s partnerským vztahem k Microsoftu ve vybraném umístění **Metro** , které se nepřevede na prostředek Azure, budou tato připojení uvedená v části **připojení partnerských vztahů** , jak je znázorněno na obrázku. Nyní můžete tato připojení partnerských vztahů převést na prostředek Azure.

        > [!div class="mx-imgBorder"]
        > ![Seznam připojení partnerských vztahů](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > Nemůžete změnit nastavení pro starší připojení partnerských vztahů. Pokud chcete přidat další připojení partnerského vztahu se společností Microsoft ve vybraném umístění **Metro** , vyberte **vytvořit novou**. Další informace najdete v tématu [Vytvoření nebo úprava partnerského vztahu serveru Exchange pomocí portálu](../howto-exchange-portal.md).
        >

1. Vyberte **Zkontrolovat a vytvořit**. Všimněte si, že portál spouští základní ověření informací, které jste zadali. Pás karet v horní části zobrazuje zprávu *s finálním ověřením...*.

    > [!div class="mx-imgBorder"]
    > ![Karta ověření partnerského vztahu](../media/setup-direct-review-tab-validation.png)

1. Po změně zprávy na *prošlé ověření* ověřte své informace. Žádost odešlete tak, že vyberete **vytvořit**. Pokud potřebujete žádost upravit, vyberte **Předchozí** a opakujte postup.

    > [!div class="mx-imgBorder"]
    > ![Odesílání partnerských vztahů](../media/setup-exchange-review-tab-submit.png)

1. Po odeslání žádosti počkejte, než se nasazení dokončí. Pokud nasazení neproběhne úspěšně, kontaktujte [partnerský vztah Microsoftu](mailto:peering@microsoft.com). Úspěšné nasazení se zobrazí, jak je znázorněno na obrázku.

    > [!div class="mx-imgBorder"]
    > ![Úspěšné vytvoření partnerského vztahu](../media/setup-direct-success.png)
