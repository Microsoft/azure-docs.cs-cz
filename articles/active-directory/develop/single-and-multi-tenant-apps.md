---
title: Aplikace s jedním a více klienty ve službě Azure AD
titleSuffix: Microsoft identity platform
description: Přečtěte si o funkcích a rozdílech mezi aplikacemi s jedním klientem a více klienty ve službě Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: 38cb1222a64b1759528749caa15dfb1bb906cef6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159909"
---
# <a name="tenancy-in-azure-active-directory"></a>Nájem ve službě Azure Active Directory

Azure Active Directory (Azure AD) organizuje objekty, jako jsou uživatelé a aplikace do skupin *nazývaných tenanty*. Klienti umožňují správci nastavit zásady pro uživatele v rámci organizace a aplikace, které organizace vlastní, aby splnili své zásady zabezpečení a provozní zásady. 

## <a name="who-can-sign-in-to-your-app"></a>Kdo se může přihlásit k vaší aplikaci?

Pokud jde o vývoj aplikací, vývojáři si můžou vybrat, aby si při registraci aplikace na [webu Azure Portal](https://portal.azure.com)nakonfigurovali svou aplikaci tak, aby byla jednoklientská nebo víceklientská .
* Aplikace s jedním tenantem jsou dostupné jenom v tenantovi, ve které byly registrované, označované také jako jejich domácí tenant.
* Aplikace pro více klientů jsou k dispozici uživatelům v jejich domovském tenantovi i v jiných tenantech.

Na webu Azure Portal můžete nakonfigurovat aplikaci tak, aby byla jednoklientská nebo víceklientská, a to takto:

| Cílová skupina | Jeden/více klientů | Kdo se může přihlásit | 
|----------|--------| ---------|
| Pouze účty v tomto adresáři | Jeden tenant | Všechny uživatelské účty a účty hosta ve vašem adresáři mohou používat vaši aplikaci nebo rozhraní API.<br>*Tuto možnost použijte, pokud je váš cílový okruh uživatelů interní ve vaší organizaci.* |
| Účty v libovolném adresáři Azure AD | Víc klientů | Vaši aplikaci nebo rozhraní API můžou používat všichni uživatelé a hosté s pracovním nebo školním účtem od Microsoftu. To zahrnuje školy a firmy, které používají Office 365.<br>*Tuto možnost použijte, pokud je vaším cílovým okruhem uživatelů obchodní nebo vzdělávací zákazníci.* |
| Účty ve všech adresářích Azure AD a osobních účtech Microsoftu (jako je Skype, Xbox, Outlook.com) | Víc klientů | Vaši aplikaci nebo rozhraní API můžou používat všichni uživatelé s pracovním nebo školním nebo osobním účtem Microsoft. Zahrnuje školy a firmy, které používají Office 365, stejně jako osobní účty, které se používají k přihlášení ke službám, jako je Xbox a Skype.<br>*Tuto možnost použijte k cílení na nejširší sadu účtů Microsoft.* | 

## <a name="best-practices-for-multi-tenant-apps"></a>Doporučené postupy pro aplikace s více tenanty

Vytváření skvělých aplikací pro více klientů může být náročné kvůli počtu různých zásad, které mohou správci IT nastavit ve svých tenantech. Pokud se rozhodnete vytvořit aplikaci pro více klientů, postupujte podle následujících doporučených postupů:

* Otestujte aplikaci v tenantovi, který nakonfiguroval [zásady podmíněného přístupu](../azuread-dev/conditional-access-dev-guide.md).
* Postupujte podle zásady nejmenšího přístupu uživatelů, abyste zajistili, že vaše aplikace požaduje pouze oprávnění, která skutečně potřebuje. Vyhněte se žádosti o oprávnění, která vyžadují souhlas správce, protože to může uživatelům zabránit v získání vaší aplikace vůbec v některých organizacích. 
* Zadejte příslušné názvy a popisy všech oprávnění, která zveřejňujete jako součást aplikace. To pomáhá uživatelům a správcům vědět, s čím souhlasí, když se pokusí používat rozhraní API vaší aplikace. Další informace naleznete v části doporučené postupy v [průvodci oprávněními](v2-permissions-and-consent.md).

## <a name="next-steps"></a>Další kroky

* [Jak převést aplikaci na víceklientské](howto-convert-app-to-be-multi-tenant.md)
