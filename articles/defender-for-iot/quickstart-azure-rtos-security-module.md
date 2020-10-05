---
title: 'Rychlý Start: konfigurace a povolení modulu zabezpečení pro Azure RTO'
description: Naučte se integrovat a povolit modul zabezpečení pro službu Azure RTO ve službě Azure IoT Hub.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: 6f438bd7dd0b3e45fd292947a3b08db80401b825
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "90946910"
---
# <a name="quickstart-security-module-for-azure-rtos-preview"></a>Rychlý Start: modul zabezpečení pro Azure RTO (Preview)

Tento článek obsahuje vysvětlení požadavků před začátkem a vysvětluje, jak povolit modul zabezpečení pro službu Azure RTO na IoT Hub. Pokud v tuto chvíli nemáte IoT Hub, přečtěte si téma [vytvoření IoT Hub pomocí Azure Portal](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) pro začátek.

> [!NOTE]
> Modul zabezpečení pro Azure RTO se podporuje jenom na rozbočovačích IoT úrovně Standard.

## <a name="prerequisites"></a>Předpoklady 

### <a name="supported-devices"></a>Podporovaná zařízení

- ST STM32F746G Discovery Kit
- NXP i.MX RT1060 EVK
- Mikročip SAM E54 Xplained pro EVK

Stáhněte, zkompilujte a spusťte jeden z souborů. zip pro konkrétní panel a nástroj (IAR, vývojové prostředí (IDE) nebo počítač) podle vašeho výběru z [modulu zabezpečení pro prostředek GitHub Azure RTO](hhtps://github.com/azure-rtos/azure-iot-preview/releases).

### <a name="azure-resources"></a>Prostředky Azure

Další fází, jak začít, je příprava prostředků Azure. Budete potřebovat IoT Hub a navrhneme Log Analytics pracovní prostor. Pro IoT Hub budete potřebovat připojovací řetězec IoT Hub, abyste se mohli připojit k vašemu zařízení. 
  
### <a name="iot-hub-connection"></a>IoT Hub připojení

Aby bylo možné začít, je nutné IoT Hub připojení. 

1. Otevřete **IoT Hub** v Azure Portal.
1. Zkopírujte připojovací řetězec IoT do [konfiguračního souboru](how-to-azure-rtos-security-module.md).


Přihlašovací údaje připojení jsou pořízeny z konfigurace aplikace uživatele **HOST_NAME**, **DEVICE_ID**a **DEVICE_SYMMETRIC_KEY**.

Modul zabezpečení pro Azure RTO používá připojení middlewaru Azure IoT založené na protokolu **MQTT** .


### <a name="log-analytics-workspace"></a>Pracovní prostor služby Log Analytics

Ingestování Log Analytics v IoT Hub je ve výchozím nastavení v programu Defender pro řešení IoT vypnuté. Pokud ho chcete povolit pro práci s modulem zabezpečení pro Azure RTO, udělejte toto: 
1. V Azure Portal přejdete do IoT Hub.
1. V nabídce **zabezpečení** vyberte **Nastavení** .
   :::image type="content" source="media/quickstart/azure-rtos-hub-settings.png" alt-text="Přístup k možnosti shromažďování dat pro Azure RTO"::: 
1. Vyberte **shromažďování dat**. 
1. V možnosti **Konfigurace pracovního prostoru** přepněte přepínač na **zapnuto**. 
1. Vytvořte nový pracovní prostor Log Analytics nebo připojte existující. Ujistěte se, že je vybraná možnost **přístup k nezpracovaným datům zabezpečení** . 
 :::image type="content" source="media/quickstart/azure-rtos-data-collection-on.png" alt-text="Přístup k možnosti shromažďování dat pro Azure RTO":::
1. Vyberte **Uložit**.
1. Vraťte se do seznamu prostředků Azure a potvrďte, že je pro IoT Hub povolený pracovní prostor Log Analytics, který jste vytvořili nebo připojeni.
    :::image type="content" source="media/quickstart/verify-azure-resource-list.png" alt-text="Přístup k možnosti shromažďování dat pro Azure RTO"::: 

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku a dokončete konfiguraci a přizpůsobení vašeho řešení.

> [!div class="nextstepaction"]
> [Konfigurace modulu zabezpečení pro Azure RTOS](how-to-azure-rtos-security-module.md)
