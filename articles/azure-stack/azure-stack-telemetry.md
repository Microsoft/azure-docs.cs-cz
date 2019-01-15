---
title: Telemetrická data Azure Stack | Dokumentace Microsoftu
description: Popisuje postup konfigurace nastavení telemetrie Azure Stack powershellu.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: comartin
ms.openlocfilehash: 190a80d5807dcc8ad9666d3ba450691bc6453b41
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265613"
---
# <a name="azure-stack-telemetry"></a>Telemetrická data Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Azure Stack telemetrie automaticky nahrává data systému společnosti Microsoft prostřednictvím uživatelského rozhraní připojené. Týmy Microsoftu používají data, která ke zlepšení zkušeností zákazníků shromažďuje telemetrická data Azure Stack. Tato data se také používá pro zabezpečení, stavu, kvality a analýza výkonu.

Telemetrie pro operátory Azure stacku, můžou poskytovat cenné informace v podnikových nasazeních a poskytuje hlasový vstup, který pomáhá tvar budoucí verze služby Azure Stack.

> [!NOTE]
> Můžete také nakonfigurovat služby Azure Stack k předávání informací o využití do Azure pro účely fakturace. Toto je nezbytné pro Azure Stack Několikauzlovými zákazníky, kteří zvolte platit jako využití fakturace. Generování sestav o využívání je řízen nezávisle z telemetrických dat a nevyžaduje pro zákazníky s několika uzly, kteří se rozhodnou kapacitního modelu, nebo pro uživatele Azure Stack Development Kit. Pro tyto scénáře vytváření sestav využití může být vypnuto [pomocí registrace skriptu](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting).

Telemetrická data Azure Stack je založená na Windows serveru 2016 připojené uživatelské prostředí a Telemetrie součásti, která používá [trasování událostí pro Windows (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) TraceLogging technologie ke shromažďování a ukládání události a data. Komponenty služby Azure Stack pomocí stejné technologie publikovat události a data shromáždit pomocí veřejného operačního systému protokolování událostí a trasování rozhraní API. Tyto součásti služby Azure Stack příklady tito poskytovatelé: Síťového prostředku, prostředek úložiště, sledování prostředků a provést upgrade prostředku. Součást připojené uživatelské prostředí a Telemetrie zašifruje data pomocí protokolu SSL a používá Připnutí certifikátu k přenosu dat prostřednictvím protokolu HTTPS na službu pro správu dat společnosti Microsoft.

> [!IMPORTANT]
> Pokud chcete povolit tok telemetrických dat, musí být otevřený ve vaší síti port 443 (HTTPS). Součásti připojené uživatelské prostředí a Telemetrie připojí ke službě správy dat společnosti Microsoft na https://v10.vortex-win.data.microsoft.com. Součásti připojené uživatelské prostředí a Telemetrie se také připojuje k https://settings-win.data.microsoft.com ke stažení informací o konfiguraci.

## <a name="privacy-considerations"></a>Aspekty ochrany osobních údajů

Trasování událostí pro Windows service směruje telemetrická data do služby chráněný cloud storage. Princip nejnižších oprávnění vodítka přístup k telemetrická data. Pouze pracovníci společnosti Microsoft s platným obchodní potřebě získávali přístup k telemetrická data. Microsoft nebude sdílet osobní zákaznická data s třetími stranami, s výjimkou uvážení zákazníka nebo omezené důvodů popsaných v [prohlášení o ochraně osobních údajů Microsoft](https://privacy.microsoft.com/PrivacyStatement). Obchodní sestavy, které jsou sdíleny s výrobci OEM a partnery zahrnovat agregované, anonymizovaná data. Data sdílení rozhodnutí, která vyrábí celá interní Microsoft team, včetně ochrany osobních údajů, právní informace a data správy zúčastněných stran.

Společnost Microsoft se řídí zásadou v a postupy pro minimalizaci informací. Usilujeme o získání informací, které je potřeba a uložte ho pro pouze po dobu podle potřeby k poskytování služeb nebo pro analýzu. Většinu informací o fungování systémech pro Azure Stack a Azure services je odstranit do šesti měsíců. Shrnuto nebo agregovaná data se uchovávají po delší dobu.

Chápeme, že je důležité o ochraně osobních údajů a zabezpečení informací o zákaznících.  Microsoft používá přístup promyšleného a komplexní ochranu osobních údajů zákazníků a ochraně zákaznických dat ve službě Azure Stack. Správci IT mají ovládací prvky pro přizpůsobení funkce a nastavení ochrany osobních údajů v každém okamžiku. Je jasné naší snahy o transparentnost a vztahu důvěryhodnosti:

- Jsme otevření se zákazníky o typech dat, které shromažďujeme.
- Máme podnikových zákazníků v ovládacím prvku, můžou přizpůsobit svoje vlastní nastavení ochrany osobních údajů.
- Máme zákazníků o ochraně osobních údajů a zabezpečení nejprve.
- Jsme transparentní o tom, jak získává telemetrická data používají.
- Telemetrická data používáme ke zlepšování zkušeností zákazníků.

Microsoft nebude v úmyslu shromažďovat citlivá data, třeba čísla kreditních karet, uživatelská jména a hesla, e-mailové adresy nebo podobné citlivé informace. Pokud vyhodnotíme neúmyslně přijatých citlivé informace, budeme ji odstranit.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Příklady, jak Microsoft využívá telemetrická data

Telemetrie hraje důležitou roli v pomáhá rychle identifikovat a opravit problémy se spolehlivostí důležité v zákaznických nasazení a konfigurace. Přehledy na základě telemetrických dat může pomoci identifikovat problémy s službami nebo konfigurací hardwaru. Možnosti Microsoftu k získání těchto dat zákazníků a vylepšovat k ekosystému, vyvolá laťku kvality integrované řešení pro Azure Stack.

Telemetrická data také pomáhají společnosti Microsoft lépe pochopit, jak zákazníci nasadit komponenty, používat funkce a služby použít k dosažení firemních cílů. Těmto poznatkům pomáhají nastavit priority investic v oblasti, které můžou mít přímo vliv spokojenost zákazníků a úloh.

Mezi příklady patří používání zákazníka kontejnery, úložiště a síťové konfigurace, které jsou spojené s rolemi Azure stacku. Použijeme také insights vylepšovat a inteligenci v Azure stacku správu a sledování řešení. Tato vylepšení usnadňují zákazníky k diagnostice problémů a ušetřit peníze tím, že méně podporu volá společnosti Microsoft.

## <a name="manage-telemetry-collection"></a>Správa shromažďování telemetrie

Není doporučenou vypnutí telemetrie ve vaší organizaci. Ale v některých případech to může být nezbytné.

V těchto scénářích platí můžete nakonfigurovat úroveň telemetrie, které jsou odesílány společnosti Microsoft s použitím nastavení registru, než nasadíte Azure Stack nebo s použitím koncových bodů telemetrická data, poté, co nasadíte Azure Stack.

### <a name="telemetry-levels-and-data-collection"></a>Telemetrie úrovně a shromažďování dat

Než změníte nastavení telemetrie, měli byste porozumět úrovně telemetrie a jaká data se shromažďují na každé úrovni.

Nastavení telemetrie jsou seskupené do čtyři úrovně (0 – 3), které jsou kumulativní a zařazená do kategorie takto:

**0 (zabezpečení)**</br>
Pouze data zabezpečení. Informace, které je potřeba zabezpečit operačního systému. To zahrnuje data o nastavení součásti připojené uživatelské prostředí a Telemetrie a programem Windows Defender. Žádná telemetrie, které jsou specifické pro Azure Stack je vygenerován na této úrovni.

**1 (basic)**</br>
Zabezpečení a základní stav a kvalitu data. Informace o základní zařízení, včetně: týkající se kvality, kompatibilita aplikací, data o využití aplikace a data z **zabezpečení** úroveň. Nastavením telemetrická data úrovně Basic umožňuje službě Azure Stack telemetrie. Data shromážděná na této úrovni zahrnují:

- *Informace o základní zařízení* , která poskytuje představu o typech a konfigurace nativní a virtuální instancí Windows serveru 2016 v ekosystému. To zahrnuje:

  - Počítač atributy, jako je například výrobce OEM a modelu.
  - Atributy, jako je počet síťových adaptérů a jejich rychlosti sítě.
  - Procesor a paměť atributy, jako je počet jader a množství nainstalované paměti.
  - Úložiště atributů, jako je počet jednotek, typ disku a velikost jednotky.

- *Telemetrické funkce*, včetně procento nahrané události, přetažené události a data poslední doba nahrávání.
- *Informace týkající se kvality* , který pomáhá rozvíjet základní znalosti o jaký je výkon služby Azure Stack společnosti Microsoft. Například počet kritických výstrah na konkrétní konfiguraci.
- *Data o kompatibilitě* , pomáhá dosahovat představu o tom, které nainstalují poskytovatele prostředků na systém a virtuální počítač. Ten identifikuje potenciální problémy s kompatibilitou.

**2 (rozšířená)**</br>
Další přehledy, včetně: použití operačního systému a služby Azure Stack, jak provést tyto služby, pokročilé spolehlivost dat a dat z **zabezpečení** a **základní** úrovně.

> [!NOTE]
> Toto je výchozí nastavení telemetrie.

**3 (úplné)**</br>
Všechna data potřebná k identifikaci a k řešení problémů a data ze **zabezpečení**, **základní**, a **rozšířená** úrovně.

> [!IMPORTANT]
> Tyto úrovně telemetrie platí jenom pro komponenty Microsoft Azure Stack. Součásti od jiných výrobců softwaru a služeb, které jsou spuštěny v hostitelském hardwaru životního cyklu od partnerů pro hardware Azure Stack může komunikovat s jejich cloudové služby mimo tyto úrovně telemetrická data. Měli byste u svého poskytovatele řešení hardware Azure Stack vám pomohou pochopit jejich zásady telemetrická data a jak můžete vyjádřit výslovný souhlas nebo odhlásit se totiž pracovat.

Vypnutí telemetrie Windows a ve službě Azure Stack také zakáže SQL telemetrie. Další informace o důsledcích nastavení telemetrie Windows serveru, najdete v článku [dokument White Paper Telemetrie Windows](https://aka.ms/winservtelemetry).

### <a name="asdk-set-the-telemetry-level-in-the-windows-registry"></a>ASDK: Nastaví úroveň telemetrie v registru Windows

Editor registru Windows můžete ručně nastavit úroveň telemetrie na fyzickém hostitelském počítači před nasazením služby Azure Stack. Pokud už existuje zásad správy, jako je například zásady skupiny, přepíše nastavení registru.

Před nasazením služby Azure Stack na hostiteli development kit, spusťte v CloudBuilder.vhdx a v okně PowerShell se zvýšenými oprávněními spusťte následující skript:

```powershell
### Get current AllowTelemetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0' # Set this value to 0,1,2,or3.  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

### <a name="asdk-and-multi-node-enable-or-disable-telemetry-after-deployment"></a>ASDK a více uzly: povolení nebo zakázání telemetrie po nasazení

K povolení nebo zakázání telemetrie po nasazení, musíte mít přístup k privilegované koncový bod (období), který je zveřejněný na virtuálních počítačích ERCS.

1. Pokud chcete povolit: `Set-Telemetry -Enable`
2. Chcete-li zakázat: `Set-Telemetry -Disable`

Parametr podrobnosti:
> . Parametr Enable - zapnout odesílání dat telemetrie</br>
> . Zakažte parametr - vypnout odesílání telemetrických dat  

**Skript pro povolení telemetrie:**

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Enable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

**Skript zakazující telemetrická data:**

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Disable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

## <a name="next-steps"></a>Další postup

[Registrace Azure Stack s Azure](azure-stack-registration.md)