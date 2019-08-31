---
title: Přihlašovací údaje k zabezpečenému ukládání
titleSuffix: Azure Data Science Virtual Machine
description: Zjistěte, jak bezpečně ukládat přihlašovací údaje pro přístup na virtuální počítač pro datové vědy. Naučíte se používat identity spravované služby a Azure Key Vault k ukládání přihlašovacích údajů pro přístup.
keywords: obsáhlý learning, AI, nástrojů pro datové vědy, virtuální počítač pro datové vědy, geoprostorové analýzy, vědecké zpracování týmových dat
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 17e611007d2b5400497597946159826df7aa4848
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195615"
---
# <a name="store-access-credentials-securely-on-an-azure-data-science-virtual-machine"></a>Bezpečné ukládání přihlašovacích údajů pro přístup k Data Science Virtual Machine Azure

Pro kód v cloudových aplikacích je běžné, že obsahuje přihlašovací údaje pro ověřování ve službě Cloud Services. Postup při správě a zabezpečení těchto přihlašovacích údajů je dobře známou výzvou při vytváření cloudových aplikací. V ideálním případě by se přihlašovací údaje neměly nikdy zobrazovat na pracovních stanicích pro vývojáře nebo se vrátit se změnami do správy zdrojového kódu.

Funkce [spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) usnadňuje řešení tohoto problému tím, že poskytuje službám Azure automaticky spravovanou identitu ve službě Azure Active Directory (Azure AD). Tuto identitu můžete použít k ověření pro jakoukoli službu, která podporuje ověřování Azure AD, aniž byste ve vašem kódu museli mít přihlašovací údaje.

Jedním ze způsobů zabezpečení přihlašovacích údajů je použití Instalační služba systému Windows (MSI) v kombinaci s [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/), spravovaná služba Azure pro bezpečné ukládání tajných klíčů a kryptografických klíčů. K trezoru klíčů můžete přistupovat pomocí spravované identity a pak z trezoru klíčů načíst autorizované tajné klíče a kryptografické klíče.

Dokumentace ke spravovaným identitám pro prostředky Azure a Key Vault obsahuje ucelený prostředek pro podrobné informace o těchto službách. Zbývající část tohoto článku vás provede základní použití Instalační služby MSI a Key Vault na Data Science virtuálního počítače (datové VĚDY) přístup k prostředkům Azure. 

## <a name="create-a-managed-identity-on-the-dsvm"></a>Vytvořte spravovanou identitu na datové VĚDY 


```
# Prerequisite: You have already created a Data Science VM in the usual way.

# Create an identity principal for the VM.
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal ID of the DSVM.
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```


## <a name="assign-key-vault-access-permissions-to-a-vm-principal"></a>Přiřazení přístupových oprávnění Key Vault k objektu zabezpečení virtuálního počítače
```
# Prerequisite: You have already created an empty Key Vault resource on Azure by using the Azure portal or Azure CLI.

# Assign only get and set permissions but not the capability to list the keys.
az keyvault set-policy --object-id <Principal ID of the DSVM from previous step> --name <Key Vault Name> -g <Resource Group of Key Vault>  --secret-permissions get set
```

## <a name="access-a-secret-in-the-key-vault-from-the-dsvm"></a>Přístup k tajným kódem v trezoru klíčů z datové VĚDY

```
# Get the access token for the VM.
x=`curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true`
token=`echo $x | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`

# Access the key vault by using the access token.
curl https://<Vault Name>.vault.azure.net/secrets/SQLPasswd?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

## <a name="access-storage-keys-from-the-dsvm"></a>Přístupové klíče úložiště z datové VĚDY

```
# Prerequisite: You have granted your VMs MSI access to use storage account access keys based on instructions at https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage. This article describes the process in more detail.

y=`curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true`
ytoken=`echo $y | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`
curl https://management.azure.com/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup of Storage account>/providers/Microsoft.Storage/storageAccounts/<Storage Account Name>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer $ytoken"

# Now you can access the data in the storage account from the retrieved storage account keys.
```
## <a name="access-the-key-vault-from-python"></a>Přístup k trezoru klíčů z Pythonu

```python
from azure.keyvault import KeyVaultClient
from msrestazure.azure_active_directory import MSIAuthentication

"""MSI Authentication example."""

# Get credentials.
credentials = MSIAuthentication(
    resource='https://vault.azure.net'
)

# Create a Key Vault client.
key_vault_client = KeyVaultClient(
    credentials
)

key_vault_uri = "https://<key Vault Name>.vault.azure.net/"

secret = key_vault_client.get_secret(
    key_vault_uri,  # Your key vault URL.
    # The name of your secret that already exists in the key vault.
    "SQLPasswd",
    ""              # The version of the secret; empty string for latest.
)
print("My secret value is {}".format(secret.value))
```

## <a name="access-the-key-vault-from-azure-cli"></a>Přístup k trezoru klíčů z příkazového řádku Azure

```
# With managed identities for Azure resources set up on the DSVM, users on the DSVM can use Azure CLI to perform the authorized functions. The following commands enable access to the key vault from Azure CLI without requiring login to an Azure account.
# Prerequisites: MSI is already set up on the DSVM as indicated earlier. Specific permissions, like accessing storage account keys, reading specific secrets, and writing new secrets, are provided to the MSI.

# Authenticate to Azure CLI without requiring an Azure account. 
az login --msi

# Retrieve a secret from the key vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new secret in the key vault.
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List access keys for the storage account.
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```
