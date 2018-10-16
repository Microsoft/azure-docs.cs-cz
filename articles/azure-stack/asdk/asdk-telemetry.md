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
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 0e8138f66c9284531b9610c9bc2996974e2075ad
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339505"
---
# <a name="azure-stack-telemetry"></a>Telemetrická data Azure Stack

Data systému Azure Stack nebo telemetrická data, je automaticky odeslána společnosti Microsoft prostřednictvím uživatelského rozhraní připojené. Data shromážděná z telemetrických dat služby Azure Stack se používá v Microsoft teams hlavně kvůli zlepšení našich zkušeností zákazníků a pro analýzu zabezpečení, stavu, kvality a výkonu.

Telemetrie jako operátory Azure stacku, můžou poskytovat cenné informace v podnikových nasazeních a poskytuje hlasový vstup, který pomáhá tvar budoucí verze služby Azure Stack.

> [!NOTE]
> Azure Stack může také nakonfigurován pro informace o přesměrování využití Azure pro účely fakturace. Toto je nezbytné pro Azure Stack Několikauzlovými zákazníky, kteří zvolte platit jako využití fakturace. Generování sestav o využívání je řízen nezávisle z telemetrických dat a nevyžaduje pro zákazníky s několika uzly, kteří se rozhodnou kapacitního modelu, nebo pro uživatele Azure Stack Development Kit. Pro tyto scénáře vytváření sestav využití může být vypnuto [pomocí registrace skriptu](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting).

Telemetrická data Azure Stack je založená na Windows serveru 2016 připojené uživatelské prostředí a Telemetrie součásti, která používá [trasování událostí pro Windows (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) protokolování technologie ke shromažďování a ukládání telemetrických událostí a data trasování. Komponenty služby Azure Stack pomocí stejné technologie protokolování publikovat události a data, která se shromažďují pomocí veřejného operačního systému protokolování událostí a trasování rozhraní API. Příklady komponenty služby Azure Stack: poskytovatel síťových prostředků, poskytovatel prostředků úložiště, monitorování poskytovatele prostředků a aktualizace poskytovatele prostředků. Součásti připojené uživatelské prostředí a Telemetrie zašifruje data pomocí protokolu SSL a odesílání telemetrických dat přes protokol HTTPS na službu pro správu dat společnosti Microsoft pomocí Připnutí certifikátu.

> [!NOTE]
> Pro podporu tok telemetrických dat, musí být otevřený ve vaší síti port 443 (HTTPS). Součásti připojené uživatelské prostředí a Telemetrie připojí ke službě správy dat společnosti Microsoft na https://v10.vortex-win.data.microsoft.com. Součásti připojené uživatelské prostředí a Telemetrie se také připojuje k https://settings-win.data.microsoft.com ke stažení informací o konfiguraci.

## <a name="privacy-considerations"></a>Aspekty ochrany osobních údajů
Trasování událostí pro Windows service směruje telemetrická data do služby chráněný cloud storage. Princip nejméně privilegovaný přístup příručky k telemetrická data. Pouze pracovníci společnosti Microsoft s platným obchodní potřebě mají povolen přístup k telemetrická data. Společnost Microsoft neposkytuje osobních údajů našich zákazníků s třetími stranami, s výjimkou, na základě vlastního uvážení zákazníka nebo pro účely omezené podle [prohlášení o ochraně osobních údajů služby Azure Stack](https://privacy.microsoft.com/PrivacyStatement). Sdílíme obchodní sestavy s výrobci OEM a partneři, které obsahují informace o agregovaných, anonymizované telemetrii. Data sdílení rozhodnutí, která vyrábí celá interní Microsoft team, včetně ochrany osobních údajů, právní informace a data správy zúčastněných stran.

Společnost Microsoft se řídí zásadou v a postupy pro minimalizaci informací. Snažíme se, jak získat jenom informace, že potřebujeme a uložíme ho pouze pokud ho potřebujete k poskytování služeb nebo pro analýzu. Většinu informací o fungování systémech pro Azure Stack a Azure services je odstranit do šesti měsíců. Shrnuto nebo agregovaná data uchovávají po delší dobu.

Chápeme, že soukromí a bezpečnost těchto informací našich zákazníků je důležité. Přesměrovali jsme promyšleného a komplexní přístupu pro ochranu osobních údajů zákazníků a ochraně zákaznických dat pomocí služby Azure Stack. Správci IT mají ovládací prvky pro přizpůsobení funkce a nastavení ochrany osobních údajů v každém okamžiku. Je jasné naší snahy o transparentnost a vztahu důvěryhodnosti:
- Jsme už nejde otevřít se zákazníky o typech dat, které shromažďujeme.
- Máme podnikových zákazníků v ovládacím prvku, můžou přizpůsobit svoje vlastní nastavení ochrany osobních údajů.
- Máme zákazníků o ochraně osobních údajů a zabezpečení nejprve.
- Nepovedlo se transparentní o získá využití telemetrie.
- Telemetrická data používáme ke zlepšování zkušeností zákazníků.

Microsoft neměli v úmyslu shromažďovat citlivé informace, třeba čísla kreditních karet, uživatelská jména a hesla, e-mailové adresy nebo jiné podobně citlivé informace. Pokud vyhodnotíme neúmyslně přijatých citlivé informace, budeme ji odstranit.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Příklady, jak Microsoft využívá telemetrická data
Telemetrie hraje důležitou roli pomáhají rychle zjistit a opravit chyby kritické spolehlivosti nasazení a konfigurace našich zákazníků. Přehled o telemetrická data, která shromažďujeme pomáhají rychle identifikovat problémy s služby nebo konfigurací hardwaru. K získání těchto dat zákazníků a vylepšovat do ekosystému Microsoftu neblokujete možnost pomáhá zvedají laťku kvality naše integrované řešení Azure Stack.

Telemetrická data také pomáhají společnosti Microsoft lépe pochopit, jak zákazníci nasadit komponenty, používat funkce a služby použít k dosažení firemních cílů. Získávání informací z dat pomáhá nastavit priority investic v oblasti, které můžou mít přímo vliv prostředí našich zákazníků a úloh.

Mezi příklady patří využití ze strany zákazníků kontejnery, úložiště a síťové konfigurace, které jsou spojené s rolemi Azure stacku. Použijeme také insights vylepšovat a inteligenci v některé z našich správy a monitorování řešení. To pomáhá zákazníkům diagnostikovat problémy s kvalitou a ušetřit peníze tím, že méně podporu volá do Microsoftu.

## <a name="manage-telemetry-collection"></a>Správa shromažďování telemetrie
Nedoporučujeme, můžete vypnout telemetrii ve vaší organizaci telemetrie, které poskytuje data, která řídí funkce vylepšení produktu a stabilitu. Chápeme ale, že v některých případech to může být nutné.

V těchto případech můžete nakonfigurovat úroveň telemetrie se Microsoftu posílají pomocí registru nastavení před nasazením nebo po nasazení Telemetrie koncových bodů.

### <a name="set-telemetry-level-in-the-windows-registry"></a>Nastaví úroveň telemetrie v registru Windows
Editor registru Windows se používá k ručnímu nastavení úroveň telemetrie na fyzickém hostitelském počítači před nasazením služby Azure Stack. Pokud už existuje zásad správy, jako je například zásady skupiny, přepíše nastavení registru.

Před nasazením služby Azure Stack na hostiteli development kit, přihlásit se CloudBuilder.vhdx a v okně PowerShell se zvýšenými oprávněními spusťte následující skript:

```powershell
### Get current AllowTelmetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0' # Set this value to 0,1,2,or3.  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

Úrovně telemetrická data jsou kumulativní a zařazená do kategorie do čtyři úrovně (0 – 3):

**0 (zabezpečení)**. Pouze data zabezpečení. Informace, které je potřeba zajistit, aby byl operační systém zabezpečení, včetně dat o nastavení součásti připojené uživatelské prostředí a Telemetrie a programem Windows Defender. Žádná konkrétní telemetrie Azure Stack je vygenerován na této úrovni.

**1 (basic)**. Zabezpečení a základní stav a kvalitu data. Informace o základní zařízení, včetně: data související s kvality, kompatibilita aplikací, data o využití aplikace a data z úroveň zabezpečení. Nastavením telemetrická data úrovně Basic umožňuje službě Azure Stack telemetrie. Data shromážděná na této úrovni zahrnují:

- **Informace o základní zařízení** , pomáhá dosahovat představu o typech a konfigurace nativní a virtualizovanou instancí Windows serveru 2016 v ekosystému, včetně:
  - Počítač atributy, jako je například výrobce OEM, model,
  - Atributy sítě, jako je počet a rychlost síťové adaptéry
  - Procesor a paměť atributy, jako je počet jader, velikosti paměti
  - Úložiště atributů, jako je počet jednotek, typ a velikost.
- **Telemetrické funkce**, včetně procent společností z žebříčku nahrané události, události vynechané a poslední doba nahrávání.
- **Informace týkající se kvality** , který pomáhá rozvíjet základní znalosti o jaký je výkon služby Azure Stack společnosti Microsoft. Příkladem je počet kritických výstrah na konkrétní konfiguraci.
- **Data o kompatibilitě**, která pomáhá poskytnout představu o tom, které jsou nainstalované v systému a virtuálního počítače poskytovatele prostředků a identifikuje potenciální problémy s kompatibilitou.

**2 (rozšířená)**. Další přehledy, včetně: použití operačního systému a další služby Azure Stack, jak jsou výkonné, pokročilé spolehlivost dat a data z úrovní Basic a zabezpečení.

**3 (úplné)**. Všechna data potřebná k identifikaci a k řešení problémů a data ze **zabezpečení**, **základní**, a **rozšířená** úrovně.

> [!NOTE]
> Výchozí hodnota úrovně telemetrie je 2 (rozšířené).

Vypnutí telemetrie Windows a ve službě Azure Stack zakáže SQL telemetrie. Další informace o důsledcích nastavení telemetrie Windows serveru najdete v článku [dokument White Paper Telemetrie Windows](https://aka.ms/winservtelemetry).

> [!IMPORTANT]
> Tyto úrovně telemetrie platí jenom pro komponenty Microsoft Azure Stack. Součásti od jiných výrobců softwaru a služeb, které jsou spuštěny v hostitelském hardwaru životního cyklu od partnerů pro hardware Azure Stack může komunikovat s jejich cloudové služby mimo tyto úrovně telemetrická data. Měli byste u svého poskytovatele řešení hardware Azure Stack vám pomohou pochopit jejich zásady telemetrická data a jak můžete vyjádřit výslovný souhlas nebo odhlásit se totiž pracovat.

### <a name="enable-or-disable-telemetry-after-deployment"></a>Povolit nebo zakázat telemetrii po nasazení

K povolení nebo zakázání telemetrie po nasazení, musíte mít přístup k privilegované koncový bod (období), který je zveřejněný na virtuálních počítačích ERCS.
1.  Pokud chcete povolit: `Set-Telemetry -Enable`
2.  Chcete-li zakázat: `Set-Telemetry -Disable`

Parametr podrobnosti:
> . Parametr Enable - zapnout odesílání dat telemetrie

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
[Spuštění a zastavení ASDK](asdk-start-stop.md)
