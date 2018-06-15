---
title: Udělení oprávnění ke mnoho aplikací pro přístup Azure trezoru klíčů | Microsoft Docs
description: Zjistěte, jak chcete udělit oprávnění k mnoha aplikace pro přístup k trezoru klíčů
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
ms.topic: article
ms.date: 12/01/2016
ms.author: ambapat
ms.openlocfilehash: ddeaf184138bd48d324799ddb45248b0a0ee8eeb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30174967"
---
# <a name="grant-permission-to-many-applications-to-access-a-key-vault"></a>Udělit oprávnění k mnoha aplikace pro přístup k trezoru klíčů

## <a name="q-i-have-several-over-16-applications-that-need-to-access-a-key-vault-since-key-vault-only-allows-16-access-control-entries-how-can-i-achieve-that"></a>Otázka: je nutné několik (více než 16) aplikace, které potřebují přístup k trezoru klíčů. Vzhledem k tomu, že Key Vault umožňuje jenom 16 položek řízení přístupu, co mám dělat?

Zásada řízení přístupu Key Vault podporuje pouze 16 položky. Ale můžete vytvořit skupiny zabezpečení služby Azure Active Directory. Přidejte všechny objekty přidružené služby do této skupiny zabezpečení a pak udělují přístup k této skupině zabezpečení Key Vault.

Tady jsou požadavky:
* [Instalace modulu Azure Active Directory V2 PowerShell](https://www.powershellgallery.com/packages/AzureAD).
* [Nainstalujte prostředí Azure PowerShell](/powershell/azure/overview).
* Pokud chcete spustit následující příkazy, potřebujete oprávnění k vytvoření nebo úpravě skupin v klientovi služby Azure Active Directory. Pokud nemáte oprávnění, budete muset obraťte se na správce služby Azure Active Directory.

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
Set-AzureRmKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId -PermissionsToKeys all –PermissionsToSecrets all –PermissionsToCertificates all 
 
# Of course you can adjust the permissions as required 
```

Pokud potřebujete udělit jinou sadu oprávnění pro skupinu aplikací, vytvořte samostatnou skupinu zabezpečení služby Azure Active Directory pro tyto aplikace.

## <a name="next-steps"></a>Další postup

Další informace o tom, jak [zabezpečit váš trezor klíčů](key-vault-secure-your-key-vault.md).
