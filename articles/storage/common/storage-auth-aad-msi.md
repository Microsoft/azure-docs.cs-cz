---
title: Autorizace přístupu k datům pomocí spravované identity
titleSuffix: Azure Storage
description: Pomocí spravovaných identit pro prostředky Azure můžete autorizovat přístup k objektům blob a frontám z aplikací běžících na virtuálních počítačích Azure, aplikacích Function App a dalších.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 16ac28e46e9231918f433c0e9eb077cb0d8cf230
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791167"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Autorizace přístupu k datům BLOB a Queue pomocí spravovaných identit pro prostředky Azure

Azure Blob a Queue Storage podporují ověřování Azure Active Directory (Azure AD) se [spravovanými identitami pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md). Spravované identity pro prostředky Azure můžou autorizovat přístup k objektům blob a Queue pomocí přihlašovacích údajů Azure AD z aplikací běžících na virtuálních počítačích Azure, aplikací Functions, Virtual Machine Scale Sets a dalších služeb. Pomocí spravovaných identit pro prostředky Azure spolu s ověřováním Azure AD se můžete vyhnout ukládání přihlašovacích údajů k vašim aplikacím, které běží v cloudu.  

Tento článek popisuje, jak autorizovat přístup k datům objektů BLOB nebo front z virtuálního počítače Azure pomocí spravovaných identit pro prostředky Azure. Také popisuje, jak testovat kód ve vývojovém prostředí.

## <a name="enable-managed-identities-on-a-vm"></a>Povolení spravovaných identit na virtuálním počítači

Než budete moct pomocí spravovaných identit pro prostředky Azure autorizovat přístup k objektům blob a frontám z virtuálního počítače, musíte nejdřív na VIRTUÁLNÍm počítači povolit spravované identity pro prostředky Azure. Informace o tom, jak povolit spravované identity pro prostředky Azure, najdete v jednom z těchto článků:

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Šablona Azure Resource Manageru](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Klientské knihovny Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Další informace o spravovaných identitách najdete v tématu [spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="authenticate-with-the-azure-identity-library"></a>Ověřování pomocí knihovny identit Azure

Klientská knihovna Azure identity poskytuje podporu ověřování tokenů Azure AD pro [sadu Azure SDK](https://github.com/Azure/azure-sdk). Nejnovější verze Azure Storage klientských knihoven pro .NET, Java, Python a JavaScript se integrují s knihovnou identit Azure, aby poskytovaly jednoduché a zabezpečené prostředky pro získání tokenu OAuth 2,0 pro autorizaci žádostí Azure Storage.

Výhodou klientské knihovny Azure identity je to, že umožňuje použít stejný kód k ověření, jestli vaše aplikace běží ve vývojovém prostředí nebo v Azure. Klientská knihovna Azure identity pro .NET ověřuje objekt zabezpečení. Když váš kód běží v Azure, je objekt zabezpečení spravovaná identita pro prostředky Azure. Ve vývojovém prostředí neexistuje spravovaná identita, takže Klientská knihovna ověřuje pro účely testování buď uživatele, nebo instanční objekt.

Po ověření získá Klientská knihovna identity Azure přihlašovací údaje tokenu. Tyto přihlašovací údaje tokenu se pak zapouzdřují v objektu klienta služby, který vytvoříte k provádění operací s Azure Storage. Knihovna to zvládne bez problémů získáním příslušných přihlašovacích údajů tokenu.

Další informace o klientské knihovně Azure identity pro .NET najdete v tématu [Klientská knihovna Azure identity pro .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity). Referenční dokumentaci ke klientské knihovně identit Azure najdete v tématu [obor názvů Azure. identity](/dotnet/api/azure.identity).

### <a name="assign-azure-roles-for-access-to-data"></a>Přiřazení rolí Azure pro přístup k datům

Když se objekt zabezpečení služby Azure AD pokusí získat přístup k datům objektu BLOB nebo fronty, musí mít tento objekt zabezpečení oprávnění k prostředku. Bez ohledu na to, jestli je objekt zabezpečení spravovanou identitou v Azure nebo uživatelským účtem Azure AD, který spouští kód ve vývojovém prostředí, musí být objektu zabezpečení přiřazená role Azure, která uděluje přístup k objektům blob nebo frontě v Azure Storage. Informace o přiřazování oprávnění přes Azure RBAC najdete v části **přiřazení rolí Azure pro přístupová práva** v tématu [autorizace přístupu k objektům blob a frontám Azure pomocí Azure Active Directory](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights).

### <a name="authenticate-the-user-in-the-development-environment"></a>Ověření uživatele ve vývojovém prostředí

Když váš kód běží ve vývojovém prostředí, ověřování může být zpracováno automaticky nebo může vyžadovat přihlášení prohlížeče v závislosti na tom, které nástroje používáte. Microsoft Visual Studio například podporuje jednotné přihlašování (SSO), aby se aktivní uživatelský účet Azure AD automaticky používal pro ověřování. Další informace o JEDNOTNÉm přihlašování najdete v tématu [jednotné přihlašování k aplikacím](../../active-directory/manage-apps/what-is-single-sign-on.md).

Jiné vývojové nástroje vás můžou vyzvat k přihlášení přes webový prohlížeč.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>Ověření instančního objektu ve vývojovém prostředí

Pokud vaše vývojové prostředí nepodporuje jednotné přihlašování nebo přihlašování přes webový prohlížeč, můžete k ověření z vývojového prostředí použít instanční objekt.

#### <a name="create-the-service-principal"></a>Vytvoření instančního objektu

Pokud chcete vytvořit instanční objekt pomocí Azure CLI a přiřadit roli Azure, zavolejte příkaz [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) . Zadejte Azure Storage roli přístupu k datům, která se přiřadí k novému instančnímu objektu. Kromě toho zadejte obor pro přiřazení role. Další informace o předdefinovaných rolích, které jsou k dispozici pro Azure Storage, najdete v tématu [předdefinované role Azure](../../role-based-access-control/built-in-roles.md).

Pokud nemáte dostatečná oprávnění k přiřazení role k instančnímu objektu, může být nutné požádat vlastníka nebo správce účtu, aby provedl přiřazení role.

V následujícím příkladu se pomocí Azure CLI vytvoří nový instanční objekt a přiřadí se k němu role **čtečky dat objektů BLOB úložiště** s rozsahem účtu.

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

`az ad sp create-for-rbac`Příkaz vrátí seznam vlastností instančního objektu ve formátu JSON. Zkopírujte tyto hodnoty, abyste je mohli použít k vytvoření potřebných proměnných prostředí v dalším kroku.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> Rozšiřování přiřazení rolí Azure může trvat několik minut.

#### <a name="set-environment-variables"></a>Nastavení proměnných prostředí

Klientská knihovna Azure identity načítá při ověřování instančního objektu hodnoty ze tří proměnných prostředí za běhu. Následující tabulka popisuje hodnotu, která se má nastavit pro každou proměnnou prostředí.

|Proměnná prostředí|Hodnota
|-|-
|`AZURE_CLIENT_ID`|ID aplikace pro instanční objekt
|`AZURE_TENANT_ID`|ID tenanta Azure AD objektu služby
|`AZURE_CLIENT_SECRET`|Heslo generované pro instanční objekt

> [!IMPORTANT]
> Po nastavení proměnných prostředí zavřete a znovu otevřete okno konzoly. Pokud používáte aplikaci Visual Studio nebo jiné vývojové prostředí, může být nutné restartovat vývojové prostředí, aby bylo možné zaregistrovat nové proměnné prostředí.

Další informace najdete v tématu věnovaném [vytvoření identity pro aplikaci Azure na portálu](../../active-directory/develop/howto-create-service-principal-portal.md).

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Příklad kódu .NET: vytvoření objektu blob bloku

Přidejte následující `using` direktivy do kódu, abyste mohli použít klientské knihovny Azure identity a Azure Storage.

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
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
    catch (RequestFailedException e)
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

- [Spravujte přístupová práva k datům úložiště pomocí Azure RBAC](./storage-auth-aad-rbac-portal.md).
- [Používejte Azure AD s aplikacemi pro úložiště](storage-auth-aad-app.md).
- [Spuštění příkazů PowerShellu s přihlašovacími údaji Azure AD pro přístup k datům objektu BLOB](../blobs/authorize-active-directory-powershell.md)