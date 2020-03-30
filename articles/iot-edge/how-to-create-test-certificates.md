---
title: Vytváření testovacích certifikátů – Azure IoT Edge | Dokumenty společnosti Microsoft
description: Vytvořte testovací certifikáty a zjistěte, jak je nainstalovat na zařízení Azure IoT Edge, abyste se připravili na produkční nasazení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5afb9b7a6ba1ffb99df064c9f92780dc820b2e8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535982"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>Vytvoření ukázkových certifikátů pro testování funkcí zařízení IoT Edge

Zařízení IoT Edge vyžadují certifikáty pro zabezpečenou komunikaci mezi modulem runtime, moduly a všemi zařízeními pro příjem dat.
Pokud nemáte certifikační autoritu k vytvoření požadovaných certifikátů, můžete použít ukázkové certifikáty vyzkoušet funkce IoT Edge v testovacím prostředí.
Tento článek popisuje funkce skriptů generování certifikátů, které poskytuje IoT Edge pro testování.

Platnost těchto certifikátů vyprší za 30 dní a neměla by být použita v žádném produkčním scénáři.

Certifikáty můžete vytvořit na libovolném počítači a pak je zkopírovat do zařízení IoT Edge.
Je jednodušší použít primární počítač k vytvoření certifikátů, než je generovat na samotném zařízení IoT Edge.
Pomocí primárního počítače můžete skripty nastavit jednou a potom proces zopakovat a vytvořit certifikáty pro více zařízení.

Následujícím postupem vytvořte ukázkové certifikáty pro testování scénáře IoT Edge:

1. [Nastavte skripty](#set-up-scripts) pro generování certifikátů v zařízení.
2. [Vytvořte kořenový certifikát certifikační autority,](#create-root-ca-certificate) který používáte k podepsání všech ostatních certifikátů pro váš scénář.
3. Vygenerujte certifikáty, které potřebujete pro scénář, který chcete testovat:
   * [Vytvořte certifikáty identity zařízení IoT Edge](#create-iot-edge-device-identity-certificates) a otestujte automatické zřizování pomocí služby Zřizování zařízení služby IoT Hub.
   * [Vytvořte certifikáty certifikační autority zařízení IoT Edge](#create-iot-edge-device-ca-certificates) pro testování produkčních scénářů nebo scénářů brány.
   * [Vytvořte certifikáty navazujících zařízení](#create-downstream-device-certificates) a otestujte ověřování navazujících zařízení do služby IoT Hub ve scénáři brány.

## <a name="prerequisites"></a>Požadavky

Vývojový počítač s nainstalovaným Gitem.

## <a name="set-up-scripts"></a>Nastavení skriptů

Úložiště IoT Edge na GitHubu obsahuje skripty pro generování certifikátů, které můžete použít k vytvoření ukázkových certifikátů.
Tato část obsahuje pokyny pro přípravu skriptů ke spuštění v počítači, a to buď v systému Windows nebo Linux.
Pokud jste na počítači s Linuxem, přeskočte dopředu [nastavit na Linuxu](#set-up-on-linux).

### <a name="set-up-on-windows"></a>Nastavení ve Windows

Chcete-li vytvořit ukázkové certifikáty na zařízení se systémem Windows, musíte nainstalovat OpenSSL a potom klonovat skripty generování a nastavit je tak, aby se spouštěly místně v prostředí PowerShell.

#### <a name="install-openssl"></a>Instalace OpenSSL

Nainstalujte OpenSSL pro Windows do počítače, který používáte ke generování certifikátů.
Pokud již máte na zařízení se systémem Windows nainstalovaný soubor OpenSSL, můžete tento krok přeskočit, ale ujistěte se, že je v proměnné prostředí PATH k dispozici soubor openssl.exe.

Existuje několik způsobů instalace OpenSSL, včetně následujících možností:

* **Jednodušší:** Stáhněte a nainstalujte všechny [binární soubory OpenSSL třetích stran](https://wiki.openssl.org/index.php/Binaries), například z [OpenSSL na SourceForge](https://sourceforge.net/projects/openssl/). Přidejte úplnou cestu k souboru openssl.exe do proměnné prostředí PATH.

* **Doporučeno:** Stáhněte si zdrojový kód OpenSSL a vytvořte binární soubory na vašem počítači sami nebo přes [vcpkg](https://github.com/Microsoft/vcpkg). Níže uvedené pokyny používají vcpkg ke stažení zdrojového kódu, kompilaci a instalaci OpenSSL do počítače se systémem Windows pomocí jednoduchých kroků.

   1. Přejděte do adresáře, do kterého chcete nainstalovat vcpkg. Podle pokynů stáhněte a nainstalujte [vcpkg](https://github.com/Microsoft/vcpkg).

   2. Po instalaci vcpkg spusťte následující příkaz z výzvy prostředí PowerShell a nainstalujte balíček OpenSSL pro systém Windows x64. Instalace obvykle trvá přibližně 5 minut.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. Přidejte `<vcpkg path>\installed\x64-windows\tools\openssl` do proměnné prostředí PATH tak, aby byl k dispozici soubor openssl.exe pro vyvolání.

#### <a name="prepare-scripts-in-powershell"></a>Příprava skriptů v PowerShellu

Úložiště Git Azure IoT Edge obsahuje skripty, které můžete použít ke generování testovacích certifikátů.
V této části můžete klonovat repo IoT Edge a spustit skripty.

1. Otevřete okno PowerShellu v režimu správce.

2. Klonujte úložiště Git IoT Edge, které obsahuje skripty pro generování demo certifikátů. Použijte `git clone` příkaz nebo [stáhněte soubor ZIP](https://github.com/Azure/iotedge/archive/master.zip).

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Přejděte do adresáře, ve kterém chcete pracovat. V tomto článku budeme volat tento adresář * \<WRKDIR>*. V tomto pracovním adresáři budou vytvořeny všechny certifikáty a klíče.

4. Zkopírujte soubory konfigurace a skriptu z klonovaného úložiště do pracovního adresáře.

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Pokud jste stáhli repo jako ZIP, pak `iotedge-master` název složky je a zbytek cesty je stejný.

5. Povolte powershellu a spouštěj skripty.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. Přenese funkce používané skripty do globálního oboru názvů prostředí PowerShell.

   ```powershell
   . .\ca-certs.ps1
   ```

   Okno Prostředí PowerShell zobrazí upozornění, že certifikáty generované tímto skriptem jsou pouze pro účely testování a neměly by být používány v produkčních scénářích.

7. Ověřte, zda byl openssl správně nainstalován a ujistěte se, že nedojde ke kolizím názvů s existujícími certifikáty. Pokud se vyskytly problémy, měl by výstup skriptu popisovat, jak je opravit v systému.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>Nastavení na Linuxu

Chcete-li vytvořit ukázkové certifikáty na zařízení se systémem Windows, musíte klonovat skripty generování a nastavit je tak, aby běžely místně v bash.

1. Klonujte úložiště Git IoT Edge, které obsahuje skripty pro generování demo certifikátů.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Přejděte do adresáře, ve kterém chcete pracovat. Budeme odkazovat na tento adresář v celém článku jako * \<WRKDIR>*. V tomto adresáři budou vytvořeny všechny soubory certifikátů a klíčů.
  
3. Zkopírujte soubory konfigurace a skriptu z naklonovaného úložiště IoT Edge do pracovního adresáře.

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

## <a name="create-root-ca-certificate"></a>Vytvořit kořenový certifikát certifikační autority

Kořenový certifikát certifikační autority se používá k vytváření všech ostatních ukázkových certifikátů pro testování scénáře IoT Edge.
Můžete nadále používat stejný kořenový certifikát certifikační autority k výrobě ukázkových certifikátů pro více zařízení IoT Edge nebo podřízené zařízení.

Pokud již máte v pracovní složce jeden kořenový certifikát certifikační autority, nevytvářejte nový.
Nový kořenový certifikát certifikační autority přepíše staré certifikáty a všechny certifikáty navazující z toho starého přestanou fungovat.
Pokud chcete více kořenových certifikátů certifikační autority, nezapomeňte je spravovat v samostatných složkách.

Než budete pokračovat v krocích v této části, postupujte podle pokynů v části [Nastavení skriptů](#set-up-scripts) a připravte si pracovní adresář se skripty pro generování ukázkových certifikátů.

### <a name="windows"></a>Windows

1. Přejděte do pracovního adresáře, do kterého jste umístili skripty pro generování certifikátů.

1. Vytvořte kořenový certifikát certifikační autority a nech ho podepsat jeden zprostředkující certifikát. Všechny certifikáty jsou umístěny ve vašem pracovním adresáři.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Tento příkaz skriptu vytvoří několik souborů certifikátů a klíčů, ale když články požadují **kořenový certifikát certifikační autority**, použijte následující soubor:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

### <a name="linux"></a>Linux

1. Přejděte do pracovního adresáře, do kterého jste umístili skripty pro generování certifikátů.

1. Vytvořte kořenový certifikát certifikační autority a jeden zprostředkující certifikát.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Tento příkaz skriptu vytvoří několik souborů certifikátů a klíčů, ale když články požadují **kořenový certifikát certifikační autority**, použijte následující soubor:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-ca-certificates"></a>Vytvoření certifikátů certifikační autority zařízení IoT Edge

Každé zařízení IoT Edge, které se bude promítat do produkčního prostředí, potřebuje certifikát certifikační autority zařízení, na který odkazuje soubor config.yaml.
Certifikát certifikační autority zařízení je zodpovědný za vytváření certifikátů pro moduly spuštěné v zařízení.
Je to také způsob, jakým zařízení IoT Edge ověřuje svou identitu při připojování k zařízením pro příjem dat.

Certifikáty certifikační autority zařízení jsou uvedeny v části **Certifikát** souboru config.yaml na zařízení IoT Edge.

Než budete pokračovat v krocích v této části, postupujte podle pokynů v částech Nastavení skriptů a [Vytvoření kořenového certifikátu certifikační](#create-root-ca-certificate) [autority.](#set-up-scripts)

### <a name="windows"></a>Windows

1. Přejděte do pracovního adresáře, který obsahuje skripty pro generování certifikátů a kořenový certifikát certifikační autority.

2. Vytvořte certifikát certifikační autority zařízení IoT Edge a soukromý klíč pomocí následujícího příkazu. Zadejte název certifikátu certifikační autority, například **MyEdgeDeviceCA**, který se používá k pojmenování výstupních souborů.

   ```powershell
   New-CACertsEdgeDevice "MyEdgeDeviceCA"
   ```

   Tento příkaz skriptu vytvoří několik souborů certifikátů a klíčů. Následující certifikát a pár klíčů je třeba zkopírovat do zařízení IoT Edge a odkazovat v souboru config.yaml:

   * `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`

Název zařízení brány předaný do těchto skriptů by neměl být stejný jako parametr "hostname" v config.yaml nebo ID zařízení v centru IoT Hub.
Skripty vám pomohou vyhnout se problémům připojením řetězce "ca" k názvu zařízení brány, aby se zabránilo kolizi názvů v případě, že uživatel nastaví IoT Edge se stejným názvem na obou místech.
Je však vhodné vyhnout se použití stejného názvu.

### <a name="linux"></a>Linux

1. Přejděte do pracovního adresáře, který obsahuje skripty pro generování certifikátů a kořenový certifikát certifikační autority.

2. Vytvořte certifikát certifikační autority zařízení IoT Edge a soukromý klíč pomocí následujícího příkazu. Zadejte název certifikátu certifikační autority, například **MyEdgeDeviceCA**, který se používá k pojmenování výstupních souborů.

   ```bash
   ./certGen.sh create_edge_device_certificate "MyEdgeDeviceCA"
   ```

   Tento příkaz skriptu vytvoří několik souborů certifikátů a klíčů. Následující certifikát a pár klíčů je třeba zkopírovat do zařízení IoT Edge a odkazovat v souboru config.yaml:

   * `<WRKDIR>/certs/iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-MyEdgeDeviceCA.key.pem`

Název zařízení brány předaný do těchto skriptů by neměl být stejný jako parametr "hostname" v config.yaml nebo ID zařízení v centru IoT Hub.
Skripty vám pomohou vyhnout se problémům připojením řetězce "ca" k názvu zařízení brány, aby se zabránilo kolizi názvů v případě, že uživatel nastaví IoT Edge se stejným názvem na obou místech.
Je však vhodné vyhnout se použití stejného názvu.

## <a name="create-iot-edge-device-identity-certificates"></a>Vytvoření certifikátů identity zařízení IoT Edge

Certifikáty identit zařízení se používají k zřizování zařízení IoT Edge prostřednictvím [služby Azure IoT Hub Device Provisioning Service (DPS).](../iot-dps/index.yml)

Certifikáty identity zařízení přecházejí v části **Zřizování** souboru config.yaml na zařízení IoT Edge.

Než budete pokračovat v krocích v této části, postupujte podle pokynů v částech Nastavení skriptů a [Vytvoření kořenového certifikátu certifikační](#create-root-ca-certificate) [autority.](#set-up-scripts)

### <a name="windows"></a>Windows

Vytvořte certifikát identity zařízení IoT Edge a soukromý klíč pomocí následujícího příkazu:

```powershell
New-CACertsEdgeDeviceIdentity "<name>"
```

Název, který předáte tomuto příkazu, bude ID zařízení pro zařízení IoT Edge v centru IoT Hub.

Nový příkaz identity zařízení vytvoří několik souborů certifikátů a klíčů, včetně dvou, které budete používat při vytváření individuální registrace v DPS a instalaci runtime IoT Edge:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

### <a name="linux"></a>Linux

Vytvořte certifikát identity zařízení IoT Edge a soukromý klíč pomocí následujícího příkazu:

```bash
./certGen.sh create_edge_device_identity_certificate "<name>"
```

Název, který předáte tomuto příkazu, bude ID zařízení pro zařízení IoT Edge v centru IoT Hub.

Skript vytvoří několik souborů certifikátů a klíčů, včetně dvou, které budete používat při vytváření individuálníregistrace v DPS a instalaci runtime IoT Edge:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

## <a name="create-downstream-device-certificates"></a>Vytvoření certifikátů navazujících zařízení

Pokud nastavujete podřízené zařízení IoT pro scénář brány, můžete generovat ukázkové certifikáty pro ověřování X.509.
Existují dva způsoby ověření zařízení IoT pomocí certifikátů X.509: pomocí certifikátů podepsaných svým držitelem nebo pomocí certifikátů podepsaných certifikační autoritou (CA).
Pro ověřování vlastním podpisem X.509, které se někdy označuje jako ověřování kryptografickým otiskem, je třeba vytvořit nové certifikáty, které chcete umístit na zařízení IoT.
Tyto certifikáty mají kryptografický otisk v nich, které sdílíte s IoT Hub pro ověřování.
Pro ověřování podepsané certifikační autoritou X.509 (CA) potřebujete kořenový certifikát certifikační autority registrovaný v centru IoT Hub, který používáte k podepisování certifikátů pro vaše zařízení IoT.
Jakékoli zařízení používající certifikát vydaný kořenovým certifikátem certifikační autority nebo některé z jeho zprostředkujících certifikátů bude povoleno k ověření.

Skripty generování certifikátů vám mohou pomoci vytvořit ukázkové certifikáty, které otestují některý z těchto scénářů ověřování.

Než budete pokračovat v krocích v této části, postupujte podle pokynů v částech Nastavení skriptů a [Vytvoření kořenového certifikátu certifikační](#create-root-ca-certificate) [autority.](#set-up-scripts)

### <a name="self-signed-certificates"></a>Certifikáty podepsané svým držitelem

Při ověřování zařízení IoT s certifikáty podepsanými svým držitelem je třeba vytvořit certifikáty zařízení na základě kořenového certifikátu certifikační autority pro vaše řešení.
Potom načtete šestnáctkový "otisk prstu" z certifikátů, které chcete poskytnout službu IoT Hub.
Vaše zařízení IoT také potřebuje kopii svých certifikátů zařízení, aby bylo možné ověřit pomocí služby IoT Hub.

#### <a name="windows"></a>Windows

1. Přejděte do pracovního adresáře, který obsahuje skripty pro generování certifikátů a kořenový certifikát certifikační autority.

2. Vytvořte dva certifikáty (primární a sekundární) pro zařízení pro příjem dat. Snadno se používá konvence pojmenování je vytvoření certifikátů s názvem zařízení IoT a pak primární nebo sekundární popisek. Například:

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   Tento příkaz skriptu vytvoří několik souborů certifikátů a klíčů. Následující certifikát a páry klíčů je třeba zkopírovat do navazujícího zařízení IoT a odkazovat v aplikacích, které se připojují k službě IoT Hub:

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. Načíst otisk prstu SHA1 (tzv. kryptografický otisk v kontextech služby IoT Hub) z každého certifikátu. Otisk prstu je řetězec 40 šestnáctkových znaků. Pomocí následujícího příkazu openssl zobrazíte certifikát a najděte otisk prstu:

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Spusťte tento příkaz dvakrát, jednou pro primární certifikát a jednou pro sekundární certifikát. Otisky prstů pro oba certifikáty poskytujete při registraci nového zařízení IoT pomocí certifikátů X.509 podepsaných svým držitelem.

#### <a name="linux"></a>Linux

1. Přejděte do pracovního adresáře, který obsahuje skripty pro generování certifikátů a kořenový certifikát certifikační autority.

2. Vytvořte dva certifikáty (primární a sekundární) pro zařízení pro příjem dat. Snadno se používá konvence pojmenování je vytvoření certifikátů s názvem zařízení IoT a pak primární nebo sekundární popisek. Například:

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   Tento příkaz skriptu vytvoří několik souborů certifikátů a klíčů. Následující certifikát a páry klíčů je třeba zkopírovat do navazujícího zařízení IoT a odkazovat v aplikacích, které se připojují k službě IoT Hub:

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. Načíst otisk prstu SHA1 (tzv. kryptografický otisk v kontextech služby IoT Hub) z každého certifikátu. Otisk prstu je řetězec 40 šestnáctkových znaků. Pomocí následujícího příkazu openssl zobrazíte certifikát a najděte otisk prstu:

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Primární i sekundární otisk prstu poskytujete při registraci nového zařízení IoT pomocí certifikátů X.509 podepsaných svým držitelem.

### <a name="ca-signed-certificates"></a>Certifikáty podepsané certifikační autoritou

Při ověřování zařízení IoT s certifikáty podepsanými svým držitelem je potřeba nahrát kořenový certifikát certifikační autority pro vaše řešení do služby IoT Hub.
Potom provedete ověření, abyste ioT Hubu prokázali, že vlastníte kořenový certifikát certifikační autority.
Nakonec použijete stejný kořenový certifikát certifikační autority k vytvoření certifikátů zařízení, které chcete umístit na zařízení IoT, aby se mohlo ověřit pomocí služby IoT Hub.

Certifikáty v této části jsou pro kroky v [nastavení zabezpečení X.509 ve vašem centru Azure IoT hub](../iot-hub/iot-hub-security-x509-get-started.md).

#### <a name="windows"></a>Windows

1. Nahrajte kořenový soubor certifikátu `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`certifikační autority z pracovního adresáře do centra IoT Hub.

2. Pomocí kódu uvedeného na portálu Azure ověřte, že vlastníte tento kořenový certifikát certifikační autority.

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. Vytvořte řetěz certifikátů pro vaše zařízení pro příjem dat. Použijte stejné ID zařízení, se kterým je zařízení registrované v centru IoT Hub.

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   Tento příkaz skriptu vytvoří několik souborů certifikátů a klíčů. Následující certifikát a páry klíčů je třeba zkopírovat do navazujícího zařízení IoT a odkazovat v aplikacích, které se připojují k službě IoT Hub:

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. Nahrajte kořenový soubor certifikátu `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`certifikační autority z pracovního adresáře do centra IoT Hub.

2. Pomocí kódu uvedeného na portálu Azure ověřte, že vlastníte tento kořenový certifikát certifikační autority.

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. Vytvořte řetěz certifikátů pro vaše zařízení pro příjem dat. Použijte stejné ID zařízení, se kterým je zařízení registrované v centru IoT Hub.

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   Tento příkaz skriptu vytvoří několik souborů certifikátů a klíčů. Následující certifikát a páry klíčů je třeba zkopírovat do navazujícího zařízení IoT a odkazovat v aplikacích, které se připojují k službě IoT Hub:

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
