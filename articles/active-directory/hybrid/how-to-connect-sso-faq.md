---
title: 'Azure AD Connect: Bezproblémové jednotné přihlašování – Nejčastější dotazy | Microsoft Docs'
description: Odpovědi na nejčastější dotazy týkající se Azure Active Directory bezproblémové jednotné přihlašování.
services: active-directory
keywords: Co je Azure AD Connect, instalace služby Active Directory, požadované součásti pro Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96d0253e1e656f4bdb5180af0d57824aceb4f0dd
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2019
ms.locfileid: "71176659"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory bezproblémové jednotné přihlašování: Nejčastější dotazy

V tomto článku se řeší často kladené otázky týkající se Azure Active Directory bezproblémového jednotného přihlašování (bezproblémové jednotné přihlašování). Pokračujte v kontrole nového obsahu.

**Otázka: Jaké metody přihlašování fungují bez problémů jednotného přihlašování**

Bezproblémové jednotné přihlašování se dá kombinovat buď se [synchronizací hodnot hash hesel](how-to-connect-password-hash-synchronization.md) , nebo [pomocí předávacích ověřovacích metod ověřování](how-to-connect-pta.md) . Tuto funkci však nelze použít s Active Directory Federation Services (AD FS) (ADFS).

**Otázka: Je bezproblémová funkce jednotného přihlašování bezplatná?**

Bezproblémové jednotné přihlašování je bezplatná funkce, takže nepotřebujete žádné placené edice Azure AD, abyste ji mohli používat.

**Otázka: Je k dispozici bezproblémové přihlašování v [cloudu Microsoft Azure (Německo)](https://www.microsoft.de/cloud-deutschland) a [Microsoft Azure Government cloudu](https://azure.microsoft.com/features/gov/)?**

Ne. Bezproblémové jednotné přihlašování je dostupné jenom v celosvětové instanci Azure AD.

**Otázka: Které aplikace `domain_hint` využívají funkci nebo `login_hint` možnost parametrů bezproblémového jednotného přihlašování?**

Níže je uvedený nevyčerpávající seznam aplikací, které mohou odesílat tyto parametry do služby Azure AD, a proto poskytují uživatelům tiché přihlašování pomocí bezproblémového jednotného přihlašování (tj. není nutné, aby vaši uživatelé zapracovali své uživatelské jméno nebo hesla):

| Název aplikace | Adresa URL aplikace, která se má použít |
| -- | -- |
| Přístupový panel | https:\//myapps.Microsoft.com/contoso.com |
| Outlook na webu | https:\//outlook.office365.com/contoso.com |
| Portály Office 365 | https:\//Portal.Office.com? domain_hint = contoso. com, https:\//www.Office.com? domain_hint = contoso. com |

Kromě toho uživatelé získají tiché přihlašování, pokud aplikace posílá žádosti o přihlášení do koncových bodů služby Azure AD nastavené jako klienti – to znamená https:\//Login.microsoftonline.com/contoso.com/<. > nebo https:\//Login.microsoftonline.com/<tenant_ID>/<.. > – místo společného koncového bodu služby Azure AD – to znamená https:\//Login.microsoftonline.com/Common/<... >. Níže je uvedený nevyčerpávající seznam aplikací, které vytvářejí tyto typy žádostí o přihlášení.

| Název aplikace | Adresa URL aplikace, která se má použít |
| -- | -- |
| SharePoint Online | https:\//contoso.SharePoint.com |
| portál Azure | https:\//portal.azure.com/contoso.com |

Ve výše uvedených tabulkách nahraďte "contoso.com" názvem domény, aby se získaly správné adresy URL aplikací pro vašeho tenanta.

Pokud chcete, aby se v rámci tichého přihlašování používaly jiné aplikace, dejte nám vědět v části o zpětné vazbě.

**Otázka: Bezproblémová podpora `Alternate ID` jednotného přihlašování jako uživatelské jméno `userPrincipalName`místo?**

Ano. Bezproblémové jednotné přihlašování podporuje `Alternate ID` jako uživatelské jméno, pokud je nakonfigurované v Azure AD Connect, jak je znázorněno [zde](how-to-connect-install-custom.md). Ne všechny aplikace Office 365 podporují `Alternate ID`. Informace o příkazu support najdete v dokumentaci konkrétní aplikace.

**Otázka: Jaký je rozdíl mezi prostředím jednotného přihlašování, které poskytuje [Azure AD JOIN](../active-directory-azureadjoin-overview.md) a bezproblémové jednotné přihlašování?**

[Připojení k Azure AD](../active-directory-azureadjoin-overview.md) poskytuje uživatelům jednotné přihlašování, pokud jsou jejich zařízení zaregistrovaná ve službě Azure AD. Tato zařízení nemusí být nutně připojená k doméně. Jednotné přihlašování se poskytuje s využitím *primárních tokenů aktualizace* nebo *PRTs*, nikoli pomocí protokolu Kerberos. Činnost koncového uživatele je optimální na zařízeních s Windows 10. Jednotné přihlašování proběhne automaticky v prohlížeči Microsoft Edge. Funguje taky na Chrome s použitím rozšíření prohlížeče.

V tenantovi můžete použít jak připojení k Azure AD, tak bezproblémové jednotné přihlašování. Tyto dvě funkce jsou doplňující. Pokud jsou obě funkce zapnuté, má připojení SSO od služby Azure AD přednost před bezproblémové jednotné přihlašování.

**Otázka: Chci registrovat zařízení s jiným systémem než Windows 10 pomocí Azure AD bez použití AD FS. Můžu místo toho použít bezproblémové přihlašování?**

Ano, tento scénář vyžaduje verzi 2,1 nebo novější z [klienta připojení k síti na pracovišti](https://www.microsoft.com/download/details.aspx?id=53554).

**Otázka: Jak se dá přenášet dešifrovací klíč `AZUREADSSOACC` protokolu Kerberos účtu počítače?**

Je důležité často přenášet dešifrovací klíč `AZUREADSSOACC` protokolu Kerberos účtu počítače (který představuje Azure AD) vytvořený ve vaší místní doménové struktuře AD.

>[!IMPORTANT]
>Důrazně doporučujeme, abyste převzali šifrovací klíč protokolu Kerberos aspoň každých 30 dnů.

Na místním serveru, na kterém běží Azure AD Connect, postupujte podle těchto kroků:

    **Step 1. Get list of AD forests where Seamless SSO has been enabled**

    1. Nejdřív si stáhněte a nainstalujte [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
    2. Přejděte do složky `%programfiles%\Microsoft Azure Active Directory Connect`.
    3. Pomocí tohoto příkazu Importujte modul prostředí PowerShell pro bezproblémové přihlašování: `Import-Module .\AzureADSSO.psd1`.
    4. Spusťte PowerShell jako správce. V prostředí PowerShell volejte `New-AzureADSSOAuthenticationContext`. Tento příkaz by vám měl poskytnout místní nabídku pro zadání přihlašovacích údajů globálního správce vašeho tenanta.
    5. Volání `Get-AzureADSSOStatus | ConvertFrom-Json`. Tento příkaz vám poskytne seznam doménových struktur AD (podívejte se na seznam domény), na kterém je tato funkce povolená.

    **Krok 2. Aktualizujte Dešifrovací klíč protokolu Kerberos v každé doménové struktuře služby AD, na které byl nastavený.**

    1. Volání `$creds = Get-Credential`. Po zobrazení výzvy zadejte přihlašovací údaje správce domény pro zamýšlenou doménovou strukturu služby AD.

    > [!NOTE]
    > K vyhledání zamýšlené doménové struktury služby AD používáme uživatelské jméno správce domény poskytované ve formátu hlavní názvyjohndoe@contoso.comuživatelů (UPN) () nebo kvalifikovaný název domény Sam-účet (contoso\johndoe nebo contoso. com\johndoe). Použijete-li kvalifikovaný název domény pro doménu Sam-účet, použijeme část Doména uživatelského jména a [vyhledáte řadič domény správce domény pomocí DNS](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Pokud místo toho použijete hlavní název uživatele (UPN), před vyhledáním příslušného řadiče domény [ho převedeme na název domény s kvalifikovaným účtem Sam](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) .

    2. Volání `Update-AzureADSSOForest -OnPremCredentials $creds`. Tento příkaz aktualizuje dešifrovací klíč protokolu Kerberos pro `AZUREADSSOACC` účet počítače v této konkrétní doménové struktuře služby AD a aktualizuje ho v Azure AD.
    3. Předchozí kroky opakujte pro každou doménovou strukturu služby AD, na které jste tuto funkci nastavili.

    >[!IMPORTANT]
    >Ujistěte se, že jste `Update-AzureADSSOForest` příkaz nespouštěli více než jednou. V opačném případě funkce přestane fungovat, dokud nevyprší doba vypršení platnosti vašich uživatelských lístků Kerberos a znovu ji nevyřeší vaše místní služba Active Directory.

**Otázka: Jak můžu zakázat bezproblémové přihlašování?**

    **Step 1. Disable the feature on your tenant**

        **Option A: Disable using Azure AD Connect**

        1. Run Azure AD Connect, choose **Change user sign-in page** and click **Next**.
        2. Uncheck the **Enable single sign on** option. Continue through the wizard.

        After completing the wizard, Seamless SSO will be disabled on your tenant. However, you will see a message on screen that reads as follows:

        "Single sign-on is now disabled, but there are additional manual steps to perform in order to complete clean-up. Learn more"

        To complete the clean-up process, follow steps 2 and 3 on the on-premises server where you are running Azure AD Connect.

        **Option B: Disable using PowerShell**

        Run the following steps on the on-premises server where you are running Azure AD Connect:

        1. First, download, and install [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
        2. Navigate to the `%programfiles%\Microsoft Azure Active Directory Connect` folder.
        3. Import the Seamless SSO PowerShell module using this command: `Import-Module .\AzureADSSO.psd1`.
        4. Run PowerShell as an Administrator. In PowerShell, call `New-AzureADSSOAuthenticationContext`. This command should give you a popup to enter your tenant's Global Administrator credentials.
        5. Call `Enable-AzureADSSO -Enable $false`.

        >[!IMPORTANT]
        >Disabling Seamless SSO using PowerShell will not change the state in Azure AD Connect. Seamless SSO will show as enabled in the **Change user sign-in** page.

    **Step 2. Get list of AD forests where Seamless SSO has been enabled**

    Follow tasks 1 through 4 below if you have disabled Seamless SSO using Azure AD Connect. If you have disabled Seamless SSO using PowerShell instead, jump ahead to task 5 below.

    1. Nejdřív si stáhněte a nainstalujte [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
    2. Přejděte do složky `%programfiles%\Microsoft Azure Active Directory Connect`.
    3. Pomocí tohoto příkazu Importujte modul prostředí PowerShell pro bezproblémové přihlašování: `Import-Module .\AzureADSSO.psd1`.
    4. Spusťte PowerShell jako správce. V prostředí PowerShell volejte `New-AzureADSSOAuthenticationContext`. Tento příkaz by vám měl poskytnout místní nabídku pro zadání přihlašovacích údajů globálního správce vašeho tenanta.
    5. Volání `Get-AzureADSSOStatus | ConvertFrom-Json`. Tento příkaz vám poskytne seznam doménových struktur AD (podívejte se na seznam domény), na kterém je tato funkce povolená.

    **Krok 3. Ručně odstraňte `AZUREADSSOACCT` účet počítače ze všech doménových struktur služby Active Directory, které vidíte v seznamu.**

## <a name="next-steps"></a>Další kroky

- [**Rychlé zprovoznění**](how-to-connect-sso-quick-start.md) – zprovoznění služby Azure AD bez problémů s jednotným přihlašováním
- [**Technický obsáhlý podrobně**](how-to-connect-sso-how-it-works.md) – pochopení, jak tato funkce funguje.
- [**Řešení potíží**](tshoot-connect-sso.md) – Naučte se řešit běžné problémy s touto funkcí.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – pro nové žádosti o funkce
