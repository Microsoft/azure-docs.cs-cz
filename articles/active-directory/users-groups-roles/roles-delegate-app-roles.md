---
title: Delegovat trvalky správy aplikací – Azure AD | Dokumenty společnosti Microsoft
description: Udělení oprávnění pro správu přístupu k aplikacím ve službě Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 251bc1c2277f9e43543f95c49d0b730a5a41c3d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253036"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>Delegování oprávnění k registraci aplikací ve službě Azure Active Directory

Tento článek popisuje, jak používat oprávnění udělená vlastními rolemi ve službě Azure Active Directory (Azure AD) k řešení vašich potřeb správy aplikací. Ve službě Azure AD můžete delegovat oprávnění k vytváření a správě aplikací následujícími způsoby:

- [Omezení, kdo může vytvářet aplikace](#restrict-who-can-create-applications) a spravovat aplikace, které vytvářejí. Ve výchozím nastavení ve službě Azure AD mohou všichni uživatelé zaregistrovat registrace aplikací a spravovat všechny aspekty aplikací, které vytvářejí. To může být omezeno tak, aby bylo možné povolit pouze vybraným osobám oprávnění.
- [Přiřazení jednoho nebo více vlastníků k aplikaci](#assign-application-owners). Toto je jednoduchý způsob, jak někomu udělit možnost spravovat všechny aspekty konfigurace Azure AD pro konkrétní aplikaci.
- [Přiřazení integrované role pro správu,](#assign-built-in-application-admin-roles) která uděluje přístup ke správě konfigurace ve službě Azure AD pro všechny aplikace. Toto je doporučený způsob, jak udělit IT odborníkům přístup ke správě širokých oprávnění konfigurace aplikací bez udělení přístupu ke správě jiných částí Azure AD, které nesouvisejí s konfigurací aplikace.
- [Vytvoření vlastní role](#create-and-assign-a-custom-role-preview) definující velmi specifická oprávnění a její přiřazení někomu k rozsahu jedné aplikace jako omezenému vlastníkovi nebo v oboru adresáře (všechny aplikace) jako omezenému správci.

Je důležité zvážit udělení přístupu pomocí jedné z výše uvedených metod ze dvou důvodů. Delegování schopnosti provádět úlohy správy nejprve snižuje režii globálního správce. Za druhé, použití omezených oprávnění zlepšuje stav zabezpečení a snižuje potenciál pro neoprávněný přístup. Problémy s delegováním a obecné pokyny jsou popsány v [správě delegátů ve službě Azure Active Directory](roles-concept-delegation.md).

## <a name="restrict-who-can-create-applications"></a>Omezit, kdo může vytvářet aplikace

Ve výchozím nastavení ve službě Azure AD mohou všichni uživatelé zaregistrovat registrace aplikací a spravovat všechny aspekty aplikací, které vytvářejí. Každý má také možnost udělit souhlas s tím, aby aplikace přistupovaly k firemním datům jejich jménem. Tato oprávnění můžete selektivně udělit nastavením globálních přepínačů na hodnotu Ne a přidáním vybraných uživatelů do role Vývojář aplikací.

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>Zakázání výchozí možnosti vytvářet registrace aplikací nebo souhlas s aplikacemi

1. Přihlaste se ke své organizaci Azure AD pomocí účtu, který má nárok na roli globálního správce ve vaší organizaci Azure AD.
1. Nastavte jednu nebo obě následující:

    - Na [stránce Nastavení uživatele pro vaši organizaci](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)nastavte nastavení **Uživatelé mohou zaregistrovat aplikace** na ne. Tím zakážete výchozí možnost pro uživatele vytvářet registrace aplikací.
    - V [uživatelském nastavení podnikových aplikací](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)můžete nastavit **možnost Uživatelé souhlasit s tím, aby aplikace přistupující k firemním datům jejich jménem nastavily** možnost Ne. Tím zakážete výchozí možnost pro uživatele, aby souhlasili s aplikacemi, které přistupují k datům společnosti jejich jménem.

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>Udělení individuálních oprávnění k vytváření a souhlasu s aplikacemi, pokud je výchozí schopnost zakázána

Přiřaďte roli vývojáře aplikace, která udělí možnost vytvářet registrace aplikací, když **mohou uživatelé zaregistrovat** nastavení aplikací, je nastaveno na ne. Tato role také uděluje oprávnění k udělení souhlasu vlastním jménem, když **uživatelé mohou souhlasit s tím, aby aplikace, které přistupují k firemním datům jejich jménem,** bylo nastaveno na ne. Jako chování systému, když uživatel vytvoří novou registraci aplikace, jsou automaticky přidány jako první vlastník. Oprávnění k vlastnictví poskytují uživateli možnost spravovat všechny aspekty registrace aplikace nebo podnikové aplikace, které vlastní.

## <a name="assign-application-owners"></a>Přiřazení vlastníků aplikací

Přiřazení vlastníků je jednoduchý způsob, jak udělit možnost spravovat všechny aspekty konfigurace Azure AD pro konkrétní registraci aplikace nebo podnikovou aplikaci. Jako chování systému, když uživatel vytvoří novou registraci aplikace, jsou automaticky přidány jako první vlastník. Oprávnění k vlastnictví poskytují uživateli možnost spravovat všechny aspekty registrace aplikace nebo podnikové aplikace, které vlastní. Původního vlastníka lze odebrat a přidat další vlastníky.

### <a name="enterprise-application-owners"></a>Vlastníci podnikových aplikací

Jako vlastník může uživatel spravovat konfiguraci podniku specifickou pro organizaci, jako je konfigurace jednotného přihlašování, zřizování a přiřazení uživatelů. Vlastník může také přidat nebo odebrat další vlastníky. Na rozdíl od globálních správců mohou vlastníci spravovat pouze podnikové aplikace, které vlastní.

V některých případech zahrnují podnikové aplikace vytvořené z galerie aplikací podnikovou aplikaci i registraci aplikace. Pokud je to pravda, přidání vlastníka do podnikové aplikace automaticky přidá vlastníka do odpovídající registrace aplikace jako vlastníka.

### <a name="to-assign-an-owner-to-an-enterprise-application"></a>Přiřazení vlastníka k podnikové aplikaci

1. Přihlaste se ke [své organizaci](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) Azure AD pomocí účtu, který má nárok na správce aplikací nebo správce cloudových aplikací pro organizaci.
1. Na [stránce](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) Registrace aplikací pro organizaci vyberte aplikaci, která otevře stránku Přehled pro aplikaci.
1. Výběrem **možnosti Vlastníci** zobrazíte seznam vlastníků aplikace.
1. Vyberte **Přidat a** vyberte jednoho nebo více vlastníků, které chcete přidat do aplikace.

> [!IMPORTANT]
> Uživatelé a instanční objekty mohou být vlastníky registrací aplikací. Vlastníky podnikových aplikací mohou být pouze uživatelé. Skupiny nelze přiřadit jako vlastníky ani jednoho z nich.
>
> Vlastníci mohou přidat pověření do aplikace a použít tato pověření zosobnit identitu aplikace. Aplikace může mít více oprávnění než vlastník, a proto by bylo zvýšení oprávnění nad co vlastník má přístup jako uživatel nebo instanční objekt. Vlastník aplikace může potenciálně vytvořit nebo aktualizovat uživatele nebo jiné objekty při zosobnění aplikace, v závislosti na oprávněních aplikace.

## <a name="assign-built-in-application-admin-roles"></a>Přiřazení předdefinovaných rolí správce aplikace

Azure AD má sadu předdefinovaných rolí správce pro udělování přístupu ke správě konfigurace ve službě Azure AD pro všechny aplikace. Tyto role jsou doporučeným způsobem, jak poskytnout IT odborníkům přístup ke správě širokých oprávnění konfigurace aplikací bez udělení přístupu ke správě jiných částí Azure AD, které nesouvisejí s konfigurací aplikace.

- Správce aplikace: Uživatelé v této roli mohou vytvářet a spravovat všechny aspekty podnikových aplikací, registrací aplikací a nastavení proxy serveru aplikace. Tato role také uděluje možnost souhlasu s delegovanými oprávněními a oprávněními aplikací s výjimkou aplikace Microsoft Graph. Uživatelé přiřazení k této roli nejsou přidáni jako vlastníci při vytváření nových registrací aplikací nebo podnikových aplikací.
- Správce cloudových aplikací: Uživatelé v této roli mají stejná oprávnění jako role Správce aplikace, s výjimkou možnosti spravovat proxy aplikace. Uživatelé přiřazení k této roli nejsou přidáni jako vlastníci při vytváření nových registrací aplikací nebo podnikových aplikací.

Další informace a popis těchto rolí naleznete v tématu [Dostupné role](directory-assign-admin-roles.md#available-roles).

Postupujte podle pokynů v příručce Přiřadit role uživatelům pomocí návodu [služby Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md) k přiřazení rolí Správce aplikací nebo Správce cloudových aplikací.

> [!IMPORTANT]
> Správci aplikací a správci cloudových aplikací mohou do aplikace přidávat přihlašovací údaje a pomocí těchto pověření zosobňovat identitu aplikace. Aplikace může mít oprávnění, která jsou zvýšení oprávnění nad oprávnění role správce. Správce v této roli může potenciálně vytvořit nebo aktualizovat uživatele nebo jiné objekty při zosobnění aplikace, v závislosti na oprávněních aplikace.
> Ani jedna z rolí neuděluje možnost spravovat nastavení podmíněného přístupu.

## <a name="create-and-assign-a-custom-role-preview"></a>Vytvoření a přiřazení vlastní role (náhled)

Vytváření vlastních rolí a přiřazování vlastních rolí jsou samostatné kroky:

- [Vytvořte vlastní *definici role* ](roles-create-custom.md) a [přidejte k ní oprávnění z přednastaveného seznamu](roles-custom-available-permissions.md). Jedná se o stejná oprávnění používaná v předdefinovaných rolích.
- [Vytvořte *přiřazení role* ](roles-assign-powershell.md) pro přiřazení vlastní role.

Toto oddělení umožňuje vytvořit jednu definici role a pak ji přiřadit mnohokrát v různých *oborech*. Vlastní roli lze přiřadit v oboru celé organizace nebo ji lze přiřadit v oboru, pokud jeden objekt Azure AD. Příkladem oboru objektu je registrace jedné aplikace. Pomocí různých oborů lze stejnou definici role přiřadit Sally přes všechny registrace aplikací v organizaci a pak na Naveen pouze registraci aplikace Contoso Expense Reports.

Tipy při vytváření a používání vlastních rolí pro delegování správy aplikací:
- Vlastní role udělují přístup jenom v nejaktuálnějších panelech registrace aplikací na portálu Azure AD. Neudělují přístup v okně registrace starší verze aplikace.
- Vlastní role neudělují přístup k portálu Azure AD, když je nastavení uživatele "Omezit přístup k portálu správy Azure AD" nastaveno na Ano.
- Registrace aplikací, ke které má uživatel přístup, se zobrazují pouze na kartě Všechny aplikace na stránce registrace aplikace. Nezobrazují se na kartě "Vlastněné aplikace".

Další informace o základech vlastních rolí naleznete v [přehledu vlastních rolí](roles-custom-overview.md)a o [tom,](roles-create-custom.md) jak vytvořit vlastní roli a jak [přiřadit roli](roles-assign-powershell.md).

## <a name="next-steps"></a>Další kroky

- [Podtypy a oprávnění registrace aplikace](roles-custom-available-permissions.md)
- [Odkaz na roli správce Azure AD](directory-assign-admin-roles.md)
