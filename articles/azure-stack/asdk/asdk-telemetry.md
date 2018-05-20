---
title: Azure zásobníku telemetrie | Microsoft Docs
description: Popisuje postup konfigurace nastavení telemetrie zásobník Azure pomocí prostředí PowerShell.
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
ms.date: 05/17/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: bfd16901c5ce036719a1ed19e9a5b5c6ef52be93
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="azure-stack-telemetry"></a>Azure telemetrie zásobníku

Data systému Azure zásobníku nebo telemetrických dat, je automaticky odeslán společnosti Microsoft prostřednictvím uživatelského prostředí připojené. Data shromážděná z Azure zásobníku telemetrie se používá týmy Microsoft hlavně kvůli zlepšení našeho zákazníka prostředí a pro analýzu zabezpečení, stavu, kvality a výkonu.

Jakožto Obsluha zásobník Azure telemetrie může poskytnout cenné informace do podnikového nasazení a poskytuje hlasové, který pomáhá tvar budoucích verzích zásobník Azure.

> [!NOTE]
> Zásobník Azure můžete nakonfigurovat taky k informací o využití dopředného do Azure pro fakturaci. To je potřeba pro více uzly Azure zásobníku zákazníky, kteří zvolte platím jako--používání fakturace. Vytváření sestav využití je řízen nezávisle z telemetrických dat a není vyžadováno pro více uzly zákazníky, kteří vyberte model kapacity, nebo pro uživatele Azure zásobníku Development Kit. Pro tyto scénáře vytváření sestav využití může být vypnuto [pomocí skriptu registrace](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting).

Azure zásobníku telemetrie podle součásti systému Windows Server 2016 připojené uživatelské rozhraní a Telemetrii, kterou používá [trasování událostí pro Windows (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) trasování protokolování technologie ke shromažďování a ukládání telemetrické události a data. Azure součásti zásobníku pomocí stejné technologie protokolování události a data, která jsou shromážděná pomocí veřejného operačního systému protokolování událostí a trasování rozhraní API publikovat. Součástí Azure zásobníku příklady poskytovatele síťových prostředků, zprostředkovatele prostředku úložiště, monitorování poskytovatele prostředků a aktualizace zprostředkovatele prostředků. Součásti připojení činnost koncového uživatele a Telemetrie šifruje data pomocí protokolu SSL a využívá Připnutí certifikátu k přenosu telemetrická data přes protokol HTTPS ke službě pro správu dat společnosti Microsoft.

> [!NOTE]
> Pro podporu tok dat telemetrie, musí být otevřen ve vaší síti port 443 (HTTPS). Součásti připojení činnost koncového uživatele a Telemetrie připojí ke službě pro správu dat společnosti Microsoft na https://v10.vortex-win.data.microsoft.com. Součásti připojení činnost koncového uživatele a Telemetrie také připojí k https://settings-win.data.microsoft.com stáhnout informace o konfiguraci.

## <a name="privacy-considerations"></a>Aspekty ochrany osobních údajů
Trasování událostí pro Windows služby směrování telemetrická data zpět do chráněného cloudového úložiště. Princip nejméně privilegovaných příručky přístup k telemetrická data. Pouze Microsoft pracovníky s platnou obchodních potřeb mají povolen přístup k datům telemetrie. Společnost Microsoft neposkytuje osobních údajů naše zákazníky s třetími stranami, s výjimkou, uvážení zákazníka nebo pro omezené účely popsané v [prohlášení o ochraně osobních údajů zásobník Azure](https://privacy.microsoft.com/PrivacyStatement). Obchodní sestavy můžeme sdílet s výrobci OEM a partnery, které zahrnují agregované, anonymizovaná telemetrické informace. Rozhodnutí o sdílení dat jsou vytvářeny k interní tým Microsoft, včetně ochrany osobních údajů, právních a data správy zúčastněným stranám.

Společnost Microsoft domnívá, v a postupů minimalizace informace. Se snažíme získat pouze informace, budeme potřebovat jsme ji a uložit pouze po dobu jako je třeba k poskytování služeb nebo pro analýzu. Většinu informací o tom, jak funguje systému zásobník Azure a službami Azure se odstraní šest měsíců. Souhrnu nebo jsou agregovaná data uchovávat po delší dobu.

Jsme uvědomit, že je důležité o ochraně osobních údajů a zabezpečení informací našich zákazníků. Přesměrovali jsme žádný jazyk a komplexní přístup na ochrana osobních údajů zákazníků a ochranu dat zákazníka se zásobníkem Azure. Správci IT mají ovládací prvky pro přizpůsobení funkce a nastavení ochrany osobních údajů kdykoli. Projektech pro průhlednost a vztah důvěryhodnosti je zrušte:
- Nepovedlo se otevřít protokolem zákazníků týkající se typy dat, které jsme shromažďovat.
- Můžeme uvést podnikových zákazníků v ovládacím prvku – mohou upravovat vlastní nastavení ochrany osobních údajů.
- Můžeme uvést ochrana osobních údajů zákazníků a zabezpečení nejdřív.
- Snažíme se transparentní o získá použití telemetrie.
- Telemetrie používáme ke zlepšování zkušeností zákazníků.

Microsoft neměli v úmyslu shromažďovat citlivé informace, třeba čísla platebních karet, uživatelská jména a hesla, e-mailové adresy nebo jiné podobně citlivé informace. Pokud jsme určit nechtěně obdržel citlivé informace, budeme ho odstranit.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Příklady, jak společnost Microsoft používá data telemetrie
Telemetrie hraje důležitou roli v tím pomáhá rychle identifikovat a opravit kritická spolehlivost problémy v nasazení a konfigurace našich zákazníků. Přehled telemetrická data, která jsme shromažďovat Pomozte nám rychle identifikovat problémy s služby ani konfigurace hardwaru. Možnost společnosti Microsoft tato data zákazníků a vylepšení jednotku do ekosystému pomáhá zvýšit panelu pro kvalitu našich integrované řešení Azure zásobníku.

Telemetrie také pomáhá tak společnosti Microsoft lépe pochopit, jak zákazníci nasadit komponenty, používat funkce a používat služby, které dosáhli svých cílů firmy. Analyzovat z těchto dat pomáhá určit priority engineering investice do oblasti, které může mít přímý vliv na prostředí našich zákazníků a úloh.

Mezi příklady patří použití zákazníka kontejnery, úložiště a síťové konfigurace, které jsou spojeny s rolemi zásobník Azure. Používáme Statistika vylepšení jednotky a intelligence do některé z našich správy a sledování řešení. To pomáhá zákazníkům diagnostikovat problémy kvality a uložit peníze tím, že méně podporu volá společnosti Microsoft.

## <a name="manage-telemetry-collection"></a>Spravovat kolekce telemetrie
Nedoporučujeme vypnout telemetrie ve vaší organizaci telemetrie, které poskytuje data, která jednotky vylepšené produktu, funkce a stability. Uvědomujeme si ale, že v některých případech to může být nutné.

V těchto případech můžete nakonfigurovat úroveň telemetrie odesílány společnosti Microsoft pomocí před nasazením nastavení registru nebo nasazení post koncové body Telemetrie.

### <a name="set-telemetry-level-in-the-windows-registry"></a>Nastaví úroveň telemetrie v registru systému Windows
Editor registru systému Windows slouží k ručně nastavit úroveň telemetrie ve fyzickém hostitelském počítači před nasazením Azure zásobníku. Pokud je k zásadě správy již existuje, například zásady skupiny, přepíše nastavení registru.

Před nasazením Azure zásobníku na hostiteli development kit, přihlaste se do CloudBuilder.vhdx a v okně Powershellu se zvýšenými oprávněními spusťte následující skript:

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

Úrovně telemetrická data jsou kumulativní a podle kategorií do čtyři úrovně (0-3):

**0 (zabezpečení)**. Pouze data zabezpečení. Informace, které je nutné zajistit, aby byl operační systém zabezpečení, včetně dat o nastavení součásti připojení činnost koncového uživatele a Telemetrie a programem Windows Defender. Žádná konkrétní telemetrie zásobník Azure jsou vydávány na této úrovni.

**1 (základní)**. Zabezpečení a základní stav a kvalita data. Informace o základní zařízení, včetně: data související s kvality, kompatibility aplikací, data o využití aplikace a data z úrovně zabezpečení. Nastavení úrovně vaší telemetrie na základní umožňuje Azure zásobníku telemetrie. Data shromážděná na této úrovni zahrnují:

- **Informace o základní zařízení** , pomáhá poskytovat představu o typech a konfigurace nativní a virtualizované instance Windows Server 2016 v ekosystému, včetně:
 - Počítač atributy, jako například OEM, modelu,
 - Atributy, jako například počet a rychlost síťové adaptéry sítě
 - Procesor a paměť atributy, jako například počet jader, velikost paměti
 - Úložiště atributů, jako je počet jednotek, typ a velikost.
- **Funkce telemetrie**, včetně procent nahrané události, vynechaných události a posledních nahrát čas.
- **Informace týkající se kvality** který pomáhá společnosti Microsoft základní znalosti jak zásobník Azure provádí vývoj. Příkladem je počet kritické výstrahy na konkrétní hardwarová konfigurace.
- ** Kompatibility data, která pomáhá poskytovat představu o tom, které zprostředkovatelé prostředků jsou nainstalovány na systém a virtuální počítač a identifikuje potenciální problémy s kompatibilitou.

**2 (rozšířené)**. Další statistiky, včetně: použití operačního systému a dalším službám Azure zásobníku, jak fungují, pokročilé spolehlivosti dat a dat z úrovní Basic i zabezpečení.

**3 (úplná záloha)**. Všechna data potřebná k identifikaci a k řešení problémů a data ze **zabezpečení**, **základní**, a **rozšířené** úrovně.

> [!NOTE]
> Výchozí hodnota úrovně telemetrie je 2 (rozšířené).

Vypnutí telemetrie pro systém Windows a protokolů Azure zakáže SQL telemetrie. Další informace o důsledky nastavení telemetrie systému Windows Server, podívejte se [Windows Telemetrie dokument White Paper](https://aka.ms/winservtelemetry).

> [!IMPORTANT]
> Tyto úrovně telemetrie platí pouze pro komponenty Microsoft Azure zásobníku. Software od jiných výrobců součástmi a službami, které jsou spuštěny v hostiteli hardwaru životního cyklu od partnerů hardwaru zásobník Azure může komunikovat s jejich cloudových služeb mimo tyto úrovně telemetrie. Měli byste spolupracovat s svého poskytovatele hardwaru řešení Azure zásobníku pochopit svoje zásady telemetrie a jak můžete vyjádřit výslovný souhlas nebo výslovný.

### <a name="enable-or-disable-telemetry-after-deployment"></a>Povolit nebo zakázat telemetrii po nasazení

Pokud chcete povolit nebo zakázat telemetrii po nasazení, musíte mít přístup k privilegovaný koncový bod období která je vystavená na virtuálních počítačích ERCS.
1.  Postup při povolení: `Set-Telemetry -Enable`
2.  Postup při zakázání: `Set-Telemetry -Disable`

Parametr podrobností:
> . Parametr Enable - nahrání dat je zapnout telemetrii

> . Zakažte parametr - vypnout nahrání dat telemetrie  

**Skript pro povolení telemetrická data:**
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

**Skript zakázání telemetrická data:**
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
[Přidat položku marketplace.](asdk-marketplace-item.md)
