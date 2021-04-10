---
title: 'Rychlý Start: požadavky na systém'
description: V tomto rychlém startu získáte požadavky na systém potřebné ke spuštění programu Azure Defender pro IoT.
ms.date: 11/30/2020
ms.topic: quickstart
ms.openlocfilehash: 2aae849b6ee772c2aa29c680f3b107af3ed600b0
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382942"
---
# <a name="quickstart-system-prerequisites"></a>Rychlý Start: požadavky na systém

V tomto článku jsou uvedené požadavky na systém pro spuštění programu Azure Defender pro IoT.

## <a name="prerequisites"></a>Požadavky

- Žádné

## <a name="minimum-requirements"></a>Minimální požadavky

- Síťové přepínače podporující monitorování přenosů přes port SPAN.
- Hardwarová zařízení pro senzory NTA.
- Role přispěvatele předplatného Azure. Vyžaduje se jenom během připojování k definování potvrzených zařízení a připojení k Sentinel Azure.
- Role **přispěvatele** v Azure IoT Hub (úroveň Free nebo Standard) pro správu připojenou k cloudu. Ujistěte se, že je povolená funkce **Azure Defender for IoT** .
- V případě programu Defender na úrovni zařízení – IoT-Micro-Agent podporuje Defender pro agenty IoT rostoucí seznam zařízení a platforem. Podívejte se na [seznam podporovaných platforem](how-to-deploy-agent.md).

## <a name="supported-service-regions"></a>Podporované oblasti služby

Defender pro IoT směruje veškerý provoz ze všech evropských oblastí do Západní Evropa regionální datacentrum. Směruje provoz ze všech zbývajících oblastí do Střed USA regionální datacentrum.

Další informace najdete v tématu [IoT Hub podporovaných oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Identifikace požadovaných zařízení](how-to-identify-required-appliances.md)
