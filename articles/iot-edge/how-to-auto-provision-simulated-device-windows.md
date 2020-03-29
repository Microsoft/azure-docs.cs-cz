---
title: Automatické zřizování zařízení s Windows pomocí DPS – Azure IoT Edge | Dokumenty společnosti Microsoft
description: Použití simulovaného zařízení na počítači s Windows k testování automatického zřizování zařízení pro Azure IoT Edge se službou zřizování zařízení
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ee4f01c3ec57b0cf9e3ecf47254b57be95ea051a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76510936"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Vytvoření a zřízení simulovaného zařízení IoT Edge s virtuálním čipem TPM v systému Windows

Zařízení Azure IoT Edge se můžou automaticky zřizovat pomocí [služby Zřizování zařízení,](../iot-dps/index.yml) stejně jako zařízení, která nemají povolenou výhodu. Pokud nejste obeznámeni s procesem automatického zřizování, zkontrolujte [koncepty automatického zřizování](../iot-dps/concepts-auto-provisioning.md) před pokračováním.

DPS podporuje symetrické atestace klíčů pro zařízení IoT Edge v individuální registraci i registraci skupiny. Pokud pro registraci skupiny zaškrtnete možnost "is IoT Edge device" tak, aby byla pravdivá v symetrickém atestaci klíčů, všechna zařízení, která jsou registrována v této skupině registrací, budou označena jako zařízení IoT Edge.

Tento článek ukazuje, jak otestovat automatické zřizování na simulovaném zařízení IoT Edge pomocí následujících kroků:

* Vytvořte instanci služby Zřizování zařízení služby IoT Hub (DPS).
* Vytvořte simulované zařízení v počítači se systémem Windows se simulovaným modulem TPM (Trusted Platform Module) pro zabezpečení hardwaru.
* Vytvořte individuální registraci pro zařízení.
* Nainstalujte runtime IoT Edge a připojte zařízení k ioT hubu.

> [!TIP]
> Tento článek popisuje testování automatického zřizování pomocí ověřování čipu TPM na virtuálních zařízeních, ale velká část z toho platí i při použití fyzického hardwaru čipu TPM.

## <a name="prerequisites"></a>Požadavky

* Vývojový počítač systému Windows. Tento článek používá Windows 10.
* Aktivní služba IoT Hub.

> [!NOTE]
> Čip TPM 2.0 je vyžadován při použití atestace čipu TPM s DPS a lze jej použít pouze k vytvoření individuálních, nikoli skupinových zápisů.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Nastavení služby zřizování zařízení služby IoT Hub

Vytvořte novou instanci služby Zřizování zařízení služby IoT Hub v Azure a propojte ji s vaším centrem IoT hub. Podle pokynů můžete [postupovat v části Nastavení DPS centra IoT](../iot-dps/quick-setup-auto-provision.md)Hub .

Po spuštění služby Device Provisioning Service zkopírujte hodnotu **oboru ID** ze stránky s přehledem. Tuto hodnotu použijete při konfiguraci běhu IoT Edge.

> [!TIP]
> Pokud používáte fyzické zařízení TPM, musíte určit **klíč potvrzení**, který je jedinečný pro každý čip Čip TPM a je získán od výrobce čipu TPM, který je k němu přidružen. Jedinečné **ID registrace** pro zařízení TPM můžete odvodit například vytvořením hash SHA-256 ověřovacího klíče.
>
> Postupujte podle pokynů v článku [Jak spravovat registrace zařízení pomocí Portálu Azure,](../iot-dps/how-to-manage-enrollments.md) abyste mohli vytvořit registraci v DPS, a pak pokračujte v části Instalace [runtime IoT Edge](#install-the-iot-edge-runtime) v tomto článku, abyste mohli pokračovat.

## <a name="simulate-a-tpm-device"></a>Simulace zařízení TPM

Vytvořte simulované zařízení TPM ve vývojovém počítači se systémem Windows. Načtěte **id registrace** a **ověřovací klíč** pro své zařízení a použijte je k vytvoření jednotlivé položky registrace v DPS.

Při vytváření registrace v DPS máte možnost deklarovat **počáteční stav dvojčete zařízení**. V dvojčeti zařízení můžete nastavit značky pro seskupení zařízení podle libovolné metriky, kterou potřebujete ve vašem řešení, jako je oblast, prostředí, umístění nebo typ zařízení. Tyto značky se používají k vytvoření [automatických nasazení](how-to-deploy-monitor.md).

Zvolte jazyk sady SDK, který chcete použít k vytvoření simulované zařízení a postupujte podle pokynů, dokud nevytvoříte individuální zápis.

Při vytváření individuální registrace vyberte **True,** chcete-li deklarovat, že simulované zařízení TPM ve vývojovém počítači se systémem Windows je **zařízení IoT Edge**.

Simulované zařízení a individuální průvodce zápisem:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C #](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Po vytvoření jednotlivé registrace uložte hodnotu **ID registrace**. Tuto hodnotu použijete při konfiguraci běhu IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Instalace runtime IoT Edge

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Jeho součásti spustit v kontejnerech a umožňují nasadit další kontejnery do zařízení, takže můžete spustit kód na okraji.

Při zřizování zařízení budete potřebovat následující informace:

* Hodnota **oboru DPS ID**
* **ID registrace** zařízení, které jste vytvořili

Nainstalujte runtime IoT Edge do zařízení, na které běží simulovaný čip TPM. Nakonfigurujete runtime IoT Edge pro automatické, ne ruční zřizování.

> [!TIP]
> Během instalace a testování ponechte otevřené okno se spuštěnou simulátorem čipu TPM.

Podrobnější informace o instalaci IoT Edge do Windows, včetně předpokladů a pokynů pro úlohy, jako je správa kontejnerů a aktualizace IoT Edge, najdete [v tématu Instalace runtime Azure IoT Edge v systému Windows](how-to-install-iot-edge-windows.md).

1. Otevřete okno PowerShellu v režimu správce. Nezapomeňte použít relaci AMD64 powershellu při instalaci IoT Edge, ne PowerShell (x86).

1. Příkaz **Deploy-IoTEdge** zkontroluje, zda je váš počítač se systémem Windows v podporované verzi, zapne funkci kontejnerů a poté stáhne modul runtime moby a modul runtime IoT Edge. Příkaz je výchozí pro použití kontejnerů systému Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. V tomto okamžiku se zařízení IoT Core mohou automaticky restartovat. K restartování vás mohou vyzvat jiná zařízení s Windows 10 nebo Windows Server. Pokud ano, restartujte zařízení nyní. Jakmile je vaše zařízení připravené, spusťte PowerShell znovu jako správce.

1. Příkaz **Initialize-IoTEdge** konfiguruje runtime IoT Edge ve vašem počítači. Příkaz výchozí ruční zřizování s kontejnery systému Windows. Pomocí `-Dps` příznaku můžete místo ručního zřizování používat službu Zřizování zařízení.

   Nahraďte zástupné `{scope_id}` `{registration_id}` hodnoty pro data, která jste shromáždili dříve, a za ně.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Pokud modul runtime úspěšně začal, můžete přejít do služby IoT Hub a začít nasazovat moduly IoT Edge do vašeho zařízení. Pomocí následujících příkazů v zařízení ověřte, zda byl runtime úspěšně nainstalován a spuštěn.  

Zkontrolujte stav služby IoT Edge.

```powershell
Get-Service iotedge
```

Zkontrolujte protokoly služby z posledních 5 minut.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Seznam spuštěných modulů.

```powershell
iotedge list
```

## <a name="next-steps"></a>Další kroky

Proces registrace služby Device Provisioning Service umožňuje nastavit ID zařízení a značky dvojčete zařízení současně s zřízením nového zařízení. Tyto hodnoty můžete použít k cílení na jednotlivá zařízení nebo skupiny zařízení pomocí automatické správy zařízení. Zjistěte, jak [nasadit a monitorovat moduly IoT Edge ve velkém měřítku pomocí portálu Azure](how-to-deploy-monitor.md) nebo [pomocí rozhraní příkazového příkazu Azure](how-to-deploy-monitor-cli.md)
