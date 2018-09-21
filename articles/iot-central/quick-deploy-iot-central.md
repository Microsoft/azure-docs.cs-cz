---
title: Vytvoření aplikace Azure IoT Central | Microsoft Docs
description: Vytvořte novou aplikaci Azure IoT Central pro správu chladících prodejních zařízení. Zobrazte telemetrická data vygenerovaná z vašich simulovaných zařízení.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/15/2018
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: af06766d89804b2f3d0aaf061494fb836f6ec262
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465601"
---
# <a name="create-an-azure-iot-central-application"></a>Vytvoření aplikace Azure IoT Central

Jako _tvůrce_ můžete pomocí uživatelského rozhraní Azure IoT Central definovat vaši aplikaci Azure IoT Central. V tomto rychlém startu se naučíte:

- Vytvořit aplikaci Azure IoT Central, který obsahuje ukázkovou _šablonu zařízení_ a simulovaná _zařízení_
- Zobrazit funkce šablony zařízení **Refrigerated Vending Machine** (Chladící prodejní automat) ve vaší aplikaci
- Zobrazit telemetrická data a analýzy ze simulovaných zařízení **Refrigerator** (Chladící box).

V tomto rychlém startu zobrazíte simulované zařízení **Refrigerator** (Chladící box) ze šablony zařízení. Simulované zařízení:

* Odesílá do vaší aplikace telemetrická data, jako je například teploty a tlak.
* Hlásí vaší aplikaci hodnoty vlastností zařízení, jako je upozornění na pohyb.
* Má nastavení zařízení, jako je třeba rychlost ventilátoru, která můžete nastavit v aplikaci.

Aby bylo možné otestovat aplikaci před připojením skutečného zařízení, aplikace Azure IoT Central umožňuje vygenerovat simulované zařízení ze šablony zařízení.

## <a name="create-the-application"></a>Vytvoření aplikace

K dokončení tohoto rychlého startu musíte vytvořit aplikaci Azure IoT Central z aplikační šablony **Sample Contoso**.

Přejděte na stránku [správce aplikací](https://aka.ms/iotcentral) Azure IoT Central. Potom zadejte e-mailovou adresu a heslo, které používáte pro přístup k vašemu předplatnému Azure:

![Zadání účtu organizace](media/quick-deploy-iot-central/sign-in.png)

Pokud chcete začít vytvářet novou aplikaci Azure IoT Central, zvolte **New Application** (Nová aplikace):

![Stránka správce aplikací Azure IoT Central](media/quick-deploy-iot-central/iotcentralhome.png)

Vytvoření nové aplikace Azure IoT Central:

1. Zvolte platební plán **Free Trial Application**.
1. Zvolte popisný název, jako je třeba **Contoso IoT**. Azure IoT Central pro vás vygeneruje jedinečnou předponu URL. Tuto předponu URL můžete změnit, aby byla snáze zapamatovatelná.
1. Zvolte šablonu aplikace **Sample Contoso**.
1. Potom zvolte **Create** (Vytvořit).

![Stránka vytvoření aplikace Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili předem naplněnou aplikaci Azure IoT Central, která obsahuje šablonu zařízení **Refrigerated Vending Machine** a simulovaná zařízení. Pokud se jako tvůrce chcete dozvědět víc o definování vlastních šablon zařízení, projděte si téma věnované [definování nové šablony zařízení ve vaší aplikaci](tutorial-define-device-type.md).
