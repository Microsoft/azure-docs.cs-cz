---
title: Příklady prostředí PowerShell pro správu skupin – Azure Active Directory | Dokumentace Microsoftu
description: Tato stránka obsahuje příklady prostředí PowerShell vám pomohou při správě skupin v Azure Active Directory
keywords: Prostředí PowerShell Azure AD, Azure Active Directory, Správa skupin, skupin
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 99facedec04eeb2f1626d502b89f3e8c60d07424
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55512036"
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Rutiny verze 2 Azure Active Directory pro správu skupin

> [!div class="op_single_selector"]
> * [Azure Portal](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
> * [PowerShell](groups-settings-v2-cmdlets.md)
>
>

Tento článek obsahuje příklady použití Powershellu ke správě skupin v Azure Active Directory (Azure AD).  Je také vysvětluje, jak nastavit pomocí modulu Azure AD PowerShell. Nejprve je nutné [stažení modulu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>Instalace modulu Azure AD Powershellu
Instalace modulu Azure AD PowerShell, použijte následující příkazy:

    PS C:\Windows\system32> install-module azuread
    PS C:\Windows\system32> import-module azuread

Pokud chcete ověřit, že modul je připravené k použití, použijte následující příkaz:

    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}

Nyní můžete začít používat rutiny v modulu. Úplný popis rutin v modulu Azure AD, najdete v online dokumentaci pro [Azure Active Directory PowerShell verze 2](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="connect-to-the-directory"></a>Připojení k adresáři
Předtím, než můžete začít spravovat skupiny pomocí rutin Powershellu pro Azure AD, musíte se připojit relace prostředí PowerShell k adresáři, který chcete spravovat. Použijte následující příkaz:

    PS C:\Windows\system32> Connect-AzureAD

Rutina vás vyzve k zadání přihlašovacích údajů, které chcete použít pro přístup k adresáři. V tomto příkladu používáme karen@drumkit.onmicrosoft.com pro přístup k adresáři ukázku. Vrátí rutina potvrzení, která se zobrazí, že relace se úspěšně připojila k adresáři:

    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…

Nyní můžete začít pomocí rutin Azure AD ke správě skupin ve vašem adresáři.

## <a name="retrieve-groups"></a>Načtení skupin
Pokud chcete načíst existující skupiny z adresáře, použijte rutinu Get-AzureADGroups. 

Pokud chcete načíst všechny skupiny v adresáři, použijte rutinu bez parametrů:

    PS C:\Windows\system32> get-azureadgroup

Rutina vrátí všechny skupiny v připojeném adresáři.

Načíst konkrétní skupinu, u kterého zadáte ID objektu skupiny můžete použít parametr - objectID:

    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b

Rutina nyní vrátí skupinu, jejíž objectID odpovídá hodnotě parametru, který jste zadali:

    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Můžete vyhledat konkrétní skupiny pomocí parametru - filtru. Tento parametr přijímá klauzuli filtru ODATA a vrací všechny skupiny, které odpovídají filtru, stejně jako v následujícím příkladu:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

> [!NOTE] 
> Rutiny Azure AD Powershellu implementovat standard dotazu OData. Další informace najdete v tématu **$filter** v [možností dotazu systému OData pomocí koncového bodu OData](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter).

## <a name="create-groups"></a>Vytvoření skupin
Chcete-li vytvořit novou skupinu ve vašem adresáři, použijte rutinu New-AzureADGroup. Tato rutina vytvoří novou skupinu zabezpečení s názvem "Marketing":

    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"

## <a name="update-groups"></a>Skupiny aktualizací
Pokud chcete aktualizovat existující skupinu, použijte rutinu Set-AzureADGroup. V tomto příkladu měníme vlastnost DisplayName skupiny "Správci Intune." Nejprve hledáte skupiny pomocí rutiny Get-AzureADGroup a filtrovat pomocí atributu DisplayName:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

V dalším kroku měníme vlastnosti Popis na novou hodnotu "Správci Intune zařízení":

    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"

Teď Pokud se nám najít skupině znovu, vidíme, že vlastnost Description aktualizuje tak, aby odrážely novou hodnotu:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

## <a name="delete-groups"></a>Odstranit skupiny
Odstranit skupiny z adresáře, použijte rutinu Remove-AzureADGroup následujícím způsobem:

    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b

## <a name="manage-group-membership"></a>Spravovat členství ve skupinách 
### <a name="add-members"></a>Přidat členy
Přidání nových členů do skupiny, použijte rutinu Add-AzureADGroupMember. Tento příkaz přidá členy do skupiny Správci Intune, které jsme použili v předchozím příkladu:

    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Parametr - ObjectId je ID objektu skupiny, do které budeme chtít přidat člena a RefObjectId – je ID objektu uživatele, kterého chcete přidat jako členy do skupiny.

### <a name="get-members"></a>Získat členy
Načíst existující členy skupiny, použijte rutinu Get-AzureADGroupMember jako v následujícím příkladu:

    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User

### <a name="remove-members"></a>Odebrat členy
Odebrat člena, který jsme dříve přidán do skupiny, použijte rutinu Remove-AzureADGroupMember, jak je znázorněno zde:

    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

### <a name="verify-members"></a>Ověřte členy
Pokud chcete ověřit uživatele členství ve skupinách, použijte rutinu AzureADGroupIdsUserIsMemberOf vyberte. Tato rutina použije jako svoje parametry ObjectId uživatele, pro který chcete zkontrolovat členství ve skupinách a seznamu skupin, pro který chcete zkontrolovat členství. Seznam skupin musí být zadaná ve formě proměnné komplexního typu "Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck", proto jsme vytvořit proměnnou daného typu:

    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck

V dalším kroku zadáme hodnoty pro identifikátory skupiny k vrácení se změnami atribut "Identifikátory skupiny" této komplexní proměnné:

    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"

Teď když chceme zkontrolovat členství ve skupinách uživatele s ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea proti skupin v $g, bychom měli použít:

    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}


Vrácená hodnota je seznam skupin, kterých je tento uživatel členem. Můžete také použít tuto metodu ke kontrole kontakty, skupinám nebo instančním objektům členství pro daný seznam skupin, pomocí vyberte-AzureADGroupIdsContactIsMemberOf, vyberte AzureADGroupIdsGroupIsMemberOf nebo Vyberte AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="disable-group-creation-by-your-users"></a>Zakázat vytvoření skupiny uživatelů
Uživatelé bez oprávnění správce můžete zabránit ve vytváření skupin zabezpečení. Výchozí chování v Microsoft Online Directory Services (MSODS) je umožnit bez oprávnění správce uživatelům vytvářet skupiny, zda je povolena také Samoobslužná správa skupin (SSGM). Nastavení SSGM řídí chování pouze v přístupovém panelu Moje aplikace. 

Chcete-li zakázat vytvoření skupiny pro uživatele bez oprávnění správce:

1. Ověřte, že uživatelé bez oprávnění správce můžou vytvářet skupiny:
   
  ```
  PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
  ```
  
2. Vrátí-li `UsersPermissionToCreateGroupsEnabled : True`, pak uživatelé bez oprávnění správce můžou vytvářet skupiny. Chcete zakázat tuto funkci:
  
  ``` 
  Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
  ```
  
## <a name="manage-owners-of-groups"></a>Správa vlastníků skupin
Přidat vlastníky ke skupině, použijte rutinu Add-AzureADGroupOwner:

    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Parametr - ObjectId je ID objektu skupiny, do které budeme chtít přidat vlastníka a RefObjectId – je ID objektu uživatele, kterého chcete přidat jako vlastníka skupiny.

K načtení vlastníků skupiny, použijte rutinu Get-AzureADGroupOwner:

    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

Rutina vrátí seznam vlastníků pro zadanou skupinu:

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User

Pokud chcete odebrání vlastníka ze skupiny, použijte rutinu Remove-AzureADGroupOwner:

    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67

## <a name="reserved-aliases"></a>Vyhrazené aliasy 
Když skupina se vytvoří, jisti, že koncové body povolí koncovému uživateli zadat mailNickname nebo alias se použije jako součást e-mailovou adresu skupiny. Globální správce Azure AD lze vytvořit pouze skupiny s následující aliasy e-mailu s vysokou úrovní oprávnění. 
  
* urážlivý příspěvek 
* admin 
* Správce 
* hostmaster 
* majordomo 
* správce pošty 
* kořen 
* zabezpečení 
* security 
* Správce protokolu SSL 
* správce webového serveru 

## <a name="next-steps"></a>Další postup
Můžete najít další dokumentaci k Azure Active Directory PowerShell na [rutiny Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

* [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](../fundamentals/active-directory-manage-groups.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Integrování místních identit do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
