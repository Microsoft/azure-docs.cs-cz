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
ms.openlocfilehash: 2a42ba8809e6895c9eea9f8762513b7fcaa9eb3a
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775274"
---
1. Na stránce **vytvořit partnerský vztah** na kartě **Konfigurace** vyplňte pole, jak vidíte níže.

    * V případě **typu partnerského vztahu**vyberte možnost *Direct*.
    * V případě **sítě Microsoft**vyberte možnost *AS8075*. Nevytvářejte partnerské vztahy s číslem ASN 8069. Je vyhrazený pro speciální aplikace a používá se jenom pro [partnerské vztahy Microsoftu](mailto:peering@microsoft.com).
    * Vyberte **SKU** jako *základní zdarma*. Nevybírejte možnost *Premium zdarma* , protože je vyhrazená pro speciální aplikace.
    * Vyberte umístění **Metro** , pro které chcete nastavit partnerský vztah.

        > [!NOTE]
        > Pokud už máte partnerské vztahy s Microsoftem ve vybraném umístění **Metro** a při prvním použití portálu pro nastavení partnerského vztahu v tomto umístění používáte portál, pak se existující připojení partnerských vztahů budou zobrazovat v části **připojení partnerských vztahů** , jak je znázorněno níže. Microsoft automaticky převede Tato připojení partnerských vztahů na prostředek Azure, aby je bylo možné spravovat společně s novými připojeními, a to na jednom místě. Další informace najdete v tématu [převedení starší verze přímého partnerského vztahu na prostředek Azure pomocí portálu](../howto-legacy-direct-portal.md) .
        >

1. V části **připojení partnerských vztahů**klikněte na **vytvořit novou** a přidejte řádek pro každé nové připojení, které chcete nastavit.

    * Pokud chcete nakonfigurovat nebo upravit nastavení připojení, klikněte na tlačítko Upravit pro řádek.

        > [!div class="mx-imgBorder"]
        > Konfigurace partnerského vztahu ![– přímé úpravy](../media/setup-direct-conf-tab-edit.png)
    
    * Pokud chcete řádek odstranit, klikněte na tlačítko **...** > **Odstranit**.

        > [!div class="mx-imgBorder"]
        > Konfigurace partnerského vztahu ![– přímé úpravy](../media/setup-direct-conf-tab-delete.png)

    * Je nutné zadat všechna nastavení pro připojení, jak je uvedeno níže.

         > [!div class="mx-imgBorder"]
         > Konfigurace partnerského vztahu ![– přímé připojení](../media/setup-direct-conf-tab-connection.png)

        1. Vyberte **zařízení partnerského vztahu** , ve kterém se musí nastavit připojení.
        1. **Zprostředkovatel adres relace** se používá k určení toho, kdo má podsíť potřebnou k nastavení relace protokolu BGP mezi vaší sítí a Microsoftem. Pokud je možné zadat podsíť, zvolte možnost *peer*. Jinak se vám bude kontaktovat partnerský vztah **Microsoftu** a [Microsoft peering](mailto:peering@microsoft.com) . Mějte na paměti, že pokud se rozhodnete, že to bude trvat déle, než Microsoft zpracuje požadavek partnerských vztahů. V některých případech nemusí být společnost Microsoft schopna poskytnout podsítě, které budou mít za následek odepření žádosti.
        1. Pokud jste zvolili možnost **zprostředkovatel adresy relace** jako *rovnocenný*, zadejte adresu IPv4 a IPv6 spolu s maskou předpony do pole **předpona IPv4 relace** a **předpony IPv6 relace** .
        1. Zadejte počet předpon IPv4 a IPv6, které budete inzerovat v polích maximální počet **inzerovaných IPv4 adres** a **maximální počet inzerovaných adres IPv6** .
        1. Nastavte posuvník **celkové šířky pásma** tak, aby odrážel šířku pásma pro připojení.
        1. Kliknutím na **OK** uložte nastavení připojení.

1. Opakujte výše uvedený krok a přidejte další připojení v jakémkoli zařízení, ve kterém je společnost Microsoft ve vaší síti, v rámci služby **Metro** vybrané dříve.

1. Po přidání všech požadovaných připojení klikněte na **zkontrolovat + vytvořit**.

    > [!div class="mx-imgBorder"]
    > Karta konfigurace partnerského vztahu ![finální](../media/setup-direct-conf-tab-final.png)

1. Všimněte si, že na portálu běží základní ověření informací, které jste zadali. Zobrazuje se na pásu karet nahoře a *spouští se konečné ověření...* .

    > [!div class="mx-imgBorder"]
    > Karta ověření partnerského vztahu ![](../media/setup-direct-review-tab-validation.png)

1. Po *úspěšném ověření*ověřte informace a odešlete žádost kliknutím na **vytvořit**. Pokud potřebujete žádost upravit, klikněte na **Předchozí** a opakujte postup výše.

    > [!div class="mx-imgBorder"]
    > ![odesílání partnerských vztahů](../media/setup-direct-review-tab-submit.png)

1. Po odeslání žádosti počkejte, než se dokončí nasazení. Pokud nasazení neproběhne úspěšně, kontaktujte [partnerský vztah Microsoftu](mailto:peering@microsoft.com). Úspěšné nasazení se zobrazí jako níže.

    > [!div class="mx-imgBorder"]
    > Úspěšnost partnerského vztahu ![](../media/setup-direct-success.png)
