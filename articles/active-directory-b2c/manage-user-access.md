---
title: Správa přístupu uživatelů v Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak identifikovat nezletilé osoby, shromažďovat data narození a zemi data a získat přijetí podmínek použití ve vaší aplikaci pomocí Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6709fb8ae328f749b367c58f95b8a9ef8da9bc65
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42054234"
---
# <a name="manage-user-access-in-azure-active-directory-b2c"></a>Správa přístupu uživatelů v Azure Active Directory B2C

Tento článek popisuje, jak spravovat přístup uživatelů k aplikacím pomocí služby Azure Active Directory (Azure AD) B2C. Správa přístupu v aplikaci zahrnuje:

- Identifikace nezletilé osoby a řízení přístupu uživatelů k aplikaci.
- Vyžaduje svolení rodičů pro nezletilým používají vaše aplikace.
- Shromažďování dat od uživatelů narození a zemi.
- Zachytávání smlouvy s podmínkami použití a přístup prostřednictvím brány.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="control-minor-access"></a>Dílčí řízení přístupu

Aplikace a organizace rozhodnout zablokovat nezletilým pomocí aplikací a služeb, které nejsou určeny pro tuto cílovou skupinu. Můžete také aplikace a organizace rozhodnout přijmout nezletilé osoby a následně spravovat svolení rodičů a zajištění přípustné prostředí pro nezletilým určený obchodní pravidla a povoluje nařízení. 

Pokud je uživatel identifikován jako za, můžete nastavit tok uživatele v Azure AD B2C na jednu ze tří možností:

- **Odeslat podepsaný token JWT id_token zpět do aplikace**: uživatel je zaregistrován v adresáři, a vrátí se token do aplikace. Aplikace pak pokračuje s použitím obchodních pravidel. Například může aplikace pokračovat v procesu svolení rodičů. Pokud chcete použít tuto metodu, zvolte pro příjem **ageGroup** a **consentProvidedForMinor** deklarace identity z aplikace.

- **Odeslat do aplikace bez znaménka tokenu JSON**: Azure AD B2C upozorní aplikaci, že uživatel je za a obsahuje informace o stavu uživatele svolení rodičů. Aplikace pak pokračuje s použitím obchodních pravidel. JSON token nedokončí úspěšné ověřování s aplikací. Aplikace musí zpracovat neověřený uživatel podle deklarací identity zahrnuje do tokenu JSON, které mohou zahrnovat **název**, **e-mailu**, **ageGroup**a **consentProvidedForMinor**.

- **Zablokuje uživateli**: Pokud je uživatel za a nebyl zadán svolení rodičů, Azure AD B2C můžete uživatele upozornit, že uživatel je blokován. Žádný token vystaven, zablokuje se přístup a uživatelský účet není vytvořeno během registrace cesty. K implementaci toto oznámení, poskytují vhodné obsahu stránky HTML/CSS informovat uživatele a k dispozici odpovídající možnosti. V žádosti o nové registrace není potřeba žádná další akce.

## <a name="get-parental-consent"></a>Získat svolení rodičů

V závislosti na aplikaci nařízení může být nutné svolení rodičů obdržet uživatelem, který je ověřený jako dospělého. Azure AD B2C je v prostředí pro ověření věku jednotlivých zákazníků a pak povolit ověřené adult udělit svolení rodičů za neposkytuje. Toto prostředí musí poskytnout aplikace nebo jiného poskytovatele služeb.

Následuje příklad pro shromažďování svolení rodičů tok uživatele:

1. [Azure Active Directory Graph API](https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog) identifikuje uživatele jako datový typ za operace a vrací data uživatele do aplikace v podobě bez znaménka tokenu JSON.

2. Aplikace zpracovává tokenu JSON a zobrazí obrazovku nezletilý oznamující mu, vyžaduje se souhlas rodiče a vyžádání souhlasu nadřazeného online. 

3. Azure AD B2C zobrazí přihlašovací cestu, že uživatel může přihlásit k obvykle a vydá token k aplikaci, která je nastavena na zahrnují **legalAgeGroupClassification = "minorWithParentalConsent"**. Aplikace shromažďuje e-mailovou adresu nadřazeného elementu a ověřuje, zda nadřazené Dospělý. K tomu použije důvěryhodného zdroje, například národní office, ověřování licencí nebo platební karty důkazu ID. Pokud je ověření úspěšné, aplikace vyzve nezletilé osoby k přihlášení pomocí Azure AD B2C tok uživatele. Pokud byl odepřen souhlasu (například pokud **legalAgeGroupClassification = "minorWithoutParentalConsent"**), Azure AD B2C vrátí token JSON (ne přihlášení) k restartování procesu souhlasu aplikace. Je možné Volitelně můžete k přizpůsobení toku uživatele tak, aby za nebo dospělého lze získat přístup k účtu menší odesláním registrační kód menší e-mailovou adresu nebo e-mailovou adresu pro dospělé v záznamu.

4. Aplikace nabízí možnost nezletilý k odvolání souhlasu.

5. Když menší nebo dospělá osoba odvolá souhlas, Azure AD Graph API můžete použít ke změně **consentProvidedForMinor** k **odepřen**. Aplikace můžete také rozhodnout odstranit podverze, jehož vyjádření souhlasu se odvolal. Je možné Volitelně můžete k přizpůsobení toku uživatele tak, aby ověřený menší (nebo nadřazené položky, která používá účet menší) můžete odvolat souhlas. Azure AD B2C záznamy **consentProvidedForMinor** jako **odepřen**.

Další informace o **legalAgeGroupClassification**, **consentProvidedForMinor**, a **ageGroup**, naleznete v tématu [typ prostředku uživatele](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/user). Další informace o uživatelských atributů, které najdete v tématu [použití vlastních atributů ke shromažďování informací o uživatelích](active-directory-b2c-reference-custom-attr.md). Při adresování rozšířené atributy s využitím Azure AD Graph API, musíte použít dlouhou verzi atribut, například *extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth*: *2011-01-01T00:00:00Z*.

## <a name="gather-date-of-birth-and-country-data"></a>Získat datum narození a zemi dat.

Aplikace může záviset na Azure AD B2C ke shromáždění datum narození (DOB) a země informace ze všech uživatelů během registrace. Pokud tyto informace již neexistuje, aplikace může si ji vyžádat od uživatele během další cesty ověřování (přihlášení). Uživatele nelze pokračovat bez zadání jejich DOB a informace o zemi. Azure AD B2C používá informace k určení, zda jednotlivých se považuje za menší podle zákonných norem této země. 

Vlastní uživatelské toku může shromažďovat DOB a informace o zemi a používání Azure AD B2C transformace deklarací identity k určení **ageGroup** a zachovat výsledek (nebo DOB uchování a informace o zemi přímo) v adresáři.

Následující kroky ukazují, logika, která se používá k výpočtu **ageGroup** z datum narození uživatele:

1. Zkuste najít směrové číslo země v seznamu. Pokud se nenajde země, vrátit zpět k **výchozí**.

2. Pokud **MinorConsent** uzlu je k dispozici v elementu země:

    a. Vypočítejte datum, které uživatel musí narodila na považovat Dospělý. Například, pokud je aktuální datum 14. března 2015 a **MinorConsent** je 18, datum narození musí být nejpozději do 14. března 2000.

    b. Porovnejte datum narození minimální datum narození skutečný. Pokud je minimální narozeniny než datum narození uživatele, vrátí výpočtu **menší** jako kategorie age group výpočet.

3. Pokud **MinorNoConsentRequired** uzlu je k dispozici v elementu země, opakujte kroky 2 a 2b z hodnoty **MinorNoConsentRequired**. Vrátí výstupní 2b **MinorNoConsentRequired** Pokud datum narození minimální datum narození uživatele. 

4. Pokud ani výpočtu vrátí hodnotu true, vrátí výpočtu **dospělé**.

Pokud má aplikace spolehlivě získaná DOB nebo data o zemích jinými metodami, může aplikace použít rozhraní Graph API k aktualizaci záznamu uživatele s těmito informacemi. Příklad:

- Pokud uživatel je znám jako Dospělý, aktualizujte atribut adresáře **ageGroup** s hodnotou **dospělé**.
- Pokud uživatel je znám jako za, aktualizujte atribut directory **ageGroup** s hodnotou **menší** a nastavte **consentProvidedForMinor**podle potřeby.

Další informace o shromažďování dat DOB najdete v tématu [použít věku v Azure AD B2C](basic-age-gating.md).

## <a name="capture-terms-of-use-agreement"></a>Zachycení smlouva o podmínkách použití

Když vyvíjíte aplikaci, obvykle zachycení uživatelů přijetí podmínek použití v rámci jejich aplikací bez nebo jen drobný účast v adresáři uživatele. To je ale možné, použijte tok, který uživatele Azure AD B2C k shromáždění uživatele přijetí podmínek použití, omezit přístup, pokud není přijetí udělen a vynutit přijetí budoucí změny s podmínkami použití, na základě data poslední přijetí a data  nejnovější verzi podmínek použití.

**Podmínky použití** může také obsahovat "Tím souhlas se sdílením dat s třetími stranami." V závislosti na místní předpisy a obchodních pravidel můžete získat uživatele přijetí obě podmínky kombinovat nebo můžete povolit uživateli, aby přijal jednu podmínku a ne na druhé.

Následující kroky popisují, jak můžete spravovat podmínky použití:

1. Přijetí podmínek použití a datum přijetí zaznamenání pomocí rozhraní Graph API a rozšířených atributů. Můžete tak učinit pomocí obou toky uživatelů předdefinované a vlastní. Doporučujeme vytvořit a použít **extension_termsOfUseConsentDateTime** a **extension_termsOfUseConsentVersion** atributy.

2. Vytvořit požadované zaškrtávací políčko s názvem "Přijmout podmínky použití" a zaznamenat výsledek během registrace. Můžete tak učinit pomocí obou toky uživatelů předdefinované a vlastní.

3. Azure AD B2C ukládá podmínky použití smlouvy a uživatel souhlasí s ujednáními. Rozhraní Graph API k dotazování můžete použít pro stav ze všech uživatelů načtením atributu rozšíření, který se používá k zaznamenání odpovědi (třeba číst **termsOfUseTestUpdateDateTime**). Můžete tak učinit pomocí obou toky uživatelů předdefinované a vlastní.

4. Vyžadovat přijetí aktualizovaných podmínek použití porovnáním datum přijetí datum nejnovější verzi podmínek použití. Data můžete porovnat pouze s použitím tok vlastní uživatele. Použití rozšířeného atributu **extension_termsOfUseConsentDateTime**a porovnat hodnotu deklarace identity z **termsOfUseTextUpdateDateTime**. Pokud přijetí je starý, zobrazení s vlastním potvrzením obrazovky vynuťte nový přijetí. V opačném případě blokování přístupu pomocí Logika zásad.

5. Vyžadovat přijetí aktualizovaných podmínek použití porovnáním číslo verze přijetí poslední číslo přijatá verze. Čísla verzí můžete porovnat pouze s použitím tok vlastní uživatele. Použití rozšířeného atributu **extension_termsOfUseConsentDateTime**a porovnat hodnotu deklarace identity z **extension_termsOfUseConsentVersion**. Pokud přijetí je starý, zobrazení s vlastním potvrzením obrazovky vynuťte nový přijetí. V opačném případě blokování přístupu pomocí Logika zásad.

Můžete zaznamenat podmínky použití přijetí v následujících případech:

- Je registraci nového uživatele. Podmínky použití jsou zobrazeny, a přijetí výsledek je uložen.
- Uživatel se přihlašuje kdo dříve přijali nejnovější nebo aktivní podmínky použití. Podmínky použití se nezobrazují.
- Uživatel se přihlašuje již kdo nepřijal nejnovější nebo aktivní podmínky použití. Podmínky použití jsou zobrazeny, a přijetí výsledek je uložen.
- Uživatel se přihlašuje kteří už přijali starší verze podmínek použití, které jsou teď aktualizovaný na nejnovější verzi. Podmínky použití jsou zobrazeny, a přijetí výsledek je uložen.

Následující obrázek znázorňuje tok doporučené uživatele:

![Tok uživatele přijetí](./media/manage-user-access/user-flow.png) 

Následuje příklad na základě data a času souhlas s podmínkami použití v deklaraci identity:

```
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

Následuje příklad verzi na základě souhlas s podmínkami použití v deklaraci identity:

```
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

## <a name="next-steps"></a>Další postup

- Zjistěte, jak odstranit a export dat uživatele, najdete v článku [spravovat uživatelská data](manage-user-data.md).
