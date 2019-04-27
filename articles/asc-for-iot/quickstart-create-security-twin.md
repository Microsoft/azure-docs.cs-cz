---
title: Vytvoření dvojčete modulu zabezpečení pro Azure Security Center pro náhled IoT | Dokumentace Microsoftu
description: Naučíte se vytvářet Azure Security Center pro dvojče zařízení IoT pro použití s ASC pro IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c782692e-1284-4c54-9d76-567bc13787cc
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 16b5525973b93bc6b073c50c0c657dcbb4679040
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61347868"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Rychlý start: Vytvoření azureiotsecurity dvojčete modulu

> [!IMPORTANT]
> Azure Security Center pro IoT je aktuálně ve verzi public preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento rychlý start vysvětlení, jak vytvořit jednotlivé _azureiotsecurity_ dvojčaty modulů pro nová zařízení nebo batch vytvořit dvojčaty modulů pro všechna zařízení ve službě IoT Hub.  

## <a name="understanding-azureiotsecurity-module-twins"></a>Principy dvojčat modulů azureiotsecurity 

Dvojčata zařízení pro řešení IoT v Azure, hrát klíčovou roli při správě zařízení a automatizace procesů. 

Azure Security Center (ASC) pro IoT nabízí plnou integraci s vaší stávající IoT platformy pro správu zařízení, můžete ke správě stavu zabezpečení zařízení a ujistěte se také použít existující řízení funkcí zařízení.
ASC integrace IoT je dosaženo zrychlením pomocí služby IoT Hub dvojčete mechanismus.  

Zobrazit [dvojčaty modulů IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) získat další informace o obecná koncepce dvojčaty modulů ve službě Azure IoT Hub. 
 
ASC pro IoT díky použití mechanismu pro dvojče modulu a udržuje dvojčete modulu zabezpečení s názvem _azureiotsecurity_ pro každé z vašich zařízení.
Dvojče modulu zabezpečení obsahuje všechny informace týkající se zabezpečení zařízení pro každou z vašich zařízení. 
 
Chcete-li plně využívat ASC pro funkce IoT, budete muset vytvořit, konfigurovat a používat tyto dvojčaty modulů zabezpečení pro každé zařízení ve službě.  

## <a name="create-azureiotsecurity-module-twin"></a>Vytvoření azureiotsecurity dvojče zařízení 

_azureiotsecurity_ dvojčaty modulů můžete vytvořit dvěma způsoby:
1. [Modul dávkový skript](https://aka.ms/iot-security-github-create-module) : automaticky vytvoří dvojče modulu pro nové zařízení nebo zařízení bez dvojčete modulu pomocí výchozí konfigurace.
2. Ruční úprava dvojčete každého modulu jednotlivě s konkrétní konfigurací pro každé zařízení.

>[!NOTE] 
> Pomocí metody batch nepřepíše existující azureiotsecurity dvojčaty modulů. Pomocí služby batch metody pouze vytvoří nový modul dvojčata zařízení, která ještě nemají dvojčete modulu zabezpečení. 

Zobrazit [konfigurace agenta](how-to-agent-configuration.md) se naučíte upravovat nebo měnit konfiguraci z existující dvojčete modulu. 

Chcete-li vytvořit ručně nový _azureiotsecurity_ dvojče zařízení pro zařízení, použijte následující pokyny: 

1. Ve službě IoT Hub vyhledejte a vyberte zařízení, které chcete vytvořit pro dvojčete modulu zabezpečení ve službě IoT Hub.
1. Klikněte na tlačítko na vašem zařízení a potom na **přidejte identitu modulu**.
1. V **název modulu Identity** zadejte **azureiotsecurity**.

1. Klikněte na **Uložit**. 

## <a name="verify-creation-of-a-module-twin"></a>Ověřte vytvoření tohoto dvojčete modulu

Chcete-li ověřit, jestli existuje dvojčete modulu zabezpečení pro určité zařízení:

1. Ve službě Azure IoT Hub, vyberte **zařízení IoT** z **Průzkumníci** nabídky.    
1. Zadejte ID zařízení, nebo vyberte možnost v **pole zařízení dotazu** a klikněte na tlačítko **dotazování zařízení**. 
    ![Dotaz zařízení](./media/quickstart/verify-security-module-twin.png)
1. Vyberte zařízení, nebo dvojím kliknutím ho otevřete stránku Podrobnosti o zařízení. 
1. Vyberte **modul identity** nabídky a ověřte existenci **azureiotsecurity** modulu v seznamu identit modul přidružené k zařízení. 
    ![Moduly, které jsou přidružené k zařízení](./media/quickstart/verify-security-module-twin-3.png)


Další informace o přizpůsobení ASC vlastnosti pro dvojče modulu IoT, najdete v článku [konfigurace agenta](how-to-agent-configuration.md).

## <a name="next-steps"></a>Další postup

Přejděte k dalším článku se naučíte, jak nakonfigurovat vlastní výstrahy...

> [!div class="nextstepaction"]
> [Konfigurace vlastní upozornění](quickstart-create-custom-alerts.md)
