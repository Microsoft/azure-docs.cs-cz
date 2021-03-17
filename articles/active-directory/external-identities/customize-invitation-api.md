---
title: Rozhraní API pro spolupráci B2B a přizpůsobení – Azure Active Directory
description: Azure Active Directory spolupráce B2B podporuje vztahy mezi společnostmi tím, že umožňuje obchodním partnerům selektivní přístup k podnikovým aplikacím.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8160859bb782ee8ffc4fef5ee03b61b6f54be1bb
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99548657"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Azure Active Directory rozhraní API pro spolupráci B2B a přizpůsobení

Spousta zákazníků nám informovala, že chce přizpůsobit proces pozvánky způsobem, který nejlépe vyhovuje jejich organizacím. Pomocí našeho rozhraní API můžete provést pouze to. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](/graph/api/resources/invitation)

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

## <a name="determine-if-a-user-was-already-invited-to-your-directory"></a>Určení, zda byl uživatel již pozván do vašeho adresáře

Rozhraní API pro pozvánky můžete použít k určení, jestli už uživatel ve vašem tenantovi prostředků existuje. To může být užitečné, když vyvíjíte aplikaci, která používá rozhraní API pro pozvání k pozvání uživatele. Pokud uživatel ve vašem adresáři prostředků už existuje, neobdrží pozvánku, takže můžete nejdřív spustit dotaz, abyste zjistili, jestli už e-mail existuje jako hlavní název uživatele (UPN) nebo jiné přihlašovací vlastnost.

1. Ujistěte se, že e-mailová doména uživatele není součástí ověřené domény vašeho tenanta prostředků.
2. V tenantovi prostředků použijte následující příkaz získat dotaz uživatele, kde {0} je e-mailová adresa, kterou zvete:

   ```
   “userPrincipalName eq '{0}' or mail eq '{0}' or proxyAddresses/any(x:x eq 'SMTP:{0}') or signInNames/any(x:x eq '{0}') or otherMails/any(x:x eq '{0}')"
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

Po odeslání pozvánky externímu uživateli můžete pomocí rutiny **Get-AzureADUser** zjistit, jestli je přijal. Když externímu uživateli pošle pozvánku, naplní se následující vlastnosti Get-AzureADUser:

* **UserState** označuje, zda je pozvánka **PendingAcceptance** nebo **přijata**.
* **UserStateChangedOn** zobrazuje časové razítko pro poslední změnu vlastnosti **userState** .

K filtrování výsledků podle **userState** můžete použít možnost **Filter** . Následující příklad ukazuje, jak filtrovat výsledky, aby se zobrazily pouze uživatelé, kteří mají nevyřízenou pozvánku. Tento příklad také ukazuje možnost **formátovat seznam** , která umožňuje určit vlastnosti, které se mají zobrazit. 
 

```powershell
Get-AzureADUser -Filter "UserState eq 'PendingAcceptance'" | Format-List -Property DisplayName,UserPrincipalName,UserState,UserStateChangedOn
```

> [!NOTE]
> Ujistěte se, že máte nejnovější verzi modulu PowerShellu AzureAD nebo modulu PowerShellu AzureADPreview. 

## <a name="see-also"></a>Viz také

Podívejte se na reference k rozhraní API pozvánky v [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](/graph/api/resources/invitation) .

## <a name="next-steps"></a>Další kroky

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Prvky e-mailu s pozvánkou pro spolupráci B2B](invitation-email-elements.md)
- [Uplatnění pozvánky B2B pro spolupráci](redemption-experience.md)
- [Přidat uživatele spolupráce B2B bez pozvánky](add-user-without-invite.md)