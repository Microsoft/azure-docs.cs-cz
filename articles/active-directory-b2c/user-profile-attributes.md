---
title: Atributy profilu uživatele v Azure Active Directory B2C
description: Přečtěte si o atributech typu prostředku uživatele, které jsou podporovány profilem uživatele Azure AD B2C Directory. Přečtěte si o předdefinovaných atributech, rozšířeních a způsobu mapování atributů na Microsoft Graph.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7dfad71d05a882e3a3941a96e12489adb5fb3234
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102500525"
---
# <a name="user-profile-attributes"></a>Atributy profilu uživatele

Váš Azure Active Directory (Azure AD) profil uživatele adresáře B2C obsahuje předdefinovanou sadu atributů, jako je například křestní jméno, příjmení, město, PSČ a telefonní číslo. Profil uživatele můžete roztáhnout vlastními daty aplikace bez nutnosti externího úložiště dat. 

Většinu atributů, které lze použít s Azure AD B2C profily uživatelů, jsou podporovány také Microsoft Graph. Tento článek popisuje podporované Azure AD B2C atributy profilu uživatele. Také poznamenejte tyto atributy, které nejsou podporovány Microsoft Graph a také Microsoft Graph atributy, které by neměly být použity s Azure AD B2C.

> [!IMPORTANT]
> Nepoužívejte předdefinované ani rozšiřující atributy k ukládání citlivých osobních údajů, jako jsou například přihlašovací údaje k účtu, identifikační čísla státní správy, data držitele, údaje o finančním účtu, informace o zdravotním péči nebo citlivé informace na pozadí.

Můžete také integrovat s externími systémy. Můžete například použít Azure AD B2C pro ověřování, ale delegovat externí databázi Customer Relationship Management (CRM) nebo věrnostní databáze zákazníků jako autoritativní zdroj zákaznických dat. Další informace najdete v tématu řešení [vzdáleného profilu](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) .

## <a name="azure-ad-user-resource-type"></a>Typ prostředku uživatele Azure AD

Následující tabulka uvádí atributy [typu prostředku uživatele](/graph/api/resources/user) , které jsou podporovány profilem uživatele Azure AD B2C Directory. Poskytuje následující informace o jednotlivých atributech:

- Název atributu, který používá Azure AD B2C (za ním následuje Microsoft Graph název v závorkách, pokud se liší)
- Typ dat atributu
- Popis atributu
- Pokud je atribut k dispozici v Azure Portal
- Pokud se atribut dá použít v toku uživatele
- Pokud se atribut dá použít ve vlastní zásadě [Azure AD Technical Profile](active-directory-technical-profile.md) a v jaké části ( &lt; InputClaims &gt; , &lt; OutputClaims &gt; nebo &lt; PersistedClaims &gt; )

|Název     |Typ     |Description|portál Azure|Toky uživatele|Vlastní zásady|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Logická hodnota|Zda je uživatelský účet povolený nebo zakázaný: **true** , pokud je účet povolený, v opačném případě **false**.|Yes|No|Trvalý výstup|
|ageGroup        |Řetězec|Věková skupina uživatele. Možné hodnoty: null, undefined, nezletilý, dospělý, NotAdult.|Yes|No|Trvalý výstup|
|alternativeSecurityId ([identity](#identities-attribute))|Řetězec|Jediná identita uživatele od externího zprostředkovatele identity.|No|No|Vstup, trvalý výstup|
|alternativeSecurityIds ([identity](#identities-attribute))|alternativní kolekce securityId|Kolekce uživatelských identit od externích zprostředkovatelů identity.|No|No|Trvalý výstup|
|city            |Řetězec|Město, ve kterém se uživatel nachází Maximální délka 128.|Yes|Yes|Trvalý výstup|
|consentProvidedForMinor|Řetězec|Zda byl souhlas poskytnut pro méně závažná. Povolené hodnoty: null, uděleno, zamítnuto nebo notRequired.|Yes|No|Trvalý výstup|
|country         |Řetězec|Země nebo oblast, ve které se uživatel nachází. Příklad: "US" nebo "UK". Maximální délka 128.|Yes|Yes|Trvalý výstup|
|createdDateTime|DateTime|Datum vytvoření objektu uživatele. Jen pro čtení.|No|No|Trvalý výstup|
|creationType    |Řetězec|Pokud byl uživatelský účet vytvořen jako místní účet pro klienta Azure Active Directory B2C, hodnota je LocalAccount nebo nameCoexistence. Jen pro čtení.|No|No|Trvalý výstup|
|dateOfBirth     |Date (Datum)|Datum narození.|No|No|Trvalý výstup|
|Oddělení      |Řetězec|Název oddělení, ve kterém uživatel pracuje. Maximální délka 64.|Yes|No|Trvalý výstup|
|displayName     |Řetězec|Zobrazované jméno uživatele Maximální délka 256.|Yes|Yes|Trvalý výstup|
|facsimileTelephoneNumber<sup>1</sup>|Řetězec|Telefonní číslo počítačového faxu uživatele podniku.|Yes|No|Trvalý výstup|
|givenName       |Řetězec|Křestní jméno (křestní jméno) uživatele. Maximální délka 64.|Yes|Yes|Trvalý výstup|
|jobTitle        |Řetězec|Pracovní pozice uživatele Maximální délka 128.|Yes|Yes|Trvalý výstup|
|immutableId     |Řetězec|Identifikátor, který se obvykle používá pro uživatele migrované z místní služby Active Directory.|No|No|Trvalý výstup|
|legalAgeGroupClassification|Řetězec|Právní klasifikace věkové skupiny. Je určena jen pro čtení a vypočítáno na základě vlastností ageGroup a consentProvidedForMinor. Povolené hodnoty: null, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult a dospělý.|Yes|No|Trvalý výstup|
|legalCountry<sup>1</sup>  |Řetězec|Země/oblast pro zákonné účely.|No|No|Trvalý výstup|
|pošta            |Řetězec|Adresa SMTP pro uživatele, například " bob@contoso.com ". Jen pro čtení.|No|No|Trvalý výstup|
|mailNickName    |Řetězec|Alias e-mailu pro uživatele Maximální délka 64.|No|No|Trvalý výstup|
|mobilní zařízení (mobilePhone) |Řetězec|Primární mobilní telefonní číslo uživatele Maximální délka 64.|Yes|No|Trvalý výstup|
|netId           |Řetězec|ID sítě|No|No|Trvalý výstup|
|objectId        |Řetězec|Globálně jedinečný identifikátor (GUID), který je jedinečným identifikátorem pro uživatele. Příklad: 12345678-9ABC-def0-1234-56789abcde. Jen pro čtení, neměnný.|Jen pro čtení|Yes|Vstup, trvalý výstup|
|otherMails      |Kolekce řetězců|Seznam jiných e-mailových adres pro uživatele Příklad: [" bob@contoso.com ", " Robert@fabrikam.com "].|Ano (alternativní e-mail)|No|Trvalý výstup|
|heslo        |Řetězec|Heslo pro místní účet během vytváření uživatele.|No|No|Trvalé|
|passwordPolicies     |Řetězec|Zásady hesla Jedná se o řetězec skládající se z názvu jiné zásady oddělené čárkou. Například "DisablePasswordExpiration, DisableStrongPassword".|No|No|Trvalý výstup|
|physicalDeliveryOfficeName (officeLocation)|Řetězec|Umístění kanceláře v místě podnikání uživatele. Maximální délka 128.|Yes|No|Trvalý výstup|
|postalCode      |Řetězec|Poštovní směrovací číslo pro poštovní adresu uživatele Poštovní směrovací číslo je specifické pro zemi nebo oblast uživatele. V USA systému America tento atribut obsahuje PSČ. Maximální délka 40.|Yes|No|Trvalý výstup|
|preferredLanguage    |Řetězec|Preferovaný jazyk pro uživatele. Preferovaný formát jazyka je založený na RFC 4646. Název je kombinací kódu jazykové verze ISO 639 2 s malým písmenem, který je přidružený k jazyku, a kódu podkultury v rámci ISO 3166 2, který je přidružený k zemi nebo oblasti. Příklad: "en-US" nebo "ES-ES".|No|No|Trvalý výstup|
|refreshTokensValidFromDateTime (signInSessionsValidFromDateTime)|DateTime|Všechny obnovovací tokeny vydané před tímto časem jsou neplatné a aplikace při použití neplatného obnovovacího tokenu k získání nového přístupového tokenu zobrazí chybu. Pokud k tomu dojde, aplikace bude muset získat nový obnovovací token tím, že odešle požadavek na koncový bod autorizace. Jen pro čtení.|No|No|Výstup|
|signInNames ([identity](#identities-attribute)) |Řetězec|Jedinečný přihlašovací jméno uživatele místního účtu libovolného typu v adresáři. Tento atribut použijte k získání uživatele s hodnotou přihlášení bez zadání typu místního účtu.|No|No|Vstup|
|signInNames. userName ([identity](#identities-attribute)) |Řetězec|Jedinečné uživatelské jméno uživatele místního účtu v adresáři. Tento atribut použijte k vytvoření nebo získání uživatele s konkrétním přihlašovacím jménem. Zadání tohoto parametru v PersistedClaims samostatně během operace patch odstraní další typy signInNames. Pokud chcete přidat nový typ signInNames, musíte také zachovat existující signInNames.|No|No|Vstup, trvalý výstup|
|signInNames. phoneNumber ([identity](#identities-attribute)) |Řetězec|Jedinečné telefonní číslo uživatele místního účtu v adresáři. Pomocí tohoto atributu můžete vytvořit nebo získat uživatele s konkrétním telefonním číslem přihlášení. Zadání tohoto atributu v PersistedClaims samostatně během operace patch odstraní další typy signInNames. Pokud chcete přidat nový typ signInNames, musíte také zachovat existující signInNames.|No|No|Vstup, trvalý výstup|
|signInNames. emailAddress ([identity](#identities-attribute))|Řetězec|Jedinečná e-mailová adresa uživatele místního účtu v adresáři Toto použijte k vytvoření nebo získání uživatele s konkrétní přihlašovací e-mailovou adresou. Zadání tohoto atributu v PersistedClaims samostatně během operace patch odstraní další typy signInNames. Pokud chcete přidat nový typ signInNames, musíte také zachovat existující signInNames.|No|No|Vstup, trvalý výstup|
|state           |Řetězec|Kraj v adrese uživatele Maximální délka 128.|Yes|Yes|Trvalý výstup|
|streetAddress   |Řetězec|Ulice na adrese uživatele místo podnikání. Maximální délka 1024.|Yes|Yes|Trvalý výstup|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|Řetězec|Sekundární telefonní číslo uživatele, které se používá pro službu Multi-Factor Authentication.|Yes|No|Trvalý výstup|
|strongAuthenticationEmailAddress<sup>1</sup>|Řetězec|Adresa SMTP pro uživatele Příklad: " bob@contoso.com " Tento atribut se používá pro přihlášení pomocí zásad uživatelského jména, aby se uložila e-mailová adresa uživatele. E-mailová adresa se pak použije v toku resetování hesla.|Yes|No|Trvalý výstup|
|strongAuthenticationPhoneNumber<sup>2</sup>|Řetězec|Primární telefonní číslo uživatele, které se používá pro službu Multi-Factor Authentication.|Yes|No|Trvalý výstup|
|surname         |Řetězec|Příjmení uživatele (název rodiny nebo příjmení). Maximální délka 64.|Yes|Yes|Trvalý výstup|
|telephoneNumber (první položka businessPhones)|Řetězec|Primární telefonní číslo místa podnikání uživatele|Yes|No|Trvalý výstup|
|userPrincipalName (Hlavní název uživatele)    |Řetězec|Hlavní název uživatele (UPN) Hlavní název uživatele (UPN) je přihlašovací jméno pro uživatele ve stylu internetu na základě standardu RFC 822 pro Internet. Doména musí být přítomna v kolekci ověřených domén klienta. Tato vlastnost je při vytvoření účtu povinná. Neměnný|No|No|Vstup, trvalý výstup|
|usageLocation   |Řetězec|Vyžadováno pro uživatele, kteří budou mít přiřazené licence z důvodu právního požadavku na kontrolu dostupnosti služeb v zemích nebo oblastech. Nelze nabývat hodnoty null. Dvoumístné označení země/oblasti (ISO standard 3166). Příklady: "US", "JP" a "GB".|Yes|No|Trvalý výstup|
|userType        |Řetězec|Řetězcová hodnota, která se dá použít ke klasifikaci uživatelských typů v adresáři. Hodnota musí být členem. Jen pro čtení.|Jen pro čtení|No|Trvalý výstup|
|userState (externalUserState)<sup>3</sup>|Řetězec|Pouze pro účet Azure AD B2B označuje, zda je pozvánka PendingAcceptance nebo přijata.|No|No|Trvalý výstup|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|DateTime|Zobrazuje časové razítko poslední změny vlastnosti UserState.|No|No|Trvalý výstup|

<sup>1 </sup> Nepodporováno Microsoft Graph<br><sup>2</sup> . Další informace najdete v tématu [atribut pro telefonní číslo MFA](#mfa-phone-number-attribute) .<br><sup>3 </sup> . Neměl by se používat s Azure AD B2C

## <a name="display-name-attribute"></a>Atribut zobrazovaného názvu

`displayName`Je název, který se má zobrazit v Azure Portal Správa uživatelů pro uživatele, a v přístupovém tokenu Azure AD B2C se vrátí do aplikace. Tato vlastnost je povinná.

## <a name="identities-attribute"></a>Atribut identity

K těmto typům identit se dá přidružit účet zákazníka, který může být příjemcem, partnerem nebo občanem:

- **Místní** identita – uživatelské jméno a heslo jsou uložené lokálně v adresáři Azure AD B2C. Na tyto identity často odkazujeme jako na místní účty.
- **Federované** identity – označované taky jako *sociální* nebo *podnikové* účty, je identita uživatele spravovaná pomocí federovaného poskytovatele identity, jako je Facebook, Microsoft, ADFS nebo Salesforce.

Uživatel s účtem zákazníka se může přihlásit s více identitami. Například uživatelské jméno, e-mail, ID zaměstnance, ID státní správy a další. Jeden účet může mít více identit, místní i sociální, se stejným heslem. 

V rozhraní Microsoft Graph API jsou místní i federované identity uloženy v `identities` atributu uživatele, který je typu [objectIdentity](/graph/api/resources/objectidentity). `identities`Kolekce představuje sadu identit, které se používají pro přihlášení k uživatelskému účtu. Tato kolekce umožňuje uživateli přihlásit se k uživatelskému účtu pomocí kterékoli z jeho přidružených identit. Atribut identity může obsahovat až deset objektů [objectIdentity](/graph/api/resources/objectidentity) . Každý objekt obsahuje následující vlastnosti:

| Název   | Typ |Description|
|:---------------|:--------|:----------|
|signInType|řetězec| Určuje typy přihlašování uživatelů v adresáři. Pro místní účet:  `emailAddress` , `emailAddress1` , `emailAddress2` , `emailAddress3` ,  `userName` nebo jakýkoli jiný typ, který chcete. Účet sociálních sítí musí být nastavený na  `federated` .|
|issuer|řetězec|Určuje vystavitele identity. U místních účtů (kde **signInType** není `federated` ) Tato vlastnost je výchozí název domény místního B2C tenanta, například `contoso.onmicrosoft.com` . Pro sociální identity (kde **signInType** je  `federated` ) hodnota je název vystavitele, například `facebook.com`|
|issuerAssignedId|řetězec|Určuje jedinečný identifikátor přiřazený uživateli vystavitelem. Kombinace **vystavitele** a **issuerAssignedId** musí být ve vašem tenantovi jedinečná. Pro místní účet, pokud je **signInType** nastaveno na `emailAddress` nebo `userName` , představuje přihlašovací jméno uživatele.<br>Když je **signInType** nastaveno na: <ul><li>`emailAddress` (nebo začíná `emailAddress` jako `emailAddress1` ) **issuerAssignedId** musí být platná e-mailová adresa.</li><li>`userName`(nebo jakákoli jiná hodnota) musí být **issuerAssignedId** platná [místní část e-mailové adresy](https://tools.ietf.org/html/rfc3696#section-3) .</li><li>`federated`, **issuerAssignedId** představuje jedinečný identifikátor federovaného účtu.</li></ul>|

Následující atribut **identity** s identitou místního účtu s přihlašovacím jménem, e-mailovou adresou jako přihlašování a sociální identitou. 

 ```json
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

Pro federované identity v závislosti na zprostředkovateli identity je **issuerAssignedId** jedinečná hodnota pro daného uživatele podle aplikace nebo vývojového účtu. Nakonfigurujte zásady Azure AD B2C se stejným ID aplikace, které dříve přiřadil poskytovatel sociálních sítí nebo jiná aplikace v rámci stejného vývojového účtu. 

## <a name="password-profile-property"></a>Vlastnost profilu hesla

Pro místní identitu je vyžadován atribut **passwordProfile** a obsahuje heslo uživatele. `forceChangePasswordNextSignIn`Atribut určuje, zda musí uživatel při příštím přihlášení resetovat heslo. Pro zpracování vynuceného resetování hesla [nastavte vynucený tok resetování hesla](force-password-reset.md).

Pro federované (sociální) identitu není atribut **passwordProfile** povinný.

```json
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

## <a name="password-policy-attribute"></a>Atribut zásad hesel

Zásady hesla Azure AD B2C (pro místní účty) jsou založené na zásadách Azure Active Directory [silného hesla](../active-directory/authentication/concept-sspr-policy.md) . Zásady Azure AD B2C pro registraci nebo přihlášení a resetování hesla vyžadují tuto silnou sílu hesla a nevypršení platnosti hesla.

Pokud účty, které chcete migrovat, mají slabší sílu hesla, než je [silná síla hesla](../active-directory/authentication/concept-sspr-policy.md) vynutila Azure AD B2C, můžete v scénářích migrace uživatelů zakázat požadavek na silný heslo. Chcete-li změnit výchozí zásady hesla, nastavte `passwordPolicies` atribut na hodnotu `DisableStrongPassword` . Požadavek na vytvoření uživatele můžete například upravit následujícím způsobem:

```json
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="mfa-phone-number-attribute"></a>Atribut telefonního čísla MFA

Při použití telefonu pro vícefaktorové ověřování (MFA) se k ověření identity uživatele používá mobilní telefon. Chcete-li [Přidat](/graph/api/authentication-post-phonemethods) nové telefonní číslo programově, [aktualizovat](/graph/api/b2cauthenticationmethodspolicy-update), [získat](/graph/api/b2cauthenticationmethodspolicy-get)nebo [Odstranit](/graph/api/phoneauthenticationmethod-delete) telefonní číslo, použijte [metodu ověřování](/graph/api/resources/phoneauthenticationmethod)pomocí aplikace MS Graph API Phone.

V Azure AD B2C [vlastní zásady](custom-policy-overview.md)je telefonní číslo dostupné prostřednictvím `strongAuthenticationPhoneNumber` typu deklarace identity.

## <a name="extension-attributes"></a>Atributy rozšíření

Každá aplikace pro zákazníky má jedinečné požadavky na shromažďování informací. Váš tenant Azure AD B2C obsahuje integrovanou sadu informací uložených ve vlastnostech, jako je křestní jméno, příjmení a poštovní směrovací číslo. Pomocí Azure AD B2C můžete roztáhnout sadu vlastností uložených v každém účtu zákazníka. Další informace najdete v tématech [Přidání atributů uživatele a přizpůsobení uživatelského vstupu v Azure Active Directory B2C](configure-user-input.md) .

Atributy rozšíření [rozšíří schéma](/graph/extensibility-overview#schema-extensions) objektů uživatele v adresáři. Atributy rozšíření se dají registrovat jenom u objektu aplikace, i když můžou obsahovat data pro uživatele. Atribut Extension je připojen k aplikaci s názvem `b2c-extensions-app` . Neupravujte tuto aplikaci, protože ji používá Azure AD B2C k ukládání uživatelských dat. Tuto aplikaci můžete najít v části Azure Active Directory Registrace aplikací.

> [!NOTE]
> - Do libovolného uživatelského účtu lze zapsat až 100 atributů rozšíření.
> - Pokud se aplikace B2C-Extensions odstraní, odeberou se tyto atributy rozšíření od všech uživatelů spolu s libovolnými daty, která obsahují.
> - Pokud atribut rozšíření odstraní aplikace, odebere se ze všech uživatelských účtů a hodnoty se odstraní.

Atributy rozšíření v Graph API jsou pojmenovány pomocí konvence `extension_ApplicationClientID_AttributeName` , kde `ApplicationClientID` je **ID aplikace (klienta)** `b2c-extensions-app` aplikace (nalezené v **Registrace aplikací**  >  **všech aplikací** v Azure Portal). Všimněte si, že **ID aplikace (klienta)** , jak je znázorněno v názvu atributu rozšíření, neobsahuje spojovníky. Například:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

Následující datové typy jsou podporovány při definování atributu v rozšíření schématu:

|Typ |Poznámky  |
|--------------|---------|
|Logická hodnota    | Možné hodnoty: **true** nebo **false**. |
|DateTime   | Musí být zadáno ve formátu ISO 8601. Bude uloženo v UTC.   |
|Integer    | 32-bitová hodnota.               |
|Řetězec     | maximálně 256 znaků.     |

## <a name="next-steps"></a>Další kroky

Další informace o atributech rozšíření:

- [Rozšíření schématu](/graph/extensibility-overview#schema-extensions)
- [Definování vlastních atributů](user-flow-custom-attributes.md)
