---
title: Automatického zřizování zařízení s DPS pomocí ověřování symetrického klíče pomocí ověření identity-Azure IoT Edge | Microsoft Docs
description: Použití ověření identity symetrického klíče k testování automatického zřizování zařízení pro Azure IoT Edge se službou Device Provisioning
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5a7e7fa011c0287d5e97ad7a8cd2e3ba77f298dd
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299852"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Vytvoření a zřízení zařízení IoT Edge pomocí ověřování symetrického klíče

Zařízení Azure IoT Edge můžou být autoprovisioned pomocí [služby Device Provisioning](../iot-dps/index.yml) stejně jako zařízení, která nejsou povolena edge. Pokud neznáte proces autoprovisioning, přečtěte si [autoprovisioning koncepty](../iot-dps/concepts-auto-provisioning.md) než budete pokračovat.

V tomto článku se dozvíte, jak vytvořit službu Device Provisioning pomocí ověření symetrického klíče na zařízení IoT Edge pomocí následujících kroků:

* Vytvoření instance z IoT Hubu zařízení zřizování služby (DPS).
* Vytvořte jednotlivou registraci pro zařízení.
* Nainstalujte modul runtime IoT Edge a připojte se k IoT Hub.

Symetrický ověření identity je jednoduchý přístup k ověřování zařízení pomocí instance služby Device Provisioning. Tato metoda ověření identity představuje prostředí "Hello World" pro vývojáře, kteří jsou noví v zřizování zařízení, nebo nemají přísné požadavky na zabezpečení. Ověření zařízení pomocí [čipu TPM](../iot-dps/concepts-tpm-attestation.md) je bezpečnější a mělo by se používat pro přísnější požadavky na zabezpečení.

## <a name="prerequisites"></a>Požadavky

* Aktivní IoT Hub
* Fyzické nebo virtuální zařízení

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Nastavte si IoT Hub Device Provisioning Service

Vytvořit novou instanci IoT Hub Device Provisioning Service v Azure a propojit jej do služby IoT hub. Můžete podle pokynů v [nastavení IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

Jakmile budete mít spuštěné služby Device Provisioning Service, zkopírujte hodnotu **rozsah ID** na stránce Přehled. Tuto hodnotu použijete při konfiguraci modulu runtime IoT Edge.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Zvolit jedinečný registrační identifikátor zařízení

Aby bylo možné identifikovat jednotlivá zařízení, musí být definováno jedinečné ID registrace. V zařízení můžete použít adresu MAC, sériové číslo nebo libovolné jedinečné informace.

V tomto příkladu používáme kombinaci adresy MAC a sériového čísla, které tvoří následující řetězec pro ID registrace.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Vytvořte jedinečné ID registrace pro vaše zařízení. Platné znaky jsou malé alfanumerické znaky a spojovníky (-).

## <a name="create-a-dps-enrollment"></a>Vytvoření registrace distribučních bodů

Pomocí ID registrace vašeho zařízení vytvořte jednotlivou registraci v DPS.

Až vytvořit registraci ve službě Device Provisioning, budete mít příležitost k deklaraci **počáteční stav Dvojčete zařízení**. Ve dvojčeti zařízení můžete nastavit značky k seskupení zařízení podle libovolné metriky, které potřebujete ve vašem řešení, jako je oblast, prostředí, umístění nebo zařízení typu. Tyto značky se používají k vytváření [automatické nasazení](how-to-deploy-monitor.md).

> [!TIP]
> Registrace skupin je také možné použít při použití symetrického ověřování klíčů a zahrnovat stejná rozhodnutí jako jednotlivé registrace.

1. V [Azure Portal](https://portal.azure.com)přejděte do vaší instance IoT Hub Device Provisioning Service.

1. V části **nastavení**vyberte **Správa registrací**.

1. Vyberte **přidat jednotlivou registraci** pak dokončete následující postup pro konfiguraci registrace:  

   1. V případě **mechanismu**vyberte **symetrický klíč**.

   1. Zaškrtněte políčko **automaticky generovat klíče** .

   1. Zadejte **ID registrace** , které jste vytvořili pro vaše zařízení.

   1. Pokud chcete, zadejte **ID zařízení IoT Hub** . ID zařízení můžete cílit na jednotlivá zařízení pro nasazení modulu. Pokud ID zařízení nezadáte, použije se ID registrace.

   1. Vyberte **hodnotu true** , pokud chcete deklarovat, že registrace je určena pro IoT Edge zařízení. V případě registrace skupiny musí být všechna zařízení IoT Edge nebo žádná z nich nemůžete.

   1. Přijměte výchozí hodnotu ze zásad přidělení služby Device Provisioning pro **způsob, jakým chcete přiřadit zařízení k rozbočovačům** , nebo vyberte jinou hodnotu, která je specifická pro tento zápis.

   1. Zvolte propojený **služby IoT Hub** , že chcete připojení k zařízení. Můžete zvolit více rozbočovačů a zařízení bude přiřazeno k jednomu z nich podle vybrané zásady přidělování.

   1. Vyberte **, jak chcete, aby se data zařízení při opětovném zřizování zpracovala** při zřizování zařízení po prvním přihlášení.

   1. Přidat hodnotu značky k **počáteční stav Dvojčete zařízení** Pokud byste o ni. Značky na cílové skupiny zařízení můžete použít pro nasazení modulu. Příklad:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. Ujistěte se, že **položka povolit položku** je nastavená na **Povolit**.

   1. Vyberte **Uložit**.

Teď, když pro toto zařízení existuje registrace, IoT Edge modul runtime může zařízení během instalace automaticky zřídit. Nezapomeňte zkopírovat hodnotu **primárního klíče** registrace, která se má použít při instalaci IoT Edge runtime, nebo pokud budete vytvářet klíče zařízení pro použití se zápisem skupiny.

## <a name="derive-a-device-key"></a>Odvodit klíč zařízení

> [!NOTE]
> Tato část se vyžaduje jenom v případě, že používáte registraci skupiny.

Každé zařízení používá svůj odvozený klíč zařízení s jedinečným ID registrace k provádění ověření symetrického klíče pomocí zápisu během zřizování. Pokud chcete vygenerovat klíč zařízení, použijte klíč, který jste zkopírovali z registrace DPS, k výpočtu [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) jedinečného ID registrace pro zařízení a převod výsledku na Formát Base64.

Do kódu zařízení nezahrnujte primární ani sekundární klíč registrace.

### <a name="linux-workstations"></a>Pracovní stanice Linux

Pokud používáte pracovní stanici se systémem Linux, můžete použít OpenSSL k vygenerování odvozeného klíče zařízení, jak je znázorněno v následujícím příkladu.

Nahraďte hodnotu **klíče** **primárním klíčem** , který jste si poznamenali dříve.

Nahraďte hodnotu **REG_ID** ID registrace vašeho zařízení.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

### <a name="windows-based-workstations"></a>Pracovní stanice založené na systému Windows

Pokud používáte pracovní stanici se systémem Windows, můžete použít PowerShell k vygenerování odvozeného klíče zařízení, jak je znázorněno v následujícím příkladu.

Nahraďte hodnotu **klíče** **primárním klíčem** , který jste si poznamenali dříve.

Nahraďte hodnotu **REG_ID** ID registrace vašeho zařízení.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

## <a name="install-the-iot-edge-runtime"></a>Nainstalovat modul runtime IoT Edge

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Jeho součástí spouštění v kontejnerech a můžete nasadit další kontejnery do zařízení tak, aby kód můžete spustit na hraničních zařízeních.

Při zřizování zařízení budete potřebovat následující informace:

* Hodnota **rozsahu ID** DPS
* **ID registrace** zařízení, které jste vytvořili
* **Primární klíč** , který jste zkopírovali z registrace DPS

> [!TIP]
> Pro zápis skupin potřebujete místo registračního klíče každého zařízení [odvozený klíč](#derive-a-device-key) každého zařízení.

### <a name="linux-device"></a>Zařízení se systémem Linux

Postupujte podle pokynů pro architekturu vašeho zařízení. Ujistěte se, že konfigurace modulu runtime IoT Edge není ruční, automatické zřizování.

[Instalace modulu runtime Azure IoT Edge v systému Linux](how-to-install-iot-edge-linux.md)

Oddíl konfiguračního souboru pro zajištění symetrického klíče vypadá takto:

```yaml
# DPS symmetric key provisioning configuration
provisioning:
   source: "dps"
   global_endpoint: "https://global.azure-devices-provisioning.net"
   scope_id: "{scope_id}"
   attestation:
      method: "symmetric_key"
      registration_id: "{registration_id}"
      symmetric_key: "{symmetric_key}"
```

Nahraďte zástupné `{scope_id}`hodnoty `{registration_id}`pro, `{symmetric_key}` a daty, která jste shromáždili dříve.

### <a name="windows-device"></a>Zařízení s Windows

Podle pokynů nainstalujte modul runtime IoT Edge na zařízení, pro které jste vygenerovali odvozený klíč zařízení. Ujistěte se, že konfigurace modulu runtime IoT Edge není ruční, automatické zřizování.

[Instalace a Automatické zřizování IoT Edge ve Windows](how-to-install-iot-edge-windows.md#option-2-install-and-automatically-provision)

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Pokud modul runtime byl úspěšně spuštěn, můžete přejít do služby IoT Hub a začít nasazovat moduly IoT Edge do zařízení. Ověřte, zda modul runtime nainstalován a úspěšně spustil pomocí následujících příkazů na vašem zařízení.

### <a name="linux-device"></a>Zařízení se systémem Linux

Zkontrolujte stav služby IoT Edge.

```cmd/sh
systemctl status iotedge
```

Projděte si protokoly služby.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Seznam s moduly.

```cmd/sh
iotedge list
```

### <a name="windows-device"></a>Zařízení s Windows

Zkontrolujte stav služby IoT Edge.

```powershell
Get-Service iotedge
```

Projděte si protokoly služby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Seznam s moduly.

```powershell
iotedge list
```

Můžete ověřit, že se použil jednotlivý zápis, který jste vytvořili v rámci služby Device Provisioning. V Azure Portal přejděte na instanci služby Device Provisioning. Otevřete podrobnosti registrace pro jednotlivou registraci, kterou jste vytvořili. Všimněte si, že je přiřazený stav registrace a že je uvedené ID zařízení.

## <a name="next-steps"></a>Další kroky

Proces registrace služby Device Provisioning umožňuje nastavit ID zařízení a značky dvojčat zařízení ve stejnou dobu, jak zřídit nové zařízení. Tyto hodnoty můžete cílit na jednotlivá zařízení nebo skupin pomocí automatické správy zařízení. Zjistěte, jak [nasazení a monitorování modulů při škálování na portálu Azure IoT Edge](how-to-deploy-monitor.md) nebo [pomocí Azure CLI](how-to-deploy-monitor-cli.md).
