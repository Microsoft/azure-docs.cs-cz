---
title: Rychlý Start – Azure Key Vault knihovna klienta Pythonu – Správa certifikátů
description: Naučte se vytvářet, načítat a odstraňovat certifikáty z trezoru klíčů Azure pomocí klientské knihovny Pythonu.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 3/30/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: tracking-python
ms.openlocfilehash: abbe07d96daf5848d01cd1f4943d4969fe1e59d9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87043723"
---
# <a name="quickstart-azure-key-vault-certificates-client-library-for-python"></a>Rychlý Start: Klientská knihovna pro Azure Key Vault certifikáty pro Python

Začněte s knihovnou klienta Azure Key Vault pro Python. Postupujte podle následujících kroků a nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úlohy.

Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Použití klientské knihovny Key Vault pro Python pro:

- Zvyšte zabezpečení a kontrolu nad klíči a hesly.
- Vytvářejte a importujte šifrovací klíče během několika minut.
- Snižte latenci díky škálování cloudu a globální redundanci.
- Zjednodušte a automatizujte úlohy pro certifikáty TLS/SSL.
- Použijte ověřený HSM úrovně 2 FIPS 140-2.

[Referenční dokumentace k](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)  |  rozhraní API [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault)  |  [Balíček (index balíčku Pythonu)](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Python 2,7, 3.5.3 nebo novější
- Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) nebo [Azure PowerShell](/powershell/azure/)

V tomto rychlém startu se předpokládá, že používáte rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) v okně terminálu Linux.

## <a name="setting-up"></a>Nastavení

### <a name="install-the-package"></a>Instalace balíčku

V okně konzoly nainstalujte Azure Key Vault knihovna certifikátů pro Python.

```console
pip install azure-keyvault-certificates
```

Pro tento rychlý Start budete muset nainstalovat taky balíček Azure. identity:

```console
pip install azure.identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Vytvoření skupiny prostředků a trezoru klíčů

V tomto rychlém startu se používá předem vytvořený Trezor klíčů Azure. Trezor klíčů můžete vytvořit podle kroků v [rychlém startu Azure CLI](quick-create-cli.md), [Azure PowerShell rychlý Start](quick-create-powershell.md)nebo v [rychlém startu Azure Portal](quick-create-portal.md). Případně můžete spustit příkazy rozhraní příkazového řádku Azure CLI níže.

> [!Important]
> Každý Trezor klíčů musí mít jedinečný název. V následujících příkladech nahraďte <název trezoru klíčů jedinečných> s názvem vašeho trezoru klíčů.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Nejjednodušší způsob, jak ověřit cloudovou aplikaci .NET, je spravovaná identita; Podrobnosti najdete v tématu [použití spravované identity App Service pro přístup k Azure Key Vault](../general/managed-identity.md) .

V zájmu zjednodušení ale v tomto rychlém startu se vytvoří desktopová aplikace, která vyžaduje použití instančního objektu a zásad řízení přístupu. Váš instanční objekt vyžaduje jedinečný název ve formátu "http:// &lt; My-Unique-Service-Principal-Name &gt; ".

Vytvořte instanční objekt pomocí příkazu Azure CLI [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

```azurecli
az ad sp create-for-rbac -n "http://&lt;my-unique-service-principal-name&gt;" --sdk-auth
```

Tato operace vrátí řadu párů klíč/hodnota. 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Poznamenejte si clientId a clientSecret, jak je budeme používat v níže uvedeném kroku [nastavení proměnné prostředí](#set-environmental-variables) .

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Udělte instančnímu objektu přístup k vašemu trezoru klíčů.

Vytvořte zásady přístupu pro váš Trezor klíčů, který uděluje oprávnění vašemu instančnímu objektu předáním příkazu [AZ klíčového trezoru set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) . Udělte instančnímu objektu oprávnění získat, vypsat a vytvořit pro certifikáty.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --certificate-permissions delete get list create 
```

#### <a name="set-environmental-variables"></a>Nastavení proměnných prostředí

Metoda DefaultAzureCredential v naší aplikaci spoléhá na tři proměnné prostředí: `AZURE_CLIENT_ID` , `AZURE_CLIENT_SECRET` a `AZURE_TENANT_ID` . Nastavte tyto proměnné na hodnoty clientId, clientSecret a tenantId, které jste si poznamenali v kroku [Vytvoření instančního objektu](#create-a-service-principal) pomocí `export VARNAME=VALUE` formátu. (Tato metoda nastavuje jenom proměnné pro vaše aktuální prostředí a procesy vytvořené z prostředí. Pokud chcete tyto proměnné do svého prostředí přidat trvale, upravte `/etc/environment ` soubor.) 

Název trezoru klíčů si taky budete muset uložit jako proměnnou prostředí s názvem `KEY_VAULT_NAME` .

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Objektový model

Klientská knihovna Azure Key Vault pro Python umožňuje správu klíčů a souvisejících prostředků, jako jsou certifikáty a tajné klíče. Následující ukázky kódu vám ukážou, jak vytvořit klienta, vytvořit certifikát, načíst certifikát a odstranit certifikát.

## <a name="code-examples"></a>Příklady kódu

### <a name="add-directives"></a>Přidat direktivy

Do horní části kódu přidejte následující direktivy:

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Ověření a vytvoření klienta

Ověřování pro váš Trezor klíčů a vytvoření klienta trezoru klíčů závisí na proměnných prostředí v kroku [nastavit proměnné prostředí](#set-environmental-variables) výše. Název trezoru klíčů se rozšíří na identifikátor URI trezoru klíčů ve formátu "https://<your-key-trezor-Name>. vault.azure.net".

```python
credential = DefaultAzureCredential()

client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Uložení certifikátu

Teď, když je vaše aplikace ověřená, můžete do trezoru klíčů vložit certifikát podepsaný svým držitelem. 

```python
certificate_operation_poller = client.begin_create_certificate(
    certificate_name=certificateName, policy=CertificatePolicy.get_default()
)
certificate = certificate_operation_poller.result()
```

Můžete ověřit, že se certifikát nastavil pomocí příkazu [AZ datatrezor Certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show) :

```azurecli
az keyvault certificate show --vault-name <your-unique-keyvault-name> --name myCertificate
```

### <a name="retrieve-a-certificate"></a>Načtení certifikátu

Teď si můžete načíst dřív vytvořený certifikát.

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

Certifikát je teď uložený jako `retrieved_certificate` .

### <a name="delete-a-certificate"></a>Odstranit certifikát

Nakonec odstraňte certifikát z vašeho trezoru klíčů.

```python
client.delete_certificate(certificateName)
```

Můžete ověřit, že certifikát je pryč pomocí příkazu [AZ datatrezor Certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show) :

```azurecli
az keyvault certifcate show --vault-name <your-unique-keyvault-name> --name myCertificate
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání trezoru klíčů a odpovídající skupiny prostředků použít Azure CLI nebo Azure PowerShell.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Ukázka kódu

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)

certificateName = "myCertificate"

print("Creating a certificate in " + keyVaultName + " called '" + certificateName  + "` ...")

certificate_operation_poller = client.begin_create_certificate(
    certificate_name=certificateName, policy=CertificatePolicy.get_default()

certificate = certificate_operation_poller.result()

print(" done.")

print("Retrieving your certificate from " + keyVaultName + ".")

retrieved_certificate = client.get_certificate(certificateName)

print("Certificate with name '{0}' was found'.".format(retrieved_certificate.name))
print("Deleting your certificate from " + keyVaultName + " ...")

client.delete_certificate(certificateName)

print(" done.")
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Trezor klíčů, uložili certifikát a načetli jste tento certifikát. Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete dál v článcích níže.

- Přečtěte si [přehled Azure Key Vault](../general/overview.md)
- Další informace najdete v [příručce pro vývojáře Azure Key Vault](../general/developers-guide.md) .
- Kontrola [Azure Key Vault osvědčených postupů](../general/best-practices.md)
