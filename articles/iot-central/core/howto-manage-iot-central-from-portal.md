---
title: Správa IoT Central z webu Azure Portal | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak vytvořit a spravovat aplikace IoT Central z webu Azure Portal.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: e7efda8efa27044168386e3ebbc557bf7fb74e8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157921"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Správa IoT Central z webu Azure Portal

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Místo vytváření a správy aplikací IoT Central na webu [správce aplikací Azure IoT Central](https://aka.ms/iotcentral) můžete ke správě aplikací použít portál [Azure.](https://portal.azure.com)

## <a name="create-iot-central-applications"></a>Vytváření aplikací IoT Central

Pokud chcete vytvořit aplikaci, přejděte na [portál Azure](https://ms.portal.azure.com) a vyberte **Vytvořit prostředek**.

V **panelu Hledat na marketplace** zadejte *IoT Central*:

![Portál pro správu: vyhledávání](media/howto-manage-iot-central-from-portal/image0a1.png)

Ve výsledcích hledání vyberte dlaždici **Centrální aplikace IoT:**

![Portál pro správu: výsledky vyhledávání](media/howto-manage-iot-central-from-portal/image0b1.png)

Nyní vyberte **Vytvořit**:

![Portál pro správu: Zdroj IoT Central](media/howto-manage-iot-central-from-portal/image0c1.png)

Vyplňte všechna pole ve formuláři. Tento formulář je podobný formuláři, který vyplníte k vytvoření aplikací na webu [správce aplikací Azure IoT Central.](https://aka.ms/iotcentral) Další informace najdete v [tématu Vytvoření aplikace IoT Central](quick-deploy-iot-central.md) rychlý start.

![Vytvořit formulář IoT Central](media/howto-manage-iot-central-from-portal/image6a.png)

**Umístění** je [zeměpis,](https://azure.microsoft.com/global-infrastructure/geographies/) kde chcete vytvořit aplikaci. Obvykle byste měli zvolit umístění, které je fyzicky nejblíže k vašim zařízením, abyste získali optimální výkon. Azure IoT Central je momentálně dostupný v **zeměpisných**oblastech Austrálie , **Asie a Tichomoří**, **Evropa**, **Spojené státy**, **Velká Británie**a **Japonsko.** Po výběru umístění nemůžete později přesunout aplikaci do jiného umístění.

Po vyplnění všech polí vyberte **Vytvořit**.

## <a name="manage-existing-iot-central-applications"></a>Správa existujících aplikací IoT Central

Pokud už máte aplikaci Azure IoT Central, můžete ji odstranit nebo přesunout do jiného předplatného nebo skupiny prostředků na webu Azure Portal.

> [!NOTE]
> Aplikace vytvořené na bezplatném cenovém plánu na webu Azure Portal neuvidíte, protože nejsou přidruženy k vašemu předplatnému.

Chcete-li začít, vyberte **všechny prostředky** na portálu. Vyberte **Zobrazit skryté typy** a začněte psát název aplikace v části Filtr podle **názvu,** abyste ji našli. Pak vyberte aplikaci IoT Central, kterou chcete spravovat.

Chcete-li přejít do aplikace, vyberte **adresu URL centrální aplikace IoT**:

![Portál pro správu: správa zdrojů](media/howto-manage-iot-central-from-portal/image3.png)

Chcete-li aplikaci přesunout do jiné skupiny prostředků, vyberte **možnost Změna** vedle skupiny prostředků. Na stránce **Přesunout prostředky** vyberte skupinu prostředků, do které chcete přesunout tuto aplikaci:

![Portál pro správu: správa zdrojů](media/howto-manage-iot-central-from-portal/image4a.png)

Pokud chcete aplikaci přesunout do jiného předplatného, vyberte **změnit** vedle předplatného. Na stránce **Přesunout prostředky** vyberte předplatné, do jehož nabídky chcete přesunout tuto aplikaci:

![Portál pro správu: správa zdrojů](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili spravovat aplikace Azure IoT Central z webu Azure Portal, tady je další doporučený krok:

> [!div class="nextstepaction"]
> [Správa vaší aplikace](howto-administer.md)