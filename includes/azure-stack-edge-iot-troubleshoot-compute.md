---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 01/21/2021
ms.openlocfilehash: 3defa62c55bb5ab042ade816f611ea45b39a0117
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761560"
---
K řešení chyb souvisejících s výpočty použijte IoT Edge odezvy agenta modulu runtime. Tady je seznam možných odpovědí:

* 200 – OK
* 400 – konfigurace nasazení je poškozená nebo neplatná.
* 417 – zařízení nemá nastavenou konfiguraci nasazení.
* 412 – verze schématu v konfiguraci nasazení není platná.
* 406 – IoT Edge zařízení je offline nebo neodesílá zprávy o stavu.
* 500 – při IoT Edge modulu runtime došlo k chybě.

Další informace najdete v tématu [Agent IoT Edge](/azure/iot-edge/iot-edge-runtime?view=iotedge-2018-06&preserve-view=true#iot-edge-agent).