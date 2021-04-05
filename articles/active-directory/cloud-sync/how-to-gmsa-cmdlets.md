---
title: Rutiny prostředí PowerShell pro gMSA Azure AD Connectho zřizování cloudu
description: Naučte se používat rutiny prostředí PowerShell pro Azure AD Connect gMSA Cloud Provisioning.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 411a8e46151a762bcd270fb676f78a91f760ac4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653792"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-gmsa-powershell-cmdlets"></a>Rutiny prostředí PowerShell pro gMSA Azure AD Connectho zřizování cloudu

Účelem tohoto dokumentu je popsat rutiny prostředí PowerShell pro Azure AD Connect gMSA Cloud zřizování. Tyto rutiny vám umožní mít větší členitost na oprávnění, která se vztahují na účet služby (gMSA). Ve výchozím nastavení se Azure AD Connect synchronizace cloudu aplikují všechna oprávnění podobná Azure AD Connect na výchozí gMSA nebo vlastní gMSA. 

Tento dokument se zabývá následujícími rutinami:  

`Set-AADCloudSyncRestrictedPermissions`

`Ste-AADCloudSyncPermissions` 

## <a name="how-to-use-the-cmdlets"></a>Jak používat rutiny:  

Pro použití těchto rutin jsou vyžadovány následující předpoklady.

1. Nainstalujte agenta zřizování. 
2. Importujte modul zřizování agenta PS do relace PowerShellu. 

 ```PowerShell
 Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll"  
 ```
3. Odeberte existující oprávnění.  Chcete-li odebrat všechna existující oprávnění k účtu služby s výjimkou SAMOOBSLUŽNého použití: `Set-AADCloudSyncRestrictedPermission` .  

    Tato rutina vyžaduje parametr s názvem `Credential` , který se může předat, nebo se zobrazí dotaz, jestli se volá bez něho.

    Vytvoření proměnné použití  

   `$credential = Get-Credential` 

   Uživateli se zobrazí výzva k zadání uživatelského jména a hesla. Přihlašovací údaje musí mít minimálně správce domény (doména, ve které je nainstalovaný Agent). může to být i podnikový správce. 

4.  Pak můžete zavolat rutinu a odebrat další oprávnění: 
   ```PowerShell
   Set-AADCloudSyncRestrictedPermissions -Credential $credential 
   ```
5. Nebo můžete jednoduše zavolat 

   `Set-AADCloudSyncRestrictedPermissions` který zobrazí výzvu k zadání přihlašovacích údajů. 

 6.  Přidat konkrétní typ oprávnění.  Přidaná oprávnění jsou stejná jako Azure AD Connect.  Příklady nastavení oprávnění najdete v tématu věnovaném [použití set-AADCloudSyncPermissions](#using-set-aadcloudsyncpermissions) .

## <a name="using-set-aadcloudsyncpermissions"></a>Použití Set-AADCloudSyncPermissions 
`Set-AADCloudSyncPermissions` podporuje následující typy oprávnění, které jsou stejné jako oprávnění, která používá Azure AD Connect. Podporovány jsou následující typy oprávnění: 

|Typ oprávnění|Description|
|-----|-----|
|BasicRead| Azure AD Connect oprávnění k [DisBasicRead](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#configure-basic-read-only-permissions)|
|PasswordHashSync|Azure AD Connect oprávnění k [DisPasswordHashSync](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-hash-synchronization)|
|PasswordWriteBack|Azure AD Connect oprávnění k [DisPasswordWriteBack](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-writeback)|
|HybridExchangePermissions|Azure AD Connect oprávnění k [DisHybridExchangePermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-hybrid-deployment)| 
|ExchangeMailPublicFolderPermissions| Azure AD Connect oprávnění k [DisExchangeMailPublicFolderPermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-mail-public-folders-preview)| 
|CloudHR| Použije "úplné řízení" na "objekty objektů následníka" a "vytvořit/odstranit objekty uživatele" u tohoto objektu a všech potomků objektů| 
|Vše|Přidá všechna výše uvedená oprávnění.| 



AADCloudSyncPermissions můžete použít jedním ze dvou způsobů:
- [Udělení určitého oprávnění všem nakonfigurovaným doménám](#grant-a-certain-permission-to-all-configured-domains) 
- [Udělení určitého oprávnění konkrétní doméně](#grant-a-certain-permission-to-a-specific-domain) 
## <a name="grant-a-certain-permission-to-all-configured-domains"></a>Udělení určitého oprávnění všem nakonfigurovaným doménám 
Udělení určitých oprávnění všem nakonfigurovaným doménám bude vyžadovat použití účtu správce organizace.


 ```PowerShell
Set-AADCloudSyncPermissions -PermissionType “Any mentioned above” -EACredential $credential (prepopulated same as above [$credential = Get-Credential]) 
```

## <a name="grant-a-certain-permission-to-a-specific-domain"></a>Udělení určitého oprávnění konkrétní doméně 
Udělení určitých oprávnění konkrétní doméně vyžaduje použití, a to minimálně účet správce domény pro doménu, kterou se pokoušíte přidat.


 ```PowerShell
Set-AADCloidSyncPermissions -PermissionType “Any mentioned above” -TargetDomain “FQDN of domain” (has to be already configured through wizard) -TargetDomaincredential $credential(same as above) 
```
 

Poznámka: 1. Přihlašovací údaje musí mít minimálně podnikového správce. 

Pro 2. Přihlašovací údaje můžou být buď správce domény nebo Podnikový správce. 

  

