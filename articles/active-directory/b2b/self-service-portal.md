---
title: Samoobslužný registrační portál pro spolupráci B2B – Azure AD
description: Spolupráce B2B ve službě Azure Active Directory podporuje vaše vztahy s ostatními společnostmi tím, že vašim obchodním partnerům umožní selektivní přístup ke podnikovým aplikacím
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 02/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: cfa67f529408efcc2a703a4f80b15143c774f0b9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77195789"
---
# <a name="self-service-for-azure-ad-b2b-collaboration-sign-up"></a>Samoobslužná registrace spolupráce Azure AD B2B

Zákazníci mají spoustu možností, jak využít integrované funkce, ke kterým mají přístup prostřednictvím webu [Azure Portal](https://portal.azure.com) a [přístupového panelu aplikací](https://myapps.microsoft.com) pro koncové uživatele. Pracovní postup onboardingu můžete ale potřebovat přizpůsobit pro uživatele B2B, aby vyhovoval potřebám vaší organizace.

## <a name="azure-ad-entitlement-management-for-b2b-guest-user-sign-up"></a>Správa oprávnění Azure AD pro registraci uživatele typu Host B2B

Jako zvoucí organizace nemusíte dopředu vědět, kdo jsou jednotliví externí spolupracovníci, kteří potřebují přístup k vašim prostředkům. Potřebujete způsob, jak se uživatelé z partnerských společností mohou zaregistrovat pomocí zásad, které ovládáte. Pokud chcete uživatelům z jiných organizací povolit žádost o přístup a po schválení být zřízeny s účty hosta a přiřazeny ke skupinám, aplikacím a webům SharePointu Online, můžete pomocí [správy nároků Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) nakonfigurovat zásady, které [spravují přístup pro externí uživatele](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users#how-access-works-for-external-users).

## <a name="azure-active-directory-b2b-invitation-api"></a>Rozhraní API pozvánky služby Azure Active Directory B2B

Organizace mohou pomocí [rozhraní API správce pozvánek microsoft graphu](https://docs.microsoft.com/graph/api/resources/invitation?view=graph-rest-1.0) vytvořit vlastní prostředí pro připojení pro uživatele typu Host B2B. Pokud chcete nabídnout samoobslužné registrace uživatele typu Host B2B, doporučujeme použít [správu nároků Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Ale pokud chcete vytvořit vlastní prostředí, můžete použít [vytvořit pozvání ROZHRANÍ API](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-1.0&tabs=http) automaticky odeslat vlastní pozvání e-mail přímo uživateli B2B, například. Nebo vaše aplikace můžete použít inviteRedeemUrl vrácené v odpovědi na vytvoření řemesla vlastní pozvání (prostřednictvím komunikačního mechanismu volby) k pozvaným uživatelům.

## <a name="next-steps"></a>Další kroky

* [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
* [Licencování spolupráce B2B ve službě Azure AD](licensing-guidance.md)
* [Nejčastější dotazy týkající se spolupráce Azure Active Directory s B2B](faq.md)
