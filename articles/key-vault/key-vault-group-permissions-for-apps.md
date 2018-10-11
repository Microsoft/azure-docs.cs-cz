---
title: Udělení oprávnění k velkému počtu aplikací pro přístup k trezoru klíčů Azure | Dokumentace Microsoftu
description: Zjistěte, jak udělit oprávnění k velkému počtu aplikací pro přístup k trezoru klíčů
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 785d4e40-fb7b-485a-8cbc-d9c8c87708e6
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: ambapat
ms.openlocfilehash: 639dfb6e3231a5eba3d6ecb9cd0198f5718b4aef
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079017"
---
# <a name="grant-permission-to-many-applications-to-access-a-key-vault"></a>Udělit oprávnění k velkému počtu aplikací pro přístup k trezoru klíčů

## <a name="q-i-have-several-applications-that-need-to-access-a-key-vault-how-can-i-give-these-applications-up-to-1024-access-to-key-vault"></a>Otázka: Mám několik aplikací, které potřebují přístup k trezoru klíčů, jak můžete mi poskytnout tyto aplikace (až 1 024) přístup do služby Key Vault?

Zásady řízení přístupu k trezoru klíčů podporuje až 1 024 položky. Ale můžete vytvořit skupiny zabezpečení služby Azure Active Directory. Přidejte všechny přidružené instanční objekty do této skupiny zabezpečení a potom jim udělit přístup k této skupině zabezpečení do služby Key Vault.

Tady jsou požadavky:
* [Instalace modulu Azure Active Directory V2 PowerShell](https://www.powershellgallery.com/packages/AzureAD).
* [Nainstalujte prostředí Azure PowerShell](/powershell/azure/overview).
* Spustit následující příkazy, budete potřebovat oprávnění k vytvoření nebo úpravě skupiny v tenantovi Azure Active Directory. Pokud nemáte příslušná oprávnění, budete muset kontaktovat svého správce Azure Active Directory. Zobrazit [o službě Azure Key Vault klíče, tajné kódy a certifikáty](about-keys-secrets-and-certificates.md) podrobnosti o službě Key Vault přístup k oprávnění zásad.

Nyní spusťte následující příkazy v prostředí PowerShell.

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
Set-AzureRmKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId `
-PermissionsToKeys decrypt,encrypt,unwrapKey,wrapKey,verify,sign,get,list,update,create,import,delete,backup,restore,recover,purge `
–PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge `
–PermissionsToCertificates get,list,delete,create,import,update,managecontacts,getissuers,listissuers,setissuers,deleteissuers,manageissuers,recover,purge,backup,restore `
-PermissionsToStorage get,list,delete,set,update,regeneratekey,getsas,listsas,deletesas,setsas,recover,backup,restore,purge 
 
# Of course you can adjust the permissions as required 
```

Pokud je potřeba udělit jinou sadu oprávnění pro skupinu aplikací, vytvořte samostatnou skupinu zabezpečení Azure Active Directory pro takové aplikace.

## <a name="next-steps"></a>Další postup

Další informace o tom, jak [zabezpečení trezoru klíčů](key-vault-secure-your-key-vault.md).
