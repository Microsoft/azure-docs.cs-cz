---
title: Vytvoření dvojčete modulu zabezpečení pro ASC pro náhled IoT | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit ASC pro dvojče zařízení IoT pro použití s ASC pro IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c782692e-1284-4c54-9d76-567bc13787cc
ms.service: ascforiot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 89802a638944ec220186e943d5fdc33524b2d4e9
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541632"
---
# <a name="quickstart-create-an-asc-for-iot-module-twin"></a>Rychlý start: Vytvoření ASC pro dvojče zařízení IoT

> [!IMPORTANT]
> ASC pro IoT je aktuálně ve verzi public preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento rychlý start vysvětlení toho, jak vytvořit jednotlivé ASC pro dvojčaty modulů IoT pro nová zařízení, nebo batch vytvořit dvojčaty modulů pro všechna zařízení ve službě IoT Hub.  

## <a name="understanding-asc-for-iot-module-twins"></a>Principy ASC pro dvojčaty modulů IoT 

Dvojčata zařízení pro řešení IoT v Azure, hrát klíčovou roli při správě zařízení a automatizace procesů. 

ASC pro IoT nabízí plnou integraci s vaší stávající IoT platformy pro správu zařízení, můžete ke správě stavu zabezpečení zařízení a ujistěte se také použít existující řízení funkcí zařízení. ASC integrace IoT je dosaženo zrychlením pomocí služby IoT Hub dvojčete mechanismus.  

Zobrazit [dvojčaty modulů IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) získat další informace o obecná koncepce dvojčaty modulů ve službě Azure IoT Hub. 
 
ASC pro IoT díky použití mechanismu pro dvojče modulu a udržuje dvojčete modulu zabezpečení pro každé z vašich zařízení. Dvojče modulu zabezpečení obsahuje všechny informace týkající se zabezpečení zařízení pro každou z vašich zařízení. 
 
Chcete-li plně využívat ASC pro funkce IoT, budete muset vytvořit, konfigurovat a používat tyto dvojčaty modulů zabezpečení pro každé zařízení ve službě.  

## <a name="create-asc-for-iot-module-twin"></a>Vytvoření ASC pro dvojče zařízení IoT 

ASC pro dvojčaty modulů IoT lze vytvořit v dávkovém režimu pomocí výchozí konfigurace nebo jednotlivě s konkrétní konfigurací pro každé zařízení. Do služby batch vytvořit nové zařízení nebo zařízení, dvojče modulu, použijte [modulu dávkový skript](https://aka.ms/iot-security-github-create-module). 

>[!NOTE] 
> Pomocí metody batch nepřepíše existující dvojčaty modulů. Pomocí služby batch metody pouze vytvoří nový modul dvojčata zařízení, která ještě nemají dvojčete modulu. 

Zobrazit [upravit dvojče modulu zabezpečení](how-to-modify-security-module-twin.md) se naučíte upravovat nebo měnit konfiguraci z existující dvojčete modulu. 

Chcete-li vytvořit nové ASC pro dvojče zařízení IoT pro zařízení použijte následující pokyny: 

1. Ve službě IoT Hub vyhledejte a vyberte zařízení, které chcete vytvořit pro dvojčete modulu zabezpečení ve službě IoT Hub. 
1. V **Microsoft Identity název** zadejte **ascforiotsecurity**.
1. Klikněte na **Uložit**. 

## <a name="verify-creation-of-a-module-twin"></a>Ověřte vytvoření tohoto dvojčete modulu

Chcete-li ověřit, jestli existuje dvojčete modulu zabezpečení pro určité zařízení:

1. Ve službě Azure IoT Hub, vyberte **zařízení IoT** z **Průzkumníci** nabídky.    
1. Zadejte ID zařízení, nebo vyberte možnost v **pole zařízení dotazu** a klikněte na tlačítko **dotazování zařízení**. 
    ![Dotaz zařízení](./media/quickstart/verify-security-module-twin.png)
1. Vyberte zařízení, nebo dvojím kliknutím ho otevřete stránku Podrobnosti o zařízení. 
1. Vyberte **modul identity** nabídky a ověřte existenci **ascforiotsecurity** modulu a **stav připojení** z **připojeno**v seznamu identit modul přidružené k zařízení. 
    ![Moduly, které jsou přidružené k zařízení](./media/quickstart/verify-security-module-twin-2.png)


Další informace o přizpůsobení ASC vlastnosti pro dvojče modulu IoT, najdete v článku [konfigurace agenta](concept-agent-configuration.md).

## <a name="next-steps"></a>Další postup

Přejděte k dalším článku se naučíte, jak nakonfigurovat vlastní výstrahy...

> [!div class="nextstepaction"]
> [Konfigurace vlastní upozornění](quickstart-create-custom-alerts.md)
