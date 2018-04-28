---
title: 'Azure Active Directory B2C: Migraci uživatelů s identitami, sociálních'
description: Informace o klíčových konceptech na migraci uživatelů s sociálních identity do Azure AD B2C, pomocí rozhraní Graph API
services: active-directory-b2c
documentationcenter: ''
author: davidmu
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/03/2018
ms.author: davidmu
ms.openlocfilehash: a150d8255171c83334ac4c1f81cbcee39c1df70a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="azure-active-directory-b2c-migrate-users-with-social-identities"></a>Azure Active Directory B2C: Migraci uživatelů s identitami, sociálních
Při plánování migrace zprostředkovatele identity do Azure AD B2C, může také muset migrovat uživatelé s sociálních identity. Tento článek vysvětluje, jak migrovat existující účty sociálních identity, jako například: Facebook, LinkedIn, Microsoft a Google účtů Azure AD B2C. Tento článek se týká také federované identity, ale tyto migrace jsou méně častých.

## <a name="prerequisites"></a>Požadavky
V tomto článku je pokračování článku migrace uživatelů a se zaměřuje na sociálních identity migrace. Než začnete, přečtěte si [migraci uživatele](active-directory-b2c-user-migration.md).

## <a name="social-identities-migration-introduction"></a>Sociální identity migrace Úvod

* V Azure AD B2C **místní účty** názvy přihlášení (uživatelského jména nebo e-mailové adresy) jsou uložené v `signInNames` kolekce v záznamu uživatele. `signInNames` Obsahuje jeden nebo více `signInName` záznamy, které zadáte názvy přihlášení pro uživatele. Každý přihlašovací jméno musí být jedinečný mezi klienta.

* **Sociální účty** se ukládají identity v `userIdentities` kolekce. Určuje položku `issuer` (název zprostředkovatele identity) jako je například facebook.com a `issuerUserId`, což je jedinečný identifikátor uživatele pro vystavitele. `userIdentities` Atribut obsahuje jeden nebo více záznamů identity uživatele, které určují typ sociálních účtu a jedinečný uživatelský identifikátor ze zprostředkovatele sociální identity.

* **Kombinovat místní účet s sociálních identity**. Jak je uvedeno, místní účet přihlášení názvy a sociálních účet identity jsou uloženy v různé atributy. `signInNames` se používá pro místní účet, při `userIdentities` sociálních účtu. Jeden účet Azure AD B2C, může být pouze místní účet, sociálních účet pouze nebo v kombinaci místní účet s sociálních identity v záznamu s jedním uživatelem. Toto chování umožňuje spravovat jeden účet, když uživatel se můžete přihlásit se pomocí místního účtu credential(s) nebo sociální identity.

* `UserIdentity` Zadejte – obsahuje informace o identitě sociálních účet uživatele v klienta Azure AD B2C:
    * `issuer` Řetězcová reprezentace zprostředkovatele identity, která vydala identifikátor uživatele, jako je například facebook.com.
    * `issuerUserId` Jedinečný uživatelský identifikátor používá zprostředkovatel sociální identity ve formátu base64.

    ```JSON
    "userIdentities": [{
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
    ```

* V závislosti na poskytovateli identity **ID sociálních uživatele** je jedinečná hodnota pro daného uživatele na účet aplikace nebo vývoj. Nakonfigurujte zásady služby Azure AD B2C se stejným ID aplikace, který byl dříve přiřazen sociálních zprostředkovatelem. Nebo jiná aplikace v rámci stejného účtu vývoj.

## <a name="use-graph-api-to-migrate-users"></a>Pomocí rozhraní Graph API pro migraci uživatelů
Vytvořit uživatelský účet Azure AD B2C prostřednictvím [rozhraní Graph API](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet). Ke komunikaci s rozhraní Graph API, nejprve musí mít účet služby s oprávněními správce. Ve službě Azure AD je třeba zaregistrovat aplikaci a ověřování do služby Azure AD. Přihlašovací údaje aplikací jsou ID aplikace a tajný klíč aplikace. Aplikace funguje jako samostatně, nikoli jako uživatel, k volání rozhraní Graph API. Postupujte podle pokynů v kroku 1 v [migraci uživatele](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-user-migration#step-1-use-graph-api-to-migrate-users) článku.

## <a name="required-properties"></a>Požadované vlastnosti
Následující seznam obsahuje vlastnosti, které jsou potřeba při vytvoření uživatele.
* **accountEnabled** – true
* **displayName** -název, který má zobrazit v seznamu adres pro uživatele.
* **passwordProfile** -profil heslo pro uživatele. 

> [!NOTE]
> Pro sociálních účet pouze (bez přihlašovacích údajů místního účtu) stále musíte zadat heslo. Azure AD B2C ignoruje heslo, které zadáte pro sociálních účty.

* **userPrincipalName** – hlavní název uživatele (someuser@contoso.com). Hlavní název uživatele musí obsahovat jeden z ověřených domén pro klienta. Chcete-li určit hlavní název uživatele, vygenerovat novou hodnotu identifikátoru GUID, zřetězení s `@` a název vašeho klienta.
* **mailNickname** -alias e-mailu pro uživatele. Tato hodnota může být stejným ID, který používáte pro userPrincipalName. 
* **signInNames** – jeden nebo více záznamů SignInName, které zadejte názvy přihlášení pro uživatele. Každý přihlašovací jméno musí být jedinečný mezi společnosti nebo klienta. Sociální pouze pro účet tato vlastnost může být prázdné.
* **userIdentities** – jeden nebo více zaznamenává identity uživatele, které určují sociálního účtů typ a jedinečný uživatelský identifikátor ze zprostředkovatele sociální identity.
* [Nepovinné] **otherMails** – sociálních pouze pro účet, e-mailové adresy uživatele 

Další informace najdete v tématu: [Graph API – referenční informace](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser)

## <a name="migrate-social-account-only"></a>Migrace sociálních účet (pouze)
Vytvoření, sociálních účtu bez přihlašovacích údajů místního účtu. Odešlete požadavek POST protokolu HTTPS k rozhraní Graph API. Text žádosti obsahuje vlastnosti sociálních účet uživatele k vytvoření. Minimálně musí zadejte požadované vlastnosti. 


**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Odešlete následující data formuláře: 

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
## <a name="migrate-social-account-with-local-account"></a>Migrace sociálních účet s místní účet
Chcete-li vytvořit kombinované místní účet s identitami, sociálních. Odešlete požadavek POST protokolu HTTPS k rozhraní Graph API. Text žádosti obsahuje vlastnosti sociálních účet uživatele k vytvoření. Minimálně musí zadejte požadované vlastnosti. 

**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Odešlete následující data formuláře: 

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
### <a name="how-can-i-know-the-issuer-name"></a>Jak poznám, název vystavitele?
Název vystavitele, nebo název zprostředkovatele identity je nakonfigurované v vaše zásady. Pokud si nejste jisti hodnotu udávající v `issuer`, postupujte takto:
1. Přihlaste se pomocí jednoho z sociálních účtů
2. Z tokenu JWT, zkopírujte `sub` hodnotu. `sub` Obvykle obsahuje ID objektu uživatele v Azure AD B2C. Nebo z portálu Azure, otevřete vlastnosti uživatele a zkopírujte ID objektu.
3. Otevřete [Explorer Azure AD Graph](https://graphexplorer.azurewebsites.net)
4. Přihlaste se pomocí svého správce. Ne
5. Spusťte následující požadavek GET. Nahraďte userObjectId ID uživatele, který jste zkopírovali. **GET** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId
6. Vyhledejte `userIdentities` element uvnitř návratový JSON z Azure AD B2C.
7. [Nepovinné] Můžete také dekódovat `issuerUserId` hodnotu.

> [!NOTE]
> Používejte účet správce klienta B2C, který je místní pro klienta B2C. Syntaxe názvu účtu není admin@tenant-name.onmicrosoft.com.

### <a name="is-it-possible-to-add-social-identity-to-an-existing-local-account"></a>Je možné přidat do existující místní účet sociálních identity?
Ano. Sociální identity můžete přidat po vytvoření místního účtu. Spusťte požadavku HTTPS PATCH. Nahraďte userObjectId ID uživatele, který chcete aktualizovat. 

**OPRAVA** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Odešlete následující data formuláře: 

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

### <a name="is-it-possible-to-add-multiple-social-identities"></a>Je možné přidat více identit sociálních?
Ano. Můžete přidat více sociálních identit pro jeden účet Azure AD B2C. Spusťte požadavku HTTPS PATCH. Nahraďte userObjectId ID uživatele. 

**OPRAVA** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Odešlete následující data formuláře: 

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

## <a name="optional-user-migration-application-sample"></a>[Nepovinné] Ukázkové aplikace migrace uživatele
[Stažení a spuštění ukázkové aplikace V2](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). Ukázková aplikace V2 používá soubor JSON, který obsahuje data fiktivní uživatele, včetně: místní účet, sociálních účet a místní a sociálních identit v jeden účet.  Chcete-li upravit soubor JSON, otevřete `AADB2C.UserMigration.sln` řešení sady Visual Studio. V `AADB2C.UserMigration` projekt, otevřete `UsersData.json` souboru. Soubor obsahuje seznam entitami uživatelů. Každá entita uživatel má následující vlastnosti:
* **signInName** – pro místní účet, e-mailovou adresu pro přihlášení
* **displayName** -zobrazované jméno uživatele
* **firstName** -křestní jméno uživatele
* **lastName** -příjmení uživatele
* **heslo** pro místní účet, který uživatelské heslo (nesmí být prázdné)
* **Vystavitel** – sociálních účtu, název zprostředkovatele identity
* **issuerUserId** – sociálních účtu, jedinečný identifikátor uživatele používané poskytovateli sociální identity. Hodnota musí být ve formátu prostého textu. Ukázková aplikace kóduje tuto hodnotu formátu Base64.
* **e-mailu** pro sociální pouze (ne kombinované), uživatelský účet pro e-mailová adresa

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
> Pokud soubor UsersData.json v ukázce nejsou aktualizována s daty, můžete přihlášení s ukázka přihlašovací údaje místního účtu, ale není s příklady sociálních účtu. Pokud chcete migrovat své sociálních účty, zadejte reálná data.

Další informace jak použít ukázkové aplikace, najdete v části [Azure Active Directory B2C: Migrace uživatele](active-directory-b2c-user-migration.md)
