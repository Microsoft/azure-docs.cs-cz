---
title: Démon procesu aplikace volání webových rozhraní API (registrace aplikace) – platforma identit Microsoft
description: Zjistěte, jak sestavit aplikaci démona, která volá webové rozhraní API – registrace aplikace
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79a355ab226e56a3dde1df5369deda5142d47848
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076237"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Démon procesu aplikace, že volání webového rozhraní API – registrace aplikace

Démon procesu aplikace zde je, co potřebujete vědět při registraci aplikace.

## <a name="supported-account-types"></a>Podporované typy účtu

Vzhledem k tomu, že démon aplikace smysl jenom v tenantovi Azure AD, když vytvoříte aplikaci budete muset zvolit:

- buď **účty v tomto adresáři organizace jenom**. Tato volba je nejběžnější případ, jako jsou obvykle napsány aplikace démon vývojáři – obchodní (LOB).
- nebo **účty v libovolném adresáři organizace**. Tato volba provede Pokud jste nezávislý dodavatel softwaru poskytuje nástroj nástroje pro vaše zákazníky. Budete potřebovat správci tenantů zákazníka ke schválení.

## <a name="authentication---no-reply-uri-needed"></a>Ověřování - žádný identifikátor URI odpovědi, který je potřeba

V případě, kdy důvěrné klientské aplikace používá **pouze** tok přihlašovacích údajů klienta, adresa URL odpovědi nemusí být registrován. Není nutná buď pro konfiguraci aplikace/konstrukce. Tok přihlašovacích údajů klienta nepoužívá se.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>Oprávnění k rozhraní API – oprávnění a vyjádření souhlasu správce

Démon procesu aplikace může požadovat pouze oprávnění aplikací k rozhraním API (ne delegovaná oprávnění). V **oprávnění rozhraní API** stránka pro registraci aplikace, po dokončení výběru **přidat oprávnění** a vybrali řady rozhraní API, zvolte **oprávnění aplikace**, a pak vyberte oprávnění

![Oprávnění a vyjádření souhlasu správce](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

Démon procesu aplikace vyžadují, jste správcem tenanta předem souhlas s aplikací volání webového rozhraní API. Svůj souhlas se poskytuje v rámci stejného **oprávnění rozhraní API** stránky výběrem správce tenanta **udělit souhlas správce *naší organizace***

Pokud jste nezávislý dodavatel softwaru vytvářet aplikace s více tenanty, chcete zkontrolovat [nasazení – případ aplikace démonů s více tenanty](scenario-daemon-production.md#deployment---case-of-multi-tenant-daemon-apps) odstavce.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Démon procesu aplikace – konfigurace kódu aplikace](./scenario-daemon-app-configuration.md)
