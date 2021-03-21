---
title: 'Rychlý Start: konfigurace a povolení programu Defender-IoT-Micro-Agent pro Azure RTO'
description: Naučte se, jak připojit a povolit službu Defender-IoT-Micro-Agent pro Azure RTO ve službě Azure IoT Hub.
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
ms.openlocfilehash: 3c1af1128b99cbd3263ddffc834eb27ab9dec564
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489841"
---
# <a name="quickstart-defender-iot-micro-agent-for-azure-rtos-preview"></a>Rychlý Start: Defender-IoT-Micro Agent pro Azure RTO (Preview)

Tento článek obsahuje vysvětlení požadavků před začátkem a vysvětluje, jak povolit službu Defender-IoT-Micro-Agent pro Azure RTO ve IoT Hub. Pokud v tuto chvíli nemáte IoT Hub, přečtěte si téma [vytvoření IoT Hub pomocí Azure Portal](../iot-hub/iot-hub-create-through-portal.md) pro začátek.

## <a name="prerequisites"></a>Předpoklady 

### <a name="supported-devices"></a>Podporovaná zařízení

- ST STM32F746G Discovery Kit
- NXP i.MX RT1060 EVK
- Mikročip SAM E54 Xplained pro EVK

Stáhněte, zkompilujte a spusťte jeden z souborů. zip pro konkrétní desku a nástroj (IAR, vývojové prostředí (IDE) nebo počítač) podle vašeho výběru od programu [Defender-IoT-Micro-Agent pro Azure RTO GitHub](https://github.com/azure-rtos/azure-iot-preview/releases).

### <a name="azure-resources"></a>Prostředky Azure

Další fází, jak začít, je příprava prostředků Azure. Budete potřebovat IoT Hub a navrhneme Log Analytics pracovní prostor. Pro IoT Hub budete potřebovat připojovací řetězec IoT Hub, abyste se mohli připojit k vašemu zařízení. 
  
### <a name="iot-hub-connection"></a>IoT Hub připojení

Aby bylo možné začít, je nutné IoT Hub připojení. 

1. Otevřete **IoT Hub** v Azure Portal.

1. Přejděte na **zařízení IoT**.

1. Vyberte **Vytvořit**.

1. Zkopírujte připojovací řetězec IoT do [konfiguračního souboru](how-to-azure-rtos-security-module.md).

Přihlašovací údaje připojení jsou pořízeny z konfigurace aplikace uživatele **HOST_NAME**, **DEVICE_ID** a **DEVICE_SYMMETRIC_KEY**.

Defender-IoT-Micro-Agent pro Azure RTO používá připojení middleware Azure IoT založené na protokolu **MQTT** .

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku a dokončete konfiguraci a přizpůsobení vašeho řešení.

> [!div class="nextstepaction"]
> [Konfigurace a přizpůsobení programu Defender-IoT-Micro-Agent pro Azure RTO (Preview)](how-to-azure-rtos-security-module.md)
