---
title: Přizpůsobení konfigurací zabezpečení operačního systému v Azure Security Center (Preview) | Microsoft Docs
description: Tento článek ukazuje, jak přizpůsobit posouzení služby Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2019
ms.author: v-mohabe
ms.openlocfilehash: df4f51f97798048b28c0193dbc61e07fc55c9adc
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69535073"
---
# <a name="customize-os-security-configurations-in-azure-security-center-retired"></a>Přizpůsobení konfigurací zabezpečení operačního systému v Azure Security Center (vyřazeno)

Tento návod ukazuje, jak přizpůsobit posouzení konfigurace zabezpečení operačního systému (Preview) v Azure Security Center.

> [!NOTE]
> Možnost přizpůsobení konfigurace zabezpečení operačního systému (funkce Preview) byla vyřazena z července 31 2019. Další informace a alternativní služby najdete v tématu [vyřazení funkcí Security Center (červenec 2019)](security-center-features-retirement-july2019.md#menu_securityconfigurations).

## <a name="what-are-os-security-configurations"></a>Co jsou konfigurace zabezpečení operačního systému?

Azure Security Center monitoruje konfigurace zabezpečení tím, že pro posílení systému uplatňuje sadu [více než 150 doporučených pravidel](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335), včetně pravidel souvisejících s branami firewall, auditováním, zásadami hesel a dalšími. Pokud se na počítači zjistí zranitelná konfigurace, Security Center vygeneruje doporučení k zabezpečení.

Díky přizpůsobení pravidel můžou organizace řídit, které možnosti konfigurace jsou pro svoje prostředí vhodnější. Můžete nastavit vlastní zásadu hodnocení a pak ji použít na všech příslušných počítačích v rámci předplatného.

> [!NOTE]
> - V současné době je k dispozici přizpůsobení konfigurace zabezpečení operačního systému pro Windows Server verze 2008, 2008 R2, 2012, 2012 R2 a 2016 jenom pro operační systémy.
> - Konfigurace se vztahuje na všechny virtuální počítače a počítače, které jsou připojené ke všem pracovním prostorům v rámci vybraného předplatného.
> - Přizpůsobení konfigurace zabezpečení operačního systému je dostupné jenom na úrovni Standard Security Center.
>
>

Pravidla konfigurace zabezpečení operačního systému můžete přizpůsobit povolením a zakázáním konkrétního pravidla, změnou požadovaného nastavení pro existující pravidlo nebo přidáním nového pravidla založeného na podporovaných typech pravidel (registr, zásady auditu a zásady zabezpečení). V současné době musí být požadované nastavení přesnou hodnotu.

Nová pravidla musí být ve stejném formátu a struktuře jako ostatní stávající pravidla stejného typu.

> [!NOTE]
> Abyste mohli přizpůsobit konfigurace zabezpečení operačního systému, musíte mít přiřazenou roli *vlastník*předplatného, *přispěvatele*předplatného nebo *Správce zabezpečení*.
>
>

## <a name="customize-the-default-os-security-configuration"></a>Přizpůsobení výchozí konfigurace zabezpečení operačního systému

K přizpůsobení výchozí konfigurace zabezpečení operačního systému v Security Center postupujte následovně:

1.  Otevřete řídicí panel **Security Center**.

2.  V levém podokně vyberte **cenové & nastavení**.

    ![Seznam zásad zabezpečení](media/security-center-customize-os-security-config/manual-provision.png)

4. Vyberte příslušné předplatné a vyberte **Upravit konfigurace zabezpečení**.  

    ![Okno Upravit konfigurace zabezpečení](media/security-center-customize-os-security-config/blade.png)

5. Postupujte podle kroků ke stažení, úpravě a nahrání upraveného souboru.

   > [!NOTE]
   > Ve výchozím nastavení je konfigurační soubor, který jste stáhli, ve formátu *JSON* . Pokyny týkající se úprav tohoto souboru najdete v tématu [přizpůsobení konfiguračního souboru](#customize-the-configuration-file).
   >

6. Změnu potvrďte tak, že vyberete **Uložit**. V opačném případě se zásada neuloží.

    ![Tlačítko Uložit](media/security-center-customize-os-security-config/save-successfully.png)

   Po úspěšném uložení souboru se konfigurace použije na všechny virtuální počítače a počítače, které jsou připojené k pracovním prostorům v rámci předplatného. Proces obvykle trvá několik minut, ale v závislosti na velikosti infrastruktury může trvat delší dobu.

V libovolném okamžiku můžete obnovit aktuální konfiguraci zásad do výchozího stavu. Provedete to tak, že v okně **upravit pravidla konfigurace zabezpečení operačního systému** vyberete **obnovit**. Potvrďte tuto možnost výběrem možnosti **Ano** v automaticky otevíraném okně pro potvrzení.

![Okno potvrzení obnovení](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>Přizpůsobení konfiguračního souboru

V souboru vlastního nastavení má každá podporovaná verze operačního systému sadu pravidel nebo RuleSet. Každý RuleSet má svůj vlastní název a jedinečné ID, jak je znázorněno v následujícím příkladu:

![Název a ID RuleSet](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> Tento ukázkový soubor byl upraven v aplikaci Visual Studio, ale můžete také použít program Poznámkový blok, pokud máte nainstalovaný modul plug-in aplikace JSON Viewer.
>
>

Když upravujete soubor vlastního nastavení, můžete upravit jedno nebo více pravidel. Každý RuleSet obsahuje oddíl *pravidel* , který je rozdělený na tři kategorie: Registr, zásady auditu a zásady zabezpečení, jak je znázorněno zde:

![Tři kategorie RuleSet](media/security-center-customize-os-security-config/rules-section.png)

Každá kategorie má svou vlastní sadu atributů. Můžete změnit následující atributy:

- **expectedValue**: Tento datový typ pole atributu se musí shodovat s podporovanými hodnotami na *Typ pravidla*, například:

  - **baselineRegistryRules**: Hodnota by měla odpovídat [regValueType](https://msdn.microsoft.com/library/windows/desktop/ms724884) , který je definovaný v tomto pravidle.

  - **baselineAuditPolicyRules**: Použijte jednu z následujících hodnot řetězce:

    - *Úspěch a selhání*

    - *Nástup*

  - **baselineSecurityPolicyRules**: Použijte jednu z následujících hodnot řetězce:

    - *Nikdo jiný*

    - Seznam povolených skupin uživatelů, například: *Administrators*, *Backup Operators*

-   **stav**: Řetězec může obsahovat možnosti *zakázáno* nebo *povoleno*. V tomto vydání řetězec rozlišuje velká a malá písmena.

Toto jsou jediná pole, která je možné nakonfigurovat. Pokud porušujete formát nebo velikost souboru, nebudete moci změnu uložit. Zobrazí se chyba oznamující, že potřebujete nahrát platný konfigurační soubor JSON.

Seznam dalších možných chyb naleznete v tématu [kódy chyb](#error-codes).

Následující tři části obsahují příklady předchozích pravidel. Atributy *expectedValue* a *State* lze změnit.

**baselineRegistryRules**
```json
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
```json
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
```json
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

Některá pravidla jsou duplikována pro různé typy operačních systémů. Duplicitní pravidla mají stejný atribut *originalId* .

## <a name="create-custom-rules"></a>Vytvoření vlastních rolí

Můžete také vytvořit nová pravidla. Před vytvořením nového pravidla mějte na paměti následující omezení:

-   Verze schématu, *baselineId* a *směrný plán* se nedají změnit.

-   RuleSet nelze odebrat.

-   RuleSet nelze přidat.

-   Maximální povolený počet pravidel (včetně výchozích pravidel) je 1000.

Nová vlastní pravidla jsou označená novým vlastním zdrojem (! = "Microsoft"). Pole *ruleId* může mít hodnotu null nebo být prázdné. Pokud je prázdný, Microsoft ho vygeneruje. Pokud tato hodnota není prázdná, musí mít platný identifikátor GUID, který je jedinečný ve všech pravidlech (výchozí a vlastní). Zkontrolujte následující omezení pro základní pole:

-   **originalId**: Může mít hodnotu null nebo být prázdný. Pokud *originalId* není prázdné, měl by to být platný identifikátor GUID.

-   **cceId**: Může mít hodnotu null nebo být prázdný. Pokud *cceId* není prázdné, musí být jedinečný.

-   **ruleType**: (vyberte jeden) registr, AuditPolicy nebo securityPolicy.

-   **Závažnost**: (vyberte jednu) neznámá, kritická, varovná nebo informativní.

-   **analyzeOperation**: Musí být *rovno*.

-   **auditPolicyId**: Musí být platný identifikátor GUID.

-   **regValueType**: (vyberte jednu) int, Long, String nebo MultipleString.

> [!NOTE]
> Podregistr musí být *LocalMachine*.
>
>

Příklad nového vlastního pravidla:

**Registr**:
```json
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\Netlogon\\\\MyKeyName",
    "valueName": "MyValueName",
    "originalId": "",
    "cceId": "",
    "ruleName": "My new registry rule", "baselineRuleType": "Registry",
    "expectedValue": "123", "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "MyCustomSource",
    "state": "Enabled"
    }
```
**Zásady zabezpečení**:
```json
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
```json
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

Pokud je odeslaný konfigurační soubor neplatný z důvodu chyb v hodnotách nebo formátování, zobrazí se chyba selhání, například **Akce Uložit**. Před odesláním opraveného konfiguračního souboru si můžete stáhnout podrobnou sestavu Errors. csv k nápravě a opravě chyb.

Příklad souboru s chybou:

![Příklad souboru chyby](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>Kódy chyb

Všechny potenciální chyby jsou uvedené v následující tabulce:

| **Chyba**                                | **Popis**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfigurationSchemaVersionError  | Vlastnost *schemaVersion* byla nalezena jako neplatná nebo prázdná. Hodnota musí být nastavena na *{0}* .                                                         |
| BaselineInvalidStringError               | Vlastnost *{0}* nesmí obsahovat  *\\n*.                                                                                                         |
| BaselineNullRuleError                    | Seznam pravidel konfigurace standardních hodnot obsahuje pravidlo s hodnotou *null*.                                                                         |
| BaselineRuleCceIdNotUniqueError          | CCE-ID *{0}* není jedinečné.                                                                                                                  |
| BaselineRuleEmptyProperty                | Vlastnost *{0}* chybí nebo je neplatná.                                                                                                       |
| BaselineRuleIdNotInDefault               | Pravidlo má vlastnost zdroje *Microsoft* , ale nenašla se ve výchozí RuleSet společnosti Microsoft.                                                   |
| BaselineRuleIdNotUniqueError             | Identifikátor pravidla není jedinečný.                                                                                                                       |
| BaselineRuleInvalidGuid                  | Vlastnost *{0}* byla nalezena jako neplatná. Hodnota není platný identifikátor GUID.                                                                             |
| BaselineRuleInvalidHive                  | Podregistr musí být LocalMachine.                                                                                                                   |
| BaselineRuleNameNotUniqueError           | Název pravidla není jedinečný.                                                                                                                 |
| BaselineRuleNotExistInConfigError        | Pravidlo nebylo v nové konfiguraci nalezeno. Pravidlo nelze odstranit.                                                                     |
| BaselineRuleNotFoundError                | Pravidlo nebylo nalezeno ve výchozím směrném RuleSet.                                                                                        |
| BaselineRuleNotInPlace                   | Pravidlo odpovídá výchozímu pravidlu typu {0} a je uvedeno v seznamu v {1} seznamu.                                                                       |
| BaselineRulePropertyTooLong              | Vlastnost *{0}* je příliš dlouhá. Maximální povolená délka {1}:.                                                                                        |
| BaselineRuleRegTypeInvalidError          | Očekávaná hodnota *{0}* se neshoduje s typem hodnoty registru, který je definován.                                                              |
| BaselineRulesetAdded                     | RuleSet s identifikátorem *{0}* se ve výchozí konfiguraci nepovedlo najít. RuleSet nelze přidat.                                               |
| BaselineRulesetIdMustBeUnique            | Zadané RuleSet *{0}* směrného plánu musí být jedinečné.                                                                                           |
| BaselineRulesetNotFound                  | V dané konfiguraci nebyl *{0}* nalezen RuleSet *{1}* s identifikátorem a názvem. RuleSet nelze odstranit.                                |
| BaselineRuleSourceNotMatch               | Pravidlo s identifikátorem *{0}* je již definováno.                                                                                                       |
| BaselineRuleTypeDoesntMatch              | Výchozí typ pravidla je *{0}* .                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | Skutečný typ pravidla je *{0}* , ale vlastnost *ruleType* je. *{1}*                                                                          |
| BaselineRuleUnpermittedChangesError      | Mohou být změněny pouze vlastnosti *expectedValue* a *State* .                                                                       |
| BaselineTooManyRules                     | Maximální počet povolených přizpůsobených pravidel {0} je rules. Daná konfigurace obsahuje {1} pravidla, {2} výchozí pravidla a {3} přizpůsobená pravidla. |
| ErrorNoConfigurationStatus               | Nebyl nalezen žádný stav konfigurace. Stav požadované konfigurace: *Výchozí* nebo *vlastní*.                                    |
| ErrorNonEmptyRulesetOnDefault            | Stav konfigurace je nastaven na výchozí. Seznam *BaselineRulesets* musí mít hodnotu null nebo být prázdný.                                                          |
| ErrorNullRulesetsPropertyOnCustom        | Daný stav konfigurace je *vlastní* , ale vlastnost *baselineRulesets* má hodnotu null nebo je prázdná.                                             |
| ErrorParsingBaselineConfig               | Zadaná konfigurace je neplatná. Jedna nebo více definovaných hodnot má hodnotu null nebo je neplatného typu.                                  |
| ErrorParsingIsDefaultProperty            | Zadaná *{0}* hodnota *configurationStatus* je neplatná. Hodnota může být pouze *výchozí* nebo *vlastní*.                                         |
| InCompatibleViewVersion                  | Verze *{0}* zobrazení není v tomto typu pracovního prostoru podporována.                                                                                   |
| InvalidBaselineConfigurationGeneralError | Zadaná základní konfigurace byla nalezena s jednou nebo více chybami ověřování typu.                                                          |
| ViewConversionError                      | Zobrazení je starší verze, než podporuje pracovní prostor. Převod zobrazení se nezdařil: {0}.                                                                 |

Pokud nemáte dostatečná oprávnění, může se zobrazit Obecná chyba selhání, jak je znázorněno zde:

![Chybová zpráva uložení akce se nezdařila](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>Další postup
Tento článek popisuje, jak přizpůsobit vyhodnocení konfigurace zabezpečení operačního systému v Security Center. Další informace o pravidlech konfigurace a nápravě najdete v těchto tématech:

- [Security Center běžné identifikátory konfigurace a pravidla standardních hodnot](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- Security Center používá společný výčet konfigurace (CCE) k přiřazení jedinečných identifikátorů ke konfiguračním pravidlům. Další informace najdete v tématu [CCE](https://nvd.nist.gov/config/cce/index).
- Řešení chyb zabezpečení v případě, že se konfigurace operačního systému neshoduje s doporučenými pravidly konfigurace zabezpečení, najdete v tématu napravení [konfigurací zabezpečení](security-center-virtual-machine-protection.md).
