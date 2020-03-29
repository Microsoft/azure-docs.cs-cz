---
title: Autorizace přístupu k datům se spravovanou identitou
titleSuffix: Azure Storage
description: Zjistěte, jak používat spravované identity pro prostředky Azure k autorizaci přístupu k datům objektů blob a fronty z aplikací spuštěných ve virtuálních počítačích Azure, aplikacích funkcí, škálovacích sadách virtuálních počítačů a dalších.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f3bac0d47a53da1ec4d1fa08b5f0933f5f65dc56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255337"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Autorizace přístupu k datům objektů blob a fronty se spravovanými identitami pro prostředky Azure

Azure Blob and Queue storage podporuje ověřování Azure Active Directory (Azure AD) se [spravovanými identitami pro prostředky Azure.](../../active-directory/managed-identities-azure-resources/overview.md) Spravované identity pro prostředky Azure můžou autorizovat přístup k datům objektů blob a fronty pomocí přihlašovacích údajů Azure AD z aplikací spuštěných ve virtuálních počítačích Azure, aplikací chodů, škálovacích sad virtuálních počítačů a dalších služeb. Pomocí spravovaných identit pro prostředky Azure společně s ověřováním Azure AD se můžete vyhnout ukládání přihlašovacích údajů s vašimi aplikacemi, které běží v cloudu.  

Tento článek ukazuje, jak autorizovat přístup k datům objektů blob nebo fronty z virtuálního počítače Azure pomocí spravovaných identit pro prostředky Azure. Také popisuje, jak otestovat kód ve vývojovém prostředí.

## <a name="enable-managed-identities-on-a-vm"></a>Povolení spravovaných identit na virtuálním počítači

Než budete moct použít spravované identity pro prostředky Azure k autorizaci přístupu k objektům BLOB a frontám z vašeho virtuálního počítače, musíte nejdřív povolit spravované identity pro prostředky Azure na virtuálním počítači. Informace o povolení spravovaných identit pro prostředky Azure najdete v jednom z těchto článků:

- [Portál Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Šablona Azure Resource Manageru](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Klientské knihovny Azure Resource Manageru](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Další informace o spravovaných identitách najdete [v tématu Spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="authenticate-with-the-azure-identity-library"></a>Ověření pomocí knihovny Azure Identity

Klientská knihovna Azure Identity poskytuje podporu ověřování tokenů Azure AD pro [azure sdk.](https://github.com/Azure/azure-sdk) Nejnovější verze klientských knihoven Azure Storage pro .NET, Java, Python a JavaScript se integrují s knihovnou Azure Identity a poskytují jednoduchý a bezpečný prostředek k získání tokenu OAuth 2.0 pro autorizaci požadavků azure storage.

Výhodou klientské knihovny Azure Identity je, že umožňuje použít stejný kód k ověření, zda vaše aplikace běží ve vývojovém prostředí nebo v Azure. Klientská knihovna Azure Identity pro rozhraní .NET ověřuje objekt zabezpečení. Když váš kód běží v Azure, objekt zabezpečení je spravovaná identita pro prostředky Azure. Ve vývojovém prostředí spravovaná identita neexistuje, takže klientská knihovna ověřuje uživatele nebo instanční objekt pro účely testování.

Po ověření získá klientská knihovna Azure Identity pověření tokenu. Tento pověření tokenu je pak zapouzdřen v objektu klienta služby, který vytvoříte k provedení operací proti Azure Storage. Knihovna zpracovává to pro vás bez problémů získáním příslušné pověření tokenu.

Další informace o knihovně klienta Azure Identity pro rozhraní .NET najdete v tématu [Knihovna klientů Azure Identity pro rozhraní .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity). Referenční dokumentaci pro klientskou knihovnu Azure Identity najdete v [tématu Azure.Identity Namespace](/dotnet/api/azure.identity).

### <a name="assign-role-based-access-control-rbac-roles-for-access-to-data"></a>Přiřazení rolí řízení přístupu na základě rolí (RBAC) pro přístup k datům

Když se objekt zabezpečení Azure AD pokusí o přístup k datům objektu blob nebo fronty, musí mít tento objekt zabezpečení oprávnění k prostředku. Ať už je objekt zabezpečení spravovanou identitou v Azure nebo uživatelský účet Azure AD spuštěný kód ve vývojovém prostředí, musí být objektu zabezpečení přiřazena role RBAC, která uděluje přístup k datům objektů blob nebo fronty ve službě Azure Storage. Informace o přiřazování oprávnění prostřednictvím služby RBAC naleznete v části **Přiřazení rolí RBAC pro přístupová práva** v [tématu Autorizace přístupu k objektům BLOB a frontám Azure pomocí služby Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

### <a name="authenticate-the-user-in-the-development-environment"></a>Ověření uživatele ve vývojovém prostředí

Pokud je váš kód spuštěn ve vývojovém prostředí, ověřování může být zpracováno automaticky nebo může vyžadovat přihlášení prohlížeče v závislosti na tom, které nástroje používáte. Například Microsoft Visual Studio podporuje jednotné přihlašování (SSO), takže aktivní uživatelský účet Azure AD se automaticky používá pro ověřování. Další informace o jednotném přihlašování naleznete [v tématu jednotné přihlašování k aplikacím](../../active-directory/manage-apps/what-is-single-sign-on.md).

Další vývojové nástroje vás mohou vyzvat k přihlášení prostřednictvím webového prohlížeče.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>Ověření instančního objektu ve vývojovém prostředí

Pokud vaše vývojové prostředí nepodporuje jednotné přihlašování nebo přihlášení prostřednictvím webového prohlížeče, můžete použít inistenci služby k ověření z vývojového prostředí.

#### <a name="create-the-service-principal"></a>Vytvoření instančního objektu

Chcete-li vytvořit instanční objekt s Azure CLI a přiřadit roli RBAC, zavolejte příkaz [az ad sp create-for-rbac.](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) Poskytněte roli přístupu k datům služby Azure Storage, kterou chcete přiřadit k novému instančnímu objektu služby. Dále zadejte rozsah pro přiřazení role. Další informace o předdefinovaných rolích poskytovaných pro Azure Storage najdete [v tématu Předdefinované role pro prostředky Azure](../../role-based-access-control/built-in-roles.md).

Pokud nemáte dostatečná oprávnění k přiřazení role instančnímu objektu, bude pravděpodobně nutné požádat vlastníka nebo správce účtu o provedení přiřazení role.

Následující příklad používá azure CLI k vytvoření nového instančního objektu a přiřadit roli **čtečky dat objektu blob úložiště** k němu s oborem účtu

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
> Přiřazení rolí RBAC může trvat několik minut k šíření.

#### <a name="set-environment-variables"></a>Nastavení proměnných prostředí

Klientská knihovna Azure Identity čte hodnoty ze tří proměnných prostředí za běhu k ověření instančního objektu. Následující tabulka popisuje hodnotu, která má být nastavena pro každou proměnnou prostředí.

|Proměnná prostředí|Hodnota
|-|-
|`AZURE_CLIENT_ID`|ID aplikace pro instanční objekt
|`AZURE_TENANT_ID`|ID klienta Azure AD hlavního povinného servisu
|`AZURE_CLIENT_SECRET`|Heslo generované pro instanční objekt

> [!IMPORTANT]
> Po nastavení proměnných prostředí zavřete a znovu otevřete okno konzoly. Pokud používáte Visual Studio nebo jiné vývojové prostředí, budete muset restartovat vývojové prostředí, aby mohl zaregistrovat nové proměnné prostředí.

Další informace najdete [v tématu Vytvoření identity pro aplikaci Azure na portálu](../../active-directory/develop/howto-create-service-principal-portal.md).

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Příklad kódu rozhraní .NET: Vytvoření objektu blob bloku

Přidejte `using` do kódu následující direktivy, abyste použili klientské knihovny Azure Identity a Azure Storage.

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
```

Chcete-li získat pověření tokenu, které váš kód můžete použít k autorizaci požadavků na Azure Storage, vytvořte instanci třídy [DefaultAzureCredential.](/dotnet/api/azure.identity.defaultazurecredential) Následující příklad kódu ukazuje, jak získat ověřené pověření tokenu a použít je k vytvoření objektu klienta služby, potom pomocí klienta služby nahrát nový objekt blob:

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
> Chcete-li autorizovat požadavky proti datům objektů blob nebo fronty pomocí Azure AD, musíte pro tyto požadavky použít protokol HTTPS.

## <a name="next-steps"></a>Další kroky

- [Správa přístupových práv k datům úložiště pomocí RBAC](storage-auth-aad-rbac.md).
- [Azure AD používejte s aplikacemi úložiště](storage-auth-aad-app.md).
- [Spouštět příkazy Azure CLI nebo PowerShell s přihlašovacími údaji Azure AD pro přístup k datům objektu blob nebo fronty](authorize-active-directory-powershell.md).
