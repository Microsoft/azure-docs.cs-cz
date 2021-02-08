---
title: 'Rychlý Start: konfigurace a povolení modulu zabezpečení pro Azure RTO'
description: Naučte se integrovat a povolit modul zabezpečení pro službu Azure RTO ve službě Azure IoT Hub.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: 3054981bbbff45666297399033663d1830ad9e4e
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820428"
---
# <a name="quickstart-security-module-for-azure-rtos-preview"></a>Rychlý Start: modul zabezpečení pro Azure RTO (Preview)

Tento článek obsahuje vysvětlení požadavků před začátkem a vysvětluje, jak povolit modul zabezpečení pro službu Azure RTO na IoT Hub. Pokud v tuto chvíli nemáte IoT Hub, přečtěte si téma [vytvoření IoT Hub pomocí Azure Portal](../iot-hub/iot-hub-create-through-portal.md) pro začátek.

## <a name="prerequisites"></a>Požadavky 

### <a name="supported-devices"></a>Podporovaná zařízení

- ST STM32F746G Discovery Kit
- NXP i.MX RT1060 EVK
- Mikročip SAM E54 Xplained pro EVK

Stáhněte, zkompilujte a spusťte jeden z souborů. zip pro konkrétní panel a nástroj (IAR, vývojové prostředí (IDE) nebo počítač) podle vašeho výběru z [modulu zabezpečení pro prostředek GitHub Azure RTO](https://github.com/azure-rtos/azure-iot-preview/releases).

### <a name="azure-resources"></a>Prostředky Azure

Další fází, jak začít, je příprava prostředků Azure. Budete potřebovat IoT Hub a navrhneme Log Analytics pracovní prostor. Pro IoT Hub budete potřebovat připojovací řetězec IoT Hub, abyste se mohli připojit k vašemu zařízení. 
  
### <a name="iot-hub-connection"></a>IoT Hub připojení

Aby bylo možné začít, je nutné IoT Hub připojení. 

1. Otevřete **IoT Hub** v Azure Portal.

1. Přejděte na **zařízení IoT**.

1. Vyberte **Vytvořit**.

1. Zkopírujte připojovací řetězec IoT do [konfiguračního souboru](how-to-azure-rtos-security-module.md).

Přihlašovací údaje připojení jsou pořízeny z konfigurace aplikace uživatele **HOST_NAME**, **DEVICE_ID** a **DEVICE_SYMMETRIC_KEY**.

Modul zabezpečení pro Azure RTO používá připojení middlewaru Azure IoT založené na protokolu **MQTT** .

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku a dokončete konfiguraci a přizpůsobení vašeho řešení.

> [!div class="nextstepaction"]
> [Konfigurace modulu zabezpečení pro Azure RTOS](how-to-azure-rtos-security-module.md)