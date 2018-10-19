---
title: Ověření přístupu k objektům BLOB a fronty s Azure Active Directory spravovat identity pro prostředky Azure (Preview) – služby Azure Storage | Dokumentace Microsoftu
description: Úložiště objektů Blob a fronty Azure podporuje ověřování Azure Active Directory pomocí spravované identity pro prostředky Azure. Spravované identity pro prostředky Azure můžete použít k ověření přístupu k objektům BLOB a fronty z aplikací běžících v Azure virtuální počítače, aplikace function App, škálovací sady virtuálních počítačů a dalších. Pomocí spravované identity pro prostředky Azure a využívá širokých možností ověřování Azure AD, můžete zabránit ukládání přihlašovacích údajů s vašimi aplikacemi, které běží v cloudu.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/15/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: d8382cac86abb112018195695340ed12663a2333
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427412"
---
# <a name="authenticate-access-to-blobs-and-queues-with-managed-identities-for-azure-resources-preview"></a>Ověření přístupu k objektům BLOB a fronty pomocí spravované identity pro prostředky Azure (Preview)

Úložiště objektů Blob a fronty Azure podporují ověřování Azure Active Directory (Azure AD) s [spravovaných identit pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md). Spravovat identity pro prostředky Azure může ověřit přístup k objektům BLOB a fronty pomocí přihlašovacích údajů Azure AD z aplikace běžící v Azure virtuální počítače (VM), aplikace function App, škálovací sady virtuálních počítačů a dalších. Pomocí spravované identity pro prostředky Azure a využívá širokých možností ověřování Azure AD, můžete zabránit ukládání přihlašovacích údajů s vašimi aplikacemi, které běží v cloudu.  

Udělení oprávnění pro spravovanou identitu do kontejneru objektů blob nebo fronty, přiřadíte roli řízení přístupu na základě role spravovanou identitu, která zahrnuje oprávnění pro daný prostředek na příslušný obor. Další informace o rolích RBAC v úložišti, najdete v části [Správa přístupových práv k datům úložiště pomocí RBAC (Preview)](storage-auth-aad-rbac.md). 

Tento článek ukazuje, jak ověřit do fronty nebo Azure Blob storage s využitím spravované identity z virtuálního počítače Azure.  

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="enable-managed-identities-on-a-vm"></a>Povolit spravovaným identitám na virtuálním počítači

Před použitím spravovaných identit pro prostředky Azure k ověření přístupu k objektům BLOB a fronty ze svého virtuálního počítače, je nutné povolit spravovaných identit pro prostředky Azure na virtuálním počítači. Zjistěte, jak povolit spravovaných identit pro prostředky Azure, najdete v některém z těchto článků:

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Šablona Azure Resource Manageru](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Sady Azure SDK](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="assign-an-rbac-role-to-an-azure-ad-managed-identity"></a>Přiřaďte roli RBAC na identitu spravované služby Azure AD

K ověření spravovanou identitu aplikace služby Azure Storage, nejprve nakonfigurujte nastavení řízení přístupu na základě role pro tuto spravovanou identitu. Azure Storage definuje role RBAC, které zahrnuje oprávnění pro kontejnery a fronty. Když RBAC role je přiřazená spravovanou identitu, která se identita spravované je udělen přístup k prostředku. Další informace najdete v tématu [Správa přístupových práv k datům objektů Blob v Azure a fronty pomocí RBAC (Preview)](storage-auth-aad-rbac.md).

## <a name="get-a-managed-identity-access-token"></a>Získání tokenu přístupu spravované identity

K ověření pomocí spravované identity, musíte získat přístupový token spravovanou identitu vaše aplikace nebo skript. Další informace o tom, jak získat přístupový token, naleznete v tématu [použití spravované identity pro prostředky Azure na Virtuálním počítači Azure získat přístupový token](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="net-code-example-create-a-block-blob"></a>Příklad kódu .NET: vytvoření objektu blob bloku

Příklad kódu předpokládá, že máte přístupový token spravovaná identita. Přístupový token se používá k autorizaci spravovanou identitu, chcete-li vytvořit objekt blob bloku.

### <a name="add-references-and-using-statements"></a>Přidání odkazů a příkazy using  

V sadě Visual Studio nainstalujte klientské knihovny Azure Storage ve verzi preview. Z **nástroje** nabídce vyberte možnost **Správce balíčků Nuget**, pak **Konzola správce balíčků**. Do konzoly zadejte následující příkaz:

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage  
```

Přidejte následující příkazy using do kódu:

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-managed-identity-access-token"></a>Vytvořte přihlašovací údaje z přístupového tokenu spravované identity

Chcete-li vytvořit objekt blob bloku, použijte **TokenCredentials** třídy poskytovaný balíček ve verzi preview. Vytvořit novou instanci třídy **TokenCredentials**a předejte spravovanou identitu přístupový token, který jste získali dříve:

```dotnet
// Create storage credentials from your managed identity access token.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using the credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
``` 

> [!NOTE]
> Integrace Azure AD pomocí služby Azure Storage vyžaduje použití protokolu HTTPS pro operace služby Azure Storage.

## <a name="next-steps"></a>Další postup

- Další informace o rolích RBAC pro Azure storage najdete v tématu [Správa přístupových práv k datům úložiště pomocí RBAC (Preview)](storage-auth-aad-rbac.md).
- Zjistěte, jak autorizovat přístup ke kontejnerům a front z v rámci aplikace úložiště, najdete v článku [pomocí služby Azure AD s aplikacemi úložiště](storage-auth-aad-app.md).
- Zjistěte, jak k přihlášení do Powershellu a Azure CLI s Azure AD identity, najdete v článku [pomocí identity Azure AD pro přístup k Azure Storage s využitím rozhraní příkazového řádku nebo Powershellu (Preview)](storage-auth-aad-script.md).
- Další informace o integraci služby Azure AD pro objekty BLOB Azure a front, najdete v článku na blogu týmu Azure Storage účtovat, [oznamujeme vydání verze Preview služby Azure AD Authentication pro službu Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
