---
title: Vytvoření transparentní brány zařízení – Azure IoT Edge | Dokumentace Microsoftu
description: Použití zařízení Azure IoT Edge jako transparentní brána, která dokáže zpracovávat informace ze zařízení příjem dat
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d0ac7fa3a1dbb1c91da5b9919bc2c62de74213b5
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456785"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Konfigurace zařízení tak, aby fungoval jako transparentní brána IoT Edge

Tento článek poskytuje podrobné pokyny ke konfiguraci IoT Edge zařízení pro fungování jako transparentní brány pro jiná zařízení, která budou komunikovat s IoT Hub. V tomto článku se pojem *IoT Edge brána* vztahuje na IoT Edge zařízení, které se používá jako transparentní brána. Další informace najdete v tématu [jak se dá zařízení IoT Edge použít jako brána](./iot-edge-as-gateway.md).

>[!NOTE]
>Aktuálně:
> * Hraniční zařízení nemůže připojit k brány IoT Edge. 
> * Příjem dat zařízení nemohou použít nahrávání souboru.

Existují tři obecné kroky k nastavení úspěšného transparentního připojení brány. Tento článek popisuje první krok:

1. **Zařízení brány musí být schopné bezpečně připojit se k zařízením pro příjem dat, přijímat komunikaci ze zařízení pro příjem dat a směrovat zprávy do správného umístění.**
2. Aby se zařízení mohla ověřit pomocí IoT Hub, musí mít k dispozici identitu zařízení, která může komunikovat prostřednictvím zařízení brány. Další informace najdete v tématu [ověření zařízení pro příjem dat do Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. Zařízení pro příjem dat musí být schopné se bezpečně připojit k zařízení brány. Další informace najdete v tématu [připojení zařízení pro příjem dat k bráně Azure IoT Edge](how-to-connect-downstream-device.md).


Aby zařízení fungovalo jako brána, musí být schopné bezpečně se připojit ke svým zařízením pro příjem dat. Azure IoT Edge umožňuje používat infrastrukturu veřejných klíčů (PKI) nastavení zabezpečeného připojení mezi zařízeními. V tomto případě jsme se umožní příjem dat zařízení pro připojení k zařízení IoT Edge sloužit jako transparentní brána. Aby bylo možné zajistit přiměřené zabezpečení, musí zařízení pro příjem dat potvrdit identitu zařízení brány. Tato kontrolu identity zabraňuje zařízením v připojení k potenciálně škodlivým branám.

Pro podřízené zařízení v transparentní bráně může být libovolná aplikace nebo platforma, která má identitu vytvořenou pomocí cloudové služby [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) . V mnoha případech tyto aplikace používají [sadu SDK pro zařízení Azure IoT](../iot-hub/iot-hub-devguide-sdks.md). Pro všechny praktické účely může být podřízený zařízení i aplikace běžící na samotném zařízení brány IoT Edge. IoT Edge zařízení ale nemůže být podřízená bráně IoT Edge. 

Můžete vytvořit jakékoli infrastrukturu certifikátů, která umožňuje důvěryhodnosti vyžadované pro topologii zařízení brány. V tomto článku se předpokládáme, že použijete stejné nastavení certifikátu, které byste použili k povolení [zabezpečení CA x. 509](../iot-hub/iot-hub-x509ca-overview.md) v IoT Hub, což zahrnuje certifikát CA x. 509, který je přidružený ke konkrétnímu centru IoT (KOŘENová CA služby IoT Hub), sérii certifikátů podepsaných touto certifikační autoritou a certifikační autoritou pro IoT Edge zařízení.

![Instalace certifikátu brány](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>Pojem "Kořenová certifikační autorita", který se používá v celém tomto článku, odkazuje na veřejný certifikát certifikační autority certifikátu PKI, a ne nutně na kořen certifikátu pro syndikátní certifikační autoritu. V mnoha případech je ve skutečnosti veřejný certifikát zprostředkující certifikační autority. 

Brána při zahájení připojení prezentuje své IoT Edge certifikát CA zařízení. Zařízení pro příjem dat zkontroluje, že certifikát CA IoT Edge zařízení je podepsaný certifikátem kořenové certifikační autority. V rámci tohoto procesu může zařízení pro příjem dat potvrdit, že brána pochází z důvěryhodného zdroje.

Následující kroky vás provedou procesem vytvoření certifikátů a jejich instalací do správných míst v bráně. Můžete použít libovolný počítač, čímž vygenerujete certifikáty a potom zkopírovat je do zařízení IoT Edge. 

## <a name="prerequisites"></a>Požadavky

* Vývojový počítač pro vytváření certifikátů. 
* Zařízení Azure IoT Edge jako bránu nakonfigurovat. Použijte postup instalace IoT Edge pro jeden z následujících operačních systémů:
  * [Windows](how-to-install-iot-edge-windows.md)
  * [Linux](how-to-install-iot-edge-linux.md)

## <a name="generate-certificates-with-windows"></a>Generování certifikátů s Windows

Pomocí kroků v této části můžete vygenerovat testovací certifikáty ve Windows. K vygenerování certifikátů můžete použít počítač s Windows a pak je zkopírovat do libovolného IoT Edge zařízení, které běží na jakémkoli podporovaném operačním systému. 

Certifikáty generované v této části jsou určeny pouze pro účely testování. 

### <a name="install-openssl"></a>Nainstalujte OpenSSL

Nainstalujte OpenSSL pro Windows na počítači, který používáte, čímž vygenerujete certifikáty. Pokud jste již na zařízení s Windows nainstalovali OpenSSL, můžete tento krok přeskočit, ale zajistěte, aby byl v proměnné prostředí PATH k dispozici OpenSSL. exe. 

Existuje několik způsobů, jak nainstalovat OpenSSL, včetně:

* **Jednodušší:** Stáhněte si a nainstalujte všechny [binární soubory OpenSSL třetích stran](https://wiki.openssl.org/index.php/Binaries), například z [OpenSSL v sourceforge](https://sourceforge.net/projects/openssl/). Přidejte úplná cesta k openssl.exe do proměnné prostředí PATH. 
   
* **Doporučené:** Stáhněte si zdrojový kód OpenSSL a sestavte binární soubory na svém počítači sami nebo prostřednictvím [vcpkg](https://github.com/Microsoft/vcpkg). Níže uvedené pokyny používají vcpkg stáhnout zdrojový kód, kompilace a nainstalujte OpenSSL na svůj počítač s Windows pomocí jednoduchých kroků.

   1. Přejděte do adresáře, kam chcete nainstalovat vcpkg. Do tohoto adresáře odkazujeme jako *\<VCPKGDIR >* . Podle pokynů stáhněte a nainstalujte [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. Po instalaci vcpkg spusťte následující příkaz z příkazového řádku PowerShellu a nainstalujte balíček OpenSSL pro Windows x64. Instalace obvykle trvá přibližně 5 minut na dokončení.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. Přidejte `<VCPKGDIR>\installed\x64-windows\tools\openssl` do proměnné prostředí PATH, aby byl k dispozici soubor OpenSSL. exe pro vyvolání.

### <a name="prepare-creation-scripts"></a>Příprava skriptů pro vytvoření

Azure IoT Edge úložiště Git obsahuje skripty, které můžete použít k vygenerování testovacích certifikátů. V této části naklonujte úložiště IoT Edge a spustíte skripty. 

1. Otevřete okno Powershellu v režimu správce. 

2. Naklonujte úložiště git, který obsahuje skripty pro generování certifikátů nevýrobní prostředí. Tyto skripty vám pomůžou vytvořit potřebné certifikáty pro nastavení transparentní brány. Použijte příkaz `git clone` nebo [Stáhněte soubor zip](https://github.com/Azure/iotedge/archive/master.zip). 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Přejděte do adresáře, ve kterém chcete pracovat. V celém tomto článku budeme volat tento adresář *\<WRKDIR >* . V tomto pracovním adresáři se vytvoří všechny certifikáty a klíče.

4. Zkopírujte konfigurační soubory a soubory skriptu z klonovaného úložiště do svého pracovního adresáře. 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Pokud jste úložiště stáhli jako soubor ZIP, název složky je `iotedge-master` a zbytek cesty je stejný. 
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

7. Přeneste funkce používané skripty do globálního oboru názvů prostředí PowerShell.
   
   ```powershell
   . .\ca-certs.ps1
   ```

   V okně PowerShellu se zobrazí upozornění, že certifikáty generované tímto skriptem jsou jenom pro účely testování a neměly by se používat v produkčních scénářích.

8. Ověřte, že je OpenSSL správně nainstalovaný, a ujistěte se, že se nejedná o kolizi názvů se stávajícími certifikáty. Pokud dochází k problémům, by měl skript popisují, jak je opravit ve vašem systému.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Vytvoření certifikátů

V této části vytvoříte tři certifikáty a připojit je v řetězu. Uvedení certifikáty v řetězu souboru umožňuje snadno nainstalovat na všechny podřízené zařízeních a vaše zařízení brány IoT Edge.  

1. Vytvořte certifikát kořenové certifikační autority a podepište ho jako jeden zprostředkující certifikát. Certifikáty jsou umístěné v pracovním adresáři.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Tento příkaz skriptu vytvoří několik souborů certifikátů a klíčů, ale budeme na něj odkazovat ještě dále v tomto článku:
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Pomocí následujícího příkazu vytvořte certifikát certifikační autority IoT Edge zařízení a privátní klíč. Zadejte název certifikátu certifikační autority, například **MyEdgeDeviceCA**. Název slouží k pojmenování souborů a během generování certifikátu. 

   ```powershell
   New-CACertsEdgeDeviceCA "MyEdgeDeviceCA"
   ```

   Tento příkaz skriptu vytvoří několik souborů certifikátů a klíčů, včetně dvou informací, které budeme odkazovat na později v tomto článku:
   * `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >Pokud zadáte jiný název než **MyEdgeDeviceCA**, pak certifikáty a klíče vytvořené tímto příkazem budou tento název zobrazovat. 

Teď, když máte certifikáty, přeskočte k [instalaci certifikátů v bráně](#install-certificates-on-the-gateway)

## <a name="generate-certificates-with-linux"></a>Generování certifikátů s Linuxem

Pomocí kroků v této části můžete vygenerovat testovací certifikáty v systému Linux. K vygenerování certifikátů můžete použít počítač se systémem Linux a pak je zkopírovat do libovolného IoT Edge zařízení, které běží na jakémkoli podporovaném operačním systému. 

Certifikáty generované v této části jsou určeny pouze pro účely testování. 

### <a name="prepare-creation-scripts"></a>Příprava skriptů pro vytvoření

Azure IoT Edge úložiště Git obsahuje skripty, které můžete použít k vygenerování testovacích certifikátů. V této části naklonujte úložiště IoT Edge a spustíte skripty. 

1. Naklonujte úložiště git, který obsahuje skripty pro generování certifikátů nevýrobní prostředí. Tyto skripty vám pomůžou vytvořit potřebné certifikáty pro nastavení transparentní brány. 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Přejděte do adresáře, ve kterém chcete pracovat. Do tohoto adresáře odkazujeme v celém článku *\<WRKDIR >* . V tomto adresáři se vytvoří všechny soubory certifikátů a klíčů.
  
3. Zkopírujte soubory config a Script z naklonovaného IoT Edge úložiště do svého pracovního adresáře.

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

1. Vytvořte certifikát kořenové certifikační autority a jeden zprostředkující certifikát. Tyto certifikáty jsou umístěné v *\<WRKDIR >* .

   Pokud jste už v tomto pracovním adresáři vytvořili kořenové a zprostředkující certifikáty, tento skript znovu nespouštějte. Po spuštění tohoto skriptu dojde k přepsání stávajících certifikátů. Místo toho přejděte k dalšímu kroku. 

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Skript vytvoří několik certifikátů a klíčů. Poznamenejte si ji, na kterou odkazujeme v další části:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. Pomocí následujícího příkazu vytvořte certifikát certifikační autority IoT Edge zařízení a privátní klíč. Zadejte název certifikátu certifikační autority, například **MyEdgeDeviceCA**. Název slouží k pojmenování souborů a během generování certifikátu. 

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "MyEdgeDeviceCA"
   ```

   Skript vytvoří několik certifikátů a klíčů. Poznamenejte si dva, na které budeme odkazovat v následující části: 
   * `<WRKDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >Pokud zadáte jiný název než **MyEdgeDeviceCA**, pak certifikáty a klíče vytvořené tímto příkazem budou tento název zobrazovat. 

## <a name="install-certificates-on-the-gateway"></a>Instalace certifikátů na bráně

Teď, když jste provedli řetěz certifikátů, budete muset nainstalovat na zařízení brány IoT Edge a nakonfigurovat modul runtime IoT Edge tak, aby odkazovaly na nové certifikáty. 

1. Zkopírujte následující soubory z *\<WRKDIR >* . Uložte kamkoli na vašem zařízení IoT Edge. Jako *>\<CERTDIR*odkazujeme na cílový adresář na vašem zařízení IoT Edge. 

   * Certifikát certifikační autority zařízení – `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * Privátní klíč certifikační autority zařízení – `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`
   * Kořenová certifikační autorita – `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   K přesunutí souborů certifikátů můžete použít službu, jako je [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) , nebo funkci, jako je [protokol Secure Copy](https://www.ssh.com/ssh/scp/) .  Pokud jste certifikáty vygenerovali na samotném IoT Edge zařízení, můžete tento krok přeskočit a použít cestu k pracovnímu adresáři.

2. Otevřete konfigurační soubor démon zabezpečení IoT Edge. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Nastavte vlastnosti **certifikátu** v souboru config. yaml na úplnou cestu k certifikátu a souborům klíčů na zařízení IoT Edge. Odeberte znak `#` před tím, než vlastnosti certifikátu Odkomentujte čtyři řádky. Nezapomeňte, že odsazení v YAML jsou dvě mezery.

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux: 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. V zařízeních se systémem Linux se ujistěte, že uživatel **iotedge** má oprávnění ke čtení pro adresář, který obsahuje certifikáty. 

## <a name="deploy-edgehub-to-the-gateway"></a>Nasazení EdgeHub do brány

Při první instalaci IoT Edge na zařízení se automaticky spustí pouze jeden systémový modul: Agent IoT Edge. Pro vaše zařízení k práci jako brána budete potřebovat oba moduly systému. Pokud jste do zařízení brány ještě nenainstalovali žádné moduly, vytvořte počáteční nasazení pro vaše zařízení a spusťte druhý systémový modul, Centrum IoT Edge. Nasazení bude vypadat prázdné, protože do Průvodce nepřidáte žádné moduly, ale zajistěte, aby byly spuštěny oba systémové moduly. 

Pomocí příkazu `iotedge list`můžete zjistit, které moduly jsou spuštěné na zařízení. Pokud seznam vrátí jenom modul **edgeAgent** bez **edgeHub**, použijte následující postup:

1. Na webu Azure Portal přejděte do svého centra IoT.

2. Přejít na **IoT Edge** a vybrat IoT Edge zařízení, které chcete použít jako bránu.

3. Vyberte **Nastavit moduly**.

4. Vyberte **Next** (Další).

5. Na stránce **zadat trasy** byste měli mít výchozí trasu, která odesílá všechny zprávy ze všech modulů do IoT Hub. Pokud tomu tak není, přidejte následující kód a vyberte **Next** (Další).

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. Na stránce **zkontrolovat šablonu** vyberte **Odeslat**.

## <a name="open-ports-on-gateway-device"></a>Otevřít porty na zařízení brány

Zařízení Standard IoT Edge nepotřebují žádné příchozí připojení, protože veškerá komunikace s IoT Hub se provádí prostřednictvím odchozích připojení. Zařízení brány jsou odlišná, protože potřebují přijímat zprávy ze svých podřízených zařízení. Pokud je brána firewall mezi zařízeními pro příjem dat a zařízením brány, musí být komunikace také možná přes bránu firewall.

Aby mohl scénář brány fungovat, musí být aspoň jeden z podporovaných protokolů centra IoT Edge otevřený pro příchozí provoz ze zařízení se systémem. Podporované protokoly jsou MQTT, AMQP a HTTPS. 

| Port | Protocol (Protokol) |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>Směrování zpráv ze zařízení příjem dat
Modul runtime IoT Edge může směrovat zprávy odeslané ze zařízení příjem dat, stejně jako zprávy odeslané moduly. Tato funkce umožňuje provádět analýzy v modulu spuštěném v bráně před odesláním dat do cloudu. 

V současné době je tak, jak směrovat zprávy odesílané zařízeními podřízené rozlišení na zprávy odeslané moduly. Zprávy odesílané modulem All obsahují vlastnost systému nazvanou **connectionModuleId** , ale zprávy odesílané ze zařízení pro příjem dat nejsou. Klauzule WHERE trasy můžete vyloučit všechny zprávy, které obsahují tuto vlastnost systému. 

Níže uvedený postup slouží jako příklad, který by odesílal zprávy ze všech podřízených zařízení do modulu s názvem `ai_insights`a potom z `ai_insights` na IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Další informace o směrování zpráv najdete v tématu [nasazení modulů a vytváření tras](./module-composition.md#declare-routes).


## <a name="enable-extended-offline-operation"></a>Povolit rozšířenou offline operaci

Od verze sady IoT Edge runtime v [1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) se dá zařízení brány a zařízení, která se k němu připojují, nakonfigurovat pro rozšířené operace offline. 

Díky této funkci se můžou místní moduly nebo zařízení se stejným zařízením znovu ověřit podle potřeby IoT Edge zařízení a vzájemně komunikovat pomocí zpráv a metod, a to i v případě, že se odpojíte od služby IoT Hub. Další informace najdete v tématu [vysvětlení rozšířených funkcí offline pro IoT Edge zařízení, moduly a podřízená zařízení](offline-capabilities.md).

Chcete-li povolit rozšířené možnosti offline, navažte vztah mezi nadřazenými a podřízenými zařízeními mezi zařízením IoT Edge brány a zařízeními pro příjem dat, která se k němu připojí. Tyto kroky jsou podrobněji vysvětleny v tématu [ověření zařízení pro příjem dat do Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Další kroky

Teď, když máte zařízení IoT Edge funguje jako transparentní brána, musíte pro příjem dat zařízení důvěřovat brány a odesílat zprávy do něj. Pokračujte tím, že na [Azure IoT Hub ověříte zařízení pro příjem dat](how-to-authenticate-downstream-device.md) s dalšími kroky v části nastavení vašeho transparentního scénáře brány. 
