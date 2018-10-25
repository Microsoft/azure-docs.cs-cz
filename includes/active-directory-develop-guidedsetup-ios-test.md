---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: d8d7d5649ca1dc215f85f928d111ff1367c60bb5
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988243"
---
## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>Testování dotazů na rozhraní Microsoft Graph API z aplikace pro iOS

Chcete-li spustit kód v simulátoru, stiskněte **příkaz** + **R**.

![Otestujte aplikaci v simulátoru](media/active-directory-develop-guidedsetup-ios-test/iostestscreenshot.png)

Jakmile budete připraveni k testování, vyberte **volat Microsoft Graph API**. Po zobrazení výzvy zadejte svoje uživatelské jméno a heslo.

### <a name="provide-consent-for-application-access"></a>Zadejte svůj souhlas pro přístup k aplikaci

Při prvním přihlášení do aplikace, budete vyzváni k souhlasíte s tím, aby aplikace k profilu a pro přihlášení:

![Zadejte svůj souhlas pro přístup k aplikaci](media/active-directory-develop-guidedsetup-ios-test/iosconsentscreen.png)

### <a name="view-application-results"></a>Zobrazení výsledků aplikace
Po přihlášení, byste měli vidět údajům vašeho uživatelského profilu vrácený voláním rozhraní Microsoft Graph API v **protokolování** oddílu. 

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Další informace o oborech a delegovaná oprávnění

Vyžaduje rozhraní Microsoft Graph API **user.read** obory a čtení profilu uživatele. Tento obor se automaticky přidá ve výchozím nastavení v každé aplikaci, která je zaregistrovaná na portálu pro registraci. Další rozhraní API pro Microsoft Graph, stejně jako vlastní rozhraní API pro back endového serveru může vyžadovat další obory. Vyžaduje rozhraní Microsoft Graph API **Calendars.Read** oboru seznam kalendářů uživatele.

Chcete-li přístup ke kalendářům uživatele v rámci aplikace, přidejte **Calendars.Read** delegovaná oprávnění aplikace informace o registraci. Pak přidejte **Calendars.Read** rozsah **acquireTokenSilent** volání.

>[!NOTE]
>Uživatel může zobrazit výzva pro další souhlasy zvýšit počet oborů.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
