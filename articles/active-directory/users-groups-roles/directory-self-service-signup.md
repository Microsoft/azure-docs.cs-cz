---
title: Samoobslužná registrace pro uživatele ověřené e-mailem – Azure AD | Dokumenty společnosti Microsoft
description: Použití samoobslužné registrace v tenantovi Služby Azure Active Directory (Azure AD)
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74027634"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>Co je samoobslužná registrace pro Službu Azure Active Directory?

Tento článek vysvětluje, jak používat samoobslužné registrace k naplnění organizace ve službě Azure Active Directory (Azure AD). Pokud chcete převzít název domény od nespravované organizace Azure AD, přečtěte si informace [o převzetí nespravovaného adresáře jako správce](domains-admin-takeover.md).

## <a name="why-use-self-service-sign-up"></a>Proč používat samoobslužnou registraci?
* Získejte zákazníky ke službám, které chtějí, rychleji
* Vytváření e-mailových nabídek pro službu
* Vytvořte toky registrace založené na e-mailech, které uživatelům rychle umožní vytvářet identity pomocí jejich snadno zapamatovatelnéch pracovních e-mailových aliasů
* Adresář Azure AD vytvořený samoobslužnými službami lze převést na spravovaný adresář, který lze použít pro jiné služby.

## <a name="terms-and-definitions"></a>Pojmy a definice
* **Samoobslužné registrace**: Jedná se o metodu, kterou se uživatel zaregistruje ke cloudové službě a má identitu, která se pro ně automaticky vytvoří ve službě Azure AD na základě jejich e-mailové domény.
* **Nespravovaný adresář Azure AD**: Toto je adresář, kde se tato identita vytvoří. Nespravovaný adresář je adresář, který nemá žádného globálního správce.
* **Uživatel ověřený e-mailem**: Jedná se o typ uživatelského účtu ve službě Azure AD. Uživatel, který má identitu vytvořenou automaticky po přihlášení k samoobslužné nabídce, se označuje jako uživatel ověřený e-mailem. Uživatel ověřený e-mailem je pravidelným členem adresáře označeného creationmethod=EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Jak mohu ovládat samoobslužné nastavení?
Správci mají dnes dva samoobslužné ovládací prvky. Mohou určit, zda:

* Uživatelé se mohou připojit k adresáři prostřednictvím e-mailu
* Uživatelé se mohou licencovat pro aplikace a služby

### <a name="how-can-i-control-these-capabilities"></a>Jak mohu tyto možnosti ovládat?
Správce můžete nakonfigurovat tyto možnosti pomocí následujících rutiny Azure AD Set-MsolCompanySettings parametry:

* **AllowEmailVerifiedUsers** určuje, zda může uživatel vytvořit adresář nebo se k němu připojit. Pokud tento parametr nastavíte na $false, žádný uživatel ověřený e-mailem se k adresáři nemůže připojit.
* **AllowAdHocSubscriptions** řídí možnost pro uživatele provádět samoobslužné registrace. Pokud nastavíte tento parametr na $false, žádný uživatel může provést samoobslužné registrace.
  
AllowEmailVerifiedUsers a AllowAdHocSubscriptions jsou nastavení pro celý adresář, které lze použít pro spravovaný nebo nespravovaný adresář. Zde je příklad, kde:

* Spravujete adresář s ověřenou doménou, například contoso.com
* Pomocí spolupráce B2B z jiného adresáře můžete pozvatuserdoesnotexist@contoso.comuživatele, který ještě neexistuje ( ) v domovském adresáři contoso.com
* Domovský adresář má zapnutou možnost AllowEmailVerifiedUsers

Pokud jsou splněny předchozí podmínky, je v domovském adresáři vytvořen členský uživatel a v zvoucím adresáři je vytvořen uživatel typu Host B2B.

Registrace zkušebních verzí Flow a PowerApps nejsou řízeny nastavením **AllowAdHocSubscriptions.** Další informace najdete v těchto článcích:

* [Jak můžu stávajícím uživatelům zabránit v tom, aby začali používat Power BI?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [Otázky a odpovědi týkající se Flow ve vaší organizaci](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Jak ovládací prvky spolupracují?
Tyto dva parametry lze použít ve spojení k definování přesnější kontroly nad samoobslužnou registrací. Například následující příkaz umožní uživatelům provádět samoobslužné registrace, ale pouze v případě, že tito uživatelé již mají účet ve službě Azure AD (jinými slovy, uživatelé, kteří by potřebovali účet ověřený e-mailem, který má být vytvořen jako první, nemohou provést samoobslužnou registraci):

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

Následující vývojový diagram vysvětluje různé kombinace těchto parametrů a výsledné podmínky pro adresář a samoobslužné registrace.

![vývojový diagram samoobslužných ovládacích prvků registrace](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

Další informace a příklady použití těchto parametrů naleznete v tématu [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Další kroky

* [Přidání vlastního názvu domény do Služby Azure AD](../fundamentals/add-custom-domain.md)
* [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Referenční informace k rutinám Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)
* [Zavření pracovního nebo školního účtu v nespravovaném adresáři](users-close-account.md)
