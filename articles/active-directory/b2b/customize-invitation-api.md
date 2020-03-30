---
title: Rozhraní API pro spolupráci b2B a přizpůsobení – Azure Active Directory
description: Spolupráce B2B ve službě Azure Active Directory podporuje vaše vztahy s ostatními společnostmi tím, že vašim obchodním partnerům umožní selektivní přístup ke podnikovým aplikacím
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 04/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a090ee3f9588ff6bff01e12db469bf04407a7fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263462"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Rozhraní API pro spolupráci a přizpůsobení služby Azure Active Directory B2B

Mnoho zákazníků nám říká, že chtějí přizpůsobit proces pozvání způsobem, který nejlépe vyhovuje jejich organizacím. S naším ROZHRANÍm API to můžete udělat. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>Možnosti rozhraní API pro pozvání

Rozhraní API nabízí následující funkce:

1. Pozvěte externího uživatele s *libovolnou* e-mailovou adresou.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Přizpůsobte si, kde mají uživatelé přistát poté, co přijmou pozvání.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Zvolte, zda chcete prostřednictvím nás poslat standardní e-mail s pozvánkou

    ```
    "sendInvitationMessage": true
    ```

   se zprávou příjemci, kterou můžete přizpůsobit

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. A zvolte cc: lidé, které chcete udržet v obraze o své pozvání tohoto spolupracovníka.

5. Nebo úplně přizpůsobte pozvánku a pracovní postup registrace tím, že se rozhodnete neodesílat oznámení prostřednictvím Azure AD.

    ```
    "sendInvitationMessage": false
    ```

   V takovém případě získáte zpět adresu URL pro využití z rozhraní API, kterou můžete vložit do šablony e-mailu, zasílání odložených společností nebo jiné metody distribuce podle vašeho výběru.

6. A konečně, pokud jste správce, můžete pozvat uživatele jako člena.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Autorizační model

Rozhraní API lze spustit v následujících režimech autorizace:

### <a name="app--user-mode"></a>Aplikace + uživatelský režim

V tomto režimu, ten, kdo používá rozhraní API musí mít oprávnění k vytváření b2B pozvánky.

### <a name="app-only-mode"></a>Režim pouze pro aplikaci

V kontextu pouze aplikace aplikace potřebuje User.Invite.All obor pro pozvání k úspěchu.

Další informace naleznete v:https://developer.microsoft.com/graph/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell

Pomocí PowerShellu můžete snadno přidávat a zvát externí uživatele do organizace. Vytvořte pozvánku pomocí rutiny:

```powershell
New-AzureADMSInvitation
```

Můžete použít následující možnosti:

* -InvitedUserDisplayName
* -InvitedUserEmailAddress -InvitedUserEmailAddress -InvitedUserEmailAddress -Invite
* -SendInvitationMessage
* -InvitedUserMessageInfo

### <a name="invitation-status"></a>Stav pozvánky

Po odeslání pozvánky externímu uživateli můžete pomocí rutiny **Get-AzureADUser** zjistit, jestli ji přijali. Následující vlastnosti Get-AzureADUser jsou naplněny při odeslání pozvánky externímu uživateli:

* **UserState** označuje, zda je **pozvánka PendingAcceptance** nebo **Accepted**.
* **UserStateChangedOn** zobrazí časové razítko pro nejnovější změnu **vlastnosti UserState.**

Pomocí možnosti **Filtr** můžete filtrovat výsledky podle **UserState**. Následující příklad ukazuje, jak filtrovat výsledky tak, aby zobrazovalo pouze uživatele, kteří mají nevyřízenou pozvánku. V příkladu je také zobrazena možnost **Formát-List,** která umožňuje určit vlastnosti, které se mají zobrazit. 
 

```powershell
Get-AzureADUser -Filter "UserState eq 'PendingAcceptance'" | Format-List -Property DisplayName,UserPrincipalName,UserState,UserStateChangedOn
```

> [!NOTE]
> Ujistěte se, že máte nejnovější verzi modulu AzureAD PowerShell nebo Modul UVerU Pro PowerShell AzureADPreview. 

## <a name="see-also"></a>Viz také

Podívejte se na odkaz [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)na rozhraní API pozvánky v .

## <a name="next-steps"></a>Další kroky

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Prvky b2B spolupráce pozvání e-mail](invitation-email-elements.md)
- [Uplatnění pozvánky na spolupráci B2B](redemption-experience.md)
- [Přidání uživatelů spolupráce B2B bez pozvání](add-user-without-invite.md)