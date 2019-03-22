---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: 33e2ac136ae68ee0c0ce0109a6f6934727d3a6c5
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203680"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Přihlašování uživatelů a volání Microsoft Graphu z aplikace pro Android

V tomto kurzu se dozvíte, jak vytvářet aplikace pro Android a integrace do Microsoft identity platform. Konkrétně tato aplikace se přihlásit uživatele získání přístupového tokenu pro volání rozhraní Microsoft Graph API a vytvořte žádost na základní rozhraní Microsoft Graph API.  

Po dokončení průvodce bude vaše aplikace akceptovat přihlášení osobní účty Microsoft (včetně outlook.com, live.com a další) a pracovní nebo školní účty z jakéhokoli společnosti nebo organizace, která používá Azure Active Directory.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak funguje ukázkové aplikace vygenerované v této příručce

![Ukazuje, jak ukázková aplikace vygenerované tohle funguje kurzy](media/active-directory-develop-guidedsetup-android-intro/android-intro-updated.png)

Aplikace v této ukázce se přihlásit uživatele a jejich jménem získat data.  Tato data budou mít přístup přes vzdálenou API (Microsoft Graph API v tomto případě), který vyžaduje ověření a také chráněn platforma identit Microsoft.

A konkrétně:

* Vaše aplikace se spustí webovou stránku pro přihlášení uživatele.
* Vaše aplikace budou vydány lístky přístupového tokenu pro rozhraní Microsoft Graph API.
* Požadavek HTTP do webového rozhraní API zahrne přístupový token.
* Zpracování odpovědi Microsoft Graphu.

Tato ukázka používá Microsoft Authentication library pro Android (MSAL) pro koordinaci a pomáhá s ověřeného Knihovna MSAL automaticky obnovit tokeny, poskytovat jednotné přihlašování mezi jinými aplikacemi na zařízení, pomáhá spravovat účty a zpracování většinou podmíněného přístupu.

## <a name="prerequisites"></a>Požadavky

* Tento instalační program s asistencí používá Android Studio 3.0.
* Vyžaduje se Android 21 nebo novější (25 + je doporučeno).
* Google Chrome nebo webový prohlížeč, který používá vlastní karty je požadovaný pro tuto verzi MSAL pro Android.

## <a name="library"></a>Knihovna

Tato příručka používá následující knihovny pro ověřování:

|Knihovna|Popis|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Knihovna Microsoft Authentication Library (MSAL)|
