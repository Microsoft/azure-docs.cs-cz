---
title: Účet spravovaného úložiště Azure Key Vault – verze PowerShellu
description: Funkce účtu spravovaného úložiště poskytuje bezproblémovou integraci mezi Azure Key Vault a účtem úložiště Azure.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: cbd7bd034c5cbbdf5308ec660a96dc52a9a2b310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78200698"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Načtení tokenů sdíleného přístupového podpisu v kódu

Účet úložiště můžete spravovat pomocí [tokenů podpisu sdíleného přístupu](../storage/common/storage-dotnet-shared-access-signature-part-1.md) v trezoru klíčů. Tento článek obsahuje příklady kódu Jazyka C#, který načte token SAS a provádí s ním operace.  Informace o tom, jak vytvářet a ukládat tokeny SAS, najdete [v tématu Správa klíčů účtů úložiště pomocí trezoru klíčů klíčů a klíče](key-vault-ovw-storage-keys.md) účtu Azure CLI nebo Správa účtů úložiště pomocí [trezoru klíčů a Azure PowerShellu](key-vault-overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Ukázky kódů

V tomto příkladu kód načte token SAS z trezoru klíčů, použije jej k vytvoření nového účtu úložiště a vytvoří nového klienta služby Objektů blob.  

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

Pokud platnost sdíleného přístupového podpisového tokenu brzy vyprší, můžete načíst token sdíleného přístupového podpisu z trezoru klíčů a kód aktualizovat.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Další kroky
- Zjistěte, jak [spravovat klíče účtů úložiště pomocí trezoru klíčů a Azure CLI](key-vault-ovw-storage-keys.md) nebo Azure [PowerShellu](key-vault-overview-storage-keys-powershell.md).
- Viz [Ukázky klíčů účtu spravovaného úložiště](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Informace o klíčích, tajných kódech a certifikátech](about-keys-secrets-and-certificates.md)
- [Odkaz na prostředí PowerShell trezoru klíčů](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
