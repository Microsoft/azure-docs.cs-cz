---
title: Kurz – použití skriptů Microsoft k vytváření testovacích certifikátů x. 509 pro Azure IoT Hub | Microsoft Docs
description: Kurz – použití vlastních skriptů k vytvoření certifikátů certifikační autority a zařízení pro Azure IoT Hub
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: fc3717436619468e2db0bf4b408059112dae24cc
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384149"
---
# <a name="tutorial-using-microsoft-supplied-scripts-to-create-test-certificates"></a>Kurz: použití skriptů dodaných společností Microsoft k vytváření testovacích certifikátů

Microsoft poskytuje skripty PowerShellu a Bash, které vám pomůžou pochopit, jak vytvářet vlastní certifikáty X. 509 a ověřovat je IoT Hub. Skripty se nacházejí v [úložišti](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates)GitHubu. Jsou k dispozici pouze pro demonstrační účely. Certifikáty, které vytvořily, se nesmí používat pro produkční prostředí. Certifikáty obsahují pevně zakódované heslo ("1234") a vyprší po 30 dnech. V produkčním prostředí budete muset použít vlastní osvědčené postupy pro vytváření certifikátů a správu životního cyklu.

## <a name="powershell-scripts"></a>Skripty prostředí PowerShell

### <a name="step-1---setup"></a>Krok 1 – instalace

Získejte OpenSSL pro Windows. Viz <https://www.openssl.org/docs/faq.html#MISC4> místa ke stažení nebo <https://www.openssl.org/source/> sestavení ze zdroje. Pak spusťte předběžné skripty:

1. Zkopírujte skripty z tohoto [úložiště](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) GitHub do místního adresáře, ve kterém chcete pracovat. Všechny soubory budou vytvořeny jako podřízené položky tohoto adresáře.

1. Spusťte PowerShell jako správce.

1. Přejděte do adresáře, kam jste načetli skripty.

1. Na příkazovém řádku nastavte proměnnou prostředí `$ENV:OPENSSL_CONF` na adresář, ve kterém se nachází konfigurační soubor OpenSSL (OpenSSL. CNF).

1. Spusťte `Set-ExecutionPolicy -ExecutionPolicy Unrestricted` , aby PowerShell mohl spouštět skripty.

1. Spusťte `. .\ca-certs.ps1`. To přináší funkce skriptu do globálního oboru názvů prostředí PowerShell.

1. Spusťte `Test-CACertsPrerequisites`. Prostředí PowerShell používá ke správě certifikátů úložiště certifikátů Windows. Tento příkaz ověří, že nedojde ke kolizi názvů později se stávajícími certifikáty a že OpenSSL je správně nastavená.

### <a name="step-2---create-certificates"></a>Krok 2 – Vytvoření certifikátů

Spusťte `New-CACertsCertChain [ecc|rsa]`. Pro certifikáty certifikační autority se doporučuje ECC, ale není to nutné. Tento skript aktualizuje vaše adresáře a úložiště certifikátů Windows pomocí následujících certifikačních autorit a zprostředkujících certifikátů:

* intermediate1. pem
* intermediate2. pem
* intermediate3. pem
* RootCA. cer
* RootCA. pem

Po spuštění skriptu přidejte do svého IoT Hub nový certifikát certifikační autority (RootCA. pem):

1. Přejděte na IoT Hub a přejděte k certifikátům.

1. Vyberte **Přidat**.

1. Zadejte zobrazovaný název certifikátu certifikační autority.

1. Nahrajte certifikát certifikační autority.

1. Vyberte **Uložit**.

### <a name="step-3---prove-possession"></a>Krok 3 – důkaz vlastnictví

Teď, když jste do svého IoT Hub nahráli certifikát certifikační autority, budete muset prokázat, že ho skutečně vlastníte:

1. Vyberte nový certifikát certifikační autority.

1. V dialogovém okně **Podrobnosti o certifikátu** vyberte **Generovat ověřovací kód** . Další informace najdete v tématu [prokázání vlastnictví certifikátu certifikační autority](tutorial-x509-prove-possession.md).

1. Vytvořte certifikát, který obsahuje ověřovací kód. Pokud je například ověřovací kód `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` , spusťte následující příkaz pro vytvoření nového certifikátu v pracovním adresáři, který obsahuje předmět `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` . Skript vytvoří certifikát s názvem `VerifyCert4.cer` .

    `New-CACertsVerificationCert "106A5SD242AF512B3498BD609C4941E66R34H268DDB3288"`

1. Nahrajte `VerifyCert4.cer` do svého IoT Hub v dialogovém okně **Podrobnosti o certifikátu** .

1. Vyberte **Ověřit**.

### <a name="step-4---create-a-new-device"></a>Krok 4 – Vytvoření nového zařízení

Vytvořte zařízení pro IoT Hub:

1. V IoT Hub přejděte do části **zařízení IoT** .

1. Přidejte nové zařízení s ID `mydevice` .

1. Pro ověřování vyberte **podepsaná CA X. 509**.

1. Spuštěním `New-CACertsDevice mydevice` vytvořte nový certifikát zařízení. Tím se v pracovním adresáři vytvoří následující soubory:

   * `mydevice.pfx`
   * `mydevice-all.pem`
   * `mydevice-private.pem`
   * `mydevice-public.pem`

### <a name="step-5---test-your-device-certificate"></a>Krok 5 – testování certifikátu zařízení

Pokud chcete zjistit, jestli se váš certifikát zařízení může ověřit pro váš IoT Hub, Projděte si [test ověřování certifikátu](tutorial-x509-test-certificate.md) . Budete potřebovat verzi PFX certifikátu, `mydevice.pfx` .

### <a name="step-6---cleanup"></a>Krok 6 – vyčištění

V nabídce Start otevřete **spravovat certifikáty počítače** a přejděte na  **certifikáty – místní počítač > osobní**. Odebrání certifikátů vydaných "Azure IoT CA TestOnly *". Podobně odeberte příslušné certifikáty z **>důvěryhodné kořenové certifikační autority > certifikáty a >zprostředkujících certifikačních autorit > certifikátů**.

## <a name="bash-scripts"></a>Skripty bash

### <a name="step-1---setup"></a>Krok 1 – instalace

1. Spusťte bash.

1. Přejděte do adresáře, ve kterém chcete pracovat. Všechny soubory budou vytvořeny v tomto adresáři.

1. Zkopírujte `*.cnf` a `*.sh` do svého pracovního adresáře.

### <a name="step-2---create-certificates"></a>Krok 2 – Vytvoření certifikátů

1. Spusťte `./certGen.sh create_root_and_intermediate`. Tím se v adresáři **certifikáty** vytvoří následující soubory:

    * Azure-IoT-test-Only. Chain. ca. CERT. pem
    * Azure-IoT-test-Only. Intermediate. CERT. pem
    * Azure-IoT-test-Only. root. ca. CERT. pem

1. Přejděte na IoT Hub a přejděte k **certifikátům**.

1. Vyberte **Přidat**.

1. Zadejte zobrazovaný název certifikátu certifikační autority.

1. Nahrajte do svého IoT Hub jenom certifikát certifikační autority. Název certifikátu je `./certs/azure-iot-test-only.root.ca.cert.pem.`

1. Vyberte **Uložit**.

### <a name="step-3---prove-possession"></a>Krok 3 – důkaz vlastnictví

1. Vyberte nový certifikát certifikační autority vytvořený v předchozím kroku.

1. V dialogovém okně **Podrobnosti o certifikátu** vyberte **Generovat ověřovací kód** . Další informace najdete v tématu [prokázání vlastnictví certifikátu certifikační autority](tutorial-x509-prove-possession.md).

1. Vytvořte certifikát, který obsahuje ověřovací kód. Pokud je například ověřovací kód `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` , spusťte následující příkaz pro vytvoření nového certifikátu v pracovním adresáři s názvem, `verification-code.cert.pem` který obsahuje předmět `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` .

    `./certGen.sh create_verification_certificate "106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`

1. Nahrajte certifikát do služby IoT Hub v dialogovém okně **Podrobnosti certifikátu** .

1. Vyberte **Ověřit**.

### <a name="step-4---create-a-new-device"></a>Krok 4 – Vytvoření nového zařízení

Vytvořte zařízení pro IoT Hub:

1. V IoT Hub přejděte do části zařízení IoT.

1. Přidejte nové zařízení s ID `mydevice` .

1. Pro ověřování vyberte **podepsaná CA X. 509**.

1. Spuštěním `./certGen.sh create_device_certificate mydevice` vytvořte nový certifikát zařízení. Tím `new-device.cert.pem` se v pracovním adresáři vytvoří dva soubory s názvem a `new-device.cert.pfx` soubory.

### <a name="step-5---test-your-device-certificate"></a>Krok 5 – testování certifikátu zařízení

Pokud chcete zjistit, jestli se váš certifikát zařízení může ověřit pro váš IoT Hub, Projděte si [test ověřování certifikátu](tutorial-x509-test-certificate.md) . Budete potřebovat verzi PFX certifikátu, `new-device.cert.pfx` .

### <a name="step-6---cleanup"></a>Krok 6 – vyčištění

Protože skript bash jednoduše vytvoří certifikáty v pracovním adresáři, stačí je po dokončení testování odstranit.

## <a name="next-steps"></a>Další kroky

Pokud chcete svůj certifikát otestovat, použijte možnost [testování certifikátu ověřování](tutorial-x509-test-certificate.md) , abyste zjistili, jestli váš certifikát může ověřit vaše zařízení s vaším IoT Hub.
