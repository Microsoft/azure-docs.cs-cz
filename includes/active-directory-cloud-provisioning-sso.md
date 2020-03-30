---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: 3aa1571b46938b03f556fa124d3f0a2a70f2c5c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "79504382"
---
## <a name="steps-to-enable-single-sign-on"></a>Postup povolení jednotného přihlašování
Zřizování cloudu funguje s jedním přihlášením.  V současné době neexistuje možnost povolit při správě při instalaci agenta, ale můžete použít níže uvedené kroky k povolení přisuzu a použít jej. 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>Krok 1: Stažení a extrahování souborů Azure AD Connect
1.  Nejprve si stáhněte nejnovější verzi [Služby Azure AD Connect.](https://www.microsoft.com/download/details.aspx?id=47594)
2.  Otevřete příkazový řádek s oprávněními správce a přejděte na právě staženou položku msi.
3.  Spusťte následující:`msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`
4. Změňte cestu souboru a složku extrahujte tak, aby odpovídaly cestě k souboru a názvu extrakční složky.  Obsah by nyní měl být ve složce extrakce.

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>Krok 2: Import bezešvého modulu PowerShell u jednotného přihlašování

1. Stáhněte a nainstalujte [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Přejděte `%programfiles%\Microsoft Azure Active Directory Connect` do složky.
3. Importujte bezešvý modul Prostředí SSO PowerShell pomocí tohoto příkazu: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Krok 3: Získání seznamu doménových struktur služby Active Directory, ve kterých bylo povoleno bezproblémové jednotné přihlašování

1. Spusťte PowerShell jako správce. V Prostředí PowerShell volejte `New-AzureADSSOAuthenticationContext`. Po zobrazení výzvy zadejte pověření globálního správce klienta.
2. Zavolejte `Get-AzureADSSOStatus`. Tento příkaz poskytuje seznam doménových struktur služby Active Directory (podívejte se na seznam Domén), ve kterém byla tato funkce povolena.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Krok 4: Povolení bezproblémového jednotného přihlašování pro každou doménovou strukturu služby Active Directory

1. Zavolejte `Enable-AzureADSSOForest`. Po zobrazení výzvy zadejte pověření správce domény pro zamýšlenou doménovou strukturu služby Active Directory.

   > [!NOTE]
   >Uživatelské jméno správce domény musí být zadáno ve formátu názvu účtu SAM (contoso\johndoe nebo contoso.com\johndoe). Část uživatelského jména domény používáme k vyhledání řadiče domény správce domény pomocí služby DNS.

   >[!NOTE]
   >Použitý účet správce domény nesmí být členem skupiny Chránění uživatelé. Pokud ano, operace se nezdaří.

2. Opakujte předchozí krok pro každou doménovou strukturu služby Active Directory, ve které chcete funkci nastavit.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Krok 5. Povolení funkce v tenantovi

Chcete-li zapnout funkci v `Enable-AzureADSSO -Enable $true`tenantovi, zavolejte .
