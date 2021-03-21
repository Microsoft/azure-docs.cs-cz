---
title: Vytvoření testovacích certifikátů – Azure IoT Edge | Microsoft Docs
description: Vytvořte testovací certifikáty a Naučte se, jak je nainstalovat na zařízení Azure IoT Edge pro přípravu na produkční nasazení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d8cf3dbe9d1dc2ad329a0b5ab8fa9554c85ae55c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199069"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>Vytváření ukázkových certifikátů pro účely testování funkcí zařízení IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge zařízení vyžadují certifikáty pro zabezpečenou komunikaci mezi modulem runtime, moduly a všemi podřízenými zařízeními.
Pokud nemáte certifikační autoritu k vytvoření požadovaných certifikátů, můžete použít ukázkové certifikáty k vyzkoušení IoT Edge funkcí v testovacím prostředí.
Tento článek popisuje funkce skriptů generování certifikátů, které IoT Edge poskytuje pro testování.

Platnost těchto certifikátů vyprší během 30 dnů a neměly by se používat v žádném produkčním scénáři.

Můžete vytvořit certifikáty na jakémkoli počítači a pak je zkopírovat do zařízení IoT Edge.
K vytvoření certifikátů je snazší použít primární počítač místo jejich generování na svém IoT Edge samotném zařízení.
Pomocí primárního počítače můžete nastavit skripty jednou a pak je použít k vytvoření certifikátů pro více zařízení.

Při vytváření ukázkových certifikátů pro testování IoT Edge scénář použijte následující postup:

1. [Nastavte skripty](#set-up-scripts) pro generování certifikátů na vašem zařízení.
2. [Vytvořte certifikát kořenové certifikační autority](#create-root-ca-certificate) , který používáte k podepsání všech ostatních certifikátů pro váš scénář.
3. Vygenerujte certifikáty, které potřebujete pro scénář, který chcete testovat:
   * [Vytvořte IoT Edge certifikáty identit zařízení](#create-iot-edge-device-identity-certificates) pro zřizování zařízení s ověřováním pomocí certifikátu X. 509, a to buď ručně, nebo pomocí IoT Hub Device Provisioning Service.
   * [Vytvářejte IoT Edge certifikáty certifikační autority zařízení](#create-iot-edge-device-ca-certificates) pro IoT Edge zařízení ve scénářích brány.
   * [Vytvoření certifikátů pro příjem zařízení](#create-downstream-device-certificates) pro ověřování zařízení se systémem pro příjem dat ve scénáři brány.

## <a name="prerequisites"></a>Předpoklady

Vývojový počítač s nainstalovaným Git.

## <a name="set-up-scripts"></a>Nastavení skriptů

IoT Edge úložiště na GitHubu zahrnuje skripty pro generování certifikátů, které můžete použít k vytvoření ukázkových certifikátů.
V této části najdete pokyny k přípravě skriptů pro spuštění v počítači, a to buď v systému Windows nebo Linux.
Pokud jste na počítači se systémem Linux, přeskočte dopředu k [nastavení v systému Linux](#set-up-on-linux).

### <a name="set-up-on-windows"></a>Nastavení ve Windows

K vytvoření ukázkových certifikátů na zařízení s Windows je potřeba nainstalovat OpenSSL a pak klonovat skripty pro generování a nastavit je tak, aby se spouštěly místně v PowerShellu.

#### <a name="install-openssl"></a>Nainstalovat OpenSSL

Nainstalujte OpenSSL pro Windows na počítači, který používáte k vygenerování certifikátů.
Pokud jste již na zařízení s Windows nainstalovali OpenSSL, ujistěte se, že je v proměnné prostředí PATH k dispozici openssl.exe.

Existuje několik způsobů, jak nainstalovat OpenSSL, včetně následujících možností:

* **Jednodušší:** Stáhněte si a nainstalujte všechny [binární soubory OpenSSL třetích stran](https://wiki.openssl.org/index.php/Binaries), například z [OpenSSL v sourceforge](https://sourceforge.net/projects/openssl/). Přidejte úplnou cestu k openssl.exe do proměnné prostředí PATH.

* **Doporučené:** Stáhněte si zdrojový kód OpenSSL a sestavte binární soubory na svém počítači sami nebo prostřednictvím [vcpkg](https://github.com/Microsoft/vcpkg). Níže uvedené pokyny používají vcpkg ke stažení zdrojového kódu, kompilaci a instalaci OpenSSL na počítači s Windows pomocí jednoduchých kroků.

   1. Přejděte do adresáře, kam chcete nainstalovat vcpkg. Podle pokynů stáhněte a nainstalujte [vcpkg](https://github.com/Microsoft/vcpkg).

   2. Po instalaci vcpkg spusťte následující příkaz z příkazového řádku PowerShellu a nainstalujte balíček OpenSSL pro Windows x64. Dokončení instalace obvykle trvá přibližně 5 minut.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. Přidejte `<vcpkg path>\installed\x64-windows\tools\openssl` do proměnné prostředí PATH, aby byl soubor openssl.exe k dispozici pro vyvolání.

#### <a name="prepare-scripts-in-powershell"></a>Příprava skriptů v PowerShellu

Azure IoT Edge úložiště Git obsahuje skripty, které můžete použít k vygenerování testovacích certifikátů.
V této části naklonujte úložiště IoT Edge a spustíte skripty.

1. Otevřete okno PowerShellu v režimu správce.

2. Naklonujte IoT Edge úložiště Git, které obsahuje skripty pro generování ukázkových certifikátů. Použijte `git clone` příkaz nebo [Stáhněte soubor zip](https://github.com/Azure/iotedge/archive/master.zip).

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Přejděte do adresáře, ve kterém chcete pracovat. V celém tomto článku budeme tento adresář volat *\<WRKDIR>* . V tomto pracovním adresáři se vytvoří všechny certifikáty a klíče.

4. Zkopírujte konfigurační soubory a soubory skriptu z klonovaného úložiště do svého pracovního adresáře.

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Pokud jste úložiště stáhli jako soubor ZIP, název složky je `iotedge-master` a zbytek cesty je stejný.

5. Povolte prostředí PowerShell ke spouštění skriptů.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. Přeneste funkce používané skripty do globálního oboru názvů prostředí PowerShell.

   ```powershell
   . .\ca-certs.ps1
   ```

   V okně PowerShellu se zobrazí upozornění, že certifikáty generované tímto skriptem jsou jenom pro účely testování a neměly by se používat v produkčních scénářích.

7. Ověřte, že je OpenSSL správně nainstalovaný, a ujistěte se, že se nejedná o kolizi názvů se stávajícími certifikáty. V případě problémů by měl výstup skriptu popsat, jak je opravit v systému.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>Nastavení v systému Linux

K vytvoření ukázkových certifikátů na zařízení s Windows je potřeba klonovat skripty pro generování a nastavit je tak, aby se spouštěly místně v bash.

1. Naklonujte IoT Edge úložiště Git, které obsahuje skripty pro generování ukázkových certifikátů.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Přejděte do adresáře, ve kterém chcete pracovat. Do tohoto adresáře odkazujeme v celém článku *\<WRKDIR>* . V tomto adresáři se vytvoří všechny soubory certifikátů a klíčů.
  
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

## <a name="create-root-ca-certificate"></a>Vytvořit certifikát kořenové certifikační autority

Certifikát kořenové certifikační autority se používá ke zpřístupnění všech ostatních ukázkových certifikátů pro testování IoT Edgeho scénáře.
Stejný certifikát kořenové certifikační autority můžete dál používat ke zpřístupnění ukázkových certifikátů pro více IoT Edge nebo pro zařízení se zabezpečením.

Pokud v pracovní složce už máte certifikát kořenové certifikační autority, nevytvářejte nové.
Nový certifikát kořenové certifikační autority přepíše Starý a všechny podřízené certifikáty z původního certifikátu přestanou fungovat.
Pokud chcete více kořenových certifikátů certifikačních autorit, nezapomeňte je spravovat v samostatných složkách.

Než budete pokračovat s postupem v této části, postupujte podle kroků v oddílu [nastavení skriptů](#set-up-scripts) a připravte pracovní adresář se skripty pro generování ukázkových certifikátů.

### <a name="windows"></a>Windows

1. Přejděte do pracovního adresáře, kam jste umístili skripty pro generování certifikátů.

1. Vytvořte certifikát kořenové certifikační autority a podepište ho jako jeden zprostředkující certifikát. Certifikáty jsou umístěné v pracovním adresáři.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Tento příkaz skriptu vytvoří několik souborů certifikátů a klíčů, ale pokud se články dotazují na **certifikát kořenové certifikační autority**, použijte následující soubor:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

### <a name="linux"></a>Linux

1. Přejděte do pracovního adresáře, kam jste umístili skripty pro generování certifikátů.

1. Vytvořte certifikát kořenové certifikační autority a jeden zprostředkující certifikát.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Tento příkaz skriptu vytvoří několik souborů certifikátů a klíčů, ale pokud se články dotazují na **certifikát kořenové certifikační autority**, použijte následující soubor:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-identity-certificates"></a>Vytvoření certifikátů identit IoT Edge zařízení

Certifikáty identity zařízení slouží k zřizování IoT Edgech zařízení, pokud se rozhodnete použít ověřování pomocí certifikátu X. 509. Tyto certifikáty fungují bez ohledu na to, jestli používáte ruční zřizování nebo Automatické zřizování prostřednictvím Azure IoT Hub Device Provisioning Service (DPS).

Certifikáty identity zařízení najdete v oddílu **zřizování** konfiguračního souboru na zařízení IoT Edge.

Než budete postupovat podle kroků v této části, postupujte podle kroků v částech [nastavení skriptů](#set-up-scripts) a [Vytvoření certifikátu kořenové certifikační autority](#create-root-ca-certificate) .

### <a name="windows"></a>Windows

Pomocí následujícího příkazu vytvořte certifikát identity zařízení IoT Edge a privátní klíč:

```powershell
New-CACertsEdgeDeviceIdentity "<name>"
```

Název, který do tohoto příkazu předáte, bude ID zařízení IoT Edge v IoT Hub.

Příkaz nového zařízení identity vytvoří několik souborů certifikátů a klíčů, včetně tří, které budete používat při vytváření individuální registrace v DPS a instalaci IoT Edge Runtime:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

### <a name="linux"></a>Linux

Pomocí následujícího příkazu vytvořte certifikát identity zařízení IoT Edge a privátní klíč:

```bash
./certGen.sh create_edge_device_identity_certificate "<name>"
```

Název, který do tohoto příkazu předáte, bude ID zařízení IoT Edge v IoT Hub.

Skript vytvoří několik souborů certifikátů a klíčů, včetně tří, které budete používat při vytváření individuální registrace v DPS a instalaci IoT Edge Runtime:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

## <a name="create-iot-edge-device-ca-certificates"></a>Vytvoření certifikátů certifikační autority IoT Edge zařízení

Každé IoT Edge zařízení, které bude v produkčním prostředí potřebovat certifikát certifikační autority zařízení, na který odkazuje konfigurační soubor.
Certifikát certifikační autority zařízení zodpovídá za vytváření certifikátů pro moduly běžící na zařízení.
Je taky potřeba pro scénáře bran, protože certifikát certifikační autority zařízení je způsob, jakým IoT Edge zařízení ověřuje svoji identitu pro zařízení se stavem pro příjem dat.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Certifikáty certifikační autority zařízení najdete v části **certifikát** v souboru config. yaml na zařízení IoT Edge.
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Certifikáty certifikační autority zařízení najdete v části **hraniční autorita** v souboru config. toml na zařízení IoT Edge.
:::moniker-end

Než budete postupovat podle kroků v této části, postupujte podle kroků v částech [nastavení skriptů](#set-up-scripts) a [Vytvoření certifikátu kořenové certifikační autority](#create-root-ca-certificate) .

### <a name="windows"></a>Windows

1. Přejděte do pracovního adresáře, který obsahuje skripty generování certifikátů a certifikát kořenové certifikační autority.

2. Pomocí následujícího příkazu vytvořte certifikát certifikační autority IoT Edge zařízení a privátní klíč. Zadejte název certifikátu certifikační autority.

   ```powershell
   New-CACertsEdgeDevice "<CA cert name>"
   ```

   Tento příkaz vytvoří několik souborů certifikátů a klíčů. Následující pár certifikátů a klíčů je nutné zkopírovat do zařízení IoT Edge a odkazuje v konfiguračním souboru:

   * `<WRKDIR>\certs\iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-<CA cert name>.key.pem`

Název předaný příkazu **New-CACertsEdgeDevice** by neměl být stejný jako parametr HostName v konfiguračním souboru nebo ID zařízení v IoT Hub.

### <a name="linux"></a>Linux

1. Přejděte do pracovního adresáře, který obsahuje skripty generování certifikátů a certifikát kořenové certifikační autority.

2. Pomocí následujícího příkazu vytvořte certifikát certifikační autority IoT Edge zařízení a privátní klíč. Zadejte název certifikátu certifikační autority.

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "<CA cert name>"
   ```

   Tento příkaz skriptu vytvoří několik souborů certifikátů a klíčů. Následující pár certifikátů a klíčů je nutné zkopírovat do zařízení IoT Edge a odkazuje v konfiguračním souboru:

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

Název předaný příkazu **create_edge_device_ca_certificate** nesmí být stejný jako parametr HostName v konfiguračním souboru nebo ID zařízení v IoT Hub.

## <a name="create-downstream-device-certificates"></a>Vytvoření certifikátů pro příjem zařízení

Pokud nastavujete pro scénář brány podřízené zařízení IoT a chcete použít ověřování pomocí X. 509, můžete pro zařízení pro příjem dat vygenerovat ukázkové certifikáty.
Pokud chcete používat ověřování pomocí symetrického klíče, nemusíte pro zařízení pro příjem dat vytvářet další certifikáty.
Existují dva způsoby, jak ověřit zařízení IoT pomocí certifikátů X. 509: pomocí certifikátů podepsaných svým držitelem nebo pomocí certifikátů podepsaných certifikační autoritou (CA).
Pro ověřování X. 509 podepsané svým držitelem, které se někdy označuje jako ověřování kryptografickým otiskem, je potřeba vytvořit nové certifikáty, které se mají na zařízení IoT umístit.
Tyto certifikáty obsahují kryptografický otisk, který sdílíte s IoT Hub pro ověřování.
Pro ověřování podepsané certifikační autoritou (CA) X. 509 potřebujete certifikát od kořenové certifikační autority zaregistrovaný v IoT Hub, který používáte k podepisování certifikátů pro vaše zařízení IoT.
Jakékoli zařízení používající certifikát vydaný kořenovým certifikátem certifikační autority nebo jakýmkoli z jeho zprostředkujících certifikátů bude povoleno ověřování.

Skripty pro generování certifikátů vám můžou usnadnit testování těchto scénářů ověřování pomocí ukázkových certifikátů.

Než budete postupovat podle kroků v této části, postupujte podle kroků v částech [nastavení skriptů](#set-up-scripts) a [Vytvoření certifikátu kořenové certifikační autority](#create-root-ca-certificate) .

### <a name="self-signed-certificates"></a>Certifikáty podepsané svým držitelem

Když ověříte zařízení IoT pomocí certifikátů podepsaných svým držitelem, budete muset vytvořit certifikáty zařízení založené na certifikátu kořenové certifikační autority pro vaše řešení.
Pak z certifikátů načtete hexadecimální otisk, který zajistí IoT Hub.
Vaše zařízení IoT taky potřebuje kopii svých certifikátů zařízení, aby se mohla ověřit pomocí IoT Hub.

#### <a name="windows"></a>Windows

1. Přejděte do pracovního adresáře, který obsahuje skripty generování certifikátů a certifikát kořenové certifikační autority.

2. Pro zařízení pro příjem dat vytvořte dva certifikáty (primární a sekundární). Jednoduchá konvence pojmenování, která se má použít, je vytvoření certifikátů s názvem zařízení IoT a pak primárního nebo sekundárního popisku. Například:

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   Tento příkaz skriptu vytvoří několik souborů certifikátů a klíčů. Následující páry certifikátů a klíčů je nutné zkopírovat do podřízeného zařízení IoT a odkazovat v aplikacích, které se připojují k IoT Hub:

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. Načte otisk prstu SHA1 (nazývaný kryptografický otisk v IoT Hub kontextech) z každého certifikátu. Otisk prstu je řetězec šestnáctkového znaku 40. K zobrazení certifikátu a nalezení otisku prstu použijte následující příkaz OpenSSL:

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint
   ```

   Spusťte tento příkaz dvakrát, jednou pro primární certifikát a jednou pro sekundární certifikát. Při registraci nového zařízení IoT pomocí certifikátů X. 509 podepsaných svým držitelem zadáte otisky prstů pro oba certifikáty.

#### <a name="linux"></a>Linux

1. Přejděte do pracovního adresáře, který obsahuje skripty generování certifikátů a certifikát kořenové certifikační autority.

2. Pro zařízení pro příjem dat vytvořte dva certifikáty (primární a sekundární). Jednoduchá konvence pojmenování, která se má použít, je vytvoření certifikátů s názvem zařízení IoT a pak primárního nebo sekundárního popisku. Například:

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   Tento příkaz skriptu vytvoří několik souborů certifikátů a klíčů. Následující páry certifikátů a klíčů je nutné zkopírovat do podřízeného zařízení IoT a odkazovat v aplikacích, které se připojují k IoT Hub:

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. Načte otisk prstu SHA1 (nazývaný kryptografický otisk v IoT Hub kontextech) z každého certifikátu. Otisk prstu je řetězec šestnáctkového znaku 40. K zobrazení certifikátu a nalezení otisku prstu použijte následující příkaz OpenSSL:

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Při registraci nového zařízení IoT pomocí certifikátů X. 509 podepsaných svým držitelem zadáte jak primární, tak i sekundární otisk.

### <a name="ca-signed-certificates"></a>Certifikáty podepsané certifikační autoritou

Při ověřování zařízení IoT pomocí certifikátů podepsaných svým držitelem je potřeba nahrát certifikát kořenové certifikační autority pro vaše řešení do IoT Hub.
Pak provedete ověření, které IoT Hub, že vlastníte certifikát kořenové certifikační autority.
Nakonec použijete stejný certifikát kořenové certifikační autority k vytvoření certifikátů zařízení, které se umístí do zařízení IoT, aby se mohlo ověřit pomocí IoT Hub.

Certifikáty v této části jsou popsané v tématu [nastavení zabezpečení X. 509 ve službě Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md).

#### <a name="windows"></a>Windows

1. Nahrajte soubor certifikátu kořenové certifikační autority z pracovního adresáře `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` do služby IoT Hub.

2. Pomocí kódu uvedeného v Azure Portal ověřte, zda vlastníte certifikát kořenové certifikační autority.

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. Vytvořte řetěz certifikátů pro vaše zařízení pro příjem dat. Použijte stejné ID zařízení, ve kterém je zařízení zaregistrované ve službě IoT Hub.

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   Tento příkaz skriptu vytvoří několik souborů certifikátů a klíčů. Následující páry certifikátů a klíčů je nutné zkopírovat do podřízeného zařízení IoT a odkazovat v aplikacích, které se připojují k IoT Hub:

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. Nahrajte soubor certifikátu kořenové certifikační autority z pracovního adresáře `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` do služby IoT Hub.

2. Pomocí kódu uvedeného v Azure Portal ověřte, zda vlastníte certifikát kořenové certifikační autority.

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. Vytvořte řetěz certifikátů pro vaše zařízení pro příjem dat. Použijte stejné ID zařízení, ve kterém je zařízení zaregistrované ve službě IoT Hub.

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   Tento příkaz skriptu vytvoří několik souborů certifikátů a klíčů. Následující páry certifikátů a klíčů je nutné zkopírovat do podřízeného zařízení IoT a odkazovat v aplikacích, které se připojují k IoT Hub:

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
