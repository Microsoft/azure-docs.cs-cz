---
title: Delegovat role správce aplikace – Azure Active Directory | Dokumentace Microsoftu
description: Správa přístupu k aplikacím delegování rolí udělovat oprávnění ve službě Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 2db33a6a2f086d5dacaff04ae226841d32cdc99a
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513551"
---
# <a name="delegate-app-administrator-roles-in-azure-active-directory"></a>Delegovat role správce aplikace v Azure Active Directory

 Azure AD umožňuje delegovat správu přístupu k aplikacím na sadu předdefinovaných rolí pro správu. Kromě snížení režie globální správce, delegování specializované oprávnění ke správě úloh aplikace s přístupem můžete zlepšit tak stav zabezpečení a snížení rizika neoprávněného přístupu. Problémů s delegováním a obecné pokyny jsou popsány v [delegovat správu v Azure Active Directory](roles-concept-delegation.md).

## <a name="delegate-app-administration"></a>Delegovat správu aplikace

Následující role udělit oprávnění ke správě registrace aplikací, nastavení jednotného přihlašování, uživatele a skupiny přiřazení a souhlas s delegovaná oprávnění a oprávnění aplikací (s výjimkou Microsoft Graph a Azure AD Graph). Jediným rozdílem je, že aplikace Správce role navíc uděluje oprávnění ke správě nastavení Proxy aplikací. Ani jedna role uděluje možnost spravovat nastavení podmíněného přístupu.
> [!IMPORTANT]
> Uživatelé s touto rolí můžete přidat přihlašovací údaje k aplikaci a používat tyto přihlašovací údaje zosobnit identitu aplikace. Tento zosobnění identitu aplikace může být zvýšení oprávnění prostřednictvím co může uživatel provést v rámci svá přiřazení rolí ve službě Azure AD. Uživatel přiřazený k této roli by mohly vytvořit nebo aktualizovat uživatele nebo jiných objektů při zosobňování aplikace.

Chcete-li udělit schopnost spravovat přístup k aplikaci na webu Azure Portal:

1. Přihlaste se k vaší [tenanta Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) pomocí účtu, který oprávněné pro roli globálního správce tenanta.
2. Pokud máte dostatečná oprávnění, otevřete [role a správci stránky](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators).
3. Otevřete jednu z následujících rolí zobrazíte jeho přiřazení členů:
  * **Správce aplikace**
  * **Správce cloudové aplikace**
4. Na **členy** stránku pro roli, vyberte **přidat člena**.
5. Vyberte jednoho nebo více členů, které chcete přidat do role. <!--Members can be users or groups.-->

Popis pro tyto role v lze zobrazit [dostupných rolí](directory-assign-admin-roles.md#available-roles).

## <a name="delegate-app-registration"></a>Registrace aplikace delegáta

Ve výchozím nastavení všichni uživatelé můžou vytvářet registrace aplikací, ale můžete selektivně udělit oprávnění vytvářet registrace aplikací nebo oprávnění vyjádřit souhlas k autorizaci aplikace.

1. Přihlaste se k vaší [tenanta Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) pomocí účtu, který oprávněné pro roli globálního správce tenanta.
2. Když zakoupíte dostatečná oprávnění, nastavte jednu nebo obě z následujících akcí:
  * Na [stránka nastavení uživatele pro vašeho tenanta](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings), nastavte **uživatelé můžou registrovat aplikace** na Ne.
  * Na [uživatelská nastavení pro podnikové aplikace](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/), nastavte **uživatelé můžou udělit souhlas s aplikací, které přistupují k firemním datům jejich jménem** na Ne.
3. Pak přiřaďte uživatele, kteří potřebují toto oprávnění mají být členy role pro vývojáře aplikací podle potřeby.

Když se uživatel zaregistruje aplikace, jsou automaticky přidány jako první vlastník aplikace.

## <a name="delegate-app-ownership"></a>Delegovat vlastnictví aplikace

Vlastníci aplikace a vlastníci registrace aplikací můžou každý spravovat jenom aplikace aplikace nebo registrace aplikací, které vlastní. Například když přidáte vlastníka aplikace Salesforce, tento vlastník můžete spravovat přístup k a konfigurace pro Salesforce, ale ne všech ostatních aplikací. Aplikace může mít mnoho vlastníků a uživatel může být vlastníkem velký počet aplikací.

Vlastníka aplikace provádět následující akce:

* Změnit vlastnosti aplikace, jako je jméno a oprávnění žádostí aplikace
* Správa přihlašovacích údajů
* Konfigurace jednotného přihlašování
* Přiřadit přístup uživatelům
* Přidání nebo odebrání vlastníků
* Upravit manifest aplikace
* Publikujte aplikaci do Galerie aplikací

> [!IMPORTANT]
> Uživatelé s touto rolí můžete přidat přihlašovací údaje k aplikaci a používat tyto přihlašovací údaje zosobnit identitu aplikace. Tento zosobnění identitu aplikace může být zvýšení oprávnění prostřednictvím co může uživatel provést v rámci svá přiřazení rolí ve službě Azure AD. Uživatel přiřazený k této roli by mohly vytvořit nebo aktualizovat uživatele nebo jiných objektů při zosobňování aplikace.

Vlastník registrace aplikace můžete zobrazit a upravit registraci aplikace.

<!-- ### To assign an enterprise app ownership role to a user

1. Sign in to your [Azure AD tenant](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) with an account that is the Global Administrator for the tenant.
2. On the [Roles and administrators page](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators), open one of the following roles to see its member assignments:
  * **Enterprise Application Owner**
  * **Application Registration Owner**
3. On the **Members** page for the role, select **Add member**.
4. Select one or more members to add to the role. -->

### <a name="to-assign-an-owner-to-an-application"></a>Přiřadit vlastníka do aplikace

1. Přihlaste se k vaší [tenanta Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) pomocí účtu, který nárok aplikace správce nebo správce cloudové aplikace pro příslušného tenanta.
2. Na [stránce registrace aplikace](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) pro tenanta, vyberte aplikace, aby **přehled** stránku pro aplikaci.
3. Vyberte **vlastníky** zobrazíte seznam vlastníků pro aplikaci.
4. Vyberte **přidat** k výběru jednoho nebo několika vlastníka do aplikace přidat.

### <a name="to-assign-an-owner-to-an-application-registration"></a>Přiřadit vlastníka registraci aplikace

1. Přihlaste se k vaší [tenanta Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) pomocí účtu, který oprávnění pro správce aplikace nebo role správce cloudové aplikace v tenantovi.
2. Pokud máte dostatečná oprávnění na [stránky podnikové aplikace](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) tenanta, vyberte registrace aplikace ho otevřete.
3. Vyberte **nastavení**.
4. Vyberte **vlastníky** na **nastavení** stránku, aby zobrazil seznam vlastníků pro aplikaci.
5. Vyberte **přidat vlastníka** k výběru jednoho nebo několika vlastníka do aplikace přidat.

## <a name="next-steps"></a>Další postup

* [Reference k roli správce Azure AD](directory-assign-admin-roles.md)
