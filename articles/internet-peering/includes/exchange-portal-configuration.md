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
ms.openlocfilehash: 268703081a2a40e8bcc665889eaeaf8edd673bfd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680956"
---
1. Na stránce **Vytvořit partnerský vztah vyplňte** na kartě **Konfigurace** pole tak, jak je znázorněno.

    > [!div class="mx-imgBorder"]
    > ![Vytvoření typu partnerského vztahu stránky partnerského vztahu serveru Partner](../media/setup-exchange-conf-tab.png)

    * V **pole Typ partnerského vztahu**vyberte možnost **Exchange**.
    * Vyberte **skladovou položku** jako **základní volnou položku**.
    * Vyberte umístění **Metro,** kde chcete nastavit partnerský vztah.

        > [!NOTE]
        > Pokud již máte připojení partnerského vztahu se společností Microsoft ve vybraném umístění **Metro** a portál používáte poprvé k nastavení partnerského vztahu v tomto umístění, budou vaše stávající připojení partnerského vztahu uvedena v části **Připojení partnerského vztahu,** jak je znázorněno. Microsoft automaticky převede tato připojení partnerského vztahu na prostředek Azure, takže je můžete spravovat společně s novými připojeními na jednom místě. Další informace najdete [v tématu Převod staršího partnerského vztahu Exchange na prostředek Azure pomocí portálu](../howto-legacy-exchange-portal.md).
        >

1. V části **Připojení partnerského vztahu**vyberte **Vytvořit nový** a přidejte řádek pro každé nové připojení, které chcete nastavit.

    * Chcete-li konfigurovat nebo upravit nastavení připojení, vyberte tlačítko pro úpravy linky.

        > [!div class="mx-imgBorder"]
        > ![Tlačítko Upravit](../media/setup-exchange-conf-tab-edit.png)

    * Chcete-li řádek odstranit, vyberte **...**  >  **Odstranit**.

        > [!div class="mx-imgBorder"]
        > ![Tlačítko Odstranit](../media/setup-exchange-conf-tab-delete.png)

    * Musíte zadat všechna nastavení pro připojení, jak je znázorněno zde.

         > [!div class="mx-imgBorder"]
         > ![Stránka Připojení partnerského vztahu serveru Exchange](../media/setup-exchange-conf-tab-connection.png)

        1. Vyberte **zařízení partnerského vztahu,** ve kterém je potřeba připojení nastavit.
        1. Do polí **Adresy IPv4** a **Adresy IPv6** zadejte adresy IPv4 a IPv6, které by byly konfigurovány v směrovačích Microsoft pomocí sousedního příkazu.
        1. Zadejte počet předpon IPv4 a IPv6, které budete inzerovat, do polí **Maximální inzerované adresy IPv4** a **Maximální inzerované adresy IPv6.**
        1. Chcete-li uložit nastavení připojení, vyberte **ok.**

1. Opakováním kroku přidáte další připojení v libovolném zařízení, kde je společnost Microsoft umístěna společně s vaší sítí, v rámci dříve vybraného **zařízení Metro.**

1. Po přidání všech požadovaných připojení vyberte **Zkontrolovat + vytvořit**.

    > [!div class="mx-imgBorder"]
    > ![Karta Konfigurace partnerského vztahu](../media/setup-exchange-conf-tab-final.png)

1. Všimněte si, že portál spustí základní ověření zadaných informací. Pás karet v horní části zobrazuje zprávu *Spuštění konečného ověření...*.

    > [!div class="mx-imgBorder"]
    > ![Karta Ověření partnerského vztahu](../media/setup-direct-review-tab-validation.png)

1. Po schválení zprávy na *ověření*ověřte své informace. Odešlete žádost výběrem **možnosti Vytvořit**. Chcete-li upravit požadavek, vyberte **Předchozí** a opakujte kroky.

    > [!div class="mx-imgBorder"]
    > ![Odeslání partnerského vztahu](../media/setup-exchange-review-tab-submit.png)

1. Po odeslání požadavku počkejte na dokončení nasazení. Pokud se nasazení nezdaří, obraťte se na [partnerský vztah společnosti Microsoft](mailto:peering@microsoft.com). Úspěšné nasazení se zobrazí, jak je znázorněno zde.

    > [!div class="mx-imgBorder"]
    > ![Úspěch peeringu](../media/setup-direct-success.png)
