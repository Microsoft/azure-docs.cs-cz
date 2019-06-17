---
title: Ověření přístupu k objektům BLOB a fronty pomocí spravované identity pro prostředky Azure – Azure Storage | Dokumentace Microsoftu
description: Úložiště objektů Blob a fronty Azure podporují ověřování pomocí Azure Active Directory pomocí spravované identity pro prostředky Azure. Spravované identity pro prostředky Azure můžete použít k ověření přístupu k objektům BLOB a fronty z aplikací běžících v Azure virtuální počítače, aplikace function App, škálovací sady virtuálních počítačů a dalších.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f7525c3e125010bb4db9655bc214861e22dc8875
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65787961"
---
# <a name="authenticate-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Ověření přístupu k objektům BLOB a fronty s Azure Active Directory a spravovaným identitám pro prostředky Azure

Úložiště objektů Blob a fronty Azure podporují ověřování Azure Active Directory (Azure AD) s [spravovaných identit pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md). Spravovat identity pro prostředky Azure můžete autorizovat přístup k objektu blob a fronty dat pomocí přihlašovacích údajů Azure AD z aplikace běžící v Azure virtuální počítače (VM), aplikace function App, škálovací sady virtuálních počítačů a dalším službám. Pomocí spravované identity pro prostředky Azure spolu s ověřováním Azure AD, se můžete vyhnout ukládání přihlašovacích údajů s vašimi aplikacemi, které běží v cloudu.  

Tento článek ukazuje, jak autorizovat přístup k datům objektu blob nebo fronty pomocí spravované identity z virtuálního počítače Azure. 

## <a name="enable-managed-identities-on-a-vm"></a>Povolit spravovaným identitám na virtuálním počítači

Před použitím spravovaných identit pro prostředky Azure k autorizaci přístupu k objektům BLOB a fronty ze svého virtuálního počítače, musíte nejprve povolit spravovaných identit pro prostředky Azure na virtuálním počítači. Zjistěte, jak povolit spravovaných identit pro prostředky Azure, najdete v některém z těchto článků:

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Šablona Azure Resource Manageru](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Sady Azure SDK](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-an-azure-ad-managed-identity"></a>Udělení oprávnění identitě spravované služby Azure AD

K autorizaci žádost službě objektů Blob nebo fronty ze spravovaných identit ve vaší aplikaci Azure Storage, nejprve nakonfigurujte nastavení řízení přístupu na základě rolí pro tuto spravovanou identitu. Azure Storage definuje role RBAC, které zahrnuje oprávnění pro data objektů blob a fronty. Pokud RBAC role je přiřazená spravovanou identitu, spravovanou identitu se udělí oprávnění k datům objektu blob nebo fronty na příslušný obor. 

Další informace o přiřazení role RBAC naleznete v následujících článcích:

- [Udělení přístupu k Azure data objektů blob a fronty pomocí RBAC na webu Azure Portal](storage-auth-aad-rbac-portal.md)
- [Udělení přístupu k Azure data objektů blob a fronty pomocí RBAC pomocí rozhraní příkazového řádku Azure](storage-auth-aad-rbac-cli.md)
- [Udělení přístupu k Azure data objektů blob a fronty pomocí RBAC pomocí Powershellu](storage-auth-aad-rbac-powershell.md)

## <a name="authorize-with-a-managed-identity-access-token"></a>Autorizovat s přístupovým tokenem spravované identity

K autorizaci požadavků na úložiště objektů Blob a fronty pomocí spravované identity, musí vaše aplikace nebo skript získat OAuth token. [Ověřování pomocí služby Microsoft Azure App](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) Klientská knihovna pro .NET (preview) zjednodušuje pořízení a obnovuje se token z vašeho kódu.

Klientská knihovna ověřování aplikace automaticky spravuje ověřování. Knihovny používá přihlašovací údaje pro vývojáře k ověřování během místního vývoje. Pomocí přihlašovacích údajů pro vývojáře při místním vývojovém je bezpečnější, protože není potřeba vytvořit přihlašovací údaje služby Azure AD nebo sdílet přihlašovacích údajů mezi vývojáři. Při řešení je později nasadit do Azure, knihovně, automaticky se přepne do pomocí přihlašovacích údajů pro aplikace.

Pokud chcete použít knihovnu ověřování aplikace v aplikaci Azure Storage, nainstalujete nejnovější balíček ve verzi preview z [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication), a také nejnovější verzi [knihovny běžných klienta služby Azure Storage pro .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) a [Klientská knihovna pro úložiště objektů Blob v Azure pro .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/). Přidejte následující **pomocí** příkazy kódu:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```

Poskytuje knihovnu ověřování aplikace **AzureServiceTokenProvider** třídy. Instance této třídy může být předán do zpětného volání, která získá token a potom token obnovuje, než vyprší její platnost.

Následující příklad získá token a použije ho k vytvoření nového objektu blob a pak používá stejný token ke čtení objektu blob.

```csharp
const string blobName = "https://storagesamples.blob.core.windows.net/sample-container/blob1.txt";

// Get the initial access token and the interval at which to refresh it.
AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
var tokenAndFrequency = TokenRenewerAsync(azureServiceTokenProvider, 
                                            CancellationToken.None).GetAwaiter().GetResult();

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
blob.UploadTextAsync(string.Format("This is a blob named {0}", blob.Name));

// Continue to make requests against Azure Storage. 
// The token is automatically refreshed as needed in the background.
do
{
    // Read blob contents
    Console.WriteLine("Time accessed: {0} Blob Content: {1}", 
                        DateTimeOffset.UtcNow, 
                        blob.DownloadTextAsync().Result);

    // Sleep for ten seconds, then read the contents of the blob again.
    Thread.Sleep(TimeSpan.FromSeconds(10));
} while (true);
```

Metoda zpětného volání zkontroluje čas vypršení platnosti tokenu a podle potřeby jej obnoví:

```csharp
private static async Task<NewTokenAndFrequency> TokenRenewerAsync(Object state, CancellationToken cancellationToken)
{
    // Specify the resource ID for requesting Azure AD tokens for Azure Storage.
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

Další informace o knihovně App ověřování najdete v tématu [ověřování služba služba do služby Azure Key Vault pomocí rozhraní .NET](../../key-vault/service-to-service-authentication.md). 

Další informace o tom, jak získat přístupový token, naleznete v tématu [použití spravované identity pro prostředky Azure na Virtuálním počítači Azure získat přístupový token](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

> [!NOTE]
> K autorizaci požadavků na data blob nebo fronty s Azure AD, musí používat protokol HTTPS pro tyto požadavky.

## <a name="next-steps"></a>Další postup

- Další informace o rolích RBAC pro Azure storage najdete v tématu [Správa přístupových práv k datům úložiště pomocí RBAC](storage-auth-aad-rbac.md).
- Zjistěte, jak autorizovat přístup ke kontejnerům a front z v rámci aplikace úložiště, najdete v článku [pomocí služby Azure AD s aplikacemi úložiště](storage-auth-aad-app.md).
- Zjistěte, jak spouštět příkazy rozhraní příkazového řádku Azure a prostředí PowerShell pomocí přihlašovacích údajů Azure AD, najdete v článku [příkazy spuštění Azure CLI nebo Powershellu pomocí přihlašovacích údajů Azure AD pro přístup k datům objektu blob nebo fronty](storage-auth-aad-script.md).