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
ms.openlocfilehash: 2325509f68ced7c66d9f733b07247ae01301b565
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82181527"
---
## <a name="test-your-code"></a>Testování kódu

Chcete-li spustit projekt, v aplikaci Visual Studio vyberte **F5**. Zobrazí se **MainWindow** vaší aplikace, jak je znázorněno zde:

![Testování aplikace](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

Při prvním spuštění aplikace a výběru tlačítka **volat Microsoft Graph rozhraní API** se zobrazí výzva, abyste se přihlásili. K otestování použijte účet Azure Active Directory (pracovní nebo školní účet) nebo účet Microsoft (live.com, outlook.com).

![Přihlášení k aplikaci](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Poskytnutí souhlasu pro přístup k aplikaci

Při prvním přihlášení do aplikace se zobrazí také výzva k poskytnutí souhlasu, aby aplikace mohla přistupovat k vašemu profilu a přihlásili se, jak je znázorněno zde:

![Zadejte svůj souhlas s přístupem k aplikacím.](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Zobrazit výsledky aplikace

Po přihlášení byste měli vidět informace o profilu uživatele, které jsou vráceny voláním rozhraní Microsoft Graph API. Výsledky se zobrazí v poli **výsledky volání rozhraní API** . Základní informace o tokenu, který byl získán prostřednictvím volání `AcquireTokenInteractive` nebo, `AcquireTokenSilent` by měly být viditelné v poli **informace o tokenu** . Výsledky obsahují následující vlastnosti:

|Vlastnost  |Formát  |Popis |
|---------|---------|---------|
|**Uživatelské jméno** |<span>user@domain.com</span> |Uživatelské jméno, které se používá k identifikaci uživatele.|
|**Vypršení platnosti tokenu** |DateTime |Čas vypršení platnosti tokenu MSAL rozšiřuje datum vypršení platnosti tím, že podle potřeby obnoví token.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Další informace o oborech a delegovaných oprávněních

Rozhraní Microsoft Graph API vyžaduje, aby *uživatel. přečetl* obor pro čtení profilu uživatele. Tento obor se ve výchozím nastavení automaticky přidá v každé aplikaci, která je registrovaná na portálu pro registraci aplikací. Jiná rozhraní API pro Microsoft Graph a také vlastní rozhraní API pro back-end Server můžou vyžadovat další obory. Rozhraní Microsoft Graph API vyžaduje *kalendáře. Přečtěte* si obor pro výpis kalendářů uživatele.

Chcete-li získat přístup k kalendářům uživatele v kontextu aplikace, přidejte *kalendáře. Přečtěte si* delegované oprávnění k informacím o registraci aplikace. Pak přidejte *kalendáře. Přečtěte* si rozsah `acquireTokenSilent` volání.

>[!NOTE]
>Uživatel může být vyzván k dalšímu souhlasu při zvýšení počtu oborů.

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
