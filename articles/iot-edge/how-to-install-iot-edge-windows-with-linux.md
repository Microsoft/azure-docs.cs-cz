---
title: Jak nainstalovat Azure IoT Edge ve Windows s kontejnery Linuxu | Dokumentace Microsoftu
description: Azure IoT Edge pokyny k instalaci na Windows s kontejnery Linuxu
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: kgremban
ms.openlocfilehash: 2ff7c3482100545c476040ba556d464b9f44e434
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031114"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows-to-use-with-linux-containers"></a>Nainstalovat modul runtime Azure IoT Edge ve Windows pro použití s kontejnery Linuxu

Modul runtime Azure IoT Edge je co se změní na zařízení do zařízení IoT Edge. Modul runtime můžete nasadit na zařízení jako Raspberry Pi malé nebo velké průmyslové serveru. Jakmile je zařízení nakonfigurovaná s modulem runtime IoT Edge, můžete začít nasazovat obchodní logiky k němu z cloudu. 

Další informace o fungování modul runtime IoT Edge a jaké součásti jsou zahrnuty, naleznete v tématu [pochopení runtime Azure IoT Edge a jeho architektura](iot-edge-runtime.md).

V tomto článku jsou uvedené kroky k instalaci modulu runtime Azure IoT Edge s kontejnery Linuxu ve vaší Windows x64 (AMD nebo Intel) systému. Podpora Windows je aktuálně ve verzi Preview.

>[!NOTE]
Pomocí kontejnerů Linuxu na systémy Windows není produkční doporučené nebo podporované konfigurace pro Azure IoT Edge. To však lze použít pro účely vývoje a testování.

## <a name="supported-windows-versions"></a>Podporované verze Windows
Azure IoT Edge může být používané pro vývoj a testování v následujících verzích Windows, při použití kontejnerů Linuxu:
  * Windows 10 nebo novější operační systémy.
  * Windows Server 2016 nebo nové serverové operační systémy.

Další informace o tom, které jsou aktuálně podporované operační systémy, najdete [podpory Azure IoT Edge](support.md#operating-systems). 

## <a name="install-the-container-runtime"></a>Nainstalovat modul runtime kontejneru 

Azure IoT Edge se může spolehnout [OCI kompatibilní] [ lnk-oci] runtime kontejneru (například Dockeru). 

Můžete použít [Docker pro Windows] [ lnk-docker-for-windows] pro vývoj a testování. Konfigurace Dockeru pro Windows [používat kontejnery Linuxu][lnk-docker-config]

## <a name="install-the-azure-iot-edge-security-daemon"></a>Instalace démona zabezpečení Azure IoT Edge

>[!NOTE]
>Azure IoT Edge softwarové balíčky jsou souladu s licenčními podmínkami umístěný v balíčcích (v adresáři licencí). Přečtěte si licenční podmínky před použitím balíčku. Instalace a použití balíčku se považuje za svůj souhlas s těmito podmínkami. Pokud s licenčními podmínkami nesouhlasíte, nepoužívejte balíček.

Jedno zařízení IoT Edge se dá zřídit ručně pomocí řetězce připojení zařízení k dispozici ve službě IoT Hub. Nebo můžete do služby Device Provisioning k automatickému zřízení zařízení, což je užitečné, když máte velký počet zařízení ke zřízení. Podle svého výběru: zřizování zvolte příslušný instalační skript. 

### <a name="option-1-install-and-manually-provision"></a>Možnost 1: Instalace a zajištění ručně

1. Postupujte podle kroků v [zaregistrovat nová zařízení Azure IoT Edge] [ lnk-dcs] načíst připojovací řetězec zařízení a zaregistrovat své zařízení. 

2. Na svém zařízení IoT Edge spusťte PowerShell jako správce. 

3. Stáhněte a nainstalujte modul runtime IoT Edge. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Manual -ContainerOs Linux
   ```

4. Při zobrazení výzvy k zadání **DeviceConnectionString**, zadejte připojovací řetězec, který jste získali ze služby IoT Hub. Nezahrnují uvozovky kolem připojovací řetězec. 

### <a name="option-2-install-and-automatically-provision"></a>Možnost 2: Instalace a automaticky zřizovat

1. Postupujte podle kroků v [vytvoření a zřízení simulovaného zařízení TPM Edge ve Windows] [ lnk-dps] nastavení služby Device Provisioning a získání jeho **ID oboru**, simulovat čip TPM zařízení a jejich načtení jeho **ID registrace**, pak vytvoření jednotlivé registrace. Jakmile je vaše zařízení zaregistrované ve službě IoT Hub, pokračujte v instalaci.  

   >[!TIP]
   >Nechte okno, na kterém běží simulátor TPM otevřených během instalace a testování. 

2. Na svém zařízení IoT Edge spusťte PowerShell jako správce. 

3. Stáhněte a nainstalujte modul runtime IoT Edge. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Dps -ContainerOs Linux
   ```

4. Po zobrazení výzvy zadejte **ScopeID** a **RegistrationID** pro vaši službu zřizování a zařízení.

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

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

## <a name="tips-and-suggestions"></a>Tipy a návrhy

Pokud má vaše síť proxy server, postupujte podle kroků v [nakonfigurujte zařízení IoT Edge pro komunikaci přes proxy server](how-to-configure-proxy-support.md) nainstalujte a spusťte modul runtime IoT Edge.

## <a name="next-steps"></a>Další postup

Teď, když máte zařízení IoT Edge zřízené s modulem runtime nainstalovaný, je možné [nasadit moduly IoT Edge][lnk-modules].

Pokud máte problémy s modulu runtime Edge instalaci správně, podívejte se [řešení potíží s] [ lnk-trouble] stránky.


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
[lnk-modules]: how-to-deploy-modules-portal.md
