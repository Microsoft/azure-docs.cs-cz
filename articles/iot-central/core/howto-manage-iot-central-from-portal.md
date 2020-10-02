---
title: Spravovat IoT Central z Azure Portal | Microsoft Docs
description: Tento článek popisuje, jak vytvořit a spravovat vaše aplikace IoT Central z Azure Portal.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: 0802169898cad51e9a5d208e546a004f7ea7b3c0
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653328"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Správa IoT Central z Azure Portal

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Místo vytváření a správy aplikací IoT Central na webu [Azure IoT Central Správce aplikací](https://aka.ms/iotcentral) můžete ke správě aplikací použít [Azure Portal](https://portal.azure.com) .

## <a name="create-iot-central-applications"></a>Vytváření IoT Centralch aplikací

Chcete-li vytvořit aplikaci, přejděte do [Azure Portal](https://ms.portal.azure.com) a vyberte možnost **vytvořit prostředek**.

V **části Hledat na panelu Marketplace** zadejte *IoT Central*:

![Portál pro správu: hledání](media/howto-manage-iot-central-from-portal/image0a1.png)

Ve výsledcích hledání vyberte dlaždici **IoT Central aplikace** :

![Portál pro správu: výsledky hledání](media/howto-manage-iot-central-from-portal/image0b1.png)

Nyní vyberte **vytvořit**:

![Portál pro správu: IoT Central prostředek](media/howto-manage-iot-central-from-portal/image0c1.png)

Vyplňte všechna pole ve formuláři. Tento formulář je podobný formuláři, který vyplníte, abyste mohli vytvářet aplikace na webu [Správce aplikací Azure IoT Central](https://aka.ms/iotcentral) . Další informace najdete v tématu rychlý Start [k vytvoření IoT Central aplikace](quick-deploy-iot-central.md) .

![Vytvořit formulář IoT Central](media/howto-manage-iot-central-from-portal/image6a.png)

**Umístění** je [geografická](https://azure.microsoft.com/global-infrastructure/geographies/) oblast, kde byste chtěli vytvořit aplikaci. Obvykle byste měli zvolit umístění, které je fyzicky nejbližší pro vaše zařízení, aby se dosáhlo optimálního výkonu. Služba Azure IoT Central je aktuálně dostupná v zeměpisných oblastech **Austrálie**, **Asie a Tichomoří**, **Evropa**, **USA**, **Spojené království**a **Japonsko** . Jakmile zvolíte umístění, nemůžete později přesunout aplikaci do jiného umístění.

Po vyplnění všech polí vyberte **vytvořit**.

## <a name="manage-existing-iot-central-applications"></a>Spravovat existující aplikace IoT Central

Pokud už máte aplikaci Azure IoT Central, můžete ji odstranit nebo ji přesunout do jiného předplatného nebo skupiny prostředků v Azure Portal.

> [!NOTE]
> V Azure Portal neuvidíte aplikace vytvořené v cenovém plánu zdarma, protože nejsou přidružené k vašemu předplatnému.

Začněte tím, že vyberete **všechny prostředky** na portálu. Vyberte **Zobrazit skryté typy** a začněte psát název vaší aplikace ve **filtru podle názvu** a vyhledejte ji. Pak vyberte aplikaci IoT Central, kterou chcete spravovat.

Pokud chcete přejít na aplikaci, vyberte **IoT Central adresa URL aplikace**:

![Snímek obrazovky zobrazující stránku "Přehled" s zvýrazněnou adresou URL aplikace IoT Central.](media/howto-manage-iot-central-from-portal/image3.png)

Pokud chcete aplikaci přesunout do jiné skupiny prostředků, vyberte u skupiny prostředků možnost **změnit** . Na stránce **přesunout prostředky** vyberte skupinu prostředků, do které chcete tuto aplikaci přesunout:

![Snímek obrazovky zobrazující stránku "Přehled" se zvýrazněnou skupinou prostředků (změnit).](media/howto-manage-iot-central-from-portal/image4a.png)

Pokud chcete aplikaci přesunout do jiného předplatného, vyberte u předplatného možnost  **změnit** . Na stránce **Přesunutí prostředků** vyberte předplatné, do kterého chcete tuto aplikaci přesunout:

![Portál pro správu: Správa prostředků](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak spravovat aplikace IoT Central Azure z Azure Portal, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Správa vaší aplikace](howto-administer.md)