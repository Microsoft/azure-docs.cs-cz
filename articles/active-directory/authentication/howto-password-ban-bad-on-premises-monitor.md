---
title: Monitorování a protokolování v Azure AD hesla Protection ve verzi preview
description: Vysvětlení ochrana hesel Azure AD, monitorování a protokolování
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a3127cde66ce7de9a3920d238193a3a3b2225be
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56195783"
---
# <a name="preview-azure-ad-password-protection-monitoring-and-logging"></a>Verze Preview: Ochrana hesel Azure AD monitorování a protokolování

|     |
| --- |
| Ochrana hesel Azure AD je funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Po nasazení ochrany hesel služby Azure AD monitorování a vytváření sestav jsou základní úlohy. Tento článek obsahuje podrobnosti k pomoct pochopit různé postupy monitorování, včetně, kde každá služba protokoluje informace a hlášení týkající se použití ochrany hesel služby Azure AD.

# <a name="dc-agent-event-logging"></a>Protokolování událostí agenta řadiče domény

V každém řadiči domény k softwaru agenta služby řadiče domény zapíše výsledky operace ověření každé jednotlivé heslo (a další stav) do místního protokolu událostí:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Protokolu řadiče domény agenta správy je primární zdroj informací pro jak software nepracuje.

Všimněte si, že protokol trasování je vypnuto ve výchozím nastavení.

Události zapsané podle různých komponent agenta DC spadají do následujících oblastí:

|Komponenta |Rozsah ID událostí|
| --- | --- |
|Knihovny dll filtru hesel agenta řadiče domény| 10000-19999|
|Řadič domény agenta služby hostitelského procesu| 20000-29999|
|Logiku ověřování zásad služby agenta řadiče domény| 30000-39999|

## <a name="dc-agent-admin-event-log"></a>Protokol událostí správce agenta řadiče domény

### <a name="password-validation-outcome-events"></a>Události výsledek ověření hesla

V každém řadiči domény k softwaru agenta služby řadiče domény zapíše výsledky ověření každé jednotlivé hesla do protokolu událostí správce agentů DC.

Pro operace ověření hesla se podařila je obvykle jednu událost se zaznamená z knihovny dll filtru hesel agenta řadiče domény. Pro neúspěšného operace ověření hesla, obecně existují dvě události protokolované, jeden z řadiče domény služba agent a jeden z knihovny dll filtru hesel agenta DC.

Diskrétní události zaznamenat tyto situace se protokolují, podle následující faktory:

* Určuje, zda se dané heslo nastavit nebo změnit.
* Určuje, zda ověřování daného hesla úspěšné nebo neúspěšné.
* Ověření, jestli se nezdařilo z důvodu Microsoft globální zásady, zásady organizace nebo kombinaci.
* Určuje, zda režim jenom pro auditování je momentálně zapnuto nebo vypnuto pro aktuální zásady hesel.

Klíč události související s hesly ověření jsou následující:

|   |Změna hesla |Nastavení hesla|
| --- | :---: | :---: |
|Úspěch |10014 |10015|
|(Z důvodu zásad hesel zákazníka)| 10016, 30002| 10017, 30003|
|(Z důvodu zásad hesel Microsoft)| 10016, 30004| 10017, 30005|
|(Z důvodu kombinované Microsoft a zákazník zásady hesel)| 10016, 30026| 10017, 30027|
|Jenom pro auditování Pass (by se nezdařil zásady hesel zákazníka)| 10024, 30008| 10025, 30007|
|Jenom pro auditování Pass (by se nezdařil zásady hesel Microsoft)| 10024, 30010| 10025, 30009|
|Jenom pro auditování Pass (by se nezdařil kombinované zásady pro hesla Microsoft a zákazníka)| 10024, 30028| 10025, 30029|

Případy v předchozí tabulce, které odkazují na "kombinované zásady" odkazují na situace, kde bylo zjištěno heslo uživatele obsahovat alespoň jeden token ze seznamu Microsoft zakázané hesla a heslo seznam zákazníků zakázané.

Když dvojice událostí, které se zaznamená společně, jak událostí jsou explicitně přidružené k tím, že stejné ID korelace.

### <a name="password-validation-summary-reporting-via-powershell"></a>Souhrn ověření heslo vytváření sestav pomocí Powershellu

`Get-AzureADPasswordProtectionSummaryReport` Rutina slouží k vytvoření souhrnné zobrazení Aktivita ověřování hesla. Ukázkový výstup této rutiny je následujícím způsobem:

```PowerShell
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

Rozsah rutiny reporting může jím můžou být jedním z – doménové struktury, domény nebo – DomainController parametrů. Bez zadání parametru znamená – doménová struktura.

`Get-AzureADPasswordProtectionSummaryReport` Rutina funguje tak, že dotazování protokolu událostí správce agenta řadiče domény a pak počítání celkový počet událostí, které odpovídají každé kategorie zobrazený výsledek. Následující tabulka obsahuje mapování mezi každý výsledek a jeho odpovídající id události:

|Get-AzureADPasswordProtectionSummaryReport property |Id odpovídající události|
| :---: | :---: |
|PasswordChangesValidated |10014|
|PasswordSetsValidated |10015|
|PasswordChangesRejected |10016|
|PasswordSetsRejected |10017|
|PasswordChangeAuditOnlyFailures |10024|
|PasswordSetAuditOnlyFailures |10025|
|PasswordChangeErrors |10012|
|PasswordSetErrors |10013|

Všimněte si, že `Get-AzureADPasswordProtectionSummaryReport` rutiny je dodáno ve formě skriptu prostředí PowerShell a v případě potřeby může odkazovat přímo v následujícím umístění:

`%ProgramFiles%\WindowsPowerShell\Modules\AzureADPasswordProtection\Get-AzureADPasswordProtectionSummaryReport.ps1`

> [!NOTE]
> Tato rutina funguje tak, že otevřete relaci Powershellu na každém řadiči domény. Aby bylo možné úspěšné, musí být povolena podpora vzdálené relace Powershellu na každém řadiči domény a klient musí mít dostatečná oprávnění. Další informace o požadavcích vzdálené relace prostředí PowerShell spusťte v okně Powershellu "Get-Help about_Remote_Troubleshooting".

> [!NOTE]
> Tato rutina funguje tak, že vzdáleně dotazování protokolu událostí správce každé služby agenta řadiče domény. Když protokoly událostí obsahují velký počet událostí, rutina může trvat dlouhou dobu pro dokončení. Kromě toho hromadné sítě dotazy velkých datových sad může ovlivnit výkon řadiče domény. Proto by měla tuto rutinu použít pečlivě v produkčním prostředí.

### <a name="sample-event-log-message-for-event-id-10014-successful-password-change"></a>Ukázka protokolu událostí zprávy pro ID události 10014 (Změna hesla se podařila)

```text
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Ukázka protokolu událostí zprávy pro ID události 10017 a 30003 (nastavení hesel)

10017:

```text
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003:

```text
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Ukázka protokolu událostí zprávy pro ID události 30001 (hesla přijata z důvodu žádné zásady, které jsou k dispozici)

```text
The password for the specified user was accepted because an Azure password policy is not available yet

UserName: SomeUser
FullName: Some User

This condition may be caused by one or more of the following reasons:%n

1. The forest has not yet been registered with Azure.

   Resolution steps: an administrator must register the forest using the Register-AzureADPasswordProtectionForest cmdlet.

2. An Azure AD password protection Proxy is not yet available on at least one machine in the current forest.

   Resolution steps: an administrator must install and register a proxy using the Register-AzureADPasswordProtectionProxy cmdlet.

3. This DC does not have network connectivity to any Azure AD password protection Proxy instances.

   Resolution steps: ensure network connectivity exists to at least one Azure AD password protection Proxy instance.

4. This DC does not have connectivity to other domain controllers in the domain.

   Resolution steps: ensure network connectivity exists to the domain.
```

### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Ukázka protokolu událostí zprávy pro ID události 30006 (nové zůstala vynucená zásada)

```text
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

### <a name="sample-event-log-message-for-event-id-30019-azure-ad-password-protection-is-disabled"></a>Ukázka protokolu událostí zprávy pro ID události 30019 (ochrana hesel Azure AD je zakázána)

```text
The most recently obtained Azure password policy was configured to be disabled. All passwords submitted for validation from this point on will automatically be considered compliant with no processing performed.

No further events will be logged until the policy is changed.%n

```

## <a name="dc-agent-operational-log"></a>Řadič domény agenta operační protokol

Služba agenta řadiče domény navíc zaznamená provozní související události do protokolu následující:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

## <a name="dc-agent-trace-log"></a>Protokol trasování agenta řadiče domény

Služba agenta řadiče domény můžete také podrobné úroveň ladění trasování událostí v protokolu do následující protokol:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Protokolování trasování je ve výchozím nastavení zakázána.

> [!WARNING]
> Při povolení protokolu trasování přijímá k velkému počtu událostí a může ovlivnit výkon řadiče domény. Proto tento Vylepšený protokol by měl být povoleno pouze při vyžaduje hlubší šetření, problém a pak pouze pro co nejkratším čase.

## <a name="dc-agent-text-logging"></a>Řadič domény agenta protokolování textu

Řadič domény agenta služby lze nastavit k zápisu do protokolu text tak, že nastavíte následující hodnotu registru:

```text
HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)
```

Protokolování textu je standardně zakázáno. Restartovat službu agenta řadiče domény je požadovaná pro tuto hodnotu, než se projeví změny. Pokud povolena řadiče domény Služba agenta bude zapisovat do souboru protokolu, která je umístěna ve složce:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> Textový protokol obdrží stejnou úroveň ladění položky, které může být zaznamenány do protokolu trasování, ale je obvykle ve formátu snadněji zkontrolovat a analyzovat.

> [!WARNING]
> Při povolení tohoto protokolu přijímá k velkému počtu událostí a může ovlivnit výkon řadiče domény. Proto tento Vylepšený protokol by měl být povoleno pouze při vyžaduje hlubší šetření, problém a pak pouze pro co nejkratším čase.

## <a name="dc-agent-performance-monitoring"></a>Sledování výkonu agenta řadiče domény

Software služby agenta řadiče domény nainstaluje objekt čítače výkonu s názvem **ochrana hesel Azure AD**. Následující čítače výkonu jsou aktuálně k dispozici:

|Název čítače výkonu | Popis|
| --- | --- |
|Hesla zpracování |Tento čítač zobrazuje celkový počet zpracovaných hesla (přijímat nebo odmítat) od minulého restartování.|
|Hesla přijata |Tento čítač zobrazuje celkový počet hesel, která byla přijata od posledního restartování.|
|Hesla zamítnuto |Tento čítač zobrazuje celkový počet hesel, které byly odmítnuty od posledního restartování.|
|Heslo filtrovat žádosti v průběhu |Tento čítač zobrazuje počet požadavků filtr heslo, které aktuálně probíhá.|
|Požadavky filtru hesel ve špičce |Tento čítač zobrazuje nejvyšší počet souběžných heslo filtrovat žádosti od minulého restartování.|
|Heslo filtrování požadavku chyb |Tento čítač zobrazuje celkový počet požadavků filtr heslo, které se nezdařilo z důvodu chyby od posledního restartování. Pokud není spuštěna Služba agenta ochrany DC hesel služby Azure AD, může dojít k chybám.|
|Heslo filtru požadavků za sekundu |Tento čítač zobrazuje míru, ve kterém jsou zpracovávány hesla.|
|Doba zpracování požadavku filtru hesel |Tento čítač zobrazuje průměrný čas potřebný ke zpracování požadavku filtru hesel.|
|Doba zpracování požadavku filtru hesel ve špičce |Tento čítač zobrazuje čas od minulého restartování zpracování požadavků filtru hesel ve špičce.|
|Hesla přijata z důvodu režimu auditování |Tento čítač zobrazuje celkový počet hesel, která by obvykle byly odmítnuty, ale byla přijata, protože zásady hesel nastavený tak, aby se v režimu auditování (od minulého restartování).|

## <a name="dc-agent-discovery"></a>Zjišťování agentů DC

`Get-AzureADPasswordProtectionDCAgent` Rutiny lze zobrazit základní informace o různých agentů DC spuštěných v doméně nebo doménové struktuře. Tyto informace získány z serviceConnectionPoint objekty registrované ve spuštěné službě agenta řadiče domény.

Ukázkový výstup této rutiny je následujícím způsobem:

```PowerShell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
PasswordPolicyDateUTC : 2/16/2018 8:35:01 AM
HeartbeatUTC          : 2/16/2018 8:35:02 AM
```

Různé vlastnosti se aktualizují každou službu agenta DC přibližně hodinu. Data se stále latenci replikace služby Active Directory.

Rozsah rutiny dotazu může jím můžou být buď pomocí – doménová struktura nebo – doména parametry.

Pokud hodnota HeartbeatUTC získá zastaralá, může to být příznakem, který Agent ochrany DC hesel služby Azure AD na příslušném řadiči domény není spuštěný, nebo byla odinstalována, nebo byla snížena na počítači a už není řadičem domény.

Pokud získá hodnotu PasswordPolicyDateUTC zastaralé, důvodem může být příznakem, který má Agent ochrany DC hesel služby Azure AD na tomto počítači nefunguje správně.

## <a name="proxy-service-event-logging"></a>Protokolování událostí service proxy

Proxy služba generuje minimální sadu událostí k následující protokoly událostí:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Všimněte si, že protokol trasování je vypnuto ve výchozím nastavení.

> [!WARNING]
> Při povolení protokolu trasování přijímá k velkému počtu událostí a to může mít vliv na výkon hostitele proxy serveru. Proto tento protokol by měl být povoleno pouze při vyžaduje hlubší šetření, problém a pak pouze pro co nejkratším čase.

Události jsou protokolovány podle různých komponent proxy server pomocí následující rozsahy:

|Komponenta |Rozsah ID událostí|
| --- | --- |
|Hostitelský proces service proxy| 10000-19999|
|Proxy služby jádra obchodní logiky| 20000-29999|
|Rutiny prostředí PowerShell| 30000-39999|

## <a name="proxy-service-text-logging"></a>Protokolování textu service proxy

Proxy služby mohou být konfigurovány pro zápis do protokolu text tak, že nastavíte následující hodnotu registru:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters!EnableTextLogging = 1 (REG_DWORD value)

Protokolování textu je standardně zakázáno. Restartování služby serveru Proxy je požadovaná pro tuto hodnotu, než se projeví změny. Pokud povolena proxy serveru, kdy bude služba zapisovat do souboru protokolu, která je umístěna ve složce:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> Textový protokol obdrží stejnou úroveň ladění položky, které může být zaznamenány do protokolu trasování, ale je obvykle ve formátu snadněji zkontrolovat a analyzovat.

> [!WARNING]
> Při povolení tohoto protokolu přijímá k velkému počtu událostí a může mít vliv na výkon počítače. Proto tento Vylepšený protokol by měl být povoleno pouze při vyžaduje hlubší šetření, problém a pak pouze pro co nejkratším čase.

## <a name="powershell-cmdlet-logging"></a>Protokolování rutiny prostředí PowerShell

Rutiny Powershellu, které způsobit změnu stavu (například Register-AzureADPasswordProtectionProxy) bude obvykle protokolovat událost v výsledek do provozních protokolů.

V další, většina rutin Powershellu pro ochranu hesel služby Azure AD bude zapisovat do protokolu textu umístěna ve složce:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Pokud dojde k chybě rutiny a příčina a řešení není snadno pozná, tyto textové protokoly mohou také brán ohled.

## <a name="proxy-discovery"></a>Proxy zjišťování

`Get-AzureADPasswordProtectionProxy` Rutiny lze zobrazit základní informace o různých hesel služby Azure AD ochrany Proxy služby spuštěné v doméně nebo doménové struktuře. Tyto informace získány z serviceConnectionPoint objektů registrovaných spuštěné služby serveru Proxy.

Ukázkový výstup této rutiny je následujícím způsobem:

```PowerShell
Get-AzureADPasswordProtectionProxy
ServerFQDN            : bplProxy.bplchild2.bplRootDomain.com
Domain                : bplchild2.bplRootDomain.com
Forest                : bplRootDomain.com
HeartbeatUTC          : 12/25/2018 6:35:02 AM
```

Různé vlastnosti se aktualizují každou službu Proxy přibližně hodinu. Data se stále latenci replikace služby Active Directory.

Rozsah rutiny dotazu může jím můžou být buď pomocí – doménová struktura nebo – doména parametry.

Pokud hodnota HeartbeatUTC získá zastaralá, může to být příznak, který Proxy ochrana hesel Azure AD na tomto počítači není spuštěna nebo k byla odinstalována.

## <a name="next-steps"></a>Další postup

[Řešení potíží pro ochranu hesel Azure AD](howto-password-ban-bad-on-premises-troubleshoot.md)

Další informace o seznamech globálních a vlastních zakázaných hesel, najdete v článku [zakázat chybná hesla](concept-password-ban-bad.md)
