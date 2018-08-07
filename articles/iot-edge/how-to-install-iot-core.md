---
title: Nainstalujte Azure IoT Edge na IoT Core | Dokumentace Microsoftu
description: Nainstalujte modul runtime Azure IoT Edge na zařízení s Windows IoT Core
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 03/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f57db00894dab80f96f45111331d47a173520ced
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39575994"
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Nainstalovat Windows IoT Core runtime IoT Edge – preview

Azure IoT Edge a [Windows IoT Core](https://docs.microsoft.com/windows/iot-core/) spolupracují, aby povolit edge computing podle i malá zařízení. Modul Runtime Azure IoT Edge můžete spustit i na malý zařízeních jeden panel počítače (typu), která je velmi rozšířené v odvětví IoT. 

Tento článek vás provede zřizování modulu runtime v vývojářská deska s Windows IoT Core. 

**V současné době Windows IoT Core podporuje Azure IoT Edge pouze na procesorech Intel x64.**

## <a name="install-the-container-runtime"></a>Nainstalovat modul runtime kontejneru

1. Konfigurace panelu s **sestavení 17134 vydaná (RS4)** image IoT Core. 
1. Zapnutí zařízení, potom [přihlášení s využitím Powershellu][lnk-powershell].
1. V konzole Powershellu nainstalujte modul runtime kontejneru: 

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
   >Tento modul runtime kontejneru je ze serveru pro sestavení projektu Moby a je určená jenom pro účely vyhodnocení. Má nejsou testovány, se schválenou sadou nebo podporuje Docker.

## <a name="finish-installing"></a>Dokončení instalace

Démon zabezpečení IoT Edge nainstalujte a nakonfigurujte ho podle pokynů v [v tomto článku][lnk-install-windows-on-windows]

## <a name="next-steps"></a>Další postup

Teď, když máte zařízení se systémem modul runtime IoT Edge, zjistěte, jak [nasadit a monitorovat moduly IoT Edge ve velkém měřítku][lnk-deploy].

<!--Links-->
[lnk-install-windows-on-windows]: how-to-install-iot-edge-windows-with-windows.md
[lnk-powershell]: https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell
[lnk-deploy]: how-to-deploy-monitor.md
[lnk-docker-install]: https://docs.docker.com/engine/installation/linux/docker-ce/binaries#install-server-and-client-binaries-on-windows
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
