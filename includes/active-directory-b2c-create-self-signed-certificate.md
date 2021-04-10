---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 41d9962657aa81dbe34a52302d1b68ec655f2893
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102095285"
---
Pokud ještě certifikát nemáte, můžete použít certifikát podepsaný svým držitelem. Certifikát podepsaný svým držitelem je certifikát zabezpečení, který není podepsaný certifikační autoritou (CA), a neposkytuje bezpečnostní záruky certifikátu podepsaného certifikační autoritou. 

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

1. Otevřete **Spravovat uživatelské certifikáty**  >  **aktuální uživatelé**  >  **osobní**  >  **certifikáty**  >  *YourAppName.yourtenant.onmicrosoft.com*.
1. Vyberte certifikát a potom vyberte **akci**  >  **všechny úkoly**  >  **exportovat**.
1. Vyberte **Ano**  >    >  **, další Ano, exportovat privátní klíč**  >  .
1. Přijměte výchozí hodnoty pro **Formát souboru pro export**.
1. Zadejte heslo pro certifikát.

Aby Azure AD B2C přijímalo heslo k souboru. pfx, musí být heslo zašifrované pomocí možnosti TripleDES-SHA1 v nástroji pro export certifikátů Windows, a to na rozdíl od AES256-SHA256.

# <a name="macos"></a>[macOS](#tab/macos)

V macOS použijte k vygenerování certifikátu [pomocníka certifikátů](https://support.apple.com/guide/keychain-access/aside/glosa3ed0609/11.0/mac/11.0) v řetězci klíčů.

1. Postupujte podle pokynů pro [Vytvoření certifikátů podepsaných svým držitelem v přístupu k řetězci klíčů na Macu](https://support.apple.com/guide/keychain-access/kyca8916/mac).
1. V aplikaci pro přístup k řetězci klíčů na Macu vyberte certifikát, který jste vytvořili.
1. Vyberte   >  **položky exportu** souborů.
1. Vyberte název souboru a uložte svůj certifikát. Například **certifikát podepsaný svým držitelem. p12**.
1. Jako **Formát souboru** vyberte **Personal Information Exchange (. P12)**.
1. Vyberte **Uložit**.
1. Zadejte **heslo** a pak **Ověřte** heslo.
1. Nahraďte příponu souboru na `.pfx` . Například **self-signed-certificate. pfx**.

---