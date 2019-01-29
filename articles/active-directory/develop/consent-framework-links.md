---
title: Jak funguje souhlasu s aplikací | Dokumentace Microsoftu
description: Další informace o fungování rozhraní pro udělování souhlasu Azure AD, pokud chcete zobrazit, jak ho můžete použít při vývoji aplikací v Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: da22c7a62d39b348e2114fd208d2aa8dd9711e0d
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55099222"
---
# <a name="how-application-consent-works"></a>Způsob fungování povolení spuštění aplikace

V tomto článku je pomoct vám další informace o fungování rozhraní pro udělování souhlasu Azure AD, takže můžete vyvíjet aplikace efektivněji.

## <a name="recommended-documents"></a>Doporučené dokumenty

- Získat obecné principy systému [jak umožňuje souhlas vlastníka prostředku k řízení přístupu aplikace k prostředkům](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#consent).
- Získejte podrobný přehled o [způsob implementace rozhraní Azure AD pro udělování souhlasu souhlasu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#overview-of-the-consent-framework).
- Větší hloubky, přečtěte si [použití rozhraní pro udělování souhlasu víceklientské aplikaci](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) k implementaci "user" a "admin" souhlas, podpora více pokročilé modely vícevrstvých aplikací.
- Větší hloubky, přečtěte si [jak souhlasu se podporuje ve vrstvě protokolu OAuth 2.0 během toku přidělení kódu autorizace.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)

## <a name="next-steps"></a>Další postup
[StackOverflow pro Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
