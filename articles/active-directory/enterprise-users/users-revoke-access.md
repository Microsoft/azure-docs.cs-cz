---
title: Odvolání přístupu uživatele v nouzi v Azure Active Directory | Microsoft Docs
description: Jak odvolat veškerý přístup pro uživatele v Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
author: curtand
ms.author: curtand
manager: daveba
ms.reviewer: krbain
ms.date: 03/29/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 578e8f5f3126542c579cd573c82b732049d407b6
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105959818"
---
# <a name="revoke-user-access-in-azure-active-directory"></a>Odvolání přístupu uživatele v Azure Active Directory

Scénáře, které by mohly vyžadovat, aby správce odvolal veškerý přístup pro uživatele, zahrnují napadené účty, ukončení zaměstnanců a další hrozby programu Insider. V závislosti na složitosti prostředí můžou správci provést několik kroků, aby byl přístup odvolaný. V některých scénářích může nastat období mezi zahájením odvolání přístupu a v případě, že je přístup účinně odvolán.

Chcete-li zmírnit rizika, je nutné pochopit, jak tokeny fungují. Existuje mnoho druhů tokenů, které spadají do jednoho ze vzorů uvedených v následujících částech.

## <a name="access-tokens-and-refresh-tokens"></a>Přístupové tokeny a aktualizace tokenů

Přístupové tokeny a aktualizační tokeny se často používají s tlustými klientskými aplikacemi a používají se také v aplikacích založených na prohlížeči, jako jsou například jednostránkové aplikace.

- Když se uživatelé ověřují ve službě Azure AD, vyhodnocují se zásady autorizace, které určují, jestli se uživateli dá udělit přístup ke konkrétnímu prostředku.  

- V případě autorizace služba Azure AD vydá přístupový token a obnovovací token pro daný prostředek.  

- Přístupové tokeny vydané službou Azure AD ve výchozím nastavení poslední po dobu 1 hodiny. Pokud protokol ověřování povoluje, může aplikace bez upozornění znovu ověřit uživatele předáním aktualizačního tokenu do služby Azure AD, když platnost přístupového tokenu vyprší.

Služba Azure AD pak znovu vyhodnotí své zásady autorizace. Pokud je uživatel pořád autorizovaný, Azure AD vydá nový přístupový token a aktualizuje token.

Přístupové tokeny můžou být bezpečnostními právy, pokud je nutné přístup odvolat v době, která je kratší než doba života tokenu, což je obvykle přibližně hodina. Z tohoto důvodu Microsoft aktivně pracuje na zajištění [průběžného vyhodnocení přístupu](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation) do aplikací Office 365, což pomáhá zajistit neplatnost přístupových tokenů téměř v reálném čase.  

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

1. Zakáže uživatele ve službě Active Directory. Podívejte se na téma [Disable-ADAccount](https://docs.microsoft.com/powershell/module/addsadministration/disable-adaccount?view=win10-ps).

    ```PowerShell
    Disable-ADAccount -Identity johndoe  
    ```

2. Resetujte heslo uživatele dvakrát ve službě Active Directory. Další informace najdete v tématu [set-ADAccountPassword](https://docs.microsoft.com/powershell/module/addsadministration/set-adaccountpassword?view=win10-ps).

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

1. Zakáže uživatele v Azure AD. Další informace najdete v tématu [set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/Set-AzureADUser?view=azureadps-2.0).

    ```PowerShell
    Set-AzureADUser -ObjectId johndoe@contoso.com -AccountEnabled $false
    ```

2. Odvolá obnovovací tokeny Azure AD uživatele. Podívejte se na téma [REVOKE-AzureADUserAllRefreshToken](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0).

    ```PowerShell
    Revoke-AzureADUserAllRefreshToken -ObjectId johndoe@contoso.com
    ```

3. Zakažte zařízení uživatele. Další informace najdete v tématu [Get-AzureADUserRegisteredDevice](https://docs.microsoft.com/powershell/module/azuread/get-azureaduserregistereddevice?view=azureadps-2.0).

    ```PowerShell
    Get-AzureADUserRegisteredDevice -ObjectId johndoe@contoso.com | Set-AzureADDevice -AccountEnabled $false
    ```
## <a name="when-access-is-revoked"></a>Při odvolání přístupu

Jakmile správci přijmou výše uvedené kroky, uživatel nemůže získat nové tokeny pro žádnou aplikaci vázanou na Azure Active Directory. Uplynulý čas mezi zrušením a uživatelem, který ztratí přístup, závisí na tom, jak aplikace uděluje přístup:

- U **aplikací využívajících přístupové tokeny** ztratí uživatel přístup po vypršení platnosti přístupového tokenu.

- U **aplikací, které používají tokeny relací**, se stávající relace ukončí ihned po vypršení platnosti tokenu. Pokud je zakázaný stav uživatele synchronizovaný s aplikací, může aplikace automaticky odvolat existující relace uživatele, pokud je to tak nakonfigurované.  Doba, kterou trvá, závisí na četnosti synchronizace mezi aplikací a službou Azure AD.

## <a name="best-practices"></a>Osvědčené postupy

- Nasaďte automatizované řešení zřizování a zrušení zřízení. Zrušení zřízení uživatelů z aplikací představuje účinný způsob odvolávání přístupu, zejména u aplikací, které používají tokeny relací. Vytvořte proces pro zrušení zřízení uživatelů pro aplikace, které nepodporují automatické zřizování a rušení zřizování. Zajistěte, aby aplikace odvolaly vlastní tokeny relace a zastavily přijímání přístupových tokenů Azure AD, i když jsou stále platné.

  - Použijte [zřizování aplikací Azure AD SaaS](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning). Zřizování aplikací Azure AD SaaS se obvykle spouští automaticky každých 20-40 minut. [Nakonfigurujte zřizování Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) pro zrušení zřízení nebo deaktivace zakázaných uživatelů v aplikacích.
  
  - Pro aplikace, které nepoužívají zřizování aplikací Azure AD SaaS, použijte [Nástroj MIM (Identity Manager)](https://docs.microsoft.com/microsoft-identity-manager/mim-how-provision-users-adds) nebo řešení třetí strany pro automatizaci zrušení zřizování uživatelů.  
  - Identifikujte a vývojujte proces pro aplikace, které vyžadují ruční zrušení zřízení. Zajistěte, aby správci mohli rychle spustit požadované ruční úlohy, aby v případě potřeby mohli uživatele z těchto aplikací zrušit.
  
- [Spravujte zařízení a aplikace pomocí Microsoft Intune](https://docs.microsoft.com/mem/intune/remote-actions/device-management). Zařízení spravovaná přes Intune je [možné obnovit do továrního nastavení](https://docs.microsoft.com/mem/intune/remote-actions/devices-wipe). Pokud je zařízení nespravované, můžete [firemní data vymazat ze spravovaných aplikací](https://docs.microsoft.com/mem/intune/apps/apps-selective-wipe). Tyto procesy jsou platné pro odebrání potenciálně citlivých dat ze zařízení koncových uživatelů. Aby se ale kterýkoli proces aktivoval, zařízení musí být připojené k Internetu. Pokud je zařízení offline, bude mít stále přístup k jakýmkoli místně uloženým datům.

> [!NOTE]
> Po vymazání nelze data v zařízení obnovit.

- Pokud je to vhodné, použijte [Microsoft Cloud App Security (MCAS) k blokování stahování dat](https://docs.microsoft.com/cloud-app-security/use-case-proxy-block-session-aad) . Pokud můžou být data dostupná jenom online, můžou organizace monitorovat relace a dosáhnout vynucování zásad v reálném čase.

- [Ve službě Azure AD povolte Průběžné vyhodnocování přístupu (CAE)](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation). CAE umožňuje správcům odvolat tokeny relace a přístupové tokeny pro aplikace, které jsou schopné CAE.  

## <a name="next-steps"></a>Další kroky

- [Postupy zabezpečeného přístupu pro správce Azure AD](https://docs.microsoft.com/azure/active-directory/roles/security-planning)
- [Přidat nebo aktualizovat informace o profilu uživatele](../fundamentals/active-directory-users-profile-azure-portal.md)
