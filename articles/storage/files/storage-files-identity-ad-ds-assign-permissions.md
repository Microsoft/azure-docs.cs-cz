---
title: Řízení přístupu ke sdíleným složkám Azure – místní služba AD DS ověřování
description: Naučte se, jak přiřadit oprávnění Active Directory Domain Services identitě, která představuje váš účet úložiště. To umožňuje řídit přístup pomocí ověřování na základě identity.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: 287bd02a11c71fbdd29b28b5ec9fc8424a477fea
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320349"
---
# <a name="part-two-assign-share-level-permissions-to-an-identity"></a>Druhá část: přiřazení oprávnění na úrovni sdílení k identitě

Než začnete s tímto článkem, ujistěte se, že jste dokončili předchozí článek, a [Povolte pro svůj účet služba AD DS ověřování](storage-files-identity-ad-ds-enable.md).

Po povolení ověřování Active Directory Domain Services (služba AD DS) na svém účtu úložiště musíte nakonfigurovat oprávnění na úrovni sdílení, aby bylo možné získat přístup ke sdíleným složkám souborů. Identita, ke které chcete získat přístup k prostředkům sdílené složky Azure, musí být hybridní identitou, která existuje v služba AD DS i v Azure AD. Řekněme například, že máte uživatele ve vašem služba AD DS, který je a jste se user1@onprem.contoso.com synchronizoval s Azure AD jako při user1@contoso.com použití Azure AD Connect synchronizace. Pokud chcete tomuto uživateli dovolit přístup k souborům Azure, musíte přiřadit oprávnění na úrovni sdílené složky user1@contoso.com . Stejný koncept se vztahuje na skupiny nebo objekty služby. Z tohoto důvodu je třeba synchronizovat uživatele a skupiny z služba AD DS do služby Azure AD pomocí Azure AD Connect synchronizace. 

Oprávnění na úrovni sdílené složky musí být přiřazená identitě Azure AD, která představuje stejného uživatele nebo skupinu ve vašem služba AD DS, aby podporovala služba AD DS ověřování pro sdílenou složku Azure. Ověřování a autorizace proti identitám, které existují pouze ve službě Azure AD, jako jsou například spravované identity Azure (MSIs), nejsou podporovány při ověřování služba AD DS. Tento článek ukazuje, jak přiřadit k identitě oprávnění na úrovni sdílené složky pro sdílenou složku.


## <a name="share-level-permissions"></a>Oprávnění na úrovni sdílené složky

Obecně doporučujeme, abyste pro skupinu Azure AD, která představuje skupinu uživatelů a identit, používali oprávnění na úrovni sdílené složky pro správu vysoké úrovně přístupu ke skupině Azure AD, a to pomocí seznamů ACL systému Windows pro podrobné řízení přístupu k úrovni adresáře nebo souboru. 

Existují tři předdefinované role Azure pro udělení oprávnění na úrovni sdílení pro uživatele:

- **Čtečka sdílené složky SMB souborů úložiště** umožňuje přístup pro čtení v Azure Storage sdílené složky přes protokol SMB.
- **Přispěvatel sdílené složky SMB soubor úložiště** umožňuje přístup ke čtení, zápisu a odstraňování v Azure Storage sdílených složkách přes SMB.
- **Soubor úložiště data sdílené složky SMB se zvýšenými oprávněními** umožňuje číst, zapisovat, odstraňovat a upravovat seznamy řízení přístupu (ACL) Windows v Azure Storage sdílených složkách přes SMB.

> [!IMPORTANT]
> Úplná Správa sdílené složky, včetně možnosti převzít vlastnictví souboru, vyžaduje použití klíče účtu úložiště. Pro přihlašovací údaje Azure AD se nepodporuje administrativní řízení.

Pomocí Azure Portal, Azure PowerShell nebo rozhraní příkazového řádku Azure můžete přiřadit předdefinované role k identitě uživatele Azure AD pro udělení oprávnění na úrovni sdílené složky.

## <a name="assign-an-azure-role"></a>Přiřazení role Azure

### <a name="azure-portal"></a>portál Azure

Pokud chcete přiřadit roli Azure k identitě Azure AD, použijte [Azure Portal](https://portal.azure.com)podle těchto kroků:

1. V Azure Portal přejdete do sdílené složky nebo [vytvoříte sdílenou složku](storage-how-to-create-file-share.md).
1. Vyberte **Access Control (IAM)**.
1. Vyberte **Přidat přiřazení role** .
1. V okně **Přidat přiřazení role** vyberte příslušnou integrovanou roli (soubor úložiště, sdílenou složku SMB pro sdílení souborů úložiště, přispěvatel sdílené složky SMB) ze seznamu **rolí** . Nechte **přiřadit přístup k** výchozímu nastavení: **uživatel, skupina nebo instanční objekt služby Azure AD**. Vyberte cílovou identitu Azure AD podle jména nebo e-mailové adresy. **Vybraná identita Azure AD musí být hybridní identitou a nemůže být Cloudová identita.** To znamená, že stejná identita je také zastoupena v služba AD DS.
1. Výběrem **Uložit** dokončete operaci přiřazení role.

### <a name="powershell"></a>PowerShell

Následující ukázka prostředí PowerShell ukazuje, jak přiřadit k identitě Azure AD roli Azure na základě přihlašovacího jména. Další informace o přiřazování rolí Azure pomocí PowerShellu najdete v tématu [Správa přístupu pomocí RBAC a Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Před spuštěním následujícího ukázkového skriptu nahraďte hodnoty zástupných symbolů, včetně závorek.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

### <a name="cli"></a>Rozhraní příkazového řádku
  
Následující příkaz CLI 2,0 přiřadí roli Azure AD identitě na základě přihlašovacího jména. Další informace o přiřazování rolí Azure pomocí rozhraní příkazového řádku Azure najdete v tématu [Správa přístupu pomocí RBAC a Azure CLI](../../role-based-access-control/role-assignments-cli.md). 

Před spuštěním následujícího ukázkového skriptu Nezapomeňte nahradit hodnoty zástupných symbolů, včetně závorek, vlastními hodnotami.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="next-steps"></a>Další kroky

Teď, když máte přiřazená oprávnění na úrovni sdílené složky, musíte nakonfigurovat oprávnění k adresářům a souborům. Přejděte k dalšímu článku.

[Třetí část: Konfigurace oprávnění adresářů a souborů přes SMB](storage-files-identity-ad-ds-configure-permissions.md)
