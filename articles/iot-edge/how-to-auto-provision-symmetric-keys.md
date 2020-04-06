---
title: Zřizovací zařízení pomocí symetrického atestace klíče – Azure IoT Edge
description: Použití symetrického atestace klíče k testování automatického zřizování zařízení pro Azure IoT Edge se službou zřizování zařízení
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 139a2cafe137d000b991cbad8b8567e005ffc728
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668677"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Vytvoření a zřízení zařízení IoT Edge pomocí symetrického atestace klíče

Zařízení Azure IoT Edge se můžou automaticky zřizovat pomocí [služby Zřizování zařízení,](../iot-dps/index.yml) stejně jako zařízení, která nemají povolenou výhodu. Pokud nejste obeznámeni s procesem automatického zřizování, zkontrolujte [koncepty automatického zřizování](../iot-dps/concepts-auto-provisioning.md) před pokračováním.

Tento článek ukazuje, jak vytvořit individuální zápis služby Device Provisioning Service pomocí atestace symetrického klíče na zařízení IoT Edge s následujícími kroky:

* Vytvořte instanci služby Zřizování zařízení služby IoT Hub (DPS).
* Vytvořte individuální registraci pro zařízení.
* Nainstalujte runtime IoT Edge a připojte se k ioT hubu.

Symetrické atestace klíče je jednoduchý přístup k ověřování zařízení s instancí služby Device Provisioning Service. Tato metoda atestace představuje prostředí "Hello world" pro vývojáře, kteří jsou nové pro zřizování zařízení nebo nemají přísné požadavky na zabezpečení. Atestace zařízení pomocí certifikátů [TPM](../iot-dps/concepts-tpm-attestation.md) nebo [X.509](../iot-dps/concepts-security.md#x509-certificates) je bezpečnější a měla by být použita pro přísnější požadavky na zabezpečení.

## <a name="prerequisites"></a>Požadavky

* Aktivní centrum IoT Hub
* Fyzické nebo virtuální zařízení

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Nastavení služby zřizování zařízení služby IoT Hub

Vytvořte novou instanci služby Zřizování zařízení služby IoT Hub v Azure a propojte ji s vaším centrem IoT hub. Podle pokynů můžete [postupovat v části Nastavení DPS centra IoT](../iot-dps/quick-setup-auto-provision.md)Hub .

Po spuštění služby Device Provisioning Service zkopírujte hodnotu **oboru ID** ze stránky s přehledem. Tuto hodnotu použijete při konfiguraci běhu IoT Edge.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Zvolte jedinečné ID registrace pro zařízení

Pro identifikaci každého zařízení musí být definováno jedinečné ID registrace. Můžete použít mac adresu, sériové číslo nebo jakékoli jedinečné informace ze zařízení.

V tomto příkladu používáme kombinaci ADRESY MAC a sériového čísla tvořícího následující řetězec pro ID registrace: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`.

Vytvořte jedinečné ID registrace pro vaše zařízení. Platné znaky jsou malá alfanumerická a pomlčka ('-').

## <a name="create-a-dps-enrollment"></a>Vytvoření registrace DPS

Pomocí ID registrace vašeho zařízení vytvořte individuální registraci v DPS.

Při vytváření registrace v DPS máte možnost deklarovat **počáteční stav dvojčete zařízení**. V dvojčeti zařízení můžete nastavit značky pro seskupení zařízení podle libovolné metriky, kterou potřebujete ve vašem řešení, jako je oblast, prostředí, umístění nebo typ zařízení. Tyto značky se používají k vytvoření [automatických nasazení](how-to-deploy-monitor.md).

> [!TIP]
> Skupiny zápisy jsou také možné při použití symetrické hoda a osazení a zahrnují stejná rozhodnutí jako jednotlivé zápisy.

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na instanci služby Zřizování zařízení služby IoT Hub.

1. V části **Nastavení**vyberte **Spravovat registrace**.

1. Vyberte **Přidat individuální zápis** a proveďte následující kroky konfigurace registrace:  

   1. V **případě mechanismu**vyberte možnost **Symetrický klíč**.

   1. Zaškrtněte políčko **Automaticky generovat klávesy.**

   1. Zadejte **ID registrace,** které jste pro své zařízení vytvořili.

   1. Pokud chcete, zadejte **ID zařízení centra IoT** Hub pro své zařízení. ID zařízení můžete použít k cílení na jednotlivé zařízení pro nasazení modulu. Pokud ID zařízení nezadáte, použije se ID registrace.

   1. Vyberte **True,** chcete-li deklarovat, že registrace je pro zařízení IoT Edge. Pro registraci skupiny musí být všechna zařízení zařízení IoT Edge nebo žádná z nich nemůže být.

   > [!TIP]
   > V rozhraní příkazového příkazu k systému Azure můžete vytvořit [registraci](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment) nebo [skupinu zápisů](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group) a pomocí **příznaku s podporou okrajů** určit, že zařízení nebo skupina zařízení je zařízení IoT Edge.

   1. Přijměte výchozí hodnotu ze zásad přidělení služby Device Provisioning Service pro **způsob přiřazení zařízení k rozbočovačům** nebo zvolte jinou hodnotu, která je specifická pro tento zápis.

   1. Vyberte propojené **služby IoT Hub,** ke kterému chcete zařízení připojit. Můžete zvolit více rozbočovačů a zařízení bude přiřazeno jednomu z nich podle vybraných zásad přidělení.

   1. **Zvolte, jak chcete s daty zařízení nacházet při opětovném zřizování,** když zařízení poprvním požadavku na zřízení požadují zřizování.

   1. Pokud chcete, přidejte hodnotu značky do **počátečního stavu dvojčete zařízení.** Značky můžete použít k cílení skupin zařízení pro nasazení modulu. Například:

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

   1. Ujistěte **se, že** položka Povolit je nastavena na **povolit**.

   1. Vyberte **Uložit**.

Teď, když pro toto zařízení existuje registrace, může runtime IoT Edge automaticky zřídit zařízení během instalace. Nezapomeňte zkopírovat hodnotu **primárního klíče** registrace, která se použije při instalaci runtime IoT Edge, nebo pokud budete vytvářet klíče zařízení pro použití s registrací skupiny.

## <a name="derive-a-device-key"></a>Odvození klíče zařízení

> [!NOTE]
> Tato část je vyžadována pouze v případě, že používáte registraci skupiny.

Každé zařízení používá svůj odvozený klíč zařízení s jedinečným ID registrace k provedení symetrického atestace klíče s registrací během zřizování. Chcete-li generovat klíč zařízení, použijte klíč, který jste zkopírovali z registrace DPS, k výpočtu [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) jedinečného Registračního ID pro zařízení a převeďte výsledek do formátu Base64.

Do kódu zařízení nezahrnujte primární nebo sekundární klíč registrace.

### <a name="linux-workstations"></a>Linuxové pracovní stanice

Pokud používáte pracovní stanici Linux, můžete použít openssl ke generování odvozeného klíče zařízení, jak je znázorněno v následujícím příkladu.

Nahraďte hodnotu **klíče** **primárním klíčem,** který jste si poznamenali dříve.

Nahraďte hodnotu **REG_ID** registračním ID zařízení.

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

Pokud používáte pracovní stanici se systémem Windows, můžete pomocí prostředí PowerShell vygenerovat odvozený klíč zařízení, jak je znázorněno v následujícím příkladu.

Nahraďte hodnotu **klíče** **primárním klíčem,** který jste si poznamenali dříve.

Nahraďte hodnotu **REG_ID** registračním ID zařízení.

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

## <a name="install-the-iot-edge-runtime"></a>Instalace runtime IoT Edge

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Jeho součásti spustit v kontejnerech a umožňují nasadit další kontejnery do zařízení, takže můžete spustit kód na okraji.

Při zřizování zařízení budete potřebovat následující informace:

* Hodnota **oboru DPS ID**
* **ID registrace** zařízení, které jste vytvořili
* **Primární klíč,** který jste zkopírovali z registrace DPS

> [!TIP]
> Pro registrace skupiny potřebujete [odvozený klíč](#derive-a-device-key) každého zařízení, nikoli registrační klíč DPS.

### <a name="linux-device"></a>Linuxové zařízení

Postupujte podle pokynů pro architekturu vašeho zařízení. Ujistěte se, že konfigurace ioT Edge runtime pro automatické, ne ruční zřizování.

[Instalace runtime Azure IoT Edge na Linux](how-to-install-iot-edge-linux.md)

Oddíl v konfiguračním souboru pro zřizování symetrických klíčů vypadá takto:

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

Nahraďte zástupné `<SCOPE_ID>` `<REGISTRATION_ID>`hodnoty `<SYMMETRIC_KEY>` pro , a data, která jste shromáždili dříve. Ujistěte se, že **zřizování:** řádek nemá žádné předchozí mezery a že vnořené položky jsou odsazeny dvěma mezerami.

### <a name="windows-device"></a>Zařízení s Windows

Nainstalujte runtime IoT Edge na zařízení, pro které jste vygenerovali odvozený klíč zařízení. Nakonfigurujete runtime IoT Edge pro automatické, ne ruční zřizování.

Podrobnější informace o instalaci IoT Edge do Windows, včetně předpokladů a pokynů pro úlohy, jako je správa kontejnerů a aktualizace IoT Edge, najdete [v tématu Instalace runtime Azure IoT Edge v systému Windows](how-to-install-iot-edge-windows.md).

1. Otevřete okno PowerShellu v režimu správce. Nezapomeňte použít relaci AMD64 powershellu při instalaci IoT Edge, ne PowerShell (x86).

1. Příkaz **Deploy-IoTEdge** zkontroluje, zda je váš počítač se systémem Windows v podporované verzi, zapne funkci kontejnerů a poté stáhne modul runtime moby a modul runtime IoT Edge. Příkaz je výchozí pro použití kontejnerů systému Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. V tomto okamžiku se zařízení IoT Core mohou automaticky restartovat. K restartování vás mohou vyzvat jiná zařízení s Windows 10 nebo Windows Server. Pokud ano, restartujte zařízení nyní. Jakmile je vaše zařízení připravené, spusťte PowerShell znovu jako správce.

1. Příkaz **Initialize-IoTEdge** konfiguruje runtime IoT Edge ve vašem počítači. Příkaz výchozí ruční zřizování s kontejnery `-Dps` systému Windows, pokud používáte příznak použít automatické zřizování.

   Nahraďte zástupné `{scope_id}` `{registration_id}`hodnoty `{symmetric_key}` pro , a data, která jste shromáždili dříve.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Pokud modul runtime úspěšně začal, můžete přejít do služby IoT Hub a začít nasazovat moduly IoT Edge do vašeho zařízení. Pomocí následujících příkazů v zařízení ověřte, zda byl runtime úspěšně nainstalován a spuštěn.

### <a name="linux-device"></a>Linuxové zařízení

Zkontrolujte stav služby IoT Edge.

```cmd/sh
systemctl status iotedge
```

Zkontrolujte protokoly služby.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Seznam spuštěných modulů.

```cmd/sh
iotedge list
```

### <a name="windows-device"></a>Zařízení s Windows

Zkontrolujte stav služby IoT Edge.

```powershell
Get-Service iotedge
```

Zkontrolujte protokoly služby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Seznam spuštěných modulů.

```powershell
iotedge list
```

Můžete ověřit, že byla použita individuální registrace, kterou jste vytvořili ve službě Device Provisioning Service. Přejděte na instanci služby zřizování zařízení na webu Azure Portal. Otevřete podrobnosti o registraci pro jednotlivé registrace, které jste vytvořili. Všimněte si, že je **přiřazen** stav registrace a id zařízení je uveden.

## <a name="next-steps"></a>Další kroky

Proces registrace služby Device Provisioning Service umožňuje nastavit ID zařízení a značky dvojčete zařízení současně s zřízením nového zařízení. Tyto hodnoty můžete použít k cílení na jednotlivá zařízení nebo skupiny zařízení pomocí automatické správy zařízení. Zjistěte, jak [nasadit a monitorovat moduly IoT Edge ve velkém měřítku pomocí portálu Azure](how-to-deploy-monitor.md) nebo [pomocí rozhraní příkazového příkazového příkazu Azure](how-to-deploy-monitor-cli.md).
