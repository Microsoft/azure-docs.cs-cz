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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81680956"
---
1. Na stránce **vytvořit partnerský vztah** na kartě **Konfigurace** vyplňte pole, jak je vidět na obrázku.

    > [!div class="mx-imgBorder"]
    > ![Vytvoření partnerského vztahu typu stránky pro výměnu](../media/setup-exchange-conf-tab.png)

    * Jako **typ partnerského vztahu**vyberte **Exchange**.
    * Vyberte **SKU** jako **základní zdarma**.
    * Vyberte umístění **Metro** , kde chcete nastavit partnerský vztah.

        > [!NOTE]
        > Pokud už máte partnerské vztahy s Microsoftem ve vybraném umístění **Metro** a při prvním použití portálu nastavíte partnerský vztah v tomto umístění, budou se existující připojení partnerských vztahů zobrazovat v části **připojení partnerských vztahů** , jak je znázorněno na obrázku. Microsoft automaticky převede Tato připojení partnerských vztahů na prostředek Azure, aby je bylo možné spravovat společně s novými připojeními na jednom místě. Další informace najdete v tématu [převedení staršího partnerského vztahu serveru Exchange na prostředek Azure pomocí portálu](../howto-legacy-exchange-portal.md).
        >

1. V části **připojení partnerského vztahu**vyberte **vytvořit novou** a přidejte řádek pro každé nové připojení, které chcete nastavit.

    * Chcete-li konfigurovat nebo upravit nastavení připojení, vyberte tlačítko Upravit pro řádek.

        > [!div class="mx-imgBorder"]
        > ![Tlačítko Upravit](../media/setup-exchange-conf-tab-edit.png)

    * Pokud chcete řádek odstranit, vyberte **...**  >  **Odstraňte**.

        > [!div class="mx-imgBorder"]
        > ![Tlačítko Odstranit](../media/setup-exchange-conf-tab-delete.png)

    * Je nutné zadat všechna nastavení pro připojení, jak je znázorněno zde.

         > [!div class="mx-imgBorder"]
         > ![Stránka připojení partnerského vztahu Exchange](../media/setup-exchange-conf-tab-connection.png)

        1. Vyberte **zařízení partnerského vztahu** , ve kterém se musí nastavit připojení.
        1. Do polí **adresa IPv4** a **IPv6 adresa** zadejte adresy IPv4 a IPv6, které by byly nakonfigurované ve směrovačích Microsoft pomocí sousedního příkazu.
        1. Zadejte počet předpon IPv4 a IPv6, které budete inzerovat v **maximálních inzerovaných IPv4 adresách** a **maximálních polích inzerovaných IPv6 adres** v uvedeném pořadí.
        1. Vyberte **OK** a uložte nastavení připojení.

1. Opakováním tohoto kroku přidejte další připojení v jakémkoli zařízení, ve kterém je společnost Microsoft ve vaší síti, v rámci služby **Metro** vybrané dříve.

1. Po přidání všech požadovaných připojení vyberte **zkontrolovat + vytvořit**.

    > [!div class="mx-imgBorder"]
    > ![Karta konfigurace partnerského vztahu](../media/setup-exchange-conf-tab-final.png)

1. Všimněte si, že portál spouští základní ověření informací, které jste zadali. Pás karet v horní části zobrazuje zprávu *s finálním ověřením...*.

    > [!div class="mx-imgBorder"]
    > ![Karta ověření partnerského vztahu](../media/setup-direct-review-tab-validation.png)

1. Po změně zprávy na *prošlé ověření*ověřte své informace. Žádost odešlete tak, že vyberete **vytvořit**. Pokud chcete žádost upravit, vyberte **Předchozí** a opakujte postup.

    > [!div class="mx-imgBorder"]
    > ![Odesílání partnerských vztahů](../media/setup-exchange-review-tab-submit.png)

1. Po odeslání žádosti počkejte, než se nasazení dokončí. Pokud nasazení neproběhne úspěšně, kontaktujte [partnerský vztah Microsoftu](mailto:peering@microsoft.com). Úspěšné nasazení se zobrazí, jak je znázorněno zde.

    > [!div class="mx-imgBorder"]
    > ![Úspěšné vytvoření partnerského vztahu](../media/setup-direct-success.png)
