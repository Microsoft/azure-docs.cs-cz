---
ms.openlocfilehash: 3f92bae608284c8b619be34a0e08f15e831bf88e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101750365"
---
V kroku [generování a nasazení IoT Edge manifestu nasazení](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) rozbalte v části Visual Studio Code uzel **lva-Sample-Device** v rámci služby **Azure IoT Hub** (v části vlevo dole). Měly by se zobrazit následující moduly nasazené:

* Modul Live video Analytics s názvem `lvaEdge`
* `rtspsim`Modul, který simuluje Server RTSP, který funguje jako zdroj živého informačního kanálu videa

  ![Moduly](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> Výše uvedené kroky předpokládají, že používáte virtuální počítač vytvořený instalačním skriptem. Pokud místo toho používáte vlastní hraniční zařízení, přečtěte si hraniční zařízení a spusťte následující příkazy s **právy správce**, abyste mohli načíst a uložit ukázkový videosoubor, který se používá pro tento rychlý Start:  

```
mkdir /home/lvaedgeuser/samples
mkdir /home/lvaedgeuser/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaedgeuser/samples/input/camera-300s.mkv  
chown -R lvalvaedgeuser:localusergroup /home/lvaedgeuser/samples/  
```
