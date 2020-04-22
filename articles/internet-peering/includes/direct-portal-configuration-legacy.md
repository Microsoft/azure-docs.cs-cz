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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678830"
---
1. Na stránce **Vytvořit partnerský vztah vyplňte** na kartě **Konfigurace** pole, jak je znázorněno zde.

    > [!div class="mx-imgBorder"]
    > ![Vytvoření karty Konfigurace stránky partnerského vztahu](../media/setup-direct-conf-tab.png)

    * V **pole Typ partnerského vztahu**vyberte možnost **Přímé**.
    * V **síti Microsoft**vyberte možnost **AS8075**. Nevybírejte ASN 8069. Je vyhrazenpro speciální aplikace a používá jej pouze [partnerský vztah microsoftu](mailto:peering@microsoft.com).
    * Vyberte **skladovou položku** jako **základní volnou položku**. Nevybírejte Premium Free, protože je vyhrazenpro speciální aplikace.
    * Vyberte umístění **Metro,** kde chcete převést partnerský vztah na prostředek Azure. Pokud máte partnerské vztahy s Microsoftem ve vybraném umístění **Metro,** které nejsou převedeny na prostředek Azure, tato připojení budou uvedeny v části **připojení partnerského vztahu,** jak je znázorněno. Teď můžete převést tato připojení partnerského vztahu na prostředek Azure.

        > [!div class="mx-imgBorder"]
        > ![Seznam připojení partnerského vztahu](../media/setup-directlegacy-conf-tab.png)

1. Pokud potřebujete aktualizovat šířku pásma, vyberte tlačítko upravit pro řádek a upravte nastavení připojení.

    > [!div class="mx-imgBorder"]
    > ![Tlačítko Upravit](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Pokud chcete přidat další připojení partnerského vztahu se společností Microsoft ve vybraném umístění **Metro,** vyberte **Vytvořit nový**. Další informace naleznete v [tématu Vytvoření nebo úprava přímého partnerského vztahu pomocí portálu](../howto-direct-portal.md).
    >

1. Vyberte **Zkontrolovat a vytvořit**. Všimněte si, že portál spustí základní ověření zadaných informací. Pás karet v horní části zobrazuje zprávu *Spuštění konečného ověření...*.

    > [!div class="mx-imgBorder"]
    > ![Karta Ověření partnerského vztahu](../media/setup-direct-review-tab-validation.png)

1. Po schválení zprávy na *ověření*ověřte své informace. Odešlete žádost výběrem **možnosti Vytvořit**. Chcete-li upravit požadavek, vyberte **Předchozí** a opakujte kroky.

    > [!div class="mx-imgBorder"]
    > ![Odeslání partnerského vztahu](../media/setup-direct-review-tab-submit.png)

1. Po odeslání požadavku počkejte na dokončení nasazení. Pokud se nasazení nezdaří, obraťte se na [partnerský vztah společnosti Microsoft](mailto:peering@microsoft.com). Úspěšné nasazení se zobrazí, jak je znázorněno zde.

    > [!div class="mx-imgBorder"]
    > ![Úspěch peeringu](../media/setup-direct-success.png)
