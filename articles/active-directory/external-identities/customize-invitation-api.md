---
title: Rozhraní API pro spolupráci B2B a přizpůsobení – Azure Active Directory
description: Azure Active Directory spolupráce B2B podporuje vztahy mezi společnostmi tím, že umožňuje obchodním partnerům selektivní přístup k podnikovým aplikacím.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cc959d23aebea082d873e9474998e7f17b1b320
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91295154"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Azure Active Directory rozhraní API pro spolupráci B2B a přizpůsobení

Spousta zákazníků nám informovala, že chce přizpůsobit proces pozvánky způsobem, který nejlépe vyhovuje jejich organizacím. Pomocí našeho rozhraní API můžete provést pouze to. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>Schopnosti rozhraní API pro pozvánky

Rozhraní API nabízí následující možnosti:

1. Pozvání externího uživatele pomocí *libovolné* e-mailové adresy

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Přizpůsobte si, kam chcete, aby vaši uživatelé mohli po přijetí pozvánky vykládat.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Vyberte, chcete-li odeslat standardní e-mail s pozvánkou prostřednictvím nás.

    ```
    "sendInvitationMessage": true
    ```

   se zprávou na příjemce, kterého můžete přizpůsobit

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. A zvolíte možnost CC: lidi, které chcete ve smyčce zachovávat o pozvání tohoto spolupracovníka.

5. Můžete také úplně přizpůsobit pozvánku a pracovní postup připojování tím, že zvolíte, že nechcete odesílat oznámení prostřednictvím služby Azure AD.

    ```
    "sendInvitationMessage": false
    ```

   V takovém případě se vrátí URL pro uplatnění z rozhraní API, které můžete vložit do e-mailové šablony, IM nebo jiné metody distribuce podle vašeho výběru.

6. Nakonec, pokud jste správce, můžete zvolit, aby uživatel pozvaní jako člen.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Autorizační model

Rozhraní API je možné spustit v následujících režimech autorizace:

### <a name="app--user-mode"></a>Aplikace a uživatelský režim

V tomto režimu musí, kdo používá rozhraní API, mít oprávnění k vytváření pozvánek B2B.

### <a name="app-only-mode"></a>Režim pouze aplikace

V kontextu pouze aplikace potřebuje aplikace uživatele. pozve. All, aby bylo pozvání úspěšné.

Další informace najdete v tématu: https://developer.microsoft.com/graph/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell

K snadnému přidání a pozvání externích uživatelů do organizace můžete použít PowerShell. Vytvoření pozvánky pomocí rutiny:

```powershell
New-AzureADMSInvitation
```

Můžete použít následující možnosti:

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

### <a name="invitation-status"></a>Stav pozvánky

Po odeslání pozvánky externímu uživateli můžete pomocí rutiny **Get-AzureADUser** zjistit, jestli je přijal. Když se externímu uživateli pošle pozvánka, naplní se následující vlastnosti Get-AzureADUser:

* **UserState** označuje, zda je pozvánka **PendingAcceptance** nebo **přijata**.
* **UserStateChangedOn** zobrazuje časové razítko pro poslední změnu vlastnosti **userState** .

K filtrování výsledků podle **userState**můžete použít možnost **Filter** . Následující příklad ukazuje, jak filtrovat výsledky, aby se zobrazily pouze uživatelé, kteří mají nevyřízenou pozvánku. Tento příklad také ukazuje možnost **formátovat seznam** , která umožňuje určit vlastnosti, které se mají zobrazit. 
 

```powershell
Get-AzureADUser -Filter "UserState eq 'PendingAcceptance'" | Format-List -Property DisplayName,UserPrincipalName,UserState,UserStateChangedOn
```

> [!NOTE]
> Ujistěte se, že máte nejnovější verzi modulu PowerShellu AzureAD nebo modulu PowerShellu AzureADPreview. 

## <a name="see-also"></a>Viz také

Podívejte se na reference k rozhraní API pozvánky v [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) .

## <a name="next-steps"></a>Další kroky

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Prvky e-mailu s pozvánkou pro spolupráci B2B](invitation-email-elements.md)
- [Uplatnění pozvánky B2B pro spolupráci](redemption-experience.md)
- [Přidat uživatele spolupráce B2B bez pozvánky](add-user-without-invite.md)