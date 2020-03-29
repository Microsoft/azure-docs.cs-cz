---
title: Principy delegování rolí správce – Azure Active Directory | Dokumenty společnosti Microsoft
description: Modely delegování, příklady a zabezpečení rolí ve službě Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fa3c6bf39dbef601fe64e125999f519f725f2e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67083778"
---
# <a name="delegate-administration-in-azure-active-directory"></a>Delegování správy ve službě Azure Active Directory

S organizačním růstem přichází složitost. Jednou z běžných odpovědí je snížit některé úlohy správy přístupu pomocí rolí správců služby Azure Active Directory (AD). Uživatelům můžete přiřadit co nejmenší oprávnění k přístupu k jejich aplikacím a k provádění jejich úkolů. I v případě, že nepřiřadíte roli globálního správce každému vlastníkovi aplikace, umístíte odpovědnost za správu aplikací na stávající globální správce. Existuje mnoho důvodů pro organizaci, která směřuje k decentralizovanější administrativě. Tento článek vám může pomoci naplánovat delegování ve vaší organizaci.

<!--What about reporting? Who has which role and how do I audit?-->

## <a name="centralized-versus-delegated-permissions"></a>Centralizovaná a delegovaná oprávnění

S růstem organizace může být obtížné sledovat, kteří uživatelé mají konkrétní role správce. Pokud má zaměstnanec práva správce, která by neměl, může být vaše organizace náchylnější k narušení zabezpečení. Obecně platí, kolik správců podporujete a jak podrobné jejich oprávnění jsou závisí na velikosti a složitosti vašeho nasazení.

* V malých nasazeních nebo otekvaných konceptech jeden nebo několik správců dělá všechno. Není tu žádná delegace. V takovém případě vytvořte každého správce s rolí Globální správce.
* Ve větších nasazeních s více počítači, aplikacemi a stolními počítači je potřeba více delegování. Několik správců může mít konkrétnější funkční odpovědnosti (role). Někteří mohou být například správci privilegované identity a jiní správci aplikací. Správce může navíc spravovat pouze určité skupiny objektů, jako jsou zařízení.
* I větší nasazení může vyžadovat ještě podrobnější oprávnění, plus případně správci s nekonvenční nebo hybridní role.

Na portálu Azure AD můžete [zobrazit všechny členy libovolné role](directory-manage-roles-portal.md), které vám pomůžou rychle zkontrolovat oprávnění nasazení a delegovat.

Pokud máte zájem o delegování přístupu k prostředkům Azure namísto přístupu pro správu ve službě Azure AD, přečtěte si část [Přiřazení role řízení přístupu (RBAC) na základě rolí](../../role-based-access-control/role-assignments-portal.md).

## <a name="delegation-planning"></a>Plánování delegací

Je to práce na vývoji delegování model, který vyhovuje vašim potřebám. Vývoj modelu delegování je iterativní proces návrhu a doporučujeme postupovat takto:

* Definování rolí, které potřebujete
* Delegování správy aplikací
* Udělit možnost registrace žádostí
* Delegování vlastnictví aplikace
* Vytvoření bezpečnostního plánu
* Vytvoření nouzových účtů
* Zabezpečení rolí správce
* Dočasné zvýšení oprávnění

## <a name="define-roles"></a>Definování rolí

Určete úkoly služby Active Directory, které provádějí správci, a způsob jejich mapování na role. Podrobné [popisy rolí](directory-manage-roles-portal.md) můžete zobrazit na webu Azure Portal.

Každý úkol by měl být vyhodnocen z hlediska četnosti, důležitosti a obtížnosti. Tato kritéria jsou zásadními aspekty definice úkolu, protože řídí, zda má být oprávnění delegováno:

* Úkoly, které děláte rutinně, mají omezené riziko a jsou triviální dokončit jsou vynikající kandidáty pro delegování.
* Úkoly, které děláte zřídka, ale mají velký vliv na celou organizaci a vyžadují vysoké úrovně dovedností, by měly být před delegováním velmi pečlivě zváženy. Místo toho můžete [dočasně zvýšit úroveň účtu na požadovanou roli](../active-directory-privileged-identity-management-configure.md) nebo znovu přiřadit úkol.

## <a name="delegate-app-administration"></a>Delegování správy aplikací

Šíření aplikací v rámci vaší organizace může zatížit váš model delegování. Pokud umístí zátěž pro správu přístupu k aplikacím na globálního správce, je pravděpodobné, že model zvyšuje jeho režii, jak plyne čas. Pokud jste lidem udělili roli globálního správce pro akce, jako je konfigurace podnikových aplikací, můžete je nyní převést na následující méně privilegované role. To pomáhá zlepšit stav zabezpečení a snižuje potenciál pro nešťastné chyby. Nejvíce privilegované role správce aplikací jsou:

* Role **Správce aplikace,** která umožňuje spravovat všechny aplikace v adresáři, včetně registrací, nastavení jednotného přihlášení, přiřazení uživatelů a skupin a licencování, nastavení proxy aplikace a souhlasu. Neuděluje možnost spravovat podmíněný přístup.
* Role **Správce cloudových aplikací,** která uděluje všechny možnosti správce aplikace, s tím rozdílem, že neuděluje přístup k nastavení proxy aplikace (protože nemá žádné místní oprávnění).

## <a name="delegate-app-registration"></a>Delegování registrace aplikace

Ve výchozím nastavení mohou všichni uživatelé vytvářet registrace aplikací. Chcete-li selektivně udělit možnost vytvářet registrace žádostí:

* Nastavit **uživatelé mohou zaregistrovat aplikace** na ne v **uživatelském nastavení**
* Přiřazení uživatele k roli Vývojář aplikace

Chcete-li selektivně udělit souhlas s povolením přístupu k údajům pro aplikaci:

* Nastavit **Uživatelé mohou souhlasit s tím, aby aplikace přistupující k firemním datům jejich jménem** Ne v **nastavení uživatele**
* Přiřazení uživatele k roli Vývojář aplikace

Když vývojář aplikace vytvoří novou registraci aplikace, jsou automaticky přidány jako první vlastník.

## <a name="delegate-app-ownership"></a>Delegování vlastnictví aplikace

Pro ještě jemnější delegování přístupu k aplikacím můžete přiřadit vlastnictví jednotlivým podnikovým aplikacím. To doplňuje stávající podporu pro přiřazení vlastníků registrace aplikace. Vlastnictví je přiřazeno na základě podnikové aplikace v okně Podnikové aplikace. Výhodou je, že vlastníci mohou spravovat pouze podnikové aplikace, které vlastní. Můžete například přiřadit vlastníka pro aplikaci Salesforce a tento vlastník může spravovat přístup a konfiguraci pro Salesforce a žádné jiné aplikace. Podniková aplikace může mít mnoho vlastníků a uživatel může být vlastníkem mnoha podnikových aplikací. Existují dvě role vlastníka aplikace:

* Role **Vlastník podnikových aplikací** uděluje možnost spravovat podnikové aplikace, které uživatel vlastní, včetně nastavení jednotného přihlašování, přiřazení uživatelů a skupin a přidání dalších vlastníků. Neuděluje možnost spravovat nastavení proxy aplikace nebo podmíněný přístup.
* Role **Vlastník registrace aplikace** uděluje možnost spravovat registrace aplikací pro aplikaci, kterou uživatel vlastní, včetně manifestu aplikace a přidání dalších vlastníků.

## <a name="develop-a-security-plan"></a>Vytvoření bezpečnostního plánu

Azure AD poskytuje rozsáhlý průvodce plánování a provádění plánu zabezpečení na rolích správce Azure AD, [zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení](directory-admin-roles-secure.md).

## <a name="establish-emergency-accounts"></a>Vytvoření nouzových účtů

Chcete-li zachovat přístup k úložišti správy identit v případě problému, připravte účty nouzového přístupu podle [možnosti Vytvořit účty pro správu nouzového přístupu](directory-emergency-access.md).

## <a name="secure-your-administrator-roles"></a>Zabezpečení rolí správce

Útočníci, kteří získají kontrolu nad privilegovanými účty, mohou způsobit obrovské škody, takže tyto účty nejprve ochraňte pomocí [zásady přístupu podle směrného plánu,](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/22/baseline-security-policy-for-azure-ad-admin-accounts-in-public-preview/) která je ve výchozím nastavení dostupná všem klientům Azure AD (ve verzi Public Preview). Zásady vynucují vícefaktorové ověřování na privilegovaných účtech Azure AD. Následující role Azure AD jsou pokryty zásady směrného plánu Azure AD:

* Globální správce
* Správce SharePointu
* Správce Exchange
* Správce podmíněného přístupu
* Správce zabezpečení

## <a name="elevate-privilege-temporarily"></a>Dočasné zvýšení oprávnění

U většiny každodenních aktivit ne všichni uživatelé potřebují globální práva správce a ne všichni by měli být trvale přiřazeni k roli globálního správce. Když uživatelé potřebují oprávnění globálního správce, měli by aktivovat přiřazení role v Azure AD [Privileged Identity Management](../active-directory-privileged-identity-management-configure.md) na vlastní účet nebo alternativní účet pro správu.

## <a name="next-steps"></a>Další kroky

Odkaz na popisy rolí Azure AD najdete v tématu [Přiřazení rolí správců ve službě Azure AD.](directory-assign-admin-roles.md)
