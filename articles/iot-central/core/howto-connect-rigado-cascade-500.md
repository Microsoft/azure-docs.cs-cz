---
title: Připojení rigadoské kaskády 500 v Azure IoT Central | Dokumenty společnosti Microsoft
description: Přečtěte si, jak připojit zařízení brány Rigado Cascade 500 k aplikaci IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 3e6026e683d46ca9062c5c139fcd7febd7df9bd1
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758245"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>Připojení zařízení brány Rigado Cascade 500 k aplikaci Azure IoT Central

*Tento článek se vztahuje na tvůrce řešení a vývojáře zařízení.*

Tento článek popisuje, jak můžete jako tvůrce řešení připojit zařízení brány Rigado Cascade 500 k aplikaci Microsoft Azure IoT Central. 

## <a name="what-is-cascade-500"></a>Co je Cascade 500?

Cascade 500 IoT gateway je hardwarová nabídka z Rigado, která je součástí jejich řešení Cascade Edge-as-a-Service. Poskytuje komerčním projektovým a produktovým týmům IoT flexibilní výpočetní výkon na okraji, robustní prostředí kontejnerových aplikací a širokou škálu možností připojení k bezdrátovému zařízení, včetně Bluetooth 5, LTE, & Wi-Fi.

Cascade 500 je předem certifikovaná pro Azure IoT Plug and Play (preview), což umožňuje našim tvůrcům řešení snadno připojit zařízení do svých koncových řešení. Brána Cascade umožňuje bezdrátové připojení k různým senzorům pro sledování stavu, které jsou v blízkosti zařízení brány. Tyto senzory mohou být na palubě do IoT Central přes zařízení brány.

## <a name="prerequisites"></a>Požadavky
Chcete-li procházet tímto návodem, potřebujete následující zdroje:

* Zařízení Rigado Cascade 500. Pro více informací navštivte [prosím Rigado](https://www.rigado.com/).
* Aplikace Azure IoT Central. Další informace naleznete v [tématu vytvoření nové aplikace](./quick-deploy-iot-central.md).

## <a name="add-a-device-template"></a>Přidání šablony zařízení

Aby bylo možné napalubě zařízení brány Cascade 500 do instance aplikace Azure IoT Central, budete muset nakonfigurovat odpovídající šablonu zařízení v rámci vaší aplikace.

Přidání šablony zařízení Cascade 500: 

1. Přejděte na kartu ***Šablony zařízení*** v levém podokně, vyberte **+ Nový**: ![Vytvořit novou šablonu zařízení.](./media/howto-connect-rigado-cascade-500/device-template-new.png)
1. Stránka nabízí možnost ***vytvořit vlastní šablonu*** nebo ***použít předkonfigurovanou šablonu zařízení.***
1. Vyberte šablonu zařízení C500 ze seznamu předkonfigurovaných šablon zařízení, jak je znázorněno níže: ![Vyberte šablonu zařízení C500](./media/howto-connect-rigado-cascade-500/device-template-preconfigured.png)
1. Vyberte ***Další: Chcete-li*** pokračovat dalším krokem, můžete pokračovat. 
1. Na další obrazovce vyberte ***Vytvořit*** pro připojení šablony zařízení C500 do aplikace IoT Central.

## <a name="retrieve-application-connection-details"></a>Načtení podrobností o připojení aplikace

Teď budete muset načíst **ID oboru** a **primární klíč** pro vaši aplikaci Azure IoT Central, abyste mohli připojit zařízení Cascade 500. 

1. Přejděte do **režimu Správa** v levém podokně a klepněte na **tlačítko Připojení zařízení**. 
2. Poznamenejte si **ID oboru** pro vaši aplikaci IoT Central.
![ID oboru aplikace](./media/howto-connect-rigado-cascade-500/app-scope-id.png)
3. Nyní klikněte na **Zobrazit klíče** a poznamenejte si primární **klíč**
![primárního klíče](./media/howto-connect-rigado-cascade-500/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>Pro připojení brány kontaktujte Rigado. 

Chcete-li připojit zařízení Cascade 500 k aplikaci IoT Central, budete muset kontaktovat Rigado a poskytnout jim podrobnosti o připojení aplikace z výše uvedených kroků. 

Jakmile je zařízení připojeno k internetu, Rigado bude moci posunout aktualizaci konfigurace dolů na zařízení brány Cascade 500 prostřednictvím zabezpečeného kanálu. 

Tato aktualizace bude používat podrobnosti o připojení IoT Central na zařízení Cascade 500 a zobrazí se v seznamu vašich zařízení. 

![Primární klíč](./media/howto-connect-rigado-cascade-500/devices-list-c500.png)  

Nyní jste připraveni používat zařízení C500 ve vaší aplikaci IoT Central!

## <a name="next-steps"></a>Další kroky

Pokud jste vývojář zařízení, některé navrhované další kroky jsou:

- Přečtěte si o [připojení zařízení v Azure IoT Central](./concepts-get-connected.md)
- Zjistěte, jak [monitorovat připojení zařízení pomocí rozhraní příkazového příkazu Azure](./howto-monitor-devices-azure-cli.md)
