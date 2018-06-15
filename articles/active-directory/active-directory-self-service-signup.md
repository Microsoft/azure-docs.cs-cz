---
title: Samoobslužná služba nebo zkušební registrace v Azure Active Directory | Microsoft Docs
description: Použít samoobslužné registrace v klienta služby Azure Active Directory (Azure AD)
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
ms.openlocfilehash: 67fd5ba9be2de1f79511c806ffaa0ae3001bfdcf
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33760285"
---
# <a name="what-is-self-service-signup-for-azure-active-directory"></a>Co je samoobslužné registrace pro Azure Active Directory?
Tento článek vysvětluje samoobslužné registrace a jak ho podporují v Azure Active Directory (Azure AD). Pokud chcete převzít kontrolu nad název domény klienta z nespravovaných Azure AD najdete v tématu [převzít kontrolu nad adresář nespravované jako správce](domains-admin-takeover.md).

## <a name="why-use-self-service-signup"></a>Proč používat samoobslužné registrace?
* Získejte zákazníků ke službám, které mají být rychlejší
* Vytvoření e mailové nabídky pro službu
* Vytvoření e mailové registrace toky, které rychle povolit uživatelům vytvářet identit pomocí jejich aliasy snadno zapamatovat pracovní e-mailu
* Samoobslužných-služeb vytvořené adresář Azure AD mohou být změněny na spravované adresáře, který lze použít pro jiné služby

## <a name="terms-and-definitions"></a>Termíny a definice
* **Samoobslužné registrace**: Toto je metoda, podle kterého uživatel zaregistruje cloudovou službu a má u nich automaticky vytvoří ve službě Azure AD identity založené na jejich e-mailovou doménu.
* **Nespravované adresář Azure AD**: Toto je adresář, kde se má vytvořit danou identitu. Nespravované adresáře se adresář, který má žádný globální správce.
* **Ověřit e-mailu uživatel**: Jedná se o typ uživatelského účtu ve službě Azure AD. Uživatel, který má identity vytvoří automaticky po registraci na nabídku samoobslužné služby se označuje jako uživatel s ověřenou e-mailu. Ověření e-mailu uživatel je členem regulární adresáře označené creationmethod = EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Jak řídit nastavení samoobslužné služby?
Správci mají dvou ovládacích prvků samoobslužné služby ještě dnes. Kontroly nad zda:

* Uživatelé mohou připojit adresáři e-mailem.
* Uživatelé mohou sami licence pro aplikace a služby.

### <a name="how-can-i-control-these-capabilities"></a>Jak můžete řídit tyto funkce?
Správce můžete nakonfigurovat tyto možnosti, pomocí následujících parametrů rutiny Set-MsolCompanySettings Azure AD:

* **AllowEmailVerifiedUsers** Určuje, jestli uživatel může vytvořit nebo připojit k nespravované adresáři. Pokud tento parametr nastavte na $false, žádní uživatelé ověřit e-mailu se můžete zapojit do adresáře.
* **AllowAdHocSubscriptions** řídí schopnost uživatelům provádět samoobslužné registrace. Pokud tento parametr nastavte na $false, žádní uživatelé provést samoobslužné registrace. 
  
  > [!NOTE]
  > Nejsou řídí tok a PowerApps zkušební přihlašování **AllowAdHocSubscriptions** nastavení. Další informace najdete v následujících článcích:
  > * [Jak může zabránit mé existující uživatelům v začne používat Power BI?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
  > * [Tok ve vaší organizaci otázek a odpovědí](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Jak ovládací prvky spolupracují?
Tyto dva parametry můžete použít ve spojení definovat přesnější kontrolu nad samoobslužné registrace. Například následující příkaz umožní uživatelům provádět samoobslužné registrace, ale pouze pokud tito uživatelé již máte účet ve službě Azure AD (jinými slovy, uživatele, kteří se potřebují účet ověřit e-mailu, který chcete vytvořit nejprve nelze provést samoobslužné registrace):

````
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
````
Následující vývojový diagram vysvětluje různé kombinace pro tyto parametry a výsledný podmínky k adresáři a samoobslužné registrace.

![][1]

Další informace a příklady použití těchto parametrů najdete v tématu [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Další postup
* [Přidání vlastního názvu domény do Azure AD](add-custom-domain.md)
* [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Referenční informace k rutinám Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
