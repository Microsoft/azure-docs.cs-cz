---
title: Rozhraní API modulu zabezpečení pro Azure RTOS
description: Referenční rozhraní API pro modul zabezpečení pro Azure RTO.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: cec28f9290808836ec2dfd334b23fe8c76df03fc
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120058"
---
# <a name="security-module-for-azure-rtos-api"></a>Rozhraní API modulu zabezpečení pro Azure RTOS 

Toto rozhraní API je určené pro použití s modulem zabezpečení pouze pro Azure RTO. Další zdroje najdete v [modulu zabezpečení prostředku GitHubu Azure RTO](https://github.com/azure-rtos/azure-iot-preview/releases). 

## <a name="enable-security-module-for-azure-rtos"></a>Povolení modulu zabezpečení pro Azure RTO

**nx_azure_iot_security_module_enable**

### <a name="prototype"></a>Prototype

```c
UINT nx_azure_iot_security_module_enable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Popis

Tato rutina umožňuje podsystém modulu zabezpečení Azure IoT. Interní Stavový počítač spravuje shromažďování událostí zabezpečení a odesílá je do Azure IoT Hub. Pro správu shromažďování dat je vyžadována pouze jedna instance NX_AZURE_IOT_SECURITY_MODULE.

### <a name="parameters"></a>Parametry

| Název | Popis |
|---------|---------|
| nx_azure_iot_ptr [in]    | Ukazatel na `NX_AZURE_IOT` .  |

### <a name="return-values"></a>Vrácené hodnoty

|Vrácené hodnoty  |Popis |
|---------|---------|
|NX_AZURE_IOT_SUCCESS|   Modul zabezpečení Azure IoT se úspěšně povolil.     |
|NX_AZURE_IOT_FAILURE   |  Kvůli vnitřní chybě se nepovedlo povolit modul zabezpečení Azure IoT.    |
|NX_AZURE_IOT_INVALID_PARAMETER   |  Modul zabezpečení vyžaduje platnou instanci #NX_AZURE_IOT.      |

### <a name="allowed-from"></a>Povoleno z

Vlákna

## <a name="disable-azure-iot-security-module"></a>Zakázat modul zabezpečení Azure IoT

**nx_azure_iot_security_module_disable**


### <a name="prototype"></a>Prototype

```c
UINT nx_azure_iot_security_module_disable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Popis

Tato rutina zakáže podsystém modulu zabezpečení Azure IoT.

### <a name="parameters"></a>Parametry

| Název | Popis |
|---------|---------|
| nx_azure_iot_ptr [in]    | Ukazatel na `NX_AZURE_IOT` . Pokud je hodnota NULL, je zakázaná instance singleton. |

### <a name="return-values"></a>Vrácené hodnoty

|Vrácené hodnoty  |Popis |
|---------|---------|
|NX_AZURE_IOT_SUCCESS     |   Po úspěšném zakázání modulu zabezpečení Azure IoT došlo k úspěšnému vypnutí.      |
|NX_AZURE_IOT_INVALID_PARAMETER   |  Instance Azure IoT Hub se liší od složené instance s jedním prvkem.       |
|NX_AZURE_IOT_FAILURE    |  Kvůli vnitřní chybě se nepovedlo zakázat modul zabezpečení Azure IoT.       |

### <a name="allowed-from"></a>Povoleno z

Vlákna


## <a name="next-steps"></a>Další kroky

Další informace o tom, jak začít s modulem zabezpečení Azure RTO, najdete v následujících článcích:

- Projděte si [Přehled](iot-security-azure-rtos.md)modulu Security Defender pro IoT RTO.