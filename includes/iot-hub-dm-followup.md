---
title: zahrnout soubor
description: zahrnout soubor
services: iot-hub
ms.service: iot-hub
author: robinsh
ms.topic: include
ms.date: 02/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 7f1f7d6f9ab6036fbcfcd1d19e175302bbd1a2a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87298774"
---
## <a name="customize-and-extend-the-device-management-actions"></a>Přizpůsobení a rozšiřování akcí správy zařízení

Vaše řešení IoT mohou rozšířit definovanou sadu vzorů správy zařízení nebo povolit vlastní vzory pomocí primitivních metod, které jsou v zařízení a jsou typu cloud-zařízení. Mezi další příklady akcí správy zařízení patří obnovení továrního nastavení, aktualizace firmwaru, aktualizace softwaru, řízení spotřeby, Správa sítě a připojení a šifrování dat.

## <a name="device-maintenance-windows"></a>Okna údržby zařízení

Obvykle se zařízení konfigurují v době, která minimalizuje přerušení a výpadky. Časové intervaly pro správu zařízení jsou běžně používané vzory, které definují čas, kdy má zařízení aktualizovat svou konfiguraci. Vaše back-endové řešení můžou pomocí požadovaných vlastností vlákna zařízení definovat a aktivovat zásady na zařízení, které umožňuje časové období údržby. Když zařízení obdrží zásady pro časový interval pro správu a údržbu, může k hlášení stavu zásady použít hlášené vlastnosti vlákna zařízení. Back-endové aplikace potom může pomocí dvojitých dotazů zařízení ověřit dodržování předpisů u zařízení a jednotlivých zásad.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste použili přímý způsob aktivace vzdáleného restartování zařízení. Pomocí ohlášených vlastností jste nahlásili čas posledního restartování ze zařízení a nahlásili jste se s ním dotazem, aby se zjistil čas posledního restartování zařízení z cloudu.

Pokud chcete pokračovat v seznámení se IoT Hub a způsoby správy zařízení, jako je například vzdálené prostřednictvím aktualizace firmwaru Air, přečtěte si téma [Jak provést aktualizaci firmwaru](../articles/iot-hub/tutorial-firmware-update.md).

Informace o tom, jak roztáhnout řešení IoT a naplánovat volání metod na více zařízení, najdete v tématu [úlohy plánování a vysílání](../articles/iot-hub/iot-hub-node-node-schedule-jobs.md).