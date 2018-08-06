---
title: Simulace Azure IoT Edge ve Windows | Microsoft Docs
description: Instalace modulu runtime Azure IoT Edge na simulované zařízení ve Windows a nasazení prvního modulu
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 06/08/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 28d50ac3a4c080062c12c11977eebb61b0e52eed
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412531"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Rychlý start: Nasazení prvního modulu IoT Edge z webu Azure Portal do zařízení s Windows – Preview

Azure IoT Edge umožňuje na zařízení analyzovat a zpracovávat data, abyste je nemuseli všechna odesílat do cloudu. V kurzech o IoT Edge si ukážeme, jak nasazovat různé typy modulů, ale napřed potřebujete testovací zařízení. 

V tomto rychlém startu se naučíte:

1. Vytvořit IoT Hub
2. Zaregistrovat zařízení IoT Edge
3. Spustit modul runtime IoT Edge
4. Nasadit modul

![Architektura kurzu][2]

Modul, který v tomto rychlém startu nasadíte, je simulovaný snímač, který generuje údaje o teplotě, vlhkosti a atmosferickém tlaku. Další kurzy o Azure IoT Edge vycházejí z tohoto kurzu. V něm nasadíte moduly, které analyzují simulovaná data kvůli získání obchodních informací. 

>[!NOTE]
>Modul runtime IoT Edge ve Windows je ve verzi [Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud nemáte aktivní předplatné Azure, vytvořte si napřed [bezplatný účet][lnk-account].

## <a name="prerequisites"></a>Požadavky

Předpokladem tohoto rychlého startu je, že k simulaci zařízení IoT používáte počítač nebo virtuální počítač s Windows. Pokud na virtuálním počítači běží Windows, povolte [vnořenou virtualizaci][lnk-nested] a přidělte alespoň 2 GB paměti. 

Počítač, který používáte jako zařízení IoT Edge, musí splňovat následující požadavky:

1. Zkontrolujte, že používáte podporovanou verzi Windows:
   * Windows 10 nebo novější
   * Windows Server 2016 nebo novější
2. Nainstalujte [Docker for Windows][lnk-docker] a zkontrolujte, že běží.
3. Nakonfigurujte Docker na používání [kontejnerů Linuxu](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

V tomto rychlém startu nejprve na webu Azure Portal vytvoříte IoT Hub.
![Vytvoření IoT Hubu][3]

Vytvořte IoT Hub ve skupině prostředků, pomocí které můžete udržovat a spravovat všechny prostředky vytvořené v tomto rychlém startu. Použijte pro ni nějaký snadno zapamatovatelný název, například **IoTEdgeResources**. Když umístíte všechny prostředky používané v těchto rychlých startech a kurzech do skupiny, můžete je spravovat společně. Až testování dokončíte, můžete je snadno odebrat. 

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Registrace zařízení IoT Edge

Zaregistrujte zařízení IoT Edge do nově vytvořeného IoT Hubu.
![Registrace zařízení][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>Instalace a spuštění modulu runtime IoT Edge

Nainstalujte na zařízení IoT Edge modul runtime Azure IoT Edge a spusťte ho. 
![Registrace zařízení][5]

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Skládá se ze tří částí. **Proces démon zabezpečení IoT Edge**, který se spustí při každém restartování a spuštění zařízení Edge tím, že se spustí agent IoT Edge. **Agent IoT Edge** umožňuje nasadit a monitorovat moduly na zařízení IoT Edge, včetně centra služby IoT Edge. Druhým je **IoT Edge Hub**, který na zařízení IoT Edge řídí komunikaci mezi moduly a také mezi zařízením a IoT Hubem. 

>[!NOTE]
>Instalační skript se vyvíjí. Prozatím se tedy postup instalace v této části provádí ručně. 

Podle pokynů v této části se nakonfiguruje modul runtime IoT Edge s kontejnery Linuxu. Pokud chcete použít kontejnery Windows, přečtěte si téma [Instalace modulu runtime Azure IoT Edge ve Windows pro použití s kontejnery Windows](how-to-install-iot-edge-windows-with-windows.md).

### <a name="download-and-install-the-iot-edge-service"></a>Stažení a instalace služby IoT Edge

1. Na svém zařízení IoT Edge spusťte PowerShell jako správce.

2. Stáhněte balíček služby IoT Edge.

   ```powershell
   Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
   Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
   Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
   rmdir C:\ProgramData\iotedge\iotedged-windows
   $sysenv = "HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment"
   $path = (Get-ItemProperty -Path $sysenv -Name Path).Path + ";C:\ProgramData\iotedge"
   Set-ItemProperty -Path $sysenv -Name Path -Value $path
   ```

3. Nainstalujte vcruntime.

  ```powershell
  Invoke-WebRequest -useb https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe -o vc_redist.exe
  .\vc_redist.exe /quiet /norestart
  ```

4. Vytvořte a spusťte službu IoT Edge.

   ```powershell
   New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
   Start-Service iotedge
   ```

5. Přidejte výjimky brány firewall pro porty, které používá služba IoT Edge.

   ```powershell
   New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
   ```

6. Vytvořte nový soubor **iotedge.reg** a otevřete ho v textovém editoru. 

7. Přidejte do souboru následující obsah a uložte ho. 

   ```input
   Windows Registry Editor Version 5.00
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
   "CustomSource"=dword:00000001
   "EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
   "TypesSupported"=dword:00000007
   ```

8. Přejděte k souboru v Průzkumníku souborů a dvakrát na něj klikněte. Tím se změny importují do registru systému Windows. 

### <a name="configure-the-iot-edge-runtime"></a>Konfigurace modulu runtime IoT Edge 

Nakonfigurujte modul runtime s použitím připojovacího řetězce zařízení IoT Edge, který jste si zkopírovali při registraci nového zařízení. Pak nakonfigurujte síť modulu runtime. 

1. Otevřete konfigurační soubor IoT Edge, který se nachází v umístění `C:\ProgramData\iotedge\config.yaml`. Tento soubor je chráněný, takže otevřete textový editor (například Poznámkový blok) jako správce a pak v editoru otevřete soubor. 

2. Vyhledejte část **provisioning** (zřizování) a aktualizujte hodnotu **device_connection_string** s použitím připojovacího řetězce zařízení, který jste si zkopírovali z podrobností o zařízení IoT Edge. 

3. V okně PowerShellu s oprávněními správce načtěte název hostitele vašeho zařízení IoT Edge a zkopírujte výstup. 

   ```powershell
   hostname
   ```

4. V konfiguračním souboru vyhledejte část **Edge device hostname** (Název hostitele zařízení Edge). Aktualizujte hodnotu **hostname** s použitím názvu hostitele, který jste si zkopírovali z PowerShellu.

3. V okně PowerShellu s oprávněními správce načtěte IP adresu vašeho zařízení IoT Edge. 

   ```powershell
   ipconfig
   ```

4. Z výstupu zkopírujte hodnotu **IPv4 Address** v části **vEthernet (DockerNAT)**. 

5. Vytvořte proměnnou prostředí **IOTEDGE_HOST** a nahraďte *\<ip_address\>* IP adresou vašeho zařízení IoT Edge. 

  ```powershell
  [Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://<ip_address>:15580")
  ```

  Zachovejte proměnnou prostředí mezi restartováními.

  ```powershell
  SETX /M IOTEDGE_HOST "http://<ip_address>:15580"
  ```

6. V souboru `config.yaml` vyhledejte část **Connect settings** (Nastavení připojení). Aktualizujte hodnoty **management_uri** a **workload_uri** s použitím vaší IP adresy a portů, které jste otevřeli v předchozí části. Nahraďte **\<GATEWAY_ADDRESS\>** IP adresou z DockerNAT, kterou jste zkopírovali.

   ```yaml
   connect: 
     management_uri: "http://<GATEWAY_ADDRESS>:15580"
     workload_uri: "http://<GATEWAY_ADDRESS>:15581"
   ```

7. Vyhledejte část **Listen settings** (Nastavení naslouchání) a přidejte stejné hodnoty pro **management_uri** a **workload_uri**. 

   ```yaml
   listen:
     management_uri: "http://<GATEWAY_ADDRESS>:15580"
     workload_uri: "http://<GATEWAY_ADDRESS>:15581"
   ```

8. Vyhledejte část **Moby Container Runtime settings** (Nastavení modulu runtime kontejneru Moby) a ověřte, že hodnota **network** není zakomentovaná a že je nastavená na **azure-iot-edge**.

   ```yaml
   moby_runtime:
     docker_uri: "npipe://./pipe/docker_engine"
     network: "azure-iot-edge"
   ```
   
9. Uložte konfigurační soubor. 

10. V PowerShellu restartujte službu IoT Edge.

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

### <a name="view-the-iot-edge-runtime-status"></a>Zobrazení stavu modulu runtime IoT Edge

Ověřte, že se modul runtime úspěšně nainstaloval a nakonfiguroval.

1. Zkontrolujte stav služby IoT Edge.

   ```powershell
   Get-Service iotedge
   ```

2. Pokud potřebujete řešit potíže se službou, načtěte protokoly služby. 

   ```powershell
   # Displays logs from today, newest at the bottom.

   Get-WinEvent -ea SilentlyContinue `
    -FilterHashtable @{ProviderName= "iotedged";
      LogName = "application"; StartTime = [datetime]::Today} |
    select TimeCreated, Message |
    sort-object @{Expression="TimeCreated";Descending=$false} |
    format-table -autosize -wrap
   ```

3. Zobrazte všechny moduly spuštěné na vašem zařízení IoT Edge. Vzhledem k tomu, že jde o první spuštění služby, měl by se zobrazit pouze spuštěný modul **edgeAgent**. Modul edgeAgent se spouští ve výchozím nastavení a pomáhá s instalací a spouštěním všech dalších modulů, které do zařízení nasadíte. 

   ```powershell
   iotedge list
   ```

   ![Zobrazení jednoho modulu na zařízení](./media/quickstart/iotedge-list-1.png)

## <a name="deploy-a-module"></a>Nasazení modulu

Pokud budete zařízení Azure IoT Edge spravovat v cloudu, můžete nasadit modul, který bude odesílat telemetrická data do služby IoT Hub.
![Registrace zařízení][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

V tomto rychlém startu jste vytvořili nové zařízení IoT Edge a nainstalovali jste na něj modul runtime IoT Edge. Pak jste použili Azure Portal k doručení modulu IoT Edge a jeho spuštění na zařízení, aniž byste museli měnit samotné zařízení. V tomto případě doručený modul vytvoří data o prostředí, která použijete pro tyto kurzy. 

Zkontrolujte, že na zařízení IoT Edge běží modul, který jste nasadili z cloudu. 

```powershell
iotedge list
```

   ![Zobrazení tří modulů na zařízení](./media/quickstart/iotedge-list-2.png)

Prohlédněte si zprávy, které posílá modul tempSensor do cloudu. 

```powershell
iotedge logs tempSensor -f
```

  ![Zobrazení dat z modulu](./media/quickstart/iotedge-logs.png)

K zobrazení zpráv, které přijímá vaše centrum IoT, můžete použít také [nástroj IoT Hub Explorer][lnk-iothub-explorer] nebo [rozšíření Azure IoT Toolkit pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat dalšími kurzy o IoT Edge, použijte zařízení, které jste zaregistrovali a nastavili v tomto rychlém startu. Jinak můžete odstranit prostředky Azure, které jste vytvořili, a odebrat modul runtime IoT Edge ze zařízení. 

### <a name="delete-azure-resources"></a>Odstranění prostředků Azure

Pokud jste virtuální počítač a centrum IoT vytvořili v nové skupině prostředků, můžete odstranit tuto skupinu a všechny související prostředky. Pokud chcete z této skupiny prostředků něco zachovat, odstraňte pouze jednotlivé prostředky, které chcete vyčistit. 

Pokud chcete odebrat skupinu prostředků, postupujte následovně: 

1. Přihlaste se k webu [Azure Portal ](https://portal.azure.com) a klikněte na **Skupiny prostředků**.
2. Do textového pole **Filtrovat podle názvu...** zadejte název skupiny prostředků obsahující vaši službu IoT Hub. 
3. V seznamu výsledků klikněte na **...** napravo od vaší skupiny prostředků a pak na **Odstranit skupinu prostředků**.
4. Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Potvrďte odstranění tím, že znovu zadáte název vaší skupiny prostředků, a pak klikněte na **Odstranit**. Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.

### <a name="remove-the-iot-edge-runtime"></a>Odebrání modulu runtime IoT Edge

Pokud máte v úmyslu používat zařízení IoT Edge pro testování v budoucnu, ale v době, kdy se nepoužívá, chcete zastavit odesílání dat z modulu tempSensor do vašeho IoT Hubu, zastavte službu IoT Edge pomocí následujícího příkazu. 

   ```powershell
   Stop-Service iotedge -NoWait
   ```

Až budete připravení k opětovnému zahájení testování, můžete tuto službu znovu spustit.

   ```powershell
   Start-Service iotedge
   ```

Pokud chcete instalace ze zařízení odebrat, použijte následující příkazy.  

Odeberte modul runtime IoT Edge.

   ```powershell
   cmd /c sc delete iotedge
   rm -r c:\programdata\iotedge
   ```

Při odebrání modulu runtime IoT Edge se zastaví kontejnery, které vytvořil, ale na zařízení se zachovají. Zobrazte všechny kontejnery.

   ```powershell
   docker ps -a
   ```

Odstraňte kontejnery, které na vašem zařízení vytvořil modul runtime IoT Edge. Změňte název kontejneru tempSensor, pokud jste ho pojmenovali nějak jinak. 

   ```powershell
   docker rm -f tempSensor
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nové zařízení IoT Edge a použili jste cloudové rozhraní Azure IoT Edge k nasazení kódu do zařízení. Teď máte testovací zařízení, které generuje nezpracovaná data o prostředí. 

Jste připraveni pokračovat některým z dalších kurzů, ve kterých se seznámíte s dalšími způsoby, jak vám může Azure IoT Edge pomoct přeměnit data na obchodní informace na hraničním zařízení.

> [!div class="nextstepaction"]
> [Filtrování dat snímače pomocí funkce Azure](tutorial-deploy-function.md)

<!-- Images -->
[2]: ./media/quickstart/install-edge-full.png
[3]: ./media/quickstart/create-iot-hub.png
[4]: ./media/quickstart/register-device.png
[5]: ./media/quickstart/start-runtime.png
[6]: ./media/quickstart/deploy-module.png

<!-- Links -->
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-python]: https://www.python.org/downloads/
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-install-iotcore]: how-to-install-iot-core.md
[lnk-account]: https://azure.microsoft.com/free
