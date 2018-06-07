---
title: Ověření pomocí Azure AD z virtuálního počítače Azure identita spravované služby (Preview) | Microsoft Docs
description: Ověření pomocí Azure AD z virtuálního počítače Azure identita spravované služby (Preview).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
ms.openlocfilehash: 83d3a2d973604e3b8a709b24cabcb3abba1e304c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660739"
---
# <a name="authenticate-with-azure-ad-from-an-azure-managed-service-identity-preview"></a>Ověření pomocí Azure AD z Identity spravované služby Azure (Preview)

Úložiště Azure podporuje ověřování Azure Active Directory (Azure AD) s [identita spravované služby](../../active-directory/managed-service-identity/overview.md). Identita spravované služby (MSI) poskytuje automaticky spravované identity v Azure Active Directory (Azure AD). MSI můžete použít k ověření do služby Azure Storage z aplikací, které běží ve virtuálních počítačích Azure, funkce aplikací, sady škálování virtuálního počítače a dalších. Pomocí Instalační služby MSI a využití možností ověřování Azure AD, se můžete vyhnout ukládání přihlašovacích údajů s vašimi aplikacemi, které běží v cloudu.  

Udělení oprávnění identitě spravované služby pro kontejnery úložiště nebo fronty, přiřaďte role RBAC zahrnující úložiště oprávnění k souboru MSI. Další informace o rolích RBAC v úložišti, najdete v části [spravovat přístupová práva k úložišti dat pomocí RBAC (Preview)](storage-auth-aad-rbac.md). 

> [!IMPORTANT]
> Tato verze preview je určeno pouze pro nevýrobní prostředí. Produkční smlouvy o úrovni služeb (SLA) nebudou dostupné, dokud integrace Azure AD pro Azure Storage je deklarovaná všeobecně dostupná. Pokud pro váš scénář není dosud podporován integrace Azure AD, nadále používat sdílený klíč autorizace nebo tokeny SAS ve svých aplikacích. Další informace o verzi preview najdete v tématu [ověření přístupu k úložišti Azure pomocí služby Azure Active Directory (Preview)](storage-auth-aad.md).
>
> Ve verzi Preview přiřazení rolí pro RBAC může trvat až pět minut, aby se rozšířily změny.

Tento článek ukazuje, jak k ověření do služby Azure Storage s MSI z virtuálního počítače Azure.  

## <a name="enable-msi-on-the-vm"></a>Povolit MSI ve virtuálním počítači

Než MSI můžete použít k ověření do služby Azure Storage z virtuálního počítače, musíte nejdřív povolit MSI ve virtuálním počítači. Další informace o povolení MSI, najdete v jednom z těchto článků:

- [Azure Portal](https://docs.microsoft.com/en-us/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-service-identity/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-service-identity/qs-configure-cli-windows-vm.md)
- [Šablona Azure Resource Manageru](../../active-directory/managed-service-identity/qs-configure-template-windows-vm.md)
- [Azure SDK](../../active-directory/managed-service-identity/qs-configure-sdk-windows-vm.md)

## <a name="get-an-msi-access-token"></a>Získání tokenu přístupu MSI

K ověření s MSI, musí získat přístupový token MSI aplikace nebo skriptu. Další informace o tom, jak získat přístupový token, najdete v části [jak používat Azure virtuálního počítače spravované služby Identity (MSI) pro získání tokenu](../../active-directory/managed-service-identity/how-to-use-vm-token.md).

## <a name="net-code-example-create-a-block-blob"></a>Příklad kódu .NET: Vytvořte objekt blob bloku

Příklad kódu předpokládá, že máte MSI přístupový token. Přístupový token se používá k autorizaci identita spravované služby vytvořit objekt blob bloku.

### <a name="add-references-and-using-statements"></a>Přidejte odkazy a pomocí příkazů  

V sadě Visual Studio instalace verze preview Klientská knihovna pro úložiště Azure. Z **nástroje** nabídce vyberte možnost **Správce balíčků Nuget**, pak **Konzola správce balíčků**. Zadejte následující příkaz do konzoly:

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0  
```

Přidejte následující příkazy using do vašeho kódu:

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-msi-access-token"></a>Vytvořit přihlašovací údaje z přístupového tokenu, MSI

Chcete-li vytvořit objekt blob bloku, použijte **TokenCredentials** třída poskytovaný balíček preview. Vytvořit novou instanci třídy **TokenCredentials**a předejte MSI přístupový token, který jste získali dříve:

```dotnet
// Create storage credentials from your MSI access token.
TokenCredential tokenCredential = new TokenCredential(msiAccessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using the credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
``` 

> [!NOTE]
> Integrace služby Azure AD s Azure Storage vyžaduje pomocí protokolu HTTPS pro operace úložiště Azure.

## <a name="next-steps"></a>Další postup

- Další informace o rolích RBAC pro Azure storage najdete v tématu [spravovat přístupová práva k úložišti dat pomocí RBAC (Preview)](storage-auth-aad-rbac.md).
- Informace o tom, k autorizaci přístupu ke kontejnerům a fronty ze v rámci aplikace úložiště, najdete v části [používání Azure AD s aplikacemi úložiště](storage-auth-aad-app.md).
- Další postupy k přihlášení do příkazového řádku Azure CLI a prostředí PowerShell s Azure AD identity najdete v tématu [používat Azure AD identity pro přístup k Azure Storage pomocí rozhraní CLI nebo Powershellu (Preview)](storage-auth-aad-script.md).
- Další informace o integraci služby Azure AD pro front a objektů BLOB služby Azure najdete v tématu blog týmu Azure Storage, post, [uvedení ověřování Preview služby Azure AD pro Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
