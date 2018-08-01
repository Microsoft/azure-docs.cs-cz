---
title: Jak funguje souhlasu s aplikací | Dokumentace Microsoftu
description: Další informace o fungování rozhraní pro udělování souhlasu Azure AD, pokud chcete zobrazit, jak ho můžete použít při vývoji aplikací v Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 91378f4816db773aebd038bd9f176596a4f2c316
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366923"
---
# <a name="how-application-consent-works"></a>Způsob fungování povolení spuštění aplikace

V tomto článku je pomoct vám další informace o fungování rozhraní pro udělování souhlasu Azure AD, takže můžete vyvíjet aplikace efektivněji.

## <a name="recommended-documents"></a>Doporučené dokumenty

- Získat obecné principy systému [jak umožňuje souhlas vlastníka prostředku k řízení přístupu aplikace k prostředkům](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#consent).
- Získejte podrobný přehled o [způsob implementace rozhraní Azure AD pro udělování souhlasu souhlasu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#overview-of-the-consent-framework).
- Větší hloubky, přečtěte si [použití rozhraní pro udělování souhlasu víceklientské aplikaci](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) k implementaci "user" a "admin" souhlas, podpora více pokročilé modely vícevrstvých aplikací.
- Větší hloubky, přečtěte si [jak souhlasu se podporuje ve vrstvě protokolu OAuth 2.0 během toku přidělení kódu autorizace.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)

## <a name="next-steps"></a>Další postup
[StackOverflow pro Azure AD](http://stackoverflow.com/questions/tagged/azure-active-directory)
