---
title: Aktualizace verze IoT Edge na zařízení – Azure IoT Edge | Dokumentace Microsoftu
description: Jak aktualizovat zařízení IoT Edge spustit nejnovější verze démona zabezpečení a modul runtime IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: a3b6327b9e05b039696cc1743fc2d16c5e945e26
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65152636"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Aktualizace zabezpečení démon IoT Edge a modulu runtime

Jak služba IoT Edge vydání nové verze, budete chtít aktualizaci vašich zařízení IoT Edge pro nejnovější funkce a vylepšení zabezpečení. Tento článek obsahuje informace o tom, jak aktualizovat zařízení IoT Edge, když je dostupná nová verze. 

Dvě součásti zařízení IoT Edge je potřeba aktualizovat, pokud chcete přejít na novější verzi. První je démon zabezpečení, který běží na zařízení a při spuštění zařízení spustí moduly runtime. Proces démon zabezpečení v současné době je možné pouze aktualizovat ze samotného zařízení. Druhá komponenta je modulu runtime, která se skládá z centra IoT Edge a moduly IoT Edge agenta. V závislosti na tom, jak strukturovat vaše nasazení modul runtime aktualizovat ze zařízení nebo vzdáleně. 

Nejnovější verzi služby Azure IoT Edge najdete v tématu [Azure IoT Edge uvolní](https://github.com/Azure/azure-iotedge/releases).

>[!IMPORTANT]
>Pokud používáte Azure IoT Edge na zařízení s Windows, neaktualizují na verzi 1.0.5 Pokud některou z těchto platí pro zařízení: 
>* Vaše zařízení jste neupgradovali na Windows 17763 sestavení. Sestavení verze IoT Edge 1.0.5 nepodporuje Windows starší než 17763.
>* Spusťte moduly Java nebo Node.js na zařízení s Windows. Přeskočte verze 1.0.5 i v případě, že jste aktualizovali zařízení s Windows na nejnovější verzi. 
>
>Další informace o verzi 1.0.5 IoT Edge najdete v tématu [poznámky k verzi 1.0.5](https://github.com/Azure/azure-iotedge/releases/tag/1.0.5). Další informace o tom, jak zabránit aktualizace na nejnovější verzi nástroje pro vývoj najdete v tématu [blog pro vývojáře IoT](https://devblogs.microsoft.com/iotdev/).


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

Na zařízeních s Windows použijte skript Powershellu k aktualizaci démona zabezpečení. Skript automaticky stáhne nejnovější verzi démona zabezpečení. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Proces démon zabezpečení spuštění příkazu Update-IoTEdge odebere ze zařízení, spolu s dvěma imagí kontejnerů modulu runtime. Soubor config.yaml se ukládají na zařízení, a také data z kontejnerů modulu Moby (Pokud používáte Windows kontejnery). Udržování znamená konfigurační informace, že není nutné zadat připojovací řetězec nebo informace o službě Device Provisioning pro vaše zařízení znovu během procesu aktualizace. 

Pokud chcete nainstalovat konkrétní verzi démona zabezpečení, stáhněte si odpovídající souboru Microsoft-Azure-IoTEdge.cab z [uvolní IoT Edge](https://github.com/Azure/azure-iotedge/releases). Potom použijte `-OfflineInstallationPath` parametru tak, aby odkazoval na umístění souboru. Další informace najdete v tématu [Offline instalaci](how-to-install-iot-edge-windows.md#offline-installation).

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

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Budete možná muset použít síla `-f` příznak odebrat bitové kopie. 

Služba IoT Edge o přijetí změn nejnovější verze modulu runtime obrázků a automaticky je znovu spusťte ve vašem zařízení. 

### <a name="update-a-specific-tag-image"></a>Aktualizace image s konkrétní značkou

Pokud ve svém nasazení používáte konkrétními značkami (například mcr.microsoft.com/azureiotedge-hub:**1.0.2**) je všechno, co potřebujete udělat, aktualizujte značky v manifestu nasazení a změny aplikujte na vaše zařízení. 

Na webu Azure Portal, bitové kopie nasazení modulu runtime jsou deklarovány v **konfigurovat rozšířená nastavení modulu Runtime Edge** oddílu. 

![Konfigurace nastavení modulu runtime edge Upřesnit](./media/how-to-update-iot-edge/configure-runtime.png)

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