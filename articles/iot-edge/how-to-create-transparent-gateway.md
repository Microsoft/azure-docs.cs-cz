---
title: Vytvoření transparentní brány zařízení – Azure IoT Edge | Dokumentace Microsoftu
description: Použití zařízení Azure IoT Edge jako transparentní brána, která dokáže zpracovávat informace ze zařízení příjem dat
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5881adb7e2fc0d52cc2037d3d4a9e986b3e29d74
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058381"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Konfigurace zařízení tak, aby fungoval jako transparentní brána IoT Edge

Tento článek obsahuje podrobné pokyny ke konfiguraci zařízení IoT Edge, aby fungoval jako transparentní Brána pro jiná zařízení ke komunikaci s centrem IoT. V tomto článku se termín *brána IoT Edge* odkazuje na zařízení IoT Edge použít jako transparentní brána. Další informace najdete v tématu [zařízení jak IoT Edge může sloužit jako brána](./iot-edge-as-gateway.md).

>[!NOTE]
>Aktuálně:
> * Hraniční zařízení nemůže připojit k brány IoT Edge. 
> * Příjem dat zařízení nemohou použít nahrávání souboru.

Existují tři hlavní kroky k nastavení připojení úspěšné transparentní brány. Tento článek se týká první krok:

1. **Zařízení brány musí mít k zabezpečenému připojení pro příjem dat zařízení, přijímat komunikační sdělení od podřízené zařízení a směrování zpráv do správné cíl.**
2. Příjem dat zařízení musí mít identitu zařízení mohli ověřovat pomocí služby IoT Hub a vědět, ke komunikaci prostřednictvím zařízení brány. Další informace najdete v tématu [ověření podřízené zařízení do služby Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. Příjem dat zařízení potřebuje k bezpečnému připojování k zařízení brány. Další informace najdete v tématu [připojte zařízení za příjem dat k bráně Azure IoT Edge](how-to-connect-downstream-device.md).


Pro zařízení, aby fungoval jako brána je třeba jej bezpečně připojit k jeho podřízený zařízení. Azure IoT Edge umožňuje používat infrastrukturu veřejných klíčů (PKI) nastavení zabezpečeného připojení mezi zařízeními. V tomto případě jsme se umožní příjem dat zařízení pro připojení k zařízení IoT Edge sloužit jako transparentní brána. Zachování přiměřené zabezpečení, by měl podřízené zařízení potvrdit identitu zařízení brány. Tato kontrola identity zařízení brání v připojení k potenciálně škodlivé brány.

Příjem dat zařízení může být jakékoli aplikaci nebo platforma, která má identitu vytvořené pomocí [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) cloudovou službu. V mnoha případech se tyto aplikace používat [zařízení Azure IoT SDK](../iot-hub/iot-hub-devguide-sdks.md). Pro všechny praktické účely může být podřízený zařízení i aplikace běžící na samotném zařízení brány IoT Edge. 

Můžete vytvořit jakékoli infrastrukturu certifikátů, která umožňuje důvěryhodnosti vyžadované pro topologii zařízení brány. V tomto článku se budeme předpokládat stejné nastavení certifikátu, který můžete použít k povolení [zabezpečení certifikační Autority X.509](../iot-hub/iot-hub-x509ca-overview.md) ve službě IoT Hub, která zahrnuje přidružený ke konkrétní Centrum IoT (IoT hubu kořenové certifikační Autority), řadu certifikáty podepsané certifikátu webu X.509 Pomocí této certifikační Autority a certifikační Autority pro zařízení IoT Edge.

![Instalace certifikátu brány](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>Termín "kořenové certifikační Autority" používaný v celém Tento článek odkazuje na veřejný certifikát nejvyšší autority řetězu certifikátů infrastruktury veřejných KLÍČŮ které nutně kořenovém certifikátu syndikovaný certifikační autority. V mnoha případech je ve skutečnosti certifikátem zprostředkující certifikační Autority veřejného. 

Brána uvede jeho IoT Edge zařízení certifikát certifikační Autority na podřízené zařízení během inicializace připojení. Příjem dat zařízení zkontroluje, ujistěte se, že certifikát certifikační Autority zařízení IoT Edge je podepsán certifikát kořenové certifikační Autority. Tento proces povolí příjem dat zařízení potvrďte, že brána pochází z důvěryhodného zdroje.

Následující kroky vás provedou procesem vytváření certifikátů a jejich instalace na správných místech v bráně. Můžete použít libovolný počítač, čímž vygenerujete certifikáty a potom zkopírovat je do zařízení IoT Edge. 

## <a name="prerequisites"></a>Požadavky

Zařízení Azure IoT Edge jako bránu nakonfigurovat. Použijte instalační postup, IoT Edge pro jednu z následujících operačních systémů:
  * [Windows](./how-to-install-iot-edge-windows.md)
  * [Linux x64](./how-to-install-iot-edge-linux.md)
  * [Linux ARM32](./how-to-install-iot-edge-linux-arm.md)

Tento článek odkazuje *název hostitele brány* na několika místech. Název hostitele brány je deklarován v **hostname** parametr config.yaml souboru na zařízení brány IoT Edge. Slouží k vytvoření certifikátů, které v tomto článku a je uvedené v připojovacím řetězci podřízené prostředky. Název hostitele brány musí být možné přeložit na IP adresu pomocí DNS nebo položkou hostitelského souboru.

## <a name="generate-certificates-with-windows"></a>Generování certifikátů s Windows

Postupujte podle kroků v této části se vygenerovat testovací certifikáty na Windows. Můžete použít ke generování certifikátů počítače s Windows a potom zkopírovat je do libovolného zařízení IoT Edge běží na kterýkoliv podporovaný operační systém. 

Certifikáty generované v této části jsou určeny pouze pro účely testování. 

### <a name="install-openssl"></a>Nainstalujte OpenSSL

Nainstalujte OpenSSL pro Windows na počítači, který používáte, čímž vygenerujete certifikáty. Pokud už máte nainstalovaný na zařízení s Windows OpenSSL, můžete tento krok přeskočit, ale ujistěte se, že tento openssl.exe je k dispozici ve vaší proměnné prostředí PATH. 

Můžete nainstalovat OpenSSL, několika způsoby:

* **Jednodušší:** Stáhněte a nainstalujte všechny [binárních souborů třetí strany OpenSSL](https://wiki.openssl.org/index.php/Binaries), například z [OpenSSL na SourceForge](https://sourceforge.net/projects/openssl/). Přidejte úplná cesta k openssl.exe do proměnné prostředí PATH. 
   
* **Doporučené:** Stáhněte si zdrojový kód OpenSSL a sestavit binární soubory na svém počítači sami nebo prostřednictvím [vcpkg](https://github.com/Microsoft/vcpkg). Níže uvedené pokyny používají vcpkg stáhnout zdrojový kód, kompilace a nainstalujte OpenSSL na svůj počítač s Windows pomocí jednoduchých kroků.

   1. Přejděte do adresáře, kam chcete nainstalovat vcpkg. Budeme odkazovat na tento adresář jako  *\<VCPKGDIR >* . Postupujte podle pokynů ke stažení a instalaci [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. Jednou vcpkg nainstalovaný, spuštěním následujícího příkazu z příkazového řádku powershellu nainstalujte OpenSSL balíček pro Windows x64. Instalace obvykle trvá přibližně 5 minut na dokončení.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. Přidat `<VCPKGDIR>\installed\x64-windows\tools\openssl` do proměnné prostředí PATH, aby je k dispozici pro vyvolání openssl.exe soubor.

### <a name="prepare-creation-scripts"></a>Příprava skriptů pro vytvoření

Azure IoT Edge úložiště git obsahuje skripty, které můžete použít ke generování certifikátů testu. V této části klonovat úložiště hraničních zařízeních IoT a spouštět skripty. 

1. Otevřete okno Powershellu v režimu správce. 

2. Naklonujte úložiště git, který obsahuje skripty pro generování certifikátů nevýrobní prostředí. Tyto skripty vám pomůžou vytvořit potřebné certifikáty pro nastavení transparentní brány. Použití `git clone` příkazu nebo [stáhnout ZIP](https://github.com/Azure/iotedge/archive/master.zip). 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Přejděte do adresáře, ve kterém chcete pracovat. V tomto článku, zavoláme vám tento adresář  *\<WRKDIR >* . Všechny certifikáty a klíče se vytvoří v tomto pracovním adresáři.

4. Zkopírujte soubory konfigurace a skript z naklonovaného úložiště do vašeho pracovního adresáře. 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Pokud jste si stáhli úložiště jako ZIP, pak je název složky `iotedge-master` a zbývající část cesty je stejný. 
<!--
5. Set environment variable OPENSSL_CONF to use the openssl_root_ca.cnf configuration file.

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```
-->
5. Povolte spouštění skriptů prostředí PowerShell.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. Používání funkce, které skripty používají do prostředí PowerShell pro globální obor názvů.
   
   ```powershell
   . .\ca-certs.ps1
   ```

   V okně PowerShell se zobrazí upozornění, že certifikáty generované tímto skriptem jsou určeny pouze pro účely testování a neměli byste používat v produkčních scénářích.

8. Ověřte, jestli je správně nainstalovaná OpenSSL a ujistěte se, že nebudou kolize názvů s využitím existujících certifikátů. Pokud dochází k problémům, by měl skript popisují, jak je opravit ve vašem systému.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Vytvoření certifikátů

V této části vytvoříte tři certifikáty a připojit je v řetězu. Uvedení certifikáty v řetězu souboru umožňuje snadno nainstalovat na všechny podřízené zařízeních a vaše zařízení brány IoT Edge.  

1. Vytvořte certifikát kořenové certifikační Autority a jeho podepsat jeden zprostředkující certifikát. Certifikáty jsou umístěny ve svém pracovním adresáři.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Tento příkaz skriptu vytvoří několik souborů klíčů a certifikátů, ale My budeme odkazovat na jeden zejména dále v tomto článku:
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Vytvoření IoT Edge zařízení certifikát certifikační Autority a privátního klíče pomocí následujícího příkazu. Zadejte název hostitele brány, který se nachází v souboru iotedge\config.yaml na zařízení brány. Název hostitele brány se používá k pojmenování souborů a během generování certifikátů. 

   ```powershell
   New-CACertsEdgeDevice "<gateway hostname>"
   ```

   Tento příkaz skriptu se vytvoří několik certifikátů a soubory klíčů, včetně dvou, My budeme odkazovat na dále v tomto článku:
   * `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`

Teď, když máte certifikáty, přeskočte k části [nainstalovat certifikáty na bráně](#install-certificates-on-the-gateway)

## <a name="generate-certificates-with-linux"></a>Generování certifikátů s Linuxem

Postupujte podle kroků v této části se vygenerovat testovací certifikáty v Linuxu. Můžete použít počítač s Linuxem ke generování certifikátů a pak zkopírovat je do libovolného zařízení IoT Edge běží na kterýkoliv podporovaný operační systém. 

Certifikáty generované v této části jsou určeny pouze pro účely testování. 

### <a name="prepare-creation-scripts"></a>Příprava skriptů pro vytvoření

Azure IoT Edge úložiště git obsahuje skripty, které můžete použít ke generování certifikátů testu. V této části klonovat úložiště hraničních zařízeních IoT a spouštět skripty. 

1. Naklonujte úložiště git, který obsahuje skripty pro generování certifikátů nevýrobní prostředí. Tyto skripty vám pomůžou vytvořit potřebné certifikáty pro nastavení transparentní brány. 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Přejděte do adresáře, ve kterém chcete pracovat. Budeme odkazovat na tento adresář v celém článku jako  *\<WRKDIR >* . Všechny soubory certifikátu a klíče se vytvoří v tomto adresáři.
  
3. Zkopírujte soubory config a skript z naklonovaného úložiště IoT Edge do vašeho pracovního adresáře.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

<!--
4. Configure OpenSSL to generate certificates using the provided script. 

   ```bash
   chmod 700 certGen.sh 
   ```
-->

### <a name="create-certificates"></a>Vytvoření certifikátů

V této části vytvoříte tři certifikáty a připojit je v řetězu. Uvedení certifikáty v řetězu souboru umožňuje snadno je instalovat na vaše zařízení brány IoT Edge a všechny podřízené zařízení.  

1. Vytvořte kořenový certifikát CA a zprostředkujících certifikátů. Tyto certifikáty jsou umístěny v  *\<WRKDIR >* .

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Tento skript vytvoří několik certifikátů a klíčů. Poznamenejte si jeho jednom, které nám budete odkazovat v následující části:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. Vytvoření IoT Edge zařízení certifikát certifikační Autority a privátního klíče pomocí následujícího příkazu. Zadejte název hostitele brány, který se nachází v souboru iotedge/config.yaml na zařízení brány. Název hostitele brány se používá k pojmenování souborů a během generování certifikátů. 

   ```bash
   ./certGen.sh create_edge_device_certificate "<gateway hostname>"
   ```

   Tento skript vytvoří několik certifikátů a klíčů. Poznamenejte si dvě, což nám budete odkazovat v následující části: 
   * `<WRKDIR>/certs/iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<gateway hostname>.key.pem`

## <a name="install-certificates-on-the-gateway"></a>Instalace certifikátů na bráně

Teď, když jste provedli řetěz certifikátů, budete muset nainstalovat na zařízení brány IoT Edge a nakonfigurovat modul runtime IoT Edge tak, aby odkazovaly na nové certifikáty. 

1. Zkopírujte následující soubory z  *\<WRKDIR >* . Uložte kamkoli na vašem zařízení IoT Edge. Budeme odkazovat do cílového adresáře na zařízení IoT Edge jako  *\<CERTDIR >* . 

   * Certifikát certifikační Autority zařízení –  `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * Privátní klíč certifikační Autority zařízení – `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`
   * Kořenové certifikační Autority – `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   Je možné použít službu jako je [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) nebo funkce, jako je [kopírování zabezpečený protokol](https://www.ssh.com/ssh/scp/) přesunout soubory certifikátu.  Pokud jste vygenerovali certifikáty na samotném zařízení IoT Edge, můžete tento krok přeskočit a použít cestu k pracovnímu adresáři.

2. Otevřete konfigurační soubor démon zabezpečení IoT Edge. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Nastavte **certifikát** vlastností v souboru config.yaml úplnou cestu k souboru certifikátu a klíče na zařízení IoT Edge. Odeberte `#` znak před vlastnosti certifikátu pro zrušte komentář u uvedených čtyř řádků. Mějte na paměti, že odsazení v yaml byly mezery dvě.

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\iot-edge-device-<gateway hostname>-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\iot-edge-device-<gateway hostname>.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux: 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/iot-edge-device-<gateway hostname>-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/iot-edge-device-<gateway hostname>.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. Na zařízeních s Linuxem, ujistěte se, že uživatel **iotedge** má oprávnění pro adresář uchovávající certifikáty ke čtení. 

## <a name="deploy-edgehub-to-the-gateway"></a>Nasazení EdgeHub do brány

Při první instalaci IoT Edge na zařízení, modul jenom jeden systému automaticky spustí: agenta IoT Edge. Pro vaše zařízení k práci jako brána budete potřebovat oba moduly systému. Pokud jste nenasadili všechny moduly, které pro vaše zařízení brány před, vytvoření počáteční nasazení pro vaše zařízení spusťte druhý systému modul IoT Edge hub. Nasazení bude vypadat prázdný, protože všechny moduly, které nepřidáte v průvodci, ale to budete mít jistotu, že oba moduly systému běží. 

Můžete zkontrolovat, které moduly jsou spuštěny na zařízení pomocí příkazu `iotedge list`. Pokud seznamu vrátí jenom modul **edgeAgent** bez **edgeHub**, použijte následující postup:

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

## <a name="open-ports-on-gateway-device"></a>Otevření portů na zařízení brány

Standardní zařízení IoT Edge nepotřebují příchozí připojení na funkci, protože veškerá komunikace s IoT Hub se provádí prostřednictvím odchozího připojení. Brána zařízení se liší, protože potřebují pro příjem zpráv z jejich podřízených zařízení. Pokud brána firewall je mezi zařízeními příjem dat a zařízení brány, komunikace musí být možné provádět prostřednictvím brány firewall a.

Pro scénáře brány pro práci musí být otevřené pro příchozí provoz z podřízené zařízení alespoň jeden z podporovaných protokolů Centrum IoT Edge. Podporované protokoly jsou protokol MQTT, AMQP a protokolu HTTPS. 

| Port | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>Směrování zpráv ze zařízení příjem dat
Modul runtime IoT Edge může směrovat zprávy odeslané ze zařízení příjem dat, stejně jako zprávy odeslané moduly. Tato funkce umožňuje provádět analýzy v modulu na bráně spuštěna před odesláním všechna data do cloudu. 

V současné době je tak, jak směrovat zprávy odesílané zařízeními podřízené rozlišení na zprávy odeslané moduly. Zprávy odesílané všechny moduly obsahují systém vlastnost s názvem **connectionModuleId** ale zprávy odesílané zařízeními, podřízené, tomu tak není. Klauzule WHERE trasy můžete vyloučit všechny zprávy, které obsahují tuto vlastnost systému. 

Níže trasy je příklad, který bude posílat zprávy z libovolného zařízení podřízené modul s názvem `ai_insights`a potom z `ai_insights` do služby IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Další informace o směrování zpráv, najdete v části [nasadit moduly a vytvářet](./module-composition.md#declare-routes).


## <a name="enable-extended-offline-operation"></a>Povolit rozšířené operace vypnutí

Počínaje [vydání verzi v1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) modulu runtime IoT Edge na zařízení brány a podřízené zařízení připojující se k němu lze nakonfigurovat pro rozšířené operace vypnutí. 

Díky této funkci můžete lokální moduly nebo příjem dat zařízení opakované ověření u zařízení IoT Edge, podle potřeby a komunikovat mezi sebou pomocí zprávy a metody i po odpojení ze služby IoT hub. Další informace najdete v tématu [porozumění rozšířené offline možnosti pro IoT Edge, zařízení, moduly a podřízená zařízení](offline-capabilities.md).

Pokud chcete povolit rozšířené možnosti offline, je vytvořit vztah nadřazený podřízený mezi zařízení brány IoT Edge a podřízené zařízení, která se připojí k němu. Tyto kroky jsou vysvětlené podrobněji [ověření podřízené zařízení do služby Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Další postup

Teď, když máte zařízení IoT Edge funguje jako transparentní brána, musíte pro příjem dat zařízení důvěřovat brány a odesílat zprávy do něj. Další informace najdete v tématu [připojte zařízení za příjem dat k bráně Azure IoT Edge](how-to-connect-downstream-device.md) a [ověření podřízené zařízení do služby Azure IoT Hub](how-to-authenticate-downstream-device.md).
