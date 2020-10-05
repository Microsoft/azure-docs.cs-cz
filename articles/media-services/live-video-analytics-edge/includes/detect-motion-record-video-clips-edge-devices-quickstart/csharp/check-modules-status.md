---
ms.openlocfilehash: e46a56742ab8b98c53c1cd05e840e8ad4b8a73da
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88682115"
---
V kroku [generování a nasazení IoT Edge manifestu nasazení](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) rozbalte v části Visual Studio Code uzel **lva-Sample-Device** v rámci služby **Azure IoT Hub** (v části vlevo dole). Měly by se zobrazit následující moduly nasazené:

* Modul Live video Analytics s názvem `lvaEdge`
* `rtspsim`Modul, který simuluje Server RTSP, který funguje jako zdroj živého informačního kanálu videa

  ![Moduly](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> Pokud místo toho, které jste zřídili pomocí našeho skriptu pro instalaci, používáte vlastní hraniční zařízení, přečtěte si příslušné hraniční zařízení a spusťte následující příkazy s **právy správce**, abyste mohli načíst a uložit ukázkový videosoubor, který se používá pro tento rychlý Start:  

```
mkdir /home/lvaadmin/samples
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
