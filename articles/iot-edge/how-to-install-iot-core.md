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
ms.openlocfilehash: ac729963e63bd97c83719e21dad3ad2cfc9b4fee
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49392790"
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Nainstalovat Windows IoT Core runtime IoT Edge – preview

Azure IoT Edge a [Windows IoT Core](https://docs.microsoft.com/windows/iot-core/) spolupracují, aby povolit edge computing podle i malá zařízení. Modul Runtime Azure IoT Edge můžete spustit i na malý zařízeních jeden panel počítače (typu), která je velmi rozšířené v odvětví IoT. 

Tento článek vás provede zřizování modulu runtime v vývojářská deska s Windows IoT Core. 

**V současné době Windows IoT Core podporuje Azure IoT Edge pouze na procesorech Intel x64.**

## <a name="install-the-container-runtime"></a>Nainstalovat modul runtime kontejneru

1. Konfigurace panelu s **sestavení 17134 vydaná (RS4)** image IoT Core. 
1. Zapnutí zařízení, potom [přihlášení s využitím Powershellu](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell).
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

Démon zabezpečení IoT Edge nainstalujte a nakonfigurujte ho podle pokynů v [v tomto článku](how-to-install-iot-edge-windows-with-windows.md)

## <a name="next-steps"></a>Další postup

Teď, když máte zařízení se systémem modul runtime IoT Edge, zjistěte, jak [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md).