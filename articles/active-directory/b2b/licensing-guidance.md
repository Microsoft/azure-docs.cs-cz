---
title: Doprovodné materiály k licencování spolupráce Azure Active Directory s B2B | Dokumentace Microsoftu
description: Azure Active Directory s B2B, které nevyžaduje spolupráci placené licence Azure AD, ale je můžete také získat placených funkcí pro uživatele typu Host B2B
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 08/09/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 2050952eb924e1eee5e6d7d6312d9cd02f475d10
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39093092"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Doprovodné materiály k licencování spolupráce Azure Active Directory s B2B

Možnosti spolupráce B2B ve službě Azure AD můžete pozvat uživatele typu Host do tenanta Azure AD a povolení jejich přístupu služby Azure AD a dalších prostředků ve vaší organizaci. Pokud chcete poskytnout přístup k placených funkcí Azure AD, musí mít licenci uživatele typu Host pro spolupráci B2B odpovídající licence Azure AD. 

Zejména:
* Bezplatných možností Azure AD jsou k dispozici pro uživatele typu Host bez další licence.
* Pokud chcete poskytnout přístup k placených funkcí Azure AD uživatelům B2B, musí mít dostatek licencí pro podporu těchto uživatelů typu Host B2B.
* Pozvání tenanta s placenou licenci Azure AD je spolupráce B2B ve službě užívací práva další pět B2B uživatelům typu Host do tenanta pozvat.
* Zákazníka, který vlastní pozval tenanta musí být ten, který má-li určit, kolik spolupráce B2B, které uživatelé potřebují placených funkcí Azure AD. V závislosti na placené služby Azure AD placené funkce, které chcete použít pro vaše uživatele typu Host, musí mít dostatečně Azure AD licence pro uživatele spolupráce B2B ve stejném poměru 5:1.

Uživatele typu Host spolupráce B2B se přidá jako uživatele z partnerskou firmou, nejsou zaměstnanci vaší organizace nebo zaměstnance z jiné firmy ve vaší konglomerátu. Uživatel typu Host B2B se můžete přihlásit pomocí externí přihlašovací údaje nebo přihlašovací údaje, které vlastní vaše organizace, jak je popsáno v tomto článku. 

Jinými slovy licencování B2B je nastaven není tak jak se uživatel ověřuje, ale spíše vztahu uživatele pro vaši organizaci. Pokud tito uživatelé nejsou partnerů, jsou jinak považovány v licenční podmínky. Se nepovažují za uživatele spolupráce B2B pro účely licencování i v případě, že jejich UserType je označena jako "Guest". Byste měli licencovat za normálních okolností na jednu licenci na uživatele. Mezi tyto uživatele patří:
* Vaši zaměstnanci
* Zaměstnanci přihlášení pomocí externí identity
* Zaměstnanec z jiné firmy ve vaší konglomerátu


## <a name="licensing-examples"></a>Příklady licencování
- Zákazník chce pozvat 100 uživatele spolupráce B2B do jeho tenanta služby Azure AD. Zákazník přiřadí řízení přístupu a zřizování pro všechny uživatele, ale 50 uživatelů také vyžadovat vícefaktorové ověřování a podmíněného přístupu. 10 licencí Azure AD Basic a 10 licencí Azure AD Premium P1 pro tyto uživatele B2B správně, musí tento zákazník zakoupit. Pokud zákazník plánuje používat funkce Identity Protection spolu s uživatele B2B, musí mít licence Azure AD Premium P2 pro pokrytí pozvaným uživatelům stejný poměr 5:1.
- Zákazník má 10 zaměstnanci, kteří jsou všechny aktuálně licenci Azure AD Premium P1. Se teď chcete pozvat 60 uživatele B2B, které vyžadují vícefaktorové ověřování (MFA). V části pravidla licencování 5:1 zákazník musí mít alespoň 12 licencí Azure AD Premium P1 pro pokrytí všech 60 uživatele spolupráce B2B. Vzhledem k tomu, že už mají 10 licencí Premium P1 pro jejich 10 zaměstnance, aby měli práva pozvat 50 uživatelů B2B s funkcí Premium P1, jako je vícefaktorové ověřování. V tomto příkladu pak může musíte koupit 2 další licence Premium P1 pro pokrytí zbývajících 10 uživatele spolupráce B2B.

> [!NOTE]
> Neexistuje žádný způsob ještě k přiřazení licencí přímo uživatelům B2B k povolení tato práva uživatele spolupráce B2B.

Zákazníka, který vlastní pozval tenanta musí být ten, který má-li určit, kolik spolupráce B2B, které uživatelé potřebují placených funkcí Azure AD. V závislosti na tom, které placených funkcí Azure AD, který chcete použít pro vaše uživatele typu Host musí mít dostatečně Azure AD placené licence pro uživatele spolupráce B2B v poměru 5:1. 

## <a name="additional-licensing-details"></a>Další podrobnosti o licencování
- Není nutné skutečně přiřadit licence účtům uživatelů B2B. Na základě poměru 5:1, licencování je automaticky vypočítá a hlásí.
- Pokud ne, placené licence Azure AD existuje v rámci tenanta, získá každý pozvaný uživatel práva, která nabízí edice Free služby Azure AD.
- Pokud licenci B2B spolupráce uživatel už má placené služby Azure AD z jejich organizace, spotřebují není jedna z licencí spolupráce B2B pozval tenanta.

## <a name="advanced-discussion-what-are-the-licensing-considerations-when-we-add-users-from-a-conglomerate-organization-as-members-using-your-apis"></a>Pokročilé diskuse: jaké jsou licenční požadavky při jsme přidávání uživatelů z konglomerátu organizace jako "členy" použití rozhraní API?
Uživatel typu Host B2B je ten, který je z partnerské organizace pozvaný pro práci s organizací hostitele. Obvykle všechny ostatní případy nesplňuje jako B2B i jeho použití funkce B2B. Pojďme se podívat na dva možné případy zejména:

1. Pokud hostitel vyzývá zaměstnance pomocí adresy příjemce
  * Tento scénář není kompatibilní se našich zásad licencování a nedoporučuje se používat.

2. Pokud v organizaci hostitelů přidá uživatele z jiné organizace konglomerátu
  1. V takovém případě je uživatel vyzván pomocí rozhraní API pro B2B, ale tento případ se nedá tradičně B2B. V ideálním případě měli bychom mít tyto organizace pozvat další organizace uživatele jako členy (naše rozhraní API umožňuje, které). V takovém případě muset licence přiřazené těmto členům jim přístup k prostředkům ve zvoucí organizaci.

  2. Některé organizace chtít přidat přidat jako "Guest" jako zásadu pro uživatele vaší organizace. Existují dva možné případy tady:
      * Konglomerátu organizace používá Azure AD a pozvaní uživatelé mají licenci v jiné organizaci: v tomto případě Neočekáváme pozvaní uživatelé potřebovat použít vzorec 1:5 rozloženy dříve v tomto dokumentu. 

      * Konglomerátu organizace nepoužívá Azure AD nebo nemá odpovídající licence: V tomto případě postupujte podle vzorce 1:5 rozloženy dříve v tomto dokumentu.

## <a name="next-steps"></a>Další postup

Na spolupráce B2B ve službě Azure AD najdete v následujících článcích:

* [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
* [Spolupráce Azure Active Directory s B2B – nejčastější dotazy (FAQ)](faq.md)
