---
title: Přihlašovací údaje k zabezpečenému ukládání
titleSuffix: Azure Data Science Virtual Machine
description: Přečtěte si, jak bezpečně ukládat přihlašovací údaje pro přístup k Data Science Virtual Machine. Naučíte se používat identity spravované služby a Azure Key Vault k ukládání přihlašovacích údajů pro přístup.
keywords: obsáhlý Learning, AI, nástroje pro datové vědy, virtuální počítač pro datové vědy, geoprostorové analýzy, vědecké zpracování týmových dat
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: adc66fc794ac6cce3fc0f8fd1552f701a4968860
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519673"
---
# <a name="store-access-credentials-securely-on-an-azure-data-science-virtual-machine"></a>Bezpečné ukládání přihlašovacích údajů pro přístup k Data Science Virtual Machine Azure

Pro kód v cloudových aplikacích je běžné, že obsahuje přihlašovací údaje pro ověřování ve službě Cloud Services. Postup při správě a zabezpečení těchto přihlašovacích údajů je dobře známou výzvou při vytváření cloudových aplikací. V ideálním případě by se přihlašovací údaje neměly nikdy zobrazovat na pracovních stanicích pro vývojáře nebo se vrátit se změnami do správy zdrojového kódu.

Funkce [spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md) usnadňuje řešení tohoto problému tím, že poskytuje službám Azure automaticky spravovanou identitu ve službě Azure Active Directory (Azure AD). Tuto identitu můžete použít k ověření pro jakoukoli službu, která podporuje ověřování Azure AD, aniž byste ve vašem kódu museli mít přihlašovací údaje.

Jedním ze způsobů zabezpečení přihlašovacích údajů je použití Instalační služba systému Windows (MSI) v kombinaci s [Azure Key Vault](../../key-vault/index.yml), spravovaná služba Azure pro bezpečné ukládání tajných klíčů a kryptografických klíčů. K trezoru klíčů můžete přistupovat pomocí spravované identity a pak z trezoru klíčů načíst autorizované tajné klíče a kryptografické klíče.

Dokumentace ke spravovaným identitám pro prostředky Azure a Key Vault obsahuje ucelený prostředek pro podrobné informace o těchto službách. Zbývající část tohoto článku vás provede základními použití MSI a Key Vault na Data Science Virtual Machine (DSVM) pro přístup k prostředkům Azure. 

## <a name="create-a-managed-identity-on-the-dsvm"></a>Vytvoření spravované identity na DSVM

```azurecli-interactive
# Prerequisite: You have already created a Data Science VM in the usual way.

# Create an identity principal for the VM.
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal ID of the DSVM.
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```

## <a name="assign-key-vault-access-permissions-to-a-vm-principal"></a>Přiřazení přístupových oprávnění Key Vault k objektu zabezpečení virtuálního počítače

```azurecli-interactive
# Prerequisite: You have already created an empty Key Vault resource on Azure by using the Azure portal or Azure CLI.

# Assign only get and set permissions but not the capability to list the keys.
az keyvault set-policy --object-id <Principal ID of the DSVM from previous step> --name <Key Vault Name> -g <Resource Group of Key Vault>  --secret-permissions get set
```

## <a name="access-a-secret-in-the-key-vault-from-the-dsvm"></a>Přístup ke tajným klíčům v trezoru klíčů z DSVM

```bash
# Get the access token for the VM.
x=`curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true`
token=`echo $x | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`

# Access the key vault by using the access token.
curl https://<Vault Name>.vault.azure.net/secrets/SQLPasswd?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

## <a name="access-storage-keys-from-the-dsvm"></a>Přístup k klíčům úložiště z DSVM

```bash
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

## <a name="access-the-key-vault-from-azure-cli"></a>Přístup k trezoru klíčů z Azure CLI

```azurecli-interactive
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