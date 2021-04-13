---
title: Sledování odhadů cen nákladů na virtuální počítače s Windows – Azure
description: Jak odhadnout náklady a ceny za používání Azure Monitor pro virtuální počítače s Windows
author: Heidilohr
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 26262f83e14602d6ea93f96ec47630ef870c357d
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309301"
---
# <a name="estimate-azure-monitor-costs"></a>Odhad nákladů na Azure Monitor

Protokoly Azure Monitor jsou služby, které shromažďují, indexují a ukládají data generovaná vaším prostředím. Z tohoto důvodu je cenový model Azure Monitor založený na množství dat, která se přenesou do pracovního prostoru Log Analytics v gigabajtech za den a jsou na ně zpracována (nebo "ingestně"). Náklady na Log Analytics pracovní prostor nejsou založené jenom na objemu shromažďovaných dat, ale také na tom, který plán plateb Azure jste vybrali a jak dlouho se rozhodnete ukládat data, která vaše prostředí vygeneruje.

Tento článek vysvětluje následující věci, které vám pomůžou pochopit, jak ceny v Azure Monitor fungují:

- Jak odhadnout náklady na ingestování a ukládání dat před povolením této funkce
- Postup měření a řízení ingestování a ukládání za účelem snížení nákladů při použití této funkce

>[!NOTE]
> Všechny velikosti a ceny uvedené v tomto článku jsou pouze příklady, které ukazují, jak odhad funguje. Přesnější hodnocení založené na cenovém modelu Azure Monitor Log Analytics a oblasti Azure najdete v tématu [Azure monitor ceny](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="estimate-data-ingestion-and-storage-costs"></a>Odhad příjmu dat a nákladů na úložiště

Doporučujeme použít předdefinovanou sadu dat zapsaných jako protokoly v pracovním prostoru Log Analytics. V následujících ukázkových odhadech se podíváme na fakturovatelná data ve výchozí konfiguraci.

Mezi předdefinované datové sady pro Azure Monitor pro virtuální počítače s Windows patří:

- Čítače výkonu z hostitelů relací
- Protokoly událostí systému Windows z hostitelů relací
- Diagnostika virtuálních klientů Windows z infrastruktury služeb

Náklady na ingestování a ukládání dat závisí na velikosti vašeho prostředí, stavu a využití. Příklady odhadů, které v tomto článku použijeme k výpočtu rozsahu nákladů, který můžete očekávat, je založený na zdravých virtuálních počítačích, na kterých běží, na základě [pokynů pro velikost virtuálních počítačů](/windows-server/remote/remote-desktop-services/virtual-machine-recs), které vám pomůžou vypočítat rozsah příjmu dat a nákladů na úložiště, které byste mohli očekávat.

Virtuální počítač s lehkým využitím, který budeme používat v našem příkladu, obsahuje následující komponenty:

- 4 vCPU, 1 disk
- 16 relací za den
- Průměrná doba trvání relace 2 hodiny (120 minut)
- 100 procesů na relaci

Virtuální počítač využívání energie, který budeme používat v našem příkladu, zahrnuje tyto komponenty:

- 6 vCPU, 1 disk
- 6 relací za den
- Průměrná doba trvání relace 4 hodiny (240 minut)
- 200 procesů na relaci

## <a name="estimating-performance-counter-ingestion"></a>Odhad příjmu čítače výkonu

Čítače výkonu ukazují, jak systém provádí systémové prostředky. Příjem dat čítače výkonu závisí na velikosti a využití vašeho prostředí. Ve většině případů by čítače výkonu měly vytvořit 80 až 99% příjmu dat pro Azure Monitor pro virtuální počítače s Windows.

Než začnete s odhadem, je důležité pochopit, že každý čítač výkonu odesílá data s určitou frekvencí. Nastavíme výchozí vzorkovací frekvenci na minuty (tuto rychlost můžete také upravit v nastavení), ale tato sazba se použije v různých faktorech vynásobení v závislosti na čítači. Rychlost má vliv na tyto faktory:

- Pro každý virtuální počítač (VM) každý čítač odesílá data na virtuální počítač ve vašem prostředí s výchozí vzorkovací sazbou za minutu, když je virtuální počítač spuštěný. Počet záznamů, které čítače odesílají, můžete odhadnout za den vynásobením výchozí vzorkovací frekvence za minutu počtem virtuálních počítačů ve vašem prostředí a následným vynásobením tohoto čísla průměrným časem běhu virtuálního počítače za den.

   Shrnutí:

   Výchozí vzorkovací frekvence za minutu × počet jader procesoru v SKU virtuálního počítače × počet virtuálních počítačů × Průměrná doba běhu virtuálního počítače za den = počet záznamů odeslaných za den

- V případě faktoru pro každý procesor odesílá každý čítač výchozí vzorkovací frekvenci za minutu na vCPU v každém VIRTUÁLNÍm počítači ve vašem prostředí, když je virtuální počítač spuštěný. Počet záznamů, které budou čítače odesílat za den, můžete odhadnout vynásobením výchozí vzorkovací frekvence za minutu počtem jader procesoru v SKU virtuálního počítače a vynásobením tohoto čísla počtem minut, po které se virtuální počítač spustí, a počtem virtuálních počítačů ve vašem prostředí.

   Shrnutí:
   
   Výchozí vzorkovací frekvence za minutu × počet jader procesoru v SKU virtuálního počítače × počet minut, po které se virtuální počítač spouští × počet virtuálních počítačů = počet odeslaných záznamů za den

- Pro faktor každého disku každý čítač odesílá data na základě výchozí vzorkovací frekvence pro každý disk v každém VIRTUÁLNÍm počítači ve vašem prostředí. Počet záznamů, které tyto čítače pošle za den, se rovná výchozí vzorkovací frekvence za minutu vynásobená počtem disků v SKU virtuálního počítače, vynásobený 60 minut za hodinu a nakonec vynásobený průměrem aktivních hodin pro virtuální počítač.

   Shrnutí:

   Výchozí vzorkovací frekvence za minutu × počet disků v SKU virtuálního počítače × 60 minut za hodinu × počet virtuálních počítačů × Průměrná doba běhu virtuálního počítače za den = počet záznamů odeslaných za den

- Pro faktor jednotlivých relací každý čítač odesílá data při výchozím vzorkovacím intervalu pro každou relaci ve vašem prostředí, zatímco je relace připojená. Počet záznamů, které budou tyto čítače odesílat za den, můžete odhadnout vynásobením výchozí vzorkovací frekvence za minutu průměrem počtu relací za den a průměrnou dobou trvání relace.

   Shrnutí:

   Výchozí vzorkovací frekvence za minutu × počet relací za den × Průměrná doba trvání relace = počet odeslaných záznamů za den

- Pro faktor každého procesu každý čítač odesílá data výchozí sazbou pro každý proces v každé relaci ve vašem prostředí. Počet záznamů, které budou tyto čítače odesílat za den, můžete odhadnout vynásobením výchozí vzorkovací frekvence za minutu průměrem počtu relací za den a vynásobením průměrné doby trvání relace a průměrného počtu procesů na relaci.
  
   Shrnutí:

   Výchozí vzorkovací frekvence za minutu × počet relací za den × Průměrná doba trvání relace × průměrný počet procesů na relaci = počet odeslaných záznamů za den

V následující tabulce jsou uvedené 20 čítačů výkonu Azure Monitor shromažďuje virtuální plocha Windows a jejich výchozí počty:

| Název čítače | Výchozí vzorkovací frekvence | Faktor frekvence |
|--------------|---------------------|------------------|
| Logický disk (C:) \\ % volného místa | 60 sekund  | Na disk             |
| Logický disk (C:) \\ Průměrná délka fronty disku   | 30 sekund    | Na disk             |
| Logický disk (C:) \\ Střední doba disku/přenos  | 60 sekund       | Na disk             |
| Logický disk (C:) \\ aktuální délka fronty disku  | 30 sekund      | Na disk             |
| Paměť ( \* ) \\ dostupné v MB | 30 sekund    | Na virtuální počítač  |
| Paměť ( \* ) \\ stránky chyby/s | 30 sekund   | Na virtuální počítač  |
| Paměť ( \* ) \\ stránky/s | 30 sekund   | Na virtuální počítač  |
| Paměť ( \* ) \\ % používaných potvrzených bajtů | 30 sekund    | Na virtuální počítač  |
| Fyzický disk ( \* ) \\ Průměrná délka fronty disku | 30 sekund      | Na disk             |
| Fyzický \* disk () \\ Střední doba disku/čtení | 30 sekund  | Na disk             |
| Fyzický disk ( \* ) \\ Střední doba disku/přenos | 30 sekund  | Na disk             |
| Fyzický \* disk () \\ Střední doba disku/zápis | 30 sekund | Na disk             |
| Informace o procesoru (_Total) \\ % času procesoru | 30 sekund | Za jádro/procesor         |
| Aktivní relace Terminálové služby ( \* ) \\          | 60 sekund | Na virtuální počítač  |
| Neaktivní relace Terminálové služby ( \* ) \\        | 60 sekund | Na virtuální počítač  |
| \*Celkový počet relací Terminálové služby () \\ | 60 sekund | Na virtuální počítač  |
| Zpoždění vstupu uživatele na proces ( \* ) \\ maximálního zpoždění vstupu         | 30 sekund | Za proces          |
| Zpoždění vstupu uživatele na relaci ( \* ) \\ Max. zpoždění vstupu        | 30 sekund | Za relaci          |
| Síť RemoteFX ( \* ) \\ aktuální doba RTT TCP | 30 sekund | Na virtuální počítač  |
| \* \\ Aktuální šířka pásma UDP sítě RemoteFX     | 30 sekund | Na virtuální počítač  |

Pokud vyhodnotí každou velikost záznamu na 200 bajtů, ukázkový virtuální počítač, který používá úlohu s nejnižším zatížením na základě výchozí vzorkovací frekvence, pošle zhruba 90 megabajtů dat čítače výkonu za den v jednom virtuálním počítači. Příklad: virtuální počítač se spuštěnou úlohou napájení by mohl na každý virtuální počítač odeslat zhruba 130 megabajtů dat čítače výkonu za den. Velikost záznamů a využití prostředí se ale může lišit, takže megabajty za den, kdy se vaše nasazení používá, můžou být odlišné.

Další informace o čítačích výkonu zpoždění vstupu najdete v tématu [čítače výkonu zpoždění vstupu uživatele](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

## <a name="estimating-windows-event-log-ingestion"></a>Odhad přijímání protokolů událostí systému Windows

Protokoly událostí systému Windows jsou zdroje dat shromažďované agenty Log Analytics na virtuálních počítačích s Windows. Můžete shromažďovat události ze standardních protokolů, jako jsou systém a aplikace, i vlastní protokoly vytvořené aplikacemi, které potřebujete monitorovat.

Toto jsou výchozí události Windows pro Azure Monitor pro virtuální počítače s Windows:

- Aplikace
- Microsoft-Windows-TerminalServices-RemoteConnectionManager/admin
- Microsoft-Windows-TerminalServices-LocalSessionManager/Operational
- Systémový
- Microsoft-FSLogix – aplikace/provozní
- Microsoft-FSLogix – aplikace/Správce

Události systému Windows odesílají pokaždé, když se podmínky události splní v prostředí. Počítače v zdravých stavech odesílají méně událostí než počítače ve stavech není v pořádku. Vzhledem k tomu, že počet událostí je nepředvídatelný, používáme rozsah 1 000 až 10 000 událostí na každý virtuální počítač na základě příkladů z zdravých prostředí pro tento odhad. Pokud například vyhodnotí každou velikost záznamu události v tomto příkladu tak, aby byla 1 500 bajtů, tak přibližně 2 až 15 megabajtů dat událostí za den pro zadané prostředí.

Další informace o událostech systému Windows naleznete v tématu [vlastnosti záznamů událostí systému Windows](../azure-monitor/agents/data-sources-windows-events.md).

## <a name="estimating-diagnostics-ingestion"></a>Odhad přijímání diagnostiky

Služba diagnostiky vytvoří protokoly aktivit pro uživatele i akce správy.

Toto jsou názvy protokolů aktivit, které čítač diagnostiky sleduje:

- WVDCheckpoints
- WVDConnections
- WVDErrors
- WVDFeeds
- WVDManagement
- WVDAgentHealthStatus

Služba odesílá diagnostické informace vždy, když prostředí splňuje podmínky vyžadované k vytvoření záznamu. Vzhledem k tomu, že se počet záznamů diagnostiky nepředpokládá, používáme pro tento odhad rozsah 500 až 1000 událostí na jeden virtuální počítač a každý den podle příkladů.

Pokud například vyhodnotí každou velikost diagnostického záznamu v tomto příkladu tak, aby byla 200 bajtů, pak celkový počet zpracovaných dat bude menší než 1 MB na jeden virtuální počítač za den.

Další informace o kategoriích protokolů aktivit najdete v tématu [Diagnostika virtuálních klientů Windows](diagnostics-log-analytics.md).

## <a name="estimating-total-costs"></a>Odhad celkových nákladů

Nakonec odhadujte celkové náklady. V tomto příkladu řekněme, že jsme narazili na následující výsledky na základě ukázkových hodnot v předchozích částech:

| Zdroj dat        | Odhad velikosti za den (v megabajtech)   |
|-------------------------------------|------------------------------------------|
| Čítače výkonu   | 90-130 |
| Události    | 2-15 |
| Diagnostika virtuálních klientů Windows | \< první |

V tomto příkladu je celkový počet zpracovaných dat pro Azure Monitor pro virtuální počítač s Windows mezi 92 až 145 megabajtů za den. Jinými slovy, každých 31 dní, každý virtuální počítač ingestuje zhruba 3 až 5 gigabajtů dat.

Pomocí výchozího modelu průběžných plateb pro [Log Analytics ceny](https://azure.microsoft.com/pricing/details/monitor/)můžete odhadnout Azure monitor shromažďování dat a náklady na úložiště za měsíc. V závislosti na příjmu dat můžete také zvážit model rezervace kapacity pro Log Analytics ceny.

## <a name="manage-your-data-ingestion-to-reduce-costs"></a>Správa příjmu dat za účelem snížení nákladů

V této části se dozvíte, jak měřit a spravovat přijímání dat za účelem snížení nákladů.

Další informace o správě práv a oprávnění k sešitu najdete v tématu [řízení přístupu](../azure-monitor/visualize/workbooks-access-control.md).

>[!NOTE]
>Odebírání datových bodů ovlivní odpovídající vizuály v Azure Monitor pro virtuální počítače s Windows.

### <a name="log-analytics-settings"></a>Nastavení Log Analytics

Tady jsou některé návrhy na optimalizaci nastavení Log Analytics pro správu příjmu dat:

- Pro prostředky virtuálních počítačů s Windows použijte určený pracovní prostor Log Analytics, abyste zajistili, že Log Analytics jenom shromažďuje čítače výkonu a události pro virtuální počítače v nasazení virtuálních počítačů s Windows.
- Upravte nastavení úložiště Log Analytics, abyste mohli spravovat náklady. Dobu uchovávání můžete zkrátit, vyhodnotit, jestli cenová úroveň pevného úložiště bude cenově výhodnější, nebo nastavit hranice na množství dat, která můžete ingestovat, aby se omezil dopad na nasazení, které není v pořádku. Další informace najdete v tématu [Správa využití a nákladů na protokoly Azure monitor](../azure-monitor/platform/manage-cost-storage.md).

### <a name="remove-excess-data"></a>Odebrat nadbytečná data

Naše výchozí konfigurace je jediná sada dat, kterou doporučujeme Azure Monitor pro virtuální počítače s Windows. Kdykoli máte možnost přidat další datové body a zobrazit je v diagnostice hostitele: prohlížeč hostitele nebo sestavit vlastní grafy pro ně, ale přidaná data budou zvýšit náklady na Log Analytics. Ty je možné odebrat z důvodu úspory nákladů.

### <a name="measure-and-manage-your-performance-counter-data"></a>Měření a správa dat čítače výkonu

Vaše skutečné náklady na monitorování budou záviset na velikosti vašeho prostředí, využití a stavu. Informace o tom, jak měřit příjem dat v pracovním prostoru Log Analytics, najdete v tématu [vysvětlení objemu dat](../azure-monitor/logs/manage-cost-storage.md#understanding-ingested-data-volume)s příjmem protokolu.

Čítače výkonu, které hostitelé relace používají, budou pravděpodobně největší zdrojem ingestně zpracovaných dat pro Azure Monitor pro virtuální počítače s Windows. Následující vlastní šablona dotazu pro Log Analytics pracovní prostor může sledovat četnost a počet megabajtů, které jsou v megabajtech v průběhu posledního dne sledovány na čítači výkonu:

```azure
let WVDHosts = dynamic(['Host1.MyCompany.com', 'Host2.MyCompany.com']);
Perf
| where TimeGenerated > ago(1d)
| where Computer in (WVDHosts)
| extend PerfCounter = strcat(ObjectName, ":", CounterName)
| summarize Records = count(TimeGenerated), InstanceNames = dcount(InstanceName), Bytes=sum(_BilledSize) by PerfCounter
| extend Billed_MBytes = Bytes / (1024 * 1024), BytesPerRecord = Bytes / Records
| sort by Records desc
```

>[!NOTE]
>Zástupné hodnoty šablony nahraďte hodnotami, které vaše prostředí používá, jinak dotaz nebude fungovat.

Tento dotaz zobrazí všechny čítače výkonu, které jste v prostředí povolili, a ne jenom výchozí hodnoty pro Azure Monitor pro virtuální počítače s Windows. Tyto informace vám můžou porozumět tomu, které oblasti se mají zaměřit, aby se snížily náklady, jako je omezení četnosti počítadla nebo jejich zcela odebrání.

Odebráním čítačů výkonu můžete také snížit náklady. Informace o tom, jak odebrat čítače výkonu nebo upravit existující čítače za účelem snížení četnosti, najdete v tématu [Konfigurace čítačů výkonu](../azure-monitor/platform/data-sources-performance-counters.md#configuring-performance-counters).

### <a name="manage-windows-event-logs"></a>Správa protokolů událostí systému Windows

Události systému Windows nejsou pravděpodobně příčinou špičky při příjmu dat, když jsou všichni hostitelé v dobrém stavu. Hostitel, který není v pořádku, může zvýšit počet událostí odeslaných do protokolu, ale tyto informace můžou být klíčové pro řešení problémů hostitele. Doporučujeme je zachovat. Další informace o správě protokolů událostí systému Windows najdete v tématu [Konfigurace protokolů událostí systému Windows](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs).

### <a name="manage-diagnostics"></a>Správa diagnostiky

Diagnostika virtuálních počítačů s Windows by měla snížit náklady na úložiště na více než 1%, takže je nedoporučujeme odebírat. Pokud chcete spravovat diagnostiku virtuálních počítačů s Windows, [použijte pro diagnostickou funkci Log Analytics](diagnostics-log-analytics.md).

## <a name="next-steps"></a>Další kroky

Další informace o Azure Monitor pro virtuální počítače s Windows najdete v těchto článcích:

- [K monitorování nasazení použijte Azure monitor pro virtuální počítač s Windows](azure-monitor.md).
- K získání dalších informací o pojmech a konceptech použijte [Glosář](azure-monitor-glossary.md) .
- Pokud narazíte na problém, Projděte si [Průvodce řešením potíží](troubleshoot-azure-monitor.md) , kde najdete nápovědu.
- Podívejte se na [sledování využití a odhadované náklady v Azure monitor](../azure-monitor/usage-estimated-costs.md) , kde se dozvíte víc o správě nákladů na monitorování.
