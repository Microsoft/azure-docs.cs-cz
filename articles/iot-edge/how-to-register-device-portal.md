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
ms.openlocfilehash: 16660fbed465cc70f16cde430024f33b8aa4350e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66495356"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Zaregistrujte si nové zařízení Azure IoT Edge z portálu Azure portal

Než budete moct použít zařízení IoT pomocí Azure IoT Edge, budete muset zaregistrovat u služby IoT hub. Po registraci zařízení se zobrazí připojovací řetězec, který slouží k nastavení zařízení pro úlohy IoT Edge.

Tento článek ukazuje, jak registrovat nová zařízení IoT Edge pomocí webu Azure portal.

## <a name="prerequisites"></a>Požadavky

Zdarma pro standardní [služby IoT hub](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure.

## <a name="create-a-device"></a>Vytvoření zařízení

Na webu Azure Portal zařízení IoT Edge se vytváří a spravují samostatně ze zařízení, která připojení ke službě IoT hub, ale nejsou povolené edge.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do služby IoT hub.
2. Vyberte **IoT Edge** z nabídky.
3. Vyberte **přidat zařízení IoT Edge**.
4. Zadejte ID popisný zařízení. Výchozí nastavení můžete automaticky vygenerovat klíče ověřování a připojení nového zařízení k centru.
5. Vyberte **Uložit**.

## <a name="view-all-devices"></a>Zobrazit všechna zařízení

Jsou uvedeny všechny povolené hraniční zařízení, která připojení ke službě IoT hub na **IoT Edge** stránky.

## <a name="retrieve-the-connection-string"></a>Načtení připojovacího řetězce

Až budete připravení nastavit vaše zařízení, budete potřebovat připojovací řetězec, který propojí vaše fyzické zařízení do jeho identitu ve službě IoT hub.

1. Z **IoT Edge** stránky na portálu, klikněte na ID zařízení v seznamu zařízení IoT Edge.
2. Zkopírujte hodnotu buď **připojovací řetězec (primární klíč)** nebo **připojovací řetězec (sekundární klíč)** .

## <a name="next-steps"></a>Další postup

Zjistěte, jak [nasadit moduly do zařízení pomocí webu Azure portal](how-to-deploy-modules-portal.md).
