---
title: Vytvoření transparentní brány zařízení – Azure IoT Edge | Dokumentace Microsoftu
description: Použití zařízení Azure IoT Edge jako transparentní brána, která dokáže zpracovávat informace ze zařízení příjem dat
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 29c7fc279aec79750df48c70be7792869e89ae78
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53094351"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Konfigurace zařízení tak, aby fungoval jako transparentní brána IoT Edge

Tento článek obsahuje podrobné pokyny ke konfiguraci zařízení IoT Edge, aby fungoval jako transparentní Brána pro jiná zařízení ke komunikaci s centrem IoT. V tomto článku se termín *brána IoT Edge* odkazuje na zařízení IoT Edge použít jako transparentní brána. Další informace najdete v tématu [zařízení jak IoT Edge může sloužit jako brána](./iot-edge-as-gateway.md), která poskytuje koncepční přehled.

>[!NOTE]
>Aktuálně:
> * Pokud brána není připojený k ze služby IoT Hub, se nemůže ověřit podřízené zařízení s bránou.
> * Hraniční zařízení nemůže připojit k brány IoT Edge. 
> * Příjem dat zařízení nemohou použít nahrávání souboru.

Pro zařízení, aby fungoval jako brána je třeba jej bezpečně připojit k podřízené zařízení. Azure IoT Edge umožňuje používat infrastrukturu veřejných klíčů (PKI) nastavení zabezpečeného připojení mezi zařízeními. V tomto případě jsme se umožní příjem dat zařízení pro připojení k zařízení IoT Edge sloužit jako transparentní brána. Zachování přiměřené zabezpečení, by měl podřízené zařízení potvrdit identitu hraniční zařízení, protože pouze chcete, aby vaše zařízení připojující se k vaší brány a potenciálně škodlivých bránu.

Příjem dat zařízení může být jakékoli aplikaci nebo platforma, která má identitu vytvořené pomocí [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) cloudovou službu. V mnoha případech se tyto aplikace používat [zařízení Azure IoT SDK](../iot-hub/iot-hub-devguide-sdks.md). Pro všechny praktické účely může být podřízený zařízení i aplikace běžící na samotném zařízení brány IoT Edge. 

Můžete vytvořit jakékoli infrastrukturu certifikátů, která umožňuje důvěryhodnosti vyžadované pro topologii zařízení brány. V tomto článku se budeme předpokládat stejné nastavení certifikátu, který můžete použít k povolení [zabezpečení certifikační Autority X.509](../iot-hub/iot-hub-x509ca-overview.md) ve službě IoT Hub, který zahrnuje certifikátu webu X.509 přidružený ke konkrétní IoT hub (IoT hub vlastník certifikační Autority) a řadou certifikáty, Hraniční zařízení zaregistrovali pomocí této certifikační Autority a certifikační Autority.

![Instalace certifikátu brány](./media/how-to-create-transparent-gateway/gateway-setup.png)

Brána svůj certifikát hraniční zařízení certifikační Autority podřízené zařízení během inicializace připojení. Příjem dat zařízení zkontroluje, ujistěte se, že hraniční zařízení certifikační Autority certifikát je podepsaný certifikátem pro certifikační Autority vlastníka. Tento proces povolí příjem dat zařízení potvrďte, že brána pochází z důvěryhodného zdroje.

Následující kroky vás provedou procesem vytváření certifikátů a jejich instalace na správných místech.

## <a name="prerequisites"></a>Požadavky

Zařízení Azure IoT Edge jako bránu nakonfigurovat. Vývojovém počítači nebo virtuální počítač můžete použít jako zařízení IoT Edge s kroky pro následující operační systémy:
* [Windows](./how-to-install-iot-edge-windows-with-windows.md)
* [Linux x64](./how-to-install-iot-edge-linux.md)
* [Linux ARM32](./how-to-install-iot-edge-linux-arm.md)

Můžete použít libovolný počítač, čímž vygenerujete certifikáty a potom zkopírovat je do zařízení IoT Edge. 

## <a name="generate-certificates-with-windows"></a>Generování certifikátů s Windows

Postupujte podle kroků v této části pro generování certifikátů testů na zařízení s Windows. Můžete vygenerovat certifikáty na zařízení IoT Edge, nebo použít samostatný počítač a zkopírovat konečný certifikáty do libovolného zařízení IoT Edge spuštěn kterýkoliv podporovaný operační systém. 

Certifikáty generované v této části jsou určeny pouze pro účely testování. 

### <a name="install-openssl"></a>Nainstalujte OpenSSL

Nainstalujte OpenSSL pro Windows na počítači, který používáte, čímž vygenerujete certifikáty. Můžete nainstalovat OpenSSL, několika způsoby:

   >[!NOTE]
   >Pokud už máte nainstalovaný na zařízení s Windows OpenSSL, můžete tento krok přeskočte, ale ujistěte se, že tento openssl.exe je k dispozici ve vaší proměnné prostředí PATH.

* **Jednodušší:** stáhněte a nainstalujte všechny [binárních souborů třetí strany OpenSSL](https://wiki.openssl.org/index.php/Binaries), například z [tento projekt na SourceForge](https://sourceforge.net/projects/openssl/). Přidejte úplná cesta k openssl.exe do proměnné prostředí PATH. 
   
* **Doporučeno:** stáhnout zdrojový kód OpenSSL a sestavit binární soubory na svém počítači sami nebo prostřednictvím [vcpkg](https://github.com/Microsoft/vcpkg). Níže uvedené pokyny používají vcpkg stáhnout zdrojový kód, kompilace a nainstalujte OpenSSL na svůj počítač s Windows pomocí jednoduchých kroků.

   1. Přejděte do adresáře, kam chcete nainstalovat vcpkg. Budeme odkazovat na tento adresář jako  *\<VCPKGDIR >*. Postupujte podle pokynů ke stažení a instalaci [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. Po instalaci se vcpkg z příkazového řádku powershellu, spusťte následující příkaz k instalaci balíčku OpenSSL pro Windows x64. Instalace obvykle trvá přibližně 5 minut na dokončení.

      ```PowerShell
      .\vcpkg install openssl:x64-windows
      ```
   3. Přidat `<VCPKGDIR>\installed\x64-windows\tools\openssl` do proměnné prostředí PATH, aby je k dispozici pro vyvolání openssl.exe soubor.

### <a name="prepare-creation-scripts"></a>Příprava skriptů pro vytvoření

Zařízení Azure IoT SDK pro jazyk C obsahuje skripty, které můžete použít ke generování certifikátů testu. V této části klonovat sadu SDK a konfigurace prostředí PowerShell.

1. Otevřete okno Powershellu v režimu správce. 

2. Naklonujte úložiště git, který obsahuje skripty pro generování certifikátů nevýrobní prostředí. Tyto skripty vám pomůžou vytvořit potřebné certifikáty pro nastavení transparentní brány. Použití `git clone` příkazu nebo [stáhnout ZIP](https://github.com/Azure/azure-iot-sdk-c/archive/master.zip). 

   ```PowerShell
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

3. Přejděte do adresáře, ve kterém chcete pracovat. Budeme odkazovat na tento adresář jako  *\<WRKDIR >*.  Vytvoří se všechny soubory v tomto adresáři.

4. Zkopírujte soubory konfigurace a skript do pracovního adresáře. 

   ```PowerShell
   copy <path>\azure-iot-sdk-c\tools\CACertificates\*.cnf .
   copy <path>\azure-iot-sdk-c\tools\CACertificates\ca-certs.ps1 .
   ```

   Pokud jste si stáhli sadu SDK jako ZIP, pak je název složky `azure-iot-sdk-c-master` a zbývající část cesty je stejný. 

5. Nastavte proměnnou prostředí OPENSSL_CONF použijte openssl_root_ca.cnf konfigurační soubor.

    ```PowerShell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```

6. Povolte spouštění skriptů prostředí PowerShell.

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. Používání funkce, které využívají skripty, prostředí PowerShell pro globální obor názvů.
   
   ```PowerShell
   . .\ca-certs.ps1
   ```

8. Ověřte, jestli je správně nainstalovaná OpenSSL a ujistěte se, že nebudou kolize názvů s využitím existujících certifikátů. Pokud dochází k problémům, by měl skript popisují, jak je opravit ve vašem systému.

   ```PowerShell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Vytvoření certifikátů

V této části vytvoříte tři certifikáty a připojit je v řetězu. Uvedení certifikáty v řetězu souboru umožňuje snadno nainstalovat na všechny podřízené zařízeních a vaše zařízení brány IoT Edge.  

1. Vytvoření certifikátu vlastníka certifikační Autority a jeho podepsat jeden zprostředkující certifikát. Certifikáty jsou umístěny v  *\<WRKDIR >*.

      ```PowerShell
      New-CACertsCertChain rsa
      ```

2. Pomocí následujícího příkazu vytvořte certifikát hraniční zařízení certifikační Autority a privátního klíče. Zadejte název pro zařízení brány, který se použije pro pojmenování souborů a během generování certifikátů. 

   ```PowerShell
   New-CACertsEdgeDevice "<gateway name>"
   ```

3. Z certifikátu certifikační Autority vlastníka, zprostředkující certifikát a certifikát certifikační Autority hraničních zařízení pomocí následujícího příkazu vytvořte řetěz certifikátů. 

   ```PowerShell
   Write-CACertsCertificatesForEdgeDevice "<gateway name>"
   ```

   Tento skript vytvoří následující certifikáty a klíče:
   * `<WRKDIR>\certs\new-edge-device.*`
   * `<WRKDIR>\private\new-edge-device.key.pem`
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

## <a name="generate-certificates-with-linux"></a>Generování certifikátů s Linuxem

Ke generování certifikátů testů na zařízení s Linuxem pomocí kroků v této části. Můžete vygenerovat certifikáty na zařízení IoT Edge, nebo použít samostatný počítač a zkopírovat konečný certifikáty do libovolného zařízení IoT Edge spuštěn kterýkoliv podporovaný operační systém. 

### <a name="prepare-creation-scripts"></a>Příprava skriptů pro vytvoření

1. Naklonujte úložiště git, který obsahuje skripty pro generování certifikátů nevýrobní prostředí. Tyto skripty vám pomůžou vytvořit potřebné certifikáty pro nastavení transparentní brány. 

   ```bash
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

2. Přejděte do adresáře, ve kterém chcete pracovat. Budeme odkazovat na tento adresář jako  *\<WRKDIR >*.  Vytvoří se všechny soubory v tomto adresáři.
  
3. Zkopírujte soubory config a skript do pracovního adresáře.

   ```bash
   cp <path>/azure-iot-sdk-c/tools/CACertificates/*.cnf .
   cp <path>/azure-iot-sdk-c/tools/CACertificates/certGen.sh .
   ```

4. Nakonfigurujte OpenSSL, čímž vygenerujete certifikáty pomocí dodávaného skriptu. 

   ```bash
   chmod 700 certGen.sh 
   ```

### <a name="create-certificates"></a>Vytvoření certifikátů

V této části vytvoříte tři certifikáty a připojit je v řetězu. Uvedení certifikáty v řetězu souboru umožňuje snadno je instalovat na vaše zařízení brány IoT Edge a všechny podřízené zařízení.  

1.  Vytvořte certifikát certifikační Autority vlastníka a jeden zprostředkující certifikát. Tyto certifikáty jsou umístěny v  *\<WRKDIR >*.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Tento skript vytvoří následující certifikáty a klíče:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>/certs/azure-iot-test-only.intermediate.cert.pem`
   * `<WRKDIR>/private/azure-iot-test-only.root.ca.key.pem`
   * `<WRKDIR>/private/azure-iot-test-only.intermediate.key.pem`

2.  Pomocí následujícího příkazu vytvořte certifikát hraniční zařízení certifikační Autority a privátního klíče. Zadejte název pro zařízení brány, který se použije pro pojmenování souborů a během generování certifikátů. 

   ```bash
   ./certGen.sh create_edge_device_certificate "<gateway name>"
   ```

   Tento skript vytvoří následující certifikáty a klíče:
   * `<WRKDIR>/certs/new-edge-device.*`
   * `<WRKDIR>/private/new-edge-device.key.pem`

3. Vytvořit řetěz certifikátů, která je volána **nové edge – zařízení full-chain.cert.pem** od vlastníka certifikační Autority certifikátu, zprostředkující certifikát a certifikát hraniční zařízení certifikační Autority.

   ```bash
   cat ./certs/new-edge-device.cert.pem ./certs/azure-iot-test-only.intermediate.cert.pem ./certs/azure-iot-test-only.root.ca.cert.pem > ./certs/new-edge-device-full-chain.cert.pem
   ```

## <a name="install-certificates-on-the-gateway"></a>Instalace certifikátů na bráně

Teď, když jste provedli řetěz certifikátů, budete muset nainstalovat na zařízení brány IoT Edge a nakonfigurovat modul runtime IoT Edge tak, aby odkazovaly na nové certifikáty. 

1. Zkopírujte následující soubory z  *\<WRKDIR >*. Uložte kamkoli na vašem zařízení IoT Edge. Budeme odkazovat do cílového adresáře na zařízení IoT Edge jako  *\<CERTDIR >*. 

   Pokud jste vygenerovali certifikáty na samotném zařízení Edge, můžete tento krok přeskočit a použít cestu k pracovnímu adresáři.

   * Certifikát certifikační Autority zařízení –  `<WRKDIR>\certs\new-edge-device-full-chain.cert.pem`
   * Privátní klíč certifikační Autority zařízení – `<WRKDIR>\private\new-edge-device.key.pem`
   * Vlastník CA- `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Otevřete konfigurační soubor démon zabezpečení IoT Edge. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Nastavte **certifikát** vlastností v souboru config.yaml k cestě, kterého jste umístili certifikát a klíč souborů na zařízení IoT Edge.

```yaml
certificates:
  device_ca_cert: "<CERTDIR>\\certs\\new-edge-device-full-chain.cert.pem"
  device_ca_pk: "<CERTDIR>\\private\\new-edge-device.key.pem"
  trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
```

## <a name="deploy-edgehub-to-the-gateway"></a>Nasazení EdgeHub do brány

Při první instalaci IoT Edge na zařízení, modul jenom jeden systému automaticky spustí: Edge agent. Pro vaše zařízení k práci jako brána budete potřebovat oba moduly systému. Pokud jste nenasadili všechny moduly, které pro vaše zařízení brány před, vytvořte nasazení pro vaše zařízení spusťte druhý modul systému Centrum Edge. Nasazení bude vypadat prázdný, protože všechny moduly, které nepřidáte v průvodci, ale oba moduly systému se nasadí. 

Můžete zkontrolovat, které moduly jsou spuštěny na zařízení pomocí příkazu `iotedge list`.

1. Na webu Azure Portal přejděte do svého centra IoT.

2. Přejděte na **IoT Edge** a vyberte zařízení IoT Edge, který chcete použít jako bránu.

3. Vyberte **Nastavit moduly**.

4. Vyberte **Další**.

5. V **trasy zadejte** stránky, měli byste mít výchozí trasu, která odešle všechny zprávy ze všech modulů pro službu IoT Hub. Pokud tomu tak není, přidejte následující kód a vyberte **Next** (Další).

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. V **šablona kontrolní** stránce **odeslat**.

## <a name="route-messages-from-downstream-devices"></a>Směrování zpráv ze zařízení příjem dat
Modul runtime IoT Edge může směrovat zprávy odeslané ze zařízení příjem dat, stejně jako zprávy odeslané moduly. To umožňuje provádět analýzy v modulu na bráně spuštěna před odesláním všechna data do cloudu. 

V současné době je tak, jak směrovat zprávy odesílané zařízeními podřízené rozlišení na zprávy odeslané moduly. Zprávy odesílané všechny moduly obsahují systém vlastnost s názvem **connectionModuleId** ale zprávy odesílané zařízeními, podřízené, tomu tak není. Klauzule WHERE trasy můžete vyloučit všechny zprávy, které obsahují tuto vlastnost systému. 

Níže trasy se použije pro odesílání zpráv z libovolného zařízení pro příjem dat s názvem modulu `ai_insights`.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Další informace o směrování zpráv, najdete v části [nasadit moduly a vytvářet](./module-composition.md#declare-routes).

[!INCLUDE [iot-edge-extended-ofline-preview](../../includes/iot-edge-extended-offline-preview.md)]

## <a name="next-steps"></a>Další postup

Teď, když máte zařízení IoT Edge funguje jako transparentní brána, musíte pro příjem dat zařízení důvěřovat brány a odesílat zprávy do něj. Další informace najdete v tématu [připojte zařízení za příjem dat k bráně Azure IoT Edge](how-to-connect-downstream-device.md).
