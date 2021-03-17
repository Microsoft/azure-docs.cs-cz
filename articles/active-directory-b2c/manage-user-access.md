---
title: Správa přístupu uživatele v Azure Active Directory B2C | Microsoft Docs
description: Naučte se identifikovat nezletilé, shromažďovat data narození a země nebo oblasti a získávat ve své aplikaci přijetí podmínek použití pomocí Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0ee26e7fe74d87f7b20f9a28b049b8043b376273
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518048"
---
# <a name="manage-user-access-in-azure-active-directory-b2c"></a>Správa přístupu uživatelů v Azure Active Directory B2C

Tento článek popisuje, jak spravovat přístup uživatelů k aplikacím pomocí Azure Active Directory B2C (Azure AD B2C). Správa přístupu v aplikaci zahrnuje:

- Identifikace nezletilých a řízení přístupu uživatelů k vaší aplikaci.
- Vyžadování souhlasu rodičů pro nezletilé aplikace pro používání vašich aplikací.
- Shromažďování dat o narození a zemi nebo oblasti od uživatelů
- Zachytávání smlouvy o používání podmínek a přístupu k nim.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="control-minor-access"></a>Řízení vedlejšího přístupu

Aplikace a organizace se můžou rozhodnout, jestli mají v používání aplikací a služeb, které nejsou cílené na tuto cílovou skupinu, zablokovat mladistvé. Případně se můžou aplikace a organizace rozhodnout přijmout nezletilý a následně spravovat souhlas rodičů a poskytovat oprávněná prostředí pro menšiny, jak je stanoví obchodní pravidla a která jsou povolená nařízením.

Pokud je uživatel označený jako nepatrný, můžete nastavit tok uživatele v Azure AD B2C na jednu ze tří možností:

- **Odeslání podepsaného ID_TOKEN JWT zpět do aplikace**: uživatel je zaregistrován v adresáři a do aplikace se vrátí token. Aplikace pak pokračuje použitím obchodních pravidel. Aplikace může například pokračovat v procesu souhlasu rodičů. Chcete-li použít tuto metodu, vyberte možnost přijímat deklarace identity **ageGroup** a **consentProvidedForMinor** z aplikace.

- **Odeslání nepodepsaného tokenu JSON do aplikace**: Azure AD B2C upozorní aplikaci, že uživatel je nepatrný, a poskytuje stav souhlasu uživatele rodičů. Aplikace pak pokračuje použitím obchodních pravidel. Token JSON nedokončil úspěšné ověření v aplikaci. Aplikace musí zpracovat neověřeného uživatele v závislosti na deklaracích obsažených v tokenu JSON, což může zahrnovat **název**, **e-mail**, **ageGroup** a **consentProvidedForMinor**.

- **Blokování uživatele**: Pokud je uživatel nezletilý a nebyl zadán souhlas rodičů, Azure AD B2C může uživatele informovat o tom, že jsou zablokované. Není vystaven žádný token, přístup je zablokován a uživatelský účet není vytvořen během registrační cesty. Chcete-li implementovat toto oznámení, poskytněte vhodnou stránku obsahu HTML/CSS, která uživatele informuje a nabídne příslušné možnosti. Aplikace nevyžaduje pro nové registrace žádnou další akci.

## <a name="get-parental-consent"></a>Získání souhlasu rodičů

V závislosti na pravidle aplikace může být nutné udělit souhlas rodičů uživateli, který je ověřený jako dospělý. Azure AD B2C neposkytuje prostředí pro kontrolu stáří jednotlivce a pak umožní ověřenému dospělému udělit všem uživatelům souhlas rodičů. Toto prostředí musí poskytnout aplikace nebo jiný poskytovatel služeb.

Následuje příklad toku uživatele pro shromáždění souhlasu rodičů:

1. Operace [Microsoft Graph API](/graph/use-the-api) identifikuje uživatele jako nezletilý a vrátí data uživatelů do aplikace ve formě nepodepsaného tokenu JSON.

2. Aplikace zpracovává token JSON a zobrazuje obrazovku jako nezletilou, oznamuje jim, že je vyžadován souhlas rodičů a žádá o souhlas nadřazeného objektu online.

3. Azure AD B2C zobrazuje cestu pro přihlášení, ke které se uživatel může přihlásit normálně, a vydá token aplikaci, která je nastavená na include **legalAgeGroupClassification = "minorWithParentalConsent"**. Aplikace shromáždí e-mailovou adresu nadřazeného objektu a ověří, zda je nadřazeným prvkem dospělý. Pokud to chcete udělat, používá důvěryhodný zdroj, jako je státní úřad pro identifikaci, ověření licence nebo důkaz platební karty. Pokud je ověření úspěšné, aplikace vyzve k jejímu přihlášení méně pomocí Azure AD B2C toku uživatele. Pokud je souhlas odepřen (například pokud **legalAgeGroupClassification = "minorWithoutParentalConsent"**), Azure AD B2C vrátí token JSON (nejedná se o přihlášení) do aplikace, aby mohl restartovat proces souhlasu. Je volitelně možné přizpůsobit tok uživatele tak, aby nezletilý nebo dospělý mohl znovu získat přístup k účtu k menšímu účtu odesláním registračního kódu na e-mailovou adresu nezletilé osoby nebo e-mailovou adresu dospělého.

4. Aplikace nabízí možnost podverze k odvolání souhlasu.

5. V případě, že podverze nebo dospělý odvolá souhlas, lze Microsoft Graph rozhraní API použít ke změně **consentProvidedForMinor** na hodnotu **Odepřít**. Alternativně se může aplikace rozhodnout pro odstranění menšího, ale jeho souhlasu bylo odvoláno. Je volitelně možné přizpůsobit tok uživatele tak, aby ověřený vedlejší (nebo nadřazená položka, která používá účet nezletilý), mohla odvolat souhlas. Azure AD B2C zaznamenává **consentProvidedForMinor** jako **odepřené**.

Další informace o **legalAgeGroupClassification**, **consentProvidedForMinor** a **ageGroup** najdete v tématu [typ prostředku uživatele](/graph/api/resources/user). Další informace o vlastních atributech najdete v tématu [použití vlastních atributů ke shromažďování informací o vašich spotřebitelích](user-flow-custom-attributes.md). Při adresování rozšířených atributů pomocí rozhraní Microsoft Graph API je nutné použít dlouhou verzi atributu, například *extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth*: *2011-01-01T00:00:00Z*.

## <a name="gather-date-of-birth-and-countryregion-data"></a>Shromážděte data narození a země/oblasti.

Aplikace se můžou spoléhat na Azure AD B2C ke shromáždění data narození (DOB) a informací o zemi nebo oblasti od všech uživatelů během registrace. Pokud tyto informace ještě neexistují, může je aplikace požádat od uživatele při příští cestě k ověřování (přihlášení). Uživatelé nemohou pokračovat, aniž by poskytovali informace o DOB a zemi/oblasti. Azure AD B2C používá informace k určení, zda se jednotlivec považuje za nezletilé podle regulativních standardů této země nebo oblasti.

Přizpůsobený tok uživatele může shromažďovat informace o DOB a zemi/oblasti a používat Azure AD B2C transformaci deklarací identity k určení **ageGroup** a uchování výsledku (nebo uchování informací o dob a zemi/oblasti přímo) v adresáři.

Následující kroky ukazují logiku, která se používá k výpočtu **ageGroup** z data narození uživatele:

1. Zkuste najít zemi nebo oblast podle kódu země nebo oblasti v seznamu. Pokud se země nebo oblast nenajde, vraťte se k **výchozímu**.

2. Pokud je uzel **MinorConsent** přítomný v prvku země/oblasti:

    a. Vypočítat datum, kdy uživatel musí být ve stavu, aby byl považován za dospělé. Pokud je aktuální datum například 14. března 2015 a **MinorConsent** je 18, datum narození nesmí být pozdější než 14. března 2000.

    b. Porovnejte minimální datum narození s aktuálním datem narození. Pokud je minimální datum narození před datem narození uživatele, vrátí tento výpočet jako výpočet věkové skupiny **vedlejší** hodnotu.

3. Pokud se uzel **MinorNoConsentRequired** nachází v prvku země/oblast, opakujte kroky 2a a 2b pomocí hodnoty z **MinorNoConsentRequired**. Výstupem programu 2b vrátí **MinorNoConsentRequired** , pokud je minimální datum narození před datem narození uživatele.

4. Pokud žádný výpočet nevrátí hodnotu true, výpočet vrátí **dospělý**.

Pokud aplikace spolehlivě shromáždila data DOB nebo země nebo oblasti jinými metodami, aplikace může použít Graph API k aktualizaci záznamu uživatele s těmito informacemi. Například:

- Pokud je uživatel známý jako dospělý, aktualizujte atribut adresáře **ageGroup** hodnotou **dospělé**.
- Je-li uživatel znám jako nepatrný, aktualizujte atribut adresáře **ageGroup** hodnotou **podverze** a podle potřeby nastavte **consentProvidedForMinor**.

## <a name="minor-calculation-rules"></a>Vedlejší pravidla výpočtů

Omezení stáří zahrnuje dvě věkové hodnoty: stáří, které už někdo nepovažuje za nezletilou, a stáří, ve kterém by měl mít uživatel souhlas rodičů. V následující tabulce jsou uvedena věková pravidla, která se používají k definování vedlejších a méně závažného souhlasu.

| Země/oblast | Název země nebo oblasti | Věk pro menší souhlas | Menší stáří |
| -------------- | ------------------- | ----------------- | --------- |
| Výchozí | Žádné | Žádné | 18 |
| AE | Spojené arabské emiráty | Žádné | 21 |
| AT | Rakousko | 14 | 18 |
| BE | Belgie | 14 | 18 |
| BG | Bulharsko | 16 | 18 |
| BH | Bahrajn | Žádné | 21 |
| CM | Kamerun | Žádné | 21 |
| CY | Kypr | 16 | 18 |
| CZ | Česká republika | 16 | 18 |
| DE | Německo | 16 | 18 |
| DK | Dánsko | 16 | 18 |
| EE | Estonsko | 16 | 18 |
| EG | Egypt | Žádné | 21 |
| ES | Španělsko | 13 | 18 |
| FR | Francie | 16 | 18 |
| GB | Spojené království | 13 | 18 |
| GR | Řecko | 16 | 18 |
| HR | Chorvatsko | 16 | 18 |
| HU | Maďarsko | 16 | 18 |
| IE | Irsko | 13 | 18 |
| IT | Itálie | 16 | 18 |
| KR | Korejská republika | 14 | 18 |
| LT | Litva | 16 | 18 |
| LU | Lucembursko | 16 | 18 |
| LV | Lotyšsko | 16 | 18 |
| MT | Malta | 16 | 18 |
| NA | Namibie | Žádné | 21 |
| NL | Nizozemsko | 16 | 18 |
| PL | Polsko | 13 | 18 |
| PT | Portugalsko | 16 | 18 |
| RO | Rumunsko | 16 | 18 |
| SE | Švédsko | 13 | 18 |
| SG | Singapur | Žádné | 21 |
| SI | Slovinsko | 16 | 18 |
| SK | Slovensko | 16 | 18 |
| TD | Čad | Žádné | 21 |
| TH | Thajsko | Žádné | 20 |
| TW | Tchaj-wan | Žádné | 20 |
| USA | USA | 13 | 18 |



## <a name="capture-terms-of-use-agreement"></a>Zachytit smlouvu podmínek použití

Při vývoji aplikace obvykle zaznamenáte přijetí podmínek použití v rámci svých aplikací uživateli, a to bez jakýchkoli místních účastí z adresáře uživatele. Je ale možné použít tok uživatele Azure AD B2C ke shromáždění souhlasu uživatele s podmínkami použití, omezení přístupu, pokud není uděleno přijetí, a vynucení přijetí budoucích změn podmínek použití, a to na základě data poslední kolaudace a data nejnovější verze podmínek použití.

**Podmínek použití** můžou zahrnovat taky souhlas se sdílením dat s třetími stranami. V závislosti na místních nařízeních a obchodních pravidlech můžete shromáždit obě podmínky přijetí uživatele nebo můžete uživateli dovolit přijmout jednu podmínku a ne druhou.

Následující kroky popisují, jak můžete spravovat podmínek použití:

1. Poznamenejte si přijetí podmínek použití a datum přijetí pomocí Graph API a rozšířených atributů. Můžete to udělat pomocí integrovaných i vlastních uživatelských toků. Doporučujeme vytvořit a použít atributy **extension_termsOfUseConsentDateTime** a **extension_termsOfUseConsentVersion** .

2. Vytvořte povinné zaškrtávací políčko s názvem přijmout podmínky použití a zaznamenejte výsledek během registrace. Můžete to udělat pomocí integrovaných i vlastních uživatelských toků.

3. Azure AD B2C ukládá podmínek smlouvy o používání a přijetí uživatele. Můžete použít Graph API k dotazování na stav libovolného uživatele načtením atributu Extension, který se používá k zaznamenání odpovědi (například čtení **termsOfUseTestUpdateDateTime**). Můžete to udělat pomocí integrovaných i vlastních uživatelských toků.

4. Vyžádat přijetí aktualizovaných podmínek použití porovnáním data přijetí s datem poslední verze podmínek použití. Data můžete porovnat jenom pomocí vlastního toku uživatele. Použijte rozšířený atribut **extension_termsOfUseConsentDateTime** a porovnejte hodnotu s deklarací identity **termsOfUseTextUpdateDateTime**. Pokud je přijetí staré, vynutí nové přijetí zobrazením obrazovky s vlastním kontrolním výrazem. V opačném případě Zablokujte přístup pomocí logiky zásad.

5. Vyžádat přijetí aktualizovaných podmínek použití porovnáním čísla verze přijetí k nejnovějšímu přijatému číslu verze. Čísla verzí můžete porovnat jenom pomocí vlastního toku uživatele. Použijte rozšířený atribut **extension_termsOfUseConsentDateTime** a porovnejte hodnotu s deklarací **extension_termsOfUseConsentVersion**. Pokud je přijetí staré, vynutí nové přijetí zobrazením obrazovky s vlastním kontrolním výrazem. V opačném případě Zablokujte přístup pomocí logiky zásad.

Přijetí podmínek použití můžete zachytit v následujících scénářích:

- Nový uživatel se registruje. Zobrazí se podmínek použití a výsledek přijetí je uložen.
- Uživatel se přihlašuje, který dřív přijal nejnovější nebo aktivní podmínek použití. Podmínkami použití se nezobrazí.
- Uživatel se přihlašuje, kteří ještě nepřijali nejnovější nebo aktivní podmínek použití. Zobrazí se podmínek použití a výsledek přijetí je uložen.
- Uživatel se přihlašuje, který již přijal starší verzi podmínek použití, který je nyní aktualizován na nejnovější verzi. Zobrazí se podmínek použití a výsledek přijetí je uložen.

Následující obrázek znázorňuje doporučený tok uživatele:

![Diagram toku grafu znázorňující doporučený tok uživatelů pro přijetí](./media/manage-user-access/user-flow.png)

Tady je příklad souhlasu s podmínkami použití v deklaraci identity na základě data. Pokud `extension_termsOfUseConsentDateTime` je deklarace identity starší než `2025-01-15T00:00:00` , vynutí nové přijetí kontrolou `termsOfUseConsentRequired` logické deklarace identity a zobrazením obrazovky s vlastním kontrolním výrazem. 

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentDateTime" TransformationMethod="GetCurrentDateTime">
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="currentDateTime" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequired" TransformationMethod="IsTermsOfUseConsentRequired">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="termsOfUseConsentDateTime" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="termsOfUseTextUpdateDateTime" DataType="dateTime" Value="2025-01-15T00:00:00" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="result" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Následuje příklad souhlasu s podmínkami použití v deklaraci identity na základě verze. Pokud `extension_termsOfUseConsentVersion` se deklarace identity nerovná `V1` , vynutí nové přijetí kontrolou `termsOfUseConsentRequired` logické deklarace identity a zobrazením obrazovky s vlastním kontrolním výrazem.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetEmptyTermsOfUseConsentVersionForNewUser" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value=""/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentVersion" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="V1"/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="compareTo" DataType="string" Value="V1" />
      <InputParameter Id="operator" DataType="string" Value="not equal" />
      <InputParameter Id="ignoreCase" DataType="string" Value="true" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

## <a name="next-steps"></a>Další kroky

- [V Azure AD B2C povolte omezení stáří](age-gating.md).
- Informace o tom, jak odstranit a exportovat uživatelská data, najdete v tématu [Správa uživatelských dat](manage-user-data.md).
- Příklad vlastních zásad, které implementují podmínku použití, najdete v tématu [vlastní zásady B2C IEF – registrace a přihlášení pomocí výzvy k zadání podmínek použití](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-sign-up-versioned-tou).