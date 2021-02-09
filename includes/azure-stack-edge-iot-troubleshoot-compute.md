---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 02/05/2021
ms.openlocfilehash: b06b91e972fd07543cf02105360cb0400ef6b0f1
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831535"
---
K řešení chyb souvisejících s výpočty použijte IoT Edge odezvy agenta modulu runtime. Tady je seznam možných odpovědí:

* 200 – OK
* 400 – konfigurace nasazení je poškozená nebo neplatná.
* 417 – zařízení nemá nastavenou konfiguraci nasazení.
* 412 – verze schématu v konfiguraci nasazení není platná.
* 406 – IoT Edge zařízení je offline nebo neodesílá zprávy o stavu.
* 500 – při IoT Edge modulu runtime došlo k chybě.

Další informace najdete v tématu [Agent IoT Edge](/azure/iot-edge/iot-edge-runtime?view=iotedge-2018-06&preserve-view=true#iot-edge-agent).

Následující chyba se týká služby IoT Edge v Azure Stack Edge pro<!--/ Data Box Gateway--> device.

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>Výpočetní moduly mají neznámý stav a nejde je použít.

#### <a name="error-description"></a>Popis chyby

Všechny moduly v zařízení zobrazují neznámý stav a nelze je použít. Neznámý stav přetrvává při restartování.<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>Navrhované řešení

Odstraňte službu IoT Edge a znovu nasaďte moduly (y). Další informace najdete v tématu [Odebrání služby IoT Edge](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service).
