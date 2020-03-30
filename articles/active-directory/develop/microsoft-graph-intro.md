---
title: Rozhraní Microsoft Graph API | Dokumenty společnosti Microsoft
description: Rozhraní Microsoft Graph API je webové rozhraní API RESTful, které umožňuje přístup k prostředkům služby Microsoft Cloud.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: 6c1b4390282f6d54178365714b1e2e665b4cf061
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136496"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph API

Rozhraní Microsoft Graph API je webové rozhraní API RESTful, které umožňuje přístup k prostředkům služby Microsoft Cloud. Po registraci aplikace a získání ověřovacích tokenů pro uživatele nebo službu můžete požádat o rozhraní Microsoft Graph API. Další informace naleznete v [tématu Přehled aplikace Microsoft Graph](https://docs.microsoft.com/graph/overview).

Microsoft Graph zpřístupňuje rozhraní REST API a klientské knihovny pro přístup k datům v následujících službách Microsoft 365:
- Služby Office 365: Delve, Excel, Microsoft Bookings, Microsoft Teams, OneDrive, OneNote, Outlook/Exchange, Planner a SharePoint
- Podnikové služby mobility a zabezpečení: Pokročilá analýza hrozeb, pokročilá ochrana před hrozbami, Azure Active Directory, Správce identit a Intune
- Služby Windows 10: aktivity, zařízení, oznámení
- Dynamics 365 Business Central

## <a name="versions"></a>Verze

Microsoft Graph v současné době podporuje dvě verze: v1.0 a beta. Verze v1.0 obsahuje obecně dostupná rozhraní API. Použijte verzi v1.0 pro všechny produkční aplikace. Beta verze obsahuje api, která jsou aktuálně ve verzi Preview. Vzhledem k tomu, že můžeme zavést nejnovější změny našich beta api, doporučujeme použít beta verzi pouze k testování aplikací, které jsou ve vývoji; nepoužívejte beta API ve vašich produkčních aplikacích. Další informace naleznete v [tématu Správa verzí, podpora a zásady změn pro microsoft graph](https://docs.microsoft.com/graph/versioning-and-support).

Informace o tom, jak začít používat beta rozhraní API, najdete v [tématu Odkaz na koncový bod beta aplikace Microsoft Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-beta)

Informace o tom, jak začít používat rozhraní API v1.0, naleznete [v tématu Microsoft Graph REST API v1.0 reference](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)

## <a name="get-started"></a>Začínáme

Chcete-li číst nebo zapisovat do prostředku, jako je například uživatel nebo e-mailová zpráva, vytvořte požadavek, který vypadá takto:

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

Další informace o prvcích vytvořeného požadavku najdete v [tématu Použití rozhraní Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api)

K dispozici jsou ukázky rychlého startu, které vám ukáží, jak získat přístup k výkonu rozhraní Microsoft Graph API. Ukázky, které jsou k dispozici přístup dvě služby s jedním ověřováním: účet Microsoft a Outlook. Každý rychlý start přistupuje k informacím z profilů uživatelů účtu Microsoft a zobrazuje události z jejich kalendáře.
Rychlé starty zahrnují čtyři kroky:
- Vyberte si platformu
- Získání ID aplikace (ID klienta)
- Sestavení ukázky
- Přihlášení a zobrazení událostí v kalendáři

Po dokončení rychlého startu máte aplikaci, která je připravená ke spuštění. Další informace naleznete v [nejčastějších dotazech k rychlému spuštění aplikace Microsoft Graph](https://docs.microsoft.com/graph/quick-start-faq). Chcete-li začít s ukázkami, naleznete [v tématu Microsoft Graph QuickStart](https://developer.microsoft.com/graph/quick-start).

## <a name="tools"></a>Nástroje

Microsoft Graph Explorer je webový nástroj, který můžete použít k vytváření a testování požadavků pomocí rozhraní API aplikace Microsoft Graph. K aplikaci Microsoft Graph `https://developer.microsoft.com/graph/graph-explorer`Explorer můžete přistupovat na adrese: .

Pošťák je nástroj, který můžete také použít k vytváření a testování požadavků pomocí rozhraní API aplikace Microsoft Graph. Zde si můžete stáhnout `https://www.getpostman.com/`Pošťák na adrese: . Chcete-li komunikovat s Microsoft Graph v Postman, použijte kolekci Microsoft Graph v Postman. Další informace naleznete [v tématu Použití postman s rozhraním Microsoft Graph API](/graph/use-postman?context=graph%2Fapi%2Fbeta&view=graph-rest-beta).
