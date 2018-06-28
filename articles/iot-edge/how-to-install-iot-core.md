---
title: Nainstalovat Azure IoT Edge na jádro IoT | Microsoft Docs
description: Nainstalovat modul runtime Azure IoT Edge na jádro IoT Windows zařízení
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 03/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ae5644a62b794dc8d6ace52f21a452fa70027d39
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029556"
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Nainstalovat modul runtime IoT Edge na jádro IoT Windows – náhled

Azure IoT okraj a [jádro IoT Windows](https://docs.microsoft.com/windows/iot-core/) spolupracují, aby povolit edge computing na i malé zařízení. Modulu Runtime Azure IoT Edge můžete spustit i na malá velikost počítače jeden Tabule (SBC) zařízení, které jsou velmi běžně se vyskytujícím v odvětví IoT. 

Tento článek vás provede zřizování modul runtime na vývěsce vývoj systémem jádro IoT Windows. 

**Jádro IoT Windows v současné době podporuje Azure IoT Edge pouze na x64 64 procesory Intel.**

## <a name="install-the-container-runtime"></a>Nainstalovat modul runtime kontejneru

1. Konfigurace panel s **sestavení 17134 (RS4)** jádro IoT image. 
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

## <a name="finish-installing"></a>Dokončení instalace

Instalace démona IoT Edge zabezpečení a nakonfigurovat je pomocí pokynů v [v tomto článku][lnk-install-windows-on-windows]

## <a name="next-steps"></a>Další postup

Teď, když máte zařízení se systémem runtime IoT Edge, zjistěte, jak [nasadit a monitorovat moduly IoT Edge ve velkém měřítku][lnk-deploy].

<!--Links-->
[lnk-install-windows-on-windows]: how-to-install-iot-edge-windows-with-windows.md#download-the-edge-daemon-package-and-install
[lnk-powershell]: https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell
[lnk-deploy]: how-to-deploy-monitor.md
[lnk-docker-install]: https://docs.docker.com/engine/installation/linux/docker-ce/binaries#install-server-and-client-binaries-on-windows
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers