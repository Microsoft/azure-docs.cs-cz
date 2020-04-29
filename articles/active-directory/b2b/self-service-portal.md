---
title: Samoobslužná registrace portálu pro spolupráci B2B – Azure AD
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77195789"
---
# <a name="self-service-for-azure-ad-b2b-collaboration-sign-up"></a>Samoobslužná registrace pro spolupráci služby Azure AD B2B

Zákazníci mají spoustu možností, jak využít integrované funkce, ke kterým mají přístup prostřednictvím webu [Azure Portal](https://portal.azure.com) a [přístupového panelu aplikací](https://myapps.microsoft.com) pro koncové uživatele. Pracovní postup onboardingu můžete ale potřebovat přizpůsobit pro uživatele B2B, aby vyhovoval potřebám vaší organizace.

## <a name="azure-ad-entitlement-management-for-b2b-guest-user-sign-up"></a>Správa opravňujících k Azure AD pro registraci uživatele hosta B2B

Jako pozvání k organizaci nesmíte znát čas, kdy jednotliví externí spolupracovníci potřebují mít přístup k vašim prostředkům. Potřebujete způsob, jak se uživatelům z partnerských společností zaregistrovat sami se zásadami, které ovládáte. Pokud chcete povolit uživatelům z jiných organizací, aby si vyžádali přístup, a po schválení se zřídí účty hostů a přiřadí se ke skupinám, aplikacím a webům SharePointu Online, můžete pomocí [správy nároků Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) nakonfigurovat zásady, které [spravují přístup pro externí uživatele](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users#how-access-works-for-external-users).

## <a name="azure-active-directory-b2b-invitation-api"></a>Rozhraní API pro pozvání Azure Active Directory B2B

Organizace můžou používat [rozhraní API pro Microsoft Graph pro správce pozvání](https://docs.microsoft.com/graph/api/resources/invitation?view=graph-rest-1.0) k sestavování vlastního prostředí pro uživatele typu Host B2B. Pokud chcete nabízet samoobslužné registrace uživatelů pro službu B2B, doporučujeme použít [správu nároků Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Pokud ale chcete vytvořit vlastní prostředí, můžete použít [rozhraní API pro vytvoření pozvánky](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-1.0&tabs=http) k automatickému odeslání přizpůsobeného e-mailu pozvánky přímo uživateli B2B, například. Nebo vaše aplikace může použít inviteRedeemUrl vrácenou v reakci na vytvoření k vytvoření vlastní pozvánky (prostřednictvím vašeho komunikačního mechanismu výběru) pozvánému uživateli.

## <a name="next-steps"></a>Další kroky

* [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
* [Licencování spolupráce B2B ve službě Azure AD](licensing-guidance.md)
* [Nejčastější dotazy týkající se spolupráce Azure Active Directory s B2B](faq.md)
