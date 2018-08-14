---
title: Simulace Azure IoT Edge v Linuxu | Microsoft Docs
description: V tomto rychlém startu se naučíte na zařízení IoT Edge vzdáleně nasadit předem připravený kód.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 07/02/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: dfbe931bbe5887e9c0545558c4d2b2565718dd0a
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578486"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-x64-device"></a>Rychlý start: Nasazení prvního modulu IoT Edge na zařízení Linux x64

Azure IoT Edge umožňuje na zařízení analyzovat a zpracovávat data, abyste je nemuseli všechna odesílat do cloudu. V kurzech o IoT Edge si ukážeme, jak nasazovat různé typy modulů, ale napřed potřebujete testovací zařízení. 

V tomto rychlém startu se naučíte:

1. Vytvořit IoT Hub.
2. Zaregistrovat zařízení IoT Edge do IoT Hubu.
3. Spustit modul runtime IoT Edge.
4. Vzdáleně nasadit modul na zařízení IoT Edge.

![Architektura kurzu][2]

V tomto rychlém startu změníte svůj linuxový nebo virtuální počítač na zařízení IoT Edge. Potom můžete modul nasadit z webu Azure Portal do svého zařízení. Modul, který v tomto rychlém kurzu nasadíte, je simulovaný snímač, který generuje údaje o teplotě, vlhkosti a atmosferickém tlaku. Další kurzy o Azure IoT Edge vycházejí z tohoto kurzu. V něm nasadíte moduly, které analyzují simulovaná data kvůli získání obchodních informací. 

Pokud nemáte aktivní předplatné Azure, vytvořte si napřed [bezplatný účet][lnk-account].

## <a name="prerequisites"></a>Požadavky

V tomto rychlém startu se jako zařízení IoT Edge používá počítač s Linuxem. Pokud pro účely testování žádný k dispozici nemáte, postupujte podle pokynů v tématu [Vytvoření virtuálního počítače s Linuxem na webu Azure Portal](../virtual-machines/linux/quick-create-portal.md). 
* Není potřeba provádět postup instalace a spuštění webového serveru. Po připojení k virtuálnímu počítači můžete skončit.  
* Vytvořte virtuální počítač v nové skupině prostředků, kterou můžete použít při vytváření zbývajících prostředků Azure pro účely tohoto rychlého startu. Použijte pro ni nějaký rozpoznatelný název, například *IoTEdgeResources*. 
* K testování IoT Edge nepotřebujete příliš velký virtuální počítač. Například velikost **B1ms** je dostatečná. 

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

V tomto rychlém startu nejprve na webu Azure Portal vytvoříte službu IoT Hub.
![Vytvoření IoT Hubu][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Registrace zařízení IoT Edge

Zaregistrujte zařízení IoT Edge do nově vytvořeného IoT Hubu.
![Registrace zařízení][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]


## <a name="install-and-start-the-iot-edge-runtime"></a>Instalace a spuštění modulu runtime IoT Edge

Nainstalujte na zařízení modul runtime Azure IoT Edge a spusťte ho. 
![Registrace zařízení][5]

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Skládá se ze tří částí. **Proces démon zabezpečení IoT Edge**, který se spustí při každém restartování a spuštění zařízení Edge tím, že se spustí agent IoT Edge. **Agent IoT Edge** umožňuje nasadit a monitorovat moduly na zařízení IoT Edge, včetně centra služby IoT Edge. Druhým je **IoT Edge Hub**, který na zařízení IoT Edge řídí komunikaci mezi moduly a také mezi zařízením a IoT Hubem. 

Na počítači nebo virtuálním počítači s Linuxem, který jste si připravili pro tento rychlý start, proveďte následující kroky. 

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

Aktualizujte nástroj **apt-get**.

   ```bash
   sudo apt-get update
   ```

Nainstalujte kontejnerový modul runtime **Moby**.

   ```bash
   sudo apt-get install moby-engine
   ```

Nainstalujte příkazy rozhraní příkazového řádku pro Moby. 

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

3. Přidejte připojovací řetězec zařízení IoT Edge. Vyhledejte proměnnou **device_connection_string** a aktualizujte její hodnotu řetězcem, který jste zkopírovali po registraci zařízení.

4. Uložte soubor a zavřete ho. 

   `CTRL + X`, `Y`, `Enter`

4. Restartujte proces démon zabezpečení IoT Edge.

   ```bash
   sudo systemctl restart iotedge
   ```

5. Ověřte, že Edge Security Daemon běží jako systémová služba.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Kontrola, že Edge Daemon běží jako systémová služba](./media/quickstart-linux/iotedged-running.png)

   Protokoly služby Edge Security Daemon také můžete zobrazit, když spustíte následující příkaz:

   ```bash
   journalctl -u iotedge
   ```

6. Zobrazte moduly spuštěné na vašem zařízení. 

   >[!TIP]
   >Abyste mohli spouštět příkazy `iotedge`, je potřeba nejprve použít *sudo*. Odhlaste se z počítače a znovu se přihlaste, aby se aktualizovala oprávnění. Pak budete moct příkazy `iotedge` spouštět bez zvýšených oprávnění. 

   ```bash
   sudo iotedge list
   ```

   ![Zobrazení jednoho modulu na zařízení](./media/quickstart-linux/iotedge-list-1.png)

## <a name="deploy-a-module"></a>Nasazení modulu

Pokud budete zařízení Azure IoT Edge spravovat v cloudu, můžete nasadit modul, který bude odesílat telemetrická data do služby IoT Hub.
![Registrace zařízení][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

V tomto rychlém startu jste vytvořili nové zařízení IoT Edge a nainstalovali jste na něj modul runtime IoT Edge. Pak jste použili Azure Portal k doručení modulu IoT Edge a jeho spuštění na zařízení, aniž byste museli měnit samotné zařízení. V tomto případě doručený modul vytvoří data o prostředí, která použijete pro tyto kurzy. 

Na počítači, na kterém běží simulované zařízení, znovu otevřete příkazový řádek. Zkontrolujte, že modul, který jste nasadili z cloudu, běží na zařízení IoT Edge:

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

K zobrazení telemetrických údajů, které zařízení odesílá, můžete použít také [rozšíření Azure IoT Toolkit pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat dalšími kurzy o IoT Edge, použijte zařízení, které jste zaregistrovali a nastavili v tomto rychlém startu. Jinak můžete odstranit prostředky Azure, které jste vytvořili, a odebrat modul runtime IoT Edge ze zařízení. 

### <a name="delete-azure-resources"></a>Odstranění prostředků Azure

Pokud jste virtuální počítač a centrum IoT vytvořili v nové skupině prostředků, můžete odstranit tuto skupinu a všechny související prostředky. Pokud chcete z této skupiny prostředků něco zachovat, odstraňte pouze jednotlivé prostředky, které chcete vyčistit. 

Pokud chcete odebrat skupinu prostředků, postupujte následovně: 

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com) a klikněte na **Skupiny prostředků**.
2. Do textového pole **Filtrovat podle názvu...** zadejte název skupiny prostředků obsahující vaši službu IoT Hub. 
3. V seznamu výsledků klikněte na **...** napravo od vaší skupiny prostředků a pak na **Odstranit skupinu prostředků**.
4. Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Potvrďte odstranění tím, že znovu zadáte název vaší skupiny prostředků, a pak klikněte na **Odstranit**. Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.

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
   sudo apt-get remove --purge moby
   ```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nové zařízení IoT Edge a použili jste cloudové rozhraní Azure IoT Edge k nasazení kódu do zařízení. Teď máte simulované zařízení, které generuje nezpracovaná data o prostředí. 

Tento rychlý start je předpokladem všech dalších kurzů o IoT Edge. Pokračujte některým z dalších kurzů, ve kterých poznáte další způsoby, jak vám může Azure IoT Edge pomoci přeměnit data na obchodní informace o hraničním zařízení.

> [!div class="nextstepaction"]
> [Filtrování dat snímače pomocí funkce Azure](tutorial-deploy-function.md)


<!-- Images -->
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
[lnk-account]: https://azure.microsoft.com/free
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
