---
title: Atributy profilu uživatele ve službě Azure Active Directory B2C
description: Informace o atributy typu prostředku uživatele, které jsou podporovány profil uživatele adresáře Azure AD B2C. Přečtěte si o předdefinovaných atributech, rozšířeních a o tom, jak se atributy mapují na Microsoft Graph.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e921f0a40f53b1d08831047d1cb89ca26de41402
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057294"
---
# <a name="user-profile-attributes"></a>Atributy profilu uživatele

Váš profil uživatele adresáře Azure Active Directory (Azure AD) B2C obsahuje integrovanou sadu atributů, jako je dané jméno, příjmení, město, PSČ a telefonní číslo. Profil uživatele můžete rozšířit o vlastní data aplikace bez nutnosti externího úložiště dat. Většina atributů, které lze použít s profily uživatelů Azure AD B2C jsou také podporovány Microsoft Graph. Tento článek popisuje podporované atributy profilu uživatele Azure AD B2C. Také bere na vědomí ty atributy, které nejsou podporovány Microsoft Graph, stejně jako Microsoft Graph atributy, které by neměly být používány s Azure AD B2C.

> [!IMPORTANT]
> K ukládání citlivých osobních údajů, jako jsou přihlašovací údaje účtu, identifikační čísla státní správy, údaje o držiteli karty, údaje o finančních účtech, informace o zdravotní péči nebo citlivé základní informace, byste neměli používat integrované atributy nebo rozšiřující atributy.

Můžete také integrovat s externími systémy. Například můžete použít Azure AD B2C pro ověřování, ale delegovat na externí řízení vztahů se zákazníky (CRM) nebo databáze loajality zákazníků jako autoritativní zdroj dat zákazníků. Další informace naleznete v řešení [vzdáleného profilu.](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile)

V následující tabulce jsou uvedeny atributy [typu prostředku uživatele,](https://docs.microsoft.com/graph/api/resources/user) které jsou podporované profilem uživatele adresáře Azure AD B2C. Poskytuje následující informace o každém atributu:

- Název atributu používaný Azure AD B2C (následovaný názvem Microsoft Graph v závorce, pokud se liší)
- Datový typ atributu
- Popis atributu
- Pokud je atribut dostupný na webu Azure Portal
- Pokud lze atribut použít v toku uživatele
- Pokud atribut lze použít ve vlastní zásady [Azure AD technický profil](active-directory-technical-profile.md) a ve kterém oddílu (&lt;InputClaims&gt;, &lt;OutputClaims&gt;nebo &lt;PersistedClaims&gt;)

|Name (Název)     |Typ     |Popis|portál Azure|Toky uživatele|Vlastní zásady|
|---------|---------|----------|------------|----------|-------------|
|účetPovolený  |Logická hodnota|Zda je uživatelský účet povolen nebo zakázán: **true,** pokud je povolen, jinak **false**.|Ano|Ne|Trvalé, Výstup|
|ageGroup        |Řetězec|Věková skupina uživatele. Možné hodnoty: null, Undefined, Minor, Adult, NotAdult.|Ano|Ne|Trvalé, Výstup|
|alternativeSecurityId ([Identity](manage-user-accounts-graph-api.md#identities-property))|Řetězec|Identita jednoho uživatele od externího zprostředkovatele identity.|Ne|Ne|Vstup, Trvalé, Výstup|
|alternativeSecurityIds ([Identity](manage-user-accounts-graph-api.md#identities-property))|alternativní securityId kolekce|Kolekce identit uživatelů od externích poskytovatelů identit.|Ne|Ne|Trvalé, Výstup|
|city            |Řetězec|Město, ve kterém se uživatel nachází. Maximální délka 128.|Ano|Ano|Trvalé, Výstup|
|consentProvidedForMinor|Řetězec|Zda byl souhlas pro nezletilého poskytnut. Povolené hodnoty: null, udělena, odepřena nebo notRequired.|Ano|Ne|Trvalé, Výstup|
|country         |Řetězec|Země nebo oblast, ve které se uživatel nachází. Příklad: "US" nebo "UK". Maximální délka 128.|Ano|Ano|Trvalé, Výstup|
|createdDateTime|DateTime|Datum vytvoření objektu uživatele. Jen pro čtení.|Ne|Ne|Trvalé, Výstup|
|creationType    |Řetězec|Pokud byl uživatelský účet vytvořen jako místní účet pro klienta Služby Azure Active Directory B2C, je hodnota LocalAccount nebo nameCoexistence. Jen pro čtení.|Ne|Ne|Trvalé, Výstup|
|dateOfBirth     |Datum|Datum narození.|Ne|Ne|Trvalé, Výstup|
|Oddělení      |Řetězec|Název oddělení, ve kterém uživatel pracuje. Maximální délka 64.|Ano|Ne|Trvalé, Výstup|
|displayName     |Řetězec|Zobrazovaný název uživatele. Maximální délka 256.|Ano|Ano|Trvalé, Výstup|
|facsimileČíslo telefonu<sup>1</sup>|Řetězec|Telefonní číslo firemního faxového zařízení uživatele.|Ano|Ne|Trvalé, Výstup|
|givenName       |Řetězec|Křestní jméno (křestní jméno) uživatele. Maximální délka 64.|Ano|Ano|Trvalé, Výstup|
|název úlohy        |Řetězec|Pracovní název uživatele. Maximální délka 128.|Ano|Ano|Trvalé, Výstup|
|neměnnéId     |Řetězec|Identifikátor, který se obvykle používá pro uživatele migrované z místní služby Active Directory.|Ne|Ne|Trvalé, Výstup|
|legalAgeGroupClassification|Řetězec|Zákonná klasifikace věkových skupin. Jen pro čtení a vypočtené na základě ageGroup a consentProvidedForMinor vlastnosti. Povolené hodnoty: null, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult a adult.|Ano|Ne|Trvalé, Výstup|
|legalZemě<sup>1</sup>  |Řetězec|Země pro právní účely.|Ne|Ne|Trvalé, Výstup|
|pošta            |Řetězec|Adresa SMTP pro uživatele, napříkladbob@contoso.com" ". Jen pro čtení.|Ne|Ne|Trvalé, Výstup|
|mailNickName    |Řetězec|Poštovní alias pro uživatele. Maximální délka 64.|Ne|Ne|Trvalé, Výstup|
|mobilní (mobilní telefon) |Řetězec|Primární číslo mobilního telefonu pro uživatele. Maximální délka 64.|Ano|Ne|Trvalé, Výstup|
|netId           |Řetězec|Čisté ID.|Ne|Ne|Trvalé, Výstup|
|Objectid        |Řetězec|Globálně jedinečný identifikátor (GUID), který je jedinečným identifikátorem uživatele. Příklad: 12345678-9abc-def0-1234-56789abcde. Jen pro čtení, neměnný.|Jen pro čtení|Ano|Vstup, Trvalé, Výstup|
|otherMails      |Kolekce řetězců|Seznam dalších e-mailových adres pro uživatele. Příklad: ["bob@contoso.com", "Robert@fabrikam.com"].|Ano (alternativní e-mail)|Ne|Trvalé, Výstup|
|heslo        |Řetězec|Heslo pro místní účet během vytváření uživatele.|Ne|Ne|Trvalé|
|zásady hesel     |Řetězec|Zásady hesla. Jedná se o řetězec skládající se z různých název zásad oddělený čárkou. "DisablePasswordExpiration, DisableStrongPassword".|Ne|Ne|Trvalé, Výstup|
|physicalDeliveryOfficeName (officeLocation)|Řetězec|Umístění kanceláře v místě podnikání uživatele. Maximální délka 128.|Ano|Ne|Trvalé, Výstup|
|Postalcode      |Řetězec|PSČ poštovní adresy uživatele. PSČ je specifické pro zemi nebo oblast uživatele. Ve Spojených státech amerických obsahuje tento atribut PSČ. Maximální délka 40.|Ano|Ne|Trvalé, Výstup|
|preferredLanguage    |Řetězec|Upřednostňovaný jazyk pro uživatele. By se měl řídit kódem ISO 639-1. Příklad: "en US".|Ne|Ne|Trvalé, Výstup|
|refreshTokensValidFromDateTime|DateTime|Všechny obnovovací tokeny vydané před touto dobou jsou neplatné a aplikace se zobrazí chyba při použití neplatného obnovovacího tokenu k získání nového přístupového tokenu. Pokud k tomu dojde, aplikace bude muset získat nový obnovovací token tím, že žádost autorizační koncový bod. Jen pro čtení.|Ne|Ne|Výstup|
|signInNames ([Identity](manage-user-accounts-graph-api.md#identities-property)) |Řetězec|Jedinečné přihlašovací jméno uživatele místního účtu libovolného typu v adresáři. Pomocí této možnosti můžete získat uživatele s přihlašovací hodnotou bez zadání typu místního účtu.|Ne|Ne|Vstup|
|signInNames.userName ([Identity](manage-user-accounts-graph-api.md#identities-property)) |Řetězec|Jedinečné uživatelské jméno uživatele místního účtu v adresáři. Pomocí této funkce můžete vytvořit nebo získat uživatele s určitým přihlašovacím uživatelským jménem. Zadání tohoto v PersistedClaims sám během operace oprava odebere jiné typy signInNames. Pokud chcete přidat nový typ signInNames, musíte také zachovat existující signInNames.|Ne|Ne|Vstup, Trvalé, Výstup|
|signInNames.phoneNumber ([Identity](manage-user-accounts-graph-api.md#identities-property)) |Řetězec|Jedinečné telefonní číslo uživatele místního účtu v adresáři. Pomocí této funkce můžete vytvořit nebo získat uživatele s konkrétním přihlašovacím telefonním číslem. Zadání tohoto v PersistedClaims sám během operace oprava odebere jiné typy signInNames. Pokud chcete přidat nový typ signInNames, musíte také zachovat existující signInNames.|Ne|Ne|Vstup, Trvalé, Výstup|
|signInNames.emailAddress ([Identity](manage-user-accounts-graph-api.md#identities-property))|Řetězec|Jedinečná e-mailová adresa uživatele místního účtu v adresáři. Pomocí této možnosti můžete vytvořit nebo získat uživatele s konkrétní přihlašovací e-mailovou adresou. Zadání tohoto v PersistedClaims sám během operace oprava odebere jiné typy signInNames. Pokud chcete přidat nový typ signInNames, musíte také zachovat existující signInNames.|Ne|Ne|Vstup, Trvalé, Výstup|
|state           |Řetězec|Stát nebo provincie v adrese uživatele. Maximální délka 128.|Ano|Ano|Trvalé, Výstup|
|Streetaddress   |Řetězec|Adresa místa podnikání uživatele. Maximální délka 1024.|Ano|Ano|Trvalé, Výstup|
|strongAuthentication AlternativePhoneČíslo<sup>1</sup>|Řetězec|Sekundární telefonní číslo uživatele, které se používá pro vícefaktorové ověřování.|Ano|Ne|Trvalé, Výstup|
|silnáověřovacíeeeadresa<sup>1</sup>|Řetězec|Adresa SMTP pro uživatele. Příklad:bob@contoso.com" " Tento atribut se používá pro přihlášení pomocí zásad uživatelského jména k uložení e-mailové adresy uživatele. E-mailová adresa se pak používá v toku resetování hesla.|Ano|Ne|Trvalé, Výstup|
|strongAuthenticationPhoneČíslo<sup>1</sup>|Řetězec|Primární telefonní číslo uživatele, které se používá pro vícefaktorové ověřování.|Ano|Ne|Trvalé, Výstup|
|surname         |Řetězec|Příjmení uživatele (příjmení nebo příjmení). Maximální délka 64.|Ano|Ano|Trvalé, Výstup|
|phoneNumber (první záznam businessphones)|Řetězec|Primární telefonní číslo místa podnikání uživatele.|Ano|Ne|Trvalé, Výstup|
|userPrincipalName (Hlavní název uživatele)    |Řetězec|Hlavní název uživatele (UPN) Hlavní název uživatele je přihlašovací jméno ve stylu Internetu pro uživatele založené na standardu Sítě Internet RFC 822. Doména musí být k dispozici v kolekci ověřených domén klienta. Tato vlastnost je vyžadována při vytvoření účtu. Neměnný|Ne|Ne|Vstup, Trvalé, Výstup|
|usageUmístění   |Řetězec|Vyžadováno pro uživatele, kterým budou přiřazeny licence z důvodu zákonného požadavku na kontrolu dostupnosti služeb v zemích. Nelze zrušit. Dvoupísmenný kód země (norma ISO 3166). Příklady: "US", "JP" a "GB".|Ano|Ne|Trvalé, Výstup|
|userType        |Řetězec|Hodnota řetězce, kterou lze použít ke klasifikaci typů uživatelů v adresáři. Hodnota musí být Člen. Jen pro čtení.|Jen pro čtení|Ne|Trvalé, Výstup|
|userState (externalUserState)<sup>2</sup>|Řetězec|Pro azure ad b2b účet pouze označuje, zda je pozvánka PendingAcceptance nebo Přijato.|Ne|Ne|Trvalé, Výstup|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|DateTime|Zobrazuje časové razítko pro nejnovější změnu vlastnosti UserState.|Ne|Ne|Trvalé, Výstup|
|<sup>1.</sup> Microsoft Graph nepodporuje<br><sup>2.</sup> By se neměl používat s Azure AD B2C||||||


## <a name="extension-attributes"></a>Atributy rozšíření

Často budete muset vytvořit vlastní atributy, jako v následujících případech:

- Aplikace pro zákazníka musí pro atribut jako **LoyaltyNumber**zachovat.
- Zprostředkovatel identity má jedinečný identifikátor uživatele, jako **je uniqueUserGUID,** který musí být uložen.
- Vlastní cesta uživatele musí zachovat stav uživatele, jako je **migrationStatus**.

Azure AD B2C rozšiřuje sadu atributů uložených v každém uživatelském účtu. Atributy rozšíření [rozšiřují schéma](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions) uživatelských objektů v adresáři. Atributy rozšíření lze zaregistrovat pouze na objekt aplikace, i když mohou obsahovat data pro uživatele. Atribut rozšíření je připojen k aplikaci s názvem b2c-extensions-app. Tuto aplikaci neupravujte, protože ji používá Azure AD B2C pro ukládání uživatelských dat. Tuto aplikaci najdete v části Registrace aplikací Azure Active Directory.

> [!NOTE]
> - Do libovolného uživatelského účtu lze zapsat až 100 atributů rozšíření.
> - Pokud je aplikace b2c-extensions-app odstraněna, tyto atributy rozšíření jsou odebrány všem uživatelům spolu se všemi daty, které obsahují.
> - Pokud je atribut rozšíření odstraněn aplikací, je odebrán ze všech uživatelských účtů a hodnoty jsou odstraněny.
> - Základní název atributu rozšíření je generován ve formátu "Extension_" + ID aplikace + "_" + Název atributu. Pokud například vytvoříte atribut rozšíření LoyaltyNumber a ID aplikace b2c-extensions-app je 831374b3-bd50-41bf-aa54-263ec9e050fc, bude název atributu základní ho rozšíření: extension_831374b3bd5041bfaa54263ec9e050fc_ LoyaltyNumber. Základní název se používá při spuštění dotazů rozhraní API graph u vytvoření nebo aktualizace uživatelských účtů.

Při definování vlastnosti v rozšíření schématu jsou podporovány následující datové typy:

|Typ vlastnosti |Poznámky  |
|--------------|---------|
|Logická hodnota    | Možné hodnoty: **true** nebo **false**. |
|DateTime   | Musí být zadán ve formátu ISO 8601. Budou uloženy v UTC.   |
|Integer    | 32bitová hodnota.               |
|Řetězec     | Maximálně 256 znaků.     |

## <a name="next-steps"></a>Další kroky
Další informace o atributech rozšíření:
- [Rozšíření schématu](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)
- [Definování vlastních atributů s tokem uživatele](user-flow-custom-attributes.md)
- [Definování vlastních atributů pomocí vlastních zásad](custom-policy-custom-attributes.md)
