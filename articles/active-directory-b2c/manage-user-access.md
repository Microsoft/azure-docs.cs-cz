---
title: Správa přístupu uživatelů ve službě Azure Active Directory B2C | Dokumenty společnosti Microsoft
description: Zjistěte, jak identifikovat nezletilé osoby, shromažďovat data narození a data země/oblasti a získat přijetí podmínek použití ve vaší aplikaci pomocí Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f04a3fea3801f917a3ae4aced04ef3824d1cfa82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184515"
---
# <a name="manage-user-access-in-azure-active-directory-b2c"></a>Správa přístupu uživatelů ve službě Azure Active Directory B2C

Tento článek popisuje, jak spravovat přístup uživatelů k vašim aplikacím pomocí Služby Azure Active Directory B2C (Azure AD B2C). Správa přístupu ve vaší aplikaci zahrnuje:

- Identifikace nezletilých a řízení přístupu uživatelů k vaší aplikaci.
- Vyžadování souhlasu rodičů pro nezletilé, aby používali vaše žádosti.
- Shromažďování dat o narození a zemi/oblasti od uživatelů.
- Zachycení smlouvy o podmínkách použití a gating přístup.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="control-minor-access"></a>Řízení menšího přístupu

Aplikace a organizace se mohou rozhodnout zablokovat nezletilým osobám používání aplikací a služeb, které nejsou určeny pro tuto cílovou skupinu. Alternativně se aplikace a organizace mohou rozhodnout přijmout nezletilé osoby a následně spravovat souhlas rodičů a poskytovat přípustné zkušenosti pro nezletilé, jak je diktováno obchodními pravidly a povoleno nařízením.

Pokud je uživatel identifikován jako nezletilá, můžete nastavit tok uživatele v Azure AD B2C na jednu ze tří možností:

- **Odeslat podepsanou id_token JWT zpět do aplikace**: Uživatel je registrován v adresáři a token je vrácen do aplikace. Aplikace pak pokračuje uplatněním obchodních pravidel. Žádost může například pokračovat v procesu souhlasu rodičů. Chcete-li použít tuto metodu, zvolte přijímat **ageGroup** a **consentProvidedForMinor** deklarace z aplikace.

- **Odeslat nepodepsaný token JSON do aplikace**: Azure AD B2C upozorní aplikaci, že uživatel je nezletilá a poskytuje stav souhlasu uživatele od rodičů. Aplikace pak pokračuje uplatněním obchodních pravidel. Token JSON nedokončí úspěšné ověření s aplikací. Aplikace musí zpracovat neověřeného uživatele podle deklarací obsažených v tokenu JSON, které mohou zahrnovat **název**, **e-mail**, **ageGroup**a **consentProvidedForMinor**.

- **Blokovat uživatele:** Pokud je uživatel nezletilá a nebyl poskytnut souhlas rodičů, může Azure AD B2C upozornit uživatele, že jsou blokováni. Není vydán žádný token, přístup je blokován a uživatelský účet není vytvořen během registrační cesty. Chcete-li implementovat toto oznámení, zadejte vhodnou stránku obsahu HTML/CSS, která informuje uživatele a předloží příslušné možnosti. Žádost o nové registrace nepotřebuje žádné další kroky.

## <a name="get-parental-consent"></a>Získání souhlasu rodičů

V závislosti na regulaci použití může být nutné udělit souhlas rodičů uživatelem, který je ověřen jako dospělý. Azure AD B2C neposkytuje prostředí k ověření věku jednotlivce a pak povolit ověřené dospělé udělit souhlas rodičů nezletilého. Tato zkušenost musí být poskytnuta aplikací nebo jiným poskytovatelem služeb.

Následuje příklad toku uživatele pro shromažďování souhlasu rodičů:

1. Operace [rozhraní MICROSOFT Graph API](https://docs.microsoft.com/graph/use-the-api) identifikuje uživatele jako nezletilou a vrátí uživatelská data do aplikace ve formě nepodepsaného tokenu JSON.

2. Aplikace zpracuje token JSON a zobrazí obrazovku nezletilému, upozorní je, že je vyžadován souhlas rodičů, a požádá o souhlas rodiče online.

3. Azure AD B2C zobrazuje cestu přihlášení, že uživatel můžete přihlásit normálně a vydá token pro aplikaci, která je nastavena tak, aby **zahrnovala legalAgeGroupClassification = "minorWithParentalConsent"**. Aplikace shromažďuje e-mailovou adresu rodiče a ověřuje, zda je rodič dospělý. K tomu používá důvěryhodný zdroj, například národní id office, ověření licence nebo ověření kreditní karty. Pokud je ověření úspěšné, aplikace vyzve nezletilého k přihlášení pomocí toku uživatele Azure AD B2C. Pokud je odepřen souhlas (například pokud **legalAgeGroupClassification = "minorWithoutParentalConsent"**), Azure AD B2C vrátí token JSON (ne přihlášení) do aplikace k restartování procesu souhlasu. Volitelně je možné přizpůsobit tok uživatele tak, aby nezletilý nebo dospělý mohl znovu získat přístup k účtu nezletilého zasláním registračního kódu na e-mailovou adresu nezletilého nebo e-mailovou adresu dospělé osoby v záznamu.

4. Aplikace nabízí nezletilému možnost odvolat souhlas.

5. Pokud nezletilá osoba nebo dospělá osoba odvolá souhlas, rozhraní API aplikace Microsoft Graph API lze použít ke změně **souhlasuProvidedForMinor** na **denied**. Alternativně se žádost může rozhodnout odstranit nezletilou osobu, jejíž souhlas byl odvolán. Volitelně je možné přizpůsobit tok uživatele tak, aby ověřená nezletilá osoba (nebo nadřazená osoba, která používá účet nezletilého) mohla odvolat souhlas. Azure AD B2C záznamy **consentProvidedForMinor** jako **odepřen .**

Další informace o **legalAgeGroupClassification**, **consentProvidedForMinor**a **ageGroup**naleznete v [tématech Typ prostředku uživatele](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/user). Další informace o vlastních atributech naleznete [v tématu Použití vlastních atributů ke shromažďování informací o uživatelích](user-flow-custom-attributes.md). Při řešení rozšířených atributů pomocí rozhraní Microsoft Graph API je nutné použít dlouhou verzi atributu, například *extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth:* *2011-01-01T00:00:00Z*.

## <a name="gather-date-of-birth-and-countryregion-data"></a>Shromáždit data narození a údaje o zemi/oblasti

Aplikace mohou spoléhat na Azure AD B2C shromažďovat datum narození (DOB) a země nebo oblast informace od všech uživatelů během registrace. Pokud tyto informace ještě neexistují, aplikace může požádat uživatele během další cesty ověřování (přihlášení). Uživatelé nemohou pokračovat bez poskytnutí informací o své dob a zemi nebo oblasti. Azure AD B2C používá informace k určení, zda je jednotlivec považován za nezletilou podle regulačních standardů této země nebo oblasti.

Přizpůsobený tok uživatelů může shromažďovat informace dob a země nebo oblasti a používat transformace deklarací Azure AD B2C k určení **ageGroup** a zachovat výsledek (nebo zachovat DOB a informace o zemi nebo oblasti) přímo v adresáři.

Následující kroky ukazují logiku, která se používá k výpočtu **ageGroup** od data narození uživatele:

1. Pokuste se najít zemi podle kódu země v seznamu. Pokud země není nalezena, vraťte se do **výchozího nastavení**.

2. Pokud **minorConsent** uzel je k dispozici v elementu země:

    a. Vypočítejte datum, ve které se uživatel musel narodit, aby mohl být považován za dospělého. Pokud je například aktuální datum 14. **MinorConsent**

    b. Porovnejte minimální datum narození se skutečným datem narození. Pokud je minimální datum narození před datem narození uživatele, vrátí výpočet jako výpočet věkové skupiny **menší.**

3. Pokud **minorNoConsentRequired** uzel je k dispozici v elementu země, opakujte kroky 2a a 2b pomocí hodnoty z **MinorNoConsentRequired**. Výstup 2b vrátí **MinorNoConsentRequired,** pokud je minimální datum narození před datem narození uživatele.

4. Pokud ani jeden výpočet nevrátí hodnotu true, vrátí výpočet **hodnotu Dospělý**.

Pokud aplikace spolehlivě shromáždila data DOB nebo země nebo oblasti jinými metodami, může aplikace použít rozhraní Graph API k aktualizaci uživatelského záznamu pomocí těchto informací. Například:

- Pokud je o uživateli známo, že je dospělý, aktualizujte atribut adresáře **ageGroup** hodnotou **Adult**.
- Pokud je známo, že uživatel je nezletilá, aktualizujte atribut adresáře **ageGroup** s hodnotou **Minor** a nastavte **consentProvidedForMinor**, podle potřeby.

Další informace o shromažďování dat DOB najdete [v tématu použití věkového gatingu v Azure AD B2C](basic-age-gating.md).

## <a name="capture-terms-of-use-agreement"></a>Smlouva o podmínkách používání

Při vývoji aplikace obvykle zachycovat uživatele přijetí podmínek použití v rámci svých aplikací s žádnou nebo jen menší účast z adresáře uživatelů. Je však možné použít tok uživatele Azure AD B2C ke shromáždění přijetí podmínek použití uživatelem, omezení přístupu, pokud přijetí není uděleno, a k vynucení přijetí budoucích změn podmínek použití na základě data posledního přijetí a data nejnovější verzi podmínek použití.

**Podmínky použití** mohou také zahrnovat "Souhlas se sdílením dat s třetími stranami". V závislosti na místních předpisech a obchodních pravidlech můžete shromáždit souhlas uživatele s oběma podmínkami v kombinaci nebo můžete uživateli povolit přijmout jednu podmínku, nikoli druhou.

Následující kroky popisují, jak můžete spravovat podmínky použití:

1. Zaznamenejte přijetí podmínek použití a data přijetí pomocí rozhraní Graph API a rozšířených atributů. Můžete tak učinit pomocí předdefinované a vlastní toky uživatelů. Doporučujeme vytvořit a používat **atributy extension_termsOfUseConsentDateTime** a **extension_termsOfUseConsentVersion.**

2. Vytvořte povinné zaškrtávací políčko s názvem Přijmout podmínky použití a zaznamenejte výsledek během registrace. Můžete tak učinit pomocí předdefinované a vlastní toky uživatelů.

3. Azure AD B2C ukládá podmínky použití smlouvy a přijetí uživatele. Rozhraní GRAPH API můžete použít k dotazování na stav libovolného uživatele přečtením atributu rozšíření, který se používá k zaznamenání odpovědi (například přečtěte **si termínyOfUseTestUpdateDateTime).** Můžete tak učinit pomocí předdefinované a vlastní toky uživatelů.

4. Vyžadovat přijetí aktualizovaných podmínek použití porovnáním data přijetí s datem nejnovější verze podmínek použití. Data můžete porovnat pouze pomocí vlastního toku uživatele. Použijte rozšířený atribut **extension_termsOfUseConsentDateTime**a porovnejte hodnotu s deklarací **výrazu termsOfUseTextUpdateDateTime**. Pokud je přijetí staré, vynuťte nové přijetí zobrazením obrazovky s vlastním uplatněním. V opačném případě zablokujte přístup pomocí logiky zásad.

5. Vyžadovat přijetí aktualizovaných podmínek použití porovnáním čísla verze přijetí s nejnovějším přijatým číslem verze. Čísla verzí můžete porovnat pouze pomocí vlastního toku uživatele. Použijte rozšířený atribut **extension_termsOfUseConsentDateTime**a porovnejte hodnotu s deklarací **extension_termsOfUseConsentVersion**. Pokud je přijetí staré, vynuťte nové přijetí zobrazením obrazovky s vlastním uplatněním. V opačném případě zablokujte přístup pomocí logiky zásad.

Podmínky přijetí lze zachytit v následujících scénářích:

- Nový uživatel se přihlašuje. Zobrazí se podmínky použití a uloží se výsledek přijetí.
- Uživatel se přihlašuje, který dříve přijal nejnovější nebo aktivní podmínky použití. Podmínky použití nejsou zobrazeny.
- Uživatel se přihlašuje, který ještě nepřijal nejnovější nebo aktivní podmínky použití. Zobrazí se podmínky použití a uloží se výsledek přijetí.
- Uživatel se přihlašuje, který již přijal starší verzi podmínek použití, které jsou nyní aktualizovány na nejnovější verzi. Zobrazí se podmínky použití a uloží se výsledek přijetí.

Následující obrázek znázorňuje doporučený tok uživatele:

![Diagram vývojového diagramu znázorňující doporučený tok uživatele přijetí](./media/manage-user-access/user-flow.png)

Následuje příklad souhlasu s podmínkami použití založenými na DateTime v deklaraci:

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
      <InputParameter Id="termsOfUseTextUpdateDateTime" DataType="dateTime" Value="2098-01-30T23:03:45" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="result" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Následuje příklad souhlasu s podmínkami použití založenými na verzi v deklaraci:

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

- Informace o odstranění a exportu uživatelských dat naleznete v [tématu Správa uživatelských dat](manage-user-data.md).
- Příklad vlastní zásady, která implementuje podmínky použití výzvu, naleznete [v tématu B2C IEF vlastní zásady – registrace a přihlášení s výzvou "Podmínky použití"](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-sign-up-versioned-tou).
