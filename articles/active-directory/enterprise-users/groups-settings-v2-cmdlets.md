---
title: Příklady prostředí PowerShell v2 pro správu skupin – Azure AD | Microsoft Docs
description: Tato stránka poskytuje příklady prostředí PowerShell, které vám pomůžou se správou skupin v Azure Active Directory
keywords: Azure AD, Azure Active Directory, PowerShell, skupiny, Správa skupin
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12613362d9fc67dea14b41ece19a34febb489dda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96860639"
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Rutiny Azure Active Directory verze 2 pro správu skupin

> [!div class="op_single_selector"]
> - [Azure Portal](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
> - [PowerShell](../enterprise-users/groups-settings-v2-cmdlets.md)
>
>

Tento článek obsahuje příklady použití prostředí PowerShell ke správě skupin ve službě Azure Active Directory (Azure AD).  Dozvíte se taky, jak si nastavit modul Azure AD PowerShell. Nejdřív je nutné [Stáhnout modul Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>Instalace modulu Azure AD PowerShell

K instalaci modulu Azure AD PowerShell použijte následující příkazy:

```powershell
    PS C:\Windows\system32> install-module azuread
    PS C:\Windows\system32> import-module azuread
```

Chcete-li ověřit, zda je modul připravený k použití, použijte následující příkaz:

```powershell
    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

Teď můžete začít používat rutiny v modulu. Úplný popis rutin v modulu Azure AD najdete v online referenční dokumentaci pro [Azure Active Directory PowerShell verze 2](/powershell/azure/active-directory/install-adv2).

> [!NOTE]
> Rutiny Azure AD PowerShell nefungují s novým PowerShellem 7, protože jsou založené na .NET Core. Víme, že právě probíhá aktualizace. Počínaje teď doporučujeme použít modul Windows PowerShell 5. x, který se použije pro operace Azure AD PowerShellu. 


## <a name="connect-to-the-directory"></a>Připojit k adresáři

Než budete moct začít spravovat skupiny pomocí rutin Azure AD PowerShellu, musíte připojit relaci PowerShellu k adresáři, který chcete spravovat. Použijte následující příkaz:

```powershell
    PS C:\Windows\system32> Connect-AzureAD
```

Rutina vás vyzve k zadání přihlašovacích údajů, které chcete použít pro přístup k adresáři. V tomto příkladu používáme karen@drumkit.onmicrosoft.com pro přístup k demonstračnímu adresáři. Rutina vrátí potvrzení pro zobrazení relace, která byla úspěšně připojena k vašemu adresáři:

```powershell
    Account                       Environment Tenant ID
    -------                       ----------- ---------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…
```

Teď můžete začít používat rutiny AzureAD ke správě skupin ve vašem adresáři.

## <a name="retrieve-groups"></a>Načtení skupin

K načtení existujících skupin z adresáře použijte rutinu Get-AzureADGroups. 

Pokud chcete načíst všechny skupiny v adresáři, použijte rutinu bez parametrů:

```powershell
    PS C:\Windows\system32> get-azureadgroup
```

Rutina vrátí všechny skupiny v připojeném adresáři.

Pomocí parametru-objectID můžete načíst konkrétní skupinu, pro kterou zadáte objectID skupiny:

```powershell
    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b
```

Rutina nyní vrátí skupinu, jejíž identifikátor objectID odpovídá hodnotě parametru, který jste zadali:

```powershell
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
```

Konkrétní skupinu můžete vyhledat pomocí parametru-Filter. Tento parametr přebírá klauzuli filtru ODATA a vrátí všechny skupiny, které odpovídají filtru, jak je uvedeno v následujícím příkladu:

```powershell
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
```

> [!NOTE]
> Rutiny PowerShellu pro Azure AD implementují Standard dotazů OData. Další informace najdete v tématu **$Filter** v [možnostech dotazů na systém OData pomocí koncového bodu OData](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)#BKMK_filter).

## <a name="create-groups"></a>Vytvoření skupin

Pokud chcete ve svém adresáři vytvořit novou skupinu, použijte rutinu New-AzureADGroup. Tato rutina vytvoří novou skupinu zabezpečení nazvanou "marketing":

```powershell
    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"
```

## <a name="update-groups"></a>Aktualizovat skupiny

Chcete-li aktualizovat existující skupinu, použijte rutinu Set-AzureADGroup. V tomto příkladu měníme vlastnost displayName skupiny Administrators služby Intune. Nejdřív vyhledáme skupinu pomocí rutiny Get-AzureADGroup a pomocí atributu DisplayName použijeme filtr:

```powershell
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
```

V dalším kroku měníme vlastnost Description na novou hodnotu "Správci zařízení Intune":

```powershell
    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"
```

Pokud teď znovu vyhledáme skupinu, uvidíme, že je vlastnost Description aktualizována, aby odrážela novou hodnotu:

```powershell
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
```

## <a name="delete-groups"></a>Odstranění skupin

Pokud chcete odstranit skupiny z adresáře, použijte rutinu Remove-AzureADGroup následujícím způsobem:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b
```

## <a name="manage-group-membership"></a>Správa členství ve skupinách

### <a name="add-members"></a>Přidání členů

Chcete-li přidat nové členy do skupiny, použijte rutinu Add-AzureADGroupMember. Tento příkaz přidá člena do skupiny Správci Intune, kterou jsme použili v předchozím příkladu:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

Parametr-ObjectId je ObjectID skupiny, do které chceme přidat člena, a parametr-RefObjectId je ObjectID uživatele, který chceme přidat jako člena do skupiny.

### <a name="get-members"></a>Načíst členy

Chcete-li získat existující členy skupiny, použijte rutinu Get-AzureADGroupMember, jako v tomto příkladu:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User
```

### <a name="remove-members"></a>Odebrání členů

Chcete-li odebrat člena, který jste dříve přidali do skupiny, použijte rutinu Remove-AzureADGroupMember, jak je znázorněno zde:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

### <a name="verify-members"></a>Ověřit členy

Pokud chcete ověřit členství uživatele ve skupině, použijte rutinu Select-AzureADGroupIdsUserIsMemberOf. Tato rutina přijímá jako své parametry identifikátor ObjectId uživatele, pro který se má ověřit členství ve skupině, a seznam skupin, pro které se mají kontrolovat členství. Seznam skupin musí být uveden ve formě komplexní proměnné typu Microsoft. Open. AzureAD. model. GroupIdsForMembershipCheck, proto je nejprve nutné vytvořit proměnnou s tímto typem:

```powershell
    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck
```

Dále poskytujeme hodnoty pro identifikátory groupId pro kontrolu atributu "GroupIds" této komplexní proměnné:

```powershell
    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"
```

Pokud teď chceme zkontrolovat členství uživatele ve skupině s ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea proti skupinám v $g, měli byste použít:

```powershell
    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}
```

Vrácená hodnota je seznam skupin, ze kterých je tento uživatel členem. Tuto metodu můžete použít také ke kontrole kontaktů, skupin nebo členství instančních objektů pro daný seznam skupin pomocí Select-AzureADGroupIdsContactIsMemberOf, Select-AzureADGroupIdsGroupIsMemberOf nebo Select-AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="disable-group-creation-by-your-users"></a>Zakázání vytváření skupin pomocí uživatelů

Uživatelům, kteří nejsou správci, můžete zabránit v vytváření skupin zabezpečení. Výchozím chováním v Microsoft online Directory Services (MSODS) je povolit uživatelům bez role správce vytvářet skupiny bez ohledu na to, jestli je povolená taky Samoobslužná správa skupin (SSGM). Nastavení SSGM řídí chování pouze na přístupovém panelu Moje aplikace.

Zakázání vytváření skupin pro uživatele, kteří nejsou správci:

1. Ověřte, že uživatelé, kteří nejsou správci, můžou vytvářet skupiny:
   
   ```powershell
   PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
   ```
  
2. Pokud se vrátí `UsersPermissionToCreateGroupsEnabled : True` , pak uživatelé bez role správce můžou vytvářet skupiny. Zakázání této funkce:
  
   ```powershell 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```
  
## <a name="manage-owners-of-groups"></a>Správa vlastníků skupin

Chcete-li přidat vlastníky do skupiny, použijte rutinu Add-AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

Parametr-ObjectId je ObjectID skupiny, do které chceme přidat vlastníka, a parametr-RefObjectId je ObjectID uživatele nebo instančního objektu, který chceme přidat jako vlastníka skupiny.

Pokud chcete načíst vlastníky skupiny, použijte rutinu Get-AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
```

Rutina vrátí seznam vlastníků (uživatelů a instančních objektů) pro zadanou skupinu:

```powershell
    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User
```

Pokud chcete odebrat vlastníka ze skupiny, použijte rutinu Remove-AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67
```

## <a name="reserved-aliases"></a>Rezervované aliasy

Když se vytvoří skupina, některé koncové body umožní koncovému uživateli zadat mailNickname nebo alias, který se má použít jako součást e-mailové adresy skupiny. Skupiny s následujícími vysoce privilegovanými e-mailovými aliasy můžou vytvořit jenom globální správce Azure AD. 
  
* pošt
* správce
* správce
* hostmaster
* majordomo
* postmaster
* kořen
* požadavk
* security
* SSL – správce
* příslušného

## <a name="group-writeback-to-on-premises-preview"></a>Zpětný zápis skupin do místního prostředí (Preview)

V současné době je řada skupin stále spravována v místní službě Active Directory. Pro zodpovězení žádostí o synchronizaci cloudových skupin zpátky do místního prostředí je teď k dispozici funkce zpětného zápisu skupin Microsoft 365 pro Azure AD, která je teď dostupná ve verzi Preview.

Microsoft 365 skupiny se vytvářejí a spravují v cloudu. Funkce zpětného zápisu umožňuje zapisovat zpět Microsoft 365 skupiny jako distribuční skupiny do doménové struktury služby Active Directory s nainstalovanou službou Exchange. Uživatelé s místními poštovními schránkami Exchange můžou odesílat a přijímat e-maily z těchto skupin. Funkce zpětného zápisu skupiny nepodporuje skupiny zabezpečení nebo distribuční skupiny Azure AD.

Další podrobnosti najdete v dokumentaci ke [službě Azure AD Connect Sync](../hybrid/how-to-connect-syncservice-features.md).

Zpětný zápis skupin Microsoft 365 je funkce Public Preview služby Azure Active Directory (Azure AD) a je k dispozici u placeného licenčního plánu Azure AD. Nějaké právní informace o verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

## <a name="next-steps"></a>Další kroky

Další Azure Active Directory dokumentaci PowerShellu najdete v [Azure Active Directory rutinách](/powershell/azure/active-directory/install-adv2).

* [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](../fundamentals/active-directory-manage-groups.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Integrování místních identit do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
