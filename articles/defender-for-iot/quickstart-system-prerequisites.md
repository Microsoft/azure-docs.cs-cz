---
title: Systémové požadavky
description: Získejte požadavky na systém potřebné ke spuštění programu Azure Defender pro IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/30/2020
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 4b5db049e6d1cfe76bdd0d5cd6d7360e0b98bad0
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489807"
---
# <a name="system-prerequisites"></a>Systémové požadavky
V tomto článku jsou uvedené požadavky na systém pro spuštění programu Azure Defender pro IoT.

## <a name="minimum-requirements"></a>Minimální požadavky

- Síťové přepínače podporující monitorování přenosů přes port SPAN.
- Hardwarová zařízení pro senzory NTA.
- Role přispěvatele předplatného Azure. Vyžaduje se jenom během připojování k definování potvrzených zařízení a připojení k Sentinel Azure.
- Role **přispěvatele** v Azure IoT Hub (úroveň Free nebo Standard) pro správu připojenou k cloudu. Ujistěte se, že je povolená funkce **Azure Defender for IoT** .
- V případě programu Defender na úrovni zařízení – IoT-Micro-Agent podporuje Defender pro agenty IoT rostoucí seznam zařízení a platforem. Podívejte se na [seznam podporovaných platforem](how-to-deploy-agent.md).

## <a name="supported-service-regions"></a>Podporované oblasti služby

Defender pro IoT směruje veškerý provoz ze všech evropských oblastí do Západní Evropa regionální datacentrum. Směruje provoz ze všech zbývajících oblastí do Střed USA regionální datacentrum.

Další informace najdete v tématu [IoT Hub podporovaných oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).

## <a name="see-also"></a>Viz také

- [Identifikace požadovaných zařízení](how-to-identify-required-appliances.md)
- [O programu Azure Defender pro síť IoT](how-to-set-up-your-network.md)
