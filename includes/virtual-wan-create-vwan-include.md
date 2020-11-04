---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/09/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a5999c012ea970a7e05c66c9d4f8650261f01168
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93331020"
---
V prohlížeči přejděte na web Azure Portal a přihlaste se pomocí svého účtu Azure.

1. Přejděte na stránku Virtual WAN. Na portálu klikněte na **+ Vytvořit prostředek**. Do vyhledávacího pole zadejte **virtuální síť WAN** a vyberte Enter.
1. Z výsledků vyberte **virtuální síť WAN** . Na stránce virtuální síť WAN vyberte **vytvořit** a otevřete stránku vytvořit síť WAN.
1. Na stránce **vytvořit síť WAN** na kartě **základy** vyplňte následující pole:

   :::image type="content" source="./media/virtual-wan-create-vwan-include/basics.png" alt-text="Snímek obrazovky se zobrazí v podokně vytvořit síť WAN se zvolenou kartou základy.":::

   * **Subscription** (Předplatné) – vyberte předplatné, které chcete použít.
   * **Skupina prostředků** – vytvořte nové nebo použijte existující.
   * **Umístění skupiny prostředků** – vyberte umístění prostředku z rozevíracího seznamu. Síť WAN je globální prostředek, takže se nenachází v určité oblasti. Přesto je ale potřeba oblast vybrat, abyste mohli snáz spravovat a vyhledávat prostředek sítě WAN, který vytvoříte.
   * **Název** – zadejte název, který chcete zavolat do sítě WAN.
   * **Typ** – Basic nebo Standard. Vyberte **Standard**. Pokud vyberete základní VWAN, je třeba pochopit, že základní VWANs může obsahovat jenom základní rozbočovače, které omezují typ připojení typu Site-to-site.
1. Po dokončení vyplňování polí vyberte **zkontrolovat + vytvořit**.
1. Po úspěšném ověření vyberte **vytvořit** a vytvořte virtuální síť WAN.
