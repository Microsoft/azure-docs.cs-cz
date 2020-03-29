---
title: Pokyny k licencování pro spolupráci na b2B – Služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Spolupráce Azure Active Directory B2B nevyžaduje placené licence Azure AD, ale můžete také získat placené funkce pro uživatele typu Host B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28d02160163da4081ad8adbe233b27fee970a0df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74868843"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Pokyny k licencování služby Azure Active Directory B2B

Díky spolupráci mezi podniky azure active directory (Azure AD) můžete pozvat externí uživatele (nebo "uživatele typu hosta), aby používali placené služby Azure AD. Některé funkce jsou zdarma, ale pro všechny placené funkce Azure AD můžete pozvat až pět uživatelů typu Host pro každou licenci edice Azure AD, kterou vlastníte pro zaměstnance nebo uživatele, který není hostem ve vašem tenantovi.

> [!NOTE]
> Podrobnosti [Azure Active Directory pricing](https://azure.microsoft.com/pricing/details/active-directory/) o cenách Azure AD a funkcích spolupráce B2B najdete v informacích o cenách Služby Azure AD.

Licence pro uživatele typu Host B2B se automaticky vypočítávají a vykazují na základě poměru 1:5. 

Uživatelé typu Host navíc můžou používat bezplatné funkce Azure AD bez dalších licenčních požadavků. Uživatelé typu Host mají přístup k bezplatným funkcím Azure AD, i když nemáte žádné placené licence Azure AD. 

## <a name="examples-calculating-guest-user-licenses"></a>Příklady: Výpočet licencí pro uživatele typu Host
Jakmile určíte, kolik uživatelů typu Host potřebuje přístup k placeným službám Azure AD, ujistěte se, že máte dostatek placených licencí Azure AD, které pokryjí uživatele typu Host v požadovaném poměru 1:5. Zde je několik příkladů:

- Chcete pozvat 100 uživatelů typu Host do aplikací nebo služeb Azure AD a zajistit správu přístupu a zřizování. Pro 50 těchto uživatelů typu Host chcete také vyžadovat vícefaktorové a podmíněný přístup, takže pro tyto funkce budete potřebovat 10 licencí Azure AD Premium P1. Pokud plánujete používat funkce Identity Protection s uživateli typu Host, budete potřebovat licence Azure AD Premium P2 ve stejném poměru 1:5, abyste pokryli uživatele typu Host.
- Chcete pozvat 60 uživatelů typu Host, kteří všichni vyžadují vícefaktorové povolení, takže musíte mít alespoň 12 licencí Azure AD Premium P1. Máte 10 zaměstnanců s licencemi Azure AD Premium P1, což by umožnilo až 50 uživatelů typu Host v rámci poměru licencí 1:5. Budete si muset zakoupit další dvě licence Premium P1, aby pokryli 10 dalších uživatelů typu Host.

## <a name="next-steps"></a>Další kroky

Podívejte se na následující prostředky na Azure AD B2B spolupráce:

* [Ceny služby Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
* [Nejčastější dotazy týkající se spolupráce Azure Active Directory s B2B](faq.md)
