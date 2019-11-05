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
ms.openlocfilehash: 6f910dbe91ed8e1cb65eefa6dfc48c72a689bf25
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491617"
---
1. Vyhledejte virtuální síť WAN, kterou jste vytvořili. Na stránce virtuální síť WAN v části **připojení** vyberte **rozbočovače**.
2. Na stránce centra vyberte **+ nové centrum** a otevřete stránku **vytvořit virtuální rozbočovač** .
3. Na kartě **základy** stránky **vytvořit virtuální rozbočovač** vyplňte následující pole:

   ![Základy](./media/virtual-wan-tutorial-er-hub-include/hub1.png "Základy")

    **Podrobnosti o projektu**

   * Oblast (dříve označovaná jako umístění)
   * Name (Název)
   * Privátní adresní prostor centra Minimální adresní prostor je/24 pro vytvoření centra, což znamená, že při vytváření dojde k chybě z rozsahu od/25 do/32.
4. Vyberte **kartu ExpressRoute**.

5. Na kartě **ExpressRoute** vyplňte následující pole:

   ![ExpressRoute](./media/virtual-wan-tutorial-er-hub-include/hub2.png "ExpressRoute")

   * Vyberte **Ano** , pokud chcete vytvořit bránu **ExpressRoute** .
   * V rozevíracím seznamu vyberte hodnotu **jednotka škálování brány** .
6. Vyberte **zkontrolovat + vytvořit** k ověření.
7. Vyberte **vytvořit** a vytvořte tak centrum. Po 30 minutách **aktualizujte** zobrazení centra na stránce **centra** . Vyberte **Přejít k prostředku** a přejděte k prostředku.