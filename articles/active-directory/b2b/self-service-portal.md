---
title: Registrační portál samoobslužné služby pro spolupráci Azure Active Directory s B2B | Microsoft Docs
description: Spolupráce B2B ve službě Azure Active Directory podporuje vaše vztahy s ostatními společnostmi tím, že vašim obchodním partnerům umožní selektivní přístup ke podnikovým aplikacím
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/08/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 5ee55034e84fe09484a2f7613cc2224be70fdebb
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Samoobslužný portál pro registraci spolupráce Azure AD B2B

Zákazníci můžete dělat mnohem s integrované funkce, které jsou k dispozici prostřednictvím [portál Azure](https://portal.azure.com) a [Panel přístupu aplikace](https://myapps.microsoft.com) pro koncové uživatele. Nicméně budete muset přizpůsobení pracovního postupu registrace pro uživatele B2B podle potřeb vaší organizace. Můžete to udělat pomocí [pozvánku rozhraní API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

Pozváním organizace nemusí vědět předem, kdo jsou jednotlivé externími spolupracovníky kteří potřebují přístup k prostředkům. Potřebujete způsob, jak pro uživatele z partnerských společností sami zaregistrovat pomocí sady zásad, které vy jako pozváním organizace ovládací prvky. Tento scénář je možné provádět prostřednictvím rozhraní API. Je [ukázkového projektu na Githubu](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) právě, který provádí.

Tento projekt Githubu ukazuje, jak může organizace pomocí rozhraní API poskytovat na základě zásad, samoobslužné funkce registrace pro důvěryhodným partnerům, pravidla, která určit aplikace, které mají přístup. Uživatelé partnera můžete získat přístup k prostředkům, kdy je potřebují. Můžete to udělají bezpečně, bez nutnosti jejich pozváním organizace ručně zařadit do provozu. Můžete snadno nasaďte projekt do předplatného služby Azure podle svého výběru.

## <a name="as-is-code"></a>Jako-je kód

Tento kód je k dispozici jako ukázka k předvedení využití pozvánku Azure Active Directory s B2B rozhraní API. Je nutné přizpůsobit váš vývojový tým nebo partnera a před jejím nasazením v produkční scénář by měl zkontrolovat.

## <a name="next-steps"></a>Další postup

* [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
* [Licencování Azure AD s B2B spolupráce](licensing-guidance.md)
* [Spolupráce Azure Active Directory s B2B nejčastější dotazy (FAQ)](faq.md)