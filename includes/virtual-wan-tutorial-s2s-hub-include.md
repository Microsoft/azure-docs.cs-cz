---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3bd9489adaf46e604393fc7059d37443bdd5ec3e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73489020"
---
1. Vyhledejte virtuální wan, který jste vytvořili. Na stránce Virtual WAN vyberte v části **Připojení** **centra**.
2. Na stránce Rozbočovače vyberte **+Nové centrum,** abyste otevřeli stránku **Vytvořit virtuální rozbočovač.**

    ![Základy](./media/virtual-wan-tutorial-hub-include/basics.png "Základy")
3. Na kartě **Základy** virtuálního **rozbočovače** vyplňte následující pole:

    **Podrobnosti o projektu**

   * Oblast (dříve označovaná jako umístění)
   * Name (Název)
   * Centrální soukromý adresní prostor. Minimální adresní prostor je /24 k vytvoření rozbočovače, což znamená, že rozsah čehokoli v rozsahu od /25 do /32 způsobí chybu během vytváření.
4. Vyberte **další: Site-to-site**.

    ![Site-to-site](./media/virtual-wan-tutorial-hub-include/site-to-site.png "Site-to-Site")

5. Na kartě **Site-to-site** vyplňte následující pole:

   * Chcete-li vytvořit síť VPN mezi lokalitami, vyberte **možnost Ano.**
   * Pole Číslo AS nelze v tomto okamžiku upravovat ve virtuálním rozbočovači.
   * V rozevíracím souboru vyberte hodnotu **jednotek měřítka brány.** Škálovací jednotka umožňuje vybrat agregační propustnost brány VPN vytvářené ve virtuálním rozbočovači pro připojení webů. Pokud vyberete 1 měřítko jednotky = 500 Mbps, znamená to, že budou vytvořeny dvě instance pro redundanci, z nichž každá má maximální propustnost 500 Mbps. Například, pokud jste měli pět větví, z nichž každá dělá 10 Mbps na větvi, budete potřebovat agregaci 50 Mbps na konci hlavy. Plánování agregované kapacity brány Azure VPN by mělo být provedeno po posouzení kapacity potřebné pro podporu počtu větví do centra.
6. Vyberte **Zkontrolovat + Vytvořit,** abyste ověřili.
7. Chcete-li vytvořit rozbočovač, vyberte **Vytvořit.** Po 30 minutách **se aktualizujte** a zobrazte centrum na stránce **Rozbočovače.** Chcete-li **přejít na zdroj,** vyberte možnost Přejít na zdroj.