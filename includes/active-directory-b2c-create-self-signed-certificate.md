---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 3458a4f3ba337d4c470c21fa5d89b28f5f2701a3
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916912"
---
Pokud ještě certifikát nemáte, můžete pro tento kurz použít certifikát podepsaný svým držitelem. Certifikát podepsaný svým držitelem je certifikát zabezpečení, který není podepsaný certifikační autoritou (CA). Neposkytují všechny záruky zabezpečení certifikátu podepsaného certifikační autoritou. 

# <a name="windows"></a>[Windows](#tab/windows)

Ve Windows použijte k vygenerování certifikátu rutinu [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) prostředí PowerShell.

1. Spuštěním tohoto příkazu PowerShellu Vygenerujte certifikát podepsaný svým držitelem. Upravte `-Subject` argument podle potřeby pro vaši aplikaci a Azure AD B2C název tenanta. Můžete také upravit `-NotAfter` Datum a zadat jiné vypršení platnosti certifikátu.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. Otevřete **Spravovat uživatelské certifikáty**  >  **aktuální uživatel**  >  **osobní**  >  **certifikáty**  >  *YourAppName.yourtenant.onmicrosoft.com* .
1. Vyberte certifikát > **akci**  >  **všechny úkoly**  >  **exportovat** .
1. Vyberte **Ano**  >    >  **, další Ano, exportovat privátní klíč**  >  **Další** .
1. Přijměte výchozí hodnoty pro **Formát souboru pro export** .
1. Zadejte heslo pro certifikát.

Aby Azure AD B2C přijímalo heslo k souboru. pfx, musí být heslo šifrováno pomocí možnosti TripleDES-SHA1 v nástroji pro export certifikátů Windows, a to na rozdíl od AES256-SHA256.

# <a name="macos"></a>[macOS](#tab/macos)

V macOS použijte k vygenerování certifikátu [pomocníka certifikátů](https://support.apple.com/guide/keychain-access/aside/glosa3ed0609/11.0/mac/11.0) v řetězci klíčů.

1. Postupujte podle pokynů v tématu [Vytvoření certifikátů podepsaných svým držitelem v přístupu pomocí řetězce klíčů na Macu](https://support.apple.com/guide/keychain-access/kyca8916/mac).
1. V aplikaci pro přístup k řetězci klíčů na Macu vyberte certifikát, který jste vytvořili.
1. Vyberte soubor > exportovat položky.
1. Vyberte název souboru a uložte svůj certifikát. Například **certifikát podepsaný svým držitelem. p12**. 
1. Jako **Formát souboru** vyberte **Personal Information Exchange (. P12)**.
1. Vyberte **Uložit**.
1. Zadejte **heslo** a pak **Ověřte** heslo.
1. Nahraďte příponu souboru na `.pfx` . Například **self-signed-certificate. pfx**. 

---
