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
ms.openlocfilehash: 000971878e24c46892aaef1fa0c65237a4219883
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678531"
---
Po úspěšném nasazení prostředku **partnerského vztahu** jej můžete zobrazit podle následujících kroků.

1. Přejděte do **skupin prostředků**a vyberte skupinu prostředků, kterou jste vybrali při vytváření prostředku **partnerského vztahu.** Pokud máte příliš mnoho skupin prostředků, použijte pole **Filtr.**

    > [!div class="mx-imgBorder"]
    > ![Skupiny prostředků](../media/setup-direct-get-resourcegroup.png)

1. Vyberte prostředek **partnerského vztahu,** který jste vytvořili.

    > [!div class="mx-imgBorder"]
    > ![Zobrazení prostředku partnerského vztahu](../media/setup-direct-get-open.png)

1. Stránka **Přehled** zobrazuje informace na vysoké úrovni, jak je znázorněno zde.

    > [!div class="mx-imgBorder"]
    > ![Podokno Přehled zdrojů partnerského vztahu](../media/setup-exchange-get-overview.png)

1. Vlevo vyberte **informace ASN,** chcete-li zobrazit informace odeslané při vytvoření peerasn.

    > [!div class="mx-imgBorder"]
    > ![Informace asn zdroje partnerského vztahu](../media/setup-direct-get-asninfo.png)

1. Vlevo vyberte **Možnost Připojení**. V horní části obrazovky se zobrazí souhrn partnerských připojení mezi asn a microsoft, v různých zařízeních v rámci metra. K souhrnu připojení můžete přistupovat také ze stránky **Přehled** tak, že v prostředním podokně vyberete **možnost Připojení,** jak je znázorněno.

    > [!div class="mx-imgBorder"]
    > ![Připojení prostředků partnerského vztahu](../media/setup-exchange-get-connectionssummary.png)

    * **Stav připojení** odpovídá stavu nastavení připojení partnerského vztahu. Stavy zobrazené v tomto poli postupujte podle diagramu stavu zobrazeného v [návodu partnerského vztahu serveru Exchange](../walkthrough-exchange-all.md).
    * **Stav relace Protokolu IPv4** a **Stav relace IPv6** odpovídají stavům relace Protokolu BGP protokolu IPv4 a IPv6.  
    * Když vyberete řádek v horní části obrazovky, v části **Připojení** v dolní části se zobrazí podrobnosti pro každé připojení. Výběrem šipek rozbalte **položku Konfigurace**, **Adresa IPv4**a **Adresa IPv6**.

    > [!div class="mx-imgBorder"]
    > ![Zobrazení prostředku partnerského vztahu](../media/setup-exchange-get-connectionsipv4.png)
