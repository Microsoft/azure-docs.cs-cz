---
title: 'Azure AD Connect: Bezproblémové jednotné přihlašování – nejčastější dotazy | Dokumenty společnosti Microsoft'
description: Odpovědi na nejčastější dotazy týkající se bezproblémového jednotného přihlašování služby Azure Active Directory.
services: active-directory
keywords: co je Azure AD Connect, nainstalujte službu Active Directory, požadované součásti pro Azure AD, Jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7241c8dfbedb24f95c29ea9e1c3f763218a5668d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025671"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Bezproblémové jednotné přihlašování služby Azure Active Directory: Nejčastější dotazy

V tomto článku se budeme zabývat často kladené otázky týkající se Azure Active Directory bezproblémové jednotné přihlašování (bezproblémové jednotné přihlašování). Pokračujte v kontrole nového obsahu.

**Otázka: S jakými metodami přihlášení bezproblémové jednotné přihlašování pracovat s**

Bezproblémové jednotné přihlašování lze kombinovat s [synchronizací hash hesla](how-to-connect-password-hash-synchronization.md) nebo [předávací ověřování](how-to-connect-pta.md) přihlašovací metody. Tuto funkci však nelze použít se službou ADFS (ADFS).

**Otázka: Je bezproblémové jednotné přihlašo bezplatná funkce?**

Bezproblémové jednotné přihlašování je bezplatná funkce a nepotřebujete žádné placené edice Azure AD k jeho použití.

**Otázka: Je bezproblémové jednotné přihlašojiné připojení dostupné v [cloudu Microsoft Azure Germany](https://www.microsoft.de/cloud-deutschland) a [v cloudu Microsoft Azure Government?](https://azure.microsoft.com/features/gov/)**

Ne. Bezproblémové jednotné přihlašované jenom v celosvětové instanci Azure AD.

**Otázka: Jaké aplikace `domain_hint` využívají `login_hint` nebo parametrfunkce bezproblémové jednotné přihlašování?**

Níže je uveden nevyčerpávající seznam aplikací, které můžete odeslat tyto parametry do Služby Azure AD, a proto poskytuje uživatelům tiché přihlašovací prostředí pomocí bezproblémové jednotné přihlašování (tj. není nutné, aby uživatelé zadávali svá uživatelská jména nebo hesla):

| Název aplikace | Adresa URL aplikace, která má být použita |
| -- | -- |
| Přístupový panel | https:\//myapps.microsoft.com/contoso.com |
| Outlook na webu | https:\//outlook.office365.com/contoso.com |
| Portály Office 365 | https:\//portal.office.com?domain_hint=contoso.com,\/https: /www.office.com?domain_hint=contoso.com |

Kromě toho uživatelé získat tiché přihlašovací prostředí, pokud aplikace odesílá požadavky na přihlášení do koncových bodů Azure AD nastavených jako klienti – to znamená https:\//login.microsoftonline.com/contoso.com/<..> nebo https:\//login.microsoftonline.com/<tenant_ID>/<.. > – namísto společného koncového bodu Azure AD – to znamená https:\//login.microsoftonline.com/common/<...>. Níže je uveden neúplný seznam aplikací, které tyto typy žádostí o přihlášení.

| Název aplikace | Adresa URL aplikace, která má být použita |
| -- | -- |
| SharePoint Online | https:\//contoso.sharepoint.com |
| portál Azure | https:\//portal.azure.com/contoso.com |

Ve výše uvedených tabulkách nahraďte "contoso.com" názvem domény a přenesete se ke správným adresám URL aplikace pro vašeho tenanta.

Pokud chcete, aby naše tiché přihlašovací prostředí používaly jiné aplikace, dejte nám vědět v sekci zpětná vazba.

**Otázka: Má bezproblémové `Alternate ID` jednotné přihlašuje podporu jako uživatelské jméno, namísto `userPrincipalName`?**

Ano. Bezproblémové jednotné `Alternate ID` přihlašovací služby podporuje jako uživatelské jméno při konfiguraci v Azure AD Connect, jak je znázorněno [zde](how-to-connect-install-custom.md). Ne všechny aplikace Office `Alternate ID`365 podporují . Prohlášení o podpoře naleznete v dokumentaci ke konkrétní aplikaci.

**Otázka: Jaký je rozdíl mezi jednotné přihlašování prostředí poskytované [připojení Azure AD](../active-directory-azureadjoin-overview.md) a bezproblémové jednotné přihlašování?**

[Azure AD Join](../active-directory-azureadjoin-overview.md) poskytuje přihlašovací služby uživatelům, pokud jejich zařízení jsou registrované ve službě Azure AD. Tato zařízení nemusí být nutně připojena k doméně. SSO je k dispozici pomocí *tokeny primární aktualizace* nebo *PRT ,* a nikoli Kerberos. Uživatelské prostředí je nejoptimálnější na zařízeních s Windows 10. SSO se stane automaticky v prohlížeči Microsoft Edge. Funguje také v chromu s použitím rozšíření prohlížeče.

Můžete použít připojení Azure AD a bezproblémové jednotné přihlašování na vašem tenantovi. Tyto dvě funkce se vzájemně doplňují. Pokud jsou obě funkce zapnuté, pak jednotné přihlašované z připojení Azure AD má přednost před bezproblémové jednotné přihlašované.

**Otázka: Chci zaregistrovat zařízení, která nejsou windows 10, pomocí Azure AD, bez použití služby AD FS. Můžete místo toho použít bezproblémové jednotné přihlašovat?**

Ano, tento scénář potřebuje verzi 2.1 nebo novější [klienta připojení k pracovišti](https://www.microsoft.com/download/details.aspx?id=53554).

**Otázka: Jak lze převrátit dešifrovací klíč `AZUREADSSOACC` protokolu Kerberos účtu počítače?**

Je důležité často převrátit dešifrovací klíč kerberos účtu `AZUREADSSOACC` počítače (který představuje Azure AD) vytvořené v místní doménové struktuře služby AD.

>[!IMPORTANT]
>Důrazně doporučujeme převrátit dešifrovací klíč Kerberos alespoň každých 30 dní.

Postupujte podle následujících kroků na místním serveru, kde používáte Azure AD Connect:

   **Krok 1. Získat seznam doménových struktur služby AD, kde bylo povoleno bezproblémové jednotné přihlašování**

   1. Nejprve stáhněte a nainstalujte [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
   2. Přejděte do složky `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importujte bezešvý modul Prostředí `Import-Module .\AzureADSSO.psd1`SSO PowerShell pomocí tohoto příkazu: .
   4. Spusťte prostředí PowerShell jako správce. V Prostředí PowerShell volejte `New-AzureADSSOAuthenticationContext`. Tento příkaz by vám měl poskytnout vyskakovací okno pro zadání přihlašovacích údajů globálního správce klienta.
   5. Zavolejte `Get-AzureADSSOStatus | ConvertFrom-Json`. Tento příkaz poskytuje seznam doménových struktur služby AD (podívejte se na seznam Domén), ve kterém byla tato funkce povolena.

   **Krok 2. Aktualizace dešifrovacího klíče protokolu Kerberos v každé doménové struktuře služby AD, ve které byl nastaven**

   1. Zavolejte `$creds = Get-Credential`. Po zobrazení výzvy zadejte pověření správce domény pro zamýšlenou doménovou strukturu služby AD.

   > [!NOTE]
   >Uživatelské jméno správce domény musí být zadáno ve formátu názvu účtu SAM (contoso\johndoe nebo contoso.com\johndoe). Část uživatelského jména domény používáme k vyhledání řadiče domény správce domény pomocí služby DNS.

   >[!NOTE]
   >Použitý účet správce domény nesmí být členem skupiny Chránění uživatelé. Pokud ano, operace se nezdaří.

   2. Zavolejte `Update-AzureADSSOForest -OnPremCredentials $creds`. Tento příkaz aktualizuje dešifrovací klíč `AZUREADSSOACC` kerberos pro účet počítače v této konkrétní doménové struktuře služby AD a aktualizuje jej ve službě Azure AD.
   3. Opakujte předchozí kroky pro každou doménovou strukturu služby AD, ve které jste tuto funkci nastavili.

   >[!IMPORTANT]
   >Ujistěte se, že `Update-AzureADSSOForest` _nespustíte_ příkaz více než jednou. V opačném případě přestane funkce fungovat až do doby, než vyprší platnost lístků protokolu Kerberos uživatelů a nebude znovu vydána místní službou Active Directory.

**Otázka: Jak lze zakázat bezproblémové jednotné přihlašovat?**

   **Krok 1. Zakázání funkce v tenantovi**

   **Možnost A: Zakázání používání služby Azure AD Connect**
    
   1. Spusťte Azure AD Connect, zvolte **Změnit přihlašovací stránku uživatele** a klikněte na **Další**.
   2. Zrušením zaškrtnutí políčka **Povolit jednotné přihlašování.** Pokračujte průvodcem.

   Po dokončení průvodce bezproblémové jednotné přihlašování bude zakázáno na vašem tenantovi. Na obrazovce se však zobrazí zpráva, která zní takto:

   "Jednotné přihlašování je nyní zakázáno, ale existují další ruční kroky, které je třeba provést za účelem dokončení čištění. Další informace"

   Proces čištění dokončete podle kroků 2 a 3 na místním serveru, na kterém používáte Azure AD Connect.

   **Možnost B: Zakázání používání prostředí PowerShell**

   Spusťte následující kroky na místním serveru, kde používáte Azure AD Connect:

   1. Nejprve stáhněte a nainstalujte [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
   2. Přejděte do složky `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importujte bezešvý modul Prostředí `Import-Module .\AzureADSSO.psd1`SSO PowerShell pomocí tohoto příkazu: .
   4. Spusťte prostředí PowerShell jako správce. V Prostředí PowerShell volejte `New-AzureADSSOAuthenticationContext`. Tento příkaz by vám měl poskytnout vyskakovací okno pro zadání přihlašovacích údajů globálního správce klienta.
   5. Zavolejte `Enable-AzureADSSO -Enable $false`.

   >[!IMPORTANT]
   >Zakázání bezproblémového jednotného přihlašování pomocí prostředí PowerShell nezmění stav ve službě Azure AD Connect. Bezproblémové jednotné přihlašování se zobrazí jako povolené na stránce **změnit přihlášení uživatele.**

   **Krok 2. Získat seznam doménových struktur služby AD, kde bylo povoleno bezproblémové jednotné přihlašování**

   Postupujte podle úkolů 1 až 4 níže, pokud jste zakázali bezproblémové jednotné přihlašování pomocí Azure AD Connect. Pokud jste místo toho zakázali bezproblémové jednotné přihlašování pomocí prostředí PowerShell, přejděte k úkolu 5 níže.

   1. Nejprve stáhněte a nainstalujte [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
   2. Přejděte do složky `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importujte bezešvý modul Prostředí `Import-Module .\AzureADSSO.psd1`SSO PowerShell pomocí tohoto příkazu: .
   4. Spusťte prostředí PowerShell jako správce. V Prostředí PowerShell volejte `New-AzureADSSOAuthenticationContext`. Tento příkaz by vám měl poskytnout vyskakovací okno pro zadání přihlašovacích údajů globálního správce klienta.
   5. Zavolejte `Get-AzureADSSOStatus | ConvertFrom-Json`. Tento příkaz poskytuje seznam doménových struktur služby AD (podívejte se na seznam Domén), ve kterém byla tato funkce povolena.

   **Krok 3. Ručně odstraňte `AZUREADSSOACCT` účet počítače z každé doménové struktury služby AD, která je uvedena v seznamu.**

## <a name="next-steps"></a>Další kroky

- [**Úvodní příručka**](how-to-connect-sso-quick-start.md) – zprovoznění bezproblémového jednotného přihlašování Azure AD.
- [**Technické Deep Dive**](how-to-connect-sso-how-it-works.md) - Pochopit, jak tato funkce funguje.
- [**Poradce při potížích**](tshoot-connect-sso.md) – Přečtěte si, jak vyřešit běžné problémy s funkcí.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - Pro podání nových žádostí o funkce.
