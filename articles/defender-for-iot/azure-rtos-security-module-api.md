---
title: Defender – IoT-Micro-Agent pro Azure RTO API
description: Referenční rozhraní API pro Defender-IoT-Micro-Agent pro Azure RTO.
ms.topic: reference
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: e7000a7e6d8ba332432f1ececa12bd9543e9e4a7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779388"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-api-preview"></a>Defender – IoT-Micro-Agent pro Azure RTO API (Preview)

Toto rozhraní API je určené pro použití s Defenderem-IoT-Micro-Agent pro Azure RTO. Další zdroje najdete v tématu věnovaném programu [Defender-IoT-Micro-Agent pro Azure RTO GitHub](https://github.com/azure-rtos/azure-iot-preview/releases). 

## <a name="enable-defender-iot-micro-agent-for-azure-rtos"></a>Povolení programu Defender – IoT-Micro-Agent pro Azure RTO

**nx_azure_iot_security_module_enable**

### <a name="prototype"></a>Prototype

```c
UINT nx_azure_iot_security_module_enable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Description

Tato rutina umožňuje subsystém Azure IoT Defender – IoT-Micro-agent. Interní Stavový počítač spravuje shromažďování událostí zabezpečení a odesílá je do Azure IoT Hub. Pro správu shromažďování dat je vyžadována pouze jedna instance NX_AZURE_IOT_SECURITY_MODULE.

### <a name="parameters"></a>Parametry

| Název | Description |
|---------|---------|
| nx_azure_iot_ptr [in]    | Ukazatel na `NX_AZURE_IOT` .  |

### <a name="return-values"></a>Vrácené hodnoty

|Vrácené hodnoty  |Description |
|---------|---------|
|NX_AZURE_IOT_SUCCESS|   Modul zabezpečení Azure IoT se úspěšně povolil.     |
|NX_AZURE_IOT_FAILURE   |  Kvůli vnitřní chybě se nepovedlo povolit modul zabezpečení Azure IoT.    |
|NX_AZURE_IOT_INVALID_PARAMETER   |  Modul zabezpečení vyžaduje platnou instanci #NX_AZURE_IOT.      |

### <a name="allowed-from"></a>Povoleno z

Vlákna

## <a name="disable-azure-iot-defender-iot-micro-agent"></a>Zakázání Azure IoT Defenderu IoT-Micro-Agent

**nx_azure_iot_security_module_disable**


### <a name="prototype"></a>Prototype

```c
UINT nx_azure_iot_security_module_disable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Description

Tato rutina zakáže podsystém Azure IoT Defender – IoT-Micro-agent.

### <a name="parameters"></a>Parametry

| Název | Description |
|---------|---------|
| nx_azure_iot_ptr [in]    | Ukazatel na `NX_AZURE_IOT` . Pokud je hodnota NULL, je zakázaná instance singleton. |

### <a name="return-values"></a>Vrácené hodnoty

|Vrácené hodnoty  |Description |
|---------|---------|
|NX_AZURE_IOT_SUCCESS     |   Po úspěšném zakázání modulu zabezpečení Azure IoT došlo k úspěšnému vypnutí.      |
|NX_AZURE_IOT_INVALID_PARAMETER   |  Instance Azure IoT Hub se liší od složené instance s jedním prvkem.       |
|NX_AZURE_IOT_FAILURE    |  Kvůli vnitřní chybě se nepovedlo zakázat modul zabezpečení Azure IoT.       |

### <a name="allowed-from"></a>Povoleno z

Vlákna


## <a name="next-steps"></a>Další kroky

Další informace o tom, jak začít s Azure RTO Defenderem IoT-Micro-agent, najdete v následujících článcích:

- Přečtěte si téma Defender for IoT RTO Defender-IoT-Micro-agent [Overview](iot-security-azure-rtos.md).
