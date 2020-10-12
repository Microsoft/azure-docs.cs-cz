---
title: Monitorování ochrany heslem místní služby Azure AD
description: Přečtěte si, jak monitorovat a prohlížet protokoly pro ochranu heslem Azure AD pro místní Active Directory Domain Services prostředí.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 02937c22cbc16defb0b7672ac7ebc56c2ae2beb5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89068775"
---
# <a name="monitor-and-review-logs-for-on-premises-azure-ad-password-protection-environments"></a>Monitorování a kontrola protokolů pro místní prostředí ochrany heslem služby Azure AD

Po nasazení ochrany heslem služby Azure AD jsou monitorování a vytváření sestav zásadními úkoly. Tento článek obsahuje podrobné informace, které vám pomůžou pochopit různé techniky monitorování, včetně toho, kde každá služba protokoluje informace a jak ohlásit používání ochrany heslem Azure AD.

Monitorování a vytváření sestav se provádí buď pomocí zpráv protokolu událostí, nebo spuštěním rutin PowerShellu. Agent řadiče domény a proxy služby protokolují obě zprávy protokolu událostí. Všechny rutiny prostředí PowerShell popsané níže jsou k dispozici pouze na proxy server (viz modul prostředí PowerShell pro AzureADPasswordProtection). Software agenta DC neinstaluje modul prostředí PowerShell.

## <a name="dc-agent-event-logging"></a>Protokolování událostí agenta řadiče domény

Na každém řadiči domény software služby agenta DC zapisuje výsledky jednotlivých operací ověření hesla (a další stav) do místního protokolu událostí:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Protokol správce agenta DC je primární zdroj informací o tom, jak se software chová.

Všimněte si, že protokol trasování je ve výchozím nastavení vypnutý.

Události zaznamenané různými součástmi agenta řadiče domény spadají do těchto rozsahů:

|Součást |Rozsah ID události|
| --- | --- |
|Knihovna DLL filtru hesel agenta řadiče domény| 10000-19999|
|Proces hostování služby agenta DC| 20000-29999|
|Logika ověřování zásad služby agenta DC| 30000-39999|

## <a name="dc-agent-admin-event-log"></a>Protokol událostí správce agenta řadiče domény

### <a name="password-validation-outcome-events"></a>Události výsledku ověřování hesla

Na každém řadiči domény software služby agenta DC zapisuje výsledky jednotlivých ověření hesla do protokolu událostí správce agenta řadiče domény.

V případě úspěšné operace ověření hesla je k dispozici obvykle jedna událost, která se zaprotokoluje z knihovny DLL filtru hesel agenta řadiče domény. V případě operace ověřování hesla, která je neúspěšná, jsou k dispozici dvě události, které jsou protokolovány, jedna od služby agenta řadiče domény a druhá z knihovny DLL filtru řadiče domény.

Diskrétní události pro zachycení těchto situací jsou zaznamenány v závislosti na následujících faktorech:

* Zda se dané heslo nastavuje nebo mění.
* Zda bylo ověření daného hesla prošlo nebo nebylo úspěšné.
* Bez ohledu na to, jestli se ověření nepovedlo kvůli globálním zásadám Microsoftu, zásadě organizace nebo kombinaci.
* Zda je v případě současných zásad hesel aktuálně zapnuto nebo vypnuto režim auditování.

Klíčovým událostem souvisejícím s ověřováním hesla jsou následující:

| Událost |Změna hesla |Heslo nastaveno|
| --- | :---: | :---: |
|Dána |10014 |10015|
|Selhání (kvůli zásadám hesel zákazníka)| 10016, 30002| 10017, 30003|
|Selhání (kvůli zásadám hesel Microsoftu)| 10016, 30004| 10017, 30005|
|Selhání (kvůli kombinovaným zásadám hesel Microsoftu a zákazníků)| 10016, 30026| 10017, 30027|
|Úspěšné pouze auditování (by se nezdařila zásada pro heslo zákazníka)| 10024, 30008| 10025, 30007|
|Úspěch pouze proti auditu (by se nezdařila zásada hesla Microsoftu)| 10024, 30010| 10025, 30009|
|Úspěšné pouze auditování (by se nezdařily kombinované zásady pro hesla Microsoftu a zákazníků)| 10024, 30028| 10025, 30029|

Případy v tabulce výše, které odkazují na "kombinované zásady", odkazují na situace, ve kterých bylo nalezeno heslo uživatele, aby obsahovalo alespoň jeden token ze seznamu zakázaných hesel Microsoftu i ze seznamu zakázaných hesel zákazníka.

Je-li protokolována dvojice událostí, jsou obě události explicitně přidruženy pomocí stejného ID korelace.

### <a name="password-validation-summary-reporting-via-powershell"></a>Generování sestav souhrnu ověření hesla přes PowerShell

`Get-AzureADPasswordProtectionSummaryReport`Rutina se dá použít k vytvoření souhrnného zobrazení aktivity ověření hesla. Příklad výstupu této rutiny je následující:

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

Rozsah generování sestav rutiny může být ovlivněn pomocí jednoho z parametrů – doménová struktura,-doména nebo – DomainController. Nepovedlo se určit parametr implikuje – doménová struktura.

`Get-AzureADPasswordProtectionSummaryReport`Rutina funguje tak, že dotazuje protokol událostí správce agenta DC a pak počítá celkový počet událostí, které odpovídají jednotlivým zobrazeným kategoriím výsledek. Následující tabulka obsahuje mapování mezi každým výsledkem a jeho odpovídajícím ID události:

|Get-AzureADPasswordProtectionSummaryReport – vlastnost |ID odpovídajícího události|
| :---: | :---: |
|PasswordChangesValidated |10014|
|PasswordSetsValidated |10015|
|PasswordChangesRejected |10016|
|PasswordSetsRejected |10017|
|PasswordChangeAuditOnlyFailures |10024|
|PasswordSetAuditOnlyFailures |10025|
|PasswordChangeErrors |10012|
|PasswordSetErrors |10013|

Všimněte si, že `Get-AzureADPasswordProtectionSummaryReport` je tato rutina expedována ve formuláři PowerShell Script a v případě potřeby může být odkazována přímo v následujícím umístění:

`%ProgramFiles%\WindowsPowerShell\Modules\AzureADPasswordProtection\Get-AzureADPasswordProtectionSummaryReport.ps1`

> [!NOTE]
> Tato rutina funguje tak, že otevřete relaci PowerShellu pro každý řadič domény. Aby to bylo úspěšné, musí být na každém řadiči domény povolená podpora vzdálené relace PowerShellu a klient musí mít dostatečná oprávnění. Další informace o požadavcích na vzdálenou relaci PowerShellu získáte spuštěním příkazu Get-Help about_Remote_Troubleshooting v okně PowerShellu.

> [!NOTE]
> Tato rutina funguje vzdáleně dotazem na každý protokol událostí správce služby Agent řadiče domény. Pokud protokoly událostí obsahují velký počet událostí, dokončení rutiny může trvat dlouhou dobu. Kromě toho může mít rozsáhlé síťové dotazy velkých datových sad vliv na výkon řadiče domény. Proto by se tato rutina měla pečlivě používat v produkčním prostředí.

### <a name="sample-event-log-message-for-event-id-10014-successful-password-change"></a>Ukázka zprávy protokolu událostí pro ID události 10014 (úspěšná Změna hesla)

```text
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Ukázková zpráva protokolu událostí pro událost s ID 10017 a 30003 (sada hesel se nezdařila)

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

### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Ukázka zprávy protokolu událostí pro událost s ID 30001 (heslo bylo přijato, protože není k dispozici žádná zásada)

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

### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Ukázka zprávy protokolu událostí pro událost s ID 30006 (nové zásady se vynutily)

```text
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

### <a name="sample-event-log-message-for-event-id-30019-azure-ad-password-protection-is-disabled"></a>Zpráva protokolu ukázkové události pro událost s ID 30019 (ochrana heslem Azure AD je zakázaná)

```text
The most recently obtained Azure password policy was configured to be disabled. All passwords submitted for validation from this point on will automatically be considered compliant with no processing performed.

No further events will be logged until the policy is changed.%n

```

## <a name="dc-agent-operational-log"></a>Provozní protokol agenta DC

Služba agenta řadiče domény bude také protokolovat události týkající se provozu do následujícího protokolu:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

## <a name="dc-agent-trace-log"></a>Protokol trasování agenta řadiče domény

Služba agenta řadiče domény může také Protokolovat podrobné události trasování na úrovni ladění v následujícím protokolu:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Protokolování trasování je ve výchozím nastavení zakázáno.

> [!WARNING]
> Pokud je povoleno, protokol trasování obdrží velký objem událostí a může ovlivnit výkon řadiče domény. Proto by tento rozšířený protokol měl být povolen pouze v případě, že problém vyžaduje hlubší šetření a následně pouze v případě minimálního množství času.

## <a name="dc-agent-text-logging"></a>Protokolování textu agenta řadiče domény

Službu agenta DC je možné nakonfigurovat tak, aby zapisovala do textového protokolu nastavením následující hodnoty registru:

```text
HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)
```

Protokolování textu je ve výchozím nastavení zakázáno. Aby se změny této hodnoty projevily, vyžaduje se restartování služby agenta řadiče domény. Pokud je povoleno, služba agenta řadiče domény zapíše do souboru protokolu, který je v umístění:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> Textový protokol obdrží stejné položky na úrovni ladění, které mohou být protokolovány do protokolu trasování, ale obecně ve snazším formátu pro kontrolu a analýzu.

> [!WARNING]
> Pokud je tento protokol povolený, obdrží velký objem událostí a může ovlivnit výkon řadiče domény. Proto by tento rozšířený protokol měl být povolen pouze v případě, že problém vyžaduje hlubší šetření a následně pouze v případě minimálního množství času.

## <a name="dc-agent-performance-monitoring"></a>Monitorování výkonu agenta řadiče domény

Software služby agenta DC nainstaluje objekt čítače výkonu s názvem **ochrana heslem Azure AD**. Nyní jsou k dispozici následující čítače výkonu:

|Název čítače výkonu | Description|
| --- | --- |
|Zpracovaná hesla |Tento čítač zobrazuje celkový počet zpracovaných a odmítnutých hesel od posledního restartování.|
|Hesla přijata |Tento čítač zobrazuje celkový počet hesel, která byla přijata od posledního restartování.|
|Hesla odmítnuta |Tento čítač zobrazuje celkový počet hesel, která byla od posledního restartování odmítnuta.|
|Probíhá požadavek filtru hesel. |Tento čítač zobrazuje počet aktuálně probíhajících požadavků filtru hesel.|
|Požadavky na filtr hesel ve špičce |Tento čítač zobrazuje nejvyšší počet souběžných požadavků filtru hesel od posledního restartování.|
|Chyby žádosti filtru hesel |Tento čítač zobrazuje celkový počet požadavků filtru hesel, které selhaly kvůli chybě od posledního restartování. K chybám může dojít v případě, že služba agenta řadiče domény služby Azure AD heslem není spuštěná.|
|Požadavky filtru hesel za sekundu |Tento čítač zobrazuje rychlost zpracování hesel.|
|Doba zpracování žádosti filtru hesel |Tento čítač zobrazuje průměrnou dobu potřebnou ke zpracování žádosti filtru hesel.|
|Doba zpracování požadavku filtru hesel ve špičce |Tento čítač zobrazuje dobu zpracování požadavku filtru hesel ve špičce od posledního restartování.|
|Hesla přijatá v důsledku režimu auditu |Tento čítač zobrazuje celkový počet hesel, která by byla normálně odmítnuta, ale byla přijata, protože zásady hesel byly nakonfigurovány tak, aby byly v režimu auditování (od posledního restartování).|

## <a name="dc-agent-discovery"></a>Zjišťování agenta řadiče domény

`Get-AzureADPasswordProtectionDCAgent`Rutina se dá použít k zobrazení základních informací o různých agentech řadiče domény spuštěných v doméně nebo doménové struktuře. Tyto informace se načítají z objektů serviceConnectionPoint zaregistrovaných spuštěnými službami agenta řadiče domény.

Příklad výstupu této rutiny je následující:

```powershell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
PasswordPolicyDateUTC : 2/16/2018 8:35:01 AM
HeartbeatUTC          : 2/16/2018 8:35:02 AM
```

Jednotlivé vlastnosti se aktualizují každou službu agenta řadiče domény o přibližné hodinové bázi. Data stále podléhají latenci replikace služby Active Directory.

Rozsah dotazu rutiny může být ovlivněn pomocí parametrů – doménové struktury nebo – Domain.

Pokud je hodnota HeartbeatUTC zastaralá, může se jednat o příznak, že na tomto řadiči domény není spuštěný agent Azure AD Password Protection, nebo se odinstaloval, nebo byl počítač degradován a už není řadičem domény.

Pokud je hodnota PasswordPolicyDateUTC zastaralá, může to být příznak, který agent řadiče domény služby Azure AD pro ochranu hesel na tomto počítači nepracuje správně.

## <a name="dc-agent-newer-version-available"></a>Je dostupná novější verze agenta DC

Služba agenta DC zaznamená událost upozornění 30034 do provozního protokolu při zjištění, že je k dispozici novější verze softwaru agenta řadiče domény, například:

```text
An update for Azure AD Password Protection DC Agent is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
```

Výše uvedená událost neurčuje verzi novějšího softwaru. Měli byste přejít na odkaz ve zprávě události pro tyto informace.

> [!NOTE]
> Bez ohledu na odkazy na možnost autoupgrade ve výše uvedené zprávě o události software agenta řadiče domény tuto funkci v tuto chvíli nepodporuje.

## <a name="proxy-service-event-logging"></a>Protokolování událostí služby proxy

Služba proxy generuje minimální sadu událostí do následujících protokolů událostí:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Všimněte si, že protokol trasování je ve výchozím nastavení vypnutý.

> [!WARNING]
> Pokud je povoleno, protokol trasování obdrží velký objem událostí a to může mít vliv na výkon hostitele proxy serveru. Proto by měl být tento protokol povolen pouze v případě, že problém vyžaduje hlubší šetření a následně pouze za účelem minimálního množství času.

Události jsou protokolovány různými součástmi proxy serveru pomocí následujících rozsahů:

|Součást |Rozsah ID události|
| --- | --- |
|Proces hostování služby proxy| 10000-19999|
|Základní obchodní logika služby proxy| 20000-29999|
|Rutiny prostředí PowerShell| 30000-39999|

## <a name="proxy-service-text-logging"></a>Protokolování textu služby proxy

Službu proxy můžete nakonfigurovat tak, aby zapisovala do textového protokolu nastavením následující hodnoty registru:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters! EnableTextLogging = 1 (hodnota REG_DWORD)

Protokolování textu je ve výchozím nastavení zakázáno. Aby se změny této hodnoty projevily, vyžaduje se restartování služby proxy serveru. Pokud je povoleno, služba proxy zapíše do souboru protokolu, který se nachází v:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> Textový protokol obdrží stejné položky na úrovni ladění, které mohou být protokolovány do protokolu trasování, ale obecně ve snazším formátu pro kontrolu a analýzu.

> [!WARNING]
> Pokud je tento protokol povolený, obdrží velký objem událostí a může mít vliv na výkon počítače. Proto by tento rozšířený protokol měl být povolen pouze v případě, že problém vyžaduje hlubší šetření a následně pouze v případě minimálního množství času.

## <a name="powershell-cmdlet-logging"></a>Protokolování rutin PowerShellu

Rutiny PowerShellu, které vedou ke změně stavu (například Register-AzureADPasswordProtectionProxy), budou normálně protokolovat událost výsledku do provozního protokolu.

Většina rutin PowerShellu pro ochranu heslem Azure AD bude navíc zapisovat do textového protokolu umístěného v:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Pokud dojde k chybě rutiny a řešení příčin and\or není zřejmé, můžou se taky tyto textové protokoly prostudovat.

## <a name="proxy-discovery"></a>Zjišťování proxy

`Get-AzureADPasswordProtectionProxy`Rutina se dá použít k zobrazení základních informací o různých službách proxy ochrany heslem Azure AD spuštěných v doméně nebo doménové struktuře. Tyto informace se načítají z objektů serviceConnectionPoint zaregistrovaných běžícími službami proxy.

Příklad výstupu této rutiny je následující:

```powershell
Get-AzureADPasswordProtectionProxy
ServerFQDN            : bplProxy.bplchild2.bplRootDomain.com
Domain                : bplchild2.bplRootDomain.com
Forest                : bplRootDomain.com
HeartbeatUTC          : 12/25/2018 6:35:02 AM
```

Jednotlivé vlastnosti se aktualizují každou službu proxy serveru o přibližné hodinové bázi. Data stále podléhají latenci replikace služby Active Directory.

Rozsah dotazu rutiny může být ovlivněn pomocí parametrů – doménové struktury nebo – Domain.

Pokud je hodnota HeartbeatUTC zastaralá, může to být příznak, že proxy ochrany heslem služby Azure AD na tomto počítači není spuštěno nebo bylo odinstalováno.

## <a name="proxy-agent-newer-version-available"></a>Je dostupná novější verze agenta proxy.

Služba proxy zaznamená událost upozornění 20002 do provozního protokolu při zjištění, že je k dispozici novější verze softwaru proxy, například:

```text
An update for Azure AD Password Protection Proxy is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
.
```

Výše uvedená událost neurčuje verzi novějšího softwaru. Měli byste přejít na odkaz ve zprávě události pro tyto informace.

Tato událost bude vygenerována i v případě, že je u agenta proxy nakonfigurován povolený autoupgrade.

## <a name="next-steps"></a>Další kroky

[Řešení potíží s ochranou hesel Azure AD](howto-password-ban-bad-on-premises-troubleshoot.md)

Další informace o globálním a vlastním seznamu zakázaných hesel najdete v článku [zákaz chybných hesel](concept-password-ban-bad.md) .
