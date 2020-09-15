---
title: Automatické zřizování zařízení s Windows pomocí DPS-Azure IoT Edge | Microsoft Docs
description: Použití simulovaného zařízení na počítači s Windows k otestování automatického zřizování zařízení pro Azure IoT Edge se službou Device Provisioning
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b8a31f2739e497d8e203a9806d25b21608e5701f
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531470"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Vytvoření a zřízení simulovaného IoT Edge zařízení s virtuálním čipem TPM ve Windows

Zařízení Azure IoT Edge se dají automaticky zřídit pomocí [služby Device Provisioning](../iot-dps/index.yml) , stejně jako zařízení, která nejsou povolená přes hranice. Pokud neznáte proces automatického zřizování, přečtěte si přehled [zřizování](../iot-dps/about-iot-dps.md#provisioning-process) a teprve potom pokračujte.

DPS podporuje ověřování symetrického klíče pro IoT Edge zařízení v individuální registraci a registraci skupin. Pokud při registraci skupiny zjistíte, že možnost "je IoT Edge zařízení" v případě ověření symetrického klíče pravdivá, všechna zařízení, která jsou registrovaná v této skupině registrací, se označí jako IoT Edge zařízení.

V tomto článku se dozvíte, jak otestovat Automatické zřizování na simulovaném IoT Edge zařízení pomocí následujících kroků:

* Vytvořte instanci IoT Hub Device Provisioning Service (DPS).
* Vytvořte simulované zařízení na počítači s Windows s simulovaným čipem TPM (Trusted Platform Module) pro hardwarové zabezpečení.
* Vytvořte jednotlivou registraci zařízení.
* Nainstalujte modul runtime IoT Edge a připojte zařízení k IoT Hub.

> [!TIP]
> Tento článek popisuje testování automatického zřizování pomocí ověření identity čipem TPM na virtuálních zařízeních, ale většina z nich se používá i při použití fyzického hardwaru TPM.

## <a name="prerequisites"></a>Požadavky

* Vývojový počítač s Windows. Tento článek používá Windows 10.
* Aktivní IoT Hub.

> [!NOTE]
> ČIP TPM 2,0 se vyžaduje při použití ověření identity pomocí čipu TPM s DPS a dá se použít jenom k vytvoření individuálních, neskupinových a registrací certifikátů.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Nastavení IoT Hub Device Provisioning Service

Vytvořte novou instanci IoT Hub Device Provisioning Service v Azure a propojte ji se službou IoT Hub. Můžete postupovat podle pokynů v tématu [nastavení IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

Po spuštění služby Device Provisioning zkopírujte na stránce Přehled hodnotu **Rozsah ID** . Tuto hodnotu použijete při konfiguraci modulu runtime IoT Edge.

> [!TIP]
> Pokud používáte fyzické zařízení TPM, je nutné určit **ověřovací klíč**, který je jedinečný pro každý čip TPM a získá se od výrobce čipu TPM, který je k němu přidružený. Jedinečné **ID registrace** pro vaše zařízení TPM můžete odvodit, například vytvořením hodnoty hash SHA-256 ověřovacího klíče.
>
> Podle pokynů v článku Správa registrací [zařízení pomocí webu Azure Portal](../iot-dps/how-to-manage-enrollments.md) vytvořte registraci v DPS a potom pokračujte v části [instalace modulu runtime IoT Edge](#install-the-iot-edge-runtime) v tomto článku, abyste mohli pokračovat.

## <a name="simulate-a-tpm-device"></a>Simulace zařízení TPM

Vytvořte simulované zařízení TPM ve vývojovém počítači s Windows. Načtěte **ID registrace** a **ověřovací klíč** pro vaše zařízení a použijte je k vytvoření jednotlivé položky registrace v DPS.

Když vytvoříte registraci v DPS, budete mít možnost deklarovat **počáteční stav**dopředných zařízení. Ve vystavení zařízení můžete nastavit značky pro seskupení zařízení podle libovolné metriky, kterou potřebujete ve vašem řešení, jako je oblast, prostředí, umístění nebo typ zařízení. Tyto značky slouží k vytváření [automatických nasazení](how-to-deploy-at-scale.md).

Vyberte jazyk sady SDK, který chcete použít k vytvoření simulovaného zařízení, a postupujte podle kroků, dokud nevytvoříte jednotlivou registraci.

Když vytvoříte jednotlivou registraci, vyberte **true** , aby se deklarovalo, že simulované zařízení TPM ve vývojovém počítači s Windows je **IoT Edge zařízení**.

> [!TIP]
> V Azure CLI můžete vytvořit [registraci](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment) nebo [skupinu registrace](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group) a pomocí příznaku s **povoleným okrajem** určit, že zařízení nebo skupina zařízení je IoT Edge zařízení.

Simulovaná zařízení a příručky pro zápis:

* [R](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Po vytvoření jednotlivé registrace uložte hodnotu **ID registrace**. Tuto hodnotu použijete při konfiguraci modulu runtime IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Instalace modulu runtime IoT Edge

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Jeho komponenty se spouštějí v kontejnerech a umožňují na zařízení nasadit další kontejnery, abyste mohli spustit kód na hraničních zařízeních.

Při zřizování zařízení budete potřebovat následující informace:

* Hodnota **rozsahu ID** DPS
* **ID registrace** zařízení, které jste vytvořili

Nainstalujte modul runtime IoT Edge na zařízení, na kterém běží simulovaný čip TPM. Nastavíte modul runtime IoT Edge pro automatické, ne ruční zřizování.

> [!TIP]
> Nechte okno, ve kterém je spuštěný simulátor čipu TPM, otevřený během instalace a testování.

Podrobnější informace o instalaci IoT Edge ve Windows, včetně požadavků a pokynů pro úlohy, jako je Správa kontejnerů a aktualizace IoT Edge, najdete v tématu [Instalace modulu runtime Azure IoT Edge ve Windows](how-to-install-iot-edge-windows.md).

1. Otevřete okno PowerShellu v režimu správce. Při instalaci IoT Edge, ne pomocí PowerShellu (x86) nezapomeňte použít relaci AMD64 prostředí PowerShell.

1. Příkaz **Deploy-IoTEdge** zkontroluje, jestli má počítač s Windows podporovanou verzi, zapne funkci Containers a pak stáhne modul runtime Moby a modul runtime IoT Edge. Příkaz ve výchozím nastavení používá kontejnery Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. V tuto chvíli se zařízení IoT Core můžou restartovat automaticky. Jiná zařízení s Windows 10 nebo Windows Server vás můžou vyzvat k restartování. Pokud ano, restartujte zařízení nyní. Až bude zařízení připravené, spusťte PowerShell jako správce znovu.

1. Příkaz **Initialize-IoTEdge** nakonfiguruje IoT Edge modul runtime na vašem počítači. Příkaz je standardně nastaven na ruční zřizování pomocí kontejnerů Windows. Pomocí `-Dps` příznaku použijte službu Device Provisioning místo ručního zřizování.

   Nahraďte zástupné hodnoty pro `{scope_id}` a `{registration_id}` daty, která jste shromáždili dříve.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Pokud se modul runtime úspěšně spustil, můžete přejít do svého IoT Hub a začít nasazovat IoT Edge moduly do svého zařízení. Pomocí následujících příkazů na zařízení ověřte, že modul runtime byl úspěšně nainstalován a spuštěn.  

Zkontrolujte stav služby IoT Edge.

```powershell
Get-Service iotedge
```

Prověřte protokoly služby za posledních 5 minut.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Vypíše spuštěné moduly.

```powershell
iotedge list
```

## <a name="next-steps"></a>Další kroky

Proces registrace služby Device Provisioning umožňuje nastavit ID zařízení a nedokončené značky zařízení současně, když zřizujete nové zařízení. Tyto hodnoty můžete použít k zaměření jednotlivých zařízení nebo skupin zařízení pomocí automatické správy zařízení. Naučte se, jak [nasadit a monitorovat IoT Edge moduly ve velkém měřítku pomocí Azure Portal](how-to-deploy-at-scale.md) nebo [pomocí Azure CLI](how-to-deploy-cli-at-scale.md) .
