---
title: Rychlý start pro Azure IoT Edge + Windows | Microsoft Docs
description: Vyzkoušejte Azure IoT Edge spuštěním analýzy na simulovaném zařízení Edge.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/24/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: df22040de398810fd9250ef46da2f95b6915c4a9
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030654"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Rychlý start: Nasazení prvního modulu IoT Edge z webu Azure Portal do zařízení s Windows – Preview

V tomto rychlém startu použijeme cloudové rozhraní Azure IoT Edge ke vzdálenému nasazení předem připraveného kódu do zařízení IoT Edge. Abyste mohli tento úkol splnit, napřed použijte zařízení s Windows k simulaci zařízení IoT Edge a potom do něj nasaďte modul.

V tomto rychlém startu se naučíte:

1. Vytvořit IoT Hub.
2. Zaregistrovat zařízení IoT Edge do centra IoT Hub.
3. Nainstalovat na zařízení modul runtime Azure IoT Edge a spustit ho.
4. Vzdáleně nasadit modul na zařízení IoT Edge a odeslat telemetrická data do služby IoT Hub.

![Architektura kurzu][2]

Modul, který v tomto rychlém startu nasadíte, je simulovaný snímač, který generuje údaje o teplotě, vlhkosti a atmosferickém tlaku. Další kurzy o Azure IoT Edge vycházejí z tohoto kurzu. V něm nasadíte moduly, které analyzují simulovaná data kvůli získání obchodních informací. 

>[!NOTE]
>Modul runtime IoT Edge ve Windows je ve verzi [Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud nemáte aktivní předplatné Azure, vytvořte si napřed [bezplatný účet][lnk-account].

## <a name="prerequisites"></a>Požadavky

V tomto rychlém startu změníte svůj virtuální počítač nebo počítač s Windows na zařízení IoT Edge. Pokud na virtuálním počítači běží Windows, povolte [vnořenou virtualizaci][lnk-nested] a přidělte alespoň 2 GB paměti. 

Počítač, který používáte jako zařízení IoT Edge, musí splňovat následující požadavky:

1. Zkontrolujte, že používáte podporovanou verzi Windows:
   * Windows 10 nebo novější
   * Windows Server 2016 nebo novější
2. Nainstalujte [Docker for Windows][lnk-docker] a zkontrolujte, že běží.
3. Nakonfigurujte Docker na používání [kontejnerů Linuxu](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

K dokončení řady kroků v tomto rychlém startu použijete Azure CLI. Azure IoT má rozšíření, které nabízí další funkce. 

Přidejte rozšíření Azure IoT do instance služby Cloud Shell.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

V tomto rychlém startu nejprve na webu Azure Portal vytvoříte službu IoT Hub.
![Vytvoření IoT Hubu][3]

Pro tento rychlý start můžete použít bezplatnou úroveň IoT Hubu. Pokud jste službu IoT Hub někdy používali a máte vytvořené bezplatné centrum IoT, můžete ho použít. V každém předplatném může být jenom jeden bezplatný IoT Hub. 

1. Ve službě Azure Cloud Shell vytvořte skupinu prostředků. Následující kód vytvoří skupinu prostředků **TestResources** v oblasti **USA – západ**. Když umístíte všechny prostředky používané v těchto rychlých startech a kurzech do skupiny, můžete je spravovat společně. 

   ```azurecli-interactive
   az group create --name TestResources --location westus
   ```

1. V nové skupině prostředků vytvořte IoT Hub. Následující kód vytvoří bezplatné centrum **F1** ve skupině prostředků **TestResources**. Nahraďte *{hub_name}* jedinečným názvem vašeho centra IoT.

   ```azurecli-interactive
   az iot hub create --resource-group TestResources --name {hub_name} --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>Registrace zařízení IoT Edge

Zaregistrujte zařízení IoT Edge do nově vytvořeného IoT Hubu.
![Registrace zařízení][4]

Vytvořte identitu simulovaného zařízení, aby mohla komunikovat s centrem IoT. Zařízení IoT Edge se chovají jinak než typická zařízení IoT a jinak se i spravují, a proto je hned na začátku deklarujete jako zařízení IoT Edge. 

1. Ve službě Azure Cloud Shell zadejte následující příkaz, kterým v centru vytvoříte zařízení **myEdgeDevice**.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --hub-name {hub_name} --edge-enabled
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
  $env:Path += ";C:\ProgramData\iotedge"
  SETX /M PATH "$env:Path"
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

6. V souboru `config.yaml` vyhledejte část **Connect settings** (Nastavení připojení). Aktualizujte hodnoty **management_uri** a **workload_uri** s použitím vaší IP adresy a portů, které jste otevřeli v předchozí části. 

   ```yaml
   connect: 
     management_uri: "http://<ip_address>:15580"
     workload_uri: "http://<ip_address>:15581"
   ```

7. Vyhledejte část **Listen settings** (Nastavení naslouchání) a přidejte stejné hodnoty pro **management_uri** a **workload_uri**. 

   ```yaml
   listen:
     management_uri: "http://<ip_address>:15580"
     workload_uri: "http://<ip_address:15581"
   ```

8. Vyhledejte část **Moby Container Runtime settings** (Nastavení modulu runtime kontejneru Moby) a ověřte, že je hodnota **network** nastavená na `nat`.

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
    sort-object @{Expression="TimeCreated";Descending=$false}
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

Simulované zařízení, které jste nakonfigurovali v tomto rychlém startu, můžete použít k testování kurzů o IoT Edge. Pokud chcete zastavit odesílání dat z modulu tempSensor do centra IoT, pomocí následujícího příkazu zastavte službu IoT Edge a odstraňte kontejnery, které se vytvořily ve vašem zařízení. Až budete chtít znovu použít svůj počítač jako zařízení IoT Edge, nezapomeňte službu spustit. 

   ```powershell
   Stop-Service iotedge -NoWait
   docker rm -f $(docker ps -aq)
   ```

Až nebudete vytvořené prostředky Azure potřebovat, použijte následující příkaz, kterým odstraníte vytvořenou skupinu prostředků, včetně všech přidružených prostředků:

   ```azurecli-interactive
   az group delete --name TestResources
   ```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nové zařízení IoT Edge a použili jste cloudové rozhraní Azure IoT Edge k nasazení kódu do zařízení. Teď máte testovací zařízení, které generuje nezpracovaná data o prostředí. 

Jste připraveni pokračovat některým z dalších kurzů, ve kterých se seznámíte s dalšími způsoby, jak vám může Azure IoT Edge pomoct přeměnit data na obchodní informace na hraničním zařízení.

> [!div class="nextstepaction"]
> [Filtrování dat snímače pomocí funkce Azure](tutorial-deploy-function.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png
[2]: ./media/quickstart/install-edge-full.png
[3]: ./media/quickstart/create-iot-hub.png
[4]: ./media/quickstart/register-device.png
[5]: ./media/quickstart/start-runtime.png
[6]: ./media/quickstart/deploy-module.png


<!-- Links -->
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
