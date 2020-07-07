---
title: Azure Key Vault spravovaného účtu úložiště – verze PowerShellu
description: Funkce účet spravovaného úložiště poskytuje bezproblémovou integraci mezi Azure Key Vault a účtem služby Azure Storage.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 7307741e56c7fc912f60d0496979243eb4be77a4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "81431264"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Načtení tokenů sdíleného přístupového podpisu v kódu

V trezoru klíčů můžete spravovat svůj účet úložiště pomocí [tokenů sdíleného přístupového podpisu](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) . Tento článek poskytuje příklady kódu jazyka C#, který načte token SAS a provádí operace s ním.  Informace o tom, jak vytvářet a ukládat tokeny SAS, najdete v tématech [Správa klíčů účtu úložiště pomocí Key Vault a Azure CLI](overview-storage-keys.md) nebo [Správa klíčů účtu úložiště pomocí Key Vault a Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Ukázky kódů

V tomto příkladu kód načte token SAS z vašeho trezoru klíčů, použije ho k vytvoření nového účtu úložiště a vytvoří nového klienta Blob service.  

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<YourKeyVaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Pokud se brzo vyprší platnost tokenu sdíleného přístupového podpisu, můžete načíst token sdíleného přístupového podpisu z trezoru klíčů a kód aktualizovat.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Další kroky
- Naučte se [spravovat klíče účtu úložiště pomocí Key Vault a Azure CLI](overview-storage-keys.md) nebo [Azure PowerShell](overview-storage-keys-powershell.md).
- Viz [ukázky klíčů spravovaného účtu úložiště](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=) .
- [Reference k Key Vault PowerShellu](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
