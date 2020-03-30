---
title: MSAL pro iOS & macOS rozdíly | Azure
titleSuffix: Microsoft identity platform
description: Popisuje rozdíly v používání Microsoft Authentication Library (MSAL) mezi iOS a macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 62b79ee7398286b8e6c8ed8612bd001595e1f6ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084980"
---
# <a name="microsoft-authentication-library-for-ios-and-macos-differences"></a>Microsoft Authentication Library pro iOS a macOS – rozdíly

Tento článek vysvětluje rozdíly ve funkčnosti mezi Knihovnou ověřování Microsoft (MSAL) pro iOS a macOS.

> [!NOTE]
> Na Macu MSAL podporuje jenom aplikace pro macOS.

## <a name="general-differences"></a>Obecné rozdíly

MSAL pro macOS je podmnožinou funkcí dostupných pro iOS.

MSAL pro macOS nepodporuje:

- různé typy prohlížečů, například `ASWebAuthenticationSession`, `SFAuthenticationSession`. `SFSafariViewController`
- zprostředkované ověřování prostřednictvím aplikace Microsoft Authenticator není pro macOS podporováno.

Sdílení klíčenky mezi aplikacemi od stejného vydavatele je omezenější na macOS 10.14 a starších. Pomocí [seznamů řízení přístupu](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc) určete cesty k aplikacím, které by měly sdílet klíčenku. Uživateli se mohou zobrazit další výzvy řetězce klíčů.

V macOS 10.15+ je chování MSAL stejné mezi iOS a macOS. MSAL používá [přístupové skupiny klíčenky](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) pro sdílení řetězce klíčů. 

### <a name="conditional-access-authentication-differences"></a>Rozdíly ověřování podmíněného přístupu

Pro scénáře podmíněného přístupu bude méně uživatelských výzev při použití MSAL pro iOS. Důvodem je, že iOS používá broker aplikace (Microsoft Authenticator), který neguje potřebu vyzvat uživatele v některých případech.

### <a name="project-setup-differences"></a>Rozdíly v nastavení projektu

**Macos**

- Při nastavování projektu v systému macOS se ujistěte, že je vaše aplikace podepsána platným certifikátem pro vývoj nebo výrobu. MSAL stále pracuje v nepodepsaném režimu, ale bude se chovat jinak, pokud jde o trvalost mezipaměti. Aplikace by měla být spuštěna pouze nepodepsaná pro účely ladění. Pokud aplikaci distribuujete nepodepsanou, bude:
1. Na 10.14 a starší, MSAL vyzve uživatele k zadání hesla klíčenky při každém restartování aplikace.
2. Na 10.15+, MSAL vyzve uživatele k zadání přihlašovacích údajů pro každé získání tokenu. 

- Aplikace pro macOS nemusí implementovat volání AppDelegate.

**iOS**

- Existují další kroky k nastavení projektu pro podporu toku zprostředkovatele ověřování. Kroky jsou volány v kurzu.
- Projekty iOS musí registrovat vlastní schémata v info.plist. To není nutné v macOS.
