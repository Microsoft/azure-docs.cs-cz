---
title: Nainstalujte Windows Azure IoT Edge pro Linux | Dokumentace Microsoftu
description: Instalační pokyny pro Azure IoT Edge pro kontejnery Linuxu ve Windows 10, Windows Server a Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: b7386cbbe18d7e05c2fbffb96f6214b468956192
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66151708"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Používejte kontejnery Linuxu pomocí IoT Edge ve Windows

Otestujte moduly IoT Edge pro zařízení s Linuxem pomocí počítače s Windows. 

V případě produkčního prostředí v zařízení Windows by měla pouze spouštění kontejnerů Windows. Běžný scénář, kdy vývoj je však použití počítače Windows k vytváření modulů pro Linux zařízení IoT Edge. Modul runtime IoT Edge pro Windows umožňuje spouštět kontejnery Linuxu pro **vývoj a testování** účely. 

V tomto článku jsou uvedené kroky k instalaci modulu runtime Azure IoT Edge pomocí kontejnerů Linuxu na vaše Windows x64 (AMD nebo Intel) systému. Další informace o Instalační službě modul runtime IoT Edge, včetně podrobností o všech parametrech instalace, najdete v článku [nainstalovat modul runtime Azure IoT Edge ve Windows](how-to-install-iot-edge-windows.md).

## <a name="prerequisites"></a>Požadavky

V této části použijte ke kontrole, jestli zařízení s Windows může podporovat IoT Edge a aby byl připravený k modul container před instalací. 

### <a name="supported-windows-versions"></a>Podporované verze Windows

Azure IoT Edge s kontejnery Linux lze spustit na následující verze systému Windows: 
* Windows 10 Anniversary update (sestavení 14393) nebo novější
* Windows Server 2016 nebo novější

Další informace o co je zahrnuté v nejnovější verzi služby IoT Edge najdete v tématu [Azure IoT Edge uvolní](https://github.com/Azure/azure-iotedge/releases).

Pokud chcete nainstalovat IoT Edge na virtuálním počítači, povolit vnořenou virtualizaci a vyhradit alespoň 2 GB paměti. Jak povolit vnořenou virtualizaci se liší v závislosti na hypervizoru používání. Pro Hyper-V virtuálních počítačů generace 2 mít člověk vnořené virtualizace ve výchozím nastavení povolená. Pro replikaci z VMWare je přepínač k povolení této funkce ve vašem virtuálním počítači. 

### <a name="prepare-the-container-engine"></a>Příprava kontejneru modulu 

Azure IoT Edge se může spolehnout [OCI kompatibilní](https://www.opencontainers.org/) modul kontejneru. Největší rozdíl konfigurace mezi kontejnery Windows a Linuxem a systémem Windows, počítač je, že modul runtime kontejneru Windows zahrnuje instalace IoT Edge, ale je potřeba zadat vlastní modul runtime pro kontejnery Linuxu před instalací IoT Edge. 

Pokud chcete nastavit počítač s Windows můžete vyvíjet a testovat kontejnery pro Linux zařízení, můžete použít [Docker Desktop](https://www.docker.com/docker-windows) jako modul kontejneru. Je potřeba nainstalovat Docker a nakonfigurujte na něm [používat kontejnery Linuxu](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) před instalací IoT Edge.  

Pokud zařízení IoT Edge je počítač Windows, zkontrolujte, zda splňuje [požadavky na systém](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) pro Hyper-V.

## <a name="install-iot-edge-on-a-new-device"></a>Nainstalovat nové zařízení IoT Edge

>[!NOTE]
>Azure IoT Edge softwarové balíčky jsou souladu s licenčními podmínkami umístěný v balíčcích (v adresáři licencí). Přečtěte si licenční podmínky před použitím balíčku. Instalace a použití balíčku se považuje za svůj souhlas s těmito podmínkami. Pokud s licenčními podmínkami nesouhlasíte, nepoužívejte balíček.

Powershellový skript se stáhne a nainstaluje démona zabezpečení Azure IoT Edge. Zabezpečení proces démon potom spustí první ze dvou modulů runtime, agenta IoT Edge, které umožňuje vzdálené nasazení dalších modulů. 

Při instalaci modulu runtime IoT Edge na zařízení poprvé, budete muset zřizovat zařízení s identitou ze služby IoT hub. Jedno zařízení IoT Edge se dá zřídit ručně pomocí řetězce připojení zařízení k dispozici ve službě IoT hub. Nebo můžete do služby Device Provisioning k automatickému zřízení zařízení, což je užitečné, když máte velký počet zařízení k nastavení. 

Další informace o jinou instalační možnosti a parametry v tomto článku [nainstalovat modul runtime Azure IoT Edge ve Windows](how-to-install-iot-edge-windows.md). Jakmile budete mít Desktopu Docker nainstalovaný a nakonfigurovaný pro kontejnery Linuxu, instalace hlavní rozdíl je deklarace Linuxu se službou **- ContainerOs** parametru. Příklad: 

1. Pokud jste to ještě neudělali, zaregistrujte si nové zařízení IoT Edge a načíst připojovací řetězec zařízení. Zkopírujte připojovací řetězec pro pozdější použití v této části. Dokončení tohoto kroku pomocí následujících nástrojů:

   * [Azure Portal](how-to-register-device-portal.md)
   * [Azure CLI](how-to-register-device-cli.md)
   * [Visual Studio Code](how-to-register-device-vscode.md)

2. Spusťte PowerShell jako správce.

   >[!NOTE]
   >Instalace IoT Edge, PowerShell (x86) používáte AMD64 relaci powershellu. Pokud si nejste jistí, který typ relace používáte, spusťte následující příkaz:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. **Nasadit IoTEdge** příkaz kontroluje, zda váš počítač Windows na podporovanou verzi, se změní na funkci kontejnery a potom stáhne runtime moby (který se nepoužívá pro kontejnery Linuxu) a modul runtime IoT Edge. Příkaz výchozí hodnoty pro kontejnery Windows, tak deklarovat Linux jako operační systém požadovaný kontejner. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. V tomto okamžiku může automaticky restartovat zařízení IoT Core. Ostatní zařízení Windows 10 nebo Windows Server můžete být vyzváni k restartování. Pokud ano, restartujte zařízení. Jakmile se vaše zařízení je připravené, znovu spusťte PowerShell jako správce.

5. **Inicializace IoTEdge** příkaz nakonfiguruje modul runtime IoT Edge na svém počítači. Příkaz výchozí hodnota je ruční zřizování s připojovacím řetězcem zařízení. Znovu deklarujte jako operační systém požadovaný kontejner Linuxu. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. Po zobrazení výzvy zadejte připojovací řetězec zařízení, které jste získali v kroku 1. Připojovací řetězec zařízení přidružuje fyzického zařízení s ID zařízení ve službě IoT Hub. 

   Připojovací řetězec zařízení má následující formát a neměla by obsahovat uvozovky: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Zkontrolujte stav služby IoT Edge. By měly být uvedeny jako spuštění.  

```powershell
Get-Service iotedge
```

Zkontrolujte protokoly služby z posledních 5 minut. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Seznam s moduly. Po dokončení nové instalace pouze modulu byste měli vidět spuštění **edgeAgent**. Poté co [nasadit moduly IoT Edge](how-to-deploy-modules-portal.md), zobrazí se ostatním. 

```powershell
iotedge list
```

## <a name="next-steps"></a>Další postup

Teď, když máte zařízení IoT Edge zřízené s modulem runtime nainstalovaný, je možné [nasadit moduly IoT Edge](how-to-deploy-modules-portal.md).

Pokud máte potíže s instalací IoT Edge správně, podívejte se [řešení potíží s](troubleshoot.md) stránky.

Aktualizace stávající instalace na nejnovější verzi služby IoT Edge, najdete v článku [aktualizovat démon zabezpečení IoT Edge a modulu runtime](how-to-update-iot-edge.md).
