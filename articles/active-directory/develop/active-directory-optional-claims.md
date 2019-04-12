---
title: Zjistěte, jak poskytnout nepovinných deklarací identity do aplikace Azure AD | Dokumentace Microsoftu
description: Průvodci pro přidávání vlastních nebo další deklarace identity SAML 2.0 a webové tokeny JSON (JWT) tokeny vystavené službou Azure Active Directory.
documentationcenter: na
author: CelesteDG
services: active-directory
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/27/2019
ms.author: celested
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 253a5e247dbbea5fc7e0e556d8619328b43bff58
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2019
ms.locfileid: "59501055"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Postup: Zadejte nepovinných deklarací identity do aplikace Azure AD

Tato funkce slouží vývojáři aplikace k určení, které deklarace identity, která je v tokenech odesílaných do své aplikace. Můžete použít nepovinných deklarací identity do:

- Vyberte další deklarace identity mají být zahrnuty tokeny pro vaši aplikaci.
- Změňte chování určitých deklarací identity, které Azure AD se vrátí do tokenů.
- Přidat a přístup k vlastní deklarace identity pro vaši aplikaci.

Seznam standardních deklarace identity, najdete v článku [přístupový token](access-tokens.md) a [id_token](id-tokens.md) deklarací dokumentaci. 

I když nepovinných deklarací identity jsou podporované v jak v1.0 a v2.0 formátu tokeny, tak i tokeny SAML, poskytují většinu jejich hodnoty při přesunu z verze 1.0 na verze 2.0. Jedním z cílů systému [koncového bodu Azure AD v2.0](active-directory-appmodel-v2-overview.md) je menší velikost tokenu zajistit optimální výkon klienty. V důsledku toho několik deklarace identity dříve součástí přístup a tokeny typu ID už nejsou k dispozici v tokenech v2.0 a musíte požádat konkrétně na základě jednotlivých aplikací.

**Tabulka 1: Použitelnost.**

| Typ účtu | Tokeny V1.0 | Tokeny v2.0  |
|--------------|---------------|----------------|
| Osobní účet Microsoft  | neuvedeno  | Podporováno|
| Účet Azure AD      | Podporováno | Podporováno |

## <a name="v10-and-v20-optional-claims-set"></a>Nastaví V1.0 a V2.0 nepovinných deklarací identity

Sada nepovinných deklarací identity ve výchozím nastavení dostupné pro použití aplikacemi jsou uvedeny níže. Chcete-li přidat vlastní nepovinných deklarací identity pro vaši aplikaci, najdete v článku [rozšíření adresáře](#configuring-directory-extension-optional-claims)níže. Při přidávání deklarace identity **přístupový token**, tato změna se projeví na přístupové tokeny požadovaný *pro* aplikace (webového rozhraní API), ne ty *podle* aplikace. Tím se zajistí, že bez ohledu na to klientovi přístup k rozhraní API, jsou k dispozici v tokenu přístupu, které používají k ověřování na základě vašeho rozhraní API správná data.

> [!NOTE]
> Většina těchto deklarací mohou být součástí tokeny Jwt pro v1.0 a v2.0 tokeny, ale ne tokeny SAML, s výjimkou uvedeno ve sloupci Typ tokenu. Účty zákazníků podporují podmnožinu tyto deklarace označené ve sloupci "Typ uživatele".  Mnohé z deklarací uvedené se nevztahují na spotřebitelské uživatele (takže nemají žádný klient `tenant_ctry` nemá žádnou hodnotu).  

**Tabulka 2: V1.0 a V2.0 volitelné množině deklarací identity**

| Název                       |  Popis   | Typ tokenu | Typ uživatele | Poznámky  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Čas, kdy naposledy ověření uživatele. Specifikace OpenID Connect najdete v tématu.| JWT        |           |  |
| `tenant_region_scope`      | Oblast prostředku tenanta | JWT        |           | |
| `home_oid`                 | Pro uživatele typu Host, ID objektu uživatele v domovském tenantovi uživatele.| JWT        |           | |
| `sid`                      | ID relace používané pro každou relaci uživatele Odhlásit se. | JWT        |           |         |
| `platf`                    | Platforma zařízení    | JWT        |           | Omezeno na spravovaná zařízení, které můžete ověřit typ zařízení.|
| `verified_primary_email`   | Zdrojem je PrimaryAuthoritativeEmail uživatele      | JWT        |           |         |
| `verified_secondary_email` | Zdrojem je SecondaryAuthoritativeEmail uživatele   | JWT        |           |        |
| `enfpolids`                | ID zásady. Seznam zásad ID, která byla vyhodnocena pro aktuálního uživatele. | JWT |  |  |
| `vnet`                     | Specifikátor informace o virtuální síti. | JWT        |           |      |
| `fwd`                      | IP adresa.| JWT    |   | Přidá původní adresa IPv4 z klienta (uvnitř virtuální sítě) |
| `ctry`                     | Zemi uživatele | JWT |  | Azure AD vrací `ctry` nepovinné deklarace identity, pokud je přítomen a hodnota deklarace identity je kód země standardní dvou písmen, jako je například FR, JP, SZ a tak dále. |
| `tenant_ctry`              | Země prostředku tenanta | JWT | | |
| `xms_pdl`          | Upřednostňované umístění dat   | JWT | | U klientů geografickým oblastem jde 3písmenný kód, který ukazuje zeměpisnou oblast, ve kterém uživatel nachází. Další informace najdete v tématu [dokumentace ke službě Azure AD Connect o upřednostňované umístění dat](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Příklad: `APC` pro Asie a Tichomoří. |
| `xms_pl`                   | Uživatel upřednostňovaný jazyk  | JWT ||Uživatel upřednostňovaného jazyka, pokud se nastavení. Zdrojem je jejich domovském tenantovi ve scénářích přístup hosta. Všechny kopie ve formátu ("en-us"). |
| `xms_tpl`                  | Tenant upřednostňovaný jazyk| JWT | | Prostředků tenanta upřednostňovaného jazyka, pokud se nastavení. Formátovaný LL ("en"). |
| `ztdid`                    | Automatizované ID nasazení | JWT | | Identita zařízení používaná pro [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | Adresovatelný e-mailu pro tohoto uživatele, pokud má jeden uživatel.  | JWT, SAML | MSA, AAD | Tato hodnota je zahrnuta ve výchozím nastavení, pokud je uživatel typu Host v tenantovi.  Pro spravované uživatele (ty uvnitř tenanta) se musí být požadován prostřednictvím této volitelné deklarace identity, nebo na pouze, verze 2.0 s rozsahem OpenID.  Pro spravované uživatele, e-mailová adresa musí být nastavena v [portálu pro správu Office](https://portal.office.com/adminportal/home#/users).|  
| `acct`             | Stav účtu uživatele v tenantovi. | JWT, SAML | | Pokud je uživatel členem tenanta, hodnota je `0`. Pokud jsou hosta, hodnota je `1`. |
| `upn`                      | Deklarace identity UserPrincipalName. | JWT, SAML  |           | I když tato deklarace identity je automaticky přidána, můžete je zadat jako volitelnou deklaraci připojit další vlastnosti, změnit její chování v případě uživatelů typu Host.  |

### <a name="v20-optional-claims"></a>Verze 2.0 nepovinných deklarací identity

Tyto deklarace jsou vždy součástí v1.0 tokenů Azure AD, ale není součástí tokeny v2.0, pokud požadovaný. Tyto deklarace platí pouze pro tokeny Jwt (tokeny typu ID a přístupové tokeny). 

**Tabulka 3: Pouze pro verze 2.0 nepovinných deklarací identity**

| JWT Claim     | Název                            | Popis                                | Poznámky |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP adresa                      | IP adresa přihlášení z klienta.   |       |
| `onprem_sid`  | Místní identifikátor zabezpečení |                                             |       |
| `pwd_exp`     | Čas vypršení platnosti hesla        | Datetime, kdy vyprší platnost hesla. |       |
| `pwd_url`     | Adresy URL pro změnu hesla             | Adresa URL, které uživatel může navštěvovat ke změně hesla.   |   |
| `in_corp`     | Inside Corporate Network        | Signály, pokud je klient přihlašování z podnikové sítě. Pokud ne, není zahrnut deklarace identity.   |  Na základě odhlásit z [důvěryhodné IP adresy](../authentication/howto-mfa-mfasettings.md#trusted-ips) nastavení vícefaktorového ověřování.    |
| `nickname`    | Přezdívka                        | Další jméno pro uživatele, nezávisle na první nebo poslední název. | 
| `family_name` | Příjmení                       | Poskytuje poslední jméno, příjmení nebo příjmení uživatele, jak jsou definovány v objektu user. <br>"family_name": "Lukeš" | Podporované v MSA a AAD   |
| `given_name`  | Jméno                      | Nabízí první nebo "zadány" jméno uživatele, jak v objektu user.<br>"given_name": "Frank"                   | Podporované v MSA a AAD  |
| `upn`         | Hlavní název uživatele | Identifikátor pro uživatele, který lze použít s parametrem username_hint.  Trvalý identifikátor pro uživatele a neměl by se data klíče. | Zobrazit [další vlastnosti](#additional-properties-of-optional-claims) níže pro konfiguraci deklarace identity. |
| `sid`         | ID relace                      | Identifikátor GUID relace použité pro sledování relace ověřování s MSA. | Pouze MSA.  Nebudou zahrnuty pro účty Azure AD. | 


### <a name="additional-properties-of-optional-claims"></a>Další vlastnosti nepovinných deklarací identity

Chcete-li změnit způsob, jakým se vrátí deklarace identity je možné nakonfigurovat některé nepovinných deklarací identity. Tyto další vlastnosti většinou slouží k migraci místních aplikací s různými daty očekávání (například `include_externally_authenticated_upn_without_hash` pomáhá s klienty, kteří nemůže zpracovat znaky hash (`#`) v hlavní název uživatele)

**Tabulka 4: Hodnoty pro konfiguraci nepovinných deklarací identity**

| Název vlastnosti  | Další název vlastnosti | Popis |
|----------------|--------------------------|-------------|
| `upn`          |                          | Lze použít pro odpovědi SAML a tokenů JWT a v1.0 a v2.0 tokeny. |
|                | `include_externally_authenticated_upn`  | Zahrnuje hosta hlavní název uživatele uložené v tenantovi prostředků. Například: `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | Stejné jako výše, s tím rozdílem, že označí-the-hash (`#`) jsou nahrazeny podtržítka (`_`), například `foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>Příklad další vlastnosti

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

Tento objekt OptionalClaims způsobí, že token ID, který je vrácen do klienta, které chcete zahrnout další hlavní název uživatele s další domovském tenantovi a informace o prostředku tenanta. Tím se změní pouze `upn` deklarací identity v tokenu, pokud je uživatel typu Host v tenantovi (využívající různých zprostředkovatelů identity pro ověřování). 

## <a name="configuring-optional-claims"></a>Konfigurace nepovinných deklarací identity

Nepovinných deklarací identity pro vaši aplikaci můžete nakonfigurovat tak, že upravíte manifest aplikace (viz následující příklad). Další informace najdete v tématu [pochopení článku manifestu aplikace Azure AD](reference-app-manifest.md).

**Ukázka schéma:**

```json
"optionalClaims":  
   {
       "idToken": [
             { 
                   "name": "auth_time", 
                   "essential": false
              }
        ],
 "accessToken": [ 
             {
                    "name": "ipaddr", 
                    "essential": false
              }
        ],
"saml2Token": [ 
              { 
                    "name": "upn", 
                    "essential": false
               },
               { 
                    "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                    "source": "user", 
                    "essential": false
               }
       ]
   }
```

### <a name="optionalclaims-type"></a>Typ OptionalClaims

Deklaruje nepovinných deklarací identity požadovaný aplikací. Aplikace můžete nakonfigurovat nepovinných deklarací identity má být vrácen v každé tři typy tokenů (ID tokenu, přístupový token, SAML 2 token), že může přijímat od služby tokenů zabezpečení. Aplikace můžete nakonfigurovat sadu nepovinných deklarací identity, který se má vrátit v každé typ tokenu. Vlastnost OptionalClaims entity aplikací je OptionalClaims objekt.

**Tabulka 5: Vlastnosti typu OptionalClaims**

| Název        | Typ                       | Popis                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Kolekce (OptionalClaim) | Nepovinné deklarace vrácené v tokenu JWT ID. |
| `accessToken` | Kolekce (OptionalClaim) | Nepovinné deklarace vrácené v přístupovém tokenu JWT. |
| `saml2Token`  | Kolekce (OptionalClaim) | Vrátí nepovinných deklarací identity v tokenu SAML.   |

### <a name="optionalclaim-type"></a>Typ OptionalClaim

Obsahuje volitelnou deklaraci přidružené k aplikaci nebo instančního objektu. Vlastnosti idToken accessToken a saml2Token [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) typem je kolekce OptionalClaim.
Pokud podporovaná konkrétní deklarace identity, můžete také upravit chování OptionalClaim pomocí AdditionalProperties pole.

**Tabulka 6: Vlastnosti typu OptionalClaim**

| Název                 | Typ                    | Popis                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | Název volitelnou deklaraci.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | Zdroj (objekt adresáře) deklarace identity. Jsou předdefinované deklarace identity a uživatelem definované vlastnosti rozšíření deklarací identity. Pokud zdrojová hodnota je null, deklarace identity je předdefinovaný volitelnou deklaraci. Pokud zdrojová hodnota je uživatel, je hodnota vlastnosti název vlastnosti rozšíření v objektu user. |
| `essential`            | Edm.Boolean             | Pokud je hodnota true, je nezbytné k zajištění hladké autorizace pro konkrétní úlohu požadoval uživatel deklarace identity určená klientem. Výchozí hodnota je false.                                                                                                             |
| `additionalProperties` | Kolekce (Edm.String) | Další vlastnosti deklarace identity. Jestliže některá vlastnost existuje v této kolekci, upravuje chování volitelnou deklaraci podle vlastnosti name.                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>Konfigurace rozšíření adresáře nepovinných deklarací identity

Kromě sady standardních nepovinných deklarací identity můžete také nakonfigurovat tokeny patří rozšíření schématu adresáře. Další informace najdete v tématu [rozšíření schématu adresáře](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions). Tato funkce je užitečná pro připojení dalších informací o uživatelích, které vaše aplikace může používat – například další identifikátor nebo důležité konfigurační možnost, která nastavil uživatel. 

> [!Note]
> Rozšíření schématu adresáře jsou AAD – pouze funkce, takže pokud manifestu požadavků vaší aplikace do vaší aplikace přihlásí vlastního rozšíření a uživatele MSA, nejsou k dispozici tato rozšíření. 

### <a name="directory-extension-formatting"></a>Rozšíření adresáře formátování

Atributy rozšíření, použijte úplný název rozšíření (ve formátu: `extension_<appid>_<attributename>`) v manifestu aplikace. `<appid>` Musí odpovídat ID žádost o deklaraci identity. 

V rámci tokenů JWT, budou tyto deklarace vygenerován v následujícím formátu názvu: `extn.<attributename>`.

V rámci tokenů SAML se měl vyzařovaného tyto deklarace identity v následujícím formátu identifikátoru URI: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="optional-claims-example"></a>Příklad nepovinných deklarací identity

V této části si projdete scénáři chcete zobrazit, jak můžete použít funkci nepovinných deklarací identity pro vaši aplikaci.
Nejsou k dispozici pro aktualizaci vlastností konfigurace identity aplikace povolit a nakonfigurovat nepovinných deklarací identity několik možností:
-   Můžete upravit manifest aplikace. Následující příklad použije tuto metodu provést konfiguraci. Přečtěte si [Principy dokumentu manifestu aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) první s úvodem do manifestu.
-   Je také možné psát aplikace, která se používá [rozhraní Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) k aktualizaci aplikace. [Entity a komplexní typ odkazu](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) v Reference k rozhraní Graph API příručka vám pomůže s konfigurací nepovinných deklarací identity.

**Příklad:** V následujícím příkladu se upravte manifest aplikace k přidávání deklarací identit SAML, přístup a ID tokeny, které jsou určeny pro aplikaci.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Po ověření, vyberete v pravém horním rohu stránky zvolte tenanta Azure AD.
1. Vyberte **registrace aplikací** z levé straně.
1. Vyhledejte aplikaci, kterou chcete nakonfigurovat volitelné deklarací identity v seznamu a klikněte na něj.
1. Na stránce aplikace klikněte na tlačítko **Manifest** otevřete editor manifestu vložené. 
1. Můžete přímo upravit pomocí editoru manifestu. Následuje schéma pro manifest [aplikace entity](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)a automatické formáty – manifest jednou uložili. Přibude nové prvky `OptionalClaims` vlastnost.

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
      V takovém případě různých nepovinných deklarací identity byly přidány do každého typu token, který aplikace může přijímat. Tokeny ID bude teď obsahovat jméno UPN u federovaných uživatelů v úplné formě (`<upn>_<homedomain>#EXT#@<resourcedomain>`). Přístupové tokeny, které ostatní klienti požadují pro tuto aplikaci teď bude zahrnovat auth_time deklarace identity. Tokeny SAML bude teď obsahovat rozšíření schématu skypeId adresář (v tomto příkladu je ab603c56068041afb2f6832e2a17e237 ID aplikace pro tuto aplikaci). Tokeny SAML bude vystavovat Skypové jako `extension_skypeId`.

1. Jakmile budete hotovi, aktualizuje se manifest, klikněte na tlačítko **Uložit** pro uložení manifestu

## <a name="next-steps"></a>Další postup

Další informace o standardní deklarace identity, poskytuje Azure AD.

- [Tokeny ID](id-tokens.md)
- [Přístupové tokeny](access-tokens.md)
