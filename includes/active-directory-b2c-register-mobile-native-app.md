---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory-b2c
author: davidmu1
ms.service: active-directory-b2c
ms.topic: include
ms.date: 04/09/2018
ms.author: davidmu
ms.custom: include file
ms.openlocfilehash: 8363d023e89c77aabc0d123f19264c9a0758a656
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38740459"
---
[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Pokud chcete zaregistrovat mobilní nebo nativní aplikaci, použijte nastavení uvedená v tabulce.

![Příklad nastavení registrace nové mobilní nebo nativní aplikace](./media/active-directory-b2c-register-mobile-native-app/b2c-new-mobile-native-app-settings.png)

| Nastavení      | Ukázková hodnota  | Popis                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Název** | Aplikace Contoso B2C | Zadejte **Název** aplikace, který popíše aplikaci uživatelům. |
| **Nativní klient** | Ano | Pro mobilní nebo nativní aplikaci vyberte **Ano**. |
| **Vlastní identifikátor URI přesměrování** | `com.onmicrosoft.contoso.appname://redirect/path` | Zadejte identifikátor URI přesměrování s vlastním schématem. Nezapomeňte zvolit [dobrý identifikátor URI přesměrování](../articles/active-directory-b2c/active-directory-b2c-app-registration.md) a nepoužívat speciální znaky jako podtržítka. |

Pro registraci aplikace klikněte na **Vytvořit**.

Nově zaregistrovaná aplikace se zobrazí v seznamu aplikací pro tenanta B2C. Vyberte ze seznamu vaši mobilní nebo nativní aplikaci. Zobrazí se podokno vlastností aplikace.

![Vlastnosti aplikace](./media/active-directory-b2c-register-mobile-native-app/b2c-mobile-native-app-properties.png)

Poznamenejte si globálně jedinečné **ID klienta aplikace**. Toto ID použijete v kódu vaší aplikace.

Pokud vaše nativní aplikace volá webové rozhraní API zabezpečené pomocí Azure AD B2C, proveďte tyto kroky:
   1. Vytvořte Tajný klíč aplikace – přejděte do okna **Klíče** a klikněte na tlačítko **Vygenerovat klíč**. Poznamenejte si hodnotu **Klíč aplikace**. Tuto hodnotu použijete jako tajný klíč aplikace v kódu vaší aplikace.
   2. Klikněte na **Přístup přes rozhraní API**, pak na **Přidat** a vyberte vaše webové rozhraní API a obory (oprávnění).

> [!NOTE]
> **Tajný klíč aplikace** je důležitý údaj zabezpečení a musí být řádně zabezpečen.
> 
