---
title: "Nainstalovat Azure IoT Edge na jádro IoT | Microsoft Docs"
description: "Nainstalovat modul runtime Azure IoT Edge na jádro IoT Windows zařízení"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 03/05/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 75d2b370ed6118a30153a001a4b654d7212b56cd
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Nainstalovat modul runtime IoT Edge na jádro IoT Windows – náhled

Azure IoT okraj a [jádro IoT Windows](https://docs.microsoft.com/windows/iot-core/) spolupracují, aby povolit edge computing na i malé zařízení. Modulu Runtime Azure IoT Edge můžete spustit i na malá velikost počítače jeden Tabule (SBC) zařízení, které jsou velmi běžně se vyskytujícím v odvětví IoT. 

Tento článek vás provede zřizování modul runtime na [kambala velká MinnowBoard] [ lnk-minnow] vývoj Tabule systémem jádro IoT Windows. Jádro IoT Windows podporuje jenom na x64 64 procesory Intel Azure IoT okraj. 

## <a name="install-the-runtime"></a>Nainstalovat modul runtime

1. Nainstalujte [řídicího panelu Windows 10 IoT Core] [ lnk-core] v hostitelském systému.
1. Postupujte podle kroků v [nastavit vaše zařízení] [ lnk-board] ke konfiguraci vaší karty MinnowBoard kambala velká nebo maximální sestavení 16299 Image. 
1. Zařízení, povolit pak [přihlášení vzdáleně pomocí prostředí PowerShell][lnk-powershell].
1. V konzole PowerShell instalaci modulu runtime kontejneru: 

   ```powershell
   Invoke-WebRequest https://master.dockerproject.org/windows/x86_64/docker-0.0.0-dev.zip -o temp.zip
   Expand-Archive .\temp.zip $env:ProgramFiles -f
   Remove-Item .\temp.zip
   $env:Path += ";$env:programfiles\docker"
   SETX /M PATH "$env:Path"
   dockerd --register-service
   start-service docker
   ```

   >[!NOTE]
   >Tento modul runtime kontejneru je ze serveru sestavení projektu Moby a je určená jenom pro účely hodnocení. Ho má není otestovat, schválené nebo nepodporuje Docker.

1. Nainstalovat modul runtime IoT okraj a ověřte konfiguraci:

   ```powershell
   Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
   ```

   Tento skript obsahuje následující informace: 
   * Python 3.6
   * Skript IoT okraj ovládacího prvku (iotedgectl.exe)

Může se zobrazit informační výstup z nástroje iotedgectl.exe zeleně v okně vzdáleného prostředí PowerShell. To nutně neznamená chyby. 

## <a name="next-steps"></a>Další postup

Teď, když máte zařízení se systémem runtime IoT Edge, zjistěte, jak [nasadit a monitorovat moduly IoT Edge ve velkém měřítku][lnk-deploy].

<!--Links-->
[lnk-minnow]: https://minnowboard.org/ 
[lnk-core]: https://docs.microsoft.com/windows/iot-core/connect-your-device/iotdashboard
[lnk-board]: https://developer.microsoft.com/windows/iot/Docs/GetStarted/mbm/sdcard/stable/getstartedstep2
[lnk-powershell]: https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell
[lnk-deploy]: how-to-deploy-monitor.md
[lnk-docker-install]: https://docs.docker.com/engine/installation/linux/docker-ce/binaries#install-server-and-client-binaries-on-windows
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers