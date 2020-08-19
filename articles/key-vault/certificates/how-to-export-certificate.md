---
title: Exportovat certifikát z Azure Key Vault
description: Exportovat certifikát z Azure Key Vault
services: key-vault
author: sebansal
tags: azure-key-vault
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.custom: mvc, devx-track-azurecli
ms.date: 08/11/2020
ms.author: sebansal
ms.openlocfilehash: afab65b22d9487f30da458346bf143a557bec0d8
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588888"
---
# <a name="export-certificate-from-azure-key-vault"></a>Exportovat certifikát z Azure Key Vault

Azure Key Vault umožňuje snadno zřídit, spravovat a nasazovat digitální certifikáty pro vaši síť a povolit zabezpečenou komunikaci pro aplikace. Obecnější informace o certifikátech najdete v tématu [Azure Key Vault certifikátů](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates) .

## <a name="about-azure-key-vault-certificate"></a>O certifikátu Azure Key trezoru

### <a name="composition-of-certificate"></a>Složení certifikátu
Když se vytvoří certifikát Key Vault, vytvoří se také adresovatelný klíč a tajný kód se stejným názvem. Klíč Key Vault umožňuje operace s klíči a Key Vault tajný klíč umožňuje načtení hodnoty certifikátu jako tajného klíče. Certifikát Key Vault obsahuje také veřejná metadata certifikátu x509. [Další informace](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate)

### <a name="exportable-or-non-exportable-keys"></a>Exportovatelné nebo neexportované klíče
Když se vytvoří certifikát Key Vault, dá se načíst z adresního tajemství s privátním klíčem ve formátu PFX nebo PEM. Zásady použité k vytvoření certifikátu musí označovat, že klíč lze exportovat. Pokud zásada indikuje, že není exportovaná, privátní klíč není součástí hodnoty, když se načte jako tajný kód.

Podporovány jsou dva typy klíčů – modul HARDWAROVÉho zabezpečení RSA nebo RSA s certifikáty. Exportovatelné je povolené jenom s šifrováním RSA, které nepodporuje modul HARDWAROVÉho zabezpečení RSA. [Další informace](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#exportable-or-non-exportable-key)

Uložený certifikát v Azure Key Vault lze exportovat pomocí rozhraní příkazového řádku Azure CLI, PowerShellu nebo portálu.

> [!NOTE]
> Je důležité si uvědomit, že při importu do trezoru klíčů budete vyžadovat heslo k certifikátu. Key Vault by nebylo možné uložit přidružené heslo, takže když byste certifikát exportovali, heslo bude prázdné.

## <a name="exporting-certificate-using-cli"></a>Export certifikátu pomocí rozhraní příkazového řádku
Následující příkaz vám umožní stáhnout **veřejnou část** certifikátu Key Vault.

```azurecli
az keyvault certificate download --file
                                 [--encoding {DER, PEM}]
                                 [--id]
                                 [--name]
                                 [--subscription]
                                 [--vault-name]
                                 [--version]
```
Příklady a definice parametrů najdete [tady.](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-download)



Pokud chcete stáhnout celý certifikát, tj. **veřejné i soukromé části jeho složení**, je možné ho stáhnout jako tajný kód.

```azurecli
az keyvault secret download –file {nameofcert.pfx}
                            [--encoding {ascii, base64, hex, utf-16be, utf-16le, utf-8}]
                            [--id]
                            [--name]
                            [--subscription]
                            [--vault-name]
                            [--version]
```
Definice parametrů najdete [tady.](https://docs.microsoft.com/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-download)


## <a name="exporting-certificate-using-powershell"></a>Export certifikátu pomocí PowerShellu

Tento příkaz načte certifikát s názvem TestCert01 z trezoru klíčů s názvem ContosoKV01. Chcete-li stáhnout certifikát jako soubor PFX, spusťte následující příkaz. Tyto příkazy přistupují k SecretId a pak obsah uloží jako soubor PFX.

```azurepowershell
$cert = Get-AzKeyVaultCertificate -VaultName "ContosoKV01" -Name "TestCert01"
$kvSecret = Get-AzKeyVaultSecret -VaultName "ContosoKV01" -Name $Cert.Name
$kvSecretBytes = [System.Convert]::FromBase64String($kvSecret.SecretValueText)
$certCollection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection
$certCollection.Import($kvSecretBytes,$null,[System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable)
$password = '******'
$protectedCertificateBytes = $certCollection.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $password)
$pfxPath = [Environment]::GetFolderPath("Desktop") + "\MyCert.pfx"
[System.IO.File]::WriteAllBytes($pfxPath, $protectedCertificateBytes)
```
Tato akce Exportuje celý řetěz certifikátů s privátním klíčem a tento certifikát bude chráněný heslem.
Další informace o ```Get-AzKeyVaultCertificate``` příkazech a parametrech najdete v [příkladu 2](https://docs.microsoft.com/powershell/module/az.keyvault/Get-AzKeyVaultCertificate?view=azps-4.4.0) .

## <a name="exporting-certificate-using-portal"></a>Export certifikátu pomocí portálu

Při vytváření nebo importu certifikátu v okně certifikát na portálu se zobrazí oznámení o úspěšném vytvoření certifikátu. Po výběru certifikátu můžete kliknout na aktuální verzi a zobrazí se možnost stáhnout.


Pokud kliknete na tlačítko Stáhnout ve formátu CER nebo stáhnout ve formátu PFX/PEM, můžete si certifikát stáhnout.


![Stažení certifikátu](../media/certificates/quick-create-portal/current-version-shown.png)


## <a name="exporting-app-service-certificate-from-key-vault"></a>Export App Service Certificate z trezoru klíčů

Azure App Service certifikáty poskytují pohodlný způsob, jak koupit certifikáty SSL a přiřazovat je přímo do aplikací Azure přímo z portálu. Tyto certifikáty je také možné z portálu exportovat jako soubory PFX, které se mají použít jinde.
Po importu se certifikáty App Service můžou **nacházet pod tajnými klíči**.

Postup pro export certifikátů služby App Service najdete v [tomto článku](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx) .

## <a name="read-more"></a>Další informace
* [Různé typy a definice souborů certifikátů](https://docs.microsoft.com/archive/blogs/kaushal/various-ssltls-certificate-file-typesextensions)