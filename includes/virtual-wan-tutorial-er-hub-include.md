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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73491617"
---
1. Vyhledejte virtuální wan, který jste vytvořili. Na stránce Virtual WAN vyberte v části **Připojení** **centra**.
2. Na stránce Rozbočovače vyberte **+Nové centrum,** abyste otevřeli stránku **Vytvořit virtuální rozbočovač.**
3. Na kartě **Základy** virtuálního **rozbočovače** vyplňte následující pole:

   ![Základy](./media/virtual-wan-tutorial-er-hub-include/hub1.png "Základy")

    **Podrobnosti o projektu**

   * Oblast (dříve označovaná jako umístění)
   * Name (Název)
   * Centrální soukromý adresní prostor. Minimální adresní prostor je /24 k vytvoření rozbočovače, což znamená, že rozsah čehokoli v rozsahu od /25 do /32 způsobí chybu během vytváření.
4. Vyberte **kartu ExpressRoute**.

5. Na kartě **ExpressRoute** vyplňte následující pole:

   ![ExpressRoute](./media/virtual-wan-tutorial-er-hub-include/hub2.png "ExpressRoute")

   * Chcete-li vytvořit bránu **ExpressRoute,** vyberte **ano.**
   * V rozevíracím souboru vyberte hodnotu **jednotek měřítka brány.**
6. Vyberte **Zkontrolovat + Vytvořit,** abyste ověřili.
7. Chcete-li vytvořit rozbočovač, vyberte **Vytvořit.** Po 30 minutách **se aktualizujte** a zobrazte centrum na stránce **Rozbočovače.** Chcete-li **přejít na zdroj,** vyberte možnost Přejít na zdroj.