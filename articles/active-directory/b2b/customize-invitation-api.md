---
title: Azure Active Directory s B2B spolupráce rozhraní API a přizpůsobení | Dokumentace Microsoftu
description: Spolupráce B2B ve službě Azure Active Directory podporuje vaše vztahy s ostatními společnostmi tím, že vašim obchodním partnerům umožní selektivní přístup ke podnikovým aplikacím
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/11/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: a41eadaecc203f9371da3eee05367a4f77747253
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35642758"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Azure Active Directory s B2B spolupráce rozhraní API a přizpůsobení

Měli jsme mnoho zákazníků, řekněte nám, že chcete přizpůsobit proces pozvánky způsobem, který je nejvhodnější pro jejich organizace. Pomocí našich rozhraní API můžete provést přesně to. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>Možnosti e-mailové pozvánce rozhraní API
Rozhraní API nabízí následující funkce:

1. Pozvání externího uživatele s *jakékoli* e-mailovou adresu.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Přizpůsobení, ve kterém se mají vaši uživatelé ŘIDICÍ po přijetí svou pozvánku.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Vyberte možnost odesílat e-mailu standardní pozvání prostřednictvím USA

    ```
    "sendInvitationMessage": true
    ```

  a zobrazí se zpráva, kterou můžete přizpůsobit příjemce

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. A pokud se rozhodnete cc: osoby, které chcete sledovat ve smyčce o tomto spolupracovníka pozvání.

5. Nebo zcela přizpůsobit pozvánku a pracovní postup registrace výběrem nechcete odeslat oznámení prostřednictvím služby Azure AD.

    ```
    "sendInvitationMessage": false
    ```

  V takovém případě můžete získat zpět uplatnění adresu URL z rozhraní API, které můžete vložit do e-mailovou šablonu, zasílání rychlých zpráv nebo jiné metody distribuce podle vašeho výběru.

6. Nakonec pokud si nejste správce, můžete pozvat uživatele jako člena.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Ověření modelu
Rozhraní API můžete spustit v těchto režimech ověřování:

### <a name="app--user-mode"></a>Aplikace a uživatelského režimu
V tomto režimu, kdo používá požadavky rozhraní API oprávnění, se vytvoření pozvánky B2B.

### <a name="app-only-mode"></a>Režim pouze aplikace
V kontextu pouze pro aplikace aplikace musí User.Invite.All oboru této pozvánky proběhla úspěšně.

Další informace naleznete na: https://graph.microsoft.io/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell
Nyní je možné přidat a snadno pozvat externího uživatele organizace pomocí Powershellu. Vytvoření pozvánky pomocí rutiny:

```
New-AzureADMSInvitation
```

Můžete použít následující možnosti:

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

Můžete také si přečtěte reference k rozhraní API pozvání v [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="next-steps"></a>Další postup

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Prvky B2B spolupráce e-mailová pozvánka](invitation-email-elements.md)
- [Uplatnění pozvání spolupráce B2B](redemption-experience.md)
- [Přidat uživatele spolupráce B2B bez pozvánky](add-user-without-invite.md)

