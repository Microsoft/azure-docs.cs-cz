---
title: Autorizace přístupu k objektům blob a frontám pomocí Azure Active Directory a spravovaných identit pro prostředky Azure – Azure Storage
description: Azure Blob a Queue Storage podporují autorizaci přístupu k prostředkům pomocí Azure Active Directory a spravovaných identit pro prostředky Azure. Spravované identity pro prostředky Azure můžete použít k autorizaci přístupu k objektům blob a frontám z aplikací běžících na virtuálních počítačích Azure, aplikací Functions, Virtual Machine Scale Sets a dalších.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bed661873b195694c2fd9b30b1d98a3ecf1fc8a4
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671103"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Autorizace přístupu k objektům blob a frontám pomocí Azure Active Directory a spravovaných identit pro prostředky Azure

Azure Blob a Queue Storage podporují ověřování Azure Active Directory (Azure AD) se [spravovanými identitami pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md). Spravované identity pro prostředky Azure můžou autorizovat přístup k objektům blob a Queue pomocí přihlašovacích údajů Azure AD z aplikací běžících na virtuálních počítačích Azure, aplikací Functions, Virtual Machine Scale Sets a dalších služeb. Pomocí spravovaných identit pro prostředky Azure spolu s ověřováním Azure AD se můžete vyhnout ukládání přihlašovacích údajů k vašim aplikacím, které běží v cloudu.  

Tento článek popisuje, jak autorizovat přístup k datům objektů BLOB nebo front pomocí spravované identity z virtuálního počítače Azure.

## <a name="enable-managed-identities-on-a-vm"></a>Povolení spravovaných identit na virtuálním počítači

Než budete moct pomocí spravovaných identit pro prostředky Azure autorizovat přístup k objektům blob a frontám z virtuálního počítače, musíte nejdřív na VIRTUÁLNÍm počítači povolit spravované identity pro prostředky Azure. Informace o tom, jak povolit spravované identity pro prostředky Azure, najdete v jednom z těchto článků:

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Šablona Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Klientské knihovny Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-an-azure-ad-managed-identity"></a>Udělení oprávnění spravované identitě Azure AD

Pokud chcete autorizovat požadavek na objekt BLOB nebo Služba front ze spravované identity ve vaší aplikaci Azure Storage, nejdřív nakonfigurujte nastavení řízení přístupu na základě role (RBAC) pro tuto spravovanou identitu. Azure Storage definuje role RBAC, které zahrnují oprávnění pro data objektů BLOB a front. Když je role RBAC přiřazená ke spravované identitě, spravovaná identita jim udělí tato oprávnění k datům BLOB nebo Queue v příslušném oboru.

Další informace o přiřazování rolí RBAC najdete v jednom z následujících článků:

- [Udělení přístupu k datům front a objektů blob Azure s využitím RBAC na webu Azure Portal](storage-auth-aad-rbac-portal.md)
- [Udělení přístupu k datům front a objektů blob Azure s využitím RBAC pomocí Azure CLI](storage-auth-aad-rbac-cli.md)
- [Udělení přístupu k datům front a objektů blob Azure s využitím RBAC pomocí PowerShellu](storage-auth-aad-rbac-powershell.md)

## <a name="azure-storage-resource-id"></a>ID prostředku Azure Storage

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Příklad kódu .NET: Vytvoření objektu blob bloku

Příklad kódu ukazuje, jak získat token OAuth 2,0 z Azure AD a použít ho k autorizaci žádosti o vytvoření objektu blob bloku. Chcete-li získat tento příklad práce, postupujte podle kroků popsaných v předchozích částech.

[!INCLUDE [storage-app-auth-lib-include](../../../includes/storage-app-auth-lib-include.md)]

### <a name="add-the-callback-method"></a>Přidat metodu zpětného volání

Metoda zpětného volání kontroluje čas vypršení platnosti tokenu a obnoví ho podle potřeby:

```csharp
private static async Task<NewTokenAndFrequency> TokenRenewerAsync(Object state, CancellationToken cancellationToken)
{
    // Specify the resource ID for requesting Azure AD tokens for Azure Storage.
    // Note that you can also specify the root URI for your storage account as the resource ID.
    const string StorageResource = "https://storage.azure.com/";  

    // Use the same token provider to request a new token.
    var authResult = await ((AzureServiceTokenProvider)state).GetAuthenticationResultAsync(StorageResource);

    // Renew the token 5 minutes before it expires.
    var next = (authResult.ExpiresOn - DateTimeOffset.UtcNow) - TimeSpan.FromMinutes(5);
    if (next.Ticks < 0)
    {
        next = default(TimeSpan);
        Console.WriteLine("Renewing token...");
    }

    // Return the new token and the next refresh time.
    return new NewTokenAndFrequency(authResult.AccessToken, next);
}
```

### <a name="get-a-token-and-create-a-block-blob"></a>Získání tokenu a vytvoření objektu blob bloku

Knihovna ověřování aplikací poskytuje třídu **AzureServiceTokenProvider** . Instance této třídy může být předána zpětnému volání, které získá token, a poté token obnoví před jeho vypršením.

Následující příklad načte token a použije ho k vytvoření nového objektu BLOB a pak použije stejný token pro čtení objektu BLOB.

```csharp
const string blobName = "https://storagesamples.blob.core.windows.net/sample-container/blob1.txt";

// Get the initial access token and the interval at which to refresh it.
AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
var tokenAndFrequency = await TokenRenewerAsync(azureServiceTokenProvider,CancellationToken.None);

// Create storage credentials using the initial token, and connect the callback function
// to renew the token just before it expires
TokenCredential tokenCredential = new TokenCredential(tokenAndFrequency.Token,
                                                        TokenRenewerAsync,
                                                        azureServiceTokenProvider,
                                                        tokenAndFrequency.Frequency.Value);

StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a blob using the storage credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName),
                                            storageCredentials);

// Upload text to the blob.
await blob.UploadTextAsync(string.Format("This is a blob named {0}", blob.Name));

// Continue to make requests against Azure Storage.
// The token is automatically refreshed as needed in the background.
do
{
    // Read blob contents
    Console.WriteLine("Time accessed: {0} Blob Content: {1}",
                        DateTimeOffset.UtcNow,
                        await blob.DownloadTextAsync());

    // Sleep for ten seconds, then read the contents of the blob again.
    Thread.Sleep(TimeSpan.FromSeconds(10));
} while (true);
```

Další informace o knihovně ověřování aplikací najdete v tématu [ověřování služby-služba pro Azure Key Vault pomocí rozhraní .NET](../../key-vault/service-to-service-authentication.md).

Další informace o tom, jak získat přístupový token, najdete v tématu [Jak používat spravované identity pro prostředky Azure na virtuálním počítači Azure k získání přístupového tokenu](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

> [!NOTE]
> Pokud chcete autorizovat požadavky na data objektů BLOB nebo front pomocí Azure AD, musíte pro tyto požadavky použít protokol HTTPS.

## <a name="next-steps"></a>Další kroky

- Další informace o rolích RBAC pro Azure Storage najdete v tématu [Správa přístupových práv k datům úložiště pomocí RBAC](storage-auth-aad-rbac.md).
- Informace o tom, jak autorizovat přístup k kontejnerům a frontám v rámci aplikací úložiště, najdete v tématu [použití Azure AD s aplikacemi úložiště](storage-auth-aad-app.md).
- Pokud chcete zjistit, jak spustit příkazy Azure CLI a PowerShell pomocí přihlašovacích údajů Azure AD, přečtěte si téma [spuštění příkazů Azure CLI nebo PowerShellu s přihlašovacími údaji Azure AD pro přístup k datům BLOB nebo Queue](storage-auth-aad-script.md).
