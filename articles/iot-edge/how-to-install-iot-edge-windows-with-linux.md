---
title: Nainstalovat Azure IoT Edge pro Linux ve Windows | Microsoft Docs
description: Pokyny k instalaci Azure IoT Edge pro kontejnery pro Linux v systémech Windows 10, Windows Server a Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: c3a23e0c2546da55f977d589eb38607994d3902b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88611785"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Použití IoT Edge ve Windows ke spouštění kontejnerů Linux

Otestujte IoT Edge moduly pro zařízení se systémem Linux pomocí počítače s Windows.

V produkčním scénáři by měla zařízení s Windows spouštět jenom kontejnery Windows. Běžným scénářem vývoje je však použití počítače se systémem Windows k vytváření IoT Edgech modulů pro zařízení se systémem Linux. Modul runtime IoT Edge pro Windows umožňuje spouštět kontejnery Linux pro účely **testování a vývoje** .

V tomto článku jsou uvedené kroky pro instalaci modulu runtime Azure IoT Edge s využitím kontejnerů Linux v systému Windows x64 (AMD/Intel). Další informace o instalačním programu IoT Edge runtime, včetně podrobností o všech parametrech instalace, najdete v tématu [Instalace modulu runtime Azure IoT Edge v systému Windows](how-to-install-iot-edge-windows.md).

Informace o tom, co je zahrnuto v nejnovější verzi IoT Edge, najdete v tématu [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Požadavky

Pomocí této části můžete zkontrolovat, jestli zařízení s Windows podporuje IoT Edge, a připravit ho pro modul kontejneru před instalací.

### <a name="supported-windows-versions"></a>Podporované verze systému Windows

Azure IoT Edge s kontejnery pro Linux se dají spustit na libovolné verzi Windows, která splňuje [požadavky pro Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install) .

Pokud chcete nainstalovat IoT Edge na virtuálním počítači, povolte vnořenou virtualizaci a přidělte alespoň 2 GB paměti. Způsob povolení vnořené virtualizace se liší v závislosti na hypervisoru, který používáte. Virtuální počítače generace 2 pro Hyper-V mají ve výchozím nastavení vnořenou virtualizaci povolenou. Pro VMware je k dispozici přepínač pro povolení funkce na vašem virtuálním počítači.

### <a name="prepare-the-container-engine"></a>Příprava kontejnerového modulu

Azure IoT Edge spoléhá na modul kontejneru, který je [kompatibilní s OCI](https://www.opencontainers.org/) . Největší rozdíl v konfiguraci mezi běžícími kontejnery Windows a Linux na počítači s Windows je, že instalace IoT Edge zahrnuje modul runtime kontejneru Windows, ale před instalací IoT Edge musíte zadat vlastní modul runtime pro kontejnery Linux.

Chcete-li nastavit počítač s Windows pro vývoj a testování kontejnerů pro zařízení se systémem Linux, můžete jako modul kontejneru použít [Docker Desktop](https://www.docker.com/docker-windows) . Před instalací IoT Edge musíte nainstalovat Docker a nakonfigurovat ho tak, aby [používal kontejnery Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) .  

Pokud IoT Edge zařízení je počítač se systémem Windows, ověřte, zda splňuje [požadavky na systém](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) pro technologii Hyper-V.

## <a name="install-iot-edge-on-a-new-device"></a>Instalace IoT Edge na nové zařízení

>[!NOTE]
>Azure IoT Edge softwarové balíčky podléhají licenčním podmínkám, které jsou umístěny v balíčcích (v adresáři licencí). Přečtěte si licenční smlouvy před použitím tohoto balíčku. Vaše instalace a používání balíčku znamená přijetí těchto podmínek. Pokud s licenčními podmínkami nesouhlasíte, nepoužívejte balíček.

PowerShellový skript stáhne a nainstaluje démona zabezpečení Azure IoT Edge. Démon zabezpečení potom spustí první ze dvou běhových modulů a Agent IoT Edge, který umožňuje vzdálené nasazení jiných modulů.

Když na zařízení poprvé nainstalujete IoT Edge runtime, bude potřeba zřídit zařízení s identitou ze služby IoT Hub. Jedno zařízení IoT Edge se dá zřídit ručně pomocí řetězce připojení zařízení, které poskytuje vaše služba IoT Hub. Nebo můžete službu Device Provisioning použít k automatickému zřízení zařízení, což je užitečné, když máte spoustu zařízení, která se dají nastavit.

Další informace o různých možnostech instalace a parametrech najdete v článku [Instalace modulu runtime Azure IoT Edge v systému Windows](how-to-install-iot-edge-windows.md). Jakmile budete mít k dispozici Docker Desktop nainstalovaný a nakonfigurovaný pro kontejnery systému Linux, je hlavním rozdílem instalace deklarace systému Linux s parametrem **-ContainerOs** . Například:

1. Pokud jste to ještě neudělali, zaregistrujte nové zařízení IoT Edge a načtěte připojovací řetězec zařízení. Zkopírujte připojovací řetězec pro pozdější použití v této části. Tento krok můžete provést pomocí následujících nástrojů:

   * [Azure Portal](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. Spusťte PowerShell jako správce.

   >[!NOTE]
   >K instalaci IoT Edge, nikoli PowerShell (x86) použijte relaci AMD64 prostředí PowerShell. Pokud si nejste jistí, který typ relace používáte, spusťte následující příkaz:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Příkaz **Deploy-IoTEdge** zkontroluje, jestli má počítač s Windows podporovanou verzi, zapne funkci Containers a pak stáhne modul runtime Moby (který se nepoužívá pro kontejnery Linux) a modul runtime IoT Edge. Příkaz nastaví výchozí nastavení kontejnerů Windows, takže deklaruje Linux jako požadovaný operační systém kontejneru.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. V tuto chvíli se zařízení IoT Core můžou restartovat automaticky. Jiná zařízení s Windows 10 nebo Windows Server vás můžou vyzvat k restartování. Pokud ano, restartujte zařízení nyní. Až bude zařízení připravené, spusťte PowerShell jako správce znovu.

5. Příkaz **Initialize-IoTEdge** nakonfiguruje IoT Edge modul runtime na vašem počítači. Příkaz nastaví výchozí nastavení ručního zřizování s připojovacím řetězcem zařízení. Deklarujte Linux jako požadovaný operační systém pro kontejner.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. Po zobrazení výzvy zadejte připojovací řetězec zařízení, který jste získali v kroku 1. Připojovací řetězec zařízení přidružuje fyzické zařízení k ID zařízení v IoT Hub.

   Připojovací řetězec zařízení má následující formát a nesmí obsahovat uvozovky: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Ověřte stav služby IoT Edge:

```powershell
Get-Service iotedge
```

Prověřte protokoly služby za posledních 5 minut:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Spusťte automatizovanou kontrolu nejběžnějších chyb konfigurace a sítě:

```powershell
iotedge check
```

Vypíše spuštěné moduly. Jediným modulem, který by měl být spuštěný, se po nové instalaci zobrazí **edgeAgent**. Po prvním [nasazení IoT Edge moduly](how-to-deploy-modules-portal.md) se na zařízení spustí i druhý systémový modul **edgeHub**.

```powershell
iotedge list
```

## <a name="next-steps"></a>Další kroky

Teď, když máte nainstalovanou IoT Edge zařízení s nainstalovaným modulem runtime, můžete [nasadit IoT Edge moduly](how-to-deploy-modules-portal.md).

Pokud máte potíže s instalací IoT Edge správně, podívejte se na stránku [Poradce při potížích](troubleshoot.md) .

Chcete-li aktualizovat existující instalaci na nejnovější verzi IoT Edge, přečtěte si téma [aktualizace procesu démona zabezpečení IoT Edge a modulu runtime](how-to-update-iot-edge.md).
