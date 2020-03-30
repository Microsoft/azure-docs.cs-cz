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
ms.openlocfilehash: cd51eca0ea4563e1b56f74677df0829669d9e177
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774494"
---
1. Na stránce **Vytvořit partnerský vztah** v části Karta **Konfigurace** vyplňte pole, jak je znázorněno níže.

    > [!div class="mx-imgBorder"]
    > ![Konfigurace partnerského vztahu – výměna](../media/setup-exchange-conf-tab.png)

    * V **pole Typ partnerského vztahu**vyberte možnost *Exchange*.
    * Vyberte **skladovou položku** jako *základní volnou položku*.
    * Zvolte umístění **Metro,** kde chcete nastavit partnerský vztah.

        > [!NOTE]
        > Pokud již máte připojení partnerského vztahu se společností Microsoft ve vybraném umístění **Metro** a používáte portál poprvé k nastavení partnerského vztahu v tomto umístění, budou vaše stávající připojení partnerského vztahu uvedena v části **Připojení partnerského vztahu,** jak je znázorněno níže. Microsoft automaticky převede tato připojení partnerského vztahu na prostředek Azure, takže je můžete spravovat společně s novými připojeními na jednom místě. Další informace najdete [v tématu Převod staršího partnerského vztahu Exchange na prostředek Azure pomocí portálu.](../howto-legacy-exchange-portal.md)
        >

1. V části **Připojení partnerského vztahu**klikněte na **Vytvořit nový** a přidejte řádek pro každé nové připojení, které chcete nastavit.

    * Chcete-li konfigurovat nebo měnit nastavení připojení, klepněte na tlačítko upravit řádek.

        > [!div class="mx-imgBorder"]
        > ![Konfigurace partnerského vztahu – úpravy serveru Exchange](../media/setup-exchange-conf-tab-edit.png)

    * Chcete-li odstranit řádek, klikněte na **tlačítko ...** > **Odstranit**.

        > [!div class="mx-imgBorder"]
        > ![Konfigurace partnerského vztahu – úpravy serveru Exchange](../media/setup-exchange-conf-tab-delete.png)

    * Musíte zadat všechna nastavení pro připojení, jak je znázorněno níže.

         > [!div class="mx-imgBorder"]
         > ![Konfigurace partnerského vztahu – připojení exchange](../media/setup-exchange-conf-tab-connection.png)

        1. Vyberte **zařízení partnerského vztahu,** ve kterém je potřeba připojení nastavit.
        1. Do polí **Adresa IPv4** a **Adresa IPv6**zadejte adresu IPv4 a IPv6, která by byla konfigurována v směrovačích společnosti Microsoft pomocí sousedního příkazu.
        1. Zadejte počet předpon IPv4 a IPv6, které budete inzerovat, do polí **Maximálně inzerované adresy IPv4** a **Maximálně inzerované adresy IPv6.**
        1. Klepnutím na **tlačítko OK** uložte nastavení připojení.

1. Chcete-li přidat další připojení v jakémkoli zařízení, ve kterém je společnost Microsoft umístěna společně s vaší sítí, v rámci dříve vybraného **zařízení metro,** opakujte výše uvedený krok.

1. Po přidání všech požadovaných připojení klikněte na **zkontrolovat + vytvořit**.

    > [!div class="mx-imgBorder"]
    > ![Konfigurační karta partnerského vztahu je konečná](../media/setup-exchange-conf-tab-final.png)

1. Všimněte si, že portál spouští základní ověření zadaných informací. Tohle je nahoře zobrazeno na pásu karet, jako *Spuštěné konečné ověření...*.

    > [!div class="mx-imgBorder"]
    > ![Karta Ověření partnerského vztahu](../media/setup-direct-review-tab-validation.png)

1. Po zobrazení přejde na *ověření předáno*, ověřte své informace a odešlete žádost klepnutím na tlačítko **Vytvořit**. Pokud potřebujete upravit svůj požadavek, klikněte na **Předchozí** a opakujte výše uvedené kroky.

    > [!div class="mx-imgBorder"]
    > ![Odeslání partnerského vztahu](../media/setup-exchange-review-tab-submit.png)

1. Po odeslání žádosti počkejte na dokončení nasazení. Pokud se nasazení nezdaří, obraťte se na [partnerský vztah společnosti Microsoft](mailto:peering@microsoft.com). Úspěšné nasazení se zobrazí níže.

    > [!div class="mx-imgBorder"]
    > ![Úspěch partnerského vztahu](../media/setup-direct-success.png)
