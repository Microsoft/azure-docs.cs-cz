---
title: Zřízení zařízení pomocí ověření identity symetrického klíče – Azure IoT Edge
description: Použití ověření identity symetrického klíče k testování automatického zřizování zařízení pro Azure IoT Edge se službou Device Provisioning
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4c44ad91b4fb8581a67ea67e09faca4a9d96df91
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91447768"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Vytvoření a zřízení zařízení IoT Edge pomocí ověřování symetrického klíče

Zařízení Azure IoT Edge se dají automaticky zřídit pomocí [služby Device Provisioning](../iot-dps/index.yml) , stejně jako zařízení, která nejsou povolená přes hranice. Pokud neznáte proces automatického zřizování, přečtěte si přehled [zřizování](../iot-dps/about-iot-dps.md#provisioning-process) a teprve potom pokračujte.

V tomto článku se dozvíte, jak vytvořit službu Device Provisioning pomocí ověření symetrického klíče na zařízení IoT Edge pomocí následujících kroků:

* Vytvořte instanci IoT Hub Device Provisioning Service (DPS).
* Vytvořte jednotlivou registraci zařízení.
* Nainstalujte modul runtime IoT Edge a připojte se k IoT Hub.

Symetrický ověření identity je jednoduchý přístup k ověřování zařízení pomocí instance služby Device Provisioning. Tato metoda ověření identity představuje prostředí "Hello World" pro vývojáře, kteří jsou noví v zřizování zařízení, nebo nemají přísné požadavky na zabezpečení. Ověření zařízení pomocí [čipu TPM](../iot-dps/concepts-tpm-attestation.md) nebo [X. 509](../iot-dps/concepts-security.md#x509-certificates) je bezpečnější a mělo by se používat pro přísnější požadavky na zabezpečení.

## <a name="prerequisites"></a>Požadavky

* Aktivní IoT Hub
* Fyzické nebo virtuální zařízení

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Nastavení IoT Hub Device Provisioning Service

Vytvořte novou instanci IoT Hub Device Provisioning Service v Azure a propojte ji se službou IoT Hub. Můžete postupovat podle pokynů v tématu [nastavení IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

Po spuštění služby Device Provisioning zkopírujte na stránce Přehled hodnotu **Rozsah ID** . Tuto hodnotu použijete při konfiguraci modulu runtime IoT Edge.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Zvolit jedinečný registrační identifikátor zařízení

Aby bylo možné identifikovat jednotlivá zařízení, musí být definováno jedinečné ID registrace. V zařízení můžete použít adresu MAC, sériové číslo nebo libovolné jedinečné informace.

V tomto příkladu používáme kombinaci adresy MAC a sériového čísla, které tvoří následující řetězec pro ID registrace: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6` .

Vytvořte jedinečné ID registrace pro vaše zařízení. Platné znaky jsou malé alfanumerické znaky a spojovníky (-).

## <a name="create-a-dps-enrollment"></a>Vytvoření registrace DPS

Pomocí ID registrace vašeho zařízení vytvořte jednotlivou registraci v DPS.

Když vytvoříte registraci v DPS, budete mít možnost deklarovat **počáteční stav**dopředných zařízení. V případě zařízení můžete nastavit značky pro seskupení zařízení podle libovolné metriky, kterou potřebujete ve vašem řešení, jako je oblast, prostředí, umístění nebo typ zařízení. Tyto značky slouží k vytváření [automatických nasazení](how-to-deploy-at-scale.md).

> [!TIP]
> Registrace skupin je také možné použít při použití symetrického ověřování klíčů a zahrnovat stejná rozhodnutí jako jednotlivé registrace.

1. V [Azure Portal](https://portal.azure.com)přejděte do vaší instance IoT Hub Device Provisioning Service.

1. V části **Nastavení**vyberte **spravovat registrace**.

1. Vyberte **přidat jednotlivou registraci** a potom proveďte následující kroky, abyste nakonfigurovali registraci:  

   1. V případě **mechanismu**vyberte **symetrický klíč**.

   1. Zaškrtněte políčko **automaticky generovat klíče** .

   1. Zadejte **ID registrace** , které jste vytvořili pro vaše zařízení.

   1. Pokud chcete, zadejte **ID zařízení IoT Hub** . Pomocí ID zařízení můžete cílit na jednotlivá zařízení pro nasazení modulů. Pokud ID zařízení nezadáte, použije se ID registrace.

   1. Vyberte **hodnotu true** , pokud chcete deklarovat, že registrace je určena pro IoT Edge zařízení. V případě registrace skupiny musí být všechna zařízení IoT Edge nebo žádná z nich nemůžete.

   > [!TIP]
   > V Azure CLI můžete vytvořit [registraci](/cli/azure/ext/azure-iot/iot/dps/enrollment) nebo [skupinu registrace](/cli/azure/ext/azure-iot/iot/dps/enrollment-group) a pomocí příznaku s **povoleným okrajem** určit, že zařízení nebo skupina zařízení je IoT Edge zařízení.

   1. Přijměte výchozí hodnotu ze zásad přidělení služby Device Provisioning pro **způsob, jakým chcete přiřadit zařízení k rozbočovačům** , nebo vyberte jinou hodnotu, která je specifická pro tento zápis.

   1. Vyberte propojené **IoT Hub** , ke kterým chcete zařízení připojit. Můžete zvolit více rozbočovačů a zařízení bude přiřazeno k jednomu z nich podle vybrané zásady přidělování.

   1. Vyberte **, jak chcete, aby se data zařízení při opětovném zřizování zpracovala** při zřizování zařízení po prvním přihlášení.

   1. Pokud chcete, přidejte hodnotu značky do **počátečního stavového vlákna zařízení** . Pomocí značek můžete cílit skupiny zařízení na nasazení modulů. Například:

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

Hodnotu **REG_ID** nahraďte ID registrace vašeho zařízení.

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

Hodnotu **REG_ID** nahraďte ID registrace vašeho zařízení.

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

## <a name="install-the-iot-edge-runtime"></a>Instalace modulu runtime IoT Edge

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Jeho komponenty se spouštějí v kontejnerech a umožňují na zařízení nasadit další kontejnery, abyste mohli spustit kód na hraničních zařízeních.

Při zřizování zařízení budete potřebovat následující informace:

* Hodnota **rozsahu ID** DPS
* **ID registrace** zařízení, které jste vytvořili
* **Primární klíč** , který jste zkopírovali z registrace DPS

> [!TIP]
> Pro zápis skupin potřebujete místo registračního klíče každého zařízení [odvozený klíč](#derive-a-device-key) každého zařízení.

### <a name="linux-device"></a>Zařízení se systémem Linux

Postupujte podle pokynů pro architekturu vašeho zařízení. Nezapomeňte nakonfigurovat IoT Edge runtime pro automatické, ne ruční zřizování.

[Instalace modulu runtime Azure IoT Edge v systému Linux](how-to-install-iot-edge-linux.md)

Oddíl konfiguračního souboru pro zajištění symetrického klíče vypadá takto:

```yaml
# DPS symmetric key provisioning configuration
provisioning:
   source: "dps"
   global_endpoint: "https://global.azure-devices-provisioning.net"
   scope_id: "<SCOPE_ID>"
   attestation:
      method: "symmetric_key"
      registration_id: "<REGISTRATION_ID>"
      symmetric_key: "<SYMMETRIC_KEY>"
```

Nahraďte zástupné hodnoty pro `<SCOPE_ID>` , `<REGISTRATION_ID>` a `<SYMMETRIC_KEY>` daty, která jste shromáždili dříve. Ujistěte se, že **zřizování:** řádek neobsahuje žádné předchozí prázdné znaky a že vnořené položky jsou odsazeny dvěma mezerami.

### <a name="windows-device"></a>Zařízení s Windows

Nainstalujte modul runtime IoT Edge na zařízení, pro které jste vygenerovali odvozený klíč zařízení. Nastavíte modul runtime IoT Edge pro automatické, ne ruční zřizování.

Podrobnější informace o instalaci IoT Edge ve Windows, včetně požadavků a pokynů pro úlohy, jako je Správa kontejnerů a aktualizace IoT Edge, najdete v tématu [Instalace modulu runtime Azure IoT Edge ve Windows](how-to-install-iot-edge-windows.md).

1. Otevřete okno PowerShellu v režimu správce. Při instalaci IoT Edge, ne pomocí PowerShellu (x86) nezapomeňte použít relaci AMD64 prostředí PowerShell.

1. Příkaz **Deploy-IoTEdge** zkontroluje, jestli má počítač s Windows podporovanou verzi, zapne funkci Containers a pak stáhne modul runtime Moby a modul runtime IoT Edge. Příkaz ve výchozím nastavení používá kontejnery Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. V tuto chvíli se zařízení IoT Core můžou restartovat automaticky. Jiná zařízení s Windows 10 nebo Windows Server vás můžou vyzvat k restartování. Pokud ano, restartujte zařízení nyní. Až bude zařízení připravené, spusťte PowerShell jako správce znovu.

1. Příkaz **Initialize-IoTEdge** nakonfiguruje IoT Edge modul runtime na vašem počítači. Příkaz se ve výchozím nastavení provede ručním zřizováním pomocí kontejnerů Windows, pokud nepoužijete `-Dps` příznak pro Automatické zřizování.

   Nahraďte zástupné hodnoty pro `{scope_id}` , `{registration_id}` a `{symmetric_key}` daty, která jste shromáždili dříve.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Pokud se modul runtime úspěšně spustil, můžete přejít do svého IoT Hub a začít nasazovat IoT Edge moduly do svého zařízení. Pomocí následujících příkazů na zařízení ověřte, že modul runtime byl úspěšně nainstalován a spuštěn.

### <a name="linux-device"></a>Zařízení se systémem Linux

Zkontrolujte stav služby IoT Edge.

```cmd/sh
systemctl status iotedge
```

Projděte si protokoly služby.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Vypíše spuštěné moduly.

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

Vypíše spuštěné moduly.

```powershell
iotedge list
```

Můžete ověřit, že se použil jednotlivý zápis, který jste vytvořili v rámci služby Device Provisioning. V Azure Portal přejděte na instanci služby Device Provisioning. Otevřete podrobnosti registrace pro jednotlivou registraci, kterou jste vytvořili. Všimněte si, že je **přiřazený** stav registrace a že je uvedené ID zařízení.

## <a name="next-steps"></a>Další kroky

Proces registrace služby Device Provisioning umožňuje nastavit ID zařízení a nedokončené značky zařízení současně, když zřizujete nové zařízení. Tyto hodnoty můžete použít k zaměření jednotlivých zařízení nebo skupin zařízení pomocí automatické správy zařízení. Naučte se, jak [nasadit a monitorovat IoT Edge moduly ve velkém měřítku pomocí Azure Portal](how-to-deploy-at-scale.md) nebo [pomocí Azure CLI](how-to-deploy-cli-at-scale.md).
