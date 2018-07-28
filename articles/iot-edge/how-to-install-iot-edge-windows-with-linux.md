---
title: Jak nainstalovat Azure IoT Edge ve Windows s kontejnery Linuxu | Dokumentace Microsoftu
description: Azure IoT Edge pokyny k instalaci na Windows s kontejnery Linuxu
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: a56b2b12143a29637196d2239f648b78f1f8e763
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39307861"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-linux-containers"></a>Nainstalovat modul runtime Azure IoT Edge ve Windows pro použití s kontejnery Linuxu

Modul runtime Azure IoT Edge je nasadit na všechna zařízení IoT Edge. Skládá se ze tří částí. **Démon zabezpečení IoT Edge** poskytuje a udržuje standardy zabezpečení na hraniční zařízení. Proces démon spustí při každé spuštění a bootstraps zařízení spuštěním agenta IoT Edge. **Agenta IoT Edge** usnadňuje nasazení a monitorování modulů na hraniční zařízení, včetně hraničních zařízeních IoT hub. Druhým je **IoT Edge Hub**, který na zařízení IoT Edge řídí komunikaci mezi moduly a také mezi zařízením a IoT Hubem.

V tomto článku jsou uvedené kroky k instalaci modulu runtime Azure IoT Edge na vaše Windows x64 (AMD nebo Intel) systému. Podpora Windows je aktuálně ve verzi Preview.

>[!NOTE]
Pomocí kontejnerů Linuxu na systémy Windows není produkční doporučené nebo podporované konfigurace pro Azure IoT Edge. To však lze použít pro účely vývoje a testování.

## <a name="supported-windows-versions"></a>Podporované verze Windows
Azure IoT Edge může být používané pro vývoj a testování v následujících verzích Windows, při použití kontejnerů Linuxu:
  * Windows 10 nebo novější operační systémy.
  * Windows Server 2016 nebo nové serverové operační systémy.

## <a name="install-the-container-runtime"></a>Nainstalovat modul runtime kontejneru 

Azure IoT Edge se může spolehnout [OCI kompatibilní] [ lnk-oci] runtime kontejneru (např. Dockeru). 

Můžete použít [Docker pro Windows] [ lnk-docker-for-windows] pro vývoj a testování. Zkontrolujte Docker pro Windows je [nakonfigurován na použití kontejnery Linuxu][lnk-docker-config]

## <a name="install-the-azure-iot-edge-security-daemon"></a>Instalace démona zabezpečení Azure IoT Edge

>[!NOTE]
>Azure IoT Edge softwarové balíčky jsou souladu s licenčními podmínkami umístěný v balíčcích (v adresáři licencí). Přečtěte si licenční podmínky před použitím balíčku. Instalace a použití balíčku se považuje za svůj souhlas s těmito podmínkami. Pokud s licenčními podmínkami nesouhlasíte, nepoužívejte balíček.

### <a name="download-the-edge-daemon-package-and-install"></a>Stáhněte si balíček Edge démon a instalace

V okně Správce PowerShell spusťte následující příkazy:

```powershell
Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
rmdir C:\ProgramData\iotedge\iotedged-windows
$sysenv = "HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment"
$path = (Get-ItemProperty -Path $sysenv -Name Path).Path + ";C:\ProgramData\iotedge"
Set-ItemProperty -Path $sysenv -Name Path -Value $path
```

Nainstalujte s použitím vcruntime:

```powershell
Invoke-WebRequest -useb https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe -o vc_redist.exe
.\vc_redist.exe /quiet /norestart
 ```

Vytvoření a spuštění *iotedge* služby:

```powershell
New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
Start-Service iotedge
```

Přidáte výjimky brány Firewall pro porty používané službou:

```powershell
New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
```

Vytvoření **iotedge.reg** soubor s následujícím obsahem a importu v registru Windows na ni poklikáte nebo pomocí `reg import iotedge.reg` příkaz:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Nakonfigurujte funkce zabezpečení Azure IoT Edge

Proces démon lze konfigurovat pomocí konfiguračního souboru na `C:\ProgramData\iotedge\config.yaml`.

Hraniční zařízení můžete nakonfigurovat ručně pomocí [připojovací řetězec zařízení] [ lnk-dcs] nebo [automaticky prostřednictvím služby Azure Device Provisioning] [ lnk-dps].

* Ruční konfigurace, zrušte komentář **ruční** režim zřizování. Aktualizujte hodnotu **device_connection_string** připojovacím řetězcem z vašeho zařízení IoT Edge.

   ```yaml
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # provisioning: 
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   registration_id: "{registration_id}"
   ```

* Automatická konfigurace, zrušte komentář **dps** režim zřizování. Aktualizujte hodnoty **scope_id** a **registration_id** s hodnotami z vaší instance IoT Hub Device Provisioning a zařízení IoT Edge s čipem TPM. 

   ```yaml
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   provisioning: 
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     registration_id: "{registration_id}"
   ```

Název hraniční zařízení používá `hostname` příkazu v prostředí PowerShell a nastavte ji jako hodnotu **název hostitele:** v konfiguraci yaml. Příklad:

```yaml
  ###############################################################################
  # Edge device hostname
  ###############################################################################
  #
  # Configures the environment variable 'IOTEDGE_GATEWAYHOSTNAME' injected into
  # modules.
  #
  ###############################################################################

  hostname: "edgedevice-1"
```

V dalším kroku, zadejte IP adresu a port pro **workload_uri** a **management_uri** v **připojit:** a **naslouchání:** oddíly konfigurace.

Chcete-li získat IP adresu, zadejte `ipconfig` v prostředí PowerShell. Zkopírujte IP adresu **vEthernet (DockerNAT)**"rozhraní, jak je znázorněno v následujícím příkladu (ip adresa ve vašem systému může lišit):

![DockerNat][img-docker-nat]

Aktualizace **workload_uri** a **management_uri** v **připojit:** oddílu konfiguračního souboru. Nahraďte **\<GATEWAY_ADDRESS\>** IP adresou z DockerNAT, kterou jste zkopírovali. 

```yaml
connect:
  management_uri: "http://<GATEWAY_ADDRESS>:15580"
  workload_uri: "http://<GATEWAY_ADDRESS>:15581"
```

Zadejte stejné adresy **naslouchání:** oddílu.

```yaml
listen:
  management_uri: "http://<GATEWAY_ADDRESS>:15580"
  workload_uri: "http://<GATEWAY_ADDRESS>:15581"
```

V okně Powershellu Vytvořte proměnnou prostředí **IOTEDGE_HOST** s **management_uri** adresu.

```powershell
[Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://<GATEWAY_ADDRESS>:15580")
```

Zachovejte proměnnou prostředí mezi restartováními.

```powershell
SETX /M IOTEDGE_HOST "http://<GATEWAY_ADDRESS>:15580"
```

Nakonec se ujistěte, **sítě:** v nabídce **moby_runtime:** je komentář a nastavena na **azure-iot-edge**

```yaml
moby_runtime:
  docker_uri: "npipe://./pipe/docker_engine"
  network: "azure-iot-edge"
```

Uložit konfigurační soubor a restartujte službu:

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Pokud jste použili **ruční konfigurace** kroků v předchozí části, modul runtime IoT Edge by měla být úspěšně zřízený a spuštěný ve vašem zařízení. Pokud jste použili **automatickou konfiguraci** kroky, pak budete muset provést některé další kroky, tak, aby modul runtime můžete registraci zařízení ve službě IoT hub vaším jménem. Další pokyny najdete v článku [vytvoření a zřízení simulovaného zařízení TPM Edge ve Windows](how-to-auto-provision-simulated-device-windows.md#create-a-tpm-environment-variable).


Můžete zkontrolovat stav této služby IoT Edge: 

```powershell
Get-Service iotedge
```

Zkontrolujte protokoly služby z posledních 5 minut pomocí:

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

A seznam s moduly:

```powershell
iotedge list
```

## <a name="next-steps"></a>Další postup

Pokud máte problémy s modulu runtime Edge instalaci správně, rezervace [řešení potíží s] [ lnk-trouble] stránky.


<!-- Images -->
[img-docker-nat]: ./media/how-to-install-iot-edge-windows-with-linux/dockernat.png

<!-- Links -->
[lnk-docker-config]: https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-windows.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
[lnk-docker-for-windows]: https://www.docker.com/docker-windows

