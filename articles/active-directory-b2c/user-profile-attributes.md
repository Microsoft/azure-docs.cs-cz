---
title: Atributy profilu uživatele v Azure Active Directory B2C
description: Přečtěte si o atributech typu prostředku uživatele, které jsou podporovány profilem uživatele Azure AD B2C Directory. Přečtěte si o předdefinovaných atributech, rozšířeních a způsobu mapování atributů na Microsoft Graph.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 98c33d4b9e749e804f70d9dccb7198884c80dfe7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952696"
---
# <a name="user-profile-attributes"></a>Atributy profilu uživatele

Váš Azure Active Directory (Azure AD) profil uživatele adresáře B2C obsahuje předdefinovanou sadu atributů, jako je například křestní jméno, příjmení, město, PSČ a telefonní číslo. Profil uživatele můžete roztáhnout vlastními daty aplikace bez nutnosti externího úložiště dat. Většinu atributů, které lze použít s Azure AD B2C profily uživatelů, jsou podporovány také Microsoft Graph. Tento článek popisuje podporované Azure AD B2C atributy profilu uživatele. Také poznamenejte tyto atributy, které nejsou podporovány Microsoft Graph a také Microsoft Graph atributy, které by neměly být použity s Azure AD B2C.

> [!IMPORTANT]
> Nepoužívejte předdefinované ani rozšiřující atributy k ukládání citlivých osobních údajů, jako jsou například přihlašovací údaje k účtu, identifikační čísla státní správy, data držitele, údaje o finančním účtu, informace o zdravotním péči nebo citlivé informace na pozadí.

Můžete také integrovat s externími systémy. Můžete například použít Azure AD B2C pro ověřování, ale delegovat externí databázi Customer Relationship Management (CRM) nebo věrnostní databáze zákazníků jako autoritativní zdroj zákaznických dat. Další informace najdete v tématu řešení [vzdáleného profilu](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) .

Následující tabulka uvádí atributy [typu prostředku uživatele](/graph/api/resources/user) , které jsou podporovány profilem uživatele Azure AD B2C Directory. Poskytuje následující informace o jednotlivých atributech:

- Název atributu, který používá Azure AD B2C (za ním následuje Microsoft Graph název v závorkách, pokud se liší)
- Typ dat atributu
- Popis atributu
- Pokud je atribut k dispozici v Azure Portal
- Pokud se atribut dá použít v toku uživatele
- Pokud se atribut dá použít ve vlastní zásadě [Azure AD Technical Profile](active-directory-technical-profile.md) a v jaké části ( &lt; InputClaims &gt; , &lt; OutputClaims &gt; nebo &lt; PersistedClaims &gt; )

|Název     |Typ     |Popis|portál Azure|Toky uživatele|Vlastní zásady|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Logická hodnota|Zda je uživatelský účet povolený nebo zakázaný: **true** , pokud je účet povolený, v opačném případě **false**.|Yes|Ne|Trvalý výstup|
|ageGroup        |Řetězec|Věková skupina uživatele. Možné hodnoty: null, undefined, nezletilý, dospělý, NotAdult.|Yes|Ne|Trvalý výstup|
|alternativeSecurityId ([identity](manage-user-accounts-graph-api.md#identities-property))|Řetězec|Jediná identita uživatele od externího zprostředkovatele identity.|Ne|Ne|Vstup, trvalý výstup|
|alternativeSecurityIds ([identity](manage-user-accounts-graph-api.md#identities-property))|alternativní kolekce securityId|Kolekce uživatelských identit od externích zprostředkovatelů identity.|Ne|Ne|Trvalý výstup|
|city            |Řetězec|Město, ve kterém se uživatel nachází Maximální délka 128.|Yes|Yes|Trvalý výstup|
|consentProvidedForMinor|Řetězec|Zda byl souhlas poskytnut pro méně závažná. Povolené hodnoty: null, uděleno, zamítnuto nebo notRequired.|Yes|Ne|Trvalý výstup|
|country         |Řetězec|Země nebo oblast, ve které se uživatel nachází. Příklad: "US" nebo "UK". Maximální délka 128.|Yes|Yes|Trvalý výstup|
|createdDateTime|DateTime|Datum vytvoření objektu uživatele. Jen pro čtení.|Ne|Ne|Trvalý výstup|
|creationType    |Řetězec|Pokud byl uživatelský účet vytvořen jako místní účet pro klienta Azure Active Directory B2C, hodnota je LocalAccount nebo nameCoexistence. Jen pro čtení.|Ne|Ne|Trvalý výstup|
|dateOfBirth     |Datum|Datum narození.|Ne|Ne|Trvalý výstup|
|Oddělení      |Řetězec|Název oddělení, ve kterém uživatel pracuje. Maximální délka 64.|Yes|Ne|Trvalý výstup|
|displayName     |Řetězec|Zobrazované jméno uživatele Maximální délka 256.|Yes|Yes|Trvalý výstup|
|facsimileTelephoneNumber<sup>1</sup>|Řetězec|Telefonní číslo počítačového faxu uživatele podniku.|Yes|Ne|Trvalý výstup|
|givenName       |Řetězec|Křestní jméno (křestní jméno) uživatele. Maximální délka 64.|Yes|Yes|Trvalý výstup|
|jobTitle        |Řetězec|Pracovní pozice uživatele Maximální délka 128.|Yes|Yes|Trvalý výstup|
|immutableId     |Řetězec|Identifikátor, který se obvykle používá pro uživatele migrované z místní služby Active Directory.|Ne|Ne|Trvalý výstup|
|legalAgeGroupClassification|Řetězec|Právní klasifikace věkové skupiny. Je určena jen pro čtení a vypočítáno na základě vlastností ageGroup a consentProvidedForMinor. Povolené hodnoty: null, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult a dospělý.|Yes|Ne|Trvalý výstup|
|legalCountry<sup>1</sup>  |Řetězec|Země/oblast pro zákonné účely.|Ne|Ne|Trvalý výstup|
|pošta            |Řetězec|Adresa SMTP pro uživatele, například " bob@contoso.com ". Jen pro čtení.|Ne|Ne|Trvalý výstup|
|mailNickName    |Řetězec|Alias e-mailu pro uživatele Maximální délka 64.|Ne|Ne|Trvalý výstup|
|mobilní zařízení (mobilePhone) |Řetězec|Primární mobilní telefonní číslo uživatele Maximální délka 64.|Yes|Ne|Trvalý výstup|
|netId           |Řetězec|ID sítě|Ne|Ne|Trvalý výstup|
|Objektu        |Řetězec|Globálně jedinečný identifikátor (GUID), který je jedinečným identifikátorem pro uživatele. Příklad: 12345678-9ABC-def0-1234-56789abcde. Jen pro čtení, neměnný.|Jen pro čtení|Yes|Vstup, trvalý výstup|
|otherMails      |Kolekce řetězců|Seznam dalších e-mailových adres pro uživatele Příklad: [" bob@contoso.com ", " Robert@fabrikam.com "].|Ano (alternativní e-mail)|Ne|Trvalý výstup|
|heslo        |Řetězec|Heslo pro místní účet během vytváření uživatele.|Ne|Ne|Trvalé|
|passwordPolicies     |Řetězec|Zásady hesla Jedná se o řetězec skládající se z názvu jiné zásady oddělené čárkou. například "DisablePasswordExpiration, DisableStrongPassword".|Ne|Ne|Trvalý výstup|
|physicalDeliveryOfficeName (officeLocation)|Řetězec|Umístění kanceláře v místě podnikání uživatele. Maximální délka 128.|Yes|Ne|Trvalý výstup|
|Ovládacím      |Řetězec|Poštovní směrovací číslo pro poštovní adresu uživatele Poštovní směrovací číslo je specifické pro zemi nebo oblast uživatele. V USA systému America tento atribut obsahuje PSČ. Maximální délka 40.|Yes|Ne|Trvalý výstup|
|preferredLanguage    |Řetězec|Preferovaný jazyk pro uživatele. By měl dodržovat kód ISO 639-1. Příklad: "en-US".|Ne|Ne|Trvalý výstup|
|refreshTokensValidFromDateTime|DateTime|Všechny obnovovací tokeny vydané před tímto časem jsou neplatné a aplikace při použití neplatného obnovovacího tokenu k získání nového přístupového tokenu zobrazí chybu. Pokud k tomu dojde, aplikace bude muset získat nový obnovovací token tím, že odešle požadavek na koncový bod autorizace. Jen pro čtení.|Ne|Ne|Výstup|
|signInNames ([identity](manage-user-accounts-graph-api.md#identities-property)) |Řetězec|Jedinečný přihlašovací jméno uživatele místního účtu libovolného typu v adresáři. Tuto hodnotu použijte k získání uživatele s hodnotou přihlášení bez zadání typu místního účtu.|Ne|Ne|Vstup|
|signInNames. userName ([identity](manage-user-accounts-graph-api.md#identities-property)) |Řetězec|Jedinečné uživatelské jméno uživatele místního účtu v adresáři. Tuto hodnotu použijte k vytvoření nebo získání uživatele s konkrétním přihlašovacím jménem. Zadání tohoto parametru v PersistedClaims samostatně během operace patch odstraní další typy signInNames. Pokud chcete přidat nový typ signInNames, musíte také zachovat existující signInNames.|Ne|Ne|Vstup, trvalý výstup|
|signInNames. phoneNumber ([identity](manage-user-accounts-graph-api.md#identities-property)) |Řetězec|Jedinečné telefonní číslo uživatele místního účtu v adresáři. Tuto hodnotu použijte k vytvoření nebo získání uživatele s konkrétním telefonním číslem přihlášení. Zadání tohoto parametru v PersistedClaims samostatně během operace patch odstraní další typy signInNames. Pokud chcete přidat nový typ signInNames, musíte také zachovat existující signInNames.|Ne|Ne|Vstup, trvalý výstup|
|signInNames. emailAddress ([identity](manage-user-accounts-graph-api.md#identities-property))|Řetězec|Jedinečná e-mailová adresa uživatele místního účtu v adresáři Toto použijte k vytvoření nebo získání uživatele s konkrétní přihlašovací e-mailovou adresou. Zadání tohoto parametru v PersistedClaims samostatně během operace patch odstraní další typy signInNames. Pokud chcete přidat nový typ signInNames, musíte také zachovat existující signInNames.|Ne|Ne|Vstup, trvalý výstup|
|state           |Řetězec|Kraj v adrese uživatele Maximální délka 128.|Yes|Yes|Trvalý výstup|
|streetAddress   |Řetězec|Ulice na adrese uživatele místo podnikání. Maximální délka 1024.|Yes|Yes|Trvalý výstup|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|Řetězec|Sekundární telefonní číslo uživatele, které se používá pro službu Multi-Factor Authentication.|Yes|Ne|Trvalý výstup|
|strongAuthenticationEmailAddress<sup>1</sup>|Řetězec|Adresa SMTP pro uživatele Příklad: " bob@contoso.com " Tento atribut se používá pro přihlášení pomocí zásad uživatelského jména, aby se uložila e-mailová adresa uživatele. E-mailová adresa se pak použije v toku resetování hesla.|Yes|Ne|Trvalý výstup|
|strongAuthenticationPhoneNumber<sup>1</sup>|Řetězec|Primární telefonní číslo uživatele, které se používá pro službu Multi-Factor Authentication.|Yes|Ne|Trvalý výstup|
|surname         |Řetězec|Příjmení uživatele (název rodiny nebo příjmení). Maximální délka 64.|Yes|Yes|Trvalý výstup|
|telephoneNumber (první položka businessPhones)|Řetězec|Primární telefonní číslo místa podnikání uživatele|Yes|Ne|Trvalý výstup|
|userPrincipalName (Hlavní název uživatele)    |Řetězec|Hlavní název uživatele (UPN) Hlavní název uživatele (UPN) je přihlašovací jméno pro uživatele ve stylu internetu na základě standardu RFC 822 pro Internet. Doména musí být přítomna v kolekci ověřených domén klienta. Tato vlastnost je při vytvoření účtu povinná. Neměnný|Ne|Ne|Vstup, trvalý výstup|
|usageLocation   |Řetězec|Vyžadováno pro uživatele, kteří budou mít přiřazené licence z důvodu právního požadavku na kontrolu dostupnosti služeb v zemích nebo oblastech. Nelze nabývat hodnoty null. Dvoumístné označení země/oblasti (ISO standard 3166). Příklady: "US", "JP" a "GB".|Yes|Ne|Trvalý výstup|
|userType        |Řetězec|Řetězcová hodnota, která se dá použít ke klasifikaci uživatelských typů v adresáři. Hodnota musí být členem. Jen pro čtení.|Jen pro čtení|Ne|Trvalý výstup|
|userState (externalUserState)<sup>2</sup>|Řetězec|Pouze pro účet Azure AD B2B označuje, zda je pozvánka PendingAcceptance nebo přijata.|Ne|Ne|Trvalý výstup|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|DateTime|Zobrazuje časové razítko poslední změny vlastnosti UserState.|Ne|Ne|Trvalý výstup|
|<sup>1 </sup> Nepodporováno Microsoft Graph<br><sup>2 </sup> . Neměl by se používat s Azure AD B2C||||||


## <a name="extension-attributes"></a>Atributy rozšíření

Často budete potřebovat vytvořit vlastní atributy, jako v následujících případech:

- Zákaznická aplikace musí být zachována pro atribut, jako je **LoyaltyNumber**.
- Zprostředkovatel identity má jedinečný identifikátor uživatele, jako je **uniqueUserGUID** , který se musí uložit.
- Vlastní cesta uživatele musí uchovávat stav uživatele, například **migrationStatus**.

Azure AD B2C rozšiřuje sadu atributů uložených v každém uživatelském účtu. Atributy rozšíření [rozšíří schéma](/graph/extensibility-overview#schema-extensions) objektů uživatele v adresáři. Atributy rozšíření se dají registrovat jenom u objektu aplikace, i když můžou obsahovat data pro uživatele. Atribut Extension je připojen k aplikaci s názvem B2C-Extensions-App. Neupravujte tuto aplikaci, protože ji používá Azure AD B2C k ukládání uživatelských dat. Tuto aplikaci můžete najít v části Azure Active Directory Registrace aplikací.

> [!NOTE]
> - Do libovolného uživatelského účtu lze zapsat až 100 atributů rozšíření.
> - Pokud se aplikace B2C-Extensions odstraní, odeberou se tyto atributy rozšíření od všech uživatelů spolu s libovolnými daty, která obsahují.
> - Pokud atribut rozšíření odstraní aplikace, odebere se ze všech uživatelských účtů a hodnoty se odstraní.
> - Podkladový název atributu Extension je vygenerován ve formátu "Extension_" + ID aplikace + "_" + název atributu. Například pokud vytvoříte atribut rozšíření LoyaltyNumber a ID aplikace B2C-Extensions-App je 831374b3-bd50-41bf-aa54-263ec9e050fc, bude název podkladového atributu rozšíření: extension_831374b3bd5041bfaa54263ec9e050fc_LoyaltyNumber. Podkladový název použijete při spuštění Graph API dotazy k vytvoření nebo aktualizaci uživatelských účtů.

Následující datové typy jsou podporovány při definování vlastnosti v rozšíření schématu:

|Typ vlastnosti |Poznámky  |
|--------------|---------|
|Logická hodnota    | Možné hodnoty: **true** nebo **false**. |
|DateTime   | Musí být zadáno ve formátu ISO 8601. Bude uloženo v UTC.   |
|Integer    | 32-bitová hodnota.               |
|Řetězec     | maximálně 256 znaků.     |

## <a name="next-steps"></a>Další kroky
Další informace o atributech rozšíření:
- [Rozšíření schématu](/graph/extensibility-overview#schema-extensions)
- [Definování vlastních atributů pomocí toku uživatele](user-flow-custom-attributes.md)
- [Definování vlastních atributů vlastními zásadami](custom-policy-custom-attributes.md)