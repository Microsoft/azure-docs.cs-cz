---
title: Chráněné webové rozhraní API – registrace aplikace | Azure
description: Zjistěte, jak vytvářet chráněné webové rozhraní API a informace, které potřebujete k registraci aplikace.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1d8c8fcf84cd008957fcdb7cd14c4a07d9f3643
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074902"
---
# <a name="protected-web-api---app-registration"></a>Chráněné webové rozhraní API – registrace aplikace

Tento článek vysvětluje podrobnosti registrace aplikace pro chráněné webové rozhraní API.

Zobrazit [rychlý start: Registrace aplikace s platformou identity Microsoft](quickstart-register-app.md) běžné pokyny o tom, jak zaregistrovat aplikaci.

## <a name="accepted-token-version"></a>Přijetí tokenu verze

Microsoft identity platform endpoint může vydat dva typy tokenů: tokeny v1.0 a v2.0 tokeny. Další informace o těchto tokenů v [přístupové tokeny](access-tokens.md). Přijatý token verze závisí **podporovaných typů účtu** jste zvolili při vytváření vaší aplikace:

- Pokud hodnota **podporovaných typů účtu** je **účty v jakékoli organizaci adresáři a osobní účty Microsoft (třeba Skype, Xbox, Outlook.com)**, přijatý token verze bude verze 2.0.
- Přijatý token verze v opačném případě bude verze 2.0.

Po vytvoření aplikace, přijatý token verzi můžete změnit pomocí následujících kroků:

1. Na webu Azure Portal, vyberte svou aplikaci a pak vyberte **Manifest** pro vaši aplikaci.
2. V manifestu, vyhledejte **"accessTokenAcceptedVersion"** a, že její hodnota je **2**. Tato vlastnost umožňuje vědět, že webové rozhraní API přijímá v2.0 tokeny služby Azure AD. Pokud je **null**, přijatý token verze bude v1.0.
3. Vyberte **Uložit**.

> [!NOTE]
> Záleží jen na webové rozhraní API se rozhodnout, která token verze (verze 1.0 nebo 2.0) přijímá. Když klient vyžádá token pro vaše webového rozhraní API s využitím koncového bodu v2.0 platforma identit Microsoft, získají první token, který označuje, která verze je přijat ve webovém rozhraní API.

## <a name="no-redirect-uri"></a>Žádný identifikátor URI pro přesměrování

Webová rozhraní API nebudou muset registrovat na identifikátor URI přesměrování je žádný uživatel přihlášený interaktivní.

## <a name="expose-an-api"></a>Zpřístupnit rozhraní API

Další nastavení specifická pro webová rozhraní API je zveřejněné rozhraní API a vystavené obory.

### <a name="resource-uri-and-scopes"></a>Identifikátor URI prostředku a obory

Obory jsou obvykle ve formátu `resourceURI/scopeName`. Pro Microsoft Graph obory má zkratky jako `User.Read`, ale tento řetězec je zkratka pro `https://graph.microsoft.com/user.read`.

Při registraci aplikace budete muset definovat následující parametry:

- Jeden identifikátor URI – ve výchozím nastavení portál pro registraci aplikací doporučuje, abyste použili `api://{clientId}`. Tento identifikátor URI prostředku je jedinečný, ale není lidské čitelné. Můžete ho změnit, ale ujistěte se, že se jedná o jedinečný.
- Jeden nebo několik oborů

Obory jsou také zobrazeny v obrazovkami pro vyjádření souhlasu, který se zobrazí koncovým uživatelům, kteří používají vaše aplikace. Proto budete muset poskytnout odpovídající řetězce, které popisují oboru:

- Jak je vidět koncovým uživatelem
- Jak je vidět správcem tenanta, který vám může udělit souhlas správce

### <a name="how-to-expose-the-api"></a>Jak publikovat rozhraní API

1. Vyberte **vystavit rozhraní API** kapitoly registrace aplikace a:
   1. Vyberte **Přidat obor**.
   1. Přijměte navrhovanou identifikátor URI ID aplikace (api :// {clientId}) tak, že vyberete **uložit a pokračovat**.
   1. Zadejte následující parametry:
      - Pro **název oboru**, použijte `access_as_user`.
      - Pro **kdo může odsouhlasit**, ujistěte se, že **správci a uživatelé** je vybraná možnost.
      - V **zobrazovaný název souhlasu správce**, typ `Access TodoListService as a user`.
      - V **popis souhlasu správce**, typ `Accesses the TodoListService Web API as a user`.
      - V **zobrazovaný název souhlasu uživatele**, typ `Access TodoListService as a user`.
      - V **popis souhlasu uživatele**, typ `Accesses the TodoListService Web API as a user`.
      - Zachovat **stavu** nastavena na **povoleno**.
      - Vyberte **přidat obor**.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Konfigurace kódu aplikace](scenario-protected-web-api-app-configuration.md)
