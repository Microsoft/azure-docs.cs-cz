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
ms.openlocfilehash: b967c844ab145074490e931122cbe092d67de0c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774546"
---
Po úspěšném nasazení prostředku **partnerského vztahu** jej můžete zobrazit podle následujících kroků.

1. Přejděte do **skupin y prostředků** a klikněte na vybranou skupinu prostředků při vytváření prostředku **partnerského vztahu.** Pole *Filtr* můžete použít, pokud máte příliš mnoho skupin zdrojů.

    > [!div class="mx-imgBorder"]
    > ![Skupina prostředků partnerského vztahu](../media/setup-direct-get-resourcegroup.png)

1. Klikněte na prostředek **partnerského vztahu,** který jste vytvořili.

    > [!div class="mx-imgBorder"]
    > ![Zobrazení prostředku partnerského vztahu](../media/setup-direct-get-open.png)

1. Stránka **Přehled** zobrazuje informace na vysoké úrovni. Dodržujte níže zvýrazněné informace.

    > [!div class="mx-imgBorder"]
    > ![Zobrazení prostředku partnerského vztahu](../media/setup-exchange-get-overview.png)

1. Na levé straně klikněte na **informace ASN** pro zobrazení odeslaných informací při vytváření PeerAsn

    > [!div class="mx-imgBorder"]
    > ![Zobrazení prostředku partnerského vztahu](../media/setup-direct-get-asninfo.png)

1. Vlevo klikněte na **Připojení**. V horní části sledujte souhrn partnerských kontaktů mezi asn a microsoftem v různých zařízeních v rámci metra. Můžete také dospět k souhrnu připojení ze stránky **Přehled** kliknutím na **Připojení** v prostředním podokně, jak je zvýrazněno výše.

    > [!div class="mx-imgBorder"]
    > ![Zobrazení prostředku partnerského vztahu](../media/setup-exchange-get-connectionssummary.png)

    * **Stav připojení** odpovídá stavu nastavení připojení partnerského vztahu. Stavy zobrazené v tomto poli postupujte podle diagramu stavu zobrazeného v [návodu partnerského vztahu exchange](../walkthrough-exchange-all.md)
    * **Stav relace Protokolu IPv4** a **Stav relace IPv6** odpovídají stavům relace Protokolu BGP protokolu IPv4 a IPv6.  
    * Když vyberete řádek v horní části, část ***Připojení*** v dolní části zobrazí podrobnosti pro každé připojení. Kliknutím na značky se šipkami rozbalíte ***pododdíly Konfigurace***, ***Adresa IPv4*** a ***Adresa IPv6***

    > [!div class="mx-imgBorder"]
    > ![Zobrazení prostředku partnerského vztahu](../media/setup-exchange-get-connectionsipv4.png)
