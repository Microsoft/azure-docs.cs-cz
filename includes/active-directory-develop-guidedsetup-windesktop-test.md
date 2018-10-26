---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: ac4f826ed1d27ee39d8e35605a3baa7f94b33e64
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "50035515"
---
## <a name="test-your-code"></a>Testování kódu

Chcete-li spustit váš projekt v sadě Visual Studio, vyberte **F5**. Vaše aplikace **hlavního okna MainWindow** se zobrazí, jak je znázorněno zde:

![Testování aplikace](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

Při prvním spuštění aplikace a vyberte **volat Microsoft Graph API** tlačítko, budete vyzváni k přihlášení. Použijte účet s Azure Active Directory (pracovní nebo školní účet) nebo účet Microsoft (live.com, outlook.com) a otestovat ho.

![Přihlášení k aplikaci](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Zadejte svůj souhlas pro přístup k aplikaci

Při prvním přihlášení do aplikace, budete také vyzváni k poskytování souhlasit s povolením aplikace pro přístup k profilu a přihlášení v, jak je znázorněno zde:

![Zadejte svůj souhlas pro přístup k aplikaci](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Zobrazení výsledků aplikace

Po přihlášení, měli byste vidět informace profilu uživatele, který je vrácen voláním rozhraní Microsoft Graph API. Výsledky se zobrazí v **výsledků volání rozhraní API** pole. Základní informace o tokenu, který byl získán prostřednictvím volání `AcquireTokenAsync` nebo `AcquireTokenSilentAsync` by se zobrazovat v **informace o tokenu** pole. Výsledky obsahují následující vlastnosti:

|Vlastnost  |Formát  |Popis |
|---------|---------|---------|
|**Název** |Celé jméno uživatele |Uživatel a příjmení název.|
|**Uživatelské jméno** |<span>user@domain.com</span> |Uživatelské jméno, který se používá k identifikaci uživatele.|
|**Platnost tokenu vyprší** |DateTime |Čas, kdy vyprší platnost tokenu. Knihovna MSAL je rozšířením datum vypršení platnosti obnovuje se token podle potřeby.|
|**Přístupový Token** |Řetězec |Řetězec tokenu, který je odeslán protokolu HTTP požadavků, které vyžadují *autorizační hlavičky*.|

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Další informace o oborech a delegovaná oprávnění

Vyžaduje rozhraní Microsoft Graph API *user.read* obory a čtení profilu uživatele. Tento obor se automaticky přidá ve výchozím nastavení každá aplikace, které je registrované v portálu pro registraci aplikace. Další rozhraní API pro Microsoft Graph, stejně jako vlastní rozhraní API pro back endového serveru může vyžadovat další obory. Vyžaduje rozhraní Microsoft Graph API *Calendars.Read* oboru seznam kalendářů uživatele.

Chcete-li přístup ke kalendářům uživatele v rámci aplikace, přidejte *Calendars.Read* delegovaná oprávnění aplikace informace o registraci. Pak přidejte *Calendars.Read* rozsah `acquireTokenSilent` volání.

>[!NOTE]
>Uživatel může zobrazit výzva pro další souhlasy zvýšit počet oborů.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
