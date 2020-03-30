---
title: Příklady PowerShellu V2 pro správu skupin – Azure AD | Dokumenty společnosti Microsoft
description: Tato stránka obsahuje příklady PowerShellu, které vám pomohou spravovat vaše skupiny ve službě Azure Active Directory.
keywords: Azure AD, Azure Active Directory, PowerShell, Skupiny, Správa skupin
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a218e956c72f8005e533db7b8800e98ee72ce223
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74233120"
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Rutiny služby Azure Active Directory verze 2 pro správu skupin

> [!div class="op_single_selector"]
> - [Portál Azure](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
> - [PowerShell](groups-settings-v2-cmdlets.md)
>
>

Tento článek obsahuje příklady použití PowerShellu ke správě skupin ve službě Azure Active Directory (Azure AD).  Taky vám řekne, jak nastavit pomocí modulu Azure AD PowerShell. Nejprve je nutné [stáhnout modul Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>Instalace modulu Azure AD PowerShell

Chcete-li nainstalovat modul Azure AD PowerShell, použijte následující příkazy:

```powershell
    PS C:\Windows\system32> install-module azuread
    PS C:\Windows\system32> import-module azuread
```

Chcete-li ověřit, zda je modul připraven k použití, použijte následující příkaz:

```powershell
    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

Nyní můžete začít používat rutiny v modulu. Úplný popis rutin v modulu Azure AD najdete v online referenční dokumentaci pro [prostředí Azure Active Directory PowerShell verze 2](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="connect-to-the-directory"></a>Připojení k adresáři

Než začnete spravovat skupiny pomocí rutin prostředí Azure AD PowerShell, musíte připojit relaci PowerShellu k adresáři, který chcete spravovat. Použijte následující příkaz:

```powershell
    PS C:\Windows\system32> Connect-AzureAD
```

Rutina zobrazí výzvu k zadání pověření, která chcete použít pro přístup k adresáři. V tomto příkladu karen@drumkit.onmicrosoft.com používáme přístup k demonstračníadresář. Rutina vrátí potvrzení, že relace byla úspěšně připojena k vašemu adresáři:

```powershell
    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…
```

Teď můžete začít používat rutiny AzureAD ke správě skupin ve vašem adresáři.

## <a name="retrieve-groups"></a>Načíst skupiny

Chcete-li načíst existující skupiny z adresáře, použijte rutinu Get-AzureADGroups. 

Chcete-li načíst všechny skupiny v adresáři, použijte rutinu bez parametrů:

```powershell
    PS C:\Windows\system32> get-azureadgroup
```

Rutina vrátí všechny skupiny v připojeném adresáři.

Parametr -objectID můžete použít k načtení určité skupiny, pro kterou zadáte id objektu skupiny:

```powershell
    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b
```

Rutina nyní vrátí skupinu, jejíž id objektu odpovídá hodnotě zadaného parametru:

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

Určitou skupinu můžete vyhledat pomocí parametru -filter. Tento parametr přebírá klauzuli filtru ODATA a vrátí všechny skupiny, které odpovídají filtru, jako v následujícím příkladu:

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
> Rutiny prostředí Azure AD PowerShell implementují standard dotazu OData. Další informace naleznete **v tématu $filter** v [možnostech systémových dotazů OData pomocí koncového bodu OData](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter).

## <a name="create-groups"></a>Vytvoření skupin

Chcete-li vytvořit novou skupinu ve vašem adresáři, použijte rutinu New-AzureADGroup. Tato rutina vytvoří novou skupinu zabezpečení nazvanou "Marketing":

```powershell
    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"
```

## <a name="update-groups"></a>Aktualizovat skupiny

Chcete-li aktualizovat existující skupinu, použijte rutinu Set-AzureADGroup. V tomto příkladu měníme vlastnost DisplayName skupiny Intune Administrators. Nejprve jsme najít skupinu pomocí rutiny Get-AzureADGroup a filtr pomocí DisplayName atribut:

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

Dále měníme vlastnost Description na novou hodnotu "Správci zařízení Intune":

```powershell
    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"
```

Nyní, pokud najdeme skupinu znovu, vidíme Description vlastnost je aktualizován tak, aby odrážely novou hodnotu:

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

Chcete-li odstranit skupiny z adresáře, použijte rutinu Odebrat AzureADGroup následujícím způsobem:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b
```

## <a name="manage-group-membership"></a>Správa členství ve skupinách

### <a name="add-members"></a>Přidání členů

Chcete-li přidat nové členy do skupiny, použijte rutinu Add-AzureADGroupMember. Tento příkaz přidá člena do skupiny Administrators Intune, kterou jsme použili v předchozím příkladu:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

Parametr -ObjectId je ObjectID skupiny, do které chceme přidat člena, a -RefObjectId je ObjectID uživatele, kterého chceme přidat jako člena do skupiny.

### <a name="get-members"></a>Získat členy

Chcete-li získat stávající členy skupiny, použijte rutinu Get-AzureADGroupMember, jako v tomto příkladu:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User
```

### <a name="remove-members"></a>Odebrání členů

Chcete-li odebrat člena, který jsme dříve přidali do skupiny, použijte rutinu Remove-AzureADGroupMember, jak je znázorněno zde:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

### <a name="verify-members"></a>Ověřit členy

Chcete-li ověřit členství ve skupinách uživatele, použijte rutinu Select-AzureADGroupIdsUserIsMemberOf. Tato rutina bere jako jeho parametry ObjectId uživatele, pro které chcete zkontrolovat členství ve skupinách a seznam skupin, pro které chcete zkontrolovat členství. Seznam skupin musí být poskytnuty ve formě komplexní proměnné typu "Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck", takže nejprve musíme vytvořit proměnnou s tímto typem:

```powershell
    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck
```

Dále poskytujeme hodnoty pro groupIds se změnami v atributu "GroupIds" této komplexní proměnné:

```powershell
    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"
```

Nyní, pokud chceme zkontrolovat členství ve skupinách uživatele s ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea proti skupinám v $g, měli bychom použít:

```powershell
    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}
```

Vrácená hodnota je seznam skupin, jejichž členem je tento uživatel. Tuto metodu můžete také použít ke kontrole členství kontaktů, skupin nebo instančních objektů pro daný seznam skupin pomocí select-AzureADGroupIdsContactIsMemberOf, Select-AzureADGroupIdsGroupIsMemberOf nebo Select-AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="disable-group-creation-by-your-users"></a>Zakázání vytváření skupin uživateli

Uživatelům, kteří nejsou správci, můžete zabránit ve vytváření skupin zabezpečení. Výchozím chováním ve službě Microsoft Online Directory Services (MSODS) je umožnit uživatelům, kteří nejsou správci, vytvářet skupiny bez ohledu na to, zda je povolena také samoobslužná správa skupin (SSGM). Nastavení SSGM řídí chování pouze na přístupovém panelu Moje aplikace.

Zakázání vytváření skupin pro uživatele, kteří nejsou správci:

1. Ověřte, zda mohou uživatelé, kteří nejsou správci, vytvářet skupiny:
   
   ```powershell
   PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
   ```
  
2. Pokud se `UsersPermissionToCreateGroupsEnabled : True`vrátí , mohou skupiny vytvářet uživatelé, kteří nejsou správci. Zakázání této funkce:
  
   ```powershell 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```
  
## <a name="manage-owners-of-groups"></a>Správa vlastníků skupin

Chcete-li přidat vlastníky do skupiny, použijte rutinu Add-AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

Parametr -ObjectId je ObjectID skupiny, do které chceme přidat vlastníka, a -RefObjectId je ObjectID uživatele nebo instančního objektu, který chceme přidat jako vlastník skupiny.

Chcete-li načíst vlastníky skupiny, použijte rutinu Get-AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
```

Rutina vrátí seznam vlastníků (uživatelů a instančních objektů) pro zadanou skupinu:

```powershell
    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User
```

Pokud chcete odebrat vlastníka ze skupiny, použijte rutinu Odebrat AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67
```

## <a name="reserved-aliases"></a>Rezervované aliasy

Při vytvoření skupiny umožňují určité koncové body koncovému uživateli zadat mailNickname nebo alias, který má být použit jako součást e-mailové adresy skupiny.Skupiny s následujícími vysoce privilegovanými e-mailovými aliasy může vytvořit jenom globální správce Služby Azure AD. 
  
* Zneužívání
* admin
* správce
* hostmaster
* majordomo
* Postmaster
* kořen
* Zabezpečené
* security
* ssl-admin
* Webmaster

## <a name="group-writeback-to-on-premises-preview"></a>Skupinový zpětný zápis do místního prostředí (náhled)

V současné době je mnoho skupin stále spravováno v místní službě Active Directory. Chcete-li odpovědět na požadavky na synchronizaci cloudových skupin zpět do místního prostředí, funkce zpětného zápisu skupin Office 365 pro Azure AD je teď k dispozici pro náhled.

Skupiny Office 365 se vytvářejí a spravují v cloudu. Funkce zpětného zápisu umožňuje odepsat skupiny Office 365 jako distribuční skupiny do doménové struktury služby Active Directory s nainstalovanou službou Exchange. Uživatelé s místními poštovními schránkami Exchange pak můžou odesílat a přijímat e-maily z těchto skupin. Funkce zpětného zápisu skupiny nepodporuje skupiny zabezpečení Azure AD ani distribuční skupiny.

Další podrobnosti naleznete v dokumentaci ke [službě synchronizace Azure AD Connect](../hybrid/how-to-connect-syncservice-features.md).

Zpětný zápis skupiny Office 365 je veřejná funkce preview služby Azure Active Directory (Azure AD) a je dostupná s jakýmkoli placeným licenčním tarifem Azure AD. Některé právní informace o náhledech najdete [v tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="next-steps"></a>Další kroky

Další dokumentaci k prostředí Azure Active Directory PowerShell najdete na [rutinách služby Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

* [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](../fundamentals/active-directory-manage-groups.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Integrování místních identit do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
