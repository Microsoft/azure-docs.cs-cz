---
title: Doprovodné materiály k licencování spolupráce Azure Active Directory s B2B | Dokumentace Microsoftu
description: Azure Active Directory s B2B, které nevyžaduje spolupráci placené licence Azure AD, ale je můžete také získat placených funkcí pro uživatele typu Host B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0032eb1e6b92abb130521945f64fda3133d9b7fc
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198877"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Doprovodné materiály k licencování spolupráce Azure Active Directory s B2B

Se spoluprací Azure Active Directory (Azure AD) business-to-business (B2B), můžete pozvat externího uživatele (nebo "uživatele typu host") použití vašich placených služeb Azure AD. U každé placených licencí Azure AD, můžete přiřadit uživateli, můžete pozvat až pět uživatelů typu Host v příspěvku na externí uživatele.

Licencování uživatelů typu Host B2B je automaticky vypočítá a ohlášených závislosti na poměru 1:5. V současné době není možné přiřadit B2B licence uživatele typu Host přímo uživatelům typu Host.

Kromě toho hosta, které uživatelé můžou používat bezplatné funkce Azure AD se žádné další licenční požadavky. Uživatelé typu Host mají přístup k bezplatným funkcím služby Azure AD i v případě, že nemáte žádné placené licence Azure AD. 

## <a name="examples-calculating-guest-user-licenses"></a>Příklady: Výpočet licencí uživatelům typu Host
Jakmile určíte, kolik uživatelů typu Host potřebují přístup k vaší placené služby Azure AD, ujistěte se, že máte dostatečně Azure AD placené licence k pokrytí uživatelů typu Host v poměru vyžaduje 1:5. Zde je několik příkladů:

- Chcete pozvat 100 uživatelů typu Host do aplikace Azure AD nebo služeb, a chcete přiřadit správu přístupu a zřizování pro všechny uživatele typu Host. Budete také chtít vyžadovat vícefaktorové ověřování a podmíněný přístup pro 50 tyto uživatele typu Host. Pro tuto kombinaci, budete potřebovat 10 licencí Azure AD Basic a 10 licencí Azure AD Premium P1. Pokud máte v plánu používat funkce Identity Protection spolu s vaší uživatele typu Host, bude nutné licence Azure AD Premium P2 stejný poměr 1:5 k pokrytí uživatelů typu Host.
- Chcete pozvat 60 uživatelů typu Host, kteří vyžadují vícefaktorové ověřování, proto musí mít alespoň 12 licence Azure AD Premium P1. Máte 10 zaměstnanci s licencí Azure AD Premium P1, které by umožnilo podle licencování poměr 1:5 až 50 uživatelů typu Host. Budete muset zakoupit další dvě licence Premium P1 pro 10 uživatelů typu Host Další.

## <a name="next-steps"></a>Další postup

Na spolupráce B2B ve službě Azure AD najdete v následujících zdrojích:

* [Ceny za Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
* [Nejčastější dotazy týkající se spolupráce Azure Active Directory s B2B](faq.md)
