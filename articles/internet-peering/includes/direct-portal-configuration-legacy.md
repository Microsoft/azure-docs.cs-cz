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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775066"
---
1. Na stránce **Vytvořit partnerský vztah** v části Karta **Konfigurace** vyplňte pole, jak je znázorněno níže.

    > [!div class="mx-imgBorder"]
    > ![Konfigurace partnerského vztahu – přímá](../media/setup-direct-conf-tab.png)

    * V **pole Typ partnerského vztahu**vyberte možnost *Přímé*.
    * V **síti Microsoft**zvolte *AS8075*. Nevybírejte ASN 8069. Je vyhrazena pro speciální aplikace a používá pouze [partnerský vztah společnosti Microsoft](mailto:peering@microsoft.com).
    * Vyberte **skladovou položku** jako *základní volnou položku*. Nevybírejte *Premium Free,* protože je vyhrazenpro speciální aplikace.
    * Zvolte umístění **Metro,** kde chcete převést partnerský vztah na prostředek Azure. Pokud máte připojení partnerského vztahu s Microsoftem ve vybraném umístění **Metro,** které nejsou převedeny na prostředek Azure, pak tato připojení budou uvedeny v části **připojení partnerského vztahu,** jak je znázorněno níže. Teď můžete převést tato připojení partnerského vztahu na prostředek Azure.

        > [!div class="mx-imgBorder"]
        > ![Konfigurace partnerského vztahu – přímá – starší připojení](../media/setup-directlegacy-conf-tab.png)

1. Pokud potřebujete aktualizovat šířku pásma, klepněte na tlačítko upravit pro řádek, jak je zvýrazněno níže, a upravte nastavení připojení.

    > [!div class="mx-imgBorder"]
    > ![Konfigurace partnerského vztahu – přímé úpravy](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Pokud chcete přidat další připojení partnerského vztahu se společností Microsoft ve vybraném umístění **metro,** můžete tak učinit kliknutím na **tlačítko Vytvořit nový.** Další informace najdete [v tématu Vytvoření nebo úprava přímého partnerského vztahu pomocí portálu.](../howto-direct-portal.md)
    >

1. Klikněte na **Recenze + vytvořit**. Všimněte si, že portál spouští základní ověření zadaných informací. Tohle je nahoře zobrazeno na pásu karet, jako *Spuštěné konečné ověření...*.

    > [!div class="mx-imgBorder"]
    > ![Karta Ověření partnerského vztahu](../media/setup-direct-review-tab-validation.png)

1. Po zobrazení přejde na *ověření předáno*, ověřte své informace a odešlete žádost klepnutím na tlačítko **Vytvořit**. Pokud potřebujete upravit svůj požadavek, klikněte na **Předchozí** a opakujte výše uvedené kroky.

    > [!div class="mx-imgBorder"]
    > ![Odeslání partnerského vztahu](../media/setup-direct-review-tab-submit.png)

1. Po odeslání žádosti počkejte na dokončení nasazení. Pokud se nasazení nezdaří, obraťte se na [partnerský vztah společnosti Microsoft](mailto:peering@microsoft.com). Úspěšné nasazení se zobrazí níže.

    > [!div class="mx-imgBorder"]
    > ![Úspěch partnerského vztahu](../media/setup-direct-success.png)
