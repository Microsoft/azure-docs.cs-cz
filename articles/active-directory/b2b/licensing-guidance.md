---
title: Doprovodné materiály k licencování spolupráce Azure Active Directory s B2B | Dokumentace Microsoftu
description: Azure Active Directory s B2B, které nevyžaduje spolupráci placené licence Azure AD, ale je můžete také získat placených funkcí pro uživatele typu Host B2B
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.openlocfilehash: d89fb86209af2074794d5a5abcdcebadec904799
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434270"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Doprovodné materiály k licencování spolupráce Azure Active Directory s B2B

Se spoluprací Azure Active Directory (Azure AD) business-to-business (B2B), můžete pozvat externího uživatele (nebo "uživatele typu host") použití vašich placených služeb Azure AD. U každé placených licencí Azure AD, můžete přiřadit uživateli, můžete pozvat až pět uživatelů typu Host v příspěvku na externí uživatele.

Uživatel typu Host je kdokoli, kdo není členem vaší organizace nebo některý z pobočky vaší organizace. Uživatelé typu Host je definována v jejich vztah k vaší organizaci, ne pomocí přihlašovacích údajů, které používají pro přihlášení. Ve skutečnosti uživatele typu Host se můžete přihlásit pomocí externí identity nebo pomocí přihlašovacích údajů, které vlastní vaše organizace.

Následují *není* uživatele typu Host:
- Zaměstnanci, dodavatelé místní nebo zástupci
- Zaměstnanci, dodavatelé místní ani interní zástupci o vaší pobočky

Licencování uživatelů typu Host B2B je automaticky vypočítá a ohlášených závislosti na poměru 1:5. V současné době není možné přiřadit B2B licence uživatele typu Host přímo uživatelům typu Host.

Existují určité situace, kdy není hlášena uživatele typu Host pomocí příspěvek 1:5 externí uživatele. Pokud uživatel typu Host už má placená licence Azure AD v organizaci vlastního uživatele, uživatel nebude využívat jednu licenci uživatele typu Host B2B. Kromě toho hosta, které uživatelé můžou používat bezplatné funkce Azure AD se žádné další licenční požadavky. Uživatelé typu Host mají přístup k bezplatným funkcím služby Azure AD i v případě, že nemáte žádné placené licence Azure AD. 

## <a name="examples-calculating-guest-user-licenses"></a>Příklady: Výpočet licencí uživatelům typu Host
Jakmile určíte, kolik uživatelů typu Host potřebují přístup k vaší placené služby Azure AD, ujistěte se, že máte dostatečně Azure AD placené licence k pokrytí uživatelů typu Host v poměru vyžaduje 1:5. Zde je několik příkladů:

- Chcete pozvat 100 uživatelů typu Host do aplikace Azure AD nebo služeb, a chcete přiřadit správu přístupu a zřizování pro všechny uživatele typu Host. Budete také chtít vyžadovat vícefaktorové ověřování a podmíněný přístup pro 50 tyto uživatele typu Host. Pro tuto kombinaci, budete potřebovat 10 licencí Azure AD Basic a 10 licencí Azure AD Premium P1. Pokud máte v plánu používat funkce Identity Protection spolu s vaší uživatele typu Host, bude nutné licence Azure AD Premium P2 stejný poměr 1:5 k pokrytí uživatelů typu Host.
- Chcete pozvat 60 uživatelů typu Host, kteří vyžadují vícefaktorové ověřování, proto musí mít alespoň 12 licence Azure AD Premium P1. Máte 10 zaměstnanci s licencí Azure AD Premium P1, které by umožnilo podle licencování poměr 1:5 až 50 uživatelů typu Host. Budete muset zakoupit další dvě licence Premium P1 pro 10 uživatelů typu Host Další.

## <a name="using-the-b2b-collaboration-api-to-invite-users-from-your-affiliates"></a>Pomocí spolupráce B2B rozhraní API se pozvat uživatele z vaší pobočky

Podle definice je uživatele typu Host B2B externího uživatele vyzvat k použití vašich placených aplikací Azure AD a služeb. Zaměstnancem, dodavatele místní služby AD nebo místním zástupcem vaší společnosti nebo některou z vaší pobočky garance spolupráce B2B, i v případě, že se používají funkce B2B. Zde je několik příkladů: 
- Chcete používat externí přihlašovací údaje (například sociálních identit) se pozvat uživatele, který je zaměstnanci vaší organizace. V tomto scénáři není v souladu s licenční požadavky a není povolena. Externí přihlašovací údaje, nenastavujte zaměstnanec externího uživatele.  
- Chcete použít rozhraní API pro B2B se pozvat uživatele z jedné z pobočky vaší organizace. I když tento scénář používá rozhraní API pro B2B se pozvat uživatele, se nepovažuje za spolupráce B2B ve službě. To není v souladu se s licenčním požadavkům protože uživatele z vaší přidružená není externím uživatelem. 

V obou těchto scénářích platí, lepším řešením je použití rozhraní API pro B2B se pozvat uživatele jako členy (invitedUserType = – člen) a přiřadit jim každou licenci Azure AD. 

## <a name="next-steps"></a>Další postup

Na spolupráce B2B ve službě Azure AD najdete v následujících zdrojích:

* [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
* [Nejčastější dotazy týkající se spolupráce Azure Active Directory s B2B](faq.md)
