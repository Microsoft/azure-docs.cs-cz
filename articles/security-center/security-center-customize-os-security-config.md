---
title: Přizpůsobení konfiguracemi zabezpečení operačního systému ve službě Azure Security Center (Preview) | Dokumentace Microsoftu
description: Tento článek ukazuje, jak přizpůsobit hodnocení security center
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 18/30/2018
ms.author: rkarlin
ms.openlocfilehash: 08174a6781772abdebd9e203a3433a1a4ac82859
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378359"
---
# <a name="customize-os-security-configurations-in-azure-security-center-preview"></a>Přizpůsobení konfiguracemi zabezpečení operačního systému ve službě Azure Security Center (Preview)

Tento návod ukazuje, jak přizpůsobit hodnocení konfigurace zabezpečení operačního systému ve službě Azure Security Center.

## <a name="what-are-os-security-configurations"></a>Co jsou konfiguracemi zabezpečení operačního systému?

Azure Security Center monitoruje konfigurace zabezpečení s použitím sady [víc než 150 doporučená pravidla](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) pro posílení zabezpečení operačního systému, včetně pravidel souvisejících s branami firewall, auditování, zásady pro hesla a další. Pokud na počítači se zjistilo, že zranitelné konfigurace jim, Security Center vygeneruje doporučení k zabezpečení.

Přizpůsobením pravidla organizace řídit, jaké možnosti konfigurace jsou vhodnější pro jejich prostředí. Můžete nastavit vlastní hodnocení zásad a použijte ji ve všech příslušných počítačích v rámci předplatného.

> [!NOTE]
> - Přizpůsobení konfigurace zabezpečení operačního systému v současné době je k dispozici pro Windows Server verze 2008, 2008 R2, 2012 a 2012 R2 pouze operační systémy.
> - Bude konfigurace platit pro všechny virtuální počítače a počítače, které jsou připojené ke všem pracovním prostorům ve vybraném předplatném.
> - Přizpůsobení konfigurace zabezpečení operačního systému je k dispozici pouze v Security Center úrovně standard.
>
>

Povolení a zakázání konkrétní pravidlo, požadované nastavení pro existující pravidlo změní nebo přidání nového pravidla, která je založena na typy podporovaných pravidel (registr, zásady auditu a zásady zabezpečení), můžete upravit pravidla konfigurace zabezpečení operačního systému. V současné době na požadované nastavení musí být přesná hodnota.

Nová pravidla musí být ve stejném formátu a struktura jako ostatní existující pravidla stejného typu.

> [!NOTE]
> Přizpůsobení konfiguracemi zabezpečení operačního systému, musí mít přiřazenou roli z *vlastník předplatného*, *přispěvatele předplatného*, nebo *správce zabezpečení*.
>
>

## <a name="customize-the-default-os-security-configuration"></a>Přizpůsobení výchozí konfigurace zabezpečení operačního systému

Chcete-li přizpůsobit výchozí konfigurace zabezpečení operačního systému ve službě Security Center, postupujte takto:

1.  Otevřete řídicí panel **Security Center**.

2.  V levém podokně vyberte **zásady zabezpečení**.      

    ![Seznam zásad zabezpečení](media/security-center-customize-os-security-config/manual-provision.png)

3.  V řádku předplatné, které chcete upravit, klikněte na tlačítko **upravit nastavení**.

4. Vyberte **upravit konfigurace zabezpečení**.  
    
    ![Okna "Upravit konfigurace zabezpečení"](media/security-center-customize-os-security-config/blade.png)

5. Postupujte podle kroků ke stažení, upravit a nahrajte upravený soubor.

   > [!NOTE]
   > Ve výchozím nastavení, je konfigurační soubor, který můžete stáhnout v *json* formátu. Pokyny týkající se tento soubor upravovali, přejděte na [přizpůsobení konfiguračního souboru](#customize-the-configuration-file).
   >

6. K provedení změny, vyberte **Uložit**. V opačném případě není uložený zásady.

    ![Tlačítko Uložit](media/security-center-customize-os-security-config/save-successfully.png)

   Po uložení souboru úspěšně, tato konfigurace používá pro všechny virtuální počítače a počítače, které jsou připojeny k pracovním prostorům v rámci předplatného. Proces obvykle trvá několik minut, ale může trvat déle, v závislosti na velikosti infrastruktury.

V kterékoli fázi můžete resetovat aktuální konfigurace zásad do výchozího stavu. To se dělá takto v **OS upravit pravidla konfigurace zabezpečení** okně **resetování**. Potvrďte výběrem této možnosti **Ano** v místním okně potvrzení.

![V okně potvrzení resetování](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>Přizpůsobení konfiguračního souboru

V souboru vlastního nastavení všechny podporované verze operačního systému obsahuje sadu pravidel nebo sada pravidel. Každá sada pravidel má svůj vlastní název a jedinečné ID, jak je znázorněno v následujícím příkladu:

![Název sady pravidel a ID](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> Tento ukázkový soubor byl upraven v sadě Visual Studio, ale můžete použít také Poznámkový blok Pokud máte modul plug-in JSON prohlížeči nainstalována.
>
>

Pokud upravujete soubor vlastního nastavení, můžete upravit jedno pravidlo nebo všechny z nich. Každá sada pravidel obsahuje *pravidla* část, která je rozdělena do tří kategorií: registr, zásady auditu a zásady zabezpečení, jak je znázorněno zde:

![Tři kategorie sady pravidel](media/security-center-customize-os-security-config/rules-section.png)

Každá kategorie má svou vlastní sadu atributů. Můžete změnit následující atributy:

- **expectedValue**: datový typ pole tohoto atributu musí odpovídat podporované hodnoty za *typ pravidla*, například:

  - **baselineRegistryRules**: hodnota by měla odpovídat [regValueType](https://msdn.microsoft.com/library/windows/desktop/ms724884) , který je definován v tomto pravidle.

  - **baselineAuditPolicyRules**: použijte jednu z následujících hodnot řetězce:

    - *Úspěchy a chyby*

    - *Úspěch*

  - **baselineSecurityPolicyRules**: použijte jednu z následujících hodnot řetězce:

    - *Nikdo*

    - Seznam povolených skupin uživatelů, například: *správci*, *Backup Operators*

-   **Stav**: řetězec může obsahovat možnosti *zakázané* nebo *povoleno*. Pro toto vydání ve verzi private preview řetězec je velká a malá písmena.

Jedná se pouze pole, které lze nastavit. Pokud jste porušují formát nebo velikost, nebudete moci uložit změny. Zobrazí se chybová zpráva oznamující, že je třeba nahrát platný konfigurační soubor JSON.

Seznam dalších potenciálních chyb, naleznete v tématu [kódy chyb](#error-codes).

V následujících třech částech obsahují příklady z předchozích pravidel. *ExpectedValue* a *stavu* atributy lze změnit.

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

Některá pravidla jsou duplicitní pro různé typy operačních systémů. Duplicitní pravidla mají stejnou *originalId* atribut.

## <a name="create-custom-rules"></a>Vytvořit vlastní pravidla

Můžete také vytvořit nová pravidla. Než vytvoříte nové pravidlo, mějte na paměti následující omezení:

-   Verze schématu *baselineId* a *baselineName* nelze změnit.

-   Nelze odebrat sady pravidel.

-   Sada pravidel nelze přidat.

-   Maximální počet pravidel povolené (výchozí pravidla i), která je 1000.

Nová vlastní pravidla jsou označené nový vlastní zdroj (! = "Microsoft"). *RuleId* pole může být null nebo prázdný. Pokud je hodnota prázdná, Microsoft jej vygeneruje. Pokud není prázdná, musí mít platný identifikátor GUID, který je jedinečný ve všech pravidel (výchozí a vlastní). Projděte si následující omezení pro základní pole:

-   **originalId**: může být null nebo prázdný. Pokud *originalId* není prázdný, by mělo být platný identifikátor GUID.

-   **cceId**: může být null nebo prázdný. Pokud *cceId* není prázdná, musí být jedinečný.

-   **Typ pravidla**: (vyberte jeden) registru, AuditPolicy nebo SecurityPolicy.

-   **Závažnost**: (vyberte jeden) neznámý, kritické, upozornění nebo informační.

-   **analyzeOperation**: musí být *rovná*.

-   **auditPolicyId**: musí být platný identifikátor GUID.

-   **regValueType**: (vyberte jeden) Int, Long, String nebo MultipleString.

> [!NOTE]
> Musí být Hive *LocalMachine*.
>
>

Příkladem nového vlastního pravidla:

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

## <a name="file-upload-failures"></a>Selhání nahrání souborů

Pokud odeslal konfigurační soubor je neplatný kvůli chybám hodnoty nebo formátování, selhání se zobrazí chyba, jako například **akce uložení se nezdařila**. Můžete stáhnout sestavu CSV podrobné chyby vyřešit a opravit chyby, než můžete znovu spustit opravený konfigurační soubor.

Příklad souboru chyba:

![Ukázka chyby souborů](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>Kódy chyb

Všechny možné chyby jsou uvedené v následující tabulce:

| **Chyba**                                | **Popis**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfiguratiohSchemaVersionError  | Vlastnost *schemaVersion* byl nalezen neplatný nebo prázdný. Hodnota musí být nastavena na *{0}*.                                                         |
| BaselineInvalidStringError               | Vlastnost *{0}* nemůže obsahovat  *\\n*.                                                                                                         |
| BaselineNullRuleError                    | Seznam standardních hodnot konfigurace pravidel obsahuje pravidla s hodnotou *null*.                                                                         |
| BaselineRuleCceIdNotUniqueError          | Identifikátor CCE *{0}* není jedinečný.                                                                                                                  |
| BaselineRuleEmptyProperty                | Vlastnost *{0}* chybí nebo není platná.                                                                                                       |
| BaselineRuleIdNotInDefault               | Toto pravidlo má vlastnosti zdroje *Microsoft* , ale nebyl nalezen v sadě Microsoft výchozích pravidel.                                                   |
| BaselineRuleIdNotUniqueError             | Pravidlo Id není jedinečné.                                                                                                                       |
| BaselineRuleInvalidGuid                  | Vlastnost *{0}* nebyla platná. Hodnota není platným identifikátorem GUID.                                                                             |
| BaselineRuleInvalidHive                  | Hive je třeba úložiště LocalMachine.                                                                                                                   |
| BaselineRuleNameNotUniqueError           | Název pravidla není jedinečný.                                                                                                                 |
| BaselineRuleNotExistInConfigError        | Pravidlo nebylo nalezeno v novou konfiguraci. Pravidlo nelze odstranit.                                                                     |
| BaselineRuleNotFoundError                | Pravidlo nebylo nalezeno ve výchozích pravidel standardních hodnot.                                                                                        |
| BaselineRuleNotInPlace                   | Pravidlo výchozí pravidlo s typem {0} a je uvedené v {1} seznamu.                                                                       |
| BaselineRulePropertyTooLong              | Vlastnost *{0}* je příliš dlouhý. Maximální povolená délka: {1}.                                                                                        |
| BaselineRuleRegTypeInvalidError          | Očekávaná hodnota *{0}* neshoduje s typem hodnoty registru, který je definován.                                                              |
| BaselineRulesetAdded                     | Sada pravidel s ID *{0}* nebyl nalezen ve výchozí konfiguraci. Nelze přidat sady pravidel.                                               |
| BaselineRulesetIdMustBeUnique            | Sada pravidel daného směrného plánu *{0}* musí být jedinečný.                                                                                           |
| BaselineRulesetNotFound                  | Sada pravidel s id *{0}* a název *{1}* nebyl nalezen v příslušné konfiguraci. Nelze odstranit, sada pravidel.                                |
| BaselineRuleSourceNotMatch               | Pravidlo s ID *{0}* je již definován.                                                                                                       |
| BaselineRuleTypeDoesntMatch              | Výchozí typ pravidla je *{0}*.                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | Skutečný typ pravidla je *{0}*, ale *ruleType* vlastnost *{1}*.                                                                          |
| BaselineRuleUnpermittedChangesError      | Pouze *expectedValue* a *stavu* vlastnosti se nesmí měnit.                                                                       |
| BaselineTooManyRules                     | Maximální počet povolených přizpůsobených pravidel je {0} pravidla. Obsahuje danou konfiguraci {1} pravidla, {2} výchozí pravidla, a {3} přizpůsobit pravidla. |
| ErrorNoConfigurationStatus               | Nenašel se žádný stav konfigurace. Stav konfigurace požadovaného stavu: *výchozí* nebo *vlastní*.                                    |
| ErrorNonEmptyRulesetOnDefault            | Stav konfigurace je nastavený na výchozí hodnotu. *BaselineRulesets* seznamu musí být null ani prázdný.                                                          |
| ErrorNullRulesetsPropertyOnCustom        | Stav danou konfiguraci *vlastní* ale *baselineRulesets* vlastnost je null nebo prázdný.                                             |
| ErrorParsingBaselineConfig               | Danou konfiguraci je neplatný. Jeden nebo více definovaných hodnot mají hodnotu null nebo neplatného typu.                                  |
| ErrorParsingIsDefaultProperty            | Dané *configurationStatus* hodnotu *{0}* je neplatný. Hodnota může být pouze *výchozí* nebo *vlastní*.                                         |
| InCompatibleViewVersion                  | Zobrazit verzi *{0}* je *není* podporované na tomto typu pracovního prostoru.                                                                                   |
| InvalidBaselineConfigurationGeneralError | Pomocí jedné nebo více chybám ověření typu nebyla nalezena daného směrného plánu konfigurace.                                                          |
| ViewConversionError                      | Zobrazení je starší verze než podporuje pracovní prostor. Zobrazení došlo k chybě převodu: {0}.                                                                 |

Pokud nemáte dostatečná oprávnění, mohou dojde k chybě došlo k obecné chybě, jak je znázorněno zde:

![Chybová zpráva "uložit akce se nezdařila."](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>Další postup
Tento článek byl věnován přizpůsobení posouzení konfigurace zabezpečení operačního systému ve službě Security Center. Další informace o konfiguraci pravidel a nápravu, naleznete v tématu:

- [Security Center identifikátory obecných konfigurací a základní pravidla](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- Security Center používá společné konfigurace výčet (CCE) pro přiřazení jedinečných identifikátorů pro konfiguraci pravidla. Další informace najdete v tématu [CCE](https://nvd.nist.gov/config/cce/index).
- Při konfiguraci operačního systému se neshoduje s pravidla konfigurace doporučené zabezpečení řeší chyby zabezpečení, najdete v článku [náprava konfigurací zabezpečení](security-center-remediate-os-vulnerabilities.md).
