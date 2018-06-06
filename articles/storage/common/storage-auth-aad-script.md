---
title: Spusťte příkazy příkazového řádku Azure CLI nebo prostředí PowerShell v rámci Azure AD identity pro přístup k úložišti Azure (Preview) | Microsoft Docs
description: Azure PowerShell a rozhraní příkazového řádku podporu přihlášení pomocí služby Azure AD identity ke spuštění příkazů v Azure Storage kontejnery a fronty a jejich data. Přístupový token je zadaný pro relaci a používat k autorizaci volání operace. Oprávnění závisí na role přiřazené k identitě Azure AD.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/30/2018
ms.author: tamram
ms.openlocfilehash: 27397d490902625d10e54ea17a9e534fbfbca8cd
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757498"
---
# <a name="use-an-azure-ad-identity-to-access-azure-storage-with-cli-or-powershell-preview"></a>Používat Azure AD identity pro přístup k Azure Storage pomocí rozhraní CLI nebo Powershellu (Preview)

Úložiště Azure poskytuje preview rozšíření pro rozhraní příkazového řádku Azure a prostředí PowerShell, které vám umožní přihlásit a spuštění příkazů skriptu s identitou, Azure Active Directory (Azure AD). Identita služby Azure AD může být uživatele, skupinu nebo objekt aplikace služby, nebo může být [identita spravované služby](../../active-directory/managed-service-identity/overview.md). Můžete přiřadit oprávnění pro přístup k prostředkům úložiště do Azure AD identity prostřednictvím řízení přístupu na základě role (RBAC). Další informace o rolích RBAC ve službě Azure Storage najdete v tématu [spravovat přístupová práva ke službě Azure Storage data pomocí RBAC (Preview)](storage-auth-aad-rbac.md).

Když se přihlásit k Azure CLI nebo prostředí PowerShell s Azure AD identity, se pro přístup k úložišti Azure v rámci této identity vrátí přístupový token. Tento token poté automaticky použije rozhraní CLI nebo Powershellu k autorizaci operace Azure Storage. Pro podporované operace již nepotřebujete předávání klíč účtu nebo tokenu SAS pomocí příkazu.

> [!IMPORTANT]
> Tato verze preview je určeno pouze pro nevýrobní prostředí. Produkční smlouvy o úrovni služeb (SLA) nebudou dostupné, dokud integrace Azure AD pro Azure Storage je deklarovaná všeobecně dostupná. Pokud pro váš scénář není dosud podporován integrace Azure AD, nadále používat sdílený klíč autorizace nebo tokeny SAS ve svých aplikacích. Další informace o verzi preview najdete v tématu [ověření přístupu k úložišti Azure pomocí služby Azure Active Directory (Preview)](storage-auth-aad.md).
>
> Ve verzi Preview přiřazení rolí pro RBAC může trvat až pět minut, aby se rozšířily změny.
>
> Integrace služby Azure AD s Azure Storage vyžaduje pomocí protokolu HTTPS pro operace úložiště Azure.

## <a name="supported-operations"></a>Podporované operace

Rozšíření preview jsou podporována pro operace v kontejnery a fronty. Operací, které může volat závisí na oprávněních udělených k identitě Azure AD, se kterým se přihlásit k Azure CLI nebo prostředí PowerShell. Oprávnění pro kontejnery Azure Storage nebo fronty jsou přiřazeny prostřednictvím řízení přístupu na základě role (RBAC). Například pokud čtecí modul dat role je přiřazená identitu, potom můžete spustit příkazy skriptu, které číst data z kontejneru nebo fronty. Pokud roli přispěvatele dat je přiřazen k identitu, můžete spustit příkazy skriptu, které čtení, zápisu nebo odstranit kontejner nebo fronty nebo data, která obsahují. 

Podrobnosti o oprávněních nutných pro každou operaci Azure Storage na kontejneru nebo fronty najdete v tématu [oprávnění k volání operace REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).  

## <a name="call-cli-commands-with-an-azure-ad-identity"></a>Volání rozhraní příkazového řádku s identitou služby Azure AD

Chcete-li nainstalovat rozšíření preview pro rozhraní příkazového řádku Azure:

1. Přesvědčte se, že máte nainstalovanou verzi rozhraní příkazového řádku Azure 2.0.32 nebo novější. Spustit `az --version` zkontrolujte nainstalovanou verzi.
2. Spusťte následující příkaz pro instalaci rozšíření preview: 

    ```azurecli
    az extension add -n storage-preview
    ```

Přidá nový náhled rozšíření `--auth-mode` parametr podporované příkazy:

- Nastavte `--auth-mode` parametru `login` se přihlásit pomocí služby Azure AD identity.
- Nastavte `--auth-mode` parametr pro starší verze `key` jsou zadat hodnotu pro pokus o dotazování pro klíče Pokud účet žádné parametry ověřování pro účet. 

Například ke stažení objektů blob v Azure AD identity pomocí rozhraní příkazového řádku Azure, nejprve spusťte `az login`, pak volání příkazu s `--auth-mode` nastavena na `login`:

```azurecli
az login
az storage blob download --account-name storagesamples --container sample-container --name myblob.txt --file myfile.txt --auth-mode login 
```

Proměnné prostředí, které jsou přidružené k `--auth-mode` parametr `AZURE_STORAGE_AUTH_MODE`.

## <a name="call-powershell-commands-with-an-azure-ad-identity"></a>Volání příkazy prostředí PowerShell s Azure AD identity

Použití Azure PowerShell a přihlaste se pomocí Azure AD identity:

1. Ujistěte se, že máte nejnovější verzi PowerShellGet nainstalována. Spusťte následující příkaz pro instalaci nejnovější:
 
    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

2. Odinstalujte všechny předchozí instalace prostředí Azure PowerShell.
3. Nainstalujte AzureRM:

    ```powershell
    Install-Module AzureRM –Repository PSGallery –AllowClobber
    ```

4. Nainstalujte modul pro verzi preview:

    ```powershell
    Install-Module-Name Azure.Storage-RequiredVersion 4.4.0-AllowPrerelease –AllowClobber -Repository PSGallery -Force 
    ```

5. Volání [New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontext) rutiny vytvoření kontextu, a zahrnutí `-UseConnectedAccount` parametr. 
6. K volání rutiny s identitou služby Azure AD, se rutině předejte kontext.

Následující příklad ukazuje, jak do seznamu objektů BLOB v kontejneru z prostředí Azure PowerShell pomocí Azure AD identity: 

```powershell
$ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -UseConnectedAccount 
Get-AzureStorageBlob -Container $sample-container -Context $ctx 
```

## <a name="next-steps"></a>Další postup

- Další informace o rolích RBAC pro Azure storage najdete v tématu [spravovat přístupová práva k úložišti dat pomocí RBAC (Preview)](storage-auth-aad-rbac.md).
- Další informace o použití identita spravované služby s Azure Storage najdete v tématu [ověřit s Azure AD z Azure spravované služby Identity (Preview)](storage-auth-aad-msi.md).
- Informace o tom, k autorizaci přístupu ke kontejnerům a fronty ze v rámci aplikace úložiště, najdete v části [používání Azure AD s aplikacemi úložiště](storage-auth-aad-app.md).
- Další informace o integraci služby Azure AD pro front a objektů BLOB služby Azure najdete v tématu blog týmu Azure Storage, post, [uvedení ověřování Preview služby Azure AD pro Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
