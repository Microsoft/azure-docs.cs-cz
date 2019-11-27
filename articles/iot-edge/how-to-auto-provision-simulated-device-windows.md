---
title: Automatické zřizování zařízení s Windows pomocí DPS-Azure IoT Edge | Microsoft Docs
description: Použití simulovaného zařízení v počítači Windows k otestování automatické zřizování zařízení Azure IoT Edge službě Device Provisioning
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ad92d4cf0d5b61c778b87114d4be6c23557f8e26
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457140"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Vytvoření a zřízení simulovaného IoT Edge zařízení s virtuálním čipem TPM ve Windows

Zařízení Azure IoT Edge se dají automaticky zřídit pomocí [služby Device Provisioning](../iot-dps/index.yml) , stejně jako zařízení, která nejsou povolená přes hranice. Pokud nejste obeznámeni s procesem automatického zřizování, před pokračováním zkontrolujte [Koncepty automatického zřizování](../iot-dps/concepts-auto-provisioning.md) .

DPS podporuje ověřování symetrického klíče pro IoT Edge zařízení v individuální registraci a registraci skupin. Pokud při registraci skupiny zjistíte, že možnost "je IoT Edge zařízení" v případě ověření symetrického klíče pravdivá, všechna zařízení, která jsou registrovaná v této skupině registrací, se označí jako IoT Edge zařízení. 

V tomto článku se dozvíte, jak otestovat Automatické zřizování na simulovaném IoT Edge zařízení pomocí následujících kroků:

* Vytvoření instance z IoT Hubu zařízení zřizování služby (DPS).
* Na svém počítači Windows pomocí Simulovaná Trusted Platform Module (TPM) pro zabezpečení hardware vytvořte simulované zařízení.
* Vytvořte jednotlivou registraci pro zařízení.
* Nainstalujte modul runtime IoT Edge a připojení zařízení k centru IoT.

> [!NOTE]
> ČIP TPM 2,0 se vyžaduje při použití ověření identity pomocí čipu TPM s DPS a dá se použít jenom k vytvoření individuálních, neskupinových a registrací certifikátů.

> [!TIP]
> Tento článek popisuje testování automatického zřizování pomocí ověření identity čipem TPM na virtuálních zařízeních, ale většina z nich se používá i při použití fyzického hardwaru TPM.

## <a name="prerequisites"></a>Požadavky

* Vývojový počítač s Windows. Tento článek používá Windows 10.
* Aktivním centrem IoT.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Nastavte si IoT Hub Device Provisioning Service

Vytvořit novou instanci IoT Hub Device Provisioning Service v Azure a propojit jej do služby IoT hub. Můžete postupovat podle pokynů v tématu [nastavení IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

Po spuštění služby Device Provisioning zkopírujte na stránce Přehled hodnotu **Rozsah ID** . Tuto hodnotu použijete při konfiguraci modulu runtime IoT Edge.

> [!TIP]
> Pokud používáte fyzické zařízení TPM, je nutné určit **ověřovací klíč**, který je jedinečný pro každý čip TPM a získá se od výrobce čipu TPM, který je k němu přidružený. Jedinečné **ID registrace** pro vaše zařízení TPM můžete odvodit, například vytvořením hodnoty hash SHA-256 ověřovacího klíče.
>
> Podle pokynů v článku Správa registrací [zařízení pomocí webu Azure Portal](../iot-dps/how-to-manage-enrollments.md) vytvořte registraci v DPS a potom pokračujte v části [instalace modulu runtime IoT Edge](#install-the-iot-edge-runtime) v tomto článku, abyste mohli pokračovat.

## <a name="simulate-a-tpm-device"></a>Simulace zařízení TPM

Vytvoření simulovaného zařízení TPM na svém vývojovém počítači s Windows. Načtěte **ID registrace** a **ověřovací klíč** pro vaše zařízení a použijte je k vytvoření jednotlivé položky registrace v DPS.

Když vytvoříte registraci v DPS, budete mít možnost deklarovat **počáteční stav**dopředných zařízení. Ve dvojčeti zařízení můžete nastavit značky k seskupení zařízení podle libovolné metriky, které potřebujete ve vašem řešení, jako je oblast, prostředí, umístění nebo zařízení typu. Tyto značky slouží k vytváření [automatických nasazení](how-to-deploy-monitor.md).

Vyberte jazyk sady SDK, kterou chcete použít k vytvoření simulovaného zařízení a postupujte podle pokynů až do vytvoření jednotlivé registrace.

Když vytvoříte jednotlivou registraci, vyberte **true** , aby se deklarovalo, že simulované zařízení TPM ve vývojovém počítači s Windows je **IoT Edge zařízení**.

Simulované zařízení a jednotlivá registrace příručky:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Po vytvoření jednotlivé registrace uložte hodnotu **ID registrace**. Tuto hodnotu použijete při konfiguraci modulu runtime IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Nainstalovat modul runtime IoT Edge

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Jeho součástí spouštění v kontejnerech a můžete nasadit další kontejnery do zařízení tak, aby kód můžete spustit na hraničních zařízeních.

Při zřizování zařízení budete potřebovat následující informace:

* Hodnota **rozsahu ID** DPS
* **ID registrace** zařízení, které jste vytvořili

Nainstalujte modul runtime IoT Edge na zařízení, na kterém běží simulovaný čip TPM. Nastavíte modul runtime IoT Edge pro automatické, ne ruční zřizování.

> [!TIP]
> Nechte okno, na kterém běží simulátor TPM otevřených během instalace a testování.

Podrobnější informace o instalaci IoT Edge ve Windows, včetně požadavků a pokynů pro úlohy, jako je Správa kontejnerů a aktualizace IoT Edge, najdete v tématu [Instalace modulu runtime Azure IoT Edge ve Windows](how-to-install-iot-edge-windows.md).

1. Otevřete okno Powershellu v režimu správce. Při instalaci IoT Edge, ne pomocí PowerShellu (x86) nezapomeňte použít relaci AMD64 prostředí PowerShell.

1. Příkaz **Deploy-IoTEdge** zkontroluje, jestli má počítač s Windows podporovanou verzi, zapne funkci Containers a pak stáhne modul runtime Moby a modul runtime IoT Edge. Příkaz ve výchozím nastavení používá kontejnery Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. V tuto chvíli se zařízení IoT Core můžou restartovat automaticky. Jiná zařízení s Windows 10 nebo Windows Server vás můžou vyzvat k restartování. Pokud ano, restartujte zařízení nyní. Až bude zařízení připravené, spusťte PowerShell jako správce znovu.

1. Příkaz **Initialize-IoTEdge** nakonfiguruje IoT Edge modul runtime na vašem počítači. Příkaz je standardně nastaven na ruční zřizování pomocí kontejnerů Windows. Pomocí příznaku `-Dps` můžete místo ručního zřizování používat službu Device Provisioning.

   Nahraďte zástupné hodnoty pro `{scope_id}` a `{registration_id}` daty, která jste shromáždili dříve.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Pokud modul runtime byl úspěšně spuštěn, můžete přejít do služby IoT Hub a začít nasazovat moduly IoT Edge do zařízení. Ověřte, zda modul runtime nainstalován a úspěšně spustil pomocí následujících příkazů na vašem zařízení.  

Zkontrolujte stav služby IoT Edge.

```powershell
Get-Service iotedge
```

Zkontrolujte protokoly služby z posledních 5 minut.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Seznam s moduly.

```powershell
iotedge list
```

## <a name="next-steps"></a>Další kroky

Proces registrace služby Device Provisioning umožňuje nastavit ID zařízení a značky dvojčat zařízení ve stejnou dobu, jak zřídit nové zařízení. Tyto hodnoty můžete cílit na jednotlivá zařízení nebo skupin pomocí automatické správy zařízení. Naučte se, jak [nasadit a monitorovat IoT Edge moduly ve velkém měřítku pomocí Azure Portal](how-to-deploy-monitor.md) nebo [pomocí Azure CLI](how-to-deploy-monitor-cli.md) .
