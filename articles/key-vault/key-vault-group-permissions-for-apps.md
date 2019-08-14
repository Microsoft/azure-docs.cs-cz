---
title: Udělení oprávnění mnoha aplikacím pro přístup k trezoru klíčů Azure – Azure Key Vault | Microsoft Docs
description: Zjistěte, jak udělit oprávnění k mnoha aplikacím pro přístup k trezoru klíčů.
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 07ee544057ffeb0a5859cc771b124523ec79c9c0
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976404"
---
# <a name="grant-several-applications-access-to-a-key-vault"></a>Udělení přístupu k trezoru klíčů několika aplikacím

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zásady řízení přístupu se dají použít k udělení přístupu k trezoru klíčů několika aplikacím. Zásady řízení přístupu můžou podporovat až 1024 aplikací a nakonfigurují se takto:

1. Vytvořte Azure Active Directory skupinu zabezpečení. 
2. Do skupiny zabezpečení přidejte všechny objekty přidružené k aplikacím.
3. Udělte skupině zabezpečení přístup k vašemu Key Vault.

## <a name="prerequisites"></a>Požadavky

Tady jsou požadavky:
* [Nainstalujte prostředí Azure PowerShell](/powershell/azure/overview).
* [Nainstalujte modul Azure Active Directory v2 PowerShell](https://www.powershellgallery.com/packages/AzureAD).
* Oprávnění k vytváření a úpravám skupin v tenantovi Azure Active Directory. Pokud nemáte oprávnění, možná budete muset kontaktovat správce Azure Active Directory. Podrobnosti o oprávněních zásad přístupu Key Vault najdete v tématu [informace o Azure Key Vault klíčích, tajných klíčích a certifikátech](about-keys-secrets-and-certificates.md) .

## <a name="granting-key-vault-access-to-applications"></a>Udělení přístupu Key Vault k aplikacím

V prostředí PowerShell spusťte následující příkazy:

```powershell
# Connect to Azure AD 
Connect-AzureAD 
 
# Create Azure Active Directory Security Group 
$aadGroup = New-AzureADGroup -Description "Contoso App Group" -DisplayName "ContosoAppGroup" -MailEnabled 0 -MailNickName none -SecurityEnabled 1 
 
# Find and add your applications (ServicePrincipal ObjectID) as members to this group 
$spn = Get-AzureADServicePrincipal –SearchString "ContosoApp1" 
Add-AzureADGroupMember –ObjectId $aadGroup.ObjectId -RefObjectId $spn.ObjectId 
 
# You can add several members to this group, in this fashion. 
 
# Set the Key Vault ACLs 
Set-AzKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId `
-PermissionsToKeys decrypt,encrypt,unwrapKey,wrapKey,verify,sign,get,list,update,create,import,delete,backup,restore,recover,purge `
–PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge `
–PermissionsToCertificates get,list,delete,create,import,update,managecontacts,getissuers,listissuers,setissuers,deleteissuers,manageissuers,recover,purge,backup,restore `
-PermissionsToStorage get,list,delete,set,update,regeneratekey,getsas,listsas,deletesas,setsas,recover,backup,restore,purge 
 
# Of course you can adjust the permissions as required 
```

Pokud potřebujete pro skupinu aplikací udělit jinou sadu oprávnění, vytvořte pro takové aplikace samostatnou skupinu zabezpečení Azure Active Directory.

## <a name="next-steps"></a>Další postup

Přečtěte si další informace o tom, jak [Trezor klíčů zabezpečit](key-vault-secure-your-key-vault.md).
