---
title: Zjistěte, jak poskytnout volitelné deklarace identity do aplikace Azure AD | Microsoft Docs
description: Příručka pro přidání vlastní nebo další deklarace identity na tokeny SAML 2.0 a webové tokeny JSON (JWT) vydaný Azure Active Directory.
documentationcenter: na
author: CelesteDG
services: active-directory
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: ffd774477881be6b7f46dd38bbc88c8d019223aa
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317200"
---
# <a name="optional-claims-in-azure-ad-preview"></a>Volitelné deklarace identity ve službě Azure AD (preview)

Tato funkce slouží vývojáři aplikace k určení, které deklarace identity, která je v tokenech odeslat do své aplikace. Můžete použít volitelný deklarace identity:
-   Vyberte další deklarace identity pro zahrnutí do tokenů pro vaši aplikaci.
-   Změňte chování určité deklarace identity, které vrací Azure AD v tokenech.
-   Přidejte a přístup k vlastní deklarace pro vaši aplikaci. 

> [!Note]
> Tato funkce je aktuálně ve verzi public preview. Připravte se na obnovit nebo odeberte všechny změny. Tato funkce je k dispozici v libovolné předplatné Azure AD během verzi public preview. Když je tato funkce obecně dostupná, může vyžadovat některé aspekty funkce však předplatné služby Azure AD premium.

Seznam standardní deklarace identity a jak se používají v tokenech, naleznete v části [základy tokeny vydané službou Azure AD](active-directory-token-and-claims.md). 

Jedním z cílů systému [koncového bodu v2.0 Azure AD](active-directory-appmodel-v2-overview.md) je menší velikost tokenu zajistit optimální výkon klienty.  V důsledku toho několik deklarace identity dříve součástí přístup a tokeny typu ID již neexistují v tokenech v2.0 a musí být žádali konkrétně na základě jednotlivých aplikací.  

**Tabulka 1: použitelnosti**

| Typ účtu | Koncový bod verze 1.0                      | Koncový bod v2.0  |
|--------------|------------------------------------|----------------|
| MSA          | Není k dispozici - RPS lístky se místo toho používají. | Podpora již |
| AAD          | Podporováno                          | Podporováno      |

## <a name="standard-optional-claims-set"></a>Standardní volitelné deklarací
Níže jsou uvedeny sadu volitelné deklarací identity ve výchozím nastavení k dispozici pro použití aplikacemi.  Pokud chcete přidat vlastní deklarace volitelné pro vaši aplikaci, najdete v části [rozšíření adresáře](active-directory-optional-claims.md#Configuring-custom-claims-via-directory-extensions), níže. 

> [!Note]
>Většina těchto deklarace identity můžou být součástí tokeny Jwt, ale není tokeny SAML, s výjimkou uvedeno ve sloupci Typ tokenu.  Při volitelné deklarace identity jsou podporovány pouze pro uživatele AAD aktuálně, kromě toho je právě přidána podpora účet spravované služby.  Pokud MSA volitelné deklarací identity podporovat na koncový bod v2.0, sloupci Typ uživatele bude označují, pokud je k dispozici pro uživatele AAD nebo MSA deklarace identity.  

**Tabulka 2: Sada standardní volitelné deklarací identity**

| Název                     | Popis                                                                                                                                                                                     | Typ tokenu | Typ uživatele | Poznámky                                                                                                                                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `auth_time`                | Čas při poslední ověřeného uživatele.  Specifikace OpenID Connect najdete v tématu.                                                                                                                                | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `tenant_region_scope`      | Oblast prostředků klienta                                                                                                                                                                   | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `signin_state`             | Přihlaste se deklarace identity stavu                                                                                                                                                                             | JWT        |           | 6 návratové hodnoty, jako příznaky:<br> "dvc_mngd": zařízení je spravované<br> "dvc_cmp": zařízení je kompatibilní s<br> "dvc_dmjd": zařízení je připojené k doméně<br> "dvc_mngd_app": zařízení je spravované přes správu mobilních zařízení<br> "inknownntwk": zařízení je uvnitř známé sítě.<br> "kmsi": zachování mi přihlášené byl použit. <br> |
| `controls`                 | Více hodnot deklarace identity, který obsahuje ovládací prvky relace vynucuje zásady podmíněného přístupu.                                                                                                       | JWT        |           | 3 hodnoty:<br> "app_res": aplikace potřebuje k vynucení podrobnější omezení. <br> "ca_enf": byla odložena vynucení podmíněného přístupu a je stále vyžadují. <br> "no_cookie": Tento token je nedostatečná pro exchange pro soubor cookie v prohlížeči. <br>                              |
| `home_oid`                 | Pro uživatele typu Host, ID objektu uživatele v klientovi domácí uživatele.                                                                                                                           | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `sid`                      | ID relace, použité pro odhlášení relace uživatele.                                                                                                                                                  | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `platf`                    | Platforma zařízení                                                                                                                                                                                 | JWT        |           | Omezení pro spravovaná zařízení, které můžete ověřit typu zařízení.                                                                                                                                                                                                                              |
| `verified_primary_email`   | Jako zdroj PrimaryAuthoritativeEmail uživatele                                                                                                                                               | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `verified_secondary_email` | Jako zdroj SecondaryAuthoritativeEmail uživatele                                                                                                                                             | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `enfpolids`                | ID vynucenou zásadu. Seznam zásad ID, která byla vyhodnocena pro aktuálního uživatele.                                                                                                         | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `vnet`                     | Informace o specifikátor virtuální sítě.                                                                                                                                                                     | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `fwd`                      | IP adresa.  Přidá původní adresu IPv4 klienta, který (uvnitř virtuální sítě)                                                                                                       | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `ctry`                     | Země uživatele                                                                                                                                                                                  | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `tenant_ctry`              | Země prostředků klienta                                                                                                                                                                       | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `acct`    | Stav účtu uživatele v klientovi.  Pokud je uživatel členem skupiny klienta, je hodnota `0`.  Pokud jsou hosta, hodnota je `1`.  | JWT, SAML | | |
| `upn`                      | UserPrincipalName deklarace identity.  I když tento požadavek je automaticky zahrnuty, můžete je zadat jako deklaraci identity volitelné připojit další vlastnosti, jimiž upravíte jeho chování v případě, že uživatel guest. | JWT, SAML  |           | Další vlastnosti: <br> `include_externally_authenticated_upn` <br> `include_externally_authenticated_upn_without_hash`                                                                                                                                                                 |

### <a name="v20-optional-claims"></a>Volitelné deklarace identity v2.0
Tyto deklarace identity jsou vždy součástí tokeny verze 1.0, ale jsou odebrány z v2.0 tokenů, pokud požadovaný.  Tyto deklarace platí pouze pro tokeny Jwt (ID tokeny a přístupové tokeny).  

**Tabulka 3: Pouze V2.0 volitelné deklarace identity**

| Deklarace identity JWT     | Název                            | Popis                                                                                                                    | Poznámky |
|---------------|---------------------------------|--------------------------------------------------------------------------------------------------------------------------------|-------|
| `ipaddr`      | IP adresa                      | IP adresa klienta se přihlásili.                                                                                      |       |
| `onprem_sid`  | Identifikátor zabezpečení na místě |                                                                                                                                |       |
| `pwd_exp`     | Čas vypršení platnosti hesla        | Datum a čas, kdy vyprší platnost hesla.                                                                                    |       |
| `pwd_url`     | Adresy URL pro změnu hesla             | Adresu URL, která uživatel navštívit změnit své heslo.                                                                        |       |
| `in_corp`     | Uvnitř podnikové sítě        | Signály, pokud je klient přihlášení z podnikové sítě. Pokud tomu tak není, deklarace identity není zahrnutý                     |       |
| `nickname`    | Přezdívka                        | Další jméno pro uživatele, nezávisle na jméno nebo příjmení.                                                             |       |                                                                                                                |       |
| `family_name` | Příjmení                       | Jak jsou definovány v objektu uživatele Azure AD, poskytuje poslední jméno, příjmení nebo příjmení uživatele. <br>"family_name": "Lukeš" |       |
| `given_name`  | Jméno                      | Poskytuje první nebo "zadány" jméno uživatele, nastavené na objekt uživatele Azure AD.<br>"given_name": "Jan"                   |       |

### <a name="additional-properties-of-optional-claims"></a>Další vlastnosti volitelné deklarací identity

Chcete-li změnit způsob, jakým se vrátí deklarace identity se dá nakonfigurovat některé volitelné deklarací identity.  Tyto další vlastnosti se nejčastěji používá ke migrace místní aplikace s odlišnými daty očekávání (například `include_externally_authenticated_upn_without_hash` pomáhá s klienty, kteří nemůže zpracovat hashmarks (`#`) v hlavní název uživatele)

**Tabulka 4: Hodnoty pro konfiguraci standardní volitelné deklarace identity**

| Název vlastnosti                                     | Další název vlastnosti                                                                                                             | Popis |
|---------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-------------|
| `upn`                                                 |                                                                                                                                      |             |
| | `include_externally_authenticated_upn`              | Zahrnuje Host UPN, jak je uložen v klientovi prostředků.  Například `foo_hometenant.com#EXT#@resourcetenant.com`.                            |             
| | `include_externally_authenticated_upn_without_hash` | Stejné jako výše, s výjimkou, který hashmarks (`#`) jsou nahrazeny podtržítka (`_`), například `foo_hometenant.com_EXT_@resourcetenant.com` |             

> [!Note]
>Určení, že volitelné deklarace bez další vlastnosti nezmění žádné chování – chcete-li zobrazit novou deklaraci identity vystavené v tokenu, alespoň jeden další vlastnosti musí být přidán. 


#### <a name="additional-properties-example"></a>Příklad další vlastnosti:

```json
 "optionalClaims": 
   {
       "idToken": [ 
             { 
                "name": "upn", 
            "essential": false,
                "additionalProperties": [ "include_externally_authenticated_upn"]  
              }
        ]
}
```

Tento objekt OptionalClaims způsobí, že ID token vrácen do klienta zahrnout další upn s další domácí klienta a informace o prostředku klienta.  

## <a name="configuring-optional-claims"></a>Konfigurace volitelné deklarace identity

Volitelné deklarace pro vaši aplikaci můžete nakonfigurovat úpravou manifest aplikace (viz následující příklad). Další informace najdete v tématu [pochopení článek manifestu aplikace Azure AD](active-directory-application-manifest.md).

**Ukázka schématu:**

```json
"optionalClaims":  
   {
       "idToken": [
             { 
                   "name": "upn", 
                   "essential": false, 
                   "additionalProperties": [ "include_externally_authenticated_upn"]  
              }
        ],
 "accessToken": [ 
             {
                    "name": "auth_time", 
                    "essential": false
              }
        ],
"saml2Token": [ 
              { 
                    "name": "upn", 
                    "essential": true
               },
               { 
                    "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                    "source": "user", 
                    "essential": true
               }
       ]
   }
```

### <a name="optionalclaims-type"></a>Typ OptionalClaims

Deklaruje volitelné deklarace požadovanou aplikaci. Aplikace můžete nakonfigurovat volitelné deklarací identity má být vrácen v každé tři typy tokenů (ID tokenu, přístupový token, SAML 2 token), že může přijímat od služby tokenů zabezpečení. Aplikace můžete nakonfigurovat jiné sady volitelné deklarace identity, vrátí se každý typ tokenu. Vlastnost OptionalClaims entity aplikace je objekt OptionalClaims.

**Tabulka 5: Vlastnosti typu OptionalClaims**

| Název        | Typ                       | Popis                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Kolekce (OptionalClaim) | Volitelné deklarace identity, vrátí se v tokenu JWT ID.     |
| `accessToken` | Kolekce (OptionalClaim) | Volitelné deklarace identity vrátí přístupový token JWT. |
| `saml2Token`  | Kolekce (OptionalClaim) | Volitelné deklarace identity, vrátí se v tokenu SAML.       |


### <a name="optionalclaim-type"></a>Typ OptionalClaim

Obsahuje deklaraci volitelné přidružený k aplikaci nebo hlavní název služby. Vlastnosti idToken, accessToken a saml2Token [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) typem je kolekce OptionalClaim.
Pokud podporovaná konkrétní deklaraci identity, můžete také upravit chování OptionalClaim pomocí možnost AdditionalProperties pole.

**Tabulka 6: Vlastnosti typu OptionalClaim**

| Název                 | Typ                    | Popis                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | Název volitelné deklarace identity.                                                                                                                                                                                                                                                                               |
| `source`               | Edm.String              | Zdroj (directory object) deklarace identity. Existují předdefinované deklarace identity a uživatelem definované z vlastnosti rozšíření. Pokud je zdroj hodnota null, deklarace identity je předdefinované volitelné deklarace identity. Pokud je hodnota zdroj uživatele, vlastnost name hodnotu vlastnosti rozšíření z objektu uživatele. |
| `essential`            | Edm.Boolean             | Pokud je hodnota true, je nutné zajistit prostředí hladký autorizace pro konkrétní úlohu požadovaný koncový uživatel deklarace zadaná klientem. Výchozí hodnota je false.                                                                                                                 |
| `additionalProperties` | Kolekce (Edm.String) | Další vlastnosti deklarace identity. Pokud v této kolekci existuje vlastnost, mění chování volitelné deklarace zadaná ve vlastnosti name.                                                                                                                                                   |

## <a name="configuring-custom-claims-via-directory-extensions"></a>Konfigurace vlastní deklarace prostřednictvím rozšíření adresáře

Kromě sadu standardní volitelné deklarace identity, tokeny je také možné nakonfigurovat zahrnout rozšíření schématu adresáře (najdete v článku [článku rozšíření schématu adresáře](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) informace).  Tato funkce je užitečná pro připojení další uživatelské informace, které můžete použít aplikaci – například, další identifikátor nebo důležité konfigurační možnost nastavený uživatelem. 

> [!Note]
> Rozšíření schématu adresáře jsou jen AAD funkce, takže pokud manifestu požadavků vaší aplikace do vlastního rozšíření a uživatelé MSA protokolu do vaší aplikace, nejsou k dispozici tato rozšíření. 

### <a name="values-for-configuring-additional-optional-claims"></a>Hodnoty pro konfiguraci další volitelné deklarace identity 

Pro rozšíření atributy, použijte úplný název tohoto rozšíření (ve formátu: `extension_<appid>_<attributename>`) v manifestu aplikace. `<appid>` Musí shodovat s id aplikace požaduje deklarace identity. 

V rámci JWT, budou tyto deklarace vygenerované v následujícím formátu název: `extn.<attributename>`.

V rámci tokeny SAML bude se tyto deklarace vygenerované v následujícím formátu identifikátoru URI: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="optional-claims-example"></a>Příklad volitelné deklarace identity

V této části si můžete projít scénáři chcete zobrazit, jak můžete použít funkci volitelné deklarace pro vaši aplikaci.
K dispozici pro aktualizace vlastností konfigurace identity aplikace povolit a nakonfigurovat volitelné deklarace identity jsou několik možností:
-   Můžete upravit manifest aplikace. Tento příklad použije tuto metodu provést konfiguraci. Pro čtení [pochopení dokument s manifestem aplikací Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) první Úvod k manifestu.
-   Je také možné zapsat aplikaci, která používá [rozhraní Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) k aktualizaci aplikace. [Entity a komplexní typ odkaz](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) v referenci rozhraní Graph API vám Průvodce může pomoci s konfigurací volitelné deklarace identity.

**Příklad:** v následujícím příkladu upravíte manifest aplikace k přidávání deklarací identit a přístupu, ID SAML tokeny, které jsou určené pro aplikace.
1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2.  Po došlo k ověření, vyberte vyberete v pravém horním rohu stránky klientovi Azure AD.
3.  Vyberte **rozšíření Azure AD** z levého navigačního panelu a klikněte na **registrace aplikace**.
4.  Najít aplikaci, kterou chcete nakonfigurovat volitelné deklarace identity v seznamu a klikněte na něj.
5.  Na stránce aplikace klikněte na tlačítko **Manifest** otevřete editor pro vložené manifestu. 
6.  Manifest pomocí tohoto editoru lze přímo upravit. Manifest dodržuje schéma pro [aplikace entity](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)a automaticky formátů jednou uložit manifest. Přidá nový elemets `OptionalClaims` vlastnost.

      ```json
      "optionalClaims": 
      {
            "idToken": [ 
                  { 
                        "name": "upn", 
                        "essential": false, 
                        "additionalProperties": [ "include_externally_authenticated_upn"]  
                  }
            ],
      "accessToken": [ 
                  {
                        "name": "auth_time", 
                        "essential": false
                  }
            ],
      "saml2Token": [ 
                  { 
                        "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                        "source": "user", 
                        "essential": true
                  }
            ]
      }
      ```
      V takovém případě různé volitelné deklarace byly přidány do každého typu token, který může přijímat aplikace. Tokeny ID bude teď obsahovat jméno UPN pro federované uživatele v úplné formě (`<upn>_<homedomain>#EXT#@<resourcedomain>`). Přístupové tokeny se nyní zobrazí auth_time deklarace identity. Tokeny SAML bude teď obsahovat rozšíření schématu adresáře skypeId (v tomto příkladu je ab603c56068041afb2f6832e2a17e237 ID aplikace pro tuto aplikaci).  Tokeny SAML zveřejní Skype ID jako `extension_skypeId`.

7.  Po dokončení aktualizace manifest, klikněte na tlačítko **Uložit** pro uložení manifestu


## <a name="related-content"></a>Související obsah
* Další informace o [standardní deklarace](active-directory-token-and-claims.md) poskytované Azure AD. 
