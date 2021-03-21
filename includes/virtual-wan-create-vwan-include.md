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
ms.openlocfilehash: 5915830e4521399ad322dd4a6f3926428d811455
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94942503"
---
V prohlížeči přejděte na web [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

1. Na portálu vyberte **+ vytvořit prostředek**. Do vyhledávacího pole zadejte **virtuální síť WAN** a vyberte **ENTER**.
1. Z výsledků vyberte **virtuální síť WAN** . Na stránce virtuální síť WAN vyberte **vytvořit** a otevřete stránku vytvořit síť WAN.
1. Na stránce **vytvořit síť WAN** na kartě **základy** vyplňte následující pole:

   :::image type="content" source="./media/virtual-wan-create-vwan-include/basics.png" alt-text="Snímek obrazovky se zobrazí v podokně vytvořit síť WAN se zvolenou kartou základy.":::

   * **Subscription** (Předplatné) – vyberte předplatné, které chcete použít.
   * **Skupina prostředků** – vytvořte nové nebo použijte existující.
   * **Umístění skupiny prostředků** – vyberte umístění prostředku z rozevíracího seznamu. Síť WAN je globální prostředek, takže se nenachází v určité oblasti. Je však nutné vybrat oblast, aby bylo možné spravovat a vyhledat prostředek sítě WAN, který vytvoříte.
   * **Název** – zadejte název, který chcete zavolat do sítě WAN.
   * **Typ** – Basic nebo Standard. Vyberte **Standard**. Pokud vyberete základní VWAN, je třeba pochopit, že základní VWANs může obsahovat jenom základní rozbočovače, které omezují typ připojení typu Site-to-site.
1. Po dokončení vyplňování polí vyberte **zkontrolovat + vytvořit**.
1. Po úspěšném ověření vyberte **vytvořit** a vytvořte virtuální síť WAN.
