---
title: Autorizace přístupu k objektům blob a frontám pomocí Azure Active Directory a spravovaných identit pro prostředky Azure – Azure Storage
description: Azure Blob a Queue Storage podporují autorizaci přístupu k prostředkům pomocí Azure Active Directory a spravovaných identit pro prostředky Azure. Spravované identity pro prostředky Azure můžete použít k autorizaci přístupu k objektům blob a frontám z aplikací běžících na virtuálních počítačích Azure, aplikací Functions, Virtual Machine Scale Sets a dalších.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: d77ab142e227cfaa6533395cc256d992e698dd17
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495920"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Autorizace přístupu k objektům blob a frontám pomocí Azure Active Directory a spravovaných identit pro prostředky Azure

Azure Blob a Queue Storage podporují ověřování Azure Active Directory (Azure AD) se [spravovanými identitami pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md). Spravované identity pro prostředky Azure můžou autorizovat přístup k objektům blob a Queue pomocí přihlašovacích údajů Azure AD z aplikací běžících na virtuálních počítačích Azure, aplikací Functions, Virtual Machine Scale Sets a dalších služeb. Pomocí spravovaných identit pro prostředky Azure spolu s ověřováním Azure AD se můžete vyhnout ukládání přihlašovacích údajů k vašim aplikacím, které běží v cloudu.  

Tento článek popisuje, jak autorizovat přístup k datům objektů BLOB nebo front z virtuálního počítače Azure pomocí spravovaných identit pro prostředky Azure. Také popisuje, jak testovat kód ve vývojovém prostředí.

## <a name="enable-managed-identities-on-a-vm"></a>Povolení spravovaných identit na virtuálním počítači

Než budete moct pomocí spravovaných identit pro prostředky Azure autorizovat přístup k objektům blob a frontám z virtuálního počítače, musíte nejdřív na VIRTUÁLNÍm počítači povolit spravované identity pro prostředky Azure. Informace o tom, jak povolit spravované identity pro prostředky Azure, najdete v jednom z těchto článků:

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Šablona Azure Resource Manageru](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Klientské knihovny Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Další informace o spravovaných identitách najdete v tématu [spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="authenticate-with-the-azure-identity-library-preview"></a>Ověřování pomocí knihovny identit Azure (Preview)

Klientská knihovna Azure identity pro .NET (Preview) ověřuje objekt zabezpečení. Když váš kód běží v Azure, je objekt zabezpečení spravovaná identita pro prostředky Azure.

Když váš kód běží ve vývojovém prostředí, ověřování může být zpracováno automaticky nebo může vyžadovat přihlášení prohlížeče v závislosti na tom, které nástroje používáte. Microsoft Visual Studio podporuje jednotné přihlašování (SSO), aby se aktivní uživatelský účet Azure AD automaticky používal pro ověřování. Další informace o JEDNOTNÉm přihlašování najdete v tématu [jednotné přihlašování k aplikacím](../../active-directory/manage-apps/what-is-single-sign-on.md).

Jiné vývojové nástroje vás můžou vyzvat k přihlášení přes webový prohlížeč. K ověření z vývojového prostředí můžete také použít instanční objekt. Další informace najdete v tématu věnovaném [vytvoření identity pro aplikaci Azure na portálu](../../active-directory/develop/howto-create-service-principal-portal.md).

Po ověření získá Klientská knihovna identity Azure přihlašovací údaje tokenu. Tyto přihlašovací údaje tokenu se pak zapouzdřují v objektu klienta služby, který vytvoříte k provádění operací s Azure Storage. Knihovna to zvládne bez problémů získáním příslušných přihlašovacích údajů tokenu.

Další informace o klientské knihovně identit Azure najdete v tématu [Klientská knihovna Azure identity pro .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

## <a name="assign-rbac-roles-for-access-to-data"></a>Přiřazení rolí RBAC pro přístup k datům

Když se objekt zabezpečení služby Azure AD pokusí získat přístup k datům objektu BLOB nebo fronty, musí mít tento objekt zabezpečení oprávnění k prostředku. Bez ohledu na to, jestli je objekt zabezpečení spravovanou identitou v Azure nebo uživatelským účtem Azure AD, který spouští kód ve vývojovém prostředí, musí být objektu zabezpečení přiřazená role RBAC, která uděluje přístup k objektům blob nebo frontě v Azure Storage. Informace o přiřazování oprávnění přes RBAC najdete v části s názvem **přiřazení rolí RBAC pro přístupová práva** v tématu [autorizace přístupu k objektům blob a frontám Azure pomocí Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

## <a name="install-the-preview-packages"></a>Instalace balíčků verze Preview

Příklady v tomto článku využívají nejnovější verzi Preview klientské knihovny Azure Storage pro úložiště objektů BLOB. Chcete-li nainstalovat balíček verze Preview, spusťte následující příkaz z konzoly Správce balíčků NuGet:

```powershell
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

Příklady v tomto článku také využívají nejnovější verzi Preview [klientské knihovny Azure identity pro .NET](https://www.nuget.org/packages/Azure.Identity/) k ověřování pomocí přihlašovacích údajů Azure AD. Chcete-li nainstalovat balíček verze Preview, spusťte následující příkaz z konzoly Správce balíčků NuGet:

```powershell
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="net-code-example-create-a-block-blob"></a>Příklad kódu .NET: vytvoření objektu blob bloku

Do kódu přidejte následující direktivy `using`, abyste mohli používat verze Preview klientských knihoven identit Azure a Azure Storage.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure.Identity;
using Azure.Storage;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

Chcete-li získat přihlašovací údaje tokenu, které může váš kód použít k autorizaci požadavků na Azure Storage, vytvořte instanci třídy [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) . Následující příklad kódu ukazuje, jak získat pověření ověřeného tokenu a použít ho k vytvoření objektu klienta služby. potom pomocí klienta služby Nahrajte nový objekt BLOB:

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (StorageRequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Pokud chcete autorizovat požadavky na data objektů BLOB nebo front pomocí Azure AD, musíte pro tyto požadavky použít protokol HTTPS.

## <a name="next-steps"></a>Další kroky

- Další informace o rolích RBAC pro Azure Storage najdete v tématu [Správa přístupových práv k datům úložiště pomocí RBAC](storage-auth-aad-rbac.md).
- Informace o tom, jak autorizovat přístup k kontejnerům a frontám v rámci aplikací úložiště, najdete v tématu [použití Azure AD s aplikacemi úložiště](storage-auth-aad-app.md).
- Pokud chcete zjistit, jak spustit příkazy Azure CLI a PowerShell pomocí přihlašovacích údajů Azure AD, přečtěte si téma [spuštění příkazů Azure CLI nebo PowerShellu s přihlašovacími údaji Azure AD pro přístup k datům BLOB nebo Queue](storage-auth-aad-script.md).
