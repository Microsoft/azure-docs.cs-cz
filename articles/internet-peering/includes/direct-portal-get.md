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
ms.openlocfilehash: 3507aacc68de25f7368cbe3cda917077564c56eb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96356240"
---
1. V části **skupiny prostředků** vyberte skupinu prostředků, kterou jste vybrali při vytváření prostředku **partnerského vztahu** . Pokud máte příliš mnoho skupin prostředků, použijte pole **filtru** .

    > [!div class="mx-imgBorder"]
    > ![Skupiny prostředků](../media/setup-direct-get-resourcegroup.png)

1. Vyberte prostředek **partnerského vztahu** , který jste vytvořili.

    > [!div class="mx-imgBorder"]
    > ![V levém podokně se vybere stránka přehled. Zobrazuje informace o PeeringResourceGroup. V seznamu partnerských vztahů se AshburnPeering zvýrazní.](../media/setup-direct-get-open.png)

1. Stránka **Přehled** zobrazuje informace vysoké úrovně, jak je znázorněno zde.

    > [!div class="mx-imgBorder"]
    > ![Podokno s přehledem partnerského vztahu prostředků](../media/setup-direct-get-overview.png)

1. Na levé straně vyberte **informace o ASN** pro zobrazení informací, které jste odeslali při vytváření PeerAsn.

    > [!div class="mx-imgBorder"]
    > ![Informace o partnerském vztahu prostředků ASN](../media/setup-direct-get-asninfo.png)

1. Na levé straně vyberte **připojení**. V horní části obrazovky se zobrazí souhrn partnerských připojení mezi číslem ASN a společností Microsoft v různých zařízeních ve službě Metro. K souhrnu připojení můžete také získat přístup ze stránky **Přehled** výběrem možnosti **připojení** ve středovém podokně, jak je znázorněno na obrázku.

    > [!div class="mx-imgBorder"]
    > ![Připojení prostředků partnerského vztahu](../media/setup-direct-get-connectionssummary.png)

    * **Stav připojení** odpovídá stavu nastavení připojení partnerských vztahů. Stavy zobrazené v tomto poli následují po diagramu stavu zobrazeném v [Průvodci přímým partnerským vztahem](../walkthrough-direct-all.md).
    * Stav **relace protokolu IPv4** a **stav relace protokolu IPv6** odpovídají stavům relace protokolu BGP IPv4 a IPv6 (v uvedeném pořadí). 
    * Když vyberete řádek v horní části obrazovky, v části **připojení** v dolní části se zobrazí podrobnosti o každém připojení. Vyberte šipky a rozbalte položku **Konfigurace**, **adresa IPv4** a **adresa IPv6**.
