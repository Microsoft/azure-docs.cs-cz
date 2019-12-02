---
title: Autorizace přístupu k objektům blob a frontám pomocí Azure Active Directory a spravovaných identit pro prostředky Azure – Azure Storage
description: Azure Blob a Queue Storage podporují autorizaci přístupu k prostředkům pomocí Azure Active Directory a spravovaných identit pro prostředky Azure. Spravované identity pro prostředky Azure můžete použít k autorizaci přístupu k objektům blob a frontám z aplikací běžících na virtuálních počítačích Azure, aplikací Functions, Virtual Machine Scale Sets a dalších.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 3e24cb2d4b5b82f6878647cdd631bd8ebca16199
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666153"
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

## <a name="authenticate-with-the-azure-identity-library"></a>Ověřování pomocí knihovny identit Azure

Výhodou klientské knihovny Azure identity je to, že umožňuje použít stejný kód k ověření, jestli vaše aplikace běží ve vývojovém prostředí nebo v Azure. V kódu spuštěném v prostředí Azure Klientská knihovna ověřuje spravovanou identitu pro prostředky Azure. Ve vývojovém prostředí neexistuje spravovaná identita, takže Klientská knihovna ověřuje pro účely testování buď uživatele, nebo instanční objekt.

Klientská knihovna Azure identity pro .NET ověřuje objekt zabezpečení. Když váš kód běží v Azure, je objekt zabezpečení spravovaná identita pro prostředky Azure.

Po ověření získá Klientská knihovna identity Azure přihlašovací údaje tokenu. Tyto přihlašovací údaje tokenu se pak zapouzdřují v objektu klienta služby, který vytvoříte k provádění operací s Azure Storage. Knihovna to zvládne bez problémů získáním příslušných přihlašovacích údajů tokenu.

Další informace o klientské knihovně identit Azure najdete v tématu [Klientská knihovna Azure identity pro .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

### <a name="assign-role-based-access-control-rbac-roles-for-access-to-data"></a>Přiřazení rolí řízení přístupu na základě role (RBAC) pro přístup k datům

Když se objekt zabezpečení služby Azure AD pokusí získat přístup k datům objektu BLOB nebo fronty, musí mít tento objekt zabezpečení oprávnění k prostředku. Bez ohledu na to, jestli je objekt zabezpečení spravovanou identitou v Azure nebo uživatelským účtem Azure AD, který spouští kód ve vývojovém prostředí, musí být objektu zabezpečení přiřazená role RBAC, která uděluje přístup k objektům blob nebo frontě v Azure Storage. Informace o přiřazování oprávnění přes RBAC najdete v části s názvem **přiřazení rolí RBAC pro přístupová práva** v tématu [autorizace přístupu k objektům blob a frontám Azure pomocí Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

### <a name="authenticate-the-user-in-the-development-environment"></a>Ověření uživatele ve vývojovém prostředí

Když váš kód běží ve vývojovém prostředí, ověřování může být zpracováno automaticky nebo může vyžadovat přihlášení prohlížeče v závislosti na tom, které nástroje používáte. Microsoft Visual Studio podporuje jednotné přihlašování (SSO), aby se aktivní uživatelský účet Azure AD automaticky používal pro ověřování. Další informace o JEDNOTNÉm přihlašování najdete v tématu [jednotné přihlašování k aplikacím](../../active-directory/manage-apps/what-is-single-sign-on.md).

Jiné vývojové nástroje vás můžou vyzvat k přihlášení přes webový prohlížeč.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>Ověření instančního objektu ve vývojovém prostředí

Pokud vaše vývojové prostředí nepodporuje jednotné přihlašování nebo přihlašování přes webový prohlížeč, můžete k ověření z vývojového prostředí použít instanční objekt.

#### <a name="create-the-service-principal"></a>Vytvoření instančního objektu

Pokud chcete vytvořit instanční objekt pomocí Azure CLI a přiřadit roli RBAC, zavolejte příkaz [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) . Zadejte Azure Storage roli přístupu k datům, která se přiřadí k novému instančnímu objektu. Kromě toho zadejte obor pro přiřazení role. Další informace o předdefinovaných rolích, které jsou k dispozici pro Azure Storage, najdete v tématu [předdefinované role pro prostředky Azure](../../role-based-access-control/built-in-roles.md).

Pokud nemáte dostatečná oprávnění k přiřazení role k instančnímu objektu, může být nutné požádat vlastníka nebo správce účtu, aby provedl přiřazení role.

V následujícím příkladu se pomocí Azure CLI vytvoří nový instanční objekt a přiřadí se k němu role **čtečky dat objektů BLOB úložiště** s rozsahem účtu.

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Příkaz `az ad sp create-for-rbac` vrátí seznam vlastností instančního objektu ve formátu JSON. Zkopírujte tyto hodnoty, abyste je mohli použít k vytvoření potřebných proměnných prostředí v dalším kroku.

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
> Rozšiřování přiřazení rolí RBAC může trvat několik minut.

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

Do kódu přidejte následující direktivy `using`, aby bylo možné použít klientské knihovny Azure identity a Azure Storage.

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

- Další informace o rolích RBAC pro Azure Storage najdete v tématu [Správa přístupových práv k datům úložiště pomocí RBAC](storage-auth-aad-rbac.md).
- Informace o tom, jak autorizovat přístup k kontejnerům a frontám v rámci aplikací úložiště, najdete v tématu [použití Azure AD s aplikacemi úložiště](storage-auth-aad-app.md).
- Pokud chcete zjistit, jak spustit příkazy Azure CLI a PowerShell pomocí přihlašovacích údajů Azure AD, přečtěte si téma [spuštění příkazů Azure CLI nebo PowerShellu s přihlašovacími údaji Azure AD pro přístup k datům BLOB nebo Queue](storage-auth-aad-script.md).
