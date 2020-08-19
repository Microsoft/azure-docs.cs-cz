---
title: Načíst tokeny sdíleného přístupového podpisu v kódu | Azure Key Vault
description: Funkce účet spravovaného úložiště poskytuje bezproblémovou integraci mezi Azure Key Vault a účtem služby Azure Storage.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 6530434e36f7c9a9a60a9782bcf2dce7ba447dab
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88584909"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Načtení tokenů sdíleného přístupového podpisu v kódu

Svůj účet úložiště můžete spravovat pomocí tokenů sdíleného přístupového podpisu (SAS) uložených v trezoru klíčů. Další informace najdete v tématu [udělení omezeného přístupu k prostředkům Azure Storage pomocí SAS](../../storage/common/storage-sas-overview.md).

Tento článek poskytuje příklady kódu .NET, který načte token SAS a provádí operace s ním. Informace o tom, jak vytvářet a ukládat tokeny SAS, najdete v tématech [Správa klíčů účtu úložiště pomocí Key Vault a Azure CLI](overview-storage-keys.md) nebo [Správa klíčů účtu úložiště pomocí Key Vault a Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Ukázky kódů

V tomto příkladu kód načte token SAS z vašeho trezoru klíčů, použije ho k vytvoření nového účtu úložiště a vytvoří nového klienta Blob service.

```cs
// The shared access signature is stored as a secret in keyvault. 
// After you get a security token, create a new SecretClient with vault credentials and the key vault URI.
// The format for the key vault URI (kvuri) is https://<YourKeyVaultName>.vault.azure.net

var kv = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

// Now retrive your storage SAS token from Key Vault using the name of the secret (secretName).

KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;

// Create new storage credentials using the SAS token.
StorageCredentials accountSAS = new StorageCredentials(sasToken);

// Use these credentials and your storage account name to create a Blob service client.
CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();
```

Pokud se brzo vyprší platnost tokenu sdíleného přístupového podpisu, můžete načíst token sdíleného přístupového podpisu z trezoru klíčů a kód aktualizovat.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;
accountSAS.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Další kroky
- Přečtěte si, jak [udělit omezený přístup k prostředkům Azure Storage pomocí SAS](../../storage/common/storage-sas-overview.md).
- Naučte se [spravovat klíče účtu úložiště pomocí Key Vault a Azure CLI](overview-storage-keys.md) nebo [Azure PowerShell](overview-storage-keys-powershell.md).
- Viz [ukázky klíčů spravovaného účtu úložiště](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=) .
