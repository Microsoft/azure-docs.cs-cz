---
title: Spravovat IoT Central z Azure Portal | Microsoft Docs
description: Tento článek popisuje, jak vytvořit a spravovat vaše aplikace IoT Central z Azure Portal.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/02/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 1e206d9a1b14fc8ff1b92e4eb7217824d81212cf
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990105"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Správa IoT Central z Azure Portal

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Místo vytváření a správy aplikací IoT Central na webu [Azure IoT Central Správce aplikací](https://aka.ms/iotcentral) můžete ke správě aplikací použít [Azure Portal](https://portal.azure.com) .

## <a name="create-iot-central-applications"></a>Vytváření IoT Centralch aplikací

Pokud chcete vytvořit aplikaci, přejděte do [Azure Portal](https://ms.portal.azure.com) a v hlavním podokně vlevo vyberte **vytvořit prostředek** .

![Portál pro správu: navigační nabídka](media/howto-manage-iot-central-from-portal/image0.png)

Do panelu vyhledávání zadejte **IoT Central**.

![Portál pro správu: hledání](media/howto-manage-iot-central-from-portal/image0a1.png)

Ve výsledcích hledání vyberte položku řádek **aplikace IoT Central** .

![Portál pro správu: výsledky hledání](media/howto-manage-iot-central-from-portal/image0b1.png)

Nyní vyberte **vytvořit**.

![Portál pro správu: IoT Central prostředek](media/howto-manage-iot-central-from-portal/image0c1.png)

Vyplňte všechna pole ve formuláři. Tento formulář je podobný formuláři, který vyplníte, abyste mohli vytvářet aplikace na webu [Správce aplikací Azure IoT Central](https://aka.ms/iotcentral) . Další informace najdete v tématu rychlý Start [k vytvoření IoT Central aplikace](quick-deploy-iot-central.md) .

Můžete vytvořit aplikaci IoT Central s všeobecně dostupnými funkcemi, a to tak, že vyberete **starší aplikaci** jako šablonu aplikace, všechny ostatní šablony aplikací budou používat funkce veřejné verze Preview.

![vytvořit formulář IoT Central](media/howto-manage-iot-central-from-portal/image6a.png)

**Umístění** je [geografická](https://azure.microsoft.com/global-infrastructure/geographies/) oblast, kde byste chtěli vytvořit aplikaci. Obvykle byste měli zvolit umístění, které je fyzicky nejbližší pro vaše zařízení, aby se dosáhlo optimálního výkonu. Služba Azure IoT Central je aktuálně dostupná v **USA**, **Austrálii**, **Asie a Tichomoří**nebo v **Evropě**.  Jakmile zvolíte umístění, nemůžete později přesunout aplikaci do jiného umístění.

> [!NOTE]
> Šablony aplikace ve verzi Preview jsou v tuto chvíli dostupné jenom v oblastech **Evropa** a **USA** .


Po vyplnění všech polí vyberte **vytvořit**.

## <a name="manage-existing-iot-central-applications"></a>Spravovat existující aplikace IoT Central

Pokud už máte aplikaci Azure IoT Central, můžete ji odstranit nebo ji přesunout do jiného předplatného nebo skupiny prostředků v Azure Portal.

> [!NOTE]
> V Azure Portal neuvidíte aplikace vytvořené v cenovém plánu zdarma, protože nejsou přidružené k vašemu předplatnému.

Začněte tím, že vyberete **všechny prostředky** v hlavním podokně vlevo. Pomocí vyhledávacího pole zadejte název vaší aplikace, abyste ho našli v seznamu prostředků. Pak vyberte aplikaci IoT Central, kterou chcete spravovat.

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