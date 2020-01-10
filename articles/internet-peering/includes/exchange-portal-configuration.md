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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774494"
---
1. Na stránce **vytvořit partnerský vztah** na kartě **Konfigurace** vyplňte pole, jak vidíte níže.

    > [!div class="mx-imgBorder"]
    > Konfigurace partnerského vztahu ![– Exchange](../media/setup-exchange-conf-tab.png)

    * Jako **typ partnerského vztahu**vyberte *Exchange*.
    * Vyberte **SKU** jako *základní zdarma*.
    * Vyberte umístění **Metro** , pro které chcete nastavit partnerský vztah.

        > [!NOTE]
        > Pokud už máte partnerské vztahy s Microsoftem ve vybraném umístění **Metro** a při prvním použití portálu pro nastavení partnerského vztahu v tomto umístění používáte portál, pak se existující připojení partnerských vztahů budou zobrazovat v části **připojení partnerských vztahů** , jak je znázorněno níže. Microsoft automaticky převede Tato připojení partnerských vztahů na prostředek Azure, aby je bylo možné spravovat společně s novými připojeními, a to na jednom místě. Další informace najdete v tématu [převedení staršího partnerského vztahu serveru Exchange na prostředek Azure pomocí portálu](../howto-legacy-exchange-portal.md) .
        >

1. V části **připojení partnerských vztahů**klikněte na **vytvořit novou** a přidejte řádek pro každé nové připojení, které chcete nastavit.

    * Pokud chcete nakonfigurovat nebo upravit nastavení připojení, klikněte na tlačítko Upravit pro řádek.

        > [!div class="mx-imgBorder"]
        > Konfigurace partnerského vztahu ![– úprava pro Exchange](../media/setup-exchange-conf-tab-edit.png)

    * Pokud chcete řádek odstranit, klikněte na tlačítko **...** > **Odstranit**.

        > [!div class="mx-imgBorder"]
        > Konfigurace partnerského vztahu ![– úprava pro Exchange](../media/setup-exchange-conf-tab-delete.png)

    * Je nutné zadat všechna nastavení pro připojení, jak je uvedeno níže.

         > [!div class="mx-imgBorder"]
         > Konfigurace partnerského vztahu ![– připojení Exchange](../media/setup-exchange-conf-tab-connection.png)

        1. Vyberte **zařízení partnerského vztahu** , ve kterém se musí nastavit připojení.
        1. Do pole **adresa IPv4** a **adresa IPv6**zadejte adresu IPv4 a IPv6, která by se nakonfigurovala ve směrovačích Microsoftu pomocí příkazu Neighbor.
        1. Zadejte počet předpon IPv4 a IPv6, které budete inzerovat v polích maximální počet **inzerovaných IPv4 adres** a **maximální počet inzerovaných adres IPv6** .
        1. Kliknutím na **OK** uložte nastavení připojení.

1. Opakujte výše uvedený krok a přidejte další připojení v jakémkoli zařízení, ve kterém je společnost Microsoft ve vaší síti, v rámci služby **Metro** vybrané dříve.

1. Po přidání všech požadovaných připojení klikněte na **zkontrolovat + vytvořit**.

    > [!div class="mx-imgBorder"]
    > Karta konfigurace partnerského vztahu ![finální](../media/setup-exchange-conf-tab-final.png)

1. Všimněte si, že na portálu běží základní ověření informací, které jste zadali. Zobrazuje se na pásu karet nahoře a *spouští se konečné ověření...* .

    > [!div class="mx-imgBorder"]
    > Karta ověření partnerského vztahu ![](../media/setup-direct-review-tab-validation.png)

1. Po *úspěšném ověření*ověřte informace a odešlete žádost kliknutím na **vytvořit**. Pokud potřebujete žádost upravit, klikněte na **Předchozí** a opakujte postup výše.

    > [!div class="mx-imgBorder"]
    > ![odesílání partnerských vztahů](../media/setup-exchange-review-tab-submit.png)

1. Po odeslání žádosti počkejte, než se dokončí nasazení. Pokud nasazení neproběhne úspěšně, kontaktujte [partnerský vztah Microsoftu](mailto:peering@microsoft.com). Úspěšné nasazení se zobrazí jako níže.

    > [!div class="mx-imgBorder"]
    > Úspěšnost partnerského vztahu ![](../media/setup-direct-success.png)
