---
title: Jak nainstalovat Azure IoT Edge ve Windows s kontejnery Windows | Dokumentace Microsoftu
description: Azure IoT Edge pokyny k instalaci na Windows s kontejnery Windows
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/06/2018
ms.author: kgremban
ms.openlocfilehash: 39e0de6b378ed61ab375c6468b58c8c4a87b5fb9
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39575960"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-windows-containers"></a>Nainstalovat modul runtime Azure IoT Edge ve Windows pro použití s kontejnery Windows

Modul runtime Azure IoT Edge je nasadit na všechna zařízení IoT Edge. Skládá se ze tří částí. **Démon zabezpečení IoT Edge** poskytuje a udržuje standardy zabezpečení na hraniční zařízení. Proces démon spustí při každé spuštění a bootstraps zařízení spuštěním agenta IoT Edge. **Agenta IoT Edge** usnadňuje nasazení a monitorování modulů na hraniční zařízení, včetně hraničních zařízeních IoT hub. Druhým je **IoT Edge Hub**, který na zařízení IoT Edge řídí komunikaci mezi moduly a také mezi zařízením a IoT Hubem.

V tomto článku jsou uvedené kroky k instalaci modulu runtime Azure IoT Edge na vaše Windows x64 (AMD nebo Intel) systému. 

Podpora Windows je aktuálně ve verzi Preview.

## <a name="supported-windows-versions"></a>Podporované verze Windows
Azure IoT Edge s kontejnery Windows je možné s:
  * Windows 10/IoT Enterprise/IoT Core s platný od dubna 2018 update (sestavení 17134 vydaná).
  * Windows Server 1803

## <a name="install-the-container-runtime"></a>Nainstalovat modul runtime kontejneru 

>[!NOTE]
>Kontejner modulu instalaci na Windows IoT Core, postupujte podle kroků z [zřízení zařízení IoT Core článek] [ lnk-iot-core] a potom pokračujte v níže uvedených pokynů.

Azure IoT Edge se může spolehnout [OCI kompatibilní] [ lnk-oci] runtime kontejneru (například Dockeru). Můžete použít [Docker pro Windows] [ lnk-docker-for-windows] pro vývoj a testování. 

Konfigurace Dockeru pro Windows [používání kontejnerů Windows][lnk-docker-config].

## <a name="install-the-azure-iot-edge-security-daemon"></a>Instalace démona zabezpečení Azure IoT Edge

>[!NOTE]
>Azure IoT Edge softwarové balíčky jsou souladu s licenčními podmínkami umístěný v balíčcích (v adresáři licencí). Přečtěte si licenční podmínky před použitím balíčku. Instalace a použití balíčku se považuje za svůj souhlas s těmito podmínkami. Pokud s licenčními podmínkami nesouhlasíte, nepoužívejte balíček.

Jedno zařízení IoT Edge se dá zřídit ručně pomocí řetězce připojení zařízení k dispozici ve službě IoT Hub. Nebo můžete do služby Device Provisioning k automatickému zřízení zařízení, což je užitečné, když máte velký počet zařízení ke zřízení. Podle svého výběru: zřizování zvolte příslušný instalační skript. 

### <a name="install-and-manually-provision"></a>Instalace a ruční zřizování

1. Postupujte podle kroků v [zaregistrovat nová zařízení Azure IoT Edge] [ lnk-dcs] načíst připojovací řetězec zařízení a zaregistrovat své zařízení. 

2. Na svém zařízení IoT Edge spusťte PowerShell jako správce. 

3. Stáhněte a nainstalujte modul runtime IoT Edge. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Manual -ContainerOs Windows
   ```

4. Při zobrazení výzvy k zadání **DeviceConnectionString**, zadejte připojovací řetězec, který jste získali ze služby IoT Hub. Nezahrnují uvozovky kolem připojovací řetězec. 

### <a name="install-and-automatically-provision"></a>Instalace a automaticky zřizovat

1. Postupujte podle kroků v [vytvoření a zřízení simulovaného zařízení TPM Edge ve Windows] [ lnk-dps] nastavení služby Device Provisioning a získání jeho **ID oboru**, simulovat čip TPM zařízení a jejich načtení jeho **ID registrace**, pak vytvoření jednotlivé registrace. Jakmile je vaše zařízení zaregistrované ve službě IoT Hub, pokračujte v instalaci.  

   >[!TIP]
   >Nechte okno, na kterém běží simulátor TPM otevřených během instalace a testování. 

2. Na svém zařízení IoT Edge spusťte PowerShell jako správce. 

3. Stáhněte a nainstalujte modul runtime IoT Edge. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Dps -ContainerOs Windows
   ```

4. Po zobrazení výzvy zadejte **ScopeID** a **RegistrationID** pro vaši službu zřizování a zařízení. 

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Můžete zkontrolovat stav této služby IoT Edge: 

```powershell
Get-Service iotedge
```

Zkontrolujte protokoly služby o posledních 5 minut pomocí:

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

Teď, když máte zařízení IoT Edge zřízené s modulem runtime nainstalovaný, je možné [nasadit moduly IoT Edge][lnk-modules].

Pokud máte problémy s modulu runtime Edge instalaci správně, podívejte se [řešení potíží s] [ lnk-trouble] stránky.


<!-- Images -->
[img-nat]: ./media/how-to-install-iot-edge-windows-with-windows/nat.png

<!-- Links -->
[lnk-docker-config]: https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-windows.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
[lnk-docker-for-windows]: https://www.docker.com/docker-windows
[lnk-iot-core]: how-to-install-iot-core.md
[lnk-modules]: how-to-deploy-modules-portal.md
