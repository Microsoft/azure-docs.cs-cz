---
title: Zapisují se výrazy pro mapování atributů v Azure Active Directory | Microsoft Docs
description: Naučte se používat výraz mapování k transformaci hodnoty atributu do formátu přijatelné během automatického zřizování objektů aplikace SaaS ve službě Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: b13c51cd-1bea-4e5e-9791-5d951a518943
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.openlocfilehash: 24b20766997a9a41956f575f6cab8ee5ef0d9e25
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035811"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Zapisují se výrazy pro mapování atributů v Azure Active Directory
Při konfiguraci zřizování k aplikaci SaaS, je jeden z typů mapování atributů, které můžete zadat mapování u výrazu. Pro tyto musíte napsat skript jako výraz, který umožňuje transformovat data uživatelů do formátů, které jsou více přijatelné pro aplikace SaaS.

## <a name="syntax-overview"></a>Přehled syntaxe
Syntaxe pro výrazy pro mapování atributů je připomínající jazyka Visual Basic pro aplikace (VBA) funkce.

* Celý výraz musí být definován z hlediska funkcí, které se skládají z název, za nímž následují argumenty v závorce: <br>
  *%{FunctionName/ (<< argument 1 >>, <<argument N>>)*
* Funkce v sobě navzájem může vnořit. Příklad: <br> *FunctionOne (FunctionTwo (<<argument1>>))*
* Tři různé typy argumentů můžete předat do funkce:
  
  1. Atributy, které musí být uzavřena do odmocnina hranaté závorky. Příklad: [attributeName]
  2. Řetězcové konstanty, které musí být uzavřena do uvozovek. Například: "USA"
  3. Další funkce. Příklad: FunctionOne (<<argument1>>, FunctionTwo (<<argument2>>))
* Pro řetězcové konstanty Pokud potřebujete zpětné lomítko (\) nebo uvozovky (") v řetězci, ho, je nutné uvést symbolem zpětné lomítko (\). Například: "název společnosti: \"Contoso\""

## <a name="list-of-functions"></a>Seznam funkcí
[Připojit](#append) &nbsp; &nbsp; &nbsp; &nbsp; [FormatDateTime](#formatdatetime) &nbsp; &nbsp; &nbsp; &nbsp; [připojení](#join) &nbsp; &nbsp; &nbsp; &nbsp; [Mid](#mid) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [NormalizeDiacritics](#normalizediacritics) [není](#not) &nbsp; &nbsp; &nbsp; &nbsp; [nahradit](#replace) &nbsp; &nbsp; &nbsp; &nbsp; [SingleAppRoleAssignment](#singleapproleassignment) &nbsp; &nbsp; &nbsp; &nbsp; [StripSpaces](#stripspaces) &nbsp; &nbsp; &nbsp; &nbsp; [Přepínače](#switch)

- - -
### <a name="append"></a>Připojit
**Funkce:**<br> Append(Source, suffix)

**Popis:**<br> Přebírá hodnotu řetězce zdroje a připojí přípona na konec.

**Parametry:**<br> 

| Název | Požadované / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |Obvykle název atributu, ze zdrojového objektu |
| **přípona** |Požaduje se |Řetězec |Řetězec, který má být připojen na konec zdrojové hodnoty. |

- - -
### <a name="formatdatetime"></a>FormatDateTime
**Funkce:**<br> FormatDateTime (zdroj, inputFormat outputFormat.)

**Popis:**<br> Přebírá řetězec data z jednoho formátu a převede jej na do jiného formátu.

**Parametry:**<br> 

| Název | Požadované / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |Obvykle název atributu, ze zdrojového objektu. |
| **inputFormat** |Požaduje se |Řetězec |Očekávaný formát zdrojové hodnoty. Podporovaných formátů naleznete v části [ http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx ](http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Požaduje se |Řetězec |Formát výstupního data. |

- - -
### <a name="join"></a>Spojit
**Funkce:**<br> Připojení (oddělovač, zdroj1, zdroj2,...)

**Popis:**<br> Join() je podobná Append(), s tím rozdílem, že ho můžete kombinovat více **zdroj** hodnoty řetězce do jednoho řetězce, a všechny hodnoty oddělené bránou **oddělovače** řetězec.

Pokud je jedna z hodnot zdroj vícehodnotový atribut, pak každá hodnota v tento atribut bude propojeny, oddělené oddělovačem hodnota.

**Parametry:**<br> 

| Název | Požadované / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Oddělovač** |Požaduje se |Řetězec |Řetězec slouží k oddělení zdroj hodnoty, když jsou zřetězeny do jednoho řetězce. Může být "" Pokud žádné oddělovače je vyžadován. |
| ** zdroj1... zdrojN ** |Požadované proměnné počet pokusů |Řetězec |Řetězec hodnoty, které mají být propojeny. |

- - -
### <a name="mid"></a>Mid –
**Funkce:**<br> Mid (zdroj, spuštění, délka)

**Popis:**<br> Vrátí dílčí řetězec zdrojové hodnoty. Dílčí řetězec je řetězec, který obsahuje jenom některé znaky z zdrojový řetězec.

**Parametry:**<br> 

| Název | Požadované / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |Obvykle název atributu. |
| **start** |Požaduje se |integer |V indexu **zdroj** řetězec, kde by se měl spustit dílčí řetězec. První znak v řetězci bude mít index 1, druhý znak bude mít index 2 a tak dále. |
| **Délka** |Požaduje se |integer |Délka dílčí řetězec. Pokud délka skončí mimo **zdroj** řetězec, funkce vrátí dílčí řetězec z **spustit** indexu do konce **zdroj** řetězec. |

- - -
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Funkce:**<br> NormalizeDiacritics(source)

**Popis:**<br> Vyžaduje jeden argument řetězec. Vrátí řetězec, ale s znaky diakritická nahradit ekvivalentní-diakritických znaků. Obvykle používá pro převod názvy první a poslední názvy diakritických znaků (diakritiku) do platné hodnoty, které lze použít v různých identifikátory uživatel například hlavních názvů uživatelů, názvy účtů SAM a e-mailové adresy.

**Parametry:**<br> 

| Název | Požadované / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec | Obvykle křestní jméno nebo poslední atribut name. |

- - -
### <a name="not"></a>Není
**Funkce:**<br> Not(Source)

**Popis:**<br> Převrátí logickou hodnotu **zdroj**. Pokud **zdroj** hodnota je "*True*", vrátí "*False*". Jinak vrátí "*True*".

**Parametry:**<br> 

| Název | Požadované / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Logická hodnota řetězce |Očekávaný **zdroje** jsou hodnoty "True" nebo "Nepravda"... |

- - -
### <a name="replace"></a>Nahradit
**Funkce:**<br> Nahraďte (zdroj, oldValue, regexPattern, regexGroupName, zastaralá, replacementAttributeName, šablony)

**Popis:**<br>
Nahradí hodnoty v řetězci. Funguje jinak v závislosti na parametry zadané:

* Když **oldValue** a **zastaralá** jsou k dispozici:
  
  * Nahradí všechny výskyty oldValue ve zdroji zastaralá
* Když **oldValue** a **šablony** jsou k dispozici:
  
  * Nahradí všechny výskyty **oldValue** v **šablony** s **zdroj** hodnota
* Když **regexPattern**, **regexGroupName**, **zastaralá** jsou k dispozici:
  
  * Nahradí všechny hodnoty odpovídající oldValueRegexPattern v zdrojový řetězec s zastaralá
* Když **regexPattern**, **regexGroupName**, **replacementPropertyName** jsou k dispozici:
  
  * Pokud **zdroj** nemá žádnou hodnotu **zdroj** je vrácen
  * Pokud **zdroj** má hodnotu, používá **regexPattern** a **regexGroupName** extrahovat nahrazující hodnotou z vlastnost s **replacementPropertyName** . Nahrazující hodnotou se vrátí jako výsledek

**Parametry:**<br> 
| Název | Požadované / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |Obvykle název atributu, ze zdrojového objektu. |
| **oldValue** |Nepovinné |Řetězec |Hodnota, která má být nahrazen v **zdroj** nebo **šablony**. |
| **regexPattern** |Nepovinné |Řetězec |Vzor regulárního výrazu pro hodnota, která má být nahrazen v **zdroj**. Nebo, pokud je použita replacementPropertyName, vzor se získat hodnoty z vlastnosti nahrazení. |
| **regexGroupName** |Nepovinné |Řetězec |Název skupiny uvnitř **regexPattern**. Jenom v případě, že se používá replacementPropertyName, jsme jako zastaralá z vlastnosti nahrazení extrahuje hodnotu této skupiny. |
| **Zastaralá** |Nepovinné |Řetězec |Nová hodnota nahradit starý s. |
| **replacementAttributeName** |Nepovinné |Řetězec |Název atributu, který má být použit pro nahrazující hodnotou, pokud zdroj nemá žádnou hodnotu. |
| **Šablony** |Nepovinné |Řetězec |Když **šablony** je zadána hodnota, podíváme se **oldValue** uvnitř šablony a nahraďte ji metodou hodnota zdroje. |

- - -
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Funkce:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Popis:**<br> Vyžaduje jeden argument řetězec. Vrátí řetězec, ale žádné repalced diakritických znaků s ekvivalentní-diakritických znaků.

**Parametry:**<br> 

| Název | Požadované / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Požaduje se |Řetězec |**[appRoleAssignments]**  objektu. |

- - -
### <a name="stripspaces"></a>StripSpaces
**Funkce:**<br> StripSpaces(source)

**Popis:**<br> Odebere všechny mezery ("") znaků z řetězce zdroje.

**Parametry:**<br> 

| Název | Požadované / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |**Zdroj** hodnotu aktualizovat. |

- - -
### <a name="switch"></a>Přepínač
**Funkce:**<br> Přepínače (zdroj, defaultValue, key1, hodnota1, key2, hodnota2,...)

**Popis:**<br> Když **zdroj** hodnota odpovídá **klíč**, vrátí **hodnotu** pro tento **klíč**. Pokud **zdroj** hodnota se neshoduje se všechny klíče, vrátí **defaultValue**.  **Klíč** a **hodnotu** parametry musí vždy pocházejí v párech. Funkce očekává vždy sudý počet parametrů.

**Parametry:**<br> 

| Název | Požadované / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |**Zdroj** hodnotu aktualizovat. |
| **Výchozí hodnota** |Nepovinné |Řetězec |Výchozí hodnota má být použit při zdroj neodpovídá žádné klíče. Může být prázdný řetězec (""). |
| **key** |Požaduje se |Řetězec |**Klíč** k porovnání **zdroj** hodnotu s. |
| **value** |Požaduje se |Řetězec |Nahrazující hodnotou pro **zdroj** odpovídající klíč. |

## <a name="examples"></a>Příklady
### <a name="strip-known-domain-name"></a>Název domény známý pruhu
Je třeba odstranit název známé domény e-mailu uživatele k získání uživatelského jména. <br>
Například pokud je doména "contoso.com", pak můžete použít následující výraz:

**Výraz:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Ukázkové vstup / výstup:** <br>

* **VSTUP** (e-mailu): "john.doe@contoso.com"
* **VÝSTUP**: "john.doe"

### <a name="append-constant-suffix-to-user-name"></a>Připojit konstantní příponu k uživatelské jméno
Pokud používáte izolovaného prostoru služby Salesforce, možná budete muset připojit další příponu na všechny uživatelská jména před synchronizací je.

**Výraz:** <br>
`Append([userPrincipalName], ".test"))`

**Ukázka vstupu a výstupu:** <br>

* **VSTUP**: (userPrincipalName): "John.Doe@contoso.com"
* **VÝSTUP**: "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Generovat alias uživatele spojováním části křestní jméno a příjmení
Budete muset vygenerovat uživatele alias provedením nejprve 3 písmena křestní jméno uživatele a prvních 5 písmena příjmení uživatele.

**Výraz:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Ukázka vstupu a výstupu:** <br>

* **VSTUP** (givenName): "Jan"
* **VSTUP** (Přezdívka): "Doe"
* **VÝSTUP**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Odebrat znaky s diakritikou z řetězce
Potřebujete nahradit znaků obsahující značky zvýraznění s ekvivalentní znaky, které neobsahují žádný značky zvýraznění.

**Výraz:** <br>
NormalizeDiacritics([givenName])

**Ukázka vstupu a výstupu:** <br>

* **VSTUP** (givenName): "Zoë"
* **VÝSTUP**: "Zoe"

### <a name="output-date-as-a-string-in-a-certain-format"></a>Výstupní data jako řetězec v určitém formátu
Chcete odesílat data do aplikace SaaS v určitém formátu. <br>
Například které chcete formátovat data pro ServiceNow.

**Výraz:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Ukázka vstupu a výstupu:**

* **VSTUP** (extensionAttribute1): "20150123105347.1Z"
* **VÝSTUP**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Nahraďte hodnotu podle předdefinovanou sadu voleb
Je třeba definovat časové pásmo uživatele na základě stavu kódu uložené ve službě Azure AD. <br>
Pokud kód stavu neodpovídá žádné předdefinované možnosti, použijte výchozí hodnotu "Austrálie/Sydney".

**Výraz:** <br>

`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Ukázka vstupu a výstupu:**

* **VSTUP** (stav): "QLD"
* **VÝSTUP**: "Austrálie/Brisbane"

## <a name="related-articles"></a>Související články
* [Rejstřík článků o správě aplikací ve službě Azure Active Directory](active-directory-apps-index.md)
* [Automatizovat uživatele zřízení nebo zrušení zřízení k aplikacím SaaS](active-directory-saas-app-provisioning.md)
* [Přizpůsobení mapování atributů pro zřizování uživatelů](active-directory-saas-customizing-attribute-mappings.md)
* [Filtry pro zřizování uživatelů oborů](active-directory-saas-scoping-filters.md)
* [Zapnutí automatického zřizování uživatelů a skupin ze služby Azure Active Directory do aplikací pomocí SCIM](manage-apps/use-scim-to-provision-users-and-groups.md)
* [Účet zřizování oznámení](active-directory-saas-account-provisioning-notifications.md)
* [Seznam kurzů k integraci aplikací SaaS](saas-apps/tutorial-list.md)

