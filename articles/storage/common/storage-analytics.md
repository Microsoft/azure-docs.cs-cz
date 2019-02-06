---
title: Použití služby Azure Storage analytics ke shromažďování dat protokolů a metrik | Dokumentace Microsoftu
description: Analýza úložiště umožňuje sledovat data metriky pro všechny služby storage a shromáždí protokoly pro úložiště objektů Blob, Queue a Table.
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 1b27bbaa3d8e570c8431708934edee564e994487
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55745652"
---
# <a name="storage-analytics"></a>Storage Analytics

Analýza Azure Storage provádí protokolování a poskytuje data metriky pro účet úložiště. Tato data můžete použít k trasování požadavků, analýze trendů použití a diagnostice problémů s účtem úložiště.

Chcete-li použít funkci analýzy úložiště, musíte povolit ji jednotlivě pro každou službu, kterou chcete monitorovat. Můžete ho povolit [webu Azure Portal](https://portal.azure.com). Podrobnosti najdete v tématu [monitorování účtu úložiště na webu Azure Portal](storage-monitor-storage-account.md). Můžete také povolit Storage Analytics prostřednictvím kódu programu přes rozhraní REST API nebo knihovny klienta. Použití [získat vlastnosti služby Blob Service](https://msdn.microsoft.com/library/hh452239.aspx), [získání vlastností služby Queue](https://msdn.microsoft.com/library/hh452243.aspx), [získat vlastnosti služby tabulky](https://msdn.microsoft.com/library/hh452238.aspx), a [získat File Service Properties](https://msdn.microsoft.com/library/mt427369.aspx)operace povolující analytika úložiště pro každou službu.

Agregovaná data se ukládají v dobře známý objekt blob (pro protokolování) a dobře známých tabulek (pro metriky), které lze získat přístup pomocí služby Blob service a Table service rozhraní API.

Analýza úložiště má limit 20 TB na množství uložených dat, která je nezávislá celkový limit pro váš účet úložiště. Další informace o fakturaci a zásad uchovávání dat najdete v tématu [analytika úložiště a fakturace](https://msdn.microsoft.com/library/hh360997.aspx). Další informace o omezeních účtů úložišť najdete v tématu [Azure Storage škálovatelnost a cíle výkonnosti](storage-scalability-targets.md).

Podrobný průvodce k identifikaci, diagnostice a řešit problémy související s Azure Storage pomocí Storage Analytics a další nástroje, najdete v části [monitorování, Diagnostika a řešení problémů s Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="about-storage-analytics-logging"></a>Informace o protokolování úložiště analytics
Analýza úložiště protokoluje podrobné informace o úspěšné i neúspěšné požadavky do služby úložiště. Tyto informace slouží ke sledování jednotlivých požadavků a Diagnostikujte problémy pomocí služby storage. Žádosti o přihlášení snažíme maximálně vyhovět.

Položky protokolu se vytvoří pouze v případě, že je aktivita služby úložiště. Například pokud účet úložiště má aktivitu ve své službě Blob service, ale ne v jeho tabulek nebo front služby, bude vytvořen pouze protokoly týkající se služby Blob service.

Protokolování analýzy úložiště není k dispozici pro soubory Azure.

### <a name="logging-authenticated-requests"></a>Protokolování ověření požadavků
Jsou zaznamenány následující typy ověřených požadavků:

* Úspěšné požadavky.
* Neúspěšné požadavky, včetně vypršení časového limitu, omezování, sítě, autorizace a dalších chybách.
* Požadavky na použití sdílený přístupový podpis (SAS), včetně úspěšné a neúspěšné požadavky.
* Požadavky na analytics data.

Storage Analytics, jako je protokol vytvoření nebo odstranění, žádosti se neprotokolují. Úplný seznam dat protokolu jsou uvedené v [stavové zprávy a Storage Analytics protokolovanými operacemi](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) a [formát Log Analytics úložiště](/rest/api/storageservices/storage-analytics-log-format) témata.

### <a name="logging-anonymous-requests"></a>Protokolování anonymních požadavků
Jsou zaznamenány následující typy anonymních požadavků:

* Úspěšné požadavky.
* Chyby serveru.
* Chyby časového limitu pro klienta i serveru.
* Neúspěšné požadavky GET s kódem chyby 304 (Neupraveno).

Všechny ostatní neúspěšné anonymní žádosti se neprotokolují. Úplný seznam dat protokolu jsou uvedené v [stavové zprávy a Storage Analytics protokolovanými operacemi](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) a [formát Log Analytics úložiště](/rest/api/storageservices/storage-analytics-log-format) témata.

### <a name="how-logs-are-stored"></a>Jak se ukládají protokoly
Všechny protokoly se ukládají v objektech BLOB bloku v kontejneru nazvaném $logs, které se automaticky vytvoří, když je pro účet úložiště povolená analytika úložiště. Kontejner $logs se nachází v oboru názvů objektů blob v účtu úložiště, například: `http://<accountname>.blob.core.windows.net/$logs`. Tento kontejner nejde odstranit, jakmile bylo povoleno Storage Analytics, i když je možné odstranit její obsah.

> [!NOTE]
> Kontejner $logs se nezobrazí, pokud kontejner výpis operace probíhá, jako [ListContainers](https://msdn.microsoft.com/library/azure/dd179352.aspx) metody. To musí být k nim přistupuje přímo. Například můžete použít [ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx) metodu pro přístup k objektům BLOB ve `$logs` kontejneru.
> Jak žádosti o přihlášení, analytika úložiště odešlete mezilehlých výsledků jako bloky. Analýza úložiště bude pravidelně, potvrďte tyto bloky a zpřístupněte je jako objekt blob.
>
>

Duplicitní záznamy může existovat pro protokoly vytvořené v jedné hodiny. Můžete určit, zda záznam je duplicitní kontrolou **RequestId** a **operace** číslo.

### <a name="log-naming-conventions"></a>Zásady vytváření názvů protokolu
Každý protokol bude zapsán v následujícím formátu.

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log

Následující tabulka popisuje jednotlivé atributy v názvu protokolu.

| Atribut | Popis |
| --- | --- |
| <service-name> |Název služby úložiště. Příklad: Objekt blob, tabulek nebo front. |
| RRRR |Čtyřmístný rok protokolu. Příklad: 2011. |
| MM |Dvoumístným měsícem protokolu. Příklad: 07. |
| DD |Dvoumístným měsícem protokolu. Příklad: 07. |
| hh |Hodina dvou číslic určující počáteční hodina protokolů ve 24hodinovém formátu UTC. Příklad: 18. |
| mm |Dvoumístné číslo určující počáteční minut protokolů. Tato hodnota není podporován v aktuální verzi analytika úložiště a jeho hodnota bude vždy 00. |
| <counter> |Založený na nule čítač se šesti číslicemi, která určuje počet objektů BLOB protokolu vygenerovaný pro službu storage do jedné hodiny časové období. Tento čítač začíná 000000. Příklad: 000001. |

Následuje úplný Ukázkový název protokolu, který kombinuje v předchozích příkladech.

    blob/2011/07/31/1800/000001.log

Následuje ukázkový identifikátor URI, který lze použít pro přístup k předchozím protokolu.

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log

Když se zaznamená žádost úložiště, výsledný název protokolu souvisí s hodina dokončení požadované operace. Například, pokud žádost o GetBlob byla dokončena v 18:30:00 na 7/31/2011 protokolu by být zapsaný s následující předpony: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Metadata protokolu
Všechny objekty BLOB protokolu se ukládají se metadata, která je možné určit protokolování data, která obsahuje objekt blob. Následující tabulka popisuje každý atribut metadat.

| Atribut | Popis |
| --- | --- |
| LogType |Popisuje, zda v protokolu obsahuje informace týkající se číst, zapsat nebo odstranit operace. Tato hodnota může obsahovat jeden typ nebo s použitím kombinace všech tří, oddělené čárkami. Příklad 1: zápis; Příklad 2: Číst, zapisovat; Příklad 3: Číst, zapisovat, odstranit. |
| StartTime |Nejdřívější čas záznam v protokolu ve formátu RRRR-MM-: ssZ. Příklad: 2011-07-31T18:21:46Z. |
| EndTime |Čas posledního položky v protokolu ve formátu RRRR-MM-: ssZ. Příklad: 2011-07-31T18:22:09Z. |
| LogVersion |Verze formátu protokolu. Momentálně je jediná podporovaná hodnota 1,0. |

Následující seznam zobrazuje metadata úplnou ukázku použití v předchozích příkladech.

* LogType=write
* StartTime=2011-07-31T18:21:46Z
* EndTime=2011-07-31T18:22:09Z
* LogVersion = 1.0

### <a name="accessing-logging-data"></a>Přístup k datům protokolování
Všechna data `$logs` kontejner lze přistupovat pomocí rozhraní API služby Blob service, včetně rozhraní API pro .NET poskytuje Azure – spravovaná knihovna. Správce účtu úložiště můžete číst a odstraňovat protokoly, ale nejde vytvořit nebo aktualizovat je. Metadata v protokolu a název protokolu lze použít při dotazování na protokol. Je možné pro příslušné hodiny protokoly objeví mimo pořadí, ale metadata vždy určuje časový interval položky protokolu v protokolu. Proto můžete použít kombinaci názvy protokolů a metadata při vyhledávání pro konkrétní protokolu.

## <a name="about-storage-analytics-metrics"></a>O metrikách Storage Analytics
Analýza úložiště může ukládat data, včetně transakce agregované statistiky a dat o kapacitě požadavků na službu úložiště. Transakce jsou zaznamenány na úrovni operace rozhraní API i na úrovni služby úložiště, a je kapacita ohlášená na úrovni služby úložiště. Můžete například měřená data slouží k analýze využití služeb úložiště, Diagnostika potíží s požadavky na službu úložiště a zlepšit výkon aplikace, které používají službu.

Chcete-li použít funkci analýzy úložiště, musíte povolit ji jednotlivě pro každou službu, kterou chcete monitorovat. Můžete ho povolit [webu Azure Portal](https://portal.azure.com). Podrobnosti najdete v tématu [monitorování účtu úložiště na webu Azure Portal](storage-monitor-storage-account.md). Můžete také povolit Storage Analytics prostřednictvím kódu programu přes rozhraní REST API nebo knihovny klienta. Použití **získat vlastnosti služby** operace povolující analytika úložiště pro každou službu.

### <a name="transaction-metrics"></a>Metriku transakcí
Robustní sadu dat je zaznamenán v intervalech hodinových nebo minutu pro každou službu úložiště a požadovanou operaci rozhraní API, včetně příchozí a odchozí transakce, dostupnosti, chyby a zařazený do kategorie žádost o procenta. Můžete zobrazit úplný seznam všech podrobností transakcí v [tabulkovému schématu metrik Storage Analytics](https://msdn.microsoft.com/library/hh343264.aspx) tématu.

Transakce údaje jsou zaznamenány na dvou úrovních – úroveň služby a úroveň operace rozhraní API. Na úrovni služby statistiky sumarizace všech požadovaná operace rozhraní API se zapisují do tabulky entitu každou hodinu i v případě, že nebyly zadány žádné žádosti do služby. Na úrovni rozhraní API operace statistiky zapíšou jenom do entity Pokud za tuto hodinu se požadovaná operace.

Například pokud provedete **GetBlob** operace ve službě Blob service, metrik Storage Analytics protokolovat žádosti a zahrnout do agregovaná data pro službu Blob i na **GetBlob** operace. Ale pokud ne **GetBlob** operaci je požadováno během dané hodiny, entity nebude zapisovat `$MetricsTransactionsBlob` pro danou operaci.

Transakce jsou zaznamenány pro uživatelských požadavků a požadavků provedených Storage Analytics, samotného. Například se zaznamenávají požadavky podle analýzy úložiště pro zápis protokolů a tabulkové entity. Další informace o tom, jak se účtují tyto požadavky najdete v tématu [analytika úložiště a fakturace](https://msdn.microsoft.com/library/hh360997.aspx).

### <a name="capacity-metrics"></a>Kapacitní metriky
> [!NOTE]
> V současné době kapacitní metriky jsou k dispozici pouze pro službu Blob service.
>
>

Kapacity dat je denně zaznamenána pro službu Blob service účtu úložiště a dvě tabulky entity, které jsou zapsány. Jedna entita poskytuje statistiky pro uživatelská data a druhý poskytuje statistické údaje o `$logs` kontejner objektů blob Storage Analytics používá. `$MetricsCapacityBlob` Tabulka obsahuje následující statistiky:

* **Kapacita**: Velikost úložiště využitá službou Blob service účtu úložiště, v bajtech.
* **ContainerCount**: Počet kontejnerů objektů blob ve službě Blob service účtu úložiště.
* **ObjectCount**: Počet potvrzených a nepotvrzené bloku nebo stránky objekty BLOB ve službě Blob service účtu úložiště.

Další informace o metriky kapacity najdete v tématu [tabulkovému schématu metrik Storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema).

### <a name="how-metrics-are-stored"></a>Jak jsou uloženy metriky
Všechna data metriky pro každou ze služeb úložiště se ukládají do tří tabulek vyhrazené pro tuto službu: jednu tabulku pro informace o transakcích, jednu tabulku pro minuty transakce informace a další tabulku pro informace o kapacitě. Informace o transakcích transakce a minuty se skládá z dat požadavků a odpovědí a obsahuje informace o kapacitě úložiště dat o využití. Hodinové metriky, minutové metriky a kapacity pro službu Blob service účtu úložiště je možný v tabulkách, které jsou pojmenovány podle popisu v následující tabulce.

| Úroveň metriky | Názvy tabulek | Podporované verze |
| --- | --- | --- |
| Hodinové metriky, primární lokalita |$MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue |Verze starší než 2013-08-15 pouze. I když se tyto názvy jsou stále podporuje, doporučuje se, že přejdete na používání níže uvedené tabulky. |
| Hodinové metriky, primární lokalita |$MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue |Všechny verze, včetně 2013-08-15. |
| Minutové metriky, primární lokalita |$MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue |Všechny verze, včetně 2013-08-15. |
| Hodinové metriky, sekundární lokality |$MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue |Všechny verze, včetně 2013-08-15. Geograficky redundantní replikace jen pro čtení musí být povolena. |
| Minutové metriky, sekundární lokality |$MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue |Všechny verze, včetně 2013-08-15. Geograficky redundantní replikace jen pro čtení musí být povolena. |
| Kapacita (pouze služby Blob service) |$MetricsCapacityBlob |Všechny verze, včetně 2013-08-15. |

Tyto tabulky se automaticky vytvoří, když je pro účet úložiště povolená analytika úložiště. Jsou přístupné přes obor názvů tohoto účtu úložiště, například: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`

### <a name="accessing-metrics-data"></a>Přístup k datům metriky
Všechna data v tabulkách metriky lze přistupovat pomocí rozhraní API služby Table service, včetně rozhraní API pro .NET poskytuje Azure – spravovaná knihovna. Správce účtu úložiště můžete číst a odstraňovat tabulkové entity, ale nejde vytvořit nebo aktualizovat je.

## <a name="billing-for-storage-analytics"></a>Fakturace za Storage Analytics
Všechna data metriky se zapisují službami účtu úložiště. V důsledku toho se dají fakturovat každou operaci zápisu provádět analýzy úložiště. Kromě toho je velikost úložiště využitá službou můžete například měřená data také fakturovatelný.

Následující akce prováděné analytika úložiště se účtují:

* Požadavky na vytvoření objektů blob pro protokolování.
* Požadavky na vytvoření entity tabulky pro metriky.

Pokud jste nakonfigurovali zásady uchovávání dat, se neúčtuje transakce odstranění odstraní stará data protokolování a metrik Storage Analytics. Transakce odstranění z klienta se ale účtují. Další informace o zásadách uchovávání dat najdete v tématu [nastavení zásady uchovávání dat Storage Analytics](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Principy fakturovatelných požadavků
Každou žádost odeslanou účet služby storage je fakturovatelná nebo Nefakturovatelný. Analýza úložiště protokolů každý jednotlivý požadavek na službu, včetně stavovou zprávu, která určuje, jak se zpracovává žádost. Podobně analytika úložiště ukládá metriky pro službu a rozhraní API operace této služby, včetně procenta a počet určitých stavových zpráv. Společně tyto funkce vám pomohou analyzovat fakturovatelných požadavků, vylepšování na vaše aplikace a diagnostikovat problémy s žádostí o služby. Další informace o fakturaci najdete v tématu [Principy Azure Storage fakturace – šířka pásma, transakce a kapacita](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Při prohlížení dat Storage Analytics, můžete použít tabulky v [stavové zprávy a Storage Analytics protokolovanými operacemi](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) tématu můžete určit, jaké požadavky se účtují. Pak můžete porovnat vaše protokoly a data metriky pro stavové zprávy zobrazíte, pokud se vám účtovat určité žádosti. Tabulky v předchozím tématu můžete použít také k nalezení dostupnosti pro službu úložiště nebo jednotlivé operace rozhraní API.

## <a name="next-steps"></a>Další postup
* [Monitorování účtu úložiště na webu Azure Portal](storage-monitor-storage-account.md)
* [Protokolování Storage Analytics](https://msdn.microsoft.com/library/hh343262.aspx)
* [Metrikách Storage Analytics](https://msdn.microsoft.com/library/hh343258.aspx)
