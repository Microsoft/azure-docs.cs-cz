---
ms.openlocfilehash: 3196d377c09ca0cce24093710bc4be13fb21d2e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99531951"
---
Ujistěte se, že jste dokončili postup [přípravy na monitorování událostí](../../../detect-motion-emit-events-quickstart.md#prepare-to-monitor-events).

![Spustit sledování vestavěného koncového bodu události](../../../media/quickstarts/start-monitoring-iothub-events.png)

> [!NOTE]
> Můžete být vyzváni k zadání předdefinovaných informací koncového bodu pro IoT Hub. Chcete-li získat tyto informace, v Azure Portal přejděte do IoT Hub a vyhledejte v levém navigačním podokně možnost **Předdefinované koncové body** . Klikněte na něj a vyhledejte **koncový bod kompatibilní** s centrem událostí v části **koncový bod kompatibilní** s centrem událostí. Zkopírujte a použijte text v poli. Koncový bod bude vypadat přibližně takto:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```
