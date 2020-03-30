---
title: Připojení ruuvitagu v Azure IoT Central | Dokumenty společnosti Microsoft
description: Přečtěte si, jak připojit senzor prostředí RuuviTag k aplikaci IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: e8d1c4a605e8db2e9753bb80c9712dd6c2be7b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158222"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>Připojení senzoru RuuviTag k aplikaci Azure IoT Central

Tento článek popisuje, jak jako tvůrce řešení můžete připojit senzor RuuviTag k aplikaci Microsoft Azure IoT Central.

Co je značka Ruuvi?

RuuviTag je pokročilá open-source senzorová platforma navržená tak, aby splňovala potřeby firemních zákazníků, vývojářů, tvůrců, studentů a fandů. Zařízení je nastaveno tak, aby fungovalo, jakmile ho vynesete z krabice a je připraveno k nasazení tam, kde ho potřebujete. Je to Bluetooth LE maják s vestavěným senzorem prostředí a akcelerometrem.

RuuviTag komunikuje přes BLE (Bluetooth Low Energy) a vyžaduje zařízení brány pro komunikaci s Azure IoT Central. Ujistěte se, že máte zařízení brány, jako je Rigado Cascade 500, nastavení povolit RuuviTag pro připojení k IoT Central.

Pokud chcete nastavit zařízení brány Rigado Cascade 500, postupujte [podle pokynů zde.](./howto-connect-rigado-cascade-500.md)

## <a name="prerequisites"></a>Požadavky

Chcete-li připojit senzory RuuviTag, potřebujete následující zdroje:

* RuuviTag senzor. Pro více informací navštivte [prosím RuuviTag](https://ruuvi.com/).
* Zařízení Rigado Cascade 500 nebo jiná brána BLE. Pro více informací navštivte [prosím Rigado](https://www.rigado.com/).
* Aplikace Azure IoT Central. Další informace naleznete v [tématu vytvoření nové aplikace](./quick-deploy-iot-central.md).

## <a name="add-a-ruuvitag-device-template"></a>Přidání šablony zařízení RuuviTag

Chcete-li napalubě senzorruuviTag do instance aplikace Azure IoT Central, je třeba nakonfigurovat odpovídající šablonu zařízení v rámci vaší aplikace.

Přidání šablony zařízení RuuviTag:

1. Přejděte na kartu ***Šablony zařízení*** v levém podokně, vyberte **+ Nový**: ![Vytvořit novou šablonu](./media/howto-connect-ruuvi/devicetemplate-new.png) zařízení Stránka vám dává možnost Vytvořit vlastní ***šablonu*** nebo ***Použít předkonfigurovanou šablonu zařízení.***
1. Vyberte šablonu zařízení RuuviTag ze seznamu předkonfigurovaných šablon zařízení, jak je znázorněno níže: ![Vyberte šablonu zařízení RuuviTag.](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. Vyberte ***Další: Chcete-li*** pokračovat dalším krokem, můžete pokračovat.
1. Na další obrazovce vyberte ***Vytvořit*** pro připojení šablony zařízení C500 do aplikace IoT Central.

## <a name="connect-a-ruuvitag-sensor"></a>Připojení senzoru RuuviTag

Jak již bylo zmíněno dříve, chcete-li připojit RuuviTag s aplikací IoT Central, musíte nastavit zařízení brány. Následující kroky předpokládají, že jste nastavili zařízení brány Rigado Cascade 500.  

1. Zapnete zařízení Rigado Cascade 500 a připojte jej k síťovému připojení (přes Ethernet nebo bezdrátově)
1. Odsuďte kryt ruuviTag a vytáhněte plastový štítek, abyste zajistili spojení s baterií.
1. Umístěte RuuviTag blízko brány Rigado Cascade 500, která je už nakonfigurovaná ve vaší aplikaci IoT Central.
1. Během několika sekund by se ruuviTag měl zobrazit v seznamu zařízení v rámci IoT Central.  
    ![Seznam zařízení RuuviTag](./media/howto-connect-ruuvi/ruuvi-devicelist.png)

Nyní můžete použít tento RuuviTag v rámci aplikace IoT Central.  

## <a name="create-a-simulated-ruuvitag"></a>Vytvoření simulovanéruuviTag

Pokud nemáte fyzické zařízení RuuviTag, můžete vytvořit simulovaný senzor RuuviTag, který se použije pro testování v rámci aplikace Azure IoT Central.

Chcete-li vytvořit simulované RuuviTag:

1. Vyberte **zařízení > RuuviTag**.
1. Vyberte **+ Nový**.
1. Zadejte jedinečné **ID zařízení** a popisný **název zařízení**.  
1. Povolte **nastavení Simulované.**
1. Vyberte **Vytvořit**.  

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak připojit RuuviTag k vaší aplikaci Azure IoT Central, je dalším krokem naučit se [přizpůsobit aplikaci IoT Central](../retail/tutorial-in-store-analytics-customize-dashboard-pnp.md) k vytvoření komplexního řešení.
