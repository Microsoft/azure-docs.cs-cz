---
title: Exportovat certifikáty z Azure Key Vault
description: Naučte se exportovat certifikáty z Azure Key Vault.
services: key-vault
author: sebansal
tags: azure-key-vault
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.custom: mvc
ms.date: 08/11/2020
ms.author: sebansal
ms.openlocfilehash: 0efe0164d8e1a4e5bc3b9d6d7313855740afd316
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767902"
---
# <a name="export-certificates-from-azure-key-vault"></a>Exportovat certifikáty z Azure Key Vault

Naučte se exportovat certifikáty z Azure Key Vault. Certifikáty můžete exportovat pomocí rozhraní příkazového řádku Azure, Azure PowerShell nebo Azure Portal. 

## <a name="about-azure-key-vault-certificates"></a>Informace o certifikátech ve službě Azure Key Vault

Azure Key Vault umožňuje snadno zřídit, spravovat a nasazovat digitální certifikáty pro vaši síť. Umožňuje taky zabezpečenou komunikaci pro aplikace. Další informace najdete v tématu [Azure Key Vault certifikáty](./about-certificates.md) .

### <a name="composition-of-a-certificate"></a>Složení certifikátu

Při vytvoření certifikátu Key Vault se vytvoří adresovatelný *klíč* a *tajný kód* , který má stejný název. Klíč Key Vault umožňuje operace s klíči. Key Vault tajný klíč umožňuje načtení hodnoty certifikátu jako tajného klíče. Certifikát Key Vault obsahuje také veřejná metadata certifikátu x509. Další informace najdete v [části složení certifikátu](./about-certificates.md#composition-of-a-certificate) .

### <a name="exportable-and-non-exportable-keys"></a>Exportovatelné a neexportovatelné klíče

Po vytvoření Key Vault certifikátu ho můžete načíst z adresního tajného klíče s privátním klíčem. Načtěte certifikát ve formátu PFX nebo PEM.

- **Exportovatelný**: zásady použité k vytvoření certifikátu označují, že klíč se dá exportovat.
- **Neexportovatelný**: zásady použité k vytvoření certifikátu označují, že klíč není exportovatelný. V takovém případě privátní klíč není součástí hodnoty, když se načte jako tajný kód.

Podporované typy typů: RSA, RSA-HSM, ES, ES-HSM, Oct ( [zde](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype)uvedené) exportovatelné je povolené jenom pro RSA, ES. Klíče HSM by nebyly exportovatelné.

Další informace najdete v tématu [o Azure Key Vaultch certifikátech](./about-certificates.md#exportable-or-non-exportable-key) .

## <a name="export-stored-certificates"></a>Export uložených certifikátů

Certifikáty uložené ve službě Azure Key Vault můžete exportovat pomocí Azure CLI, Azure PowerShellu nebo webu Azure Portal.

> [!NOTE]
> Při importu certifikátu do trezoru klíčů stačí vyžadovat heslo certifikátu. Key Vault přidružené heslo neukládá. Při exportu certifikátu je heslo prázdné.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

K stažení **veřejné části** Key Vault certifikátu použijte následující příkaz v Azure CLI.

```azurecli
az keyvault certificate download --file
                                 [--encoding {DER, PEM}]
                                 [--id]
                                 [--name]
                                 [--subscription]
                                 [--vault-name]
                                 [--version]
```

Podívejte [se na příklady a definice parametrů,](/cli/azure/keyvault/certificate#az_keyvault_certificate_download) kde najdete další informace.

Stahování jako certifikát znamená získání veřejné části. Pokud chcete jak privátní klíč, tak i veřejná metadata, můžete si ho stáhnout jako tajný kód.

```azurecli
az keyvault secret download -–file {nameofcert.pfx}
                            [--encoding {ascii, base64, hex, utf-16be, utf-16le, utf-8}]
                            [--id]
                            [--name]
                            [--subscription]
                            [--vault-name]
                            [--version]
```

Další informace najdete v tématu [definice parametrů](/cli/azure/keyvault/secret#az_keyvault_secret_download).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pomocí tohoto příkazu v Azure PowerShell Získejte certifikát s názvem **TestCert01** z trezoru klíčů s názvem **ContosoKV01**. Chcete-li stáhnout certifikát jako soubor PFX, spusťte následující příkaz. Tyto příkazy přistupují k **SecretId** a pak obsah uloží jako soubor PFX.

```azurepowershell
$cert = Get-AzKeyVaultCertificate -VaultName "ContosoKV01" -Name "TestCert01"
$secret = Get-AzKeyVaultSecret -VaultName "ContosoKV01" -Name $cert.Name
$secretValueText = '';
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
    $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
$secretByte = [Convert]::FromBase64String($secretValueText)
$x509Cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509Cert.Import($secretByte, "", "Exportable,PersistKeySet")
$type = [System.Security.Cryptography.X509Certificates.X509ContentType]::Pfx
$pfxFileByte = $x509Cert.Export($type, $password)

# Write to a file
[System.IO.File]::WriteAllBytes("KeyVault.pfx", $pfxFileByte)
```

Tento příkaz exportuje celý řetěz certifikátů s privátním klíčem (to znamená, že je stejný jako při importu). Certifikát je chráněný heslem.
Další informace o příkazu **Get-AzKeyVaultCertificate** a parametrech najdete v tématu [Get-AzKeyVaultCertificate-example 2](/powershell/module/az.keyvault/Get-AzKeyVaultCertificate).

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Po vytvoření nebo importu certifikátu v okně **certifikátu** se na Azure Portal zobrazí oznámení o úspěšném vytvoření certifikátu. Výběrem certifikátu a aktuální verze zobrazíte možnost stažení.

Pokud chcete stáhnout certifikát, vyberte **Stáhnout ve formátu CER** nebo **Stáhnout ve formátu PFX/PEM**.

![Stažení certifikátu](../media/certificates/quick-create-portal/current-version-shown.png)

**Exportovat Azure App Service certifikátů**

Certifikáty Azure App Service představují pohodlný způsob, jak si koupit certifikáty SSL. V rámci portálu je můžete přiřadit k aplikacím Azure. Po importu se App Service certifikáty nacházejí v **tajných klíčích**.

Další informace najdete v postupu [export Azure App Service certifikátů](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx).

---

## <a name="read-more"></a>Další informace
* [Různé typy a definice souborů certifikátů](/archive/blogs/kaushal/various-ssltls-certificate-file-typesextensions)
