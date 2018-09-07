---
title: Ověřování pomocí Azure AD z virtuálního počítače Azure se identita spravované služby (Preview) | Dokumentace Microsoftu
description: Ověřování pomocí Azure AD z virtuálního počítače Azure se identita spravované služby (Preview).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: e20e0c412206b2a35973b192ef911bb99ed7c210
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44021859"
---
# <a name="authenticate-with-azure-ad-from-an-azure-managed-service-identity-preview"></a>Ověření ve službě Azure AD z Identity spravovaných služeb Azure (Preview)

Azure Storage podporuje ověřování Azure Active Directory (Azure AD) s [identita spravované služby](../../active-directory/managed-identities-azure-resources/overview.md). Identita spravované služby (MSI) poskytuje automaticky spravovanou identitu ve službě Azure Active Directory (Azure AD). Použití MSI pro ověření do služby Azure Storage z aplikace běžící v Azure virtuální počítače, aplikace function App, škálovací sady virtuálních počítačů a dalších. Pomocí MSI a využívá širokých možností ověřování Azure AD, můžete zabránit ukládání přihlašovacích údajů s vašimi aplikacemi, které běží v cloudu.  

Chcete-li udělit oprávnění pro identitu spravované služby pro kontejnery úložiště nebo fronty, přiřaďte roli RBAC, včetně úložiště oprávnění k souboru MSI. Další informace o rolích RBAC v úložišti, najdete v části [Správa přístupových práv k datům úložiště pomocí RBAC (Preview)](storage-auth-aad-rbac.md). 

> [!IMPORTANT]
> V této verzi preview je určeno pouze pro nevýrobní prostředí. Produkční smlouvy o úrovni služeb (SLA) nebudou dostupné, dokud integrace Azure AD pro službu Azure Storage je deklarována jako obecně dostupné. Pokud se integrace služby Azure AD není dosud podporována pro váš scénář, dál používat povolení sdíleného klíče nebo tokeny SAS ve svých aplikacích. Další informace o verzi preview najdete v tématu [ověření přístupu ke službě Azure Storage pomocí Azure Active Directory (Preview)](storage-auth-aad.md).
>
> Ve verzi preview přiřazení rolí pro RBAC může trvat až pět minut na dokončení propagace.

Tento článek ukazuje, jak ověření na Azure Storage s využitím MSI z virtuálního počítače Azure.  

## <a name="enable-msi-on-the-vm"></a>Povolení MSI ve virtuálním počítači

Než použití MSI pro ověření do služby Azure Storage z vašeho virtuálního počítače, musíte nejprve povolit MSI ve virtuálním počítači. Informace o povolení MSI, najdete v některém z těchto článků:

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Šablona Azure Resource Manageru](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Sady Azure SDK](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="get-an-msi-access-token"></a>Získání tokenu přístupu MSI

Abyste mohli ověřovat pomocí MSI, musí vaše aplikace nebo skript získání přístupového tokenu MSI. Další informace o tom, jak získat přístupový token, naleznete v tématu [použití Azure VM Identity spravované služby (MSI) pro získání tokenu](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="net-code-example-create-a-block-blob"></a>Příklad kódu .NET: vytvoření objektu blob bloku

Příklad kódu předpokládá, že máte přístupový token MSI. Přístupový token se používá k ověření identity spravované služby pro vytvoření objektu blob bloku.

### <a name="add-references-and-using-statements"></a>Přidání odkazů a příkazy using  

V sadě Visual Studio nainstalujte klientské knihovny Azure Storage ve verzi preview. Z **nástroje** nabídce vyberte možnost **Správce balíčků Nuget**, pak **Konzola správce balíčků**. Do konzoly zadejte následující příkaz:

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0  
```

Přidejte následující příkazy using do kódu:

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-msi-access-token"></a>Vytvořte přihlašovací údaje z tokenu přístupu MSI

Chcete-li vytvořit objekt blob bloku, použijte **TokenCredentials** třídy poskytovaný balíček ve verzi preview. Vytvořit novou instanci třídy **TokenCredentials**a předejte token přístupu MSI, který jste získali dříve:

```dotnet
// Create storage credentials from your MSI access token.
TokenCredential tokenCredential = new TokenCredential(msiAccessToken);
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
