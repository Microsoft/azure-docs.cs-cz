---
title: Delegovat oprávnění správce pro správu aplikací – Azure AD | Microsoft Docs
description: Udělení oprávnění pro správu přístupu k aplikacím v Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1e8a0f1919da125a571429e1efff06589c7e85a
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466707"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>Delegovat oprávnění k registraci aplikace v Azure Active Directory

Tento článek popisuje, jak pomocí oprávnění udělených vlastními rolemi v Azure Active Directory (Azure AD) řešit potřeby správy aplikací. V Azure AD můžete delegovat vytváření aplikací a oprávnění pro správu následujícími způsoby:

- [Omezení toho, kdo může vytvářet aplikace](#restrict-who-can-create-applications) a spravovat aplikace, které vytvářejí. Ve výchozím nastavení ve službě Azure AD mohou všichni uživatelé registrovat aplikace a spravovat všechny aspekty aplikací, které vytvářejí. Dá se omezit tak, aby se povolily jenom vybraným lidem, které mají oprávnění.
- [Přiřazení jednoho nebo více vlastníků k aplikaci](#assign-application-owners). Toto je jednoduchý způsob, jak někomu udělit možnost spravovat všechny aspekty konfigurace Azure AD pro konkrétní aplikaci.
- [Přiřazení předdefinované role správce](#assign-built-in-application-admin-roles) , která uděluje přístup ke správě konfigurace ve službě Azure AD pro všechny aplikace. Toto je doporučený způsob, jak udělit odborníkům na IT přístup ke správě rozsáhlých oprávnění konfigurace aplikace bez udělení přístupu ke správě jiných částí služby Azure AD, které nesouvisí s konfigurací aplikace.
- [Vytvoření vlastní role](#create-and-assign-a-custom-role-preview) definující velmi specifická oprávnění a jejich přiřazení někomu jinému jako omezeného vlastníka nebo v oboru adresáře (všechny aplikace) jako omezeného správce.

Je důležité zvážit udělení přístupu pomocí jedné z výše uvedených metod ze dvou důvodů. Nejprve delegování schopnosti provádět úlohy správy snižuje režii globálního správce. Za druhé, pomocí omezených oprávnění zlepšíte stav zabezpečení a omezíte potenciál na neoprávněný přístup. Pokyny k plánování zabezpečení rolí najdete v tématu [zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD](security-planning.md).

## <a name="restrict-who-can-create-applications"></a>Omezit, kdo může vytvářet aplikace

Ve výchozím nastavení ve službě Azure AD mohou všichni uživatelé registrovat aplikace a spravovat všechny aspekty aplikací, které vytvářejí. Všichni uživatelé mají taky možnost vyjádřit souhlas s aplikacemi, které přistupují k firemním datům jejich jménem. Můžete zvolit selektivní udělení těchto oprávnění tím, že nastavíte globální přepínače na ne a přidáte vybrané uživatele do role vývojář aplikace.

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>Zakázání výchozí možnosti vytváření registrací aplikací nebo jejich souhlasu s aplikacemi

1. Přihlaste se ke svojí organizaci Azure AD pomocí účtu s nárokem na roli globálního správce ve vaší organizaci Azure AD.
1. Nastavte jednu nebo obě z těchto možností:

    - Na [stránce uživatelská nastavení pro vaši organizaci](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)nastavte možnost **Uživatelé můžou registrovat nastavení aplikace** na ne. Tím se zakáže výchozí možnosti pro vytváření registrací aplikací pro uživatele.
    - V [nastaveních uživatelů pro podnikové aplikace](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)můžete nastavit, aby **uživatelé měli souhlas s aplikacemi, které přistupují k firemním datům v nastavení jejich jménem** na ne. Tím se zakáže výchozí možnosti pro uživatele, kteří budou souhlasit s přístupem k podnikovým datům za jejich jménem.

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>Udělení individuálních oprávnění k vytváření a souhlasům s aplikacemi, když je výchozí možnost zakázaná

Přiřaďte roli vývojáře aplikace, aby udělila možnost vytvářet registrace aplikací, když **Uživatelé mohou registrovat** aplikace nastavení je nastaveno na ne. Tato role také uděluje oprávnění k souhlasu jednoho vlastního uživatele, když **Uživatelé můžou udělit souhlas s aplikacemi, které přistupují k firemním datům, v nastavení jejich jménem** je nastavené na ne. Když uživatel vytvoří novou registraci aplikace jako chování systému, automaticky se přidá jako první vlastník. Oprávnění k vlastnictví poskytují uživateli možnost spravovat všechny aspekty registrace aplikace nebo podnikové aplikace, které vlastní.

## <a name="assign-application-owners"></a>Přiřazení vlastníků aplikace

Přiřazení vlastníků je jednoduchý způsob, jak udělit možnost spravovat všechny aspekty konfigurace Azure AD pro konkrétní registraci aplikace nebo podnikovou aplikaci. Když uživatel vytvoří novou registraci aplikace, bude jako chování systému automaticky přidán jako první vlastník. Oprávnění k vlastnictví poskytují uživateli možnost spravovat všechny aspekty registrace aplikace nebo podnikové aplikace, které vlastní. Původní vlastník je možné odebrat a přidat další vlastníky.

### <a name="enterprise-application-owners"></a>Vlastníci podnikových aplikací

Jako vlastník může uživatel spravovat konfiguraci podnikové aplikace specifické pro organizaci, jako je například konfigurace jednotného přihlašování, zřizování a přiřazování uživatelů. Vlastník může také přidat nebo odebrat další vlastníky. Na rozdíl od globálních správců můžou vlastníci spravovat jenom podnikové aplikace, které vlastní.

V některých případech podniková aplikace vytvořená z Galerie aplikací zahrnuje podnikovou aplikaci i registraci aplikace. Pokud je to pravda, přidání vlastníka do podnikové aplikace automaticky přidá vlastníka k odpovídající registraci aplikace jako vlastníka.

### <a name="to-assign-an-owner-to-an-enterprise-application"></a>Přiřazení vlastníka k podnikové aplikaci

1. Přihlaste se ke [svojí organizaci Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) pomocí účtu, který má nárok na správce aplikace nebo správce cloudové aplikace pro organizaci.
1. Na [stránce registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) pro organizaci vyberte aplikaci. otevře se stránka přehled pro aplikaci.
1. Pokud chcete zobrazit seznam vlastníků aplikace, vyberte **vlastníci** .
1. Vyberte **Přidat** a vyberte jednu nebo více vlastníků, které chcete přidat do aplikace.

> [!IMPORTANT]
> Uživatelé a instanční objekty můžou být vlastníky registrace aplikací. Pouze uživatelé můžou být vlastníci podnikových aplikací. Skupiny nelze přiřadit jako vlastníky obou.
>
> Vlastníci můžou do aplikace přidávat přihlašovací údaje a pomocí těchto přihlašovacích údajů zosobnit identitu aplikace. Aplikace může mít více oprávnění, než je vlastník, a proto by bylo zvýšení oprávnění nad tím, k čemu má vlastník přístup, jako uživatel nebo instanční objekt. Vlastník aplikace může při zosobnění aplikace potenciálně vytvořit nebo aktualizovat uživatele nebo jiné objekty v závislosti na oprávněních aplikace.

## <a name="assign-built-in-application-admin-roles"></a>Přiřadit předdefinované role Správce aplikací

Azure AD obsahuje sadu předdefinovaných rolí správce pro udělení přístupu ke správě konfigurace ve službě Azure AD pro všechny aplikace. Tyto role jsou doporučeným způsobem, jak udělit odborníkům na IT přístup ke správě rozsáhlých oprávnění konfigurace aplikace bez udělení přístupu ke správě jiných částí služby Azure AD, které nesouvisí s konfigurací aplikace.

- Správce aplikace: uživatelé v této roli můžou vytvářet a spravovat všechny aspekty podnikových aplikací, registrací aplikací a nastavení proxy aplikací. Tato role také uděluje možnost vyjádřit souhlas s delegovanými oprávněními a oprávnění aplikací s výjimkou Microsoft Graph. Uživatelé přiřazení k této roli nebudou přidáni jako vlastníci při vytváření nových registrací aplikací nebo podnikových aplikací.
- Správce cloudové aplikace: uživatelé v této roli mají stejná oprávnění jako role správce aplikace, kromě možnosti spravovat proxy aplikace. Uživatelé přiřazení k této roli nebudou přidáni jako vlastníci při vytváření nových registrací aplikací nebo podnikových aplikací.

Další informace a zobrazení popisu těchto rolí najdete v tématu [předdefinované role Azure AD](permissions-reference.md).

Postupujte podle pokynů v tématu [přiřazení rolí uživatelům pomocí průvodce Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md) , abyste přiřadili role správce aplikace nebo správce cloudové aplikace.

> [!IMPORTANT]
> Správci aplikací a správci cloudových aplikací můžou do aplikace přidat přihlašovací údaje a použít tyto přihlašovací údaje k zosobnění identity aplikace. Aplikace může mít oprávnění, která jsou zvýšením oprávnění nad oprávněními role správce. Správce v této roli může potenciálně vytvořit nebo aktualizovat uživatele nebo jiné objekty při zosobnění aplikace v závislosti na oprávněních aplikace.
> Žádná role neuděluje možnost spravovat nastavení podmíněného přístupu.

## <a name="create-and-assign-a-custom-role-preview"></a>Vytvoření a přiřazení vlastní role (Preview)

Vytváření vlastních rolí a přiřazování vlastních rolí je samostatné kroky:

- [Vytvořte vlastní *definici role*](custom-create.md) a [přidejte do ní oprávnění z přednastaveného seznamu](custom-available-permissions.md). Jedná se o stejná oprávnění, která se používají ve vestavěných rolích.
- [Vytvořte *přiřazení role*](custom-assign-powershell.md) , abyste přiřadili vlastní roli.

Toto oddělení vám umožní vytvořit jednu definici role a pak ji v různých *oborech* přiřadit mnohokrát. Vlastní roli je možné přiřadit v oboru v rámci organizace nebo ji můžete přiřadit v oboru, pokud je jeden objekt Azure AD. Příkladem oboru objektu je jediná registrace aplikace. V různých oborech je možné přiřadit stejnou definici role Sallyům přes všechny registrace aplikací v organizaci a pak Naveen jenom přes registraci aplikace se sestavami výdajů společnosti Contoso.

Tipy při vytváření a používání vlastních rolí pro delegování správy aplikací:
- Vlastní role udělují přístup jenom v aktuálních oknech pro registraci aplikací na portálu Azure AD. Neudělují přístup v okně registrace starší verze aplikace.
- Vlastní role neudělují přístup k portálu Azure AD, když je nastavení "omezení přístupu k portálu pro správu Azure AD" nastavené na hodnotu Ano.
- Registrace aplikací uživatel má přístup k použití přiřazení rolí, zobrazí se na kartě všechny aplikace na stránce pro registraci aplikace. Nezobrazují se na kartě vlastní aplikace.

Další informace o základech vlastních rolí najdete v tématu [Přehled vlastních rolí](custom-overview.md)a také o tom, jak [vytvořit vlastní roli](custom-create.md) a jak [přiřadit roli](custom-assign-powershell.md).

## <a name="next-steps"></a>Další kroky

- [Podtypy a oprávnění registrace aplikace](custom-available-permissions.md)
- [Odkaz na roli správce Azure AD](permissions-reference.md)
