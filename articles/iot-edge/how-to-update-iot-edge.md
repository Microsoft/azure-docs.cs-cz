---
title: Aktualizace verze IoT Edge na zařízení – Azure IoT Edge | Dokumentace Microsoftu
description: Jak aktualizovat zařízení IoT Edge spustit nejnovější verze démona zabezpečení a modul runtime IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/17/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: dfad3199ba3a9cd2f3bca55be50760ddde676e70
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53558188"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Aktualizace zabezpečení démon IoT Edge a modulu runtime

Jak služba IoT Edge vydání nové verze, budete chtít aktualizaci vašich zařízení IoT Edge mít nejnovější funkce a vylepšení zabezpečení. Tento článek obsahuje informace o tom, jak aktualizovat zařízení IoT Edge, když je dostupná nová verze. 

Dvě součásti zařízení IoT Edge je potřeba aktualizovat, pokud chcete přejít na novější verzi. První je démon zabezpečení, který běží na zařízení a při spuštění zařízení spustí moduly runtime. Proces démon zabezpečení v současné době je možné pouze aktualizovat ze samotného zařízení. Druhá komponenta je modulu runtime, která se skládá z centra IoT Edge a moduly IoT Edge agenta. V závislosti na tom, jak strukturovat vaše nasazení modul runtime aktualizovat ze zařízení nebo vzdáleně. 

>[!IMPORTANT]
>Pokud používáte Azure IoT Edge na zařízení s Windows, neaktualizují na verzi 1.0.5 Pokud některou z těchto platí pro zařízení: 
>* Vaše zařízení jste neupgradovali na Windows 17763 sestavení. Sestavení verze IoT Edge 1.0.5 nepodporuje Windows starší než 17763.
>* Spusťte moduly Java nebo Node.js na zařízení s Windows. Přeskočte verze 1.0.5 i v případě, že jste aktualizovali zařízení s Windows na nejnovější verzi. 
>
>Další informace o verzi 1.0.5 IoT Edge najdete v tématu [poznámky k verzi 1.0.5](https://github.com/Azure/azure-iotedge/releases/tag/1.0.5). Další informace o tom, jak zajistit vývojářských nástrojů od aktualizace na nejnovější verzi, naleznete v tématu [blog pro vývojáře IoT](https://aka.ms/dev-win-iot-edge-module).


Nejnovější verzi služby Azure IoT Edge najdete v tématu [Azure IoT Edge uvolní](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Aktualizace zabezpečení démona

Démon zabezpečení IoT Edge je nativní součást, kterou je potřeba aktualizovat pomocí Správce balíčků na zařízení IoT Edge. 

Zkontrolujte verzi démona zabezpečení běžícího ve vašem zařízení s použitím příkazu `iotedge version`. 

### <a name="linux-devices"></a>Linux zařízení

Na zařízeních s Linuxem použijte k aktualizaci zabezpečení démon apt-get nebo správce odpovídající balíček. 

```bash
apt-get update
apt-get install libiothsm iotedge
```

### <a name="windows-devices"></a>Zařízení Windows

Na zařízeních s Windows pomocí skriptu prostředí PowerShell pro odinstalaci a opětovnou instalací démona zabezpečení. Instalační skript automaticky stáhne nejnovější verzi démona zabezpečení. 

Odinstalujte démona zabezpečení v rámci relace prostředí PowerShell správce. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-SecurityDaemon
```

Spuštění `Uninstall-SecurityDaemon` příkaz bez parametrů démona zabezpečení odebere ze zařízení, spolu s dvěma imagí kontejnerů modulu runtime. Soubor config.yaml se ukládají na zařízení, a také data z kontejnerů modulu Moby. Zachování konfigurace znamená, že není nutné zadat připojovací řetězec nebo informace o službě Device Provisioning pro vaše zařízení znovu během procesu instalace. 

Přeinstalujte démona zabezpečení v závislosti na tom, zda zařízení IoT Edge používá kontejnery Windows nebo kontejnery Linuxu. Nahraďte frázi **\<Windows nebo Linux\>** s některým z operačních systémů kontejneru. Použití **- ExistingConfig** příznak tak, aby odkazovala na existující soubor config.yaml na vašem zařízení. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -ExistingConfig -ContainerOS <Windows or Linux>
```

Pokud chcete nainstalovat konkrétní verzi démona zabezpečení, stáhněte si soubor odpovídající iotedged windows.zip z [uvolní IoT Edge](https://github.com/Azure/azure-iotedge/releases). Potom použijte `-OfflineInstallationPath` parametru tak, aby odkazoval na umístění souboru. Další informace najdete v tématu [Offline instalaci](how-to-install-iot-edge-windows.md#offline-installation).

## <a name="update-the-runtime-containers"></a>Aktualizovat kontejnerů modulu runtime

Způsob, jak aktualizovat agenta IoT Edge a IoT Edge hub kontejnery závisí na, jestli používáte kumulativní značky (třeba 1.0) nebo konkrétní značky (např. 1.0.2) ve vašem nasazení. 

Kontrola verze agenta IoT Edge a moduly IoT Edge hub aktuálně na vašem zařízení pomocí příkazů `iotedge logs edgeAgent` nebo `iotedge logs edgeHub`. 

  ![Kontejner verzi naleznete v protokolech](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Vysvětlení značky IoT Edge

Agenta IoT Edge a IoT Edge hub obrázky jsou označené verzí IoT Edge, které jsou přidružené. Existují dva různé způsoby použití značky pomocí bitové kopie modulu runtime: 

* **Značky se zajištěním provozu** – pouze první dvě hodnoty čísla verze můžete získat nejnovější image, která odpovídá těchto číslic. 1.0 je třeba aktualizovat vždycky, když je nová verze tak, aby odkazoval na nejnovější verzi 1.0.x. Pokud modul runtime kontejneru na vašem zařízení IoT Edge znovu stáhne bitovou kopii, moduly runtime jsou aktualizovány na nejnovější verzi. Tento přístup je určeno pro účely vývoje. Nasazení z portálu Azure výchozí značky se zajištěním provozu. 
* **Konkrétními značkami** -použít všechny tři hodnoty čísla verze explicitně nastavit verzi image. Například 1.0.2 nedojde ke změně po its vitial release. Jakmile budete připraveni k aktualizaci je možné deklarovat nové číslo verze v manifestu nasazení. Tento přístup doporučuje pro produkční účely.

### <a name="update-a-rolling-tag-image"></a>Aktualizovat bitovou kopii postupné značky

Pokud používáte kumulativní značky ve vašem nasazení (například mcr.microsoft.com/azureiotedge-hub:**1.0**) budete muset vynutit modul runtime kontejneru na vašem zařízení o přijetí změn nejnovější verzi image. 

Odstraňte místní verzi image ze zařízení IoT Edge. Počítačích s Windows, při odinstalaci démona zabezpečení také odebere bitové kopie modulu runtime, takže není nutné tento krok provést znovu. 

```cmd/sh
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Budete možná muset použít síla `-f` příznak odebrat bitové kopie. 

Služba IoT Edge o přijetí změn nejnovější verze modulu runtime obrázků a automaticky je znovu spusťte ve vašem zařízení. 

### <a name="update-a-specific-tag-image"></a>Aktualizace image s konkrétní značkou

Pokud ve svém nasazení používáte konkrétními značkami (například mcr.microsoft.com/azureiotedge-hub:**1.0.2**) je všechno, co potřebujete udělat, aktualizujte značky v manifestu nasazení a změny aplikujte na vaše zařízení. 

Na webu Azure Portal, bitové kopie nasazení modulu runtime jsou deklarovány v **konfigurovat rozšířená nastavení modulu Runtime Edge** oddílu. 

[Konfigurace nastavení modulu runtime edge Upřesnit](./media/how-to-update-iot-edge/configure-runtime.png)

V manifestu nasazení JSON, aktualizaci bitové kopie modulu v **systemModules** oddílu. 

```json
"systemModules": {
  "edgeAgent": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-agent:1.0.2",
      "createOptions": ""
    }
  },
  "edgeHub": {
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0.2",
      "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
    }
  }
},
```

## <a name="next-steps"></a>Další postup

Zobrazit nejnovější [Azure IoT Edge uvolní](https://github.com/Azure/azure-iotedge/releases).

Udržovat aktuální stav pomocí nejnovější aktualizace a oznámení v [blog o Internetu věcí](https://azure.microsoft.com/blog/topics/internet-of-things/) 