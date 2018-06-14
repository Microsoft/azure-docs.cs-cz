---
title: Přizpůsobení operačního systému konfigurace zabezpečení v Azure Security Center (Preview) | Microsoft Docs
description: Tento článek ukazuje, jak přizpůsobit hodnocení security center
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2018
ms.author: terrylan
ms.openlocfilehash: f12441a960db9f1c45bca2a5b95f3669923c7e3d
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2018
ms.locfileid: "28200006"
---
# <a name="customize-os-security-configurations-in-azure-security-center-preview"></a>Přizpůsobení operačního systému konfigurace zabezpečení v Azure Security Center (Preview)

Tento návod ukazuje, jak přizpůsobit hodnocení konfigurace zabezpečení operačního systému v Azure Security Center.

## <a name="what-are-os-security-configurations"></a>Jaké jsou konfigurace zabezpečení operačního systému?

Azure Security Center sledovat konfigurace zabezpečení použitím sadu [více než 150 doporučená pravidla](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) pro posílení zabezpečení operačního systému, včetně pravidel souvisejících s brány firewall, auditování, zásady pro hesla a další. Pokud je počítač je zjištěn ohrožené konfigurace, Security Center generuje doporučení zabezpečení.

Přizpůsobením pravidla můžete řídit organizace, které možnosti konfigurace jsou pro jejich prostředí vhodnější. Můžete nastavit zásady, přizpůsobené hodnocení a následně použít na všech příslušných počítačích v rámci předplatného.

> [!NOTE]
> - V současné době je k dispozici pro Windows Server 2008, 2008 R2, 2012 a 2012 R2 verze operační systémy pouze přizpůsobení konfigurace zabezpečení operačního systému.
> - Konfigurace platí pro všechny virtuální počítače a počítače, které jsou připojené k všechny pracovní prostory v části vybraného předplatného.
> - Přizpůsobení konfigurace zabezpečení operačního systému je k dispozici pouze na úrovni standard Security Center.
>
>

Pravidla konfigurace zabezpečení operačního systému můžete přizpůsobit povolení a zakázání konkrétní pravidlo, změna požadované nastavení pro existující pravidlo nebo přidáním nové pravidlo, které je založené na typech podporovaných pravidlo (registr, zásady auditu a zásady zabezpečení). V současné době požadované nastavení musí být přesná hodnota.

Nová pravidla musí být ve stejné formátu a struktura jako ostatní existující pravidla stejného typu.

> [!NOTE]
> Chcete-li přizpůsobit konfigurace zabezpečení operačního systému, musí být přiřazena role z *vlastník předplatného*, *předplatné Přispěvatel*, nebo *správce zabezpečení*.
>
>

## <a name="customize-the-default-os-security-configuration"></a>Přizpůsobit výchozí konfigurace zabezpečení operačního systému

Chcete-li přizpůsobit výchozí konfigurace zabezpečení operačního systému ve službě Security Center, postupujte takto:

1.  Otevřete řídicí panel **Security Center**.

2.  V levém podokně vyberte **zásady zabezpečení**.  
    **Security Center – zásady zabezpečení** otevře se okno.

    ![Seznam zásad zabezpečení](media/security-center-customize-os-security-config/open-security-policy.png)

3.  Vyberte předplatné, se kterou chcete provést vlastní nastavení pro.

4. V části **součásti zásad**, vyberte **upravit konfigurace zabezpečení**.  
    **Upravit konfigurace zabezpečení** otevře se okno.

    ![Okna "Upravit konfigurace zabezpečení"](media/security-center-customize-os-security-config/blade.png)

5. V pravém podokně postupujte podle kroků pro stahování, úpravy a nahráváte upravený soubor.

   > [!NOTE]
   > Ve výchozím nastavení, je konfigurační soubor, který stáhnete v *json* formátu. Pokyny týkající se tento soubor upravovali, přejděte na [přizpůsobení konfiguračního souboru](#customize-the-configuration-file).
   >

   Po uložení souboru úspěšně, konfigurace platí pro všechny virtuální počítače a počítače, které jsou připojené k všechny pracovní prostory v rámci předplatného. Proces obvykle trvá několik minut, ale může trvat déle, v závislosti na velikosti infrastruktury.

6. Potvrzení změn, vyberte **Uložit**. Zásady, jinak se neuloží.

    ![Tlačítko Uložit](media/security-center-customize-os-security-config/save-successfully.png)

V libovolném bodě můžete resetovat aktuální konfiguraci zásad do výchozího stavu. Uděláte to tak, v **pravidla konfigurace zabezpečení upravit OS** vyberte **resetovat**. Potvrďte výběrem této možnosti **Ano** v místním okně potvrzení.

![Okno pro potvrzení resetování](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>Přizpůsobení konfiguračního souboru

V souboru přizpůsobení všechny podporované verze operačního systému obsahuje sadu pravidel nebo ruleset. Každý ruleset má svou vlastní název a jedinečné ID, jak je znázorněno v následujícím příkladu:

![Sada pravidel pro název a ID](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> Tento příklad souboru bylo upraveno v sadě Visual Studio, ale také můžete použít Poznámkový blok Pokud máte modul plug-in JSON prohlížeč nainstalován.
>
>

Při úpravách souboru úprav, můžete upravit jedno pravidlo nebo všechny z nich. Každý ruleset zahrnuje *pravidla* oddíl, který je rozdělené do tří kategorií: registr, zásady auditu a zásady zabezpečení, jak je vidět tady:

![Tři kategorie ruleset](media/security-center-customize-os-security-config/rules-section.png)

Každá kategorie obsahuje vlastní sadu atributů. Můžete změnit následující atributy:

- **expectedValue**: datového typu pole tento atribut musí odpovídat podporované hodnoty za *typ pravidla*, například:

  - **baselineRegistryRules**: hodnota by měla odpovídat [regValueType](https://msdn.microsoft.com/library/windows/desktop/ms724884) který je definován v tomto pravidle.

  - **baselineAuditPolicyRules**: použijte jednu z následujících hodnot řetězec:

    - *Úspěchy a chyby*

    - *Úspěch*

  - **baselineSecurityPolicyRules**: použijte jednu z následujících hodnot řetězec:

    - *Žádná*

    - Seznam povolených skupin uživatelů, například: *správci*, *Backup Operators*

-   **Stav**: řetězec může obsahovat možnosti *zakázané* nebo *povoleno*. Pro tuto verzi privátní Preview verzi řetězec je malá a velká písmena.

Jedná se o pouze pole, které lze konfigurovat. Pokud jste porušují formát souboru nebo velikost, nebudete moci uložit změny. K této chybě dojde, pokud soubor nelze zpracovat:

![Chybová zpráva Konfigurace zabezpečení](media/security-center-customize-os-security-config/invalid-json.png)

Seznam dalších potenciální chyby najdete v tématu [kódy chyb](#error-codes).

Následující tři části obsahují příklady předchozích pravidel. *ExpectedValue* a *stavu* atributy lze změnit.

**baselineRegistryRules**
```
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\LanManServer\\\\Parameters",
    "valueName": "restrictnullsessaccess",
    "ruleId": "f9020046-6340-451d-9548-3c45d765d06d",
    "originalId": "0f319931-aa36-4313-9320-86311c0fa623",
    "cceId": "CCE-10940-5",
    "ruleName": "Network access: Restrict anonymous access to Named Pipes and
    Shares",
    "ruleType": "Registry",
    "expectedValue": "1",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Disabled"

    }
```

**baselineAuditPolicyRules**
```
    {
    "auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
    "ruleId": "37745508-95fb-44ec-ab0f-644ec0b16995",
    "originalId": "2ea0de1a-c71d-46c8-8350-a7dd4d447895",
    "cceId": "CCE-11001-5",
    "ruleName": "Audit Policy: Account Management: Other Account Management Events",
    "ruleType": "AuditPolicy",
    "expectedValue": "Success and Failure",
    "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Enabled"
    }
```

**baselineSecurityPolicyRules**
```
    {
    "sectionName": "Privilege Rights",
    "settingName": "SeIncreaseWorkingSetPrivilege",
    "ruleId": "b0ec9d5e-916f-4356-83aa-c23522102b33",
    "originalId": "b61bd492-74b0-40f3-909d-36b9bf54e94c",
    "cceId": "CCE-10548-6",
    "ruleName": "Increase a process working set",
    "ruleType": "SecurityPolicy",
    "expectedValue": "Administrators, Local Service",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Enabled"
    }
```

Některá pravidla jsou duplicitní pro různé typy operačního systému. Duplicitní pravidla mají stejné *originalId* atribut.

## <a name="create-custom-rules"></a>Vytvořit vlastní pravidla

Můžete také vytvořit nová pravidla. Než vytvoříte nové pravidlo, mějte na paměti následující omezení:

-   Verze schématu *baselineId* a *baselineName* nelze změnit.

-   Nelze odebrat, RuleSet.

-   Nelze přidat RuleSet.

-   Maximální počet pravidel povolené (i výchozí pravidla), která je 1 000.

Nová vlastní pravidla jsou označené jako nový vlastní zdroj (! = "Microsoft"). *RuleId* pole může být null nebo prázdný. Pokud je prázdná, Microsoft generuje jeden. Pokud není prázdná, musí mít platný identifikátor GUID, který je jedinečný v rámci všech pravidel (výchozí a vlastní). Projděte si následující omezení pro základní pole:

-   **originalId**: může být null nebo prázdný. Pokud *originalId* je prázdná, by mělo být platný identifikátor GUID.

-   **cceId**: může být null nebo prázdný. Pokud *cceId* je prázdná, musí být jedinečný.

-   **Typ pravidla**: (vyberte jednu) registru, AuditPolicy nebo SecurityPolicy.

-   **Závažnost**: (vyberte jednu) Neznámý kritická, upozornění nebo informativní.

-   **analyzeOperation**: musí být *rovná*.

-   **auditPolicyId**: musí být platný identifikátor GUID.

-   **regValueType**: (vyberte jednu) Int, Long, řetězec nebo MultipleString.

> [!NOTE]
> Musí být Hive *LocalMachine*.
>
>

Příklad nové vlastní pravidlo:

**Registru**:
```
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\Netlogon\\\\MyKeyName",
    "valueName": "MyValueName",
    "originalId": "",
    "cceId": "",
    "ruleName": "My new registry rule”, "baselineRuleType": "Registry",
    "expectedValue": "123", "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "MyCustomSource",
    "state": "Enabled"
    }
```
**Zásady zabezpečení**:
```
   {
   "sectionName": "Privilege Rights",
   "settingName": "SeDenyBatchLogonRight",
   "originalId": "",
   "cceId": "",
   "ruleName": "My new security policy rule", "baselineRuleType":
   "SecurityPolicy",
   "expectedValue": "Guests",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```
**Zásady auditu**:
```
   {
   "auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
   "originalId": "",
   "cceId": "",
   "ruleName": " My new audit policy rule ", "baselineRuleType": "AuditPolicy",
   "expectedValue": " Failure",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```

## <a name="file-upload-failures"></a>Selhání nahrávání souborů

Pokud odeslaná konfiguračního souboru není platná z důvodu chyby ve formátování nebo hodnoty, zobrazí se chyba selhání. Můžete si stáhnout opravit a opravte chyby, než znovu odešlete opravené konfigurační soubor .csv zprávu podrobné chyby.

!["Uložit akce se nezdařilo" chybová zpráva](media/security-center-customize-os-security-config/invalid-configuration.png)

Příklad souboru chyba:

![Příklad souboru chyby](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>Kódy chyb

Všechny potenciální chyby jsou uvedené v následující tabulce:

| **Chyba**                                | **Popis**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfiguratiohSchemaVersionError  | Vlastnost *schemaVersion* byl nalezen neplatný nebo prázdný. Hodnota musí být nastavena na *{0}*.                                                         |
| BaselineInvalidStringError               | Vlastnost *{0}* nemůže obsahovat  *\\ n* .                                                                                                         |
| BaselineNullRuleError                    | V seznamu standardních hodnot konfigurace pravidel obsahuje pravidla s hodnotou *null*.                                                                         |
| BaselineRuleCceIdNotUniqueError          | Identifikátor CCE *{0}* není jedinečný.                                                                                                                  |
| BaselineRuleEmptyProperty                | Vlastnost *{0}* chybí nebo není platný.                                                                                                       |
| BaselineRuleIdNotInDefault               | Toto pravidlo má zdrojová vlastnost *Microsoft* , ale nebyl nalezen v ruleset výchozí společnosti Microsoft.                                                   |
| BaselineRuleIdNotUniqueError             | Pravidlo Id není jedinečné.                                                                                                                       |
| BaselineRuleInvalidGuid                  | Vlastnost *{0}* byl nalezen neplatný. Hodnota není platný identifikátor GUID.                                                                             |
| BaselineRuleInvalidHive                  | Podregistr musí být LocalMachine.                                                                                                                   |
| BaselineRuleNameNotUniqueError           | Název pravidla není jedinečný.                                                                                                                 |
| BaselineRuleNotExistInConfigError        | Pravidlo nebylo nalezeno v novou konfiguraci. Pravidlo nelze odstranit.                                                                     |
| BaselineRuleNotFoundError                | Pravidlo nebylo nalezeno ve výchozích ruleset směrného plánu.                                                                                        |
| BaselineRuleNotInPlace                   | Pravidlo odpovídá výchozí pravidlo s typu {0} a je uveden v seznamu {1}.                                                                       |
| BaselineRulePropertyTooLong              | Vlastnost *{0}* je příliš dlouhý. Maximální povolená délka: {1}.                                                                                        |
| BaselineRuleRegTypeInvalidError          | Očekávaná hodnota *{0}* se neshoduje typ hodnoty registru, který je definován.                                                              |
| BaselineRulesetAdded                     | Sada pravidel pro s ID *{0}* nebyl nalezen ve výchozí konfiguraci. Nelze přidat, sada pravidel pro.                                               |
| BaselineRulesetIdMustBeUnique            | Sada pravidel pro daného směrného plánu *{0}* musí být jedinečný.                                                                                           |
| BaselineRulesetNotFound                  | Sada pravidel pro s id *{0}* a název *{1}* nebyl nalezen v dané konfiguraci. Nelze odstranit, sada pravidel pro.                                |
| BaselineRuleSourceNotMatch               | Pravidlo s ID *{0}* je již definován.                                                                                                       |
| BaselineRuleTypeDoesntMatch              | Je výchozím typem pravidlo *{0}*.                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | Skutečný typ pravidla je *{0}*, ale *typ pravidla* vlastnost je *{1}*.                                                                          |
| BaselineRuleUnpermittedChangesError      | Pouze *expectedValue* a *stavu* jsou povoleny vlastnosti chcete změnit.                                                                       |
| BaselineTooManyRules                     | Maximální počet povolených vlastní pravidla je pravidla {0}. Danou konfiguraci obsahuje pravidla {1}, {2} výchozí pravidla a pravidla {3} přizpůsobit. |
| ErrorNoConfigurationStatus               | Nenašel se žádný stav konfigurace. Stav konfigurace požadovaného stavu: *výchozí* nebo *vlastní*.                                    |
| ErrorNonEmptyRulesetOnDefault            | Stav konfigurace je nastaven výchozí. *BaselineRulesets* seznamu musí být null nebo prázdný.                                                          |
| ErrorNullRulesetsPropertyOnCustom        | Stav daného konfigurace *vlastní* ale *baselineRulesets* vlastnost má hodnotu null nebo prázdný.                                             |
| ErrorParsingBaselineConfig               | Danou konfiguraci je neplatná. Jeden nebo více definovaných hodnot mít hodnotu null nebo neplatného typu.                                  |
| ErrorParsingIsDefaultProperty            | V dané *configurationStatus* hodnotu *{0}* je neplatný. Hodnota může být pouze *výchozí* nebo *vlastní*.                                         |
| InCompatibleViewVersion                  | Zobrazit verzi *{0}* je *není* podporována u tohoto typu pracovního prostoru.                                                                                   |
| InvalidBaselineConfigurationGeneralError | S jedné nebo více chybám ověření typ byl nalezen daného směrného plánu konfigurace.                                                          |
| ViewConversionError                      | Zobrazení je starší verze než podporuje pracovním prostoru. Zobrazit Chyba převodu: {0}.                                                                 |

Pokud nemáte dostatečná oprávnění, může získat k chybě došlo k obecné chybě, jak je vidět tady:

!["Uložit akce se nezdařilo" chybová zpráva](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>Další postup
Tento článek popsané postup přizpůsobení vyhodnocování konfigurace zabezpečení operačního systému ve službě Security Center. Další informace o konfiguraci pravidla a nápravě najdete v tématu:

- [Security Center běžné identifikátorů konfigurace a standardních pravidel](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- Security Center používá společné konfigurace – výčet (CCE) k přiřazení jedinečné identifikátory konfigurační pravidla. Další informace najdete v tématu [CCE](https://nvd.nist.gov/config/cce/index).
- Při konfiguraci operačního systému neodpovídá pravidla konfigurace doporučené zabezpečení řeší chyby zabezpečení, najdete v tématu [napravit konfigurace zabezpečení](security-center-remediate-os-vulnerabilities.md).
