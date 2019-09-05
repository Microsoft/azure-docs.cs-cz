---
title: Spravovat IoT Central z Azure Portal | Microsoft Docs
description: Umožňuje spravovat IoT Central z Azure Portal.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: d4d2a843248985c4fe17bf5401b261862d71fecf
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383032"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Správa IoT Central z Azure Portal

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Místo vytváření a správy aplikací IoT Central na webu [Azure IoT Central Správce aplikací](https://aka.ms/iotcentral) můžete ke správě aplikací použít [Azure Portal](https://portal.azure.com) .

## <a name="create-iot-central-applications"></a>Vytváření IoT Centralch aplikací

Chcete-li vytvořit aplikaci, přejděte do [Azure Portal](https://ms.portal.azure.com) a v hlavní navigační nabídce vlevo vyberte **vytvořit prostředek** .

![Portál pro správu: navigační nabídka](media/howto-manage-iot-central-from-portal/image0.png)

Do panelu vyhledávání zadejte **IoT Central**.

![Portál pro správu: hledání](media/howto-manage-iot-central-from-portal/image0a1.png)

Ve výsledcích hledání vyberte položku řádek **aplikace IoT Central** .

![Portál pro správu: výsledky hledání](media/howto-manage-iot-central-from-portal/image0b1.png)

Nyní vyberte **vytvořit**.

![Portál pro správu: Prostředek IoT Central](media/howto-manage-iot-central-from-portal/image0c1.png)

Vyplňte všechna pole ve formuláři. Tento formulář je podobný formuláři, který vyplníte, abyste mohli vytvářet aplikace na webu [Správce aplikací Azure IoT Central](https://aka.ms/iotcentral) . Další informace najdete v tématu rychlý Start [k vytvoření IoT Central aplikace](quick-deploy-iot-central.md) .

> [!NOTE]
> Šablona **aplikace Preview** je aktuálně dostupná pouze v oblastech **Severní Evropa** a **střed USA** .

![Portál pro správu: vytvoření prostředku IoT Central](media/howto-manage-iot-central-from-portal/image1a.png)  

Po vyplnění všech polí vyberte **vytvořit**.

## <a name="manage-existing-iot-central-applications"></a>Spravovat existující aplikace IoT Central

Pokud už máte aplikaci Azure IoT Central, můžete ji odstranit nebo ji přesunout do jiného předplatného nebo skupiny prostředků v Azure Portal.

> [!NOTE]
> V Azure Portal neuvidíte zkušební aplikace, protože nejsou přidružené k vašemu předplatnému.

Začněte tím, že vyberete **všechny prostředky** v hlavní navigační nabídce vlevo. Pomocí vyhledávacího pole zadejte název vaší aplikace, abyste ho našli v seznamu prostředků. Pak vyberte aplikaci IoT Central, kterou chcete spravovat.

![Portál pro správu: Správa prostředků](media/howto-manage-iot-central-from-portal/image2a.png)

Chcete-li přejít k aplikaci, vyberte adresu URL aplikace IoT Central.

![Portál pro správu: Správa prostředků](media/howto-manage-iot-central-from-portal/image3.png)

Pokud chcete aplikaci přesunout do jiné skupiny prostředků, vyberte u skupiny prostředků možnost **změnit** . Na stránce **přesunout prostředky** vyberte skupinu prostředků, do které chcete tuto aplikaci migrovat.

![Portál pro správu: Správa prostředků](media/howto-manage-iot-central-from-portal/image4a.png)

Pokud chcete aplikaci přesunout na jiné předplatné, vyberte odkaz **změnit** vedle předplatného. V zobrazeném dialogovém okně vyberte předplatné, na které chcete tuto aplikaci migrovat.

![Portál pro správu: Správa prostředků](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak spravovat aplikace IoT Central Azure z Azure Portal, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Správa aplikace](howto-administer.md)