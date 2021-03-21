---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/04/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f469664c716ecef6b82de2befa40b33f253e229f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627690"
---
1. Vyhledejte virtuální síť WAN, kterou jste vytvořili. Na stránce virtuální síť WAN v části **připojení** vyberte **rozbočovače**.
2. Na stránce **centra** vyberte **+ nové centrum** a otevřete stránku **vytvořit virtuální rozbočovač** .

    ![Snímek obrazovky se zobrazí v podokně vytvořit virtuální centrum se zvolenou kartou základy.](./media/virtual-wan-tutorial-hub-include/basics.png "Základy")
3. Na kartě **základy** stránky **vytvořit virtuální rozbočovač** vyplňte následující pole:

   * **Oblast** (dříve označovaná jako umístění)
   * **Název**
   * **Privátní adresní prostor centra** – minimální adresní prostor je/24 pro vytvoření centra. Pokud použijete cokoli v rozsahu od/25 do/32, během vytváření se vytvoří chyba. Nemusíte explicitně naplánovat adresní prostor podsítě pro služby ve virtuálním centru. Vzhledem k tomu, že Azure Virtual WAN je spravovaná služba, vytvoří příslušné podsítě ve virtuálním rozbočovači pro různé brány nebo služby (například brány VPN, brány ExpressRoute, brány pro uživatele VPN Point-to-site, brány firewall, směrování atd.).
4. Vyberte **Další: Site-to-site**.

    ![Snímek obrazovky zobrazuje podokno vytvořit virtuální rozbočovač s vybraným webem na webu.](./media/virtual-wan-tutorial-hub-include/site-to-site.png "Site-to-Site")

5. Na kartě **site-to-site** vyplňte následující pole:

   * Vyberte **Ano** , pokud chcete vytvořit síť VPN typu Site-to-site.
   * Pole AS Number nelze upravovat.
   * V rozevíracím seznamu vyberte hodnotu **jednotka škálování brány** . Jednotka škálování umožňuje vybrat agregovanou propustnost brány sítě VPN, která se vytváří ve virtuálním rozbočovači pro připojení lokalit k. Pokud vyberete 1 jednotku škálování = 500 MB/s, znamená to, že se vytvoří dvě instance pro redundanci, z nichž každá má maximální propustnost 500 MB/s. Pokud byste například měli pět větví, z nichž každý bude mít 10 MB/s na větvi, budete potřebovat agregaci 50 MB/s na konci hlavního umístění. Plánování agregované kapacity Azure VPN Gateway by se mělo provést po vyhodnocení kapacity potřebné k podpoře počtu větví do centra.
6. Vyberte **zkontrolovat + vytvořit** k ověření.
7. Vyberte **vytvořit** a vytvořte tak centrum. Po 30 minutách **aktualizujte** zobrazení centra na stránce **centra** . Vyberte **Přejít k prostředku** a přejděte k prostředku.
