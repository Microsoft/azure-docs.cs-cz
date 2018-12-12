---
title: Zaregistrujte si nové zařízení z webu Azure portal – Azure IoT Edge | Dokumentace Microsoftu
description: Pomocí webu Azure portal k registraci nového zařízení IoT Edge a načtení připojovacího řetězce
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: da93541339ac1c199d3ba0a220fbfff6bbb8bf9c
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53082115"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Zaregistrujte si nové zařízení Azure IoT Edge z portálu Azure portal

Než budete moct použít zařízení IoT pomocí Azure IoT Edge, budete muset zaregistrovat u služby IoT hub. Po registraci zařízení se zobrazí připojovací řetězec, který slouží k nastavení zařízení pro hraniční úlohy. 

Tento článek ukazuje, jak registrovat nová zařízení IoT Edge pomocí webu Azure portal.

## <a name="prerequisites"></a>Požadavky

* [Služby IoT hub](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure. 

## <a name="create-a-device"></a>Vytvoření zařízení

Na webu Azure Portal zařízení IoT Edge se vytváří a spravují samostatně ze zařízení, která připojení ke službě IoT hub, ale nejsou povolené edge. 

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do služby IoT hub. 
2. Vyberte **IoT Edge** z nabídky.
3. Vyberte **přidat zařízení IoT Edge**. 
4. Zadejte ID popisný zařízení. 
5. Vyberte **Uložit**. 

## <a name="view-all-devices"></a>Zobrazit všechna zařízení

Jsou uvedeny všechny povolené hraniční zařízení, která připojení ke službě IoT hub na **IoT Edge** stránky. 

## <a name="retrieve-the-connection-string"></a>Načtení připojovacího řetězce

Až budete připravení nastavit vaše zařízení, budete potřebovat připojovací řetězec, který propojí vaše fyzické zařízení do jeho identitu ve službě IoT hub.

1. Z **IoT Edge** stránky na portálu, klikněte na ID zařízení v seznamu hraničních zařízení. 
2. Zkopírujte hodnotu buď **připojovací řetězec (primární klíč)** nebo **připojovací řetězec (sekundární klíč)**. 

## <a name="next-steps"></a>Další postup

Zjistěte, jak [nasadit moduly do zařízení pomocí webu Azure portal](how-to-deploy-modules-portal.md)
