---
title: Co je Správa oprávnění Azure AD? (Preview) – Azure Active Directory
description: Získejte přehled o Azure Active Directory, Správa nároků a jak ho použít ke správě přístupu k skupiny, aplikace a weby SharePoint Online pro interní a externí uživatele.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/05/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbb4001e9496d31d9c2879721f8cf8e26b74ddf3
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204552"
---
# <a name="what-is-azure-ad-entitlement-management-preview"></a>Co je Správa oprávnění Azure AD? (Preview)

> [!IMPORTANT]
> Správa nároků Azure Active Directory (Azure AD) je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Zaměstnancům v organizacích potřebují přístup k různým skupinám, aplikace a weby k provedení své práce. Správa tohoto přístupu je náročná. Ve většině případů není žádná uspořádaný seznam všech prostředků, které uživatel potřebuje pro projekt. Vedoucí projektu bude mít dostatečné povědomí o prostředky, které potřebuje, osoby zapojené a jak dlouho bude trvat projektu. Ale projektový manažer obvykle nemá oprávnění ke schválení nebo udělit přístup ostatním uživatelům. Tento scénář složitější při pokusu o práci s externí jednotlivce nebo společnosti.

Správa nároků Azure Active Directory (Azure AD) můžete spravovat přístup k skupiny, aplikace a weby SharePoint Online pro interní uživatele a také uživatele mimo vaši organizaci.

## <a name="why-use-entitlement-management"></a>Proč používat správu nárok?

Organizace často potýkají při správě přístupu k prostředkům, jako například:

- Uživatelé nemusí vědět, jaký přístup by měly mít
- Uživatelé mohou mít problémy s nalezením správné jednotlivcům nebo správné prostředky
- Jakmile uživatelům najít a získají přístup k prostředku, může obsahovat přístup k déle, než je vyžadováno pro obchodní účely

Tyto problémy jsou compounded pro uživatele, kteří potřebují přístup z jiného adresáře, jako je například externí uživatele, kteří jsou z organizace dodavatelského řetězce nebo jinými obchodními partnery. Příklad:

- Organizace nemusí vědět vše určeným osobám v jiné adresáře pozvat je
- I v případě, že organizace byli schopni tyto uživatele pozvat, organizace pravděpodobně mějte na paměti ke správě všech přístup uživatele konzistentně

Správa oprávnění Azure AD může pomoci tyto problémy.

## <a name="what-can-i-do-with-entitlement-management"></a>Co mám dělat se správou nárok?

Tady jsou některé funkce správy oprávnění:

- Vytváření balíčků související prostředky, které uživatelé mohou požadovat
- Požadavky na prostředky a vypršení platnosti přístupu definují pravidla o tom
- Řízení životního cyklu přístup pro interní i externí uživatele
- Delegování správy prostředků
- Určit schvalovatele, které chcete schválit žádosti
- Vytváření sestav můžete sledovat historii

Přehled řízení oprávnění a zásady správného řízení identit podívejte se na následující video z konference Ignite 2018:

>[!VIDEO https://www.youtube.com/embed/aY7A0Br8u5M]

## <a name="what-resources-can-i-manage"></a>Jaké prostředky můžete spravovat?

Toto jsou typy prostředků, můžete spravovat přístup k pomocí správy oprávnění:

- Skupiny zabezpečení Azure AD
- Skupiny Office 365
- Azure AD podnikové aplikace, včetně aplikací SaaS a vlastní integrované aplikace, které podporují federace nebo zřizování
- SharePoint Online kolekce webů a weby

Můžete také řídit přístup k jiným prostředkům, které spoléhají na skupiny zabezpečení Azure AD nebo skupinám Office 365.  Příklad:

- Počet uživatelských licencí pro Microsoft Office 365 můžete udělit pomocí skupiny zabezpečení služby Azure AD, přístup k balíčku a konfigurace [licencování na základě skupiny](../users-groups-roles/licensing-groups-assign.md) pro tuto skupinu
- Uživatelům můžete udělit přístup ke správě prostředků Azure pomocí skupiny zabezpečení služby Azure AD, přístup k balíčku a vytvoření [přiřazení Azure role](../../role-based-access-control/role-assignments-portal.md) pro tuto skupinu

## <a name="what-are-access-packages-and-policies"></a>Co jsou balíčky přístupu a zásady?

Správa nároků zavádí koncepci *přístup k balíčku*. Balíček přístup je sada všech prostředků, které uživatel potřebuje k práci na projektu nebo provádění své práce. Prostředky zahrnují přístup ke skupinám, aplikace nebo weby. Balíčky přístupu se používají k řízení přístupu pro vaše interní zaměstnance a také uživatele mimo vaši organizaci. Přístup balíčky jsou definovány v kontejnerech volá *katalogy*.

Balíčky přístup také zahrnovat jeden nebo více *zásady*. Zásady definuje pravidla nebo ochranného zábradlí pro přístup k balíčku přístup. Povolení zásady vynutí, že jsou pouze správní uživatelé udělen přístup, s vhodnými zdroji a pro správné množství času.

![Přístup k balíčku a zásady](./media/entitlement-management-overview/elm-overview-access-package.png)

S balíčkem aplikace přístup a její zásady definuje správce přístupu k balíčku:

- Zdroje a prostředky
- Role uživatelů musí pro prostředky
- Interní uživatele a externí uživatele, které jsou vhodné pro vyžádání přístupu
- Proces schvalování a uživatelé, kteří můžete schválit nebo odepřít přístup
- Doba trvání přístupu uživatele

Následující diagram ukazuje příklad různé prvky ve správě oprávnění. Ukazuje dva balíčky příklad přístup.

- **Přístup k balíčku 1** zahrnuje jednu skupinu jako prostředek. Přístup je definovaný pomocí zásad, která umožňuje skupinu uživatelů v adresáři s požadavkem o přístup.
- **Přístup k balíčku 2** zahrnuje skupinu, aplikace a webu Sharepointu Online jako prostředky. Přístup je definována s dvě různé zásady. První zásady umožňují skupinu uživatelů v adresáři s požadavkem o přístup. Druhá zásada umožňuje uživatelům v externí adresáři žádat o přístup.

![Přehled správy oprávnění](./media/entitlement-management-overview/elm-overview.png)

## <a name="external-users"></a>Externí uživatelé

Při použití [Azure AD pro mezifiremní (B2B)](../b2b/what-is-b2b.md) pozvat prostředí, musí v e-mailové adresy externím uživatelům typu Host chcete přenést do adresáře prostředků a práci s znáte. Toto funguje skvělé, když pracujete na projektu menší či krátkodobé a všichni účastníci už znáte, ale je to obtížnější spravovat, pokud máte velké množství uživatelů, kterou chcete pracovat, nebo pokud účastníky v průběhu času měnit.  Například můžete může pracovat s jinou organizací a mít jeden bod kontaktu s dané organizace, ale v čase další uživatelé z této organizace také potřebovat přístup.

Pomocí správy oprávnění můžete definovat zásady, které umožňuje uživatelům z organizací, kterou zadáte, které jsou také pomocí Azure AD, chcete-li mít možnost požádat o přístup k balíčku. Můžete určit, zda je vyžadováno schválení a datem vypršení platnosti pro přístup. Pokud je vyžadováno schválení, můžete také určit jako schvalovatele jeden nebo více uživatelů od externí organizace, který jste dříve pozvali – vzhledem k tomu, které můžete chtít vědět, kteří externí uživatelé z jejich organizace potřebují přístup. Po nakonfigurování balíček přístup, můžete poslat odkaz balíček přístup k vaší kontaktní osoby v externí organizace. Tento kontakt můžete sdílet s ostatními uživateli v externí organizace, a můžou použít tento odkaz a požádat o přístup k balíčku.  Uživatelé z této organizace, kteří už byli pozváni do adresáře můžete také použít tento odkaz.

Při schválení žádosti se správa nároků zřízení uživatele s potřebnou úroveň přístupu, které mohou zahrnovat vyzve uživatele, pokud nejsou již ve vašem adresáři. Azure AD automaticky vytvoří účet B2B pro ně.  Všimněte si, že správce má dřív omezený počet organizací, které jsou povolené pro spolupráci, nastavením [B2B povolují nebo odpírají seznamu](../b2b/allow-deny-list.md) chcete povolit nebo blokovat zve do jiné organizace.  Pokud uživatel není povolený v seznamu povolených nebo blokovaných pak nebude pozvaný.

Vzhledem k tomu, že nechcete, aby externí uživatel přístup k naposledy navždy, zadáte datum vypršení platnosti v zásadách, jako je například 180 dnů. Po uplynutí 180 dnů pokud jejich přístup se neobnoví, Správa nároků odebere veškerý přístup přidružené k přístupu k balíčku.  Pokud uživatel, který je pozván prostřednictvím správy oprávnění nemá žádná další přiřazení přístupu k balíčku, potom při mohly být ztraceny jejich poslední přiřazení účtu B2B bude zablokováno přihlášení po dobu 30 dnů a následně odstraněny.  Zabráníte tím, jak narůstá zbytečné účtů.  

## <a name="terminology"></a>Terminologie

Abyste lépe pochopili Správa nároků a jeho dokumentace, měli byste zkontrolovat následující podmínky.

| Ukončení nebo koncept | Popis |
| --- | --- |
| Správa nároků | Služba, která se přiřadí, odvolá a spravuje přístup k balíčkům. |
| přístup k balíčku | Kolekce oprávnění a zásady pro prostředky, které uživatelé mohou požadovat. Balíček přístup je vždy obsažen v katalogu. |
| žádost o přístup | Žádost o přístup k balíčku přístup. Žádost o obvykle prochází pracovního postupu. |
| policy | Sada pravidel, který definuje přístup životní cyklus, jako je například jak uživatelé získají přístup, který můžete schválit a jak dlouho mají uživatelé přístup. Příkladem zásad může být zaměstnance a externí přístup. |
| catalog | Kontejner související prostředky a přístup k balíčkům. |
| Obecné katalogu | Integrované katalog, který je vždy k dispozici. Přidání prostředků do katalogu obecné vyžaduje určitá oprávnění. |
| resource | Prostředek nebo služba (jako jsou skupiny, aplikace nebo webu), která uživatele lze udělit oprávnění. |
| Typ prostředku | Typ prostředku, skupiny, aplikace a weby SharePoint Online. |
| role prostředků | Kolekce oprávnění přidružené k prostředku. |
| adresář prostředků | Adresář, který má jeden nebo více prostředků sdílet. |
| přiřazení uživatelé | Přiřazení přístupu k balíčku pro uživatele nebo skupiny. |
| Povolit | Proces zpřístupnění uživatelé můžou žádat o přístup k balíčku. |

## <a name="license-requirements"></a>Licenční požadavky

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Specializované cloudech, jako je Azure Government, Azure Germany a Azure China 21Vianet, nejsou aktuálně k dispozici pro použití v této verzi preview.

## <a name="next-steps"></a>Další postup

- [Kurz: Vytvoření vaší první přístup k balíčku](entitlement-management-access-package-first.md)
- [Obvyklé scénáře](entitlement-management-scenarios.md)
