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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678566"
---
1. Na stránce **Vytvořit partnerský vztah vyplňte** na kartě **Konfigurace** pole, jak je znázorněno zde.

    > [!div class="mx-imgBorder"]
    > ![Vytvoření typu partnerského vztahu stránky partnerského vztahu serveru Partner](../media/setup-exchange-conf-tab.png)

    * V **pole Typ partnerského vztahu**vyberte možnost **Exchange**.
    * Vyberte **skladovou položku** jako **základní volnou položku**.
    * Vyberte umístění **Metro,** kde chcete převést partnerský vztah na prostředek Azure. Pokud máte partnerské vztahy s Microsoftem ve vybraném umístění **Metro,** které nejsou převedeny na prostředek Azure, tato připojení budou uvedeny v části **připojení partnerského vztahu,** jak je znázorněno. Teď můžete převést tato připojení partnerského vztahu na prostředek Azure.

        > [!div class="mx-imgBorder"]
        > ![Seznam připojení partnerského vztahu](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > Nastavení starších připojení partnerského vztahu nelze změnit. Pokud chcete přidat další připojení partnerského vztahu se společností Microsoft ve vybraném umístění **Metro,** vyberte **Vytvořit nový**. Další informace naleznete v [tématu Vytvoření nebo úprava partnerského vztahu serveru Exchange pomocí portálu](../howto-exchange-portal.md).
        >

1. Vyberte **Zkontrolovat a vytvořit**. Všimněte si, že portál spustí základní ověření zadaných informací. Pás karet v horní části zobrazuje zprávu *Spuštění konečného ověření...*.

    > [!div class="mx-imgBorder"]
    > ![Karta Ověření partnerského vztahu](../media/setup-direct-review-tab-validation.png)

1. Po schválení zprávy na *ověření*ověřte své informace. Odešlete žádost výběrem **možnosti Vytvořit**. Pokud potřebujete upravit požadavek, vyberte **Předchozí** a opakujte kroky.

    > [!div class="mx-imgBorder"]
    > ![Odeslání partnerského vztahu](../media/setup-exchange-review-tab-submit.png)

1. Po odeslání požadavku počkejte na dokončení nasazení. Pokud se nasazení nezdaří, obraťte se na [partnerský vztah společnosti Microsoft](mailto:peering@microsoft.com). Úspěšné nasazení se zobrazí podle obrázku.

    > [!div class="mx-imgBorder"]
    > ![Úspěch peeringu](../media/setup-direct-success.png)
