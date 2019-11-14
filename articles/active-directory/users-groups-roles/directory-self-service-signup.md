---
title: Samoobslužná registrace pro uživatele ověřené e-maily – Azure AD | Microsoft Docs
description: Použití samoobslužné registrace v tenantovi Azure Active Directory (Azure AD)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 953837e22cdd3ba8a54d702eac61461739db82d2
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74027634"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>Co je samoobslužná registrace do služby Azure Active Directory?

Tento článek vysvětluje, jak pomocí samoobslužné registrace naplnit organizaci v Azure Active Directory (Azure AD). Pokud chcete převzít název domény z nespravované organizace Azure AD, přečtěte si téma [převzetí nespravovaného adresáře jako správce](domains-admin-takeover.md).

## <a name="why-use-self-service-sign-up"></a>Proč používat samoobslužné registrace?
* Získejte zákazníkům rychlejší služby, které chtějí.
* Vytváření nabídek na základě e-mailů pro službu
* Vytváření e-mailových toků pro zápis, které umožňují uživatelům rychle vytvářet identity pomocí jejich snadno zapamatování e-mailových aliasů
* Samoobslužný adresář služby Azure AD se dá přepínat na spravovaný adresář, který se dá použít pro jiné služby.

## <a name="terms-and-definitions"></a>Pojmy a definice
* **Samoobslužná registrace**: Jedná se o metodu, kterou si uživatel zaregistruje do cloudové služby a automaticky pro ně vytvořila identitu v Azure AD na základě jejich e-mailové domény.
* **Nespravovaný adresář služby Azure AD**: Jedná se o adresář, ve kterém je tato identita vytvořená. Nespravovaný adresář je adresář, který nemá žádného globálního správce.
* **Uživatel ověřený e-mailem**: Jedná se o typ uživatelského účtu ve službě Azure AD. Uživatel, který má vytvořenou identitu automaticky po registraci k samoobslužné nabídce, se označuje jako uživatel ověřený e-mailem. Uživatel ověřený pomocí e-mailu je běžným členem adresáře označeného creationmethod = EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Návody nastavení samoobslužného řízení?
Správci mají ještě dva samoobslužné ovládací prvky. Můžou řídit, jestli:

* Uživatelé se můžou k adresáři připojit prostřednictvím e-mailu.
* Uživatelé můžou licenci vlastnit pro aplikace a služby.

### <a name="how-can-i-control-these-capabilities"></a>Jak můžu řídit tyto možnosti?
Správce může tyto možnosti nakonfigurovat pomocí následujících parametrů rutiny Azure AD set-MsolCompanySettings:

* **AllowEmailVerifiedUsers** určuje, zda uživatel může vytvořit nebo připojit adresář. Pokud tento parametr nastavíte na $false, k adresáři se nemůže připojit žádný uživatel ověřený e-mailem.
* **AllowAdHocSubscriptions** řídí schopnost uživatelů provádět samoobslužné registrace. Pokud nastavíte tento parametr na $false, nemůže uživatel provádět samoobslužné registrace.
  
AllowEmailVerifiedUsers a AllowAdHocSubscriptions jsou nastavení pro celá adresáře, která se dají použít pro spravovaný nebo nespravovaný adresář. Tady je příklad, kde:

* Můžete spravovat adresář s ověřenou doménou, například contoso.com.
* Pro pozvání uživatele, který ještě neexistuje (userdoesnotexist@contoso.com) v domovském adresáři contoso.com, můžete použít spolupráci B2B z jiného adresáře.
* V domovském adresáři je zapnutá funkce AllowEmailVerifiedUsers.

Pokud jsou předchozí podmínky splněné, vytvoří se v domovském adresáři členský uživatel a v adresáři pro pozvání se vytvoří uživatel typu Host B2B.

Zápisy Flow a PowerApps zkušební verze se neřídí nastavením **AllowAdHocSubscriptions** . Další informace najdete v následujících článcích:

* [Jak můžu stávajícím uživatelům zabránit, aby začali používat Power BI?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [Postup ve vaší organizaci Q & A](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Jak fungují společné ovládací prvky?
Tyto dva parametry lze použít společně k definování přesnější kontroly nad samoobslužnou registrací. Například následující příkaz umožní uživatelům provádět samoobslužné registrace, ale pouze v případě, že uživatelé již mají účet ve službě Azure AD (jinými slovy, uživatelé, kteří by potřebovali vytvořit účet ověřený e-mailem, nemůže provést samoobslužné přihlášení):

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

Následující vývojový diagram vysvětluje různé kombinace těchto parametrů a výsledné podmínky pro adresář a samoobslužné registrace.

![Vývojový diagram pro samoobslužné ovládací prvky pro registraci](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

Další informace a příklady použití těchto parametrů naleznete v tématu [set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Další kroky

* [Přidání vlastního názvu domény do Azure AD](../fundamentals/add-custom-domain.md)
* [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Referenční informace k rutinám Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)
* [Zavřete svůj pracovní nebo školní účet v nespravovaném adresáři.](users-close-account.md)
