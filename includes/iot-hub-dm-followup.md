---
title: zahrnout soubor
description: zahrnout soubor
services: iot-hub
ms.service: iot-hub
author: dominicbetts
ms.topic: include
ms.date: 02/17/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2514325c93f0f72aa979f871e86e465093479402
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69558785"
---
## <a name="customize-and-extend-the-device-management-actions"></a>Přizpůsobení a rozšíření akcí správy zařízení

Vaše řešení IoT můžete rozšířit definovanou sadu vzorů správy zařízení nebo povolit vlastní vzory pomocí dvojčezařízení a cloud-to-device metody primitiv. Mezi další příklady akcí správy zařízení patří obnovení továrního nastavení, aktualizace firmwaru, aktualizace softwaru, řízení spotřeby, správa sítě a připojení a šifrování dat.

## <a name="device-maintenance-windows"></a>Okna údržby zařízení

Zařízení obvykle nakonfigurujete tak, aby prováděla akce v době, která minimalizuje přerušení a prostoje. Okna údržby zařízení jsou běžně používaný vzor definovat čas, kdy zařízení by měla aktualizovat jeho konfiguraci. Back-endová řešení mohou použít požadované vlastnosti dvojčete zařízení k definování a aktivaci zásad na vašem zařízení, které umožňují okno údržby. Když zařízení obdrží zásady okna údržby, může použít ohlášenou vlastnost dvojčete zařízení k hlášení stavu zásad. Back-endová aplikace pak může pomocí dotazů na dvojče zařízení dosvědčit dodržování předpisů zařízení a jednotlivých zásad.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste použili přímou metodu ke spuštění vzdáleného restartování zařízení. Použili jste hlášené vlastnosti k nahlášení času posledního restartování ze zařízení a dotazovali jste dvojčezařízení, abyste zjistili čas posledního restartování zařízení z cloudu.

Chcete-li pokračovat v začínáme s IoT Hub a vzory správy zařízení, jako je například vzdálené přes aktualizaci firmwaru vzduchu, přečtěte si informace [o tom, jak provést aktualizaci firmwaru](../articles/iot-hub/tutorial-firmware-update.md).

Informace o tom, jak rozšířit řešení IoT a naplánovat volání metod na více zařízeních, najdete v [tématu Plánování a vysílání úloh](../articles/iot-hub/iot-hub-node-node-schedule-jobs.md).