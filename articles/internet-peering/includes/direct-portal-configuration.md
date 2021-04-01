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
ms.openlocfilehash: fda22346a44388248e37473bc7891b8a130569c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "81681081"
---
1. Na stránce **vytvořit partnerský vztah** na kartě **Konfigurace** vyplňte pole, jak je znázorněno zde.

    * V případě **typu partnerského vztahu** vyberte možnost **Direct**.
    * V případě **sítě Microsoft** vyberte **AS8075**. Nevytvářejte partnerský vztah s číslem ASN 8069. Je vyhrazený pro speciální aplikace a používá se jenom pro [partnerské vztahy Microsoftu](mailto:peering@microsoft.com).
    * Vyberte **SKU** jako **základní zdarma**. Nevybírejte možnost Premium zdarma, protože je vyhrazená pro speciální aplikace.
    * Vyberte umístění **Metro** , kde chcete nastavit partnerský vztah.

        > [!NOTE]
        > Pokud už máte partnerské vztahy s Microsoftem ve vybraném umístění **Metro** a při prvním použití Azure Portal k nastavení partnerského vztahu v tomto umístění, budou se existující připojení partnerských vztahů zobrazovat v části **připojení partnerských vztahů** , jak je znázorněno na obrázku. Microsoft automaticky převede Tato propojení partnerských vztahů na prostředek Azure, aby je bylo možné spravovat společně s novými připojeními, a to na jednom místě. Další informace najdete v tématu [Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí portálu](../howto-legacy-direct-portal.md).
        >

1. V části **připojení partnerského vztahu** vyberte **vytvořit novou** a přidejte řádek pro každé nové připojení, které chcete nastavit.

    * Chcete-li konfigurovat nebo upravit nastavení připojení, vyberte tlačítko Upravit pro řádek.

        > [!div class="mx-imgBorder"]
        > ![Tlačítko Upravit](../media/setup-direct-conf-tab-edit.png)
    
    * Pokud chcete řádek odstranit, vyberte **...**  >  **Odstraňte**.

        > [!div class="mx-imgBorder"]
        > ![Tlačítko Odstranit](../media/setup-direct-conf-tab-delete.png)

    * Je nutné zadat všechna nastavení pro připojení, jak je znázorněno zde.

         > [!div class="mx-imgBorder"]
         > ![Stránka přímého připojení partnerského vztahu](../media/setup-direct-conf-tab-connection.png)

        1. Vyberte **zařízení partnerského vztahu** , ve kterém se musí nastavit připojení.
        1. **Zprostředkovatel adres relace** se používá k určení, kdo poskytuje podsíť potřebnou k nastavení relace protokolu BGP mezi vaší sítí a Microsoftem. Pokud můžete zadat podsíť, vyberte možnost **peer**. V opačném případě vás budou kontaktovat **Microsoft** a [partnerské vztahy Microsoftu](mailto:peering@microsoft.com) . Výběr této možnosti bude trvat déle, než Microsoft zpracuje požadavek partnerského vztahu. V některých případech nemusí být společnost Microsoft schopna poskytnout podsítě, což bude mít za následek odepření žádosti.
        1. Pokud jste jako **rovnocennou** vybrali možnost **zprostředkovatel adresy** , zadejte adresy IPv4 a IPv6 spolu s předponami předpony v polích předpona **IPv4 relace** a **předpona IPv6 relace** v uvedeném pořadí.
        1. Zadejte počet předpon IPv4 a IPv6, které budete inzerovat v **maximálních inzerovaných IPv4 adresách** a **maximálních polích inzerovaných IPv6 adres** v uvedeném pořadí.
        1. Nastavte posuvník **celkové šířky pásma** tak, aby odrážel šířku pásma pro připojení.
        1. Vyberte **Uložit** a uložte nastavení připojení.

1. Opakujte předchozí krok a přidejte další připojení v jakémkoli zařízení, kde společnost Microsoft spolupracuje s vaší sítí, a to v rámci služby **Metro** , kterou jste vybrali dříve.

1. Po přidání všech požadovaných připojení vyberte **zkontrolovat + vytvořit**.

    > [!div class="mx-imgBorder"]
    > ![Finální karta konfigurace partnerského vztahu](../media/setup-direct-conf-tab-final.png)

1. Všimněte si, že portál spouští základní ověření informací, které jste zadali. Pás karet v horní části zobrazuje zprávu *s finálním ověřením...*.

    > [!div class="mx-imgBorder"]
    > ![Karta ověření partnerského vztahu](../media/setup-direct-review-tab-validation.png)

1. Po změně zprávy na *prošlé ověření* ověřte své informace. Žádost odešlete tak, že vyberete **vytvořit**. Pokud chcete žádost upravit, vyberte **Předchozí** a opakujte postup.

    > [!div class="mx-imgBorder"]
    > ![Odesílání partnerských vztahů](../media/setup-direct-review-tab-submit.png)

1. Po odeslání žádosti počkejte, než se nasazení dokončí. Pokud nasazení neproběhne úspěšně, kontaktujte [partnerský vztah Microsoftu](mailto:peering@microsoft.com). Úspěšné nasazení se zobrazí, jak je znázorněno zde.

    > [!div class="mx-imgBorder"]
    > ![Úspěšné vytvoření partnerského vztahu](../media/setup-direct-success.png)
