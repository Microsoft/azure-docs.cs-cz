---
title: Instalace Azure IoT Edge pro Linux ve Windows | Dokumenty společnosti Microsoft
description: Pokyny k instalaci Azure IoT Edge pro kontejnery s Linuxem ve Windows 10, Windows Serveru a Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: 8a4579e092bbc4fd58954f1ce1f1dad3a8ddbbba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133162"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Použití IoT Edge ve Windows ke spuštění linuxových kontejnerů

Otestujte moduly IoT Edge pro linuxová zařízení používající počítač s Windows.

V produkčním scénáři by zařízení se systémem Windows měla spouštět pouze kontejnery systému Windows. Běžným scénářem vývoje je však použití počítače se systémem Windows k vytvoření modulů IoT Edge pro zařízení s Linuxem. Runtime IoT Edge pro Windows umožňuje spouštět linuxové kontejnery pro **účely testování a vývoje.**

Tento článek uvádí postup instalace runtime Azure IoT Edge pomocí kontejnerů Linuxu v systému Windows x64 (AMD/Intel). Další informace o instalačním programu prostředí IoT Edge, včetně podrobností o všech parametrech instalace, najdete [v tématu Instalace runtime Azure IoT Edge v systému Windows](how-to-install-iot-edge-windows.md).

Informace o tom, co je součástí nejnovější verze IoT Edge, najdete v [tématu Azure IoT Edge verze](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Požadavky

V této části můžete zkontrolovat, jestli vaše zařízení s Windows podporuje IoT Edge, a připravit ho na modul kontejneru před instalací.

### <a name="supported-windows-versions"></a>Podporované verze systému Windows

Azure IoT Edge s linuxovými kontejnery můžou běžet v libovolné verzi Windows, která splňuje [požadavky na Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)

Pokud chcete nainstalovat IoT Edge na virtuální můře, povolte vnořenou virtualizaci a přidělte alespoň 2 GB paměti. Způsob povolení vnořené virtualizace se liší v závislosti na hypervisoru, který používáte. Pro Hyper-V generace 2 virtuální počítače mají vnořené virtualizace povolena ve výchozím nastavení. Pro VMWare, je přepnout povolit funkci na vašem virtuálním počítači.

### <a name="prepare-the-container-engine"></a>Příprava motoru kontejneru

Azure IoT Edge spoléhá na modul kontejneru [kompatibilní s OCI.](https://www.opencontainers.org/) Největší rozdíl v konfiguraci mezi spuštěním kontejnerů Windows a Linux na počítači se systémem Windows spočívá v tom, že instalace IoT Edge zahrnuje runtime kontejneru Windows, ale před instalací IoT Edge musíte zadat vlastní runtime pro kontejnery Linux.

Chcete-li nastavit počítač se systémem Windows pro vývoj a testování kontejnerů pro zařízení s Linuxem, můžete jako modul kontejneru použít [Docker Desktop.](https://www.docker.com/docker-windows) Před instalací IoT Edge je potřeba nainstalovat Docker a nakonfigurovat ho tak, aby [používal linuxové kontejnery.](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)  

Pokud je vaše zařízení IoT Edge počítač s Windows, zkontrolujte, zda splňuje [systémové požadavky pro](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) technologie Hyper-V.

## <a name="install-iot-edge-on-a-new-device"></a>Instalace IoT Edge na nové zařízení

>[!NOTE]
>Softwarové balíčky Azure IoT Edge podléhají licenčním podmínkám umístěným v balíčcích (v adresáři LICENCE). Před použitím balíčku si přečtěte licenční podmínky. Vaše instalace a používání balíčku představuje váš souhlas s těmito podmínkami. Pokud nesouhlasíte s licenčními podmínkami, balíček nepoužívejte.

Skript Prostředí PowerShell stáhne a nainstaluje demon zabezpečení Azure IoT Edge. Daemon zabezpečení pak spustí první ze dvou modulů runtime, agenta IoT Edge, který umožňuje vzdálené nasazení jiných modulů.

Při první instalaci runtime IoT Edge na zařízení, je třeba zřídit zařízení s identitou z centra IoT. Jedno zařízení IoT Edge lze zřídit ručně pomocí řetězce připojení zařízení poskytovaného službou IoT hub. Nebo můžete použít službu zřizování zařízení k automatickému zřizování zařízení, což je užitečné, když máte mnoho zařízení k nastavení.

Další informace o různých možnostech a parametrech instalace najdete v článku [Instalace runtime Azure IoT Edge v systému Windows](how-to-install-iot-edge-windows.md). Jakmile máte docker desktop nainstalovaný a nakonfigurovaný pro linuxové kontejnery, hlavní rozdíl v instalaci je deklarování Linuxu s parametrem **-ContainerOs.** Například:

1. Pokud jste tak ještě neučinili, zaregistrujte nové zařízení IoT Edge a načtěte připojovací řetězec zařízení. Zkopírujte připojovací řetězec, který chcete použít později v této části. Tento krok můžete provést pomocí následujících nástrojů:

   * [Portál Azure](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Kód visual studia](how-to-register-device.md#register-with-visual-studio-code)

2. Spusťte PowerShell jako správce.

   >[!NOTE]
   >K instalaci IoT Edge použijte relaci AMD64 v Prostředí PowerShell, ne powershellu (x86). Pokud si nejste jisti, který typ relace používáte, spusťte následující příkaz:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Příkaz **Deploy-IoTEdge** zkontroluje, zda je váš počítač se systémem Windows v podporované verzi, zapne funkci kontejnerů a potom stáhne modul moby runtime (který se nepoužívá pro kontejnery Linux) a modul runtime IoT Edge. Příkaz výchozí kontejnery Windows, tak deklarovat Linux jako požadovaný operační systém kontejneru.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. V tomto okamžiku se zařízení IoT Core mohou automaticky restartovat. K restartování vás mohou vyzvat jiná zařízení s Windows 10 nebo Windows Server. Pokud ano, restartujte zařízení nyní. Jakmile je vaše zařízení připravené, spusťte PowerShell znovu jako správce.

5. Příkaz **Initialize-IoTEdge** konfiguruje runtime IoT Edge ve vašem počítači. Příkaz je výchozí pro ruční zřizování pomocí připojovacího řetězce zařízení. Deklarujte Linux jako požadovaný operační systém kontejneru znovu.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. Po zobrazení výzvy zadejte připojovací řetězec zařízení, který jste načetli v kroku 1. Připojovací řetězec zařízení přidruží fyzické zařízení k ID zařízení v centru IoT Hub.

   Připojovací řetězec zařízení má následující formát a neměl by obsahovat uvozovky:`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Zkontrolujte stav služby IoT Edge:

```powershell
Get-Service iotedge
```

Zkontrolujte protokoly služby z posledních 5 minut:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Spusťte automatickou kontrolu nejčastějších chyb konfigurace a sítě:

```powershell
iotedge check
```

Seznam spuštěných modulů. Po nové instalaci, jediný modul, který byste měli vidět běží je **edgeAgent**. Po [prvním nasazení modulů IoT Edge](how-to-deploy-modules-portal.md) se v zařízení spustí i druhý systémový modul **edgeHub**.

```powershell
iotedge list
```

## <a name="next-steps"></a>Další kroky

Teď, když máte zařízení IoT Edge zřízené s nainstalovaným modulem runtime, můžete [nasadit moduly IoT Edge](how-to-deploy-modules-portal.md).

Pokud máte problémy s instalací IoT Edge správně, podívejte se na stránku [řešení potíží.](troubleshoot.md)

Informace o aktualizaci existující instalace na nejnovější verzi technologie IoT Edge naleznete v [tématu Aktualizace daemonu zabezpečení IoT Edge a runtime](how-to-update-iot-edge.md).
