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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774546"
---
Po úspěšném nasazení **partnerského vztahu** se dá prostředek zobrazit podle následujících kroků.

1. Přejděte do **skupiny prostředků** a klikněte na skupinu prostředků, kterou jste vybrali při vytváření prostředku **partnerského vztahu** . Pokud máte příliš mnoho skupin prostředků, můžete použít pole *Filter* .

    > [!div class="mx-imgBorder"]
    > Skupina prostředků partnerského vztahu ![](../media/setup-direct-get-resourcegroup.png)

1. Klikněte na prostředek **partnerského vztahu** , který jste vytvořili.

    > [!div class="mx-imgBorder"]
    > ![zobrazení prostředků partnerského vztahu](../media/setup-direct-get-open.png)

1. Stránka **Přehled** zobrazuje informace vysoké úrovně. Podívejte se na informace zvýrazněné níže.

    > [!div class="mx-imgBorder"]
    > ![zobrazení prostředků partnerského vztahu](../media/setup-exchange-get-overview.png)

1. Na levé straně kliknutím na **informace o ASN** zobrazíte informace odeslané při vytváření PeerAsn.

    > [!div class="mx-imgBorder"]
    > ![zobrazení prostředků partnerského vztahu](../media/setup-direct-get-asninfo.png)

1. Na levé straně klikněte na **připojení**. Podívejte se na souhrnný přehled propojení partnerských vztahů mezi ASN a Microsoftem v různých zařízeních v rámci Metro. Kliknutím na **připojení** v prostředním podokně, jak je zvýrazněno výše, můžete také přijít na stránku Souhrn připojení ze stránky **Přehled** .

    > [!div class="mx-imgBorder"]
    > ![zobrazení prostředků partnerského vztahu](../media/setup-exchange-get-connectionssummary.png)

    * **Stav připojení** odpovídá stavu nastaveného připojení partnerského vztahu. Stavy zobrazené v tomto poli následují po diagramu stavu zobrazeném v Průvodci vytvořením [partnerského vztahu Exchange](../walkthrough-exchange-all.md) .
    * Stav **relace IPv4** a **stav relace protokolu IPv6** odpovídají stavům relace protokolu BGP IPv4 a IPv6 v uvedeném pořadí.  
    * Když vyberete řádek nahoře, v části ***připojení*** v dolní části se zobrazí podrobnosti o každém připojení. Kliknutím na šipky můžete rozbalit ***konfiguraci***dílčích částí, ***adresu IPv4*** a ***adresu IPv6*** .

    > [!div class="mx-imgBorder"]
    > ![zobrazení prostředků partnerského vztahu](../media/setup-exchange-get-connectionsipv4.png)
