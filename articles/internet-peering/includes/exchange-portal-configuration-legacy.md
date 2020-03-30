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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775209"
---
1. Na stránce **Vytvořit partnerský vztah** v části Karta **Konfigurace** vyplňte pole, jak je znázorněno níže.

    > [!div class="mx-imgBorder"]
    > ![Konfigurace partnerského vztahu – výměna](../media/setup-exchange-conf-tab.png)

    * V **pole Typ partnerského vztahu**vyberte možnost *Exchange*.
    * Vyberte **skladovou položku** jako *základní volnou položku*.
    * Zvolte umístění **Metro,** kde chcete převést partnerský vztah na prostředek Azure. Pokud máte připojení partnerského vztahu s Microsoftem ve vybraném umístění **Metro,** které nejsou převedeny na prostředek Azure, pak tato připojení budou uvedeny v části **připojení partnerského vztahu,** jak je znázorněno níže. Teď můžete převést tato připojení partnerského vztahu na prostředek Azure.

        > [!div class="mx-imgBorder"]
        > ![Konfigurace partnerského vztahu – exchange – starší připojení](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > Nastavení starších připojení partnerského vztahu nelze změnit. Pokud chcete přidat další připojení partnerského vztahu se společností Microsoft ve vybraném umístění **metro,** můžete tak učinit kliknutím na **tlačítko Vytvořit nový.** Další informace najdete [v tématu Vytvoření nebo úprava partnerského vztahu exchange pomocí portálu.](../howto-exchange-portal.md)
        >

1. Klikněte na **Recenze + vytvořit**. Všimněte si, že portál spouští základní ověření zadaných informací. Tohle je nahoře zobrazeno na pásu karet, jako *Spuštěné konečné ověření...*.

    > [!div class="mx-imgBorder"]
    > ![Karta Ověření partnerského vztahu](../media/setup-direct-review-tab-validation.png)

1. Po zobrazení přejde na *ověření předáno*, ověřte své informace a odešlete žádost klepnutím na tlačítko **Vytvořit**. Pokud potřebujete upravit svůj požadavek, klikněte na **Předchozí** a opakujte výše uvedené kroky.

    > [!div class="mx-imgBorder"]
    > ![Odeslání partnerského vztahu](../media/setup-exchange-review-tab-submit.png)

1. Po odeslání žádosti počkejte na dokončení nasazení. Pokud se nasazení nezdaří, obraťte se na [partnerský vztah společnosti Microsoft](mailto:peering@microsoft.com). Úspěšné nasazení se zobrazí níže.

    > [!div class="mx-imgBorder"]
    > ![Úspěch partnerského vztahu](../media/setup-direct-success.png)
