---
ms.openlocfilehash: 53052097fa6616f889b710c58488a9f7a616168d
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956249"
---
V kroku [generování a nasazení IoT Edge manifestu nasazení](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) rozbalte v části Visual Studio Code uzel **lva-Sample-Device** v rámci služby **Azure IoT Hub** (v části vlevo dole). Měly by se zobrazit následující moduly nasazené:

* Modul Live video Analytics s názvem `lvaEdge`
* `rtspsim`Modul, který simuluje Server RTSP, který funguje jako zdroj živého informačního kanálu videa

  ![Moduly](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> Výše uvedené kroky předpokládají, že používáte virtuální počítač vytvořený instalačním skriptem. Pokud místo toho používáte vlastní hraniční zařízení, přečtěte si hraniční zařízení a spusťte následující příkazy s **právy správce**, abyste mohli načíst a uložit ukázkový videosoubor, který se používá pro tento rychlý Start:  

```
mkdir /home/lvaadmin/samples
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
