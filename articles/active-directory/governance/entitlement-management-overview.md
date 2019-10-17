---
title: Co je správa nároků Azure AD? (Preview) – Azure Active Directory
description: Získejte přehled o Azure Active Directory správě nároků a o tom, jak ho můžete používat ke správě přístupu ke skupinám, aplikacím a webům SharePointu Online pro interní a externí uživatele.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/03/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: a467856550bf2deaab931b3fe2f54b7986f12f8a
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430309"
---
# <a name="what-is-azure-ad-entitlement-management-preview"></a>Co je správa nároků Azure AD? (Preview)

> [!IMPORTANT]
> Správa opravňujících k Azure Active Directory (Azure AD) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Zaměstnanci v organizacích potřebují k provádění svých úloh přístup k různým skupinám, aplikacím a webům. Správa tohoto přístupu je náročná. Ve většině případů není k dispozici žádný uspořádaný seznam všech prostředků, které uživatel pro projekt potřebuje. Vedoucí projektu má dobrý přehled o potřebných materiálech, zúčastněných jednotlivcůch a o tom, jak dlouho projekt bude trvat. Vedoucí projektu ale obvykle nemá oprávnění ke schválení nebo udělení přístupu jiným uživatelům. Tento scénář je složitější při pokusu o spolupráci s externími osobami nebo společnostmi.

Správa nároků Azure Active Directory (Azure AD) vám může pomáhat spravovat přístup ke skupinám, aplikacím a webům SharePointu Online pro interní uživatele a také uživatele mimo vaši organizaci.

Toto video poskytuje přehled o správě nároků a jeho obchodní hodnotě:

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="why-use-entitlement-management"></a>Proč používat správu nároků?

Podnikové organizace často čelí problémům při správě přístupu k prostředkům, jako jsou:

- Uživatelé nemusí znát, jaký má mít přístup.
- Uživatelé můžou mít potíže při hledání správných jednotlivců nebo správných prostředků.
- Jakmile uživatelé naleznou a získají přístup k prostředku, můžou mít přístup k delšímu, než je nutné pro obchodní účely.

Tyto problémy jsou složené pro uživatele, kteří potřebují přístup z jiného adresáře, například externích uživatelů, kteří pocházejí z organizací dodavatelských řetězců nebo jiných obchodních partnerů. Například:

- Organizace nemusí znát všechny konkrétní uživatele v jiných adresářích, aby je mohli pozvat.
- I v případě, že organizace byli schopni pozvat tyto uživatele, organizace si nemusí konzistentně spravovat přístup všech uživatelů.

Správa nároků Azure AD může tyto výzvy vyřešit.

## <a name="what-can-i-do-with-entitlement-management"></a>Co se dá dělat se správou nároků?

Tady jsou některé možnosti správy nároků:

- Vytváření balíčků souvisejících prostředků, které si uživatelé můžou vyžádat
- Definovat pravidla pro vyžádání prostředků a přístup k vypršení platnosti
- Řídit životní cyklus přístupu pro interní i externí uživatele
- Delegovat správu prostředků
- Určení schvalovatelů ke schválení požadavků
- Vytváření sestav pro sledování historie

Přehled řízení identit a správy nároků najdete v následujícím videu na konferenci Ignite 2018:

>[!VIDEO https://www.youtube.com/embed/aY7A0Br8u5M]

## <a name="what-resources-can-i-manage"></a>Jaké prostředky můžu spravovat?

Tady jsou typy prostředků, ke kterým můžete spravovat přístup pomocí správy nároků:

- Skupiny zabezpečení Azure AD
- Skupiny Office 365
- Podnikové aplikace Azure AD, včetně aplikací SaaS a aplikací integrovaných s vlastními, které podporují federaci nebo zřizování
- Weby a kolekce webů SharePointu Online

Můžete také řídit přístup k dalším prostředkům, které spoléhají na skupiny zabezpečení Azure AD nebo skupiny Office 365.  Například:

- Uživatelům můžete udělit licence pro systém Microsoft Office 365 pomocí skupiny zabezpečení Azure AD v balíčku přístupu a konfigurací [licencování na základě skupin](../users-groups-roles/licensing-groups-assign.md) pro tuto skupinu.
- Uživatelům můžete udělit přístup ke správě prostředků Azure pomocí skupiny zabezpečení Azure AD v balíčku pro přístup a vytvoření [přiřazení role Azure](../../role-based-access-control/role-assignments-portal.md) pro tuto skupinu.

## <a name="what-are-access-packages-and-policies"></a>Co jsou balíčky a zásady přístupu?

Správa nároků zavádí koncept *balíčku pro přístup*. Balíček pro přístup je sada všech prostředků, které uživatel potřebuje k práci na projektu nebo k provedení jejich práce. Prostředky zahrnují přístup ke skupinám, aplikacím nebo webům. Přístupové balíčky slouží k řízení přístupu k vašim interním zaměstnancům a také uživatelům mimo vaši organizaci. Balíčky přístupu jsou definované v kontejnerech nazývaných *katalogy*.

Balíčky pro přístup také obsahují jednu nebo více *zásad*. Zásady definují pravidla nebo guardrails pro přístup k balíčku přístupu. Povolení zásad vynutilo, že přístup k správným prostředkům a ke správnému času získá jenom oprávnění uživatelé.

![Přístup k balíčkům a zásadám](./media/entitlement-management-overview/elm-overview-access-package.png)

Pomocí balíčku pro přístup a jeho zásad definuje správce balíčků přístup:

- Materiály
- Role, které uživatelé potřebují pro prostředky
- Interní uživatelé a partnerské organizace externích uživatelů, kteří mají nárok na přístup
- Proces schvalování a uživatelé, kteří můžou schválit nebo odepřít přístup
- Doba trvání přístupu uživatele

Následující diagram znázorňuje příklad různých prvků ve správě nároků. Zobrazuje dva ukázkové balíčky přístupu.

- **Přístup k balíčku 1** zahrnuje jednu skupinu jako prostředek. Přístup je definován pomocí zásady, která umožňuje sadě uživatelů v adresáři požádat o přístup.
- **Přístup k balíčku 2** obsahuje skupinu, aplikaci a web SharePointu Online jako prostředky. Přístup je definován pomocí dvou různých zásad. První zásada umožňuje sadě uživatelů v adresáři požádat o přístup. Druhá zásada umožňuje uživatelům v externím adresáři požádat o přístup.

![Přehled správy nároků](./media/entitlement-management-overview/elm-overview.png)

## <a name="terminology"></a>Terminologie

Abyste lépe pochopili správu nároků a dokumentaci, měli byste si projít následující výrazy.

| Termín nebo koncept | Popis |
| --- | --- |
| Správa nároků | Služba, která přiřazuje, odvolává a spravuje balíčky přístupu. |
| přístup k balíčku | Sada prostředků, které tým nebo projekt potřebuje a řídí se zásadami. Přístupový balíček je vždycky obsažený v katalogu. |
| žádost o přístup | Žádost o přístup k prostředkům v balíčku pro přístup Požadavek obvykle projde pracovním postupem. |
| policy | Sada pravidel, která definuje životní cyklus přístupu, například způsob, jakým uživatelé získávají přístup, kteří můžou schvalovat a jak dlouho mají uživatelé přístup. Příklady zásad zahrnují přístup zaměstnanců a externí přístup. |
| zařazení | Kontejner souvisejících prostředků a přístupových balíčků. |
| Katalog pro obecné | Integrovaný katalog, který je vždy k dispozici. Pokud chcete přidat prostředky do katalogu obecné, vyžaduje určitá oprávnění. |
| Partner | Prostředek nebo služba (například skupina Office, skupina zabezpečení, aplikace nebo web SharePointu Online), ke kterým může uživateli být uděleno oprávnění. |
| Typ prostředku | Typ prostředku, který zahrnuje skupiny, aplikace a weby SharePointu Online. |
| role prostředku | Kolekce oprávnění přidružených k prostředku |
| Adresář prostředků | Adresář, který obsahuje jeden nebo více prostředků ke sdílení. |
| přiřazení uživatelé | Přiřazení balíčku přístupu uživateli, aby měl uživatel všechny role prostředků tohoto balíčku přístupu. |
| aby | Proces zpřístupnění balíčku přístupu uživatelům pro vyžádání. |

## <a name="license-requirements"></a>Licenční požadavky

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Specializované cloudy, například Azure Government, Azure Německo a Azure Čína 21Vianet, nejsou v tuto chvíli k dispozici pro použití v této verzi Preview.

### <a name="which-users-must-have-licenses"></a>Kteří uživatelé musí mít licence?

Pokud máte aktivní členské uživatele, musí mít váš tenant aspoň tolik licencí Azure AD Premium P2. Mezi aktivní členské uživatele v řízení nároků patří:

- Uživatel, který iniciuje nebo schválí požadavek na balíček pro přístup
- Uživatel, kterému byl přiřazen balíček přístupu. 
- Uživatel, který spravuje přístupové balíčky.

V rámci licencí pro členské uživatele můžete také uživatelům dovolit, aby mohli pracovat s řízením nároků na řadu uživatelů typu Host. Informace o tom, jak vypočítat počet uživatelů typu Host, které můžete zahrnout, najdete v tématu [Azure Active Directory doprovodné materiály k licencování B2B pro spolupráci](../b2b/licensing-guidance.md).

Informace o tom, jak přiřadit licence uživatelům, najdete v tématu [přiřazení nebo odebrání licencí pomocí portálu Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="next-steps"></a>Další kroky

- [Kurz: vytvoření prvního balíčku pro přístup](entitlement-management-access-package-first.md)
- [Obvyklé scénáře](entitlement-management-scenarios.md)
