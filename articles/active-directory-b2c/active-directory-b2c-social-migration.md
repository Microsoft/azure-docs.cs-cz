---
title: Migrace uživatelů se sociálními identitami v Azure Active Directory B2C | Dokumentace Microsoftu
description: Základní koncepty se popisují na migraci uživatelů se sociálními identitami do Azure AD B2C pomocí rozhraní Graph API.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: f4abacb42cfd5efe22d29b58b36eca0a7a1f5ba8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55161306"
---
# <a name="azure-active-directory-b2c-migrate-users-with-social-identities"></a>Azure Active Directory B2C: Migrace uživatelů se sociálními identitami
Při plánování migrace vašeho zprostředkovatele identity do Azure AD B2C, budete také muset migrace uživatelů se sociálními identitami. Tento článek vysvětluje, jak provést migraci existujících účtů sociálních identit, jako například: Facebook, LinkedIn, Microsoft nebo Google účty Azure AD B2C. Tento článek se týká také federované identity, ale jsou méně běžné tyto migrace.

## <a name="prerequisites"></a>Požadavky
V tomto článku je pokračování článku migrace uživatelů a se zaměřuje na migraci identity v sociálních sítích. Než začnete, přečtěte si [migraci uživatelů](active-directory-b2c-user-migration.md).

## <a name="social-identities-migration-introduction"></a>Úvod migrace sociálních identit

* V Azure AD B2C **místní účty** přihlašovací jména (uživatelského jména nebo e-mailové adresy) jsou uloženy v `signInNames` kolekce v záznamu uživatele. `signInNames` Obsahuje jeden nebo více `signInName` záznamy, které určují názvy přihlášení pro daného uživatele. Název každé přihlášení musí být jedinečný v tenantovi.

* **Sociální účty** identit jsou uloženy v `userIdentities` kolekce. Určuje položku `issuer` (název zprostředkovatele identity) jako je například facebook.com a `issuerUserId`, který je jedinečný identifikátor uživatele pro vydavatele. `userIdentities` Atribut obsahuje jeden nebo více záznamů identity uživatele, které určují typ účtu na sociální síti a jedinečný uživatelský identifikátor zprostředkovatele identity v sociálních sítích.

* **Místní účet v kombinaci s sociálních identit**. Jak už bylo zmíněno, místní účet přihlašovací jména a účtu na sociální síti identit jsou uloženy v jiné atributy. `signInNames` je použit pro místní účet, zatímco `userIdentities` pro účtu na sociální síti. Jeden účet Azure AD B2C, můžete použít pouze místní účet, pouze účtu na sociální síti nebo místní účet v kombinaci s sociálních identit v záznamu s jedním uživatelem. Toto chování můžete spravovat jeden účet, když uživatel se můžete přihlásit pomocí místního účtu credential(s) nebo se sociálními identitami.

* `UserIdentity` Typ – obsahuje informace o identitě uživatele účtu na sociální síti tenanta služby Azure AD B2C:
    * `issuer` Řetězcové vyjádření zprostředkovatele identity, která vydala identifikátor uživatele, jako je například facebook.com.
    * `issuerUserId` Jedinečný uživatelský identifikátor používá zprostředkovatele sociální identity ve formátu base64.

    ```JSON
    "userIdentities": [{
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
    ```

* V závislosti na poskytovateli identity **sociální Id_uživatele** je jedinečná hodnota pro daného uživatele a účet aplikace nebo vývoj. Konfigurovat zásady Azure AD B2C se stejným ID aplikace, která dříve byla přiřazena pomocí poskytovatele sociálních sítí. Nebo v jiné aplikaci v rámci stejného účtu vývoje.

## <a name="use-graph-api-to-migrate-users"></a>Používání rozhraní Graph API pro migraci uživatelů
Vytvoření uživatelského účtu Azure AD B2C prostřednictvím [rozhraní Graph API](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet). Ke komunikaci s rozhraním Graph API, nejprve musíte mít účet služby s oprávněními správce. Ve službě Azure AD registrace aplikace a ověřování do služby Azure AD. Přihlašovací údaje aplikací jsou ID aplikace a tajný klíč aplikace. Aplikace slouží jako samostatně, nikoli jako uživatel, pro volání rozhraní Graph API. Postupujte podle pokynů v kroku 1 v [migraci uživatelů](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-user-migration#step-1-use-graph-api-to-migrate-users) článku.

## <a name="required-properties"></a>Požadované vlastnosti
V následujícím seznamu jsou uvedeny vlastnosti, které jsou potřeba při vytvoření uživatele.
* **accountEnabled** – true
* **displayName** – název má být zobrazen v adresáři uživatele.
* **passwordProfile** – profil hesla uživatele. 

> [!NOTE]
> U účtu na sociální síti pouze (bez přihlašovacích údajů místního účtu) stále musíte zadat heslo. Azure AD B2C ignoruje heslo, které zadáte pro účtů na sociálních sítích.

* **userPrincipalName** – hlavní název uživatele (someuser@contoso.com). Hlavní název uživatele musí obsahovat jeden z ověřených domén tenanta. Chcete-li určit hlavní název uživatele, generovat nové hodnota identifikátoru GUID, pomocí zřetězení `@` a název vašeho klienta.
* **mailNickname** – alias e-mailu pro daného uživatele. Tato hodnota může být stejné ID, který používáte pro userPrincipalName. 
* **signInNames** – jeden nebo více SignInName záznamů, které určují názvy přihlášení pro daného uživatele. Název každé přihlášení musí být jedinečný mezi společnosti nebo tenantovi. U účtu na sociální síti pouze tato vlastnost může zůstat prázdné.
* **userIdentities** – jeden nebo více záznamů identity uživatele, které určují sociálního účtu typu a jedinečný uživatelský identifikátor zprostředkovatele identity v sociálních sítích.
* [volitelné] **otherMails** – účtu na sociální síti, e-mailové adresy uživatele 

Další informace naleznete v tématu: [Odkaz na rozhraní Graph API](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser)

## <a name="migrate-social-account-only"></a>Migrace účtu na sociální síti (jenom)
K vytvoření účtu na sociální síti pouze bez přihlašovacích údajů místního účtu. Odešlete požadavek POST protokolu HTTPS pro rozhraní Graph API. Text požadavku obsahuje vlastnosti tohoto uživatelského účtu na sociální síti vytvořit. Minimálně je nutné zadat požadované vlastnosti. 


**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Následující data formuláře odešlete: 

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
## <a name="migrate-social-account-with-local-account"></a>Migrace účtu na sociální síti pomocí místního účtu
Vytvoření kombinované místní účet se sociálními identitami. Odešlete požadavek POST protokolu HTTPS pro rozhraní Graph API. Text požadavku obsahuje vlastnosti tohoto uživatelského účtu na sociální síti vytvořit. Minimálně je nutné zadat požadované vlastnosti. 

**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Odešlete následující formulář dat: 

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
Název vystavitele nebo název zprostředkovatele identit je nakonfigurované v zásadách. Pokud si nejste jisti hodnotu v `issuer`, postupujte takto:
1. Přihlaste se pomocí jeden z účtů v sociálních sítích
2. Z tokenu JWT, zkopírujte `sub` hodnotu. `sub` Obvykle obsahuje ID objektu uživatele v Azure AD B2C. Nebo z webu Azure portal, otevřete vlastnosti uživatele a zkopírujte ID objektu.
3. Otevřít [Azure AD Graph Exploreru](https://graphexplorer.azurewebsites.net)
4. Přihlaste se pomocí svého správce. Ne
5. Spusťte následující požadavek GET. Nahraďte ID uživatele, který jste zkopírovali userObjectId. **GET** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId
6. Vyhledejte `userIdentities` prvek uvnitř šablony JSON návrat z Azure AD B2C.
7. [Volitelné] Můžete také dekódování `issuerUserId` hodnotu.

> [!NOTE]
> Použijte účet správce tenanta B2C, která je místní pro tenanta B2C. Syntaxe názvu účtu se admin@tenant-name.onmicrosoft.com.

### <a name="is-it-possible-to-add-social-identity-to-an-existing-local-account"></a>Je možné přidat do existující místní účet identity v sociálních sítích?
Ano. Sociální identity můžete přidat po vytvoření místního účtu. Spuštění požadavku HTTPS PATCH. UserObjectId nahraďte ID uživatele, které chcete aktualizovat. 

**OPRAVA** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Odešlete následující formulář dat: 

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
Ano. Můžete přidat více sociálních identit pro jeden účet Azure AD B2C. Spuštění požadavku HTTPS PATCH. UserObjectId nahraďte ID uživatele. 

**OPRAVA** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Odešlete následující formulář dat: 

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

## <a name="optional-user-migration-application-sample"></a>[Volitelné] Ukázkové aplikace migrace uživatelů
[Stažení a spuštění ukázkové aplikace V2](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). Ukázková aplikace V2 používá soubor JSON, který obsahuje fiktivní uživatele data, včetně: místní účet, účtu na sociální síti a místní a sociálních identit v jednom účtu.  Chcete-li upravit soubor JSON, otevřete `AADB2C.UserMigration.sln` řešení sady Visual Studio. V `AADB2C.UserMigration` projekt, otevřete `UsersData.json` souboru. Soubor obsahuje seznam entit uživatelů. Každá entita uživatele má následující vlastnosti:
* **signInName** – pro místní účet, e-mailovou adresu pro přihlášení
* **displayName** – zobrazované jméno uživatele
* **firstName** -křestní jméno uživatele
* **Příjmení** -příjmení uživatele
* **heslo** pro místní účet, heslo uživatele (může být prázdné)
* **Vystavitel** – účtu na sociální síti, název zprostředkovatele identit
* **issuerUserId** – účtu na sociální síti, jedinečný identifikátor uživatele používá zprostředkovatel identity v sociálních sítích. Hodnota musí být ve formátu prostého textu. Ukázková aplikace kóduje tuto hodnotu ve formátu Base64.
* **e-mailu** pro účtu na sociální síti pouze (ne kombinované), uživatele, kteří e-mailová adresa

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
> Pokud nechcete aktualizovat soubor UsersData.json ve vzorku s daty, můžete se přihlásit pomocí přihlašovacích údajů místního účtu vzorku, ale ne s příklady účtu na sociální síti. Pokud chcete migrovat vaše účtů na sociálních sítích, poskytují reálná data.

Další informace jak použít ukázkovou aplikaci, najdete v článku [Azure Active Directory B2C: Migrace uživatelů](active-directory-b2c-user-migration.md)
