---
title: Automatické zřizování zařízení Windows s DPS - Azure IoT Edge | Dokumentace Microsoftu
description: Použití simulovaného zařízení v počítači Windows k otestování automatické zřizování zařízení Azure IoT Edge službě Device Provisioning
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: a251eb3915ee3043ab4b69e0cd4cef8fa2170486
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53101598"
---
# <a name="create-and-provision-a-simulated-tpm-edge-device-on-windows"></a>Vytvoření a zřízení simulovaného zařízení TPM Edge ve Windows

Zařízení Azure IoT Edge může být automatické zřízení pomocí [služby Device Provisioning](../iot-dps/index.yml) stejně jako zařízení, která nejsou povolena edge. Pokud neznáte proces automatického zřizování, přečtěte si [konceptům automatického zřizování](../iot-dps/concepts-auto-provisioning.md) než budete pokračovat. 

Tento článek ukazuje, jak otestovat automatického zřizování na simulovaném zařízení Edge pomocí následujících kroků: 

* Vytvoření instance z IoT Hubu zařízení zřizování služby (DPS).
* Na svém počítači Windows pomocí Simulovaná Trusted Platform Module (TPM) pro zabezpečení hardware vytvořte simulované zařízení.
* Vytvořte jednotlivou registraci pro zařízení.
* Nainstalujte modul runtime IoT Edge a připojení zařízení k centru IoT.

## <a name="prerequisites"></a>Požadavky

* Vývojový počítač s Windows. Tento článek používá Windows 10. 
* Aktivním centrem IoT. 

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Nastavte si IoT Hub Device Provisioning Service

Vytvořit novou instanci IoT Hub Device Provisioning Service v Azure a propojit jej do služby IoT hub. Můžete podle pokynů v [nastavení IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

Jakmile budete mít spuštěné služby Device Provisioning Service, zkopírujte hodnotu **rozsah ID** na stránce Přehled. Tuto hodnotu použijete při konfiguraci modulu runtime IoT Edge. 

## <a name="simulate-a-tpm-device"></a>Simulace zařízení TPM

Vytvoření simulovaného zařízení TPM na svém vývojovém počítači s Windows. Načíst **ID registrace** a **ověřovací klíč** pro vaše zařízení a jejich používání při vytváření položky jednotlivé registrace ve službě DPS. 

Až vytvořit registraci ve službě Device Provisioning, budete mít příležitost k deklaraci **počáteční stav Dvojčete zařízení**. Ve dvojčeti zařízení můžete nastavit značky k seskupení zařízení podle libovolné metriky, které potřebujete ve vašem řešení, jako je oblast, prostředí, umístění nebo zařízení typu. Tyto značky se používají k vytváření [automatické nasazení](how-to-deploy-monitor.md). 

Vyberte jazyk sady SDK, kterou chcete použít k vytvoření simulovaného zařízení a postupujte podle pokynů až do vytvoření jednotlivé registrace. 

Při vytvoření jednotlivé registrace vybrat **povolit** deklarovat, že tento virtuální počítač určený **zařízení IoT Edge**.

Simulované zařízení a jednotlivá registrace příručky: 
* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Po vytvoření jednotlivé registrace, uložit hodnotu **ID registrace**. Tuto hodnotu použijete při konfiguraci modulu runtime IoT Edge. 

## <a name="install-the-iot-edge-runtime"></a>Nainstalovat modul runtime IoT Edge

Po dokončení předchozí části, měli byste vidět nové zařízení uvedené jako zařízení IoT Edge ve službě IoT Hub. Teď budete muset nainstalovat modul runtime IoT Edge na zařízení. 

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Jeho součástí spouštění v kontejnerech a můžete nasadit další kontejnery do zařízení tak, aby kód můžete spustit na hraničních zařízeních. Na zařízeních s Windows můžete buď používat kontejnery Windows nebo kontejnery Linuxu. Zvolte typ kontejnerů, které chcete použít a postupujte podle pokynů. Ujistěte se, že konfigurace modulu runtime IoT Edge není ruční, automatické zřizování. 

Postupujte podle pokynů a nainstalujte modul runtime IoT Edge na zařízení, na kterém běží simulované čipu TPM v předchozí části. 

Vědět, službě Device Provisioning **rozsah ID** a zařízení **ID registrace** před zahájením těchto článků. 

* [Kontejnery Windows](how-to-install-iot-edge-windows-with-windows.md)
* [Kontejnery Linuxu](how-to-install-iot-edge-windows-with-linux.md)

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Pokud modul runtime byl úspěšně spuštěn, můžete přejít do služby IoT Hub a začít nasazovat moduly IoT Edge do zařízení. Ověřte, zda modul runtime nainstalován a úspěšně spustil pomocí následujících příkazů na vašem zařízení.  

Zkontrolujte stav služby IoT Edge.

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
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

Seznam s moduly.

```powershell
iotedge list
```

## <a name="next-steps"></a>Další postup

Proces registrace služby Device Provisioning umožňuje nastavit ID zařízení a značky dvojčat zařízení ve stejnou dobu, jak zřídit nové zařízení. Tyto hodnoty můžete cílit na jednotlivá zařízení nebo skupin pomocí automatické správy zařízení. Zjistěte, jak [nasazení a monitorování modulů při škálování na portálu Azure IoT Edge](how-to-deploy-monitor.md) nebo [pomocí Azure CLI](how-to-deploy-monitor-cli.md)
