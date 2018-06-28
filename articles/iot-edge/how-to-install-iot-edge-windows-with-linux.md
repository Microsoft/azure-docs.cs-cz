---
title: Jak nainstalovat Azure IoT Edge v systému Windows s kontejnery Linux | Microsoft Docs
description: Azure IoT Edge pokyny k instalaci v systému Windows s kontejnery Linux
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: cd517d7e652b38c7ecf28a17657936698416413a
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035748"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-linux-containers"></a>Instalace modulu runtime Azure IoT Edge v systému Windows pro použití s kontejnery Linux

Modul runtime Azure IoT okraj je nasadit na všechna zařízení IoT okraj. Má tři součásti. **IoT Edge zabezpečení démon** poskytuje a udržuje standardy zabezpečení na hraniční zařízení. Démon se spustí na každém spuštění a bootstraps zařízení spuštěním agenta IoT okraj. **IoT Edge agenta** usnadňuje nasazení a monitorování modulů na hraniční zařízení, včetně IoT Edge hub. Druhým je **IoT Edge Hub**, který na zařízení IoT Edge řídí komunikaci mezi moduly a také mezi zařízením a IoT Hubem.

Tento článek obsahuje kroky k instalaci modulu runtime Azure IoT Edge na váš Windows x64 (AMD nebo Intel) systému. Podpora systému Windows je aktuálně ve verzi Preview.

>[!NOTE]
Použití kontejnerů Linux na systémy Windows není produkční doporučená nebo podporované konfigurace pro Azure IoT okraj. Můžete se ale, použít pro účely vývoje a testování.

## <a name="supported-windows-versions"></a>Podporované verze systému Windows
Azure IoT Edge lze použít pro vývoj a testování v následujících verzích systému Windows, při použití kontejnery Linux:
  * Windows 10 nebo novější operační systémy.
  * Windows Server 2016 nebo nové serverové operační systémy.

## <a name="install-the-container-runtime"></a>Nainstalovat modul runtime kontejneru 

Azure IoT Edge spoléhá na [OCI kompatibilní] [ lnk-oci] kontejner modulu runtime (např. Docker). 

Můžete použít [Docker pro systém Windows] [ lnk-docker-for-windows] pro vývoj a testování. Ujistěte se, Docker pro systém Windows je [nakonfigurované na používání Linux kontejnery][lnk-docker-config]

## <a name="install-the-azure-iot-edge-security-daemon"></a>Instalace démona zabezpečení Azure IoT Edge

>[!NOTE]
>Azure IoT Edge softwarové balíčky jsou souladu s licenčními podmínkami, který je umístěný v balíčcích (v adresáři licencí). Přečtěte si licenční podmínky před použitím balíčku. Instalace a použití balíčku, znamená to, že přijímáte tyto podmínky. Pokud s licenčními podmínkami nesouhlasíte, nepoužívejte balíčku.

### <a name="download-the-edge-daemon-package-and-install"></a>Stáhněte si balíček démon okraj a nainstalovat

V okně Powershellu správce spusťte následující příkazy:

```powershell
Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
rmdir C:\ProgramData\iotedge\iotedged-windows
$env:Path += ";C:\ProgramData\iotedge"
SETX /M PATH "$env:Path"
```

Nainstalujte vcruntime:

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

Vytvoření **iotedge.reg** soubor s následujícím obsahem a importování v registru systému Windows poklikat nebo pomocí `reg import iotedge.reg` příkaz:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Nakonfigurujte funkce zabezpečení Azure IoT Edge

Démon lze nakonfigurovat pomocí konfiguračního souboru na `C:\ProgramData\iotedge\config.yaml` hraniční zařízení je možné nakonfigurovat <!--[automatically via Device Provisioning Service][lnk-dps] or--> ručně pomocí [zařízení připojovací řetězec][lnk-dcs].

Pro ruční konfigurace, zadejte připojovací řetězec zařízení **zřizování:** části **config.yaml**

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

Získání názvu hraniční zařízení pomocí `hostname` příkazů v prostředí PowerShell a nastavte jej jako hodnota **název hostitele:** v yaml konfigurace. Příklad:

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

V dalším kroku budeme potřebovat zadejte ip adresu a port pro **workload_uri** a **management_uri** v **připojení:** oddílu konfigurace.

Pro ip adresu, zadejte `ipconfig` v okně prostředí PowerShell a vyberte ip adresu **vEthernet (DockerNAT)**, rozhraní, jak je znázorněno v následujícím příkladu (ip adresu v systému může být jiný):

![DockerNat][img-docker-nat]

```yaml
connect:
  management_uri: "http://10.0.75.1:15580"
  workload_uri: "http://10.0.75.1:15581"
```

Zadejte stejné adresy v **naslouchání:** oddílu konfigurace. Příklad:

```yaml
listen:
  management_uri: "http://10.0.75.1:15580"
  workload_uri: "http://10.0.75.1:15581"
```

V okně prostředí PowerShell vytvořit proměnnou prostředí **IOTEDGE_HOST** s **management_uri** adresu, třeba:

```powershell
[Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://10.0.75.1:15580")
```

Nakonec se ujistěte, **sítě:** nastavení v části **moby_runtime:** je uncommented a nastavený na **azure-iot-edge**

```yaml
moby_runtime:
  docker_uri: "npipe://./pipe/docker_engine"
  network: "azure-iot-edge"
```

Konfigurační soubor uložte a restartujte službu:

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Můžete zkontrolovat stav služby IoT Edge podle: 

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
  sort-object @{Expression="TimeCreated";Descending=$false}
```

A seznam systémem modulů s:

```powershell
iotedge list
```

## <a name="next-steps"></a>Další postup

Pokud máte potíže s modulem runtime Edge instalaci správně, najdete v článku věnovaném [řešení potíží s] [ lnk-trouble] stránky.


<!-- Images -->
[img-docker-nat]: ./media/how-to-install-iot-edge-windows-with-linux/dockernat.png

<!-- Links -->
[lnk-docker-config]: https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers
[lnk-dcs]: ../iot-hub/quickstart-send-telemetry-dotnet.md#register-a-device
[lnk-dps]: how-to-simulate-dps-tpm.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
[lnk-docker-for-windows]: https://www.docker.com/docker-windows

