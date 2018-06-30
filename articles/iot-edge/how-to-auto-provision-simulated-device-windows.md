---
title: Azure IoT hraniční zařízení automatického zřizování s distribučních bodů – Windows | Microsoft Docs
description: Použijte Simulovaná zařízení na počítač se systémem Windows k otestování zařízení automatické zřizování pro Azure IoT Edge službou zřizování zařízení
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0a973b248022cf3c0497f72bc2fcdd45a6527e65
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37115960"
---
# <a name="create-and-provision-a-simulated-tpm-edge-device-on-windows"></a>Vytvořit a zřídit simulované TPM hraniční zařízení v systému Windows

Azure IoT hraniční zařízení může být automaticky zajišťované pomocí [služba zřizování zařízení](../iot-dps/index.yml) stejně jako zařízení, které nejsou povolené okraj. Pokud jste obeznámeni s proces automatické zřizování, přečtěte si [automatické zřizování koncepty](../iot-dps/concepts-auto-provisioning.md) než budete pokračovat. 

Tento článek ukazuje, jak otestovat automatického zřizování na simulované hraniční zařízení pomocí následujících kroků: 

* Vytvoření instance služby IoT Hub zařízení zřizování služby (distribučních bodů).
* Vytvoření simulovaného zařízení, na počítač se systémem Windows s simulované Trusted Platform Module (TPM) pro zabezpečení hardwaru.
* Vytvořte jednotlivé zápisu pro zařízení.
* Nainstalovat modul runtime IoT okraj a připojit zařízení ke službě IoT Hub.

## <a name="prerequisites"></a>Požadavky

* Vývoj pro počítače s Windows. Tento článek používá Windows 10. 
* Aktivní Centrum IoT. 

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Nastavit službu zřizování zařízení IoT Hub

Vytvoření nové instance služby IoT Hub zařízení zřizování v Azure a propojit jej do služby IoT hub. Můžete postupovat podle pokynů v [nastavení distribučních bodů centra IoT](../iot-dps/quick-setup-auto-provision.md).

Až budete mít na kterém běží služba zřizování zařízení, zkopírujte hodnotu **ID oboru** na stránce Přehled. Tuto hodnotu použijte, pokud nakonfigurujete modul runtime IoT okraj. 

## <a name="simulate-a-tpm-device"></a>Simulace zařízení TPM

Vytvoření simulovaného zařízení TPM na vývojovém počítači Windows. Načtení **ID registrace** a **ověřovací klíč** pro vaše zařízení a použít je k vytvořte záznam jednotlivých registrace do distribučních bodů. 

Když vytvoříte zápisu v distribučních bodů, máte možnost deklarovat **počáteční stav Twin zařízení**. V dvojče zařízení můžete nastavit značky k seskupení zařízení podle všechny metriky, které potřebujete ve vašem řešení, jako je typ oblast, prostředí, umístění nebo zařízení. Tyto značky se používají k vytvoření [automatické nasazení](how-to-deploy-monitor.md). 

Vyberte jazyk SDK, kterou chcete použít k vytvoření simulovaného zařízení a postupujte podle kroků, dokud nevytvoříte jednotlivých registrace. 

Při vytváření jednotlivých registrace, vyberte **povolit** deklarovat, že je tento virtuální počítač **IoT hraniční zařízení**.

Simulované zařízení a jednotlivých registrace příručky: 
* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Po vytvoření jednotlivých registrace, uložit hodnotu **ID registrace**. Tuto hodnotu použijte, pokud nakonfigurujete modul runtime IoT okraj. 

## <a name="install-the-iot-edge-runtime"></a>Nainstalovat modul runtime IoT Edge

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Jeho součástí spusťte v kontejnerech a umožňují další kontejnery nasadit do zařízení tak, aby můžete spustit kód na hranici. Na zařízeních se systémem Windows můžete použít buď kontejnery Windows nebo Linux kontejnerů. Vyberte typ kontejnery, které chcete použít a postupujte podle pokynů. Ujistěte se, že konfigurace modulu runtime IoT Edge není ruční, automatické zřizování. 

Postupujte podle pokynů k instalaci modulu runtime IoT Edge na zařízení, které běží simulované TPM z předchozí části. 

Vědět, vaše distribučních bodů **ID oboru** a zařízení **ID registrace** před zahájením těchto článcích. 

* [Kontejnery Windows](how-to-install-iot-edge-windows-with-windows.md)
* [Kontejnery Linux](how-to-install-iot-edge-windows-with-linux.md)

## <a name="create-a-tpm-environment-variable"></a>Vytvořte proměnnou prostředí TPM

Na počítači s simulovaného zařízení, upravit **iotedge** registru služby nastavit proměnnou prostředí.

1. Z **spustit** nabídce otevřete **regedit**. 
2. Přejděte na **Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\iotedge**. 
3. Vyberte **upravit** > **nové** > **víceřetězcovou hodnotu**. 
4. Zadejte název **prostředí**. 
5. Dvakrát klikněte na nové proměnné a nastavte hodnotu na **IOTEDGE_USE_TPM_DEVICE = ON**. 
6. Klikněte na tlačítko **OK** a uložte změny. 

## <a name="restart-the-iot-edge-runtime"></a>Restartujte runtime IoT Edge

Restartujte runtime IoT Edge tak, aby se vybere všechny změny konfigurace provedené v zařízení. 

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

Zkontrolujte, zda je spuštěn modul runtime IoT okraj. 

   ```bash
   sudo systemctl status iotedge
   ```

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Pokud modul runtime spuštěna úspěšně, můžete se vrátit do služby IoT Hub a, nové zařízení se automaticky zřízený a je připraven ke spuštění IoT Edge moduly. 

Zkontrolujte stav služby IoT okraj.

```powershell
Get-Service iotedge
```

Zkontrolujte protokoly služby z posledních 5 minut.

```powershell
# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false}
```

Zobrazí seznam spuštěných modulů.

```powershell
iotedge list
```

## <a name="next-steps"></a>Další postup

Proces registrace zařízení zřizování služby umožňuje nastavit ID zařízení a zařízení twin značky ve stejnou dobu jako zřídit nové zařízení. Tyto hodnoty můžete cílit na jednotlivá zařízení nebo skupin zařízení pomocí automatického zařízení správy. Zjistěte, jak [sledování IoT Edge modulů na škálovat pomocí portálu Azure a nasadit](how-to-deploy-monitor.md) nebo [pomocí rozhraní příkazového řádku Azure](how-to-deploy-monitor-cli.md)