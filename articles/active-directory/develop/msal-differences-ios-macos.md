---
title: MSAL pro iOS & macOS rozdíly | Azure
titleSuffix: Microsoft identity platform
description: Popisuje rozdíly v používání knihovny Microsoft Authentication Library (MSAL) mezi iOS a macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 41389bc5ed8580cd80dbc40e771c7f15241f5ae7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85479398"
---
# <a name="microsoft-authentication-library-for-ios-and-macos-differences"></a>Microsoft Authentication Library pro iOS a macOS – rozdíly

Tento článek vysvětluje rozdíly mezi funkcemi v knihovně Microsoft Authentication Library (MSAL) pro iOS a macOS.

> [!NOTE]
> Na Macu MSAL podporuje jenom aplikace macOS.

## <a name="general-differences"></a>Obecné rozdíly

MSAL for macOS je podmnožinou funkcí dostupných pro iOS.

MSAL for macOS nepodporuje:

- různé typy prohlížečů, například `ASWebAuthenticationSession` , `SFAuthenticationSession` , `SFSafariViewController` .
- zprostředkované ověřování pomocí Microsoft Authenticator aplikace není pro macOS podporované.

Sdílení řetězce klíčů mezi aplikacemi od stejného vydavatele je více omezené na macOS 10,14 a starších verzích. Pomocí [seznamů řízení přístupu](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc) určete cesty k aplikacím, které by měly sdílet řetězce klíčů. Uživatel může zobrazit další výzvy pro řetězce klíčů.

V macOS 10.15 + je chování MSAL stejné mezi iOS a macOS. MSAL používá [přístupové skupiny pro řetězce](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) klíčů pro sdílení řetězce klíčů. 

### <a name="conditional-access-authentication-differences"></a>Rozdíly v ověřování podmíněného přístupu

V případě scénářů podmíněného přístupu bude při použití MSAL pro iOS k dispozici méně výzev uživatele. Je to proto, že iOS používá aplikaci zprostředkovatele (Microsoft Authenticator), která v některých případech odhlásí nutnost vyzvat uživatele.

### <a name="project-setup-differences"></a>Rozdíly v nastavení projektu

**macOS**

- Při nastavování projektu na macOS se ujistěte, že je aplikace podepsaná pomocí platného vývojového nebo produkčního certifikátu. MSAL pořád funguje v režimu bez znaménka, ale bude se chovat jinak, s ohledem na trvalost mezipaměti. Aplikace by měla být pro účely ladění pouze nepodepsaná. Pokud distribuujete aplikaci bez znaménka, bude:
1. V 10,14 a starších verzích MSAL vyzve uživatele k zadání hesla řetězce klíčů pokaždé, když aplikaci znovu spustí.
2. V 10.15 + se MSAL vyzve uživatele k zadání přihlašovacích údajů pro každé získání tokenu. 

- macOS aplikace nepotřebují implementovat volání AppDelegate.

**iOS**

- Existují další kroky pro nastavení projektu pro podporu toku zprostředkovatele ověřování. Tyto kroky jsou vyvolány v tomto kurzu.
- projekty iOS potřebují zaregistrovat vlastní schémata v souboru info. plist. To není vyžadováno v macOS.
