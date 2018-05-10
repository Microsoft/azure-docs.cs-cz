---
title: Spravovat přístup uživatelů v Azure AD B2C | Microsoft Docs
description: Zjistěte, jak identifikovat nezletilých, shromáždění datum narození a země dat a získat přijetí podmínek použití ve vaší aplikaci pomocí Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 05/04/2018
ms.author: davidmu
ms.openlocfilehash: c44135a3069966b14d8760e4daa009ab8d39ccca
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="manage-user-access-in-azure-ad-b2c"></a>Spravovat přístup uživatelů v Azure AD B2C

Tento článek obsahuje informace o tom, jak můžete spravovat přístup uživatelů do vaší aplikace s použitím Azure Active Directory (AD) B2C. Správa přístupu v aplikaci zahrnuje:

- Identifikace nezletilých a řízení přístupu na používat vaši aplikaci
- Vyžadování rodičovský souhlas pro nezletilých používat vaše aplikace
- Shromažďování dat z data narození a země od uživatele
- Zaznamenávání podmínky použití smlouvy a přístup prostřednictvím brány

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

>[!Note] 
>Tento článek obsahuje informace, které můžete použít k podpoře vaší povinností podle GDPR. Pokud hledáte obecné informace o GDPR, přečtěte si téma [GDPR části portálu služby důvěřovat](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).

## <a name="control-minor-access"></a>Méně závažné řízení přístupu

Aplikace a organizace rozhodnout blokovat nezletilých pomocí aplikací a služeb, které nejsou určeny k této cílové skupiny. Případně aplikace a organizace rozhodnout přijmout nezletilých a následně spravovat rodičovský souhlas a poskytovat povolenou prostředí pro nezletilých jako závisí na obchodní pravidla a povolenou nařízení. 

Pokud je uživatel identifikován jako vedlejší, tok uživatele v Azure AD B2C může být nastaven na jednu ze tří možností:

- **Odeslat podepsaný požadavku id_token JWT zpět do aplikace** – uživatel je zaregistrován v adresáři a token je vrácen do aplikace. Aplikace se pak pokračuje pomocí obchodní pravidla. Aplikace může například pokračovat v procesu rodičovský souhlas. K tomuto účelu můžete vybrat přijímání **ageGroup** a **consentProvidedForMinor** deklarace identity z aplikace.
- **Poslat tokenu JSON nepodepsané aplikace** – Azure AD B2C aplikace upozorní, že se uživatel menší a poskytuje stav rodičovský souhlas uživatele. Aplikace se pak pokračuje pomocí obchodní pravidla. JSON token nedokončí úspěšné ověření s aplikací. Aplikace musí zpracovat neověřený uživatel podle deklarací identity, zahrnuté v tokenu JSON, které mohou zahrnovat **název**, **e-mailu**, **ageGroup**a **consentProvidedForMinor**.
- **Blokovat uživateli** – Pokud je uživatel dílčím a rodičovský souhlas nebyl zadán.  Azure AD B2C mohou prezentovat obrazovky uživateli, které informuje o blokován.  Žádné token je vydán, zablokuje se přístup a uživatelský účet nebyl vytvořen během registrace cesty. Chcete-li tuto funkci implementovat, je zadat vhodný stránky HTML nebo CSS obsahu k informování uživatelů a existuje požadované možnosti. V žádosti o nové registrace není vyžadována žádná další akce.

## <a name="get-parental-consent"></a>Získat rodičovský souhlas

V závislosti na aplikaci nařízení může být potřeba udělit oprávnění uživatel ověřený jako dospělého rodičovský souhlas.  Azure AD B2C neposkytuje prostředí ověřte stáří jednotlivého uživatele a potom povolit ověřené pro dospělé udělit rodičovský souhlas k podverzí.  Toto prostředí je třeba zadat pomocí jiného poskytovatele služby nebo aplikace.

Následuje příklad uživatelskému toku shromažďování rodičovský souhlas:

1. [Azure Active Directory Graph API](https://msdn.microsoft.com/en-us/library/azure/ad/graph/api/api-catalog) operace identifikuje uživatele, menší a vrací data uživatele k aplikaci ve formátu unsigned tokenu JSON.
2. Aplikace zpracovává tokenu JSON a ukazuje, že obrazovce vedlejší upozornění, že rodičovský souhlas je vyžadované a požadavky souhlasu nadřazený online. 
3. Azure AD B2C ukazuje, které má uživatel pro přihlášení normálně cesty přihlášení a vydá token k aplikaci, která je nastavena na zahrnují **legalAgeGroupClassification = "minorWithParentalConsent"** shromažďuje aplikace e-mailová adresa nadřazené a ověřuje, zda je že nadřazená položka dospělého pomocí důvěryhodného zdroje, například national office, ověření licence nebo platební karty doklad ID. V případě úspěšného aplikace vyzve vedlejší se přihlásit pomocí toku uživatele Azure AD B2C. Pokud byl odepřen souhlasu (například **legalAgeGroupClassification = "minorWithoutParentalConsent"**, Azure AD B2C vrátí token JSON (ne přihlášení) do aplikace pro restartování procesu souhlasu. Volitelně lze k přizpůsobení toku uživatele, které menší nebo dospělého můžete znovu získat přístup k účtu menší poslat registrační kód na e-mailová adresa menší nebo dospělé osoby e-mailovou adresu v záznamu.
4. Aplikace nabízí možnost vedlejší odvolat souhlas.
5. Když menší nebo dospělý odvolá souhlasu, Azure AD Graph API umožňuje změnit **consetProvidedForMinor** k **odepřen**. Aplikace můžete taky rozhodnout odstranit vedlejší, jejichž souhlasu je odvolaný. Volitelně lze k přizpůsobení toku uživatele, které může ověřené vedlejší (nebo nadřazené pomocí účtu menší) odvolat souhlas. Azure AD B2C záznamy **consentProvidedForMinor** jako **odepřen**.

Další informace o **legalAgeGroupClassification**, **consentProvidedForMinor**, a **ageGroup**, najdete v části [typ prostředku uživatele](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/user). Další informace o vlastních atributů najdete v tématu [použít vlastní atributy ke shromažďování informací o uživatelích](active-directory-b2c-reference-custom-attr.md). Při řešení pomocí Azure AD Graph API rozšířené atributy, musí použít dlouho verze atributu, jako je například "extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth": "2011-01-01T00:00:00Z"

## <a name="gather-date-of-birth-and-country-data"></a>Shromážděte data narození a země dat

Aplikace může spoléhají na Azure AD B2C ke shromáždění datum narození (DOB) a země od všech uživatelů během registrace. Pokud DOB nebo informace o zemi již neexistuje, ho můžete být požádáni uživatele během další cesty ověřování (přihlášení). Uživatelé nebudou moci pokračovat bez zadání DOB a informace o zemi. Na základě země a DOB zadaný, Azure AD B2C Určuje, pokud je považován za jednotlivých vedlejší podle zákonných norem této země. 

Přizpůsobené uživatelskému toku může shromažďovat DOB a informace o zemi a používání Azure AD B2C transformace deklarací identity k určení **ageGroup** a zachovat výsledek (nebo zachování země a DOB informace přímo) v adresáři.

Následující kroky ukazují logiky, která se používá k výpočtu **ageGroup** z datum narození:

1. Pokuste se v seznamu najděte zemi kód země. Pokud není nalezen země, vrátit zpět k **výchozí**.
2. Pokud **MinorConsent** uzel je v prvku země:  <br>a. Výpočet minimální data, uživatel by musela se narodili v, aby byla považována za dospělého. Příklad: datum narození je 3/14. prosince 2015 a **MinorConsent** 18, datum narození minimální by 3/14/2000.
    <br>b. Porovnejte datum narození minimální s datum narození skutečný. Pokud je datum narození minimální před datum narození uživatele, vrátí výpočet **menší** jako výpočet stáří skupině.
3. Pokud **MinorNoConsentRequired** je přítomen v elementu země, opakováním kroků 2a a 2b uzel z hodnoty **MinorNoConsentRequired**. Vrátí výstup 2b **MinorNoConsentRequired** Pokud datum narození minimální před datum narození uživatele. 
4. Pokud ani výpočty vrátí hodnotu true, vrátí výpočet **pro dospělé**.

Pokud má aplikace spolehlivě získaná DOB nebo země data jinými metodami, může aplikace použít rozhraní GRAPH API k aktualizaci záznamu uživatele s tyto informace. Příklad:

- Pokud uživatel se označuje jako dospělého, aktualizace atributů directory **ageGroup** s hodnotou **pro dospělé**.
- Pokud uživatel se označuje jako dílčím, aktualizace atributů directory **ageGroup** s hodnotou **menší** a nastavte **consentProvidedForMinor** podle potřeby.

Další informace o shromažďování dat DOB najdete v tématu [pomocí stáří prostřednictvím brány v Azure AD B2C](basic-age-gating.md).

## <a name="capture-terms-of-use-agreement"></a>Zaznamenat smluvními podmínkami použití

Při vývoji aplikace obvykle zaznamenat uživatelské přijetí podmínek použití v rámci svých aplikací s ne, nebo jenom malé zapojení z adresáře uživatele.  Je možné, ale používat Azure AD B2C uživatele toku shromažďovat smlouvy podmínky použití, omezit přístup, pokud je povolen přijetí a vynutit přijetí budoucí změny podmínky použití podle data poslední přijetí a datum nejnovější verzi n podmínky použití.

**Podmínky použití** může také zahrnovat "Souhlas ke sdílení dat s třetími stranami."  Kladné přijetí těchto podmínek od uživatele, může být technicky shromáždit kombinovány nebo uživatel moci Přijímám není dalších v závislosti na místní předpisy a obchodní pravidla.

Následující kroky popisují možnosti pro správu podmínky použití:

1. Záznamů přijetí podmínek použití a datum přijetí pomocí rozhraní Graph API a rozšířených atributů. To lze provést pomocí obou toky předdefinované a vlastní uživatele. Doporučujeme vytvořit a použít **extension_termsOfUseConsentDateTime** a **extension_termsOfUseConsentVersion** atributy.
2. Vytvořit požadované zaškrtávací políčko s názvem "Přijměte podmínky použití" a zaznamenejte výsledek během registrace. To lze provést pomocí obou toky předdefinované a vlastní uživatele.
3. Azure AD B2C ukládá podmínky použití smlouvy a souhlasu. Rozhraní Graph API je možné zadat dotaz na stav libovolného uživatele načtením rozšíření atribut použitý k zaznamenání odpovědi, například najdete **termsOfUseTestUpdateDateTime**. To lze provést pomocí obou toky předdefinované a vlastní uživatele.
4. Vyžadovat přijetí aktualizovaných podmínek použití tak, že porovnáte datum přijetí k datu nejnovější verzi podmínky použití. To lze provést pouze pomocí vlastní uživatelskému toku. Pomocí rozšířených atributu **extension_termsOfUseConsentDateTime** a porovnat hodnotu deklarace identity z **termsOfUseTextUpdateDateTime**, pokud je v minulosti přijetí můžete vynutit nové přijetí Samoobslužné prohlašovanou obrazovky, jinak, blokujte přístup pomocí Logika zásad.
5. Vyžadovat přijetí aktualizovaných podmínek použití tak, že porovnáte číslo verze přijetí poslední číslo přijatá verze. To lze provést pouze pomocí vlastní uživatelskému toku. Pomocí rozšířených atributu **extension_termsOfUseConsentDateTime** a porovnat hodnotu deklarace identity z **extension_termsOfUseConsentVersion**, pokud je v minulosti přijetí můžete vynutit nové přijetí Samoobslužné prohlašovanou obrazovky, jinak, blokujte přístup pomocí Logika zásad.

Zaznamenání podmínky použití souhlasu lze zobrazit v rámci následující scénáře:

- Je registraci nového uživatele. Podmínky použití jsou zobrazeny souhlasu výsledek je uložen.
- Uživatel je přihlášení a již dříve přijali souhlas pro nejnovější nebo active podmínky smlouvy. Podmínky použití nejsou zobrazeny.
- Uživatel je přihlášení a není již byla přijata souhlas pro nejnovější nebo active podmínky použití. Podmínky použití jsou zobrazeny souhlasu výsledek je uložen.
- Uživatel je přihlášení a má již byla přijata souhlas pro starší podmínky použití, která se nyní aktualizuje na novější verzi. Podmínky použití jsou zobrazeny souhlasu výsledek je uložen.

Následující obrázek znázorňuje doporučené uživatelskému toku:

![přijetí uživatelskému toku](./media/manage-user-access/user-flow.png) 

Následuje příklad DateTime na základě podmínky použití souhlasu v deklaraci identity:

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

Následuje příklad verzi na základě podmínky použití souhlasu v deklaraci identity:

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

- Zjistěte, jak odstranit a export dat uživatele v [správu dat uživatele](manage-user-data.md)
