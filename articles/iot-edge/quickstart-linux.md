---
title: Rychlý start pro Azure IoT Edge + Linux | Microsoft Docs
description: V tomto rychlém startu se naučíte na zařízení IoT Edge vzdáleně nasadit předem připravený kód.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 08/14/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: af291782585cf0211cf8beac54adc36fd9fe0d34
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "42022461"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-x64-device"></a>Rychlý start: Nasazení prvního modulu IoT Edge na zařízení Linux x64

Služba Azure IoT Edge posouvá výkon cloudu k zařízením Internetu věcí. V tomto rychlém startu se naučíte používat cloudové rozhraní ke vzdálenému nasazení předem připraveného kódu do zařízení IoT Edge.

V tomto rychlém startu se naučíte:

1. Vytvořit IoT Hub.
2. Zaregistrovat zařízení IoT Edge do centra IoT Hub.
3. Nainstalovat na zařízení modul runtime Azure IoT Edge a spustit ho.
4. Vzdáleně nasadit modul na zařízení IoT Edge.

![Architektura rychlého startu][2]

V tomto rychlém startu změníte svůj linuxový nebo virtuální počítač na zařízení IoT Edge. Potom můžete modul nasadit z webu Azure Portal do svého zařízení. Modul, který v tomto rychlém kurzu nasadíte, je simulovaný snímač, který generuje údaje o teplotě, vlhkosti a atmosferickém tlaku. Další kurzy o Azure IoT Edge vycházejí z tohoto kurzu. V něm nasadíte moduly, které analyzují simulovaná data kvůli získání obchodních informací. 

Pokud nemáte aktivní předplatné Azure, vytvořte si napřed [bezplatný účet][lnk-account].


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

K dokončení řady kroků v tomto rychlém startu použijete Azure CLI. Azure IoT má rozšíření, které nabízí další funkce. 

Přidejte rozšíření Azure IoT do instance služby Cloud Shell.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```
   
## <a name="prerequisites"></a>Požadavky

Cloudové prostředky: 

* Skupina prostředků pro správu všech prostředků, které v tomto rychlém startu použijete. 

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus
   ```

Zařízení IoT Edge:

* Virtuální počítač nebo zařízení s Linuxem, který bude fungovat jako zařízení IoT Edge. Pokud chcete vytvořit virtuální počítač v Azure, můžete rychle začít pomocí následujícího příkazu:

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image Canonical:UbuntuServer:16.04-LTS:latest --admin-username azureuser --generate-ssh-keys --size Standard_B1ms
   ```

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

V tomto rychlém startu nejprve pomocí Azure CLI vytvoříte centrum IoT. 

![Vytvoření IoT Hubu][3]

Pro tento rychlý start můžete použít bezplatnou úroveň IoT Hubu. Pokud jste službu IoT Hub někdy používali a máte vytvořené bezplatné centrum IoT, můžete ho použít. V každém předplatném může být jenom jeden bezplatný IoT Hub. 

Následující kód vytvoří bezplatné centrum **F1** ve skupině prostředků **IoTEdgeResources**. Nahraďte *{hub_name}* jedinečným názvem vašeho centra IoT.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 
   ```

   Pokud dojde k chybě kvůli tomu, že vaše předplatné již jedno bezplatné centrum obsahuje, změňte skladovou položku na **S1**. 

## <a name="register-an-iot-edge-device"></a>Registrace zařízení IoT Edge

Zaregistrujte zařízení IoT Edge do nově vytvořeného centra IoT. 
![Registrace zařízení][4]

Vytvořte identitu simulovaného zařízení, aby mohla komunikovat s centrem IoT. Identita zařízení se uchovává v cloudu a k přidružení fyzického zařízení k identitě zařízení se používá jedinečný připojovací řetězec zařízení. 

Zařízení IoT Edge se chovají jinak než typická zařízení IoT a jinak se i spravují, a proto je hned na začátku deklarujete jako zařízení IoT Edge. 

1. Ve službě Azure Cloud Shell zadejte následující příkaz, kterým v centru vytvoříte zařízení **myEdgeDevice**.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {hub_name} --device-id myEdgeDevice --edge-enabled
   ```

1. Načtěte připojovací řetězec svého zařízení, který propojí vaše fyzické zařízení s jeho identitou ve službě IoT Hub. 

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

1. Zkopírujte připojovací řetězec a uložte si ho. Tuto hodnotu použijete ke konfiguraci modulu runtime IoT Edge v další části. 


## <a name="install-and-start-the-iot-edge-runtime"></a>Instalace a spuštění modulu runtime IoT Edge

Nainstalujte na zařízení IoT Edge modul runtime Azure IoT Edge a spusťte ho. 
![Registrace zařízení][5]

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Skládá se ze tří částí. **Proces démon zabezpečení IoT Edge**, který se spustí při každém restartování a spuštění zařízení Edge tím, že se spustí agent IoT Edge. **Agent IoT Edge** umožňuje nasadit a monitorovat moduly na zařízení IoT Edge, včetně centra služby IoT Edge. Druhým je **IoT Edge Hub**, který na zařízení IoT Edge řídí komunikaci mezi moduly a také mezi zařízením a IoT Hubem. 

Během konfigurace modulu runtime zadáte připojovací řetězec zařízení. Použijte řetězec, který jste získali z Azure CLI. Tento řetězec přidruží vaše fyzické zařízení k identitě zařízení IoT Edge v Azure. 

Následující kroky proveďte na počítači nebo virtuálním počítači s Linuxem, který jste připravili, aby fungoval jako zařízení IoT Edge. 

### <a name="register-your-device-to-use-the-software-repository"></a>Registrace zařízení kvůli použití úložiště softwaru

Balíčky, které potřebujete ke spuštění modulu runtime IoT Edge, se spravují v úložišti softwaru. Nakonfigurujte v zařízení IoT Edge přístup k tomuto úložišti. 

Kroky popisované v této části platí pro zařízení s **Ubuntu 16.04**. Pokud chcete nastavit přístup k úložišti softwaru v jiných verzích Linuxu, podívejte se na [instalaci modulu runtime Azure IoT Edge v Linuxu (x64)](how-to-install-iot-edge-linux.md) nebo na [instalaci modulu runtime Azure IoT Edge v Linuxu (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md).

1. Na počítači, který používáte jako zařízení IoT Edge, nainstalujte konfiguraci úložiště.

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

2. Nainstalujte veřejný klíč pro přístup k úložišti.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-a-container-runtime"></a>Instalace kontejnerového modulu runtime

Modul runtime IoT Edge je sada kontejnerů. Logika, kterou nasadíte na zařízení IoT Edge je zabalená do kontejnerů. Připravte zařízení na tyto součásti tím, že nainstalujete kontejnerový modul runtime.

1. Aktualizujte nástroj **apt-get**.

   ```bash
   sudo apt-get update
   ```

2. Nainstalujte kontejnerový modul runtime **Moby**.

   ```bash
   sudo apt-get install moby-engine
   ```

3. Nainstalujte příkazy rozhraní příkazového řádku pro Moby. 

   ```bash
   sudo apt-get install moby-cli
   ```

### <a name="install-and-configure-the-iot-edge-security-daemon"></a>Instalace a konfigurace procesu démon zabezpečení IoT Edge

Proces démon zabezpečení se nainstaluje jako systémová služba, aby se modul runtime IoT Edge spustil při každém spuštění zařízení. Součástí instalace je také verze knihovny **hsmlib**, která umožňuje procesu démon zabezpečení, aby spolupracoval s hardwarovým zabezpečením zařízení. 

1. Stáhněte a nainstalujte si proces démon zabezpečení IoT Edge. 

   ```bash
   sudo apt-get update
   sudo apt-get install iotedge
   ```

2. Otevřete konfigurační soubor IoT Edge. Soubor je chráněný, a proto možná budete muset pro přístup použít zvýšená oprávnění.
   
   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

3. Přidejte připojovací řetězec zařízení IoT Edge. Vyhledejte proměnnou **device_connection_string** a aktualizujte její hodnotu řetězcem, který jste zkopírovali po registraci zařízení. Tento připojovací řetězec přidruží vaše fyzické zařízení k identitě zařízení, kterou jste vytvořili v Azure.

4. Uložte soubor a zavřete ho. 

   `CTRL + X`, `Y`, `Enter`

5. Restartujte proces démon zabezpečení IoT Edge, aby se projevily provedené změny.

   ```bash
   sudo systemctl restart iotedge
   ```

>[!TIP]
>Ke spouštění příkazů `iotedge` potřebujete zvýšená oprávnění. Vaše oprávnění se automaticky aktualizují, jakmile se po instalaci modulu runtime IoT Edge odhlásíte z počítače a poprvé se k němu opět přihlásíte. Do té doby před příkazy používejte **sudo**. 

### <a name="view-the-iot-edge-runtime-status"></a>Zobrazení stavu modulu runtime IoT Edge

Ověřte, že se modul runtime úspěšně nainstaloval a nakonfiguroval.

1. Ověřte, že Edge Security Daemon běží jako systémová služba.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Kontrola, že Edge Daemon běží jako systémová služba](./media/quickstart-linux/iotedged-running.png)

2. Pokud potřebujete řešit potíže se službou, načtěte protokoly služby. 

   ```bash
   journalctl -u iotedge
   ```

3. Zobrazte moduly spuštěné na vašem zařízení. 

   ```bash
   sudo iotedge list
   ```

   ![Zobrazení jednoho modulu na zařízení](./media/quickstart-linux/iotedge-list-1.png)

Vaše zařízení IoT Edge je teď nakonfigurované. Je připravené na spouštění modulů nasazených v cloudu. 

## <a name="deploy-a-module"></a>Nasazení modulu

Pokud budete zařízení Azure IoT Edge spravovat v cloudu, můžete nasadit modul, který bude odesílat telemetrická data do služby IoT Hub.
![Registrace zařízení][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

V tomto rychlém startu jste vytvořili nové zařízení IoT Edge a nainstalovali jste na něj modul runtime IoT Edge. Pak jste použili Azure Portal k doručení modulu IoT Edge a jeho spuštění na zařízení, aniž byste museli měnit samotné zařízení. V tomto případě doručený modul vytvoří data o prostředí, která použijete pro tyto kurzy. 

Znovu otevřete příkazový řádek na vašem zařízení IoT Edge. Zkontrolujte, že modul, který jste nasadili z cloudu, běží na zařízení IoT Edge:

   ```bash
   sudo iotedge list
   ```

   ![Zobrazení tří modulů na zařízení](./media/quickstart-linux/iotedge-list-2.png)

Prohlédněte si zprávy, které posílá modul tempSensor:

   ```bash
   sudo iotedge logs tempSensor -f 
   ```
Po odhlášení a přihlášení nemusíte v předchozím příkazu používat příkaz *sudo*.

![Zobrazení dat z modulu](./media/quickstart-linux/iotedge-logs.png)

Pokud je poslední řádek protokolu `Using transport Mqtt_Tcp_Only`, může modul senzoru teploty čekat na připojení k Edge Hubu. Zkuste modul ukončit a nechat agenta Edge, aby ho restartoval. K ukončení použijte příkaz `sudo docker stop tempSensor`.

K zobrazení telemetrických dat přicházejících do vašeho centra IoT můžete použít také [rozšíření Azure IoT Toolkit pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). 


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat dalšími kurzy o IoT Edge, použijte zařízení, které jste zaregistrovali a nastavili v tomto rychlém startu. Jinak můžete odstranit prostředky Azure, které jste vytvořili, a odebrat modul runtime IoT Edge ze zařízení. 

### <a name="delete-azure-resources"></a>Odstranění prostředků Azure

Pokud jste virtuální počítač a centrum IoT vytvořili v nové skupině prostředků, můžete odstranit tuto skupinu a všechny související prostředky. Pokud chcete z této skupiny prostředků něco zachovat, odstraňte pouze jednotlivé prostředky, které chcete vyčistit. 

Odeberte skupinu **IoTEdgeResources**. 

   ```azurecli-interactive
   az group delete --name IoTEdgeResources 
   ```

### <a name="remove-the-iot-edge-runtime"></a>Odebrání modulu runtime IoT Edge

Pokud chcete instalace ze zařízení odebrat, použijte následující příkazy.  

Odeberte modul runtime IoT Edge.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

Při odebrání modulu runtime IoT Edge se zastaví kontejnery, které vytvořil, ale na zařízení se zachovají. Zobrazte všechny kontejnery.

   ```bash
   sudo docker ps -a
   ```

Odstraňte kontejnery, které na vašem zařízení vytvořil modul runtime IoT Edge. Změňte název kontejneru tempSensor, pokud jste ho pojmenovali nějak jinak. 

   ```bash
   sudo docker rm -f tempSensor
   sudo docker rm -f edgeHub
   sudo docker rm -f edgeAgent
   ```

Odeberte kontejnerový modul runtime.

   ```bash
   sudo apt-get remove --purge moby-cli
   sudo apt-get remove --purge moby-engine
   ```

## <a name="next-steps"></a>Další kroky

Tento rychlý start je předpokladem všech dalších kurzů o IoT Edge. Pokračujte některým z dalších kurzů, ve kterých poznáte další způsoby, jak vám může Azure IoT Edge pomoci přeměnit data na obchodní informace o hraničním zařízení.

> [!div class="nextstepaction"]
> [Filtrování dat snímače pomocí funkce Azure](tutorial-deploy-function.md)



<!-- Images -->
[0]: ./media/quickstart-linux/cloud-shell.png
[1]: ./media/quickstart-linux/view-module.png
[2]: ./media/quickstart-linux/install-edge-full.png
[3]: ./media/quickstart-linux/create-iot-hub.png
[4]: ./media/quickstart-linux/register-device.png
[5]: ./media/quickstart-linux/start-runtime.png
[6]: ./media/quickstart-linux/deploy-module.png
[7]: ./media/quickstart-linux/iotedged-running.png
[8]: ./media/tutorial-simulate-device-linux/running-modules.png
[9]: ./media/tutorial-simulate-device-linux/sensor-data.png


<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
