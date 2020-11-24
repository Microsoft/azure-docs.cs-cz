---
title: Odvolání přístupu uživatele v nouzi v Azure Active Directory | Microsoft Docs
description: Jak odvolat veškerý přístup pro uživatele v Azure Active Directory
services: active-directory
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
author: curtand
ms.author: curtand
manager: daveba
ms.reviewer: krbain
ms.date: 11/15/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 491e4f996d91fe267593e36b7ff06200c91b93f8
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2020
ms.locfileid: "95490693"
---
# <a name="revoke-user-access-in-azure-active-directory"></a>Odvolání přístupu uživatele v Azure Active Directory

Mezi scénáře, které by mohly vyžadovat správce k odvolání veškerého přístupu pro uživatele, patří ohrožené účty, ukončení zaměstnanců a další hrozby programu Insider. V závislosti na složitosti prostředí můžou správci provést několik kroků, aby byl přístup odvolaný. V některých scénářích může nastat období mezi zahájením odvolání přístupu a v případě, že je přístup účinně odvolán.

Chcete-li zmírnit rizika, je nutné pochopit, jak tokeny fungují. Existuje mnoho druhů tokenů, které spadají do jednoho ze vzorů uvedených v následujících částech.

## <a name="access-tokens-and-refresh-tokens"></a>Přístupové tokeny a aktualizace tokenů

Přístupové tokeny a aktualizační tokeny se často používají s tlustými klientskými aplikacemi a používají se také v aplikacích založených na prohlížeči, jako jsou například jednostránkové aplikace.

- Když se uživatelé ověřují ve službě Azure AD, vyhodnocují se zásady autorizace, které určují, jestli se uživateli dá udělit přístup ke konkrétnímu prostředku.  

- V případě autorizace služba Azure AD vydá přístupový token a obnovovací token pro daný prostředek.  

- Přístupové tokeny vydané službou Azure AD ve výchozím nastavení poslední po dobu 1 hodiny. Pokud protokol ověřování povoluje, může aplikace bez upozornění znovu ověřit uživatele předáním aktualizačního tokenu do služby Azure AD, když platnost přístupového tokenu vyprší.

Služba Azure AD pak znovu vyhodnotí své zásady autorizace. Pokud je uživatel pořád autorizovaný, Azure AD vydá nový přístupový token a aktualizuje token.

Přístupové tokeny můžou být bezpečnostními právy, pokud je nutné přístup odvolat v době, která je kratší než doba života tokenu, což je obvykle přibližně hodina. Z tohoto důvodu Microsoft aktivně pracuje na zajištění [průběžného vyhodnocení přístupu](../conditional-access/concept-continuous-access-evaluation.md) Microsoft 365 aplikací, což pomáhá zajistit neplatnost přístupových tokenů téměř v reálném čase.  

## <a name="session-tokens-cookies"></a>Tokeny relace (soubory cookie)

Většina aplikací založených na prohlížeči používá tokeny relace místo přístupu a aktualizace tokenů.  

- Když uživatel otevře prohlížeč a ověří aplikaci přes Azure AD, získá uživatel dvě tokeny relace. Jednu z Azure AD a jinou z aplikace.  

- Jakmile aplikace vydá svůj vlastní token relace, přístup k aplikaci se řídí relací aplikace. V tuto chvíli je uživatel ovlivněn pouze zásadami autorizace, o kterých aplikace ví.

- Zásady autorizace služby Azure AD se přehodnotí jako často, protože aplikace odesílá uživatele zpátky do Azure AD. Opakované vyhodnocení obvykle probíhá tiše, i když frekvence závisí na tom, jak je aplikace nakonfigurována. Je možné, že aplikace nebude nikdy odesílat uživatele zpět do služby Azure AD, pokud je token relace platný.

- Aby byl token relace odvolaný, aplikace musí odvolat přístup na základě vlastních zásad autorizace. Azure AD nemůže přímo odvolat token relace vydaný aplikací.  

## <a name="revoke-access-for-a-user-in-the-hybrid-environment"></a>Odvolat přístup pro uživatele v hybridním prostředí

Pro hybridní prostředí s místní službou Active Directory synchronizovanou s Azure Active Directory Microsoft doporučuje správcům IT provádět následující akce.  

### <a name="on-premises-active-directory-environment"></a>Místní prostředí Active Directory

Jako správce ve službě Active Directory se připojte k místní síti, otevřete PowerShell a proveďte následující akce:

1. Zakáže uživatele ve službě Active Directory. Podívejte se na téma [Disable-ADAccount](/powershell/module/addsadministration/disable-adaccount?view=win10-ps).

    ```PowerShell
    Disable-ADAccount -Identity johndoe  
    ```

1. Resetujte heslo uživatele dvakrát ve službě Active Directory. Další informace najdete v tématu [set-ADAccountPassword](/powershell/module/addsadministration/set-adaccountpassword?view=win10-ps).

    > [!NOTE]
    > Důvod změny hesla uživatele dvakrát znamená zmírnit riziko průchodu pass-the-hash, zejména v případě, že dojde k prodlevám při místní replikaci hesel. Pokud můžete tento účet bezpečně předpokládat, můžete heslo resetovat jenom jednou.

    > [!IMPORTANT] 
    > V následujících rutinách nepoužívejte ukázková hesla. Nezapomeňte změnit hesla na náhodný řetězec.

    ```PowerShell
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd1" -Force)
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd2" -Force)
    ```

### <a name="azure-active-directory-environment"></a>Azure Active Directory prostředí

Jako správce v Azure Active Directory otevřete PowerShell, spusťte ``Connect-AzureAD`` příkaz a proveďte následující akce:

1. Zakáže uživatele v Azure AD. Další informace najdete v tématu [set-AzureADUser](/powershell/module/azuread/Set-AzureADUser?view=azureadps-2.0).

    ```PowerShell
    Set-AzureADUser -ObjectId johndoe@contoso.com -AccountEnabled $false
    ```
1. Odvolá obnovovací tokeny Azure AD uživatele. Podívejte se na téma [REVOKE-AzureADUserAllRefreshToken](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0).

    ```PowerShell
    Revoke-AzureADUserAllRefreshToken -ObjectId johndoe@contoso.com
    ```

1. Zakažte zařízení uživatele. Další informace najdete v tématu [Get-AzureADUserRegisteredDevice](/powershell/module/azuread/get-azureaduserregistereddevice?view=azureadps-2.0).

    ```PowerShell
    Get-AzureADUserRegisteredDevice -ObjectId johndoe@contoso.com | Set-AzureADDevice -AccountEnabled $false
    ```

## <a name="optional-steps"></a>Volitelné kroky

- [Vymazání podnikových dat z aplikací spravovaných pomocí Intune](/mem/intune/apps/apps-selective-wipe)

- [Vymazáním zařízení vlastněných společností se zařízení resetuje na výchozí tovární nastavení](/mem/intune/remote-actions/devices-wipe).

> [!NOTE]
> Po vymazání nelze data v zařízení obnovit.

## <a name="when-access-is-revoked"></a>Při odvolání přístupu

Jakmile správci přijmou výše uvedené kroky, uživatel nemůže získat nové tokeny pro žádnou aplikaci vázanou na Azure Active Directory. Uplynulý čas mezi zrušením a uživatelem, který ztratí přístup, závisí na tom, jak aplikace uděluje přístup:

- U **aplikací využívajících přístupové tokeny** ztratí uživatel přístup po vypršení platnosti přístupového tokenu.

- U **aplikací, které používají tokeny relací**, se stávající relace ukončí ihned po vypršení platnosti tokenu. Pokud je zakázaný stav uživatele synchronizovaný s aplikací, může aplikace automaticky odvolat existující relace uživatele, pokud je to tak nakonfigurované.  Doba, kterou trvá, závisí na četnosti synchronizace mezi aplikací a službou Azure AD.

## <a name="next-steps"></a>Další kroky

- [Postupy zabezpečeného přístupu pro správce Azure AD](../roles/security-planning.md)
- [Přidat nebo aktualizovat informace o profilu uživatele](../fundamentals/active-directory-users-profile-azure-portal.md)