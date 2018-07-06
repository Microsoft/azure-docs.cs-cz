---
title: Samoobslužná služba nebo zkušební registrace ve službě Azure Active Directory | Dokumentace Microsoftu
description: Použijte samoobslužnou registraci do tenanta služby Azure Active Directory (Azure AD)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 01/28/2018
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 99c5e99fa3bd33ef42e8df6ceba5be4be2cd1249
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37871633"
---
# <a name="what-is-self-service-signup-for-azure-active-directory"></a>Co je Samoobslužná registrace do služby Azure Active Directory?
Tento článek vysvětluje Samoobslužná registrace do služby a jak ho podporují v Azure Active Directory (Azure AD). Pokud chcete převzít kontrolu nad název domény z nespravovaného Azure AD tenanta, naleznete v tématu [převzít kontrolu nad nespravovaného adresáře jako správce](domains-admin-takeover.md).

## <a name="why-use-self-service-signup"></a>Proč používat samoobslužné registrace?
* Dostaňte zákazníky do služeb, které chtějí rychleji
* Vytvořit e-mailu na základě nabídky pro službu
* Vytvořit e mailových registrace toky, které rychle povolit uživatelům vytvářet identity, jejich aliasy snadno zapamatovat pracovní e-mailu
* Samoobslužných-provoz vytvořený adresář Azure AD můžete převeden na spravovaný adresář, který lze použít pro další služby

## <a name="terms-and-definitions"></a>Termíny a definice
* **Samoobslužná registrace do služby**: Toto je metoda, pomocí kterého uživatel zaregistruje ke cloudové službě a automaticky vytvoří pro ně ve službě Azure AD identity je založená na jejich e-mailové doméně.
* **Nespravovaného adresáře Azure AD**: Toto je adresář, kde se vytvoří tuto identitu. Nespravovaného adresáře je adresář, který nemá žádný globální správce.
* **Ověřit e-mailu uživatele**: Toto je typ uživatelského účtu ve službě Azure AD. Uživatel, který má identitu vytvoří automaticky po registraci na nabídku samoobslužné služby se označuje jako uživatel ověřený e-mailem. Jako uživatel ověřený e-mailem je běžný člen adresáře označené creationmethod = EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Jak můžu řídit nastavení samoobslužných služeb?
Správci mají dva ovládací prvky samoobslužných služeb ještě dnes. Můžete řídit, jestli:

* Uživatelé můžou připojit k adresáři e-mailem.
* Uživatelům můžete sami licencí pro aplikace a služby.

### <a name="how-can-i-control-these-capabilities"></a>Jak můžu rozhodnout o ně?
Správce můžete nakonfigurovat tyto funkce pomocí následujících parametrů rutiny Set-MsolCompanySettings Azure AD:

* **AllowEmailVerifiedUsers** řídí, jestli uživatel může vytvořit nebo připojit nespravovaný adresář. Pokud tento parametr nastavíte na $false, můžete k adresáři připojit žádné uživatele ověřený e-mailem.
* **AllowAdHocSubscriptions** Určuje, jestli pro uživatelům provádět samoobslužné registrace. Pokud tento parametr nastavíte na $false, žádné uživatelé mohou provádět samoobslužné registrace. 
  
  > [!NOTE]
  > Flow a PowerApps zkušební přihlašování neřídí **AllowAdHocSubscriptions** nastavení. Další informace najdete v následujících článcích:
  > * [Jak se mám chránit stávajícím uživatelům z začali používat Power BI?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
  > * [Flow ve vaší organizaci, funkce Q & A](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Jak ovládacích prvků spolupracují?
Tyto dva parametry můžete použít ve spojení k definování přesnější kontrolu nad Samoobslužná registrace do služby. Například následující příkaz umožní uživatelům provádět samoobslužné registrace, ale pouze v případě, že tito uživatelé už máte účet ve službě Azure AD (jinými slovy, uživatelé, kteří bude nutné vytvořit účet ověřený e-mailem nejprve nelze provádět samoobslužné registrace):

````
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
````
Následující vývojový diagram vysvětluje různé kombinace těchto parametrů a výsledné podmínky k adresáři a Samoobslužná registrace do služby.

![][1]

Další informace a příklady použití těchto parametrů najdete v tématu [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Další postup
* [Přidání vlastního názvu domény do Azure AD](../fundamentals/add-custom-domain.md)
* [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Referenční informace k rutinám Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/directory-self-service-signup/SelfServiceSignUpControls.png
