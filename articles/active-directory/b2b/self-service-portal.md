---
title: Registrační portál samoobslužné služby pro spolupráci Azure Active Directory s B2B | Dokumentace Microsoftu
description: Spolupráce B2B ve službě Azure Active Directory podporuje vaše vztahy s ostatními společnostmi tím, že vašim obchodním partnerům umožní selektivní přístup ke podnikovým aplikacím
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/08/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 94001b005a883c172cab279029b47ac1ad0c0de5
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35642802"
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Samoobslužný portál pro registraci spolupráce B2B ve službě Azure AD

Zákazníci můžou neumí pomocí integrovaných funkcí, které jsou vystaveny prostřednictvím [webu Azure portal](https://portal.azure.com) a [přístupového panelu aplikací](https://myapps.microsoft.com) pro koncové uživatele. Však může být nutné přizpůsobit pracovní postup registrace pro uživatele B2B podle potřeb vaší organizace. Můžete to udělat pomocí [e-mailové pozvánce API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

Jako zvoucí organizaci nemusí vědět předem domluvili kdo jednotlivé externí spolupracovníci, kteří potřebují přístup k vašim prostředkům. Potřebujete způsob, jak pro uživatele z partnerských společností do sami zaregistrovat pomocí sady zásad, které vám jako zvoucí organizaci ovládací prvky. Tento scénář je možné provádět prostřednictvím rozhraní API. Je [ukázkového projektu na Githubu](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) , který právě tohle dělá.

Tohoto projektu z Githubu ukazuje, jak můžou organizace pomocí rozhraní API k poskytování technologie QoS, samoobslužné registrace funkce pro vaši důvěryhodní partneři s pravidly, která určují aplikace, které bude mít přístup k. Uživatelé partnerských můžete získat přístup k prostředkům, kdy je potřebují. To je možné provést bezpečně, bez nutnosti jejich zvoucí organizaci ručně připojit. Můžete snadno nasadíte projekt do předplatného Azure podle vašeho výběru.

## <a name="as-is-code"></a>Jako – kód

Tento kód je k dispozici jako ukázka demonstruje použití Azure Active Directory s B2B pozvánku rozhraní API. By měl být upravena váš vývojový tým nebo partnera a byste měli zkontrolovat před jejím nasazením v případě produkčního prostředí.

## <a name="next-steps"></a>Další postup

* [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
* [Licencování spolupráce v Azure AD B2B](licensing-guidance.md)
* [Spolupráce Azure Active Directory s B2B – nejčastější dotazy (FAQ)](faq.md)