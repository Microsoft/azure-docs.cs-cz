---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: f121be4ec8c3d3ab618e2955d9dbd8ab5eea461d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "79128596"
---
## <a name="test-your-code"></a>Testování kódu

Chcete-li spustit projekt, vyberte v aplikaci Visual Studio **možnost F5**. Vaše aplikace **MainWindow** se zobrazí, jak je znázorněno zde:

![Testování aplikace](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

Při prvním spuštění aplikace a výběru tlačítka **Volání rozhraní Microsoft Graph API** budete vyzváni k přihlášení. Otestujte účet Azure Active Directory (pracovní nebo školní účet) nebo účet Microsoft (live.com, outlook.com).

![Přihlášení k aplikaci](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Poskytnout souhlas s přístupem k aplikaci

Při prvním přihlášení k aplikaci budete také vyzváni k udělení souhlasu s tím, aby aplikace mohla mít přístup k vašemu profilu a přihlásila se k vám, jak je znázorněno zde:

![Zadejte svůj souhlas s přístupem k aplikaci](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Zobrazit výsledky aplikace

Po přihlášení byste měli vidět informace o profilu uživatele, které se vrátily voláním rozhraní Microsoft Graph API. Výsledky jsou zobrazeny v poli **Výsledky volání rozhraní API.** Základní informace o tokenu, který `AcquireTokenInteractive` byl `AcquireTokenSilent` získán prostřednictvím volání nebo by měly být viditelné v poli **Informace o tokenu.** Výsledky obsahují následující vlastnosti:

|Vlastnost  |Formát  |Popis |
|---------|---------|---------|
|**Username** |<span>user@domain.com</span> |Uživatelské jméno, které se používá k identifikaci uživatele.|
|**Platnost tokenu vyprší** |DateTime |Čas, kdy vyprší platnost tokenu. MSAL prodlouží datum vypršení platnosti obnovením tokenu podle potřeby.|


<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Další informace o oborech a delegovaných oprávněních

Rozhraní Microsoft Graph API vyžaduje, aby obor *user.read* četl profil uživatele. Tento obor se automaticky přidá ve výchozím nastavení v každé aplikaci, která je registrována na portálu registrace aplikací. Další rozhraní API pro Microsoft Graph, stejně jako vlastní rozhraní API pro back-end server, může vyžadovat další obory. Rozhraní Microsoft Graph API vyžaduje *Calendars.Read* obor seznam kalendářů uživatele.

Chcete-li získat přístup ke kalendářům uživatele v kontextu aplikace, přidejte k informacím o registraci aplikace delegované oprávnění *Calendars.Read.* Potom přidejte *Calendars.Read* obor `acquireTokenSilent` volání.

>[!NOTE]
>Při zvýšení počtu oborů může být uživatel vyzván k zadání dalších souhlasů.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
