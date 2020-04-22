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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681081"
---
1. Na stránce **Vytvořit partnerský vztah vyplňte** na kartě **Konfigurace** pole, jak je znázorněno zde.

    * V **pole Typ partnerského vztahu**vyberte možnost **Přímé**.
    * V **síti Microsoft**vyberte možnost **AS8075**. Nevytvářejte partnerský vztah s ASN 8069. Je vyhrazenpro speciální aplikace a používá jej pouze [partnerský vztah microsoftu](mailto:peering@microsoft.com).
    * Vyberte **skladovou položku** jako **základní volnou položku**. Nevybírejte Premium Free, protože je vyhrazenpro speciální aplikace.
    * Vyberte umístění **Metro,** kde chcete nastavit partnerský vztah.

        > [!NOTE]
        > Pokud už máte připojení partnerského vztahu s Microsoftem ve vybraném umístění **Metro** a používáte portál Azure poprvé k nastavení partnerského vztahu v tomto umístění, vaše stávající připojení partnerského vztahu budou uvedeny v části **Připojení partnerského vztahu,** jak je znázorněno. Microsoft automaticky převede tato připojení partnerského vztahu na prostředek Azure, takže je můžete spravovat všechny, spolu s novými připojeními, na jednom místě. Další informace najdete [v tématu Převod staršího přímého partnerského vztahu na prostředek Azure pomocí portálu](../howto-legacy-direct-portal.md).
        >

1. V části **Připojení partnerského vztahu**vyberte **Vytvořit nový** a přidejte řádek pro každé nové připojení, které chcete nastavit.

    * Chcete-li konfigurovat nebo upravit nastavení připojení, vyberte tlačítko pro úpravy linky.

        > [!div class="mx-imgBorder"]
        > ![Tlačítko Upravit](../media/setup-direct-conf-tab-edit.png)
    
    * Chcete-li řádek odstranit, vyberte **...**  >  **Odstranit**.

        > [!div class="mx-imgBorder"]
        > ![Tlačítko Odstranit](../media/setup-direct-conf-tab-delete.png)

    * Musíte zadat všechna nastavení pro připojení, jak je znázorněno zde.

         > [!div class="mx-imgBorder"]
         > ![Stránka Přímé připojení partnerského vztahu](../media/setup-direct-conf-tab-connection.png)

        1. Vyberte **zařízení partnerského vztahu,** ve kterém je potřeba připojení nastavit.
        1. **Zprostředkovatel adresy relace** se používá k určení, kdo poskytuje podsíť potřebnou k nastavení relace protokolu BGP mezi sítí a společností Microsoft. Pokud můžete podsíť poskytnout, vyberte **možnost Peer**. V opačném případě vás budete kontaktovat vyberte partnerský vztah **microsoftu** a [partnerský vztah Microsoftu.](mailto:peering@microsoft.com) Výběr této možnosti bude společnosti Microsoft trvat déle, než zpracuje požadavek partnerského vztahu. V některých případech nemusí být společnost Microsoft schopna poskytnout podsítě, což bude mít za následek odmítnutí požadavku.
        1. Pokud jste jako **peer**vybrali možnost **Zprostředkovatel elazírové adresy** , zadejte adresy IPv4 a IPv6 spolu s maskami předpony v polích **předpony IPv4 relace** a **předpony IPv6** relace.
        1. Zadejte počet předpon IPv4 a IPv6, které budete inzerovat, do polí **Maximální inzerované adresy IPv4** a **Maximální inzerované adresy IPv6.**
        1. Nastavte jezdec **Celková šířka pásma** tak, aby odrážel šířku pásma pro připojení.
        1. Vyberte **Uložit,** chcete-li uložit nastavení připojení.

1. Opakováním předchozího kroku přidáte další připojení v libovolném zařízení, kde je společnost Microsoft umístěna společně s vaší sítí, v rámci **zařízení Metro,** které jste vybrali dříve.

1. Po přidání všech požadovaných připojení vyberte **Zkontrolovat + vytvořit**.

    > [!div class="mx-imgBorder"]
    > ![Karta Konfigurace partnerského vztahu je konečná](../media/setup-direct-conf-tab-final.png)

1. Všimněte si, že portál spustí základní ověření zadaných informací. Pás karet v horní části zobrazuje zprávu *Spuštění konečného ověření...*.

    > [!div class="mx-imgBorder"]
    > ![Karta Ověření partnerského vztahu](../media/setup-direct-review-tab-validation.png)

1. Po schválení zprávy na *ověření*ověřte své informace. Odešlete žádost výběrem **možnosti Vytvořit**. Chcete-li upravit požadavek, vyberte **Předchozí** a opakujte kroky.

    > [!div class="mx-imgBorder"]
    > ![Odeslání partnerského vztahu](../media/setup-direct-review-tab-submit.png)

1. Po odeslání požadavku počkejte na dokončení nasazení. Pokud se nasazení nezdaří, obraťte se na [partnerský vztah společnosti Microsoft](mailto:peering@microsoft.com). Úspěšné nasazení se zobrazí, jak je znázorněno zde.

    > [!div class="mx-imgBorder"]
    > ![Úspěch peeringu](../media/setup-direct-success.png)
