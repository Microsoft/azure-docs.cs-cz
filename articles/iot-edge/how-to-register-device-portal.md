---
title: Zaregistrujte si nové zařízení z webu Azure portal – Azure IoT Edge | Dokumentace Microsoftu
description: Pomocí webu Azure portal k registraci nového zařízení IoT Edge a načtení připojovacího řetězce
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 7f3d0037bcf0fd33ae23c298679e3157046247cb
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983529"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Zaregistrujte si nové zařízení Azure IoT Edge z portálu Azure portal

Než budete moct použít zařízení IoT pomocí Azure IoT Edge, budete muset zaregistrovat u služby IoT hub. Po registraci zařízení obdržíte připojovací řetězec, který se dá použít k nastavení zařízení pro IoT Edge úlohy.

Tento článek ukazuje, jak registrovat nová zařízení IoT Edge pomocí webu Azure portal.

## <a name="prerequisites"></a>Požadavky

Bezplatné nebo standardní [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure.

## <a name="create-a-device"></a>Vytvoření zařízení

Na webu Azure Portal zařízení IoT Edge se vytváří a spravují samostatně ze zařízení, která připojení ke službě IoT hub, ale nejsou povolené edge.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do služby IoT hub.
2. Vyberte **IoT Edge** z nabídky.
3. Vyberte **Přidat zařízení IoT Edge**.
4. Zadejte ID popisný zařízení. K automatickému generování ověřovacích klíčů a připojení nového zařízení k rozbočovači použijte výchozí nastavení.
5. Vyberte **Uložit**.

## <a name="view-all-devices"></a>Zobrazit všechna zařízení

Jsou uvedeny všechny povolené hraniční zařízení, která připojení ke službě IoT hub na **IoT Edge** stránky.

## <a name="retrieve-the-connection-string"></a>Načtení připojovacího řetězce

Až budete připravení nastavit vaše zařízení, budete potřebovat připojovací řetězec, který propojí vaše fyzické zařízení do jeho identitu ve službě IoT hub.

1. Na stránce **IoT Edge** na portálu klikněte v seznamu IoT Edge zařízení na ID zařízení.
2. Zkopírujte hodnotu buď **připojovací řetězec (primární klíč)** nebo **připojovací řetězec (sekundární klíč)** .

## <a name="next-steps"></a>Další postup

Naučte se, jak [nasadit moduly do zařízení pomocí Azure Portal](how-to-deploy-modules-portal.md).
