---
title: Po instalaci a zřizování ověřit úspěšné a řešit potíže
description: zahrnout soubor
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 10/06/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 295769e5d4924e529b296dbb0b9d405ee197c1db
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979538"
---
## <a name="verify-successful-setup"></a>Ověření úspěšné instalace

Zkontrolujte stav služby IoT Edge. Měl by být uveden jako spuštěný.  

# <a name="linux"></a>[Linux](#tab/linux)

```bash
systemctl status iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

```powershell
Get-Service iotedge
```

---

Projděte si protokoly služby.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
journalctl -u iotedge --no-pager --no-full
```

# <a name="windows"></a>[Windows](#tab/windows)

Pokud jste právě dokončili instalaci IoT Edge runtime, může se zobrazit seznam chyb od času mezi spuštěním **Deploy-IoTEdge** a **Initialize-IoTEdge**. Tyto chyby se očekávají, protože se služba pokouší spustit před tím, než se nakonfiguruje.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

---

Spusťte [Nástroj pro řešení potíží](../articles/iot-edge/troubleshoot.md#run-the-check-command) , ve kterém zjistíte nejběžnější chyby konfigurace a sítě.

```powershell
iotedge check
```

Až nasadíte svůj první modul, abyste IoT Edgei na zařízení, modul **$edgeHub** systému nebude na zařízení nasazený. V důsledku toho automatizovaná kontroly vrátí chybu pro `Edge Hub can bind to ports on host` kontrolu připojení. Tuto chybu je možné ignorovat, pokud se neobjeví po nasazení modulu do zařízení.

Nakonec seznam spuštěných modulů:

```powershell
iotedge list
```

Jediným modulem, který by měl být spuštěný, se po nové instalaci zobrazí **edgeAgent**.

## <a name="tips-and-troubleshooting"></a>Tipy a řešení potíží

U zařízení s omezeným prostředkem se důrazně doporučuje nastavit proměnnou prostředí *OptimizeForPerformance* na *hodnotu false* podle pokynů v [Průvodci odstraňováním potíží](../articles/iot-edge/troubleshoot.md).

Pokud se vaše zařízení nemůže připojit k IoT Hub a vaše síť má proxy server, postupujte podle pokynů v části [Konfigurace zařízení IoT Edge pro komunikaci prostřednictvím proxy server](../articles/iot-edge/how-to-configure-proxy-support.md).

# <a name="linux"></a>[Linux](#tab/linux)

V zařízeních se systémem Linux potřebujete zvýšená oprávnění ke spouštění `iotedge` příkazů. Po instalaci modulu runtime se odhlaste z počítače a znovu se přihlaste, abyste mohli aktualizovat vaše oprávnění automaticky. Do té doby použijte `sudo` ke spuštění příkazů se zvýšenými oprávněními.

# <a name="windows"></a>[Windows](#tab/windows)

V zařízeních se systémem Windows, která používají kontejnery Windows, byl modul Moby Container nainstalován jako součást IoT Edge. Modul Moby byl navržený tak, aby běžel paralelně s Docker desktopem. `docker`Pokud chcete pracovat přímo s kontejnery na vašem zařízení, můžete použít příkazy. Je však nutné specificky cílit na modul Moby v případě, že je na zařízení nainstalován také Docker Desktop.

Pokud například chcete zobrazit seznam všech imagí Docker, použijte následující příkaz:

```powershell
docker images
```

Pokud chcete zobrazit seznam všech imagí Moby, upravte stejný příkaz s ukazatelem na modul Moby:

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

Identifikátor URI modulu je uveden ve výstupu instalačního skriptu, nebo jej můžete najít v oddílu nastavení modulu runtime kontejneru pro soubor config. yaml.

![moby_runtime identifikátor URI v souboru config. yaml](./media/iot-edge-verify-troubleshoot-install/moby-runtime-uri.png)

---
