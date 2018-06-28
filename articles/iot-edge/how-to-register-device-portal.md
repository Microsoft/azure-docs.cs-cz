---
title: Registrovat nové zařízení Azure IoT okraj (portál) | Microsoft Docs
description: Pomocí portálu Azure k registraci nového zařízení IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b61594469df33e11c23c9cbe0b9542da374fefa3
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035763"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Registrovat nové zařízení Azure IoT Edge z portálu Azure

Zařízení IoT můžete používat s Azure IoT Edge, budete muset registraci je službou IoT hub. Po registraci zařízení se zobrazí připojovací řetězec, který umožňuje nastavit zařízení pro úlohy okraj. 

Tento článek ukazuje, jak registrovat nové zařízení IoT Edge pomocí portálu Azure.

## <a name="prerequisites"></a>Požadavky

* [Služby IoT hub](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure. 

## <a name="create-a-device"></a>Vytvoření zařízení

Na portálu Azure IoT hraniční zařízení se vytváří a spravují samostatně ze zařízení, která připojení do služby IoT hub, ale nejsou povolené okraj. 

1. Přihlaste se k [portál Azure](https://portal.azure.com) a přejděte do služby IoT hub. 
2. Vyberte **IoT Edge** z nabídky.
3. Vyberte **přidání okraj IoT zařízení**. 
4. Zadejte ID popisný zařízení. 
5. Vyberte **Uložit**. 

## <a name="view-all-devices"></a>Zobrazit všechna zařízení

Všechny povolené hraniční zařízení, která připojení do služby IoT hub jsou uvedeny na **IoT Edge** stránky. 

## <a name="retrieve-the-connection-string"></a>Načtení připojovacího řetězce

Pokud jste připravení nastavit vaše zařízení, je potřeba připojovací řetězec, který odkazuje fyzického zařízení s svou identitu ve službě IoT hub.

1. Z **IoT Edge** na portálu, klikněte na ID zařízení ze seznamu hraniční zařízení. 
2. Zkopírujte hodnotu buď **připojovací řetězec – primární klíč** nebo **připojovací řetězec – sekundární klíč**. 

## <a name="next-steps"></a>Další postup

Zjistěte, jak [nasazení modulů do zařízení pomocí portálu Azure](how-to-deploy-modules-portal.md)