---
title: Azure Active Directory s B2B spolupráce rozhraní API a přizpůsobení | Dokumentace Microsoftu
description: Spolupráce B2B ve službě Azure Active Directory podporuje vaše vztahy s ostatními společnostmi tím, že vašim obchodním partnerům umožní selektivní přístup ke podnikovým aplikacím
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 04/11/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: a475c9032d2c4062b506c1e6ebcadd1901c946b6
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56177423"
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

Další informace naleznete na: https://developer.microsoft.com/graph/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell
Prostředí PowerShell můžete použít k přidání a snadno pozvat externího uživatele organizace. Vytvoření pozvánky pomocí rutiny:

```
New-AzureADMSInvitation
```

Můžete použít následující možnosti:

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

### <a name="invitation-status"></a>Stav žádosti

Jakmile odešlete pozvání externího uživatele, můžete použít **Get-AzureADUser** rutiny zobrazíte, pokud jste přijali ji. Následující vlastnosti Get-AzureADUser zaplnění odeslání pozvání externího uživatele:

* **Stav uživatele UserState** označuje, zda je e-mailové pozvánce **PendingAcceptance** nebo **přijato**.
* **UserStateChangedOn** ukazuje časové razítko pro poslední změnu **stav uživatele UserState** vlastnost.

Můžete použít **filtr** filtrovat výsledky podle **stav uživatele UserState**. Následující příklad ukazuje, jak filtrovat výsledky a zobrazit pouze uživatele, kteří mají nevyřízená Pozvánka. Příklad také ukazuje, **Format-List** možnost, která umožňuje určit vlastnosti pro zobrazení. 
 
```
Get-AzureADUser -Filter "UserState eq 'PendingAcceptance'" | Format-List -Property DisplayName,UserPrincipalName,UserState,UserStateChangedOn
```

> [!NOTE]
> Ujistěte se, že máte nejnovější verzi modulu Azure AD PowerShell nebo modulu AzureADPreview PowerShell. 

## <a name="see-also"></a>Další informace najdete v tématech

Podívejte se na odkaz na pozvánku rozhraní API v [ https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation ](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

## <a name="next-steps"></a>Další postup

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Prvky B2B spolupráce e-mailová pozvánka](invitation-email-elements.md)
- [Uplatnění pozvání spolupráce B2B](redemption-experience.md)
- [Přidat uživatele spolupráce B2B bez pozvánky](add-user-without-invite.md)

