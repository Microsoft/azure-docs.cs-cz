---
title: Spusťte příkazy prostředí PowerShell nebo rozhraní příkazového řádku Azure v rámci identity Azure AD pro přístup k úložišti Azure (Preview) | Dokumentace Microsoftu
description: Azure CLI a Powershellu podporu přihlášení pomocí identity Azure AD ke spouštění příkazů na kontejnery služby Azure Storage a fronty a jejich data. Přístupový token je k dispozici pro relaci a použít k autorizaci volání operace. Oprávnění, závisí na roli přiřazenou k identitě služby Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/29/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: b90050291a936027f66a76c14458e717b63c7257
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44090723"
---
# <a name="use-an-azure-ad-identity-to-access-azure-storage-with-cli-or-powershell-preview"></a>Pomocí identity Azure AD pro přístup k Azure Storage s využitím rozhraní příkazového řádku nebo Powershellu (Preview)

Azure Storage poskytuje rozšíření ve verzi preview pro Azure CLI a Powershellu, které vám umožní přihlásit a spuštění příkazů skriptu v rámci Azure Active Directory (Azure AD) identity. Identity Azure AD může být uživatele, skupinu nebo instanční objekt služby aplikace nebo může se jednat [se identita spravované služby](../../active-directory/managed-identities-azure-resources/overview.md). Můžete přiřadit oprávnění pro přístup k prostředkům úložiště do identity Azure AD prostřednictvím řízení přístupu na základě role (RBAC). Další informace o rolích RBAC ve službě Azure Storage najdete v tématu [Správa přístupových práv k datům služby Azure Storage pomocí RBAC (Preview)](storage-auth-aad-rbac.md).

Po přihlášení k Azure CLI nebo Powershellu s Azure AD identity, je vrácena přístupového tokenu pro přístup k Azure Storage v rámci této identity. Tento token se pak automaticky použije pomocí Powershellu nebo rozhraní příkazového řádku k autorizaci operace využívající službu Azure Storage. Pro podporované operace je už nebude potřeba předat klíč k účtu nebo token SAS pomocí příkazu.

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="supported-operations"></a>Podporované operace

Rozšíření ve verzi preview se podporují pro operace s kontejnery a fronty. Operace, které může volat závisí na oprávněních udělených identity Azure AD, pomocí kterého přihlášení k Azure CLI nebo Powershellu. Oprávnění pro kontejnery služby Azure Storage nebo fronty jsou přiřazeny prostřednictvím řízení přístupu na základě role (RBAC). Například pokud čtecí modul dat role je přiřazená k identitě, poté můžete spouštět příkazy skriptu, které číst data z kontejneru nebo fronty. Pokud přispěvatele dat role je přiřazená k identitě, poté můžete spouštět příkazy skriptu, které číst, zapsat nebo odstranit kontejner nebo fronty nebo data, která obsahují. 

Podrobnosti o oprávněních požadovaných pro každou operaci služby Azure Storage v kontejneru nebo fronty, naleznete v tématu [oprávnění pro volání operace REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).  

## <a name="call-cli-commands-with-an-azure-ad-identity"></a>Příkazy rozhraní příkazového řádku volání s identitou služby Azure AD

Chcete-li nainstalovat rozšíření ve verzi preview pro Azure CLI:

1. Ujistěte se, že máte nainstalovanou verzi Azure CLI 2.0.32 nebo novější. Spustit `az --version` zkontrolujte nainstalovanou verzi.
2. Spusťte následující příkaz k instalaci rozšíření ve verzi preview: 

    ```azurecli
    az extension add -n storage-preview
    ```

Přidá nový náhled rozšíření `--auth-mode` parametr podporované příkazy:

- Nastavte `--auth-mode` parametr `login` k přihlášení pomocí identity Azure AD.
- Nastavte `--auth-mode` parametr starší `key` jsou k dispozici hodnota pokusu o dotaz na klíče if účet žádné parametry ověřování pro účet. 

Například pokud chcete stáhnout objekty blob v Azure CLI pomocí identity Azure AD, nejprve spusťte `az login`, pak volání příkazu s `--auth-mode` nastavena na `login`:

```azurecli
az login
az storage blob download --account-name storagesamples --container sample-container --name myblob.txt --file myfile.txt --auth-mode login 
```

Přidružený k proměnné prostředí `--auth-mode` parametr `AZURE_STORAGE_AUTH_MODE`.

## <a name="call-powershell-commands-with-an-azure-ad-identity"></a>Volat příkazy prostředí PowerShell s Azure AD identity

Jak se přihlásit pomocí identity Azure AD pomocí Azure Powershellu:

1. Ujistěte se, že máte nejnovější verzi modulu PowerShellGet nainstalovaný. Spusťte následující příkaz a nainstalujte tak nejnovější:
 
    ```powershell
    Install-Module -Name Azure.Storage -AllowPrerelease –AllowClobber -RequiredVersion "4.4.1-preview"
    ```

2. Odinstalujte všechny předchozí instalace Azure Powershellu.
3. Nainstalujte AzureRM:

    ```powershell
    Install-Module AzureRM –Repository PSGallery –AllowClobber
    ```

4. Nainstalujte modul ve verzi preview:

    ```powershell
    Install-Module -Name Azure.Storage -AllowPrerelease –AllowClobber 
    ```

5. Volání [New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontext) k vytvoření kontextu a zahrnout `-UseConnectedAccount` parametru. 
6. Volání rutiny s identitou služby Azure AD, se rutině předejte kontext.

Následující příklad ukazuje, jak uvádět seznamy blobů v kontejneru v Azure Powershellu pomocí identity Azure AD: 

```powershell
$ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -UseConnectedAccount 
Get-AzureStorageBlob -Container $sample-container -Context $ctx 
```

## <a name="next-steps"></a>Další postup

- Další informace o rolích RBAC pro Azure storage najdete v tématu [Správa přístupových práv k datům úložiště pomocí RBAC (Preview)](storage-auth-aad-rbac.md).
- Další informace o použití spravované identity pro prostředky Azure pomocí služby Azure Storage, najdete v článku [ověřit přístup k objektům BLOB a fronty Azure spravovaných identit pro prostředky Azure (Preview)](storage-auth-aad-msi.md).
- Zjistěte, jak autorizovat přístup ke kontejnerům a front z v rámci aplikace úložiště, najdete v článku [pomocí služby Azure AD s aplikacemi úložiště](storage-auth-aad-app.md).
- Další informace o integraci služby Azure AD pro objekty BLOB Azure a front, najdete v článku na blogu týmu Azure Storage účtovat, [oznamujeme vydání verze Preview služby Azure AD Authentication pro službu Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
