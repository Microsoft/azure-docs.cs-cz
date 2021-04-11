---
ms.openlocfilehash: 94044e95e83742487a0d4d650814a5324f07011a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101750433"
---
Manifest nasazení definuje, které moduly jsou nasazeny do hraničního zařízení. Definuje také nastavení konfigurace pro tyto moduly. 

Pomocí těchto kroků vygenerujte manifest ze souboru šablony a potom ho nasaďte do hraničního zařízení.

1. Otevřete Visual Studio Code.
1. Vedle podokna **Azure IoT Hub** vyberte ikonu **Další akce** a nastavte připojovací řetězec IoT Hub. Můžete zkopírovat řetězec z *Src/Cloud-to-Device-Console-App/appsettings.jsv* souboru. 

    ![Nastavení připojovacího řetězce IOT](../../../media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> Můžete být vyzváni k zadání předdefinovaných informací koncového bodu pro IoT Hub. Chcete-li získat tyto informace, v Azure Portal přejděte do IoT Hub a vyhledejte v levém navigačním podokně možnost **Předdefinované koncové body** . Klikněte na něj a vyhledejte **koncový bod kompatibilní** s centrem událostí v části **koncový bod kompatibilní** s centrem událostí. Zkopírujte a použijte text v poli. Koncový bod bude vypadat přibližně takto:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. Klikněte pravým tlačítkem na **Src/Edge/deployment.template.jsna** a vyberte **generovat manifest nasazení IoT Edge**.

    ![Vygenerovat manifest nasazení IoT Edge](../../../media/quickstarts/generate-iot-edge-deployment-manifest.png)

    Tato akce by měla vytvořit soubor manifestu s názvem *deployment.amd64.js* ve složce *Src/Edge/config* .
1. Klikněte pravým tlačítkem na **Src/Edge/config/deployment.amd64.jsna**, vyberte **vytvořit nasazení pro jedno zařízení** a pak vyberte název hraničního zařízení.

    ![Vytvoření nasazení pro jedno zařízení](../../../media/quickstarts/create-deployment-single-device.png)

1. Až se zobrazí výzva k výběru zařízení IoT Hub, z rozevírací nabídky zvolte **lva-Sample-Device** .
1. Po přibližně 30 sekundách se v levém dolním rohu okna aktualizují Azure IoT Hub. Hraniční zařízení nyní zobrazuje následující nasazené moduly:

    * Live video Analytics na IoT Edge (název modulu `lvaEdge` )
    * Simulátor protokolu RTSP (Real-Time streaming Protocol) (název modulu `rtspsim` )

Modul simulátoru RTSP simuluje živý Stream videa pomocí videosouboru, který jste zkopírovali do hraničního zařízení, když jste spustili [skript pro nastavení prostředků Live video Analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). 

> [!NOTE]
> Pokud místo toho, které jste zřídili pomocí našeho skriptu pro instalaci, používáte vlastní hraniční zařízení, přečtěte si příslušné hraniční zařízení a spusťte následující příkazy s **právy správce**, abyste mohli načíst a uložit ukázkový videosoubor, který se používá pro tento rychlý Start:  

```
mkdir /home/lvaedgeuser/samples      
mkdir /home/lvaedgeuser/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaedgeuser/samples/input/camera-300s.mkv  
chown -R lvalvaedgeuser:localusergroup /home/lvaedgeuser/samples/  
```
V této fázi jsou moduly nasazeny, ale nejsou aktivní žádné mediální grafy.
