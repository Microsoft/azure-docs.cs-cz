---
title: Připojení Rigado na sebe 500 v Azure IoT Central | Microsoft Docs
description: Naučte se připojit Rigado zařízení brány 500 k vaší aplikaci IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 0000e7690ab92f469a7417e82cb375c524e0b343
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90016839"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>Připojení zařízení brány 500 Rigado do IoT Central k aplikaci Azure

*Tento článek se týká tvůrců řešení.*

Tento článek popisuje, jak jako tvůrce řešení můžete připojit Rigado zařízení brány 500 k vaší aplikaci Microsoft Azure IoT Central. 

## <a name="what-is-cascade-500"></a>Co je kaskádovitě 500?

Kaskádová brána 500 IoT Gateway je hardwarová nabídka z Rigado, která je součástí jejich kaskádového řešení jako služby. Poskytuje komerční projekt IoT a produktové týmy s flexibilním výkonem hraničního zpracování, robustním kontejnerovým prostředím aplikací a širokou škálou možností připojení bezdrátových zařízení, včetně Bluetooth 5, LTE a & Wi-Fi.

Kaskádová verze 500 je předem certifikována pro Azure IoT technologie Plug and Play (Preview), která umožňuje tvůrcům řešení snadno připojit zařízení do koncových řešení. Brána na kaskádě umožňuje bezdrátové připojení k celé řadě senzorů monitorování podmínek, které jsou v blízkosti zařízení brány. Tyto senzory je možné připojit k IoT Central prostřednictvím zařízení brány.

## <a name="prerequisites"></a>Požadavky
Chcete-li procházet tento návod, potřebujete následující zdroje:

* Rigado kaskádové zařízení 500. Další informace najdete na [Rigado](https://www.rigado.com/).
* Aplikace Azure IoT Central. Další informace najdete v tématu [Vytvoření nové aplikace](./quick-deploy-iot-central.md).

## <a name="add-a-device-template"></a>Přidání šablony zařízení

Aby bylo možné za500 členit do instance aplikace IoT Central služby Azure bránu, bude nutné v rámci aplikace nakonfigurovat odpovídající šablonu zařízení.

Přidání šablony zařízení s kaskádovou 500: 

1. V levém podokně přejděte na kartu ***šablony zařízení*** , vyberte **+ Nový**: ![ vytvořit novou šablonu zařízení.](./media/howto-connect-rigado-cascade-500/device-template-new.png)
1. Stránka vám nabídne možnost ***vytvořit vlastní šablonu*** nebo ***použít předkonfigurovaný šablonu zařízení*** .
1. V seznamu předkonfigurovaných šablon zařízení vyberte šablonu zařízení C500, jak je vidět níže: ![ Vyberte šablonu zařízení C500.](./media/howto-connect-rigado-cascade-500/device-template-preconfigured.png)
1. Vyberte ***Další: přizpůsobit*** , aby pokračoval k dalšímu kroku. 
1. Na další obrazovce vyberte ***vytvořit*** a připojte šablonu zařízení C500 do vaší aplikace IoT Central.

## <a name="retrieve-application-connection-details"></a>Načíst podrobnosti připojení aplikace

Teď budete muset pro aplikaci Azure IoT Central načíst **ID oboru** a **primární klíč** , aby bylo možné propojit zařízení s kaskádou 500. 

1. V levém podokně přejděte do části **Správa**  a klikněte na **připojení zařízení**. 
2. Poznamenejte si **ID oboru** pro vaši aplikaci IoT Central.
![ID oboru aplikace](./media/howto-connect-rigado-cascade-500/app-scope-id.png)
3. Nyní klikněte na **Zobrazit klíče** a poznamenejte si primární klíč **primárního klíče**. 
 ![](./media/howto-connect-rigado-cascade-500/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>Kontaktujte Rigado a připojte bránu. 

Aby bylo možné připojit zařízení s 500 k vaší aplikaci IoT Central, budete muset kontaktovat Rigado a poskytnout jim podrobnosti připojení aplikace z výše uvedených kroků. 

Jakmile je zařízení připojené k Internetu, Rigado bude moct přes zabezpečený kanál přejít na nižší úroveň aktualizace konfigurace na zařízení brány na kaskádě 500. 

Tato aktualizace použije informace o IoT Central připojení na zařízení s kaskádovou 500 a zobrazí se v seznamu zařízení. 

![Seznam zařízení](./media/howto-connect-rigado-cascade-500/devices-list-c500.png)  

Teď jste připraveni použít zařízení C500 v aplikaci IoT Central!

## <a name="next-steps"></a>Další kroky

Pokud jste vývojářem zařízení, některé z navrhovaných dalších kroků:

- Přečtěte si o [připojení zařízení v Azure IoT Central](./concepts-get-connected.md)
- Naučte se [monitorovat připojení zařízení pomocí Azure CLI](./howto-monitor-devices-azure-cli.md) .
