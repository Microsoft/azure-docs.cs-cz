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
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: comartin
ms.openlocfilehash: ed3f09f942bdaa803ae8024d5c02230173190107
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248193"
---
# <a name="azure-stack-telemetry"></a>Azure telemetrie zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Azure zásobníku telemetrie automaticky nahrává data systému společnosti Microsoft prostřednictvím uživatelského prostředí připojen. Týmy Microsoftu použijte data, která shromažďuje telemetrická data zásobník Azure k vylepšení zkušeností zákazníků. Tato data se také používají pro zabezpečení, stavu, kvality a analýza výkonu.

Operátor zásobník Azure telemetrie může poskytnout cenné informace do podnikového nasazení a poskytuje hlas, který pomáhá tvar budoucích verzích zásobník Azure.

> [!NOTE]
> Můžete také nakonfigurovat zásobník Azure předávání informací o využití do Azure pro fakturaci. To je potřeba pro více uzly Azure zásobníku zákazníky, kteří zvolte platím jako--používání fakturace. Vytváření sestav využití je řízen nezávisle z telemetrických dat a není vyžadováno pro více uzly zákazníky, kteří vyberte model kapacity, nebo pro uživatele Azure zásobníku Development Kit. Pro tyto scénáře vytváření sestav využití může být vypnuto [pomocí skriptu registrace](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting).

Azure zásobníku telemetrie podle součásti systému Windows Server 2016 připojené uživatelské rozhraní a Telemetrii, kterou používá [trasování událostí pro Windows (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) TraceLogging technologie ke shromažďování a ukládání události a data. Azure součásti zásobníku pomocí stejné technologie publikovat události a data shromáždit pomocí veřejného operačního systému protokolování událostí a trasování rozhraní API. Příklady těchto součástí Azure zásobníku tyto zprostředkovatele: síťových prostředků, prostředků úložiště, monitorování prostředků a prostředků aktualizace. Součásti připojení činnost koncového uživatele a Telemetrie šifruje data pomocí protokolu SSL a používá Připnutí certifikátu pro přenos dat přes protokol HTTPS ke službě pro správu dat společnosti Microsoft.

> [!IMPORTANT]
> Povolit telemetrii tok dat, je třeba otevřít ve vaší síti portu 443 (HTTPS). Součásti připojení činnost koncového uživatele a Telemetrie připojí ke službě pro správu dat společnosti Microsoft na https://v10.vortex-win.data.microsoft.com. Součásti připojení činnost koncového uživatele a Telemetrie také připojí k https://settings-win.data.microsoft.com stáhnout informace o konfiguraci.

## <a name="privacy-considerations"></a>Aspekty ochrany osobních údajů

Trasování událostí pro Windows služby směrování telemetrická data zpět do chráněného cloudového úložiště. Princip nejnižších nutných oprávnění provede přístup k telemetrická data. Pouze Microsoft pracovníky s platnou obchodních potřeb mají přístup k telemetrická data. Microsoft nesdílel osobní zákaznická data s třetími stranami, s výjimkou uvážení zákazníka nebo pro omezené účely popsané v [prohlášení o ochraně osobních údajů Microsoft](https://privacy.microsoft.com/PrivacyStatement). Obchodní sestavy, které jsou sdíleny s výrobci OEM a partnery obsahovat souhrnné, anonymizovaná data. Rozhodnutí o sdílení dat jsou vytvářeny k interní tým Microsoft, včetně ochrany osobních údajů, právních a data správy zúčastněným stranám.

Společnost Microsoft domnívá, v a postupů minimalizace informace. Se snažíme získat pouze informace, které je potřeba a uložit ho pouze po dobu podle potřeby k poskytování služeb nebo pro analýzu. Většinu informací o tom, jak funguje systému zásobník Azure a službami Azure se odstraní šest měsíců. Souhrnu nebo agregovaná data se budou uchovávat po delší dobu.

Jsme uvědomit, že je důležité o ochraně osobních údajů a zabezpečení informací o zákaznících.  Microsoft trvá žádný jazyk a komplexní přístup ochrana osobních údajů zákazníků a ochranu dat zákazníka v zásobníku Azure. Správci IT mají ovládací prvky pro přizpůsobení funkce a nastavení ochrany osobních údajů kdykoli. Projektech pro průhlednost a vztah důvěryhodnosti je zrušte:

- Nemohli jsme Otevřít protokolem zákazníků týkající se typy dat, které jsme shromažďovat.
- Můžeme uvést podnikových zákazníků v ovládacím prvku – mohou upravovat vlastní nastavení ochrany osobních údajů.
- Můžeme uvést ochrana osobních údajů zákazníků a zabezpečení nejdřív.
- Nemohli jsme transparentní o získá použití telemetrická data.
- Telemetrická data používáme ke zlepšování zkušeností zákazníků.

Microsoft nebude v úmyslu shromažďovat citlivá data, třeba čísla platebních karet, uživatelská jména a hesla, e-mailové adresy nebo podobné citlivé informace. Pokud jsme určit nechtěně obdržel citlivé informace, budeme ho odstranit.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Příklady, jak společnost Microsoft používá data telemetrie

Telemetrie hraje důležitou roli v pomáhá rychle identifikovat a opravit problémy se spolehlivosti kritické v zákaznických nasazení a konfigurace. Přehledy z telemetrická data vám pomůže zjistit problémy s služby ani konfigurace hardwaru. Schopnost společnosti Microsoft získat tato data zákazníků a vylepšení jednotku na ekosystému, vyvolá panelu pro kvalitu integrované řešení Azure zásobníku.

Telemetrie také pomáhá tak společnosti Microsoft lépe pochopit, jak zákazníci nasadit komponenty, používat funkce a používat služby, které dosáhli svých cílů firmy. Tyto přehledy pomoci určit priority engineering investice do oblasti, které může mít přímý vliv na prostředí zákazníků a úloh.

Mezi příklady patří použití zákazníka kontejnery, úložiště a síťové konfigurace, které jsou spojeny s rolemi zásobník Azure. Používáme Statistika vylepšení jednotky a intelligence do Azure zásobníku správy a sledování řešení. Tato vylepšení bylo snazší pro zákazníky diagnostikovat problémy a uložte peníze tím, že méně podporu volá společnosti Microsoft.

## <a name="manage-telemetry-collection"></a>Spravovat kolekce telemetrie

Nepodporujeme doporučené vypnutí telemetrie ve vaší organizaci. Ale v některých případech to může být nutné.

V těchto scénářích můžete nakonfigurovat úroveň telemetrie odesílány společnosti Microsoft pomocí nastavení registru před nasazením zásobník Azure nebo pomocí koncových bodů Telemetrie poté, co nasadíte zásobník Azure.

### <a name="telemetry-levels-and-data-collection"></a>Telemetrie úrovně a shromažďování dat

Než změníte nastavení telemetrie, byste měli porozumět úrovně telemetrie a jaká data se shromažďují na každé úrovni.

Nastavení telemetrie jsou seskupené do čtyři úrovně (0-3), které jsou kumulativní a podle kategorií takto:

**0 (zabezpečení)**</br>
Pouze data zabezpečení. Informace, které je potřeba zabezpečit operačního systému. To zahrnuje data o nastavení součásti připojení činnost koncového uživatele a Telemetrie a programem Windows Defender. Žádná telemetrie, které jsou specifické pro Azure zásobníku jsou vydávány na této úrovni.

**1 (basic)**</br>
Zabezpečení a základní stav a kvalita data. Informace o základní zařízení, včetně: data související s kvality, kompatibility aplikací, data o využití aplikace a dat z **zabezpečení** úroveň. Nastavení úrovně vaší telemetrie na základní umožňuje Azure zásobníku telemetrie. Data shromážděná na této úrovni zahrnují:

- *Informace o základní zařízení* představu o typech a konfigurace nativní a virtuální instancí systému Windows Server 2016 v ekosystému, který poskytuje. To zahrnuje:

  - Počítač atributy, jako například výrobce OEM a modelu.
  - Atributy, jako například počet síťových adaptérů a jejich rychlost sítě.
  - Procesor a paměť atributy, jako například počet jader a množství paměti nainstalované.
  - Úložiště atributů, jako jsou například počet jednotek, typ disku a velikost disku.

- *Funkce telemetrie*, včetně procento nahrané události, vynechaných události a data poslední nahrát čas.
- *Informace týkající se kvality* který pomáhá společnosti Microsoft základní znalosti jak zásobník Azure provádí vývoj. Například počet kritické výstrahy na konkrétní hardwarová konfigurace.
- *Údaje o kompatibilitě* , pomáhá poskytovat představu o tom, které jsou nainstalovány zprostředkovatelé prostředků na systém a virtuální počítač. Ten identifikuje potenciální problémy s kompatibilitou.

**2 (rozšířené)**</br>
Další statistiky, včetně: použití operačního systému a služeb Azure zásobníku, jak provést tyto služby, pokročilé spolehlivosti dat a dat z **zabezpečení** a **základní** úrovně.

> [!NOTE]
> Toto je výchozí nastavení telemetrie.

**3 (úplná záloha)**</br>
Všechna data potřebná k identifikaci a k řešení problémů a data ze **zabezpečení**, **základní**, a **rozšířené** úrovně.

> [!IMPORTANT]
> Tyto úrovně telemetrie platí pouze pro komponenty Microsoft Azure zásobníku. Software od jiných výrobců součástmi a službami, které jsou spuštěny v hostiteli hardwaru životního cyklu od partnerů hardwaru zásobník Azure může komunikovat s jejich cloudových služeb mimo tyto úrovně telemetrie. Měli byste spolupracovat s svého poskytovatele hardwaru řešení Azure zásobníku pochopit svoje zásady telemetrie a jak můžete vyjádřit výslovný souhlas nebo výslovný.

Vypnutí telemetrie pro systém Windows a protokolů Azure také zakáže SQL telemetrie. Další informace o důsledcích nastavení telemetrie systému Windows Server najdete v tématu [Windows Telemetrie dokument White Paper](https://aka.ms/winservtelemetry).

### <a name="asdk-set-the-telemetry-level-in-the-windows-registry"></a>ASDK: Nastaví úroveň telemetrie v registru systému Windows

Editor registru systému Windows můžete ručně nastavit úroveň telemetrie ve fyzickém hostitelském počítači před nasazením Azure zásobníku. Pokud je k zásadě správy již existuje, například zásady skupiny, přepíše nastavení registru.

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

### <a name="asdk-and-multi-node-enable-or-disable-telemetry-after-deployment"></a>ASDK a více uzly: Povolit nebo zakázat telemetrii po nasazení

Pokud chcete povolit nebo zakázat telemetrii po nasazení, musíte mít přístup k privilegovaný koncový bod období která je vystavená na virtuálních počítačích ERCS.

1. Postup při povolení: `Set-Telemetry -Enable`
2. Postup při zakázání: `Set-Telemetry -Disable`

Parametr podrobnosti:
> . Parametr Enable - nahrání dat je zapnout telemetrii</br>
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

- [Stažení balíčku pro nasazení Azure zásobníku development kit](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

- [Nasazení zásobník Azure development kit](azure-stack-run-powershell-script.md)
