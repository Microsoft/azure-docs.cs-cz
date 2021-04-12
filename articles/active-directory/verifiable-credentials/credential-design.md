---
title: Postup přizpůsobení Azure Active Directory ověřitelných přihlašovacích údajů (Preview)
description: V tomto článku se dozvíte, jak vytvořit vlastní ověřitelné přihlašovací údaje.
services: active-directory
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: a43e734c0a5bfa7c3698dcde5cb5b17f15575d90
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222924"
---
# <a name="how-to-customize-your-verifiable-credentials-preview"></a>Jak přizpůsobit ověřitelné přihlašovací údaje (Preview)

Ověřitelné přihlašovací údaje se skládají ze dvou komponent, pravidel a zobrazovaných souborů. Soubor pravidel určuje, co uživatel musí zadat předtím, než obdrží ověřitelné přihlašovací údaje. Zobrazovaný soubor řídí branding přihlašovacích údajů a stylů deklarací identity. V tomto průvodci vysvětlíme, jak upravit oba soubory tak, aby splňovaly požadavky vaší organizace. 

> [!IMPORTANT]
> Azure Active Directory ověřitelných přihlašovacích údajů je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="rules-file-requirements-from-the-user"></a>Soubor pravidel: požadavky od uživatele

Soubor pravidel je jednoduchý soubor JSON, který popisuje důležité vlastnosti ověřitelných přihlašovacích údajů. Konkrétně popisuje, jak se používají deklarace identity k naplnění ověřitelných přihlašovacích údajů.

V současné době existují tři vstupní typy, které jsou k dispozici pro konfiguraci v souboru pravidel. Tyto typy používá ověřitelná služba pro vystavování přihlašovacích údajů, která umožňuje vkládat deklarace identity do ověřitelných přihlašovacích údajů a potvrzovat je pro tyto informace s vaší službou. Níže jsou uvedené tři typy s vysvětlením.

- Token ID
- Ověřitelné přihlašovací údaje prostřednictvím ověřitelné prezentace.
- Self-Attested deklarace identity

**Token ID:** Ukázková aplikace a kurz používají token ID. Pokud je tato možnost nakonfigurovaná, budete muset zadat identifikátor URI pro otevření konfigurace připojení a zahrnout deklarace, které by měly být zahrnuté do VC. Uživatel bude v ověřovací aplikaci vyzván k přihlášení, aby splnil tento požadavek a přidal přidružené deklarace identity z jejich účtu. 

**Ověřitelné přihlašovací údaje:** Konečným výsledkem toku vystavování je vytvoření ověřitelných přihlašovacích údajů, ale také můžete požádat uživatele, aby k jeho vydání mohli ověřit pověření. Soubor pravidel je schopný přijmout konkrétní deklarace identity z předložených ověřitelných přihlašovacích údajů a zahrnout tyto deklarace do nově vydaných ověřitelných přihlašovacích údajů z vaší organizace. 

**Deklarace identity samy na sebe:** Pokud je vybrána tato možnost, bude moci uživatel přímo zadat informace do ověřovatele. V tuto chvíli jsou řetězce jediným podporovaným vstupem pro deklarace identity s sebou samým. 

![Podrobné zobrazení ověřovací karty s přihlašovacími údaji](media/credential-design/issuance-doc.png) 

**Statické deklarace identity:** Dále je možné deklarovat statickou deklaraci identity v souboru pravidel, ale tento vstup nepochází od uživatele. Vystavitel definuje statickou deklaraci identity v souboru pravidel a v rámci ověřitelných přihlašovacích údajů by vypadal jako jakákoli jiná deklarace identity. Jednoduše přidejte credentialSubject po VC. Zadejte a deklarujte atribut a deklaraci identity. 

```json
"vc": {
    "type": [ "StaticClaimCredential" ],
    "credentialSubject": {
      "staticClaim": true,
      "anotherClaim": "Your Claim Here"
    },
  }
}
```


## <a name="input-type-id-token"></a>Typ vstupu: token ID

Pro získání tokenu ID jako vstupu musí soubor pravidel nakonfigurovat známý koncový bod OIDC kompatibilního systému identit. V tomto systému je potřeba zaregistrovat aplikaci se správnými informacemi z [příkladů komunikace služby vystavitele](issuer-openid.md). Kromě toho musí být client_id umístit do souboru pravidel a parametr scope musí být vyplněn se správnými obory. Azure Active Directory například potřebuje obor e-mailu, pokud chcete vrátit deklaraci e-mailu v tokenu ID.
```json
    {
      "attestations": {
        "idTokens": [
          {
            "mapping": {
              "firstName": { "claim": "given_name" },
              "lastName": { "claim": "family_name" }
            },
            "configuration": "https://dIdPlayground.b2clogin.com/dIdPlayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
            "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
            "redirect_uri": "vcclient://openid/",
            "scope": "openid profile"
          }
        ]
      },
      "validityInterval": 2592000,
      "vc": {
        "type": ["https://schema.org/EducationalCredential", "https://schemas.ed.gov/universityDiploma2020", "https://schemas.contoso.edu/diploma2020" ]
      }
    }
```

| Vlastnost | Popis |
| -------- | ----------- |
| `attestations.idTokens` | Pole zprostředkovatelů identity OpenID Connect, kteří jsou podporováni pro informace o vlastnictví uživatele. |
| `...mapping` | Objekt, který popisuje, jakým způsobem jsou deklarace identity v jednotlivých tokenech mapovány na atributy ve výsledných ověřitelných přihlašovacích údajích. |
| `...mapping.{attribute-name}` | Atribut, který má být naplněn v výsledných ověřitelných přihlašovacích údajích. |
| `...mapping.{attribute-name}.claim` | Deklarace identity v tokenech ID, jejichž hodnota by měla být použita k naplnění atributu. |
| `...configuration` | Umístění dokumentu konfigurace vašeho zprostředkovatele identity. Tato adresa URL musí odpovídat [standardu OpenID Connect pro metadata zprostředkovatele identity](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata). Konfigurační dokument musí zahrnovat `issuer` `authorization_endpoint` pole,, a `token_endpoint` `jwks_uri` . |
| `...client_id` | ID klienta získané během procesu registrace klienta. |
| `...scope` | Seznam oborů oddělených mezerami, které IDP potřebuje k vrácení správných deklarací identity v tokenu ID. |
| `...redirect_uri` | Hodnota musí být vždy použita `vcclient://openid/` . |
| `validityInterval` | Časový interval v sekundách, který představuje dobu života ověřitelných přihlašovacích údajů. Po uplynutí tohoto časového období už ověřitelná pověření nebudou platná. Vynechání této hodnoty znamená, že každý ověřitelný přihlašovací údaj zůstane platný, dokud ho neodeberete explicitně. |
| `vc.type` | Pole řetězců, které označují schématu, která ověří vaše ověřitelná pověření. Další podrobnosti najdete v části níže. |

### <a name="vctype-choose-credential-types"></a>VC. Type: zvolit typ přihlašovacích údajů 

Všechny ověřitelné přihlašovací údaje musí v souboru pravidel deklarovat svůj typ. Typ přihlašovacích údajů rozlišuje vaše ověřitelné přihlašovací údaje od přihlašovacích údajů vydaných jinými organizacemi a zajišťuje interoperabilitu mezi vystaviteli a ověřovateli. Chcete-li označit typ přihlašovacích údajů, je nutné zadat jeden nebo více typů přihlašovacích údajů, které pověření splňuje. Každý typ je reprezentován jedinečným řetězcem – často se k zajištění globální jedinečnosti použije identifikátor URI. Identifikátor URI není nutné adresovat. je považována za řetězec. 

Například diplomové přihlašovací údaje vydané společností Contoso University mohou deklarovat následující typy:

| Typ | Účel |
| ---- | ------- |
| `https://schema.org/EducationalCredential` | Deklaruje, že diplomy vydané společností Contoso University obsahují atributy definované objektem Schema. org `EducationaCredential` . |
| `https://schemas.ed.gov/universityDiploma2020` | Deklaruje, že diplomy vydané společností Contoso University obsahují atributy definované USA ministerstvem vzdělávání. |
| `https://schemas.contoso.edu/diploma2020` | Deklaruje, že diplomy vydané společností Contoso University obsahují atributy definované společností Contoso University. |

Tím, že deklarujete všechny tři typy, mohou být diplomy společnosti Contoso University využívány k uspokojení různých požadavků ověřovatelů. Banka může požádat o sadu `EducationCredential` s od uživatele a diplom se dá použít k uspokojení žádosti. Asociace společnosti Contoso University absolventů ale může vyžádat přihlašovací údaje typu `https://schemas.contoso.edu/diploma2020` a diplom bude také splnit požadavek.

Aby se zajistila interoperabilita vašich přihlašovacích údajů, doporučujeme, abyste úzce spolupracovali se souvisejícími organizacemi a definovali jste typy přihlašovacích údajů, schémata a identifikátory URI pro použití ve vašem odvětví. Mnoho průmyslových institucí poskytuje pokyny ke struktuře oficiálních dokumentů, které je možné změnit pro účely definování obsahu ověřitelných přihlašovacích údajů. Měli byste také úzce spolupracovat s ověřovateli vašich přihlašovacích údajů, abyste pochopili, jak si chce vyžádat a využívat ověřitelné přihlašovací údaje.

## <a name="input-type-verifiable-credential"></a>Typ vstupu: ověřitelné přihlašovací údaje

>[!NOTE]
>Soubory pravidel, které žádají o ověřitelné pověření, nepoužívají pro vyžádání přihlašovacích údajů formát prezentace. Tato akce bude aktualizována, pokud Vystavovaná služba podporuje standardní manifest přihlašovacích údajů. 

```json
{
    "attestations": {
      "presentations": [
        {
          "mapping": {
            "first_name": {
              "claim": "$.vc.credentialSubject.firstName",
            },
            "last_name": {
              "claim": "$.vc.credentialSubject.lastName",
              "indexed": true
            }
          },
          "credentialType": "VerifiedCredentialNinja",
          "contracts": [
            "https://beta.did.msidentity.com/v1.0/3c32ed40-8a10-465b-8ba4-0b1e86882668/verifiableCredential/contracts/VerifiedCredentialNinja"
          ],
          "issuers": [
            {
              "iss": "did:ion:123"
            }
          ]
        }
      ]
    },
    "validityInterval": 25920000,
    "vc": {
      "type": [
        "ProofOfNinjaNinja"
      ],
    }
  }
  ```

| Vlastnost | Popis |
| -------- | ----------- |
| `attestations.presentations` | Pole ověřitelných přihlašovacích údajů, které se vyžadují jako vstupy. |
| `...mapping` | Objekt, který popisuje, jak jsou deklarace identity v jednotlivých předkládaných přihlašovacích údajích mapovány na atributy ve výsledných ověřitelných přihlašovacích údajích. |
| `...mapping.{attribute-name}` | Atribut, který má být naplněn v výsledných ověřitelných přihlašovacích údajích. |
| `...mapping.{attribute-name}.claim` | Deklarace identity v ověřitelných přihlašovacích údajích, jejichž hodnota by měla být použita k naplnění atributu. |
| `...mapping.{attribute-name}.indexed` | Pro jedno ověřitelné přihlašovací údaje se dá povolit jenom jeden, aby se uložil pro odvolat. Další informace najdete v [článku o odvolání přihlašovacích údajů](how-to-issuer-revoke.md) . |
| `credentialType` | CredentialType ověřitelných přihlašovacích údajů, které požadujete, aby byl uživatel přítomen. |
| `contracts` | Identifikátor URI kontraktu v portálu služby ověřitelných přihlašovacích údajů. |
| `issuers.iss` | Vystavitel byl pro ověřené přihlašovací údaje požádán o uživatele. |
| `validityInterval` | Časový interval v sekundách, který představuje dobu života ověřitelných přihlašovacích údajů. Po uplynutí tohoto časového období už ověřitelná pověření nebudou platná. Vynechání této hodnoty znamená, že každý ověřitelný přihlašovací údaj zůstane platný, dokud ho neodeberete explicitně. |
| `vc.type` | Pole řetězců, které označují schématu, která ověří vaše ověřitelná pověření. |


## <a name="input-type-self-attested-claims"></a>Typ vstupu: deklarace identity samy na sebe

Během toku vystavování se uživateli zobrazí výzva, aby zadali některé informace, které jsou v sobě ověřené. Od tohoto okamžiku je jediným vstupním typem řetězec. 
```json
{
  "attestations": {
    "selfIssued": {
      "mapping": {
        "alias": {
          "claim": "name"
        }
      },
    },
    "validityInterval": 25920000,
    "vc": {
      "type": [
        "ProofOfNinjaNinja"
      ],
    }
  }



```
| Vlastnost | Popis |
| -------- | ----------- |
| `attestations.selfIssued` | Pole deklarací vystavených svým držitelem, které vyžadují vstup od uživatele. |
| `...mapping` | Objekt, který popisuje, jak jsou deklarace vystavené svým držitelem mapovány na atributy ve výsledných ověřitelných přihlašovacích údajích. |
| `...mapping.alias` | Atribut, který má být naplněn v výsledných ověřitelných přihlašovacích údajích. |
| `...mapping.alias.claim` | Deklarace identity v ověřitelných přihlašovacích údajích, jejichž hodnota by měla být použita k naplnění atributu. |
| `validityInterval` | Časový interval v sekundách, který představuje dobu života ověřitelných přihlašovacích údajů. Po uplynutí tohoto časového období už ověřitelná pověření nebudou platná. Vynechání této hodnoty znamená, že každý ověřitelný přihlašovací údaj zůstane platný, dokud ho neodeberete explicitně. |
| `vc.type` | Pole řetězců, které označují schématu, která ověří vaše ověřitelná pověření. |


## <a name="display-file-verifiable-credentials-in-microsoft-authenticator"></a>Zobrazit soubor: ověřitelné přihlašovací údaje v Microsoft Authenticator

Ověřitelná pověření nabízejí omezené sady možností, které se dají použít k vyjádření vaší značky. Tento článek poskytuje pokyny pro přizpůsobení vašich přihlašovacích údajů a osvědčené postupy pro navrhování přihlašovacích údajů, které se vydávají uživatelům.

Ověřitelné přihlašovací údaje vydané uživatelům se zobrazí jako karty v Microsoft Authenticator. Jako správce můžete zvolit barvu, ikonu a textové řetězce karty, aby odpovídaly značce vaší organizace.

![dokumentace k vystavení](media/credential-design/detailed-view.png) 

Karty také obsahují přizpůsobitelná pole, která můžete použít, chcete-li uživatelům umožnit, aby znali účel karty, atributy, které obsahuje, a další.

## <a name="create-a-credential-display-file"></a>Vytvoření zobrazovaného souboru přihlašovacích údajů

Podobně jako u souboru pravidel je zobrazený soubor jednoduchý soubor JSON, který popisuje, jak by se měl obsah ověřitelných přihlašovacích údajů zobrazit v aplikaci Microsoft Authenticator. 

>[!NOTE]
> Tento model zobrazení se v tuto chvíli používá jenom Microsoft Authenticator.

Zobrazovací soubor má následující strukturu.

```json
{
    "default": {
      "locale": "en-US",
      "card": {
        "title": "University Graduate",
        "issuedBy": "Contoso University",
        "backgroundColor": "#212121",
        "textColor": "#FFFFFF",
        "logo": {
          "uri": "https://contoso.edu/images/logo.png",
          "description": "Contoso University Logo"
        },
        "description": "This digital diploma is issued to students and alumni of Contoso University."
      },
      "consent": {
        "title": "Do you want to get your digital diploma from Contoso U?",
        "instructions": "Please log in with your Contoso U account to receive your digital diploma."
      },
      "claims": {
        "vc.credentialSubject.name": {
          "type": "String",
          "label": "Name"
        }
      }
    }
}
```

| Vlastnost | Popis |
| -------- | ----------- |
| `locale` | Jazyk ověřitelných přihlašovacích údajů. Vyhrazeno pro budoucí použití. | 
| `card.title` | Zobrazí typ přihlašovacích údajů pro uživatele. Doporučená maximální délka je 25 znaků. | 
| `card.issuedBy` | Zobrazuje název vydávající organizace pro uživatele. Doporučená maximální délka 40 znaků. |
| `card.backgroundColor` | Určuje barvu pozadí karty ve formátu Hex. Na všech kartách se použije jemný přechod. |
| `card.textColor` | Určuje barvu textu karty v šestnáctkovém formátu. Doporučuje se použít černou nebo bílou. |
| `card.logo` | Logo, které se zobrazí na kartě. Poskytnutá adresa URL musí být veřejně adresovatelné. Doporučená maximální výška 36 px a maximální šířka 100 px bez ohledu na velikost telefonu. Doporučuje formát PNG s průhledným pozadím. | 
| `card.description` | Vedle každé karty se zobrazuje doplňující text. Dá se použít pro libovolný účel. Doporučená maximální délka 100 znaků. |
| `consent.title` | Doplňující text, který se zobrazí při vydání karty Slouží k zadání podrobností o procesu vystavení. Doporučená délka 100 znaků. |
| `consent.instructions` | Doplňující text, který se zobrazí při vydání karty Slouží k zadání podrobností o procesu vystavení. Doporučená délka 100 znaků. |
| `claims` | Umožňuje vám poskytnout popisky pro atributy zahrnuté v jednotlivých přihlašovacích údajích. |
| `claims.{attribute}` | Určuje atribut přihlašovacího údaje, na který se vztahuje popisek. |
| `claims.{attribute}.type` | Určuje typ atributu. Momentálně podporujeme jenom řetězec. |
| `claims.{attribute}.label` | Hodnota, která má být použita jako popisek pro atribut, který se zobrazí v ověřovateli. To může být jiné než popisek, který byl použit v souboru pravidel. Doporučená maximální délka 40 znaků. |

>[!NOTE]
  >Pokud je deklarace identity zahrnutá v souboru pravidel a pak se v zobrazeném souboru vynechá, existují dva různé typy prostředí. V systému iOS se deklarace identity nezobrazí v části Podrobnosti zobrazené na obrázku výše, zatímco na Androidu se zobrazí deklarace identity.  

## <a name="next-steps"></a>Další kroky

Teď máte lepší přehled o ověřitelných návrhech přihlašovacích údajů a o tom, jak můžete vytvořit vlastní, aby vyhovovaly vašim potřebám.

- [Příklady komunikace služby vystavitele](issuer-openid.md)
