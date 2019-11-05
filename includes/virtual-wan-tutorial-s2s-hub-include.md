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
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489020"
---
1. Vyhledejte virtuální síť WAN, kterou jste vytvořili. Na stránce virtuální síť WAN v části **připojení** vyberte **rozbočovače**.
2. Na stránce centra vyberte **+ nové centrum** a otevřete stránku **vytvořit virtuální rozbočovač** .

    ![Základy](./media/virtual-wan-tutorial-hub-include/basics.png "Základy")
3. Na kartě **základy** stránky **vytvořit virtuální rozbočovač** vyplňte následující pole:

    **Podrobnosti o projektu**

   * Oblast (dříve označovaná jako umístění)
   * Name (Název)
   * Privátní adresní prostor centra Minimální adresní prostor je/24 pro vytvoření centra, což znamená, že při vytváření dojde k chybě z rozsahu od/25 do/32.
4. Vyberte **Další: Site-to-site**.

    ![Site-to-site](./media/virtual-wan-tutorial-hub-include/site-to-site.png "Site-to-Site")

5. Na kartě **site-to-site** vyplňte následující pole:

   * Vyberte **Ano** , pokud chcete vytvořit síť VPN typu Site-to-site.
   * V současné době není možné ve virtuálním rozbočovači upravovat pole AS Number.
   * V rozevíracím seznamu vyberte hodnotu **jednotka škálování brány** . Jednotka škálování umožňuje vybrat agregovanou propustnost brány sítě VPN, která se vytváří ve virtuálním rozbočovači pro připojení lokalit k. Pokud vyberete 1 jednotku škálování = 500 MB/s, znamená to, že se vytvoří dvě instance pro redundanci, z nichž každá má maximální propustnost 500 MB/s. Pokud byste například měli pět větví, z nichž každý bude mít 10 MB/s na větvi, budete potřebovat agregaci 50 MB/s na konci hlavního umístění. Plánování agregované kapacity Azure VPN Gateway by se mělo provést po vyhodnocení kapacity potřebné k podpoře počtu větví do centra.
6. Vyberte **zkontrolovat + vytvořit** k ověření.
7. Vyberte **vytvořit** a vytvořte tak centrum. Po 30 minutách **aktualizujte** zobrazení centra na stránce **centra** . Vyberte **Přejít k prostředku** a přejděte k prostředku.