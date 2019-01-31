---
title: Store zabezpečený přístup k přihlašovací údaje na virtuální počítač pro datové vědy – Azure | Dokumentace Microsoftu
description: Zjistěte, jak bezpečně ukládat přihlašovací údaje pro přístup na virtuální počítač pro datové vědy. Se dozvíte, jak ukládat přihlašovací údaje pro přístup pomocí identity spravované služby a služby Azure Key Vault.
keywords: obsáhlý learning, AI, nástrojů pro datové vědy, virtuální počítač pro datové vědy, geoprostorové analýzy, vědecké zpracování týmových dat
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 79dba586a5f7102d0012c381593551a951f1b38e
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55451288"
---
# <a name="store-access-credentials-on-the-data-science-virtual-machine-securely"></a>Store přístup k zabezpečené přihlašovací údaje na virtuální počítač pro datové vědy

Běžné výzvy při vytváření cloudových aplikací se správa přihlašovacích údajů, které musí být ve vašem kódu za účelem ověřování totožnosti ke cloudovým službám. Zajištění zabezpečení těchto přihlašovacích údajů je důležitý úkol. V ideálním případě by se nikdy objeví na vývojářské pracovní stanice nebo získat vráceny se změnami do správy zdrojového kódu. 

[Spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) usnadňuje řešení tohoto problému jednodušší tím, že Azure services automaticky spravovanou identitu ve službě Azure Active Directory (Azure AD). Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo žádné přihlašovací údaje ve vašem kódu. 

Jedním ze způsobů zabezpečení přihlašovacích údajů je použití Instalační služby MSI v kombinaci s [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/), spravované služby Azure k bezpečnému ukládání kryptografických klíčů nebo tajných kódů. Můžete přistupovat k trezoru klíčů pomocí spravované identity a načtení autorizované tajných kódů a kryptografických klíčů z trezoru klíčů. 

Spravované identity pro prostředky Azure a dokumentace ke službě Key Vault je důležitý zdroj pro podrobné informace o těchto službách. Zbývající část tohoto článku vás provede základní použití Instalační služby MSI a Key Vault na Data Science virtuálního počítače (datové VĚDY) přístup k prostředkům Azure. 

## <a name="create-a-managed-identity-on-the-dsvm"></a>Vytvořte spravovanou identitu na datové VĚDY 


```
# Prerequisite: You have already created a Data Science VM in the usual way.

# Create an identity principal for the VM.
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal ID of the DSVM.
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```


## <a name="assign-key-vault-access-permission-to-a-vm-principal"></a>Přiřazení oprávnění služby Key Vault k instančnímu objektu VM
```
# Prerequisite: You have already created an empty Key Vault resource on Azure by using the Azure portal or Azure CLI. 

# Assign only get and set permission but not the capability to list the keys.
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
# Prerequisite: You have granted your VM's MSI access to use storage account access keys based on instructions from the article at https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage. This article describes the process in more detail.

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
"SQLPasswd",       # The name of your secret that already exists in the key vault.
""              # The version of the secret; empty string for latest.
)
print("My secret value is {}".format(secret.value))
```

## <a name="access-the-key-vault-from-azure-cli"></a>Přístup k trezoru klíčů z příkazového řádku Azure

```
# With managed identities for Azure resources set up on the DSVM, users on the DSVM can use Azure CLI to perform the authorized functions. Here are commands to access the key vault from Azure CLI without having to log in to an Azure account. 
# Prerequisites: MSI is already set up on the DSVM as indicated earlier. Specific permission, like accessing storage account keys, reading specific secrets, and writing new secrets, is provided to the MSI. 

# Authenticate to Azure CLI without requiring an Azure account. 
az login --msi

# Retrieve a secret from the key vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new secret in the key vault.
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List access keys for the storage account.
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```
