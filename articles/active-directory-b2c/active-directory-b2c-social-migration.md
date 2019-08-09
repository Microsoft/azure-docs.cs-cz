---
title: Migrace uživatelů se sociálními identitami v Azure Active Directory B2C | Microsoft Docs
description: Prodiskutujte základní koncepty migrace uživatelů se sociálními identitami do Azure AD B2C pomocí Graph API.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2018
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 0117a0881422584e3cb949661b1d58cd0257cf67
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68853851"
---
# <a name="azure-active-directory-b2c-migrate-users-with-social-identities"></a>Azure Active Directory B2C: Migrace uživatelů se sociálními identitami
Když plánujete migrovat poskytovatele identity na Azure AD B2C, budete možná muset taky migrovat uživatele se sociálními identitami. Tento článek vysvětluje, jak migrovat existující účty sociálních identit, jako jsou: Účty Facebook, LinkedIn, Microsoft a Google vám umožní Azure AD B2C. Tento článek platí také pro federované identity, ale tyto migrace jsou méně časté. Ve zbývající části tohoto článku Vezměte v úvahu cokoli, co platí pro účty sociálních sítí, které platí i pro jiné typy federovaných účtů.

## <a name="prerequisites"></a>Požadavky
Tento článek je pokračováním článku Migrace uživatelů a zaměřuje se na migraci sociální identity. Než začnete, přečtěte si [migraci uživatelů](active-directory-b2c-user-migration.md).

## <a name="social-identities-migration-introduction"></a>Úvod do migrace sociálních identit

* V Azure AD B2C se přihlašovací jména **místních účtů** (uživatelské jméno nebo e-mailová adresa) ukládají do `signInNames` kolekce v záznamu uživatele. Obsahuje jeden nebo více `signInName` záznamů, které určují přihlašovací jména pro uživatele. `signInNames` Každé přihlašovací jméno musí být v rámci tenanta jedinečné.

* Identity **sociálních účtů** jsou uložené v `userIdentities` kolekci. Položka určuje `issuer` (název zprostředkovatele identity) `issuerUserId`, jako je například Facebook.com a, což je jedinečný identifikátor uživatele pro vystavitele. `userIdentities` Atribut obsahuje jeden nebo víc záznamů UserIdentity, které určují typ účtu sociální sítě, a jedinečný identifikátor uživatele od poskytovatele sociálních identit.

* **Zkombinujte místní účet se sociální identitou**. Jak už jsme uvedli, přihlašovací jména místních účtů a identity sociálních účtů se ukládají do různých atributů. `signInNames`se používá pro místní účet, `userIdentities` který se používá pro účty sociálních sítí. Jedním Azure AD B2C účtem může být jenom místní účet, jenom sociální účet, nebo kombinovat místní účet s jednou nebo více sociálními identitami v jednom záznamu uživatele. Toto chování umožňuje spravovat jediný účet, zatímco se uživatel může přihlásit pomocí přihlašovacích údajů místního účtu nebo pomocí sociálních identit.

* `UserIdentity`Typ – obsahuje informace o identitě uživatele sociálního účtu v Azure AD B2C tenantovi:
  * `issuer`Řetězcové vyjádření zprostředkovatele identity, který vystavil identifikátor uživatele, například facebook.com.
  * `issuerUserId`Jedinečný identifikátor uživatele používaný zprostředkovatelem sociální identity ve formátu kódovaném v kódování Base64.

    ```JSON
    "userIdentities": [{
          "issuer": "Facebook.com",
          "issuerUserId": "MTIzNDU2Nzg5MA=="
      }
    ]
    ```

* V závislosti na zprostředkovateli identity je **ID uživatele** vystavitele jedinečnou hodnotou pro daného uživatele podle aplikace nebo vývojového účtu. Nakonfigurujte zásady Azure AD B2C se stejným ID aplikace, které dříve přiřadil poskytovatel sociálních sítí nebo jiná aplikace v rámci stejného vývojového účtu.

## <a name="use-graph-api-to-migrate-users"></a>Použití Graph API k migraci uživatelů
Uživatelský účet Azure AD B2C vytvoříte pomocí [Graph API](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet). Abyste mohli komunikovat s Graph API, musíte mít účet služby s oprávněními správce. Ve službě Azure AD zaregistrujete aplikaci a ověřování do služby Azure AD. Přihlašovací údaje aplikace jsou ID aplikace a tajný kód aplikace. Aplikace funguje samostatně, nikoli jako uživatel, pro volání Graph API. Postupujte podle pokynů v kroku 1 v článku [migrace uživatelů](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-user-migration) .

## <a name="required-properties"></a>Požadované vlastnosti
V následujícím seznamu jsou uvedeny vlastnosti, které jsou požadovány při vytváření uživatele.
* **accountEnabled** – true
* **DisplayName** – název, který se má zobrazit v adresáři pro uživatele.
* **passwordProfile** – profil hesla pro uživatele. 

> [!NOTE]
> Pro účty sociálních sítí (bez přihlašovacích údajů místního účtu) musíte zadat heslo. Azure AD B2C ignoruje heslo, které zadáte pro účty sociálních sítí.

* **userPrincipalName** – hlavní název uživatele (someuser@contoso.com). Hlavní název uživatele musí obsahovat jednu z ověřených domén pro tenanta. Chcete-li zadat hlavní název uživatele (UPN), vygenerujte `@` novou hodnotu GUID zřetězeně s názvem a vaším jménem tenanta.
* **mailNickname** – alias e-mailu pro uživatele Tato hodnota může být stejné ID, které použijete pro userPrincipalName. 
* **signInNames** – jeden nebo více záznamů SignInName, které určují přihlašovací jména pro uživatele. Každé přihlašovací jméno musí být v rámci společnosti nebo tenanta jedinečné. V případě účtu jenom sociální sítě může být tato vlastnost ponechána prázdná.
* **userIdentities** – jeden nebo více záznamů UserIdentity, které určují typ účtu sociální sítě a jedinečný identifikátor uživatele od poskytovatele sociální identity.
* volitelné **otherMails** – jenom pro sociální účet, e-mailové adresy uživatele 

Další informace naleznete v tématu: [Odkaz na Graph API](/previous-versions/azure/ad/graph/api/users-operations#CreateLocalAccountUser)

## <a name="migrate-social-account-only"></a>Migrace účtu sociální sítě (jenom)
Pokud chcete vytvořit jenom sociální účet bez přihlašovacích údajů místního účtu, odešlete požadavek POST protokolu HTTPS na Graph API. Tělo žádosti obsahuje vlastnosti uživatele účtu pro sociální sítě, které se mají vytvořit. Je nutné zadat alespoň požadované vlastnosti. 


**SPUŠTĚNÍ**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Odeslat následující formulář-data: 

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8",
    "signInNames": [],
    "creationType": null,
    "displayName": "Sara Bell",
    "givenName": "Sara",
    "surname": "Bell",
    "passwordProfile": {
        "password": "Test1234",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": null,
    "userIdentities": [{
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ],
    "otherMails": ["sara@live.com"],
    "userPrincipalName": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8@tenant-name.onmicrosoft.com"
}
```
## <a name="migrate-social-account-with-local-account"></a>Migrace účtu v sociální síti pomocí místního účtu
Pokud chcete vytvořit kombinovaný místní účet se sociálními identitami, odešlete do Graph API požadavek HTTPS POST. Tělo žádosti obsahuje vlastnosti uživatele účtu sociální sítě, které se mají vytvořit, včetně přihlašovacího jména pro místní účet. Je nutné zadat alespoň požadované vlastnosti. 

**SPUŠTĚNÍ**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Odeslat následující data formuláře: 

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "5164db16-3eee-4629-bfda-dcc3326790e9",
    "signInNames": [{
            "type": "emailAddress",
            "value": "david@contoso.com"
        }
    ],
    "creationType": "LocalAccount",
    "displayName": "David Hor",
    "givenName": "David",
    "surname": "Hor",
    "passwordProfile": {
        "password": "1234567",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": "DisablePasswordExpiration,DisableStrongPassword",
    "userIdentities": [{
            "issuer": "contoso.com",
            "issuerUserId": "ZGF2aWRAY29udG9zby5jb20="
        }
    ],
    "otherMails": [],
    "userPrincipalName": "5164db16-3eee-4629-bfda-dcc3326790e9@tenant-name.onmicrosoft.com"
}
```

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
### <a name="how-can-i-know-the-issuer-name"></a>Jak můžu znát název vystavitele?
Název vystavitele nebo název zprostředkovatele identity je nakonfigurovaný v zásadách. Pokud neznáte hodnotu, která se má `issuer`zadat v, použijte následující postup:
1. Přihlaste se pomocí jednoho z účtů sociálních sítí.
2. Z tokenu JWT zkopírujte `sub` hodnotu. `sub` Obvykle obsahuje ID objektu uživatele v Azure AD B2C. Nebo z Azure Portal otevřete vlastnosti uživatele a zkopírujte ID objektu.
3. Otevřít [Průzkumníka Azure AD Graph](https://graphexplorer.azurewebsites.net)
4. Přihlaste se pomocí svého správce.
5. Spusťte následující požadavek GET. Nahraďte userObjectId číslem uživatele, který jste zkopírovali. **GET** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId
6. Vyhledá `userIdentities` prvek uvnitř návratového formátu JSON z Azure AD B2C.
7. Volitelné Můžete také chtít dekódovat `issuerUserId` hodnotu.

> [!NOTE]
> Použijte účet správce klienta B2C, který je místní pro tenanta B2C. Syntaxe názvu účtu je admin@tenant-name.onmicrosoft.com.

### <a name="is-it-possible-to-add-a-social-identity-to-an-existing-user"></a>Je možné přidat k existujícímu uživateli sociální identitu?
Ano. Po vytvoření účtu Azure AD B2C (ať už se jedná o místní nebo sociální účet nebo jeho kombinaci) můžete přidat sociální identitu. Spusťte žádost o opravu HTTPS. Nahraďte userObjectId ID uživatele, které chcete aktualizovat. 

**OPRAVA**https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Odeslat následující data formuláře: 

```JSON
{
    "userIdentities": [
        {
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

### <a name="is-it-possible-to-add-multiple-social-identities"></a>Je možné přidat více sociálních identit?
Ano. K jednomu Azure AD B2C účtu můžete přidat více sociálních identit. Spusťte žádost o opravu HTTPS. Nahraďte userObjectId číslem uživatele. 

**OPRAVA**https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Odeslat následující data formuláře: 

```JSON
{
    "userIdentities": [
        {
            "issuer": "google.com",
            "issuerUserId": "MjQzMjE2NTc4NTQ="
        },
        {
            "issuer": "facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

## <a name="optional-user-migration-application-sample"></a>Volitelné Ukázka aplikace migrace uživatelů
[Stáhněte a spusťte ukázkovou aplikaci v2](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). Ukázková aplikace V2 používá soubor JSON, který obsahuje fiktivní uživatelská data, jako jsou místní účet, účet sociální sítě a místní & sociální identity v rámci jednoho účtu.  Chcete-li upravit soubor JSON, otevřete `AADB2C.UserMigration.sln` řešení sady Visual Studio. `AADB2C.UserMigration` V projektu`UsersData.json` otevřete soubor. Soubor obsahuje seznam entit uživatele. Každá entita uživatele má následující vlastnosti:
* **signInName** – místní účet, e-mailová adresa pro přihlášení
* zobrazované jméno uživatele **DisplayName**
* **FirstName** – křestní jméno uživatele
* **LastName** – příjmení uživatele
* **heslo** Pro místní účet heslo uživatele (může být prázdné)
* Vystavitelský účet pro sociální sítě, název zprostředkovatele identity
* **issuerUserId** – účet pro sociální sítě: jedinečný identifikátor uživatele používaný zprostředkovatelem sociální identity. Hodnota by měla být nešifrovaný text. Ukázková aplikace tuto hodnotu zakóduje na base64.
* **e-mail** Jenom pro sociální účet (nekombinované), e-mailová adresa uživatele

```JSON
{
  "userType": "emailAddress",
  "Users": [
    {
      // Local account only
      "signInName": "James@contoso.com",
      "displayName": "James Martin",
      "firstName": "James",
      "lastName": "Martin",
      "password": "Pass!w0rd"
    },
    {
      // Social account only
      "issuer": "Facebook.com",
      "issuerUserId": "1234567890",
      "email": "sara@contoso.com",
      "displayName": "Sara Bell",
      "firstName": "Sara",
      "lastName": "Bell"
    },
    {
      // Combine local account with social identity
      "signInName": "david@contoso.com",
      "issuer": "Facebook.com",
      "issuerUserId": "0987654321",
      "displayName": "David Hor",
      "firstName": "David",
      "lastName": "Hor",
      "password": "Pass!w0rd"
    }
  ]
}
```

> [!NOTE]
> Pokud soubor UsersData. JSON v ukázce neaktualizujete pomocí vašich dat, můžete se přihlásit pomocí ukázkových přihlašovacích údajů místního účtu, ale ne pomocí příkladů účtu pro sociální sítě. K migraci účtů sociálních sítí poskytněte skutečná data.

Další informace, jak používat ukázkovou aplikaci, najdete v tématu [Azure Active Directory B2C: Migrace uživatelů](active-directory-b2c-user-migration.md)
