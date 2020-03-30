---
title: Automatické zřizování zařízení s DPS pomocí certifikátů X.509 – Azure IoT Edge | Dokumenty společnosti Microsoft
description: Použití certifikátů X.509 k testování automatického zřizování zařízení pro Azure IoT Edge se službou zřizování zařízení
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 03/06/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 48c8179af4a4b69924fb943ac98918b48d3a2008
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537359"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>Vytvoření a zřízení zařízení IoT Edge pomocí certifikátů X.509

Pomocí [služby Azure IoT Hub Device Provisioning Service (DPS)](../iot-dps/index.yml)můžete automaticky zřídit zařízení IoT Edge pomocí certifikátů X.509. Pokud nejste obeznámeni s procesem automatického zřizování, zkontrolujte [koncepty automatického zřizování](../iot-dps/concepts-auto-provisioning.md) před pokračováním.

Tento článek ukazuje, jak vytvořit zápis služby Device Provisioning Service pomocí certifikátů X.509 na zařízení IoT Edge s následujícími kroky:

* Generovat certifikáty a klíče.
* Vytvořte buď individuální registraci pro zařízení, nebo skupinovou registraci pro sadu zařízení.
* Nainstalujte runtime IoT Edge a zaregistrujte zařízení pomocí služby IoT Hub.

Použití certifikátů X.509 jako mechanismu s ověřováním je vynikající způsob, jak škálovat výrobu a zjednodušit zřizování zařízení. Certifikáty X.509 jsou obvykle uspořádány v řetězu důvěryhodnosti certifikátů. Počínaje certifikátem podepsaným svým držitelem nebo důvěryhodným kořenovým certifikátem každý certifikát v řetězci podepíše další nižší certifikát. Tento vzor vytvoří delegovaný řetězec důvěryhodnosti z kořenového certifikátu dolů přes každý zprostředkující certifikát na konečný "listový" certifikát nainstalovaný v zařízení.

## <a name="prerequisites"></a>Požadavky

* Aktivní služba IoT Hub.
* Fyzické nebo virtuální zařízení jako zařízení IoT Edge.
* Nainstalované nejnovější verze [Gitu.](https://git-scm.com/download/)
* Instance služby Zřizování zařízení služby IoT Hub v Azure, propojené s vaším centrem IoT hub.
  * Pokud nemáte instanci služby zřizování zařízení, postupujte podle pokynů v [části Nastavení DPS služby IoT Hub](../iot-dps/quick-setup-auto-provision.md).
  * Po spuštění služby Device Provisioning Service zkopírujte hodnotu **oboru ID** ze stránky s přehledem. Tuto hodnotu použijete při konfiguraci běhu IoT Edge.

## <a name="generate-device-identity-certificates"></a>Generovat certifikáty identity zařízení

Certifikát identity zařízení je listový certifikát, který se připojuje prostřednictvím řetězu důvěryhodnosti certifikátu k nejvyššímu certifikátu certifikační autority X.509. Certifikát identity zařízení musí mít běžný název (CN) nastavený na ID zařízení, které má mít zařízení ve vašem centru IoT Hub.

Certifikáty identity zařízení se používají jenom pro zřizování zařízení IoT Edge a ověřování zařízení pomocí Služby Azure IoT Hub. Na rozdíl od certifikátů certifikační autority, které zařízení IoT Edge představuje modulům nebo listovým zařízením k ověření, se nejedná o podpisové certifikáty. Další informace najdete v [tématu Podrobnosti o využití certifikátu Azure IoT Edge](iot-edge-certs.md).

Po vytvoření certifikátu identity zařízení byste měli mít dva soubory: soubor CER nebo PEM, který obsahuje veřejnou část certifikátu, a soubor CER nebo .pem se soukromým klíčem certifikátu. Pokud plánujete použít zápis skupiny v DPS, potřebujete také veřejnou část zprostředkujícího nebo kořenového certifikátu certifikační autority ve stejném řetězci důvěryhodnosti certifikátů.

### <a name="use-test-certificates"></a>Použití testovacích certifikátů

Pokud nemáte k dispozici certifikační autoritu k vytvoření nových certifikátů identity a chcete vyzkoušet tento scénář, úložiště Git Azure IoT Edge obsahuje skripty, které můžete použít ke generování testovacích certifikátů. Tyto certifikáty jsou určeny pouze pro vývojové testování a nesmí být použity v produkčním prostředí.

Chcete-li vytvořit testovací certifikáty, postupujte podle pokynů v [části Vytvoření ukázkových certifikátů a otestujte funkce zařízení IoT Edge](how-to-create-test-certificates.md). Dokončete dvě požadované části a nastavte skripty pro generování certifikátů a vytvořte kořenový certifikát certifikační autority. Potom postupujte podle pokynů k vytvoření certifikátu identity zařízení. Po dokončení byste měli mít následující řetěz certifikátů a dvojici klíčů:

Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

Potřebujete oba tyto certifikáty na zařízení IoT Edge. Pokud budete používat individuální zápis do DPS, nahrajete soubor .cert.pem. Pokud budete používat skupinový zápis v DPS, budete k nahrání potřebovat také zprostředkující nebo kořenový certifikát certifikační autority ve stejném řetězci důvěryhodnosti certifikátů. Pokud používáte ukázkové certifikáty, `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` použijte certifikát pro zápis do skupiny.

## <a name="create-a-dps-individual-enrollment"></a>Vytvoření individuální registrace DPS

Pomocí generovaných certifikátů a klíčů vytvořte individuální registraci v DPS pro jedno zařízení IoT Edge. Jednotlivé registrace převezmou veřejnou část certifikátu identity zařízení a porovná ji s certifikátem na zařízení.

Pokud chcete zřídit více zařízení IoT Edge, postupujte podle kroků v další části [Vytvoření registrace skupiny DPS](#create-a-dps-group-enrollment).

Při vytváření registrace v DPS máte možnost deklarovat **počáteční stav dvojčete zařízení**. V dvojčeti zařízení můžete nastavit značky pro seskupení zařízení podle libovolné metriky, kterou potřebujete ve vašem řešení, jako je oblast, prostředí, umístění nebo typ zařízení. Tyto značky se používají k vytvoření [automatických nasazení](how-to-deploy-monitor.md).

Další informace o registracích ve službě Device Provisioning Service najdete v tématu [Správa registrací zařízení](../iot-dps/how-to-manage-enrollments.md).

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na instanci služby Zřizování zařízení služby IoT Hub.

1. V části **Nastavení**vyberte **Spravovat registrace**.

1. Vyberte **Přidat individuální zápis** a proveďte následující kroky konfigurace registrace:  

   * **Mechanismus**: Vyberte **X.509**.

   * **Primární soubor Pem nebo CER certifikátu**: Nahrajte veřejný soubor z certifikátu identity zařízení. Pokud jste použili skripty ke generování testovacího certifikátu, zvolte následující soubor:

      `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`

   * **ID zařízení ioT hubu**: Pokud chcete, zadejte ID zařízení. ID zařízení můžete použít k cílení na jednotlivé zařízení pro nasazení modulu. Pokud ID zařízení nezadáte, použije se běžný název (CN) v certifikátu X.509.

   * **Zařízení IoT Edge**: Výběrem možnosti **True** deklarujete, že registrace je pro zařízení IoT Edge.

   * **Vyberte služby IoT huby, kterým lze toto zařízení přiřadit:** Zvolte propojený rozbočovač IoT hub, ke kterému chcete zařízení připojit. Můžete zvolit více rozbočovačů a zařízení bude přiřazeno jednomu z nich podle vybraných zásad přidělení.

   * **Počáteční stav dvojčete zařízení:** Přidejte hodnotu značky, která se přidá do dvojčete zařízení, pokud chcete. Značky můžete použít k cílení skupin zařízení pro automatické nasazení. Například:

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

1. Vyberte **Uložit**.

Teď, když pro toto zařízení existuje registrace, může runtime IoT Edge automaticky zřídit zařízení během instalace. Pokračujte do části [Instalace runtime technologie IoT Edge](#install-the-iot-edge-runtime) a nastavte zařízení IoT Edge.

## <a name="create-a-dps-group-enrollment"></a>Vytvoření registrace skupiny DPS

Pomocí generovaných certifikátů a klíčů vytvořte skupinový zápis v DPS pro více zařízení IoT Edge. Skupinové zápisy používají zprostředkující nebo kořenový certifikát certifikační autority z řetězu důvěryhodnosti certifikátů, který se používá ke generování certifikátů identity jednotlivých zařízení.

Pokud chcete místo toho zřídit jedno zařízení IoT Edge, postupujte podle kroků v předchozí části [Vytvoření individuální registrace DPS](#create-a-dps-individual-enrollment).

Při vytváření registrace v DPS máte možnost deklarovat **počáteční stav dvojčete zařízení**. V dvojčeti zařízení můžete nastavit značky pro seskupení zařízení podle libovolné metriky, kterou potřebujete ve vašem řešení, jako je oblast, prostředí, umístění nebo typ zařízení. Tyto značky se používají k vytvoření [automatických nasazení](how-to-deploy-monitor.md).

### <a name="verify-your-root-certificate"></a>Ověření kořenového certifikátu

Při vytváření skupiny zápisů máte možnost použít ověřený certifikát. Certifikát můžete ověřit pomocí DPS tím, že prokážete, že vlastníte kořenový certifikát. Další informace naleznete v tématu [Jak provést kontrolu vlastnictví certifikátů certifikační autority X.509](../iot-dps/how-to-verify-certificates.md).

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na instanci služby Zřizování zařízení služby IoT Hub.

1. V levé nabídce vyberte **Certifikáty.**

1. Výběrem **možnosti Přidat** přidáte nový certifikát.

1. Zadejte popisný název certifikátu a vyhledejte soubor CER nebo Pem, který představuje veřejnou část certifikátu X.509.

   Pokud používáte ukázkové certifikáty, `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` nahrajte certifikát.

1. Vyberte **Uložit**.

1. Certifikát by nyní měl být uveden na stránce **Certifikáty.** Vyberte ji, chcete-li otevřít podrobnosti o certifikátu.

1. Vyberte **Generovat ověřovací kód** a pak zkopírujte generovaný kód.

1. Bez ohledu na to, zda jste si přinesli vlastní certifikát certifikační autority nebo používáte ukázkové certifikáty, můžete k ověření dokladu o vlastnictví použít ověřovací nástroj uvedený v úložišti IoT Edge. Ověřovací nástroj používá certifikát certifikační autority k podepsání nového certifikátu, který má jako název subjektu zadaný ověřovací kód.

   * Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. Na stejné stránce podrobností o certifikátu na webu Azure Portal nahrajte nově vygenerovaný ověřovací certifikát.

1. Vyberte možnost **ověření**.

### <a name="create-enrollment-group"></a>Vytvořit skupinu zápisů

Další informace o registracích ve službě Device Provisioning Service najdete v tématu [Správa registrací zařízení](../iot-dps/how-to-manage-enrollments.md).

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na instanci služby Zřizování zařízení služby IoT Hub.

1. V části **Nastavení**vyberte **Spravovat registrace**.

1. Vyberte **Přidat skupinu zápisů** a proveďte následující kroky konfigurace zápisu:

   * **Název skupiny**: Zadejte zapamatovatelný název pro registraci této skupiny.

   * **Typ atestace**: Vyberte **certifikát**.

   * **Zařízení IoT Edge**: Vyberte **True**. Pro registraci skupiny musí být všechna zařízení zařízení IoT Edge nebo žádná z nich nemůže být.

   * **Typ certifikátu**: **Pokud** máte ověřený certifikát certifikační autority uložený s DPS, nebo **zprostředkující certifikát,** chcete-li odeslat nový soubor právě pro tento zápis.

   * **Primární certifikát**: Pokud jste v poslední části zvolili certifikát certifikační autority, zvolte certifikát z rozevíracího seznamu. Pokud jste zvolili zprostředkující certifikát, nahrajte veřejný soubor z certifikátu certifikační autority v řetězci důvěryhodnosti certifikátů, který byl použit ke generování certifikátů identity zařízení.

   * **Vyberte služby IoT huby, kterým lze toto zařízení přiřadit:** Zvolte propojený rozbočovač IoT hub, ke kterému chcete zařízení připojit. Můžete zvolit více rozbočovačů a zařízení bude přiřazeno jednomu z nich podle vybraných zásad přidělení.

   * **Počáteční stav dvojčete zařízení:** Přidejte hodnotu značky, která se přidá do dvojčete zařízení, pokud chcete. Značky můžete použít k cílení skupin zařízení pro automatické nasazení. Například:

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

1. Vyberte **Uložit**.

Teď, když pro toto zařízení existuje registrace, může runtime IoT Edge automaticky zřídit zařízení během instalace. Pokračujte k další části a nastavte zařízení IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Instalace runtime IoT Edge

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Jeho součásti spustit v kontejnerech a umožňují nasadit další kontejnery do zařízení, takže můžete spustit kód na okraji.

Zřizování X.509 pomocí DPS je podporováno jenom v ioT edge verze 1.0.9 nebo novější.

Při zřizování zařízení budete potřebovat následující informace:

* Hodnota **oboru DPS ID.** Tuto hodnotu můžete načíst ze stránky přehledu instance DPS na webu Azure Portal.
* Soubor certifikátu identity zařízení v zařízení.
* Soubor klíče identity zařízení v zařízení.
* Volitelné ID registrace (natažené z běžného názvu v certifikátu identity zařízení, pokud není zadáno).

### <a name="linux-device"></a>Linuxové zařízení

Pomocí následujícího odkazu nainstalujte runtime Azure IoT Edge do zařízení pomocí příkazů vhodných pro architekturu vašeho zařízení. Když se dostanete do části o konfiguraci daemon zabezpečení, nakonfigurujte runtime IoT Edge pro X.509 automatické, nikoli ruční zřizování. Po dokončení předchozích částí tohoto článku byste měli mít všechny potřebné informace a soubory certifikátů.

[Instalace runtime Azure IoT Edge na Linux](how-to-install-iot-edge-linux.md)

Přidáte-li certifikát X.509 a informace o klíči do souboru config.yaml, měly by být cesty poskytovány jako identifikátory URI souboru. Například:

* `file:///<path>/identity_certificate.pem`
* `file:///<path>/identity_key.pem`

Oddíl v konfiguračním souboru pro automatické zřizování X.509 vypadá takto:

```yaml
# DPS X.509 provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "x509"
#   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
    identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
    identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
```

Nahraďte zástupné `scope_id` `identity_cert`hodnoty `identity_pk` pro aplikaci , ID oboru z instance DPS a identifikátory URI na umístění certifikátu a klíčových souborů v zařízení. Pokud `registration_id` chcete, poskytněte zařízení nebo ponechte tento řádek zakomentovaný pro registraci zařízení s názvem kn.

Po aktualizaci souboru config.yaml vždy restartujte bezpečnostního daemonu.

```bash
sudo systemctl restart iotedge
```

### <a name="windows-device"></a>Zařízení s Windows

Nainstalujte runtime IoT Edge na zařízení, pro které jste vygenerovali certifikát identity a klíč identity. Nakonfigurujete runtime IoT Edge pro automatické, ne ruční zřizování.

Podrobnější informace o instalaci IoT Edge do Windows, včetně předpokladů a pokynů pro úlohy, jako je správa kontejnerů a aktualizace IoT Edge, najdete [v tématu Instalace runtime Azure IoT Edge v systému Windows](how-to-install-iot-edge-windows.md).

1. Otevřete okno PowerShellu v režimu správce. Nezapomeňte použít relaci AMD64 powershellu při instalaci IoT Edge, ne PowerShell (x86).

1. Příkaz **Deploy-IoTEdge** zkontroluje, zda je váš počítač se systémem Windows v podporované verzi, zapne funkci kontejnerů a poté stáhne modul runtime moby a modul runtime IoT Edge. Příkaz je výchozí pro použití kontejnerů systému Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. V tomto okamžiku se zařízení IoT Core mohou automaticky restartovat. K restartování vás mohou vyzvat jiná zařízení s Windows 10 nebo Windows Server. Pokud ano, restartujte zařízení nyní. Jakmile je vaše zařízení připravené, spusťte PowerShell znovu jako správce.

1. Příkaz **Initialize-IoTEdge** konfiguruje runtime IoT Edge ve vašem počítači. Příkaz výchozí ruční zřizování, `-Dps` pokud používáte příznak použít automatické zřizování.

   Nahraďte zástupné `{scope_id}` `{identity cert path}`hodnoty `{identity key path}` pro , a příslušné hodnoty z instance DPS a cesty k souborům v zařízení. Chcete-li zadat ID registrace, `-RegistrationId {registration_id}` uveďte také, případně nahradit zástupný symbol.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -X509IdentityCertificate {identity cert path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >Soubor config.yaml ukládá váš certifikát a klíčové informace jako identifikátory URI souboru. Příkaz Initialize-IoTEdge však zpracovává tento krok formátování za vás, takže můžete poskytnout absolutní cestu k certifikátu a klíčovým souborům v zařízení.

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Pokud modul runtime úspěšně začal, můžete přejít do služby IoT Hub a začít nasazovat moduly IoT Edge do vašeho zařízení.

Můžete ověřit, že byla použita individuální registrace, kterou jste vytvořili ve službě Device Provisioning Service. Přejděte na instanci služby zřizování zařízení na webu Azure Portal. Otevřete podrobnosti o registraci pro jednotlivé registrace, které jste vytvořili. Všimněte si, že je **přiřazen** stav registrace a id zařízení je uveden.

Pomocí následujících příkazů v zařízení ověřte, zda byl runtime úspěšně nainstalován a spuštěn.

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

## <a name="next-steps"></a>Další kroky

Proces registrace služby Device Provisioning Service umožňuje nastavit ID zařízení a značky dvojčete zařízení současně s zřízením nového zařízení. Tyto hodnoty můžete použít k cílení na jednotlivá zařízení nebo skupiny zařízení pomocí automatické správy zařízení. Zjistěte, jak [nasadit a monitorovat moduly IoT Edge ve velkém měřítku pomocí portálu Azure](how-to-deploy-monitor.md) nebo [pomocí rozhraní příkazového příkazového příkazu Azure](how-to-deploy-monitor-cli.md).
