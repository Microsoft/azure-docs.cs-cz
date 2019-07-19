---
title: Konfigurace řídicího panelu aplikace Azure IoT Central | Microsoft Docs
description: Jako tvůrce se dozvíte, jak nakonfigurovat výchozí řídicí panel aplikace Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 94ad51ac11687dfe060176132e2030d61b8d4ffc
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850221"
---
# <a name="configure-the-application-dashboard"></a>Konfigurace řídicího panelu aplikace

**Řídicí panel** je stránka, která se načte, když uživatelé, kteří mají přístup k aplikaci, navigují na adresu URL aplikace. Pokud jste pro vytvoření aplikace vybrali buď **vzorovou šablonu contoso** nebo **Sample Devkits** , vaše aplikace má předem definovaný řídicí panel. Pokud jste zvolili šablonu **vlastní** aplikační aplikace, váš řídicí panel bude prázdný.

> [!NOTE]
> Uživatelé také mohou [vytvořit vlastní osobní řídicí panely](howto-personalize-dashboard.md) , které budou použity místo výchozího řídicího panelu aplikace.

## <a name="add-tiles"></a>Přidat dlaždice

Následující snímek obrazovky ukazuje řídicí panel v aplikaci vytvořené z ukázkové šablony **Contoso** . Pokud chcete pro svou aplikaci přizpůsobit výchozí řídicí panel, vyberte v pravém horním rohu stránky **Upravit** .

![Řídicí panel pro aplikace založené na šabloně Sample contoso](media/howto-configure-homepage/image1a.png)

Výběrem možnosti **Upravit**otevřete panel Knihovna řídicího panelu. Knihovna obsahuje prvky dlaždic a řídicích panelů, které můžete použít k přizpůsobení řídicího panelu.

![Knihovna řídicích panelů](media/howto-configure-homepage/image2a.png)

Můžete například přidat dlaždici **nastavení zařízení a vlastnosti** a zobrazit tak výběr aktuálního nastavení a hodnot vlastností pro zařízení. Provedete to tak, že nejdřív vyberete **šablonu zařízení** a pak vyberete **instanci zařízení**. Poté dejte dlaždici název a vyberte **Nastavení** nebo **vlastnost** , která se má zobrazit. Následující snímek obrazovky ukazuje nastavení a vlastnosti vybrané pro přidání do dlaždice. Výběrem **Hotovo** uložte změnu na řídicí panel.

![Formulář konfigurace podrobností o zařízení s podrobnostmi o nastavení a vlastnostech](media/howto-configure-homepage/image3a.png)

Když teď operátor zobrazí výchozí řídicí panel aplikace, uvidí novou dlaždici s nastavením nastavení **teploty** pro zařízení:

![Karta řídicí panel se zobrazeným nastavením a vlastnostmi dlaždice](media/howto-configure-homepage/image4a.png)

Můžete prozkoumat další typy dlaždic v knihovně, abyste zjistili, jak dále přizpůsobit výchozí řídicí panel aplikace.

Další informace o tom, jak používat dlaždice v Azure IoT Central, najdete v tématu [použití dlaždic řídicího panelu](howto-use-tiles.md).

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak nakonfigurovat výchozí řídicí panel aplikace Azure IoT Central, můžete:

> [!div class="nextstepaction"]
> [Informace o přípravě a nahrání imagí](howto-prepare-images.md)
