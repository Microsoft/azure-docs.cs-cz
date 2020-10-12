---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: 3aa1571b46938b03f556fa124d3f0a2a70f2c5c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "79504382"
---
## <a name="steps-to-enable-single-sign-on"></a>Postup povolení jednotného přihlašování
Zřizování cloudu funguje s jednotným přihlašováním.  V současné době není k dispozici možnost Povolit jednotné přihlašování při instalaci agenta. k povolení jednotného přihlašování (SSO) ale můžete použít následující postup. 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>Krok 1: stažení a extrakce Azure AD Connectch souborů
1.  Nejdřív Stáhněte nejnovější verzi [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2.  Otevřete příkazový řádek pomocí oprávnění správce a přejděte k souboru MSI, který jste právě stáhli.
3.  Spusťte následující příkaz:  `msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`
4. Změňte FilePath a extractfolder tak, aby odpovídaly vaší cestě k souboru a názvu složky pro extrakci.  Obsah by měl být nyní ve složce pro extrakci.

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>Krok 2: import bezproblémového modulu pro jednotné přihlašování do PowerShellu

1. Stáhněte si a nainstalujte [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Přejděte do `%programfiles%\Microsoft Azure Active Directory Connect` složky.
3. Pomocí tohoto příkazu Importujte modul prostředí PowerShell pro bezproblémové přihlašování: `Import-Module .\AzureADSSO.psd1` .

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Krok 3: Získejte seznam doménových struktur služby Active Directory, na kterých je povolené bezproblémové jednotné přihlašování.

1. Spusťte PowerShell jako správce. V prostředí PowerShell volejte `New-AzureADSSOAuthenticationContext` . Po zobrazení výzvy zadejte přihlašovací údaje globálního správce vašeho tenanta.
2. Volání `Get-AzureADSSOStatus` . Tento příkaz vám poskytne seznam doménových struktur služby Active Directory (podívejte se na seznam domény), na kterém je tato funkce povolená.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Krok 4: povolení bezproblémového jednotného přihlašování pro každou doménovou strukturu služby Active Directory

1. Volání `Enable-AzureADSSOForest` . Po zobrazení výzvy zadejte přihlašovací údaje správce domény pro požadovanou doménovou strukturu služby Active Directory.

   > [!NOTE]
   >Uživatelské jméno pověření správce domény musí být zadáno ve formátu názvu účtu SAM (contoso\johndoe nebo contoso. com\johndoe). Část Doména uživatelského jména používáme k vyhledání řadiče domény správce domény pomocí DNS.

   >[!NOTE]
   >Použitý účet správce domény nesmí být členem skupiny Protected Users. V takovém případě se operace nezdaří.

2. Opakujte předchozí krok pro každou doménovou strukturu služby Active Directory, ve které chcete tuto funkci nastavit.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Krok 5. Povolení funkce ve vašem tenantovi

Chcete-li zapnout funkci ve vašem tenantovi, zavolejte `Enable-AzureADSSO -Enable $true` .
