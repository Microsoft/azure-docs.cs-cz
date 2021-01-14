---
title: Vytvoření SAS pro delegování uživatelů pro kontejner nebo objekt BLOB pomocí .NET
titleSuffix: Azure Storage
description: Naučte se vytvářet delegování uživatelů pomocí Azure Active Directory přihlašovacích údajů pomocí klientské knihovny .NET pro Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/13/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 512b949fceda850e968a6f97b3788ae3a602f56d
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98199253"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net"></a>Vytvoření SAS pro delegování uživatelů pro kontejner nebo objekt BLOB pomocí .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

V tomto článku se dozvíte, jak používat přihlašovací údaje pro Azure Active Directory (Azure AD) k vytvoření SAS pro delegování uživatelů pro kontejner nebo objekt BLOB pomocí Azure Storage klientské knihovny pro .NET verze 12.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-azure-roles-for-access-to-data"></a>Přiřazení rolí Azure pro přístup k datům

Když se objekt zabezpečení služby Azure AD pokusí získat přístup k datům objektu blob, musí mít tento objekt zabezpečení oprávnění k prostředku. Bez ohledu na to, jestli je objekt zabezpečení spravovaná identita v Azure nebo uživatelský účet Azure AD, který spouští kód ve vývojovém prostředí, musí být objektu zabezpečení přiřazená role Azure, která uděluje přístup k datům objektu BLOB v Azure Storage. Informace o přiřazování oprávnění přes Azure RBAC najdete v části **přiřazení rolí Azure pro přístupová práva** v tématu [autorizace přístupu k objektům blob a frontám Azure pomocí Azure Active Directory](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights).

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Další informace o tom, jak ověřit pomocí klientské knihovny Azure identity z Azure Storage, najdete v části s názvem **ověřování pomocí knihovny identit Azure** v tématu [autorizace přístupu k objektům blob a frontám pomocí Azure Active Directory a spravovaných identit pro prostředky Azure](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).

## <a name="get-an-authenticated-token-credential"></a>Získání přihlašovacích údajů ověřeného tokenu

Chcete-li získat přihlašovací údaje tokenu, které může váš kód použít k autorizaci požadavků na Azure Storage, vytvořte instanci třídy [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) .

Následující fragment kódu ukazuje, jak získat pověření ověřeného tokenu a použít ho k vytvoření klienta služby pro úložiště objektů BLOB:

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>Získat klíč delegování uživatele

Každé přidružení zabezpečení je podepsáno klíčem. Chcete-li vytvořit SAS delegování uživatele, je třeba nejprve požádat o klíč pro delegování uživatele, který je pak použit k podepsání SAS. Klíč pro delegování uživatelů je podobný klíči účtu, který se používá k podepsání SAS služby nebo k přidružení zabezpečení účtu, s tím rozdílem, že spoléhá na vaše přihlašovací údaje Azure AD. Když klient požádá o klíč delegování uživatele pomocí tokenu OAuth 2,0, Azure Storage vrátí klíč delegování uživatele jménem uživatele.

Po získání klíče pro delegování uživatelů můžete pomocí tohoto klíče vytvořit libovolný počet sdílených přístupových podpisů uživatelů po celou dobu životnosti klíče. Klíč pro delegování uživatelů je nezávislý na tokenu OAuth 2,0, který se používá k získání, takže token není nutné obnovit, pokud je klíč stále platný. Můžete určit, že klíč je platný po dobu až 7 dnů.

Pro vyžádání klíče delegování uživateli použijte jednu z následujících metod:

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

Následující fragment kódu získá klíč delegování uživatele a zapíše jeho vlastnosti:

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="get-a-user-delegation-sas-for-a-blob"></a>Získání SAS pro delegování uživatelů pro objekt BLOB

Následující příklad kódu ukazuje úplný kód pro ověření objektu zabezpečení a vytvoření SAS pro delegování uživatele pro objekt BLOB:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasBlob":::

Následující příklad testuje SAS uživatele z simulované klientské aplikace vytvořené v předchozím příkladu. Pokud je podpis SAS platný, klientská aplikace může číst obsah objektu BLOB. Pokud je SAS neplatné, například pokud platnost vypršela, Azure Storage vrátí kód chyby 403 (zakázáno).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ReadBlobWithSasAsync":::

## <a name="get-a-user-delegation-sas-for-a-container"></a>Získání SAS pro delegování uživatele pro kontejner

Následující příklad kódu ukazuje, jak vygenerovat přidružení zabezpečení delegování uživatelů pro kontejner:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasContainer":::

Následující příklad testuje SAS uživatele z simulované klientské aplikace vytvořené v předchozím příkladu. Pokud je podpis SAS platný, klientská aplikace může číst obsah objektu BLOB. Pokud je SAS neplatné, například pokud platnost vypršela, Azure Storage vrátí kód chyby 403 (zakázáno).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ListBlobsWithSasAsync":::

## <a name="get-a-user-delegation-sas-for-a-directory"></a>Získání SAS uživatele k delegování pro adresář

Následující příklad kódu ukazuje, jak vygenerovat přidružení zabezpečení delegování uživatelů pro adresář, když je povolen hierarchický obor názvů:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasDirectory":::

Následující příklad testuje SAS uživatele z simulované klientské aplikace vytvořené v předchozím příkladu. Pokud je podpis SAS platný, může klientská aplikace zobrazit seznam cest k souborům pro tento adresář. Pokud je SAS neplatné, například pokud platnost vypršela, Azure Storage vrátí kód chyby 403 (zakázáno).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ListFilePathsWithDirectorySasAsync":::

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Viz také

- [Udělení omezeného přístupu k prostředkům Azure Storage pomocí sdílených přístupových podpisů (SAS)](../common/storage-sas-overview.md)
- [Získat operaci klíče delegování uživatele](/rest/api/storageservices/get-user-delegation-key)
- [Vytvoření SAS delegování uživatele (REST API)](/rest/api/storageservices/create-user-delegation-sas)
