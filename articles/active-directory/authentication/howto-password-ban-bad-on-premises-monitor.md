---
title: Monitorování místní ochrany heslem Azure AD
description: Zjistěte, jak monitorovat a kontrolovat protokoly pro azure ad heslem pro místní prostředí služby Active Directory Domain Services
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbb533d5565009fb22d686e4082c9b4bfaae6dc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671659"
---
# <a name="monitor-and-review-logs-for-on-premises-azure-ad-password-protection-environments"></a>Sledování a kontrola protokolů pro místní prostředí ochrany heslem Azure AD

Po nasazení Azure AD ochrana heslem, monitorování a vytváření sestav jsou základní úkoly. Tento článek jde do podrobností, které vám pomohou pochopit různé techniky monitorování, včetně kde každá služba protokoluje informace a jak sestavy o použití Azure AD password protection.

Monitorování a vytváření sestav se provádí buď pomocí zpráv protokolu událostí nebo spuštěním rutin prostředí PowerShell. Agent řadiče domény a proxy služby protokolovat zprávy protokolu událostí. Všechny rutiny prostředí PowerShell popsané níže jsou k dispozici pouze na proxy serveru (viz modul PowerShell AzureADPasswordProtection). Software agenta řadiče domény nenainstaluje modul prostředí PowerShell.

## <a name="dc-agent-event-logging"></a>Protokolování událostí agenta řadiče domény

Na každém řadiči domény zapisuje software služby agenta řadiče domény výsledky každé jednotlivé operace ověření hesla (a dalšístav) do místního protokolu událostí:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Protokol správce agenta řadiče domény je primárním zdrojem informací o chování softwaru.

Všimněte si, že protokol trasování je ve výchozím nastavení vypnutý.

Události protokolované různými součástmi agenta řadiče domény spadají do následujících rozsahů:

|Komponenta |Rozsah ID události|
| --- | --- |
|DLL filtru hesel agenta řadiče domény| 10000-19999|
|Proces hostování služby agenta řadiče domény| 20000-29999|
|Logika ověření zásad y agenta řadiče domény| 30000-39999|

## <a name="dc-agent-admin-event-log"></a>Protokol událostí správce agenta řadiče domény

### <a name="password-validation-outcome-events"></a>Události výsledku ověření hesla

Na každém řadiči domény zapíše software služby agenta řadiče domény výsledky každého jednotlivého ověření hesla do protokolu událostí správce agenta řadiče domény.

Pro úspěšnou operaci ověření hesla je obvykle jedna událost zaznamenána z dll filtru filtru hesla agenta řadiče domény. Pro selhání operace ověření hesla jsou obvykle zaznamenány dvě události, jedna ze služby agenta řadiče domény a jedna z dll filtru filtru hesla agenta řadiče domény.

Diskrétní události zachytit tyto situace jsou zaznamenány, na základě následujících faktorů:

* Určuje, zda je dané heslo nastaveno nebo měněno.
* Zda ověření daného hesla bylo předáno nebo se nezdařilo.
* Zda se ověření nezdařilo z důvodu globální ch od společnosti Microsoft, zásady organizace nebo kombinace.
* Určuje, zda je režim pouze auditování aktuálně zapnutý nebo vypnutý pro aktuální zásady hesel.

Události související s ověřením hesla klíče jsou následující:

|   |Změna hesla |Sada hesel|
| --- | :---: | :---: |
|Předat |10014 |10015|
|Selhání (z důvodu zásad hesla zákazníka)| 10016, 30002| 10017, 30003|
|Selhání (z důvodu zásad hesel společnosti Microsoft)| 10016, 30004| 10017, 30005|
|Selhání (z důvodu kombinovaných zásad microsoftu a hesel zákazníků)| 10016, 30026| 10017, 30027|
|Průchod pouze pro audit (selhaly zásady hesel zákazníka)| 10024, 30008| 10025, 30007|
|Průchod pouze pro audit (selhaly by zásady hesel společnosti Microsoft)| 10024, 30010| 10025, 30009|
|Pass pouze pro audit (selhaly kombinované zásady microsoftu a hesla zákazníků)| 10024, 30028| 10025, 30029|

Případy ve výše uvedené tabulce, které odkazují na "kombinované zásady", se týkají situací, kdy bylo zjištěno, že heslo uživatele obsahuje alespoň jeden token ze seznamu zakázaných hesel společnosti Microsoft a seznamu zakázaných hesel zákazníka.

Při dvojice událostí je zaznamenána společně, obě události jsou explicitně spojeny tím, že má stejné CorrelationId.

### <a name="password-validation-summary-reporting-via-powershell"></a>Souhrnné vytváření sestav ověření hesla prostřednictvím PowerShellu

Rutina `Get-AzureADPasswordProtectionSummaryReport` může být použita k vytvoření souhrnného zobrazení aktivity ověření hesla. Příklad výstupu této rutiny je následující:

```powershell
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

Rozsah sestavy rutiny může být ovlivněn pomocí jednoho z parametrů –Forest, -Domain nebo –DomainController. Není zadání parametru znamená –Forest.

Rutina `Get-AzureADPasswordProtectionSummaryReport` funguje dotazem na protokol událostí správce agenta řadiče domény a potom počítá celkový počet událostí, které odpovídají každé kategorii zobrazeného výsledku. Následující tabulka obsahuje mapování mezi jednotlivými výsledky a jeho odpovídajícím ID události:

|Vlastnost Get-AzureADPasswordProtectionSummaryReport |Odpovídající ID události|
| :---: | :---: |
|PasswordChangesValidated |10014|
|PasswordSetsValidated |10015|
|Odmítnuté změny hesla |10016|
|HeslaOdmítnuta |10017|
|PasswordChangeAuditOnlyChyby |10024|
|PasswordSetAuditOnlyChyby |10025|
|Chyby změny hesla |10012|
|PasswordSetErrors |10013|

Všimněte `Get-AzureADPasswordProtectionSummaryReport` si, že rutina je dodávána ve skriptu prostředí PowerShell a v případě potřeby může být odkazována přímo v následujícím umístění:

`%ProgramFiles%\WindowsPowerShell\Modules\AzureADPasswordProtection\Get-AzureADPasswordProtectionSummaryReport.ps1`

> [!NOTE]
> Tato rutina funguje tak, že otevře relaci prostředí PowerShell pro každý řadič domény. Aby byla úspěšná podpora vzdálené relace prostředí PowerShell, musí být povolena na každém řadiči domény a klient musí mít dostatečná oprávnění. Další informace o požadavcích na vzdálenou relaci prostředí PowerShell spusťte v okně Prostředí PowerShell spouštět pomoc about_Remote_Troubleshooting.

> [!NOTE]
> Tato rutina funguje tak, že vzdáleně dotazuje každý řadič domény agent administrátora protokolu událostí. Pokud protokoly událostí obsahují velký počet událostí, rutina může trvat dlouhou dobu. Hromadné síťové dotazy velkých datových sad mohou navíc ovlivnit výkon řadiče domény. Proto by tato rutina měla být používána opatrně v produkčním prostředí.

### <a name="sample-event-log-message-for-event-id-10014-successful-password-change"></a>Ukázková zpráva protokolu událostí pro ID události 10014 (úspěšná změna hesla)

```text
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Ukázková zpráva protokolu událostí pro ID události 10017 a 30003 (sada hesel se nezdařilo)

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

### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Ukázková zpráva protokolu událostí pro ID události 30001 (heslo přijato z důvodu, že nejsou k dispozici žádné zásady)

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

### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Ukázková zpráva protokolu událostí pro ID události 30006 (vynucování nových zásad)

```text
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

### <a name="sample-event-log-message-for-event-id-30019-azure-ad-password-protection-is-disabled"></a>Ukázková zpráva protokolu událostí pro ID události 30019 (ochrana heslem Azure AD je zakázaná)

```text
The most recently obtained Azure password policy was configured to be disabled. All passwords submitted for validation from this point on will automatically be considered compliant with no processing performed.

No further events will be logged until the policy is changed.%n

```

## <a name="dc-agent-operational-log"></a>Provozní protokol agenta řadiče domény

Služba agenta řadiče domény bude také protokolovat události související s provozem do následujícího protokolu:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

## <a name="dc-agent-trace-log"></a>Protokol trasování agenta řadiče domény

Služba agenta řadiče domény může také protokolovat podrobné události trasování na úrovni ladění do následujícího protokolu:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Protokolování trasování je ve výchozím nastavení zakázáno.

> [!WARNING]
> Pokud je povoleno, protokol trasování obdrží velký objem událostí a může mít vliv na výkon řadiče domény. Proto tento rozšířený protokol by měl být povolen pouze v případě, že problém vyžaduje hlubší šetření a potom pouze po minimální množství času.

## <a name="dc-agent-text-logging"></a>Protokolování textu agenta řadiče domény

Službu agenta řadiče domény lze nakonfigurovat tak, aby zapisovala do textového protokolu, a to nastavením následující hodnoty registru:

```text
HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)
```

Protokolování textu je ve výchozím nastavení zakázáno. Aby se změny této hodnoty projevily, je nutné restartovat službu agenta řadiče domény. Pokud je tato možnost povolena, služba agenta řadiče domény zapíše do souboru protokolu umístěného pod:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> Textový protokol obdrží stejné položky na úrovni ladění, které mohou být zaznamenány do protokolu trasování, ale je obecně ve formátu jednodušší ke kontrole a analýze.

> [!WARNING]
> Pokud je tento protokol povolen, obdrží velký objem událostí a může mít vliv na výkon řadiče domény. Proto tento rozšířený protokol by měl být povolen pouze v případě, že problém vyžaduje hlubší šetření a potom pouze po minimální množství času.

## <a name="dc-agent-performance-monitoring"></a>Monitorování výkonu agenta řadiče domény

Software služby agenta řadiče domény nainstaluje objekt čítače výkonu s názvem **Azure AD Password Protection**. V současné době jsou k dispozici následující čítače perf:

|Název čítače Perf | Popis|
| --- | --- |
|Zpracovaná hesla |Tento čítač zobrazuje celkový počet zpracovaných (přijatých nebo odmítnutých hesel) od posledního restartování.|
|Hesla přijata |Tento čítač zobrazuje celkový počet hesel, která byla přijata od posledního restartování.|
|Hesla byla odmítnuta. |Tento čítač zobrazuje celkový počet hesel, která byla odmítnuta od posledního restartování.|
|Probíhá počet požadavků na filtrování hesel |Tento čítač zobrazuje počet aktuálně probíhajících požadavků na filtr hesla.|
|Požadavky na filtrování hesel ve špičce |Tento čítač zobrazuje maximální počet souběžných požadavků filtru hesla od posledního restartování.|
|Chyby požadavku filtru hesla |Tento čítač zobrazuje celkový počet požadavků filtru hesla, které se nezdařily z důvodu chyby od posledního restartování. Chyby může dojít, pokud služba agenta azure ad ochrana heslem dc agent není spuštěna.|
|Požadavky na filtrování hesel/s |Tento čítač zobrazuje rychlost, jakou jsou hesla zpracovávána.|
|Doba zpracování požadavku na filtrování hesel |Tento čítač zobrazuje průměrnou dobu potřebnou ke zpracování požadavku filtru hesla.|
|Doba zpracování požadavku na filtrování hesel ve špičce |Tento čítač zobrazuje dobu zpracování požadavku filtru hesla ve špičce od posledního restartování.|
|Hesla přijatá z důvodu režimu auditování |Tento čítač zobrazuje celkový počet hesel, která by byla normálně odmítnuta, ale byla přijata, protože zásady hesel byly nakonfigurovány tak, aby byly v režimu auditu (od posledního restartování).|

## <a name="dc-agent-discovery"></a>Zjišťování agenta řadiče domény

Rutina `Get-AzureADPasswordProtectionDCAgent` může být použita k zobrazení základních informací o různých řadičích domény spuštěných v doméně nebo doménové struktuře. Tyto informace jsou načteny z objektů serviceConnectionPoint registrovaných spuštěnou službou agenta řadiče domény.

Příklad výstupu této rutiny je následující:

```powershell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
PasswordPolicyDateUTC : 2/16/2018 8:35:01 AM
HeartbeatUTC          : 2/16/2018 8:35:02 AM
```

Různé vlastnosti jsou aktualizovány každou službou agenta řadiče domény na přibližně hodinovém základě. Data stále podléhají latenci replikace služby Active Directory.

Rozsah dotazu rutiny může být ovlivněn pomocí parametry –Forest nebo –Domain.

Pokud heartbeatutc hodnota získá zastaralá, to může být příznakem, že Agent domény Azure AD Password Protection DC na tomto řadiči domény není spuštěna nebo byla odinstalována nebo počítač byl snížena a již není řadič domény.

Pokud passwordpolicydateutc hodnota získá zastaralé, to může být příznakem, že agent domény ochrany heslem Azure AD v tomto počítači nefunguje správně.

## <a name="dc-agent-newer-version-available"></a>Dc agent novější verze k dispozici

Služba agenta řadiče domény zaznamená událost upozornění 30034 do provozního protokolu při zjištění, že je k dispozici novější verze softwaru agenta řadiče domény, například:

```text
An update for Azure AD Password Protection DC Agent is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
```

Výše uvedená událost neurčuje verzi novějšího softwaru. Měli byste přejít na odkaz ve zprávě o události pro tyto informace.

> [!NOTE]
> Navzdory odkazům na "automatický upgrade" ve výše uvedené zprávě o události software agenta řadiče domény tuto funkci v současné době nepodporuje.

## <a name="proxy-service-event-logging"></a>Protokolování událostí služby Proxy

Služba Proxy vydává minimální sadu událostí do následujících protokolů událostí:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Všimněte si, že protokol trasování je ve výchozím nastavení vypnutý.

> [!WARNING]
> Pokud je povoleno, protokol trasování obdrží velký objem událostí a to může mít vliv na výkon hostitele proxy serveru. Proto tento protokol by měl být povolen pouze v případě, že problém vyžaduje hlubší šetření a potom pouze po minimální množství času.

Události jsou protokolovány různými součástmi proxy pomocí následujících rozsahů:

|Komponenta |Rozsah ID události|
| --- | --- |
|Proces hostování proxy služby| 10000-19999|
|Základní obchodní logika služby Proxy| 20000-29999|
|Rutiny prostředí PowerShell| 30000-39999|

## <a name="proxy-service-text-logging"></a>Protokolování textu služby Proxy

Službu Proxy lze nakonfigurovat tak, aby zapisovala do textového protokolu, a to nastavením následující hodnoty registru:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters! EnableTextLogging = 1 (REG_DWORD hodnota)

Protokolování textu je ve výchozím nastavení zakázáno. Aby se změny této hodnoty projevily, je nutné restartovat službu Proxy. Pokud je tato možnost povolena, služba Proxy zapíše do souboru protokolu umístěného pod:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> Textový protokol obdrží stejné položky na úrovni ladění, které mohou být zaznamenány do protokolu trasování, ale je obecně ve formátu jednodušší ke kontrole a analýze.

> [!WARNING]
> Pokud je povoleno, tento protokol obdrží velký objem událostí a může mít vliv na výkon počítače. Proto tento rozšířený protokol by měl být povolen pouze v případě, že problém vyžaduje hlubší šetření a potom pouze po minimální množství času.

## <a name="powershell-cmdlet-logging"></a>Protokolování rutiny prostředí PowerShell

Rutiny prostředí PowerShell, které vedou ke změně stavu (například Register-AzureADPasswordProtectionProxy) obvykle zaprotokolují událost výsledku do protokolu Operational.

Kromě toho většina rutin prostředí PowerShell azure aad password protection zapíše do textového protokolu umístěného pod:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Pokud dojde k chybě rutiny a příčina a\nebo řešení není snadno zřejmé, mohou být také konzultovány tyto textové protokoly.

## <a name="proxy-discovery"></a>Zjišťování serveru proxy

Rutina `Get-AzureADPasswordProtectionProxy` může být použita k zobrazení základních informací o různých službách proxy ochrany heslem Azure AD spuštěných v doméně nebo doménové struktuře. Tyto informace jsou načteny z objektů služby ConnectionPoint registrovaných spuštěnou službou Proxy.

Příklad výstupu této rutiny je následující:

```powershell
Get-AzureADPasswordProtectionProxy
ServerFQDN            : bplProxy.bplchild2.bplRootDomain.com
Domain                : bplchild2.bplRootDomain.com
Forest                : bplRootDomain.com
HeartbeatUTC          : 12/25/2018 6:35:02 AM
```

Různé vlastnosti jsou aktualizovány každou službou proxy na přibližně hodinovém základě. Data stále podléhají latenci replikace služby Active Directory.

Rozsah dotazu rutiny může být ovlivněn pomocí parametry –Forest nebo –Domain.

Pokud heartbeatutc hodnota získá zastaralé, to může být příznakem, že proxy ochrany heslem Azure AD v tomto počítači není spuštěnnebo byl odinstalován.

## <a name="proxy-agent-newer-version-available"></a>K dispozici je novější verze agenta proxy.

Služba Proxy zaznamená událost upozornění 20002 do provozního protokolu po zjištění, že je k dispozici novější verze proxy softwaru, například:

```text
An update for Azure AD Password Protection Proxy is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
.
```

Výše uvedená událost neurčuje verzi novějšího softwaru. Měli byste přejít na odkaz ve zprávě o události pro tyto informace.

Tato událost bude vyzařována i v případě, že je agent proxy nakonfigurován s povoleným automatickým upgradem.

## <a name="next-steps"></a>Další kroky

[Poradce při potížích s ochranou heslem služby Azure AD](howto-password-ban-bad-on-premises-troubleshoot.md)

Další informace o globálních a vlastních seznamech zakázaných hesel naleznete v článku [Zákaz chybná hesla](concept-password-ban-bad.md)
