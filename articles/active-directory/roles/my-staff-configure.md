---
title: Použití mých zaměstnanců k delegování správy uživatelů (Preview) – Azure AD | Microsoft Docs
description: Delegování správy uživatelů pomocí mých zaměstnanců a jednotek pro správu
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.topic: how-to
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 05/08/2020
ms.author: rolyon
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 304d88dfcf0c72769f27911186df04c66224419e
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740138"
---
# <a name="manage-your-users-with-my-staff-preview"></a>Správa uživatelů pomocí mých zaměstnanců (Preview)

Moji zaměstnanci vám umožňují delegovat na označení autority, jako je manažer obchodu nebo vedoucí týmu, oprávnění k zajištění toho, aby jejich zaměstnanci měli přístup ke svým účtům Azure AD. Namísto spoléhání na centrální Helpdesk můžou organizace delegovat běžné úkoly, jako je resetování hesla nebo změna telefonních čísel pro správce týmu. Uživatel, který nemá přístup ke svému účtu, může v rámci svých pracovníků získat přístup jenom v několika kliknutích, aniž by to vyžadovalo Helpdesk nebo pracovníky IT.

Před konfigurací pracovníků pro vaši organizaci doporučujeme projít si tuto dokumentaci a také [dokumentaci pro uživatele](../user-help/my-staff-team-manager.md) , abyste měli přehled o funkcích a dopadu této funkce na uživatele. Dokumentaci pro uživatele můžete využít ke školení a přípravě uživatelů na nové prostředí a k zajištění úspěšného zavedení.

Ověřování pomocí serveru SMS pro uživatele je funkce verze Public Preview služby Azure Active Directory. Další informace o verzích Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verze Preview.

## <a name="how-my-staff-works"></a>Jak funguje Moji zaměstnanci

Moji zaměstnanci vycházejí z jednotek pro správu (Austrálie), což je kontejner prostředků, které se dají použít k omezení rozsahu administrativního řízení přiřazení role. V části Moji zaměstnanci se k definování podmnožiny uživatelů organizace, jako je například obchod nebo oddělení, používá Austrálie. Potom může být například správce týmu přiřazen k roli, jejíž obor je jeden nebo více jednotek Austrálie. V následujícím příkladu byla uživateli udělena role Správce ověřování a tři jednotky Austrálie jsou oborem role. Další informace o jednotkách správy najdete v tématu [Správa jednotek pro správu v Azure Active Directory](administrative-units.md).

## <a name="before-you-begin"></a>Než začnete

K dokončení tohoto článku potřebujete následující prostředky a oprávnění:

* Musíte mít aktivní předplatné Azure.

  * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému.

  * V případě potřeby [vytvořte tenanta Azure Active Directory](../fundamentals/sign-up-organization.md) nebo [přidružte předplatné Azure k vašemu účtu](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
* Aby bylo možné povolit ověřování pomocí serveru SMS, potřebujete oprávnění *globálního správce* v TENANTOVI Azure AD.
* Každý uživatel, který je povolený v zásadě metody ověřování pomocí textové zprávy, musí mít licenci, a to i v případě, že ho nepoužívají. Každý povolený uživatel musí mít jednu z následujících licencí Azure AD nebo Microsoft 365:

  * [Azure AD Premium P1 nebo P2](https://azure.microsoft.com/pricing/details/active-directory/)
  * [Microsoft 365 (M365) F1 nebo F3](https://www.microsoft.com/licensing/news/m365-firstline-workers)
  * [Enterprise mobility + Security (EMS) E3 nebo E5](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing) nebo [Microsoft 365 (M365) E3 nebo E5](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans)

## <a name="how-to-enable-my-staff"></a>Jak povolit Moji zaměstnanci

Po nakonfigurování jednotky Austrálie můžete tento obor použít pro uživatele, kteří mají přístup k mému zaměstnanci. Přístup k zaměstnancům mají jenom uživatelé, kteří mají přiřazenou roli správce. Pokud chcete povolit Moji zaměstnanci, proveďte následující kroky:

1. Přihlaste se k Azure Portal jako správce uživatele.
2. Procházením **Azure Active Directory**  >  **uživatelských nastavení** uživatelských  >  **funkcí**  >  **verze Preview spravovat nastavení uživatelských funkcí**.
3. V části **Správci mají přístup k mým pracovníkům**, můžete se rozhodnout povolit pro všechny uživatele, vybrané uživatele nebo žádný přístup uživatelů.

> [!Note]
> K zaměstnancům mají přístup pouze uživatelé, kterým byla přiřazena role správce. Pokud povolíte možnost Moji zaměstnanci pro uživatele, který nemá přiřazenou roli správce, nebude mít přístup k zaměstnancům.

## <a name="conditional-access"></a>Podmíněný přístup

Portál My personál můžete chránit pomocí zásad podmíněného přístupu Azure AD. Použijte ho pro úkoly, jako je třeba ověřování Multi-Factor Authentication, než přistoupíte k zaměstnancům.

Důrazně doporučujeme chránit své pracovníky pomocí [zásad podmíněného přístupu Azure AD](../conditional-access/index.yml). Pokud chcete, aby se zásady podmíněného přístupu používaly pro moje pracovníky, musíte ručně vytvořit instanční objekt služby my personál pomocí PowerShellu.

### <a name="apply-a-conditional-access-policy-to-my-staff"></a>Použití zásad podmíněného přístupu u mých zaměstnanců

1. Nainstalujte [rutiny Microsoft Graph beta prostředí PowerShell](https://github.com/microsoftgraph/msgraph-sdk-powershell/blob/dev/samples/0-InstallModule.ps1).
1. Spusťte následující příkazy:

   ```powershell
   Connect-Graph -Scopes "Directory.AccessAsUser.All"
   New-MgServicePrincipal -DisplayName "My Staff" -AppId "ba9ff945-a723-4ab5-a977-bd8c9044fe61"
   ```
1. Vytvořte zásady podmíněného přístupu, které se vztahují na cloudovou aplikaci moje zaměstnanci.

    ![Vytvoření zásady podmíněného přístupu pro aplikaci My personál](./media/my-staff-configure/conditional-access.png)

## <a name="using-my-staff"></a>Používání mých zaměstnanců

Když uživatel přejde na Moji zaměstnanci, zobrazí se názvy [jednotek pro správu](administrative-units.md) , se kterými mají oprávnění správce. V [dokumentaci pro uživatele mých zaměstnanců](../user-help/my-staff-team-manager.md)používáme termín "umístění", ve kterém se odkazuje na jednotky pro správu. Pokud oprávnění správce nemají obor AU, vztahují se oprávnění na celou organizaci. Po povolení mých zaměstnanců můžou uživatelé, kteří mají povolený a přiřazenou roli správce, k němu přistupovat prostřednictvím [https://mystaff.microsoft.com](https://mystaff.microsoft.com) . Můžou vybrat AU a zobrazit uživatele v této jednotce AU a vybrat uživatele, který otevře jeho profil.

## <a name="reset-a-users-password"></a>Resetování hesla uživatele

Následující role mají oprávnění resetovat heslo uživatele:

- [Správce ověřování](permissions-reference.md#authentication-administrator)
- [Správce privilegovaného ověřování](permissions-reference.md#privileged-authentication-administrator)
- [Globální správce](permissions-reference.md#global-administrator--company-administrator)
- [Správce helpdesku](permissions-reference.md#helpdesk-administrator)
- [Správce uživatelů](permissions-reference.md#user-administrator)
- [Správce hesel](permissions-reference.md#password-administrator)

V **okně Moji zaměstnanci** otevřete profil uživatele. Vyberte **resetovat heslo**.

- Pokud je uživatel pouze Cloud, můžete zobrazit dočasné heslo, které můžete uživateli udělit.
- Pokud je uživatel synchronizovaný z místní služby Active Directory, můžete zadat heslo, které splňuje vaše místní zásady služby AD. Toto heslo pak můžete dát uživateli.

    ![Indikátor průběhu resetování hesla a oznámení o úspěšnosti](./media/my-staff-configure/reset-password.png)

Uživatel musí změnit heslo při příštím přihlášení.

## <a name="manage-a-phone-number"></a>Správa telefonního čísla

V **okně Moji zaměstnanci** otevřete profil uživatele.

- Pokud chcete přidat telefonní číslo pro uživatele, vyberte část **Přidat telefonní číslo** .
- Pokud chcete změnit telefonní číslo, vyberte **Upravit telefonní číslo** .
- Pokud chcete odebrat telefonní číslo pro uživatele, vyberte **odebrat telefonní číslo** .

V závislosti na nastavení může uživatel potom použít telefonní číslo, které jste si nastavili, abyste se přihlásili pomocí SMS, prováděli vícefaktorové ověřování a prováděli Samoobslužné resetování hesla.

Chcete-li spravovat telefonní číslo uživatele, je nutné přiřadit jednu z následujících rolí:

- [Správce ověřování](permissions-reference.md#authentication-administrator)
- [Správce privilegovaného ověřování](permissions-reference.md#privileged-authentication-administrator)
- [Globální správce](permissions-reference.md#global-administrator--company-administrator)

## <a name="search"></a>Hledat

V organizaci můžete hledat oddělení Austrálie a uživatele ve vaší organizaci pomocí panelu hledání v části Moji zaměstnanci. Můžete vyhledávat ve všech centrech a uživatelích ve vaší organizaci, ale můžete provádět změny jenom pro uživatele, kteří jsou ve AU, přes který jste měli oprávnění správce.

Můžete také vyhledat uživatele v rámci AU. K tomu použijte panel hledání v horní části seznamu uživatelů.

## <a name="audit-logs"></a>Protokoly auditu

Protokoly auditu můžete zobrazit pro akce provedené ve složce Moji zaměstnanci na portálu Azure Active Directory. Pokud byl protokol auditu vygenerovaný akcí prováděnou v části zaměstnanci, zobrazí se tento údaj, který je uvedený pod dalšími PODROBNOSTmi v události auditu.

## <a name="next-steps"></a>Další kroky

Dokumentace pro uživatele [mých zaměstnanců](../user-help/my-staff-team-manager.md) 
 [Dokumentace ke správním jednotkám](administrative-units.md)