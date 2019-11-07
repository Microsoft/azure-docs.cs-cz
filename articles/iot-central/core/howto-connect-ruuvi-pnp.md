---
title: Připojení RuuviTag v Azure IoT Central | Microsoft Docs
description: Naučte se, jak připojit senzor prostředí RuuviTag k aplikaci IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: conceptual
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 10/19/2019
ms.openlocfilehash: 9f82b7ca25c03abbc94337dcfcf0f7fe0099d0d0
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73620022"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>Připojení snímače RuuviTag k aplikaci Azure IoT Central

Tento článek popisuje, jak jako tvůrce řešení můžete připojit senzor RuuviTag k aplikaci Microsoft Azure IoT Central.

Co je značka Ruuvi?

RuuviTag je pokročilá Open Source platforma pro signalizaci, která je navržená tak, aby splňovala potřeby obchodních zákazníků, vývojářů, tvůrců, studentů a nadšence. Zařízení je nastavené tak, aby fungovalo hned po jeho přijetí, a je připravené na jeho nasazení tam, kde ho potřebujete. Jedná se o signalizaci Bluetooth LE pomocí snímače prostředí a předem připraveného zrychlení.

RuuviTag komunikuje přes tabulku (Bluetooth Low Energy) a vyžaduje, aby zařízení s bránou komunikovalo s Azure IoT Central. Ujistěte se, že máte zařízení brány, jako je Rigado Cascade 500, instalační program, aby se RuuviTag mohl připojit k IoT Central.

Pokud chcete nastavit Rigado zařízení brány 500, postupujte prosím podle [pokynů uvedených tady](./howto-connect-rigado-cascade-500-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

## <a name="prerequisites"></a>Požadavky

K propojení senzorů RuuviTag potřebujete tyto prostředky:

* Senzor RuuviTag Další informace najdete na [RuuviTag](https://ruuvi.com/).
* Rigado kaskádová zařízení 500 nebo jiná brána v ní. Další informace najdete na [Rigado](https://www.rigado.com/).
* Aplikace Azure IoT Central vytvořená z některé z šablon aplikace ve verzi Preview. Další informace najdete v tématu [Vytvoření nové aplikace](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="add-a-ruuvitag-device-template"></a>Přidání šablony zařízení RuuviTag

Pokud chcete RuuviTag senzor do instance aplikace Azure IoT Central, musíte v rámci své aplikace nakonfigurovat odpovídající šablonu zařízení.

Přidání šablony zařízení RuuviTag:

1. V levém podokně přejděte na kartu ***šablony zařízení*** , vyberte **+ Nový**: ![vytvořit novou šablonu zařízení](./media/howto-connect-ruuvi/devicetemplate-new.png) stránka vám nabídne možnost ***vytvořit vlastní šablonu*** nebo ***použít předkonfigurované šablony zařízení*** .
1. V seznamu předkonfigurovaných šablon zařízení vyberte šablonu zařízení RuuviTag, jak je znázorněno níže: ![vybrat šablonu zařízení RuuviTag](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. Vyberte ***Další: přizpůsobit*** , aby pokračoval k dalšímu kroku.
1. Na další obrazovce vyberte ***vytvořit*** a připojte šablonu zařízení C500 do vaší aplikace IoT Central.

## <a name="connect-a-ruuvitag-sensor"></a>Připojit senzor RuuviTag

Jak bylo zmíněno dříve, chcete-li připojit RuuviTag k aplikaci IoT Central, je třeba nastavit zařízení brány. V následujících krocích se předpokládá, že jste nastavili Rigado zařízení brány v kaskádě 500.  

1. Zapněte si Rigado do kaskádového zařízení 500 a připojte ho k síťovému připojení (přes Ethernet nebo bezdrátové sítě).
1. Odkrytí RuuviTag a získáním karty plasty můžete zabezpečit připojení k baterii.
1. RuuviTag blízko k Rigado kaskádě 500, která je už nakonfigurovaná ve vaší IoT Central aplikaci.
1. Během pár sekund by se váš RuuviTag měl zobrazit v seznamu zařízení v rámci IoT Central.  
    ![Seznam zařízení RuuviTag](./media/howto-connect-ruuvi/ruuvi-devicelist.png)

Tuto RuuviTag teď můžete používat v rámci aplikace IoT Central.  

## <a name="create-a-simulated-ruuvitag"></a>Vytvoření simulovaného RuuviTagu

Pokud nemáte fyzické zařízení RuuviTag, můžete vytvořit simulovaný RuuviTag senzor, který se použije pro testování v rámci aplikace IoT Central v Azure.

Postup vytvoření simulovaného RuuviTag:

1. Vyberte **zařízení > RuuviTag**.
1. Vyberte **+ Nový**.
1. Zadejte jedinečné **ID zařízení** a popisný **název zařízení**.  
1. Povolí **simulované** nastavení.
1. Vyberte **Vytvořit**.  

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili připojit RuuviTag k aplikaci Azure IoT Central, je navržený další krok, kde se dozvíte, jak [přizpůsobit aplikaci IoT Central](../retail/tutorial-in-store-analytics-customize-dashboard-pnp.md) a vytvořit tak koncové řešení.
