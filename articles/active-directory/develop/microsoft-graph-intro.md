---
title: Microsoft Graph API
description: Rozhraní Microsoft Graph API je webové rozhraní API RESTful, které umožňuje přístup k prostředkům služby Microsoft Cloud.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: 2e689e620a5aeb7c5028f1a1b30dd6def8e447ab
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529991"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph API

Rozhraní Microsoft Graph API je webové rozhraní API RESTful, které umožňuje přístup k prostředkům služby Microsoft Cloud. Po registraci aplikace a získání ověřovacích tokenů pro uživatele nebo službu můžete vytvořit požadavky na rozhraní Microsoft Graph API. Další informace najdete v tématu [přehled Microsoft Graph](/graph/overview).

Microsoft Graph zpřístupňuje rozhraní REST API a klientské knihovny pro přístup k datům v následujících Microsoft 365 službách:
- Microsoft 365 služby: Delvu, Excel, Microsoft, Microsoft teams, OneDrive, OneNote, Outlook, Exchange, Planner a SharePoint
- Enterprise mobility and Security Services: Advanced Threat Analytics, Rozšířená ochrana před internetovými útoky, Azure Active Directory, Identity Manager a Intune
- Služby Windows 10: aktivity, zařízení, oznámení
- Dynamics 365 Business Central

## <a name="versions"></a>Verze

Microsoft Graph aktuálně podporuje dvě verze: v 1.0 a beta. Verze v 1.0 zahrnuje všeobecně dostupná rozhraní API. Pro všechny produkční aplikace použijte verzi v 1.0. Beta verze obsahuje rozhraní API, která jsou momentálně ve verzi Preview. Vzhledem k tomu, že můžeme zavést zásadní změny v našich rozhraních API beta verze, doporučujeme, abyste používali beta verzi pouze pro testovací aplikace, které jsou ve vývoji. Nepoužívejte beta rozhraní API v produkčních aplikacích. Další informace najdete v tématu [Správa verzí, podpora a přerušující zásady změny pro Microsoft Graph](/graph/versioning-and-support).

Pokud chcete začít používat beta rozhraní API, přečtěte si téma [referenční informace o Microsoft Graph beta verzi](/graph/api/overview?view=graph-rest-beta&preserve-view=true)

Pokud chcete začít používat rozhraní API v 1.0, přečtěte si referenční informace o [Microsoft Graph REST API v 1.0](/graph/api/overview?view=graph-rest-1.0&preserve-view=true) .

## <a name="get-started"></a>Začínáme

Chcete-li číst nebo zapisovat do prostředku, jako je například uživatel nebo e-mailová zpráva, vytvoříte požadavek, který bude vypadat nějak takto:

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

Další informace o prvcích vytvořené žádosti najdete v tématu [použití rozhraní Microsoft Graph API](/graph/use-the-api) .

Ukázky pro rychlý Start jsou k dispozici, abyste viděli, jak získat přístup k výkonu rozhraní Microsoft Graph API. Příklady, které jsou k dispozici, mají přístup ke dvěma službám s jedním ověřováním: účet Microsoft a Outlook. Každý rychlý Start má přístup k informacím z profilů účet Microsoft uživatelů a zobrazuje události ze svého kalendáře.
Rychlé starty obsahují čtyři kroky:
- Výběr platformy
- Získat ID aplikace (ID klienta)
- Sestavení ukázky
- Přihlaste se a Prohlédněte si události v kalendáři.

Po dokončení rychlého startu máte aplikaci, která je připravená ke spuštění. Další informace najdete v [nejčastějších dotazech k rychlému startu Microsoft Graph](/graph/quick-start-faq). Pokud chcete začít s ukázkami, přečtěte si téma [rychlý start Microsoft Graph](https://developer.microsoft.com/graph/quick-start).

## <a name="tools"></a>nástroje

Microsoft Graph Explorer je webový nástroj, který můžete použít k sestavování a testování požadavků pomocí rozhraní Microsoft Graph API. K aplikaci Microsoft Graph Explorer máte přístup v umístění: `https://developer.microsoft.com/graph/graph-explorer` .

Metoda post je nástroj, který můžete použít také k sestavení a testování požadavků pomocí Microsoft Graph rozhraní API. Můžete si stáhnout tento příspěvek v: `https://www.getpostman.com/` . Pokud chcete pracovat s Microsoft Graph v nástroji post, použijete Microsoft Graph kolekci v poli post. Další informace najdete v tématu [použití metody post s rozhraním Microsoft Graph API](/graph/use-postman).
