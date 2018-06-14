---
title: Azure Storage Analytics můžete použít ke shromažďování metrik a protokolování dat | Microsoft Docs
description: Analytika úložiště umožňuje sledovat data metriky pro všechny služby, úložiště a shromažďovat protokoly pro úložiště Blob, fronty a tabulky.
services: storage
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: 7894993b-ca42-4125-8f17-8f6dfe3dca76
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: rogarana
ms.openlocfilehash: edda01cbfe1b53d934f9f4a7bb01c645fa680873
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
ms.locfileid: "30243430"
---
# <a name="storage-analytics"></a>Storage Analytics

Analýza Azure Storage provádí protokolování a poskytuje data metriky pro účet úložiště. Tato data můžete použít k trasování požadavků, analýze trendů použití a diagnostice problémů s účtem úložiště.

Pokud chcete používat analytika úložiště, musíte povolit ji jednotlivě pro každou službu, kterou chcete monitorovat. Můžete ji z povolit [portálu Azure](https://portal.azure.com). Podrobnosti najdete v tématu [monitorování účtu úložiště na portálu Azure](storage-monitor-storage-account.md). Můžete také povolit Storage Analytics programově pomocí rozhraní REST API nebo knihovny klienta. Použití [získat vlastnosti objektu Blob služby](https://msdn.microsoft.com/library/hh452239.aspx), [získat vlastnosti fronty služby](https://msdn.microsoft.com/library/hh452243.aspx), [získat vlastnosti služby Table](https://msdn.microsoft.com/library/hh452238.aspx), a [získat File Service Properties](https://msdn.microsoft.com/library/mt427369.aspx) operace povolující analytika úložiště pro každou službu.

Agregovaná data se ukládají v dobře známé objektu blob (pro protokolování) a dobře známé tabulky (pro metriky), které můžete získat přístup pomocí služby objektů Blob a služby Table rozhraní API.

Analytika úložiště může mít 20 TB na množství uložených dat, která je nezávislá celkový limit pro váš účet úložiště. Další informace o fakturaci a zásad uchovávání dat najdete v tématu [Storage Analytics a fakturace](https://msdn.microsoft.com/library/hh360997.aspx). Další informace o omezeních účtu úložiště najdete v tématu [a cíle výkonnosti služby Azure Storage Scalability](storage-scalability-targets.md).

Podrobné informace týkající se použití analytika úložiště a dalších nástrojů pro identifikovat, diagnostikovat a řešit problémy související s Azure Storage najdete v tématu [monitorování, Diagnostika a řešení Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="about-storage-analytics-logging"></a>O protokolování Storage Analytics
Analytika úložiště protokoly podrobné informace o úspěšných a nezdařených požadavků a služba úložiště. Tyto informace slouží k monitorování jednotlivých požadavků a diagnostikovat problémy s služby úložiště. Na základě typu best effort protokolované požadavky.

Položky protokolu se vytvoří pouze v případě, že je aktivita služby úložiště. Například pokud je účet úložiště v jeho služby objektů Blob, ale není v jeho tabulku nebo frontu služby, bude vytvořena pouze protokoly, která se týkají služby objektů Blob.

Protokolování Analytics úložiště není k dispozici pro Azure Files.

### <a name="logging-authenticated-requests"></a>Protokolování ověření požadavků
Jsou zaznamenány následující typy ověřených požadavků:

* Úspěšné požadavky.
* Neúspěšné požadavky, včetně vypršení časového limitu, omezení šířky pásma, sítě, autorizace a dalších chyb.
* Požadavky na použití sdíleného přístupového podpisu (SAS), včetně úspěšné a neúspěšné požadavky.
* Požadavky na analytická data.

Požadavky na úložiště Analytics samostatně, jako je například protokol vytvoření nebo odstranění, se neprotokolují. Úplný seznam dat protokolu jsou uvedené v [stavové zprávy a Storage Analytics protokolované](https://msdn.microsoft.com/library/hh343260.aspx) a [úložiště analýzy protokolů formátu](https://msdn.microsoft.com/library/hh343259.aspx) témata.

### <a name="logging-anonymous-requests"></a>Protokolování anonymních požadavků
Jsou zaznamenány následující typy anonymních požadavků:

* Úspěšné požadavky.
* Chyby serveru.
* Chyby časového limitu pro klienta a serveru.
* Neúspěšné požadavky GET s kódem chyby 304 (upraveno).

Všechny ostatní selhání anonymních požadavků se neprotokolují. Úplný seznam dat protokolu jsou uvedené v [stavové zprávy a Storage Analytics protokolované](https://msdn.microsoft.com/library/hh343260.aspx) a [úložiště analýzy protokolů formátu](https://msdn.microsoft.com/library/hh343259.aspx) témata.

### <a name="how-logs-are-stored"></a>Ukládání protokolů
Všechny protokoly se ukládají do objektů BLOB bloku v kontejneru nazvaném $logs, který se automaticky vytvoří, když je pro účet úložiště povolená analytika úložiště. Kontejner $logs se nachází v oboru názvů objektů blob účtu úložiště, například: `http://<accountname>.blob.core.windows.net/$logs`. Tento kontejner nelze odstranit, jakmile Storage Analytics je povoleno, když jeho obsah může být odstraněny.

> [!NOTE]
> Kontejner $logs se nezobrazí, pokud se provádí kontejner výpis operace, jako [ListContainers](https://msdn.microsoft.com/library/azure/dd179352.aspx) metoda. Musí se přístup přímo. Například můžete použít [ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx) metodu pro přístup k objektům BLOB ve `$logs` kontejneru.
> Jako žádosti se protokolují, odešlete Storage Analytics mezilehlých výsledků jako bloky. Analytika úložiště bude pravidelně potvrdit tyto bloky a zpřístupnit jako objekt blob.
> 
> 

Pro protokoly, které jsou vytvořené v tutéž hodinu může existují duplicitní záznamy. Můžete určit, zda záznam je duplicitní kontrolou **Id_žádosti** a **operace** číslo.

### <a name="log-naming-conventions"></a>Přihlaste se zásady vytváření názvů
Každý protokol, bude napsán v následujícím formátu.

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log

Následující tabulka popisuje jednotlivé atributy v názvu protokolového.

| Atribut | Popis |
| --- | --- |
| <service-name> |Název služby úložiště. Příklad: Objekt blob, tabulka nebo fronty. |
| RRRR |Čtyřmístný rok pro protokol. Příklad: 2011. |
| MM |Letopočty měsíce pro protokol. Příklad: 07. |
| DD |Letopočty měsíce pro protokol. Příklad: 07. |
| hh |Hodiny letopočty určující počáteční hodina protokolů, ve 24hodinovém formátu UTC. Příklad: 18. |
| mm |Dvoumístné číslo určující počáteční minut protokolů. Tato hodnota není podporována v aktuální verzi analytika úložiště a jeho hodnota bude vždy 00. |
| <counter> |Počítaný od nuly čítač se šesti číslic určující počet objektů BLOB protokolu vygenerovaný pro službu úložiště za hodinu časové období. Tento čítač se spustí v 000000. Příklad: 000001. |

Následuje název protokolu ucelenou ukázku, která sloučí v předchozích příkladech.

    blob/2011/07/31/1800/000001.log

Zde je ukázka identifikátor URI, který lze použít pro přístup k předchozím protokolu.

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log

Při zaznamenání požadavku úložiště, výsledný název protokolu korelaci hodinu po dokončení požadované operace. Například, pokud žádost o getblob – byla dokončena v 6:30 na 31/7/2011 by byla zapsána do protokolu s předponou následující: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Metadata protokolu
Všechny objekty BLOB protokolu se ukládají s metadata, která umožňuje určit protokolování data, která obsahuje objekt blob. Následující tabulka popisuje každý atribut metadat.

| Atribut | Popis |
| --- | --- |
| LogType |Popisuje, zda v protokolu obsahuje informace týkající se čtení, zápisu nebo odstranění operace. Tato hodnota může obsahovat jeden typ nebo kombinaci všechny tři, oddělených čárkami. Příklad 1: zápis; Příklad 2: Číst, zapisovat; Příklad 3: Číst, zapisovat, odstranit. |
| StartTime |Denní čas, může záznam v protokolu ve formátu RRRR-MM-ddTHH. Příklad: 2011-07-31T18:21:46Z. |
| EndTime |Čas posledního záznamu v protokolu ve formátu RRRR-MM-ddTHH. Příklad: 2011-07-31T18:22:09Z. |
| LogVersion |Verze formátu protokolu. Aktuálně jedinou podporovanou hodnotou je 1.0. |

V následujícím seznamu zobrazí ucelenou ukázku metadat pomocí v předchozích příkladech.

* LogType=write
* StartTime=2011-07-31T18:21:46Z
* EndTime=2011-07-31T18:22:09Z
* LogVersion = 1.0

### <a name="accessing-logging-data"></a>Přístup k datům protokolování
Všechna data `$logs` kontejneru je přístupná pomocí rozhraní API služby objektů Blob, včetně .NET API poskytované Azure spravovanou knihovnu. Správce účtu úložiště můžete číst a odstranit protokoly, ale nelze vytvořit nebo aktualizovat je. Metadata v protokolu a název protokolu lze použít při dotazování pro protokol. Je možné pro danou hodinu protokoly objeví mimo pořadí, ale metadata vždy určuje časový interval položky protokolu v protokolu. Proto můžete použít kombinaci protokolu názvů a metadata při vyhledávání pro určitý protokol.

## <a name="about-storage-analytics-metrics"></a>O Storage Analytics metriky
Analytika úložiště můžete ukládat metriky, které zahrnují agregované transakce statistiky a kapacity data o požadavcích služby úložiště. Transakce jsou hlášeny na úrovni operace rozhraní API, a také na úrovni služby úložiště a hlásí kapacity na úrovni služby úložiště. Metriky dat slouží k analýze využití služby úložiště, diagnostikovat problémy s požadavky na služby úložiště a zlepšit výkon aplikací, které používají službu.

Pokud chcete používat analytika úložiště, musíte povolit ji jednotlivě pro každou službu, kterou chcete monitorovat. Můžete ji z povolit [portálu Azure](https://portal.azure.com). Podrobnosti najdete v tématu [monitorování účtu úložiště na portálu Azure](storage-monitor-storage-account.md). Můžete také povolit Storage Analytics programově pomocí rozhraní REST API nebo knihovny klienta. Použití **získat vlastnosti služby** operace povolující analytika úložiště pro každou službu.

### <a name="transaction-metrics"></a>Metriky transakce
Robustní sadu dat se zaznamenává v intervalech hodinových nebo minutu pro každou službu úložiště a požadovanou operaci rozhraní API, včetně vstupní/výstupní, dostupnosti, chyby a zařazené do kategorie procenta požadavku. Zobrazí seznam všech podrobností transakcí v [schématu tabulky metriky Analytics úložiště](https://msdn.microsoft.com/library/hh343264.aspx) tématu.

Data transakcí se zaznamenává na dvou úrovních – úrovně služby a operace úroveň rozhraní API. Na úrovni služby statistiky shrnutí všechny požadované operace rozhraní API se zapisují na entitu tabulky každou hodinu i v případě, že nebyly provedeny žádné žádosti o službu. Na úrovni operace rozhraní API statistiky zapíšou jenom na entitu Pokud byla požadovaná operace v rámci této hodinu.

Například, pokud provádíte **getblob –** operace služby Blob Storage Analytics Metrics bude protokolu žádost a její zahrnutí do agregovaná data pro službu objektů Blob a taky **getblob –** operaci. Ale pokud žádné **getblob –** za hodinu je požadovaná operace, nebude možné entity zapsat do `$MetricsTransactionsBlob` pro tuto operaci.

Metriky transakce se zaznamenávají pro uživatelské požadavky a požadavky na úložiště Analytics sám sebe. Například se zaznamenávají požadavků podle Storage Analytics k zápisu protokoly a entity tabulky. Další informace o tom, jak se účtují tyto požadavky najdete v tématu [Storage Analytics a fakturace](https://msdn.microsoft.com/library/hh360997.aspx).

### <a name="capacity-metrics"></a>Metriky kapacity
> [!NOTE]
> V současné době metriky kapacity jsou k dispozici pouze pro služby objektů Blob. Metriky kapacity pro službu tabulky a fronty služby bude v budoucích verzích analytika úložiště k dispozici.
> 
> 

Kapacity dat se zaznamenává každý den pro účet úložiště služby objektů Blob a se zapisují dvě entity tabulky. Jedna entita poskytuje statistiky pro data uživatelů a dalších poskytuje statistiky o `$logs` kontejner objektů blob Storage Analytics používá. `$MetricsCapacityBlob` Tabulka obsahuje následující statistiky:

* **Kapacita**: velikost úložiště, které používá služba účtu úložiště objektů Blob, v bajtech.
* **ContainerCount**: počet kontejnerů objektů blob v účtu úložiště služby objektů Blob.
* **ObjectCount**: počet potvrzení a nepotvrzené bloku nebo stránky objektů BLOB v účtu úložiště objektů Blob služby.

Další informace o metriky kapacity najdete v tématu [schématu tabulky metriky Analytics úložiště](https://msdn.microsoft.com/library/hh343264.aspx).

### <a name="how-metrics-are-stored"></a>Ukládání metriky
Veškerá data metriky pro jednotlivé služby storage je uložena v tři tabulky, které jsou vyhrazené pro tuto službu: jednu tabulku pro informací o transakcích, jednu tabulku pro informace o minutu transakce a jinou tabulkou pro informací o kapacitě. Transakce a minutu informací o transakcích se skládá z požadavku a odpovědi dat a informací o kapacitě se skládá z dat využití úložiště. Metriky hodinu, minutu metriky a kapacity pro účet úložiště služby objektů Blob je přístupná v tabulkách, které byly pojmenovány, jak je popsáno v následující tabulce.

| Metriky úrovně | Názvy tabulek | Podporované verze |
| --- | --- | --- |
| Hodinové metriky, primární umístění |$MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue |Verze starší než 2013-08-15 jenom. Když tyto názvy jsou stále podporovány, je doporučeno, můžete přejít k používání níže uvedené tabulky. |
| Hodinové metriky, primární umístění |$MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue |Všechny verze, včetně 2013-08-15. |
| Minutu metriky, primární umístění |$MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue |Všechny verze, včetně 2013-08-15. |
| Hodinové metriky, sekundárních umístění |$MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue |Všechny verze, včetně 2013-08-15. Musí být povolen přístup pro čtení geo redundantní replikaci. |
| Minutu metriky, sekundárních umístění |$MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue |Všechny verze, včetně 2013-08-15. Musí být povolen přístup pro čtení geo redundantní replikaci. |
| Kapacita (pouze služby objektů Blob) |$MetricsCapacityBlob |Všechny verze, včetně 2013-08-15. |

Tyto tabulky se automaticky vytvoří, když je pro účet úložiště povolená analytika úložiště. Jsou přístupná prostřednictvím obor názvů účet úložiště, například: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`

### <a name="accessing-metrics-data"></a>Přístup k datům metriky
Všechna data v tabulkách metriky můžete přistupovat pomocí rozhraní API služby Table, včetně .NET API poskytované Azure spravovanou knihovnu. Správce účtu úložiště můžete číst a odstranit tabulku entity, ale nelze vytvořit nebo aktualizovat je.

## <a name="billing-for-storage-analytics"></a>Fakturace analytika úložiště
Všechny metriky data jsou zapsána pomocí služby účet úložiště. V důsledku toho je fakturovatelný každou operaci zápisu provádí analytika úložiště. Velikost úložiště používané metriky dat je navíc také fakturovatelného času.

Fakturovatelný jsou tyto akce prováděné Storage Analytics:

* Požadavky na vytvoření objektů blob pro protokolování. 
* Požadavky na vytvoření entity tabulky pro metriky.

Pokud jste nakonfigurovali zásadu uchovávání dat, se vám neúčtují poplatky pro odstranění transakce když Storage Analytics odstraní stará data protokolování a metriky. Odstranění transakce z klienta jsou však fakturovatelného času. Další informace o zásady uchovávání informací najdete v tématu [nastavení zásad uchovávání dat úložiště Analytics](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Pochopení požadavků fakturovatelného času
Každý požadavek do služby účtu úložiště je fakturovatelný nebo Nefakturovatelný. Úložiště analýzy protokolů každé jednotlivé požadavek služby, včetně stavovou zprávu, která určuje, jak byl požadavek zpracován. Podobně analytika úložiště ukládá metrik pro služby a operace rozhraní API této služby, včetně procenta a počet určitých stavových zpráv. Společně tyto funkce vám může pomoct analyzovat fakturovatelný žádostí, zlepšení na vaší aplikace a diagnostikovat problémy s požadavky k vašim službám. Další informace o fakturaci, viz [Principy Azure úložiště fakturace - šířku pásma, transakce a kapacity](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Při prohlížení analytika úložiště dat, můžete použít tabulky v [stavové zprávy a Storage Analytics protokolované](https://msdn.microsoft.com/library/azure/hh343260.aspx) tématu, které chcete určit, jaké požadavky jsou fakturovatelné. Pak můžete porovnat protokoly a metriky data stavových zpráv a zjistí, pokud byla požadována pro konkrétní žádost. Tabulky v předchozím tématu můžete použít také k nalezení dostupnosti pro službu úložiště nebo jednotlivé operace rozhraní API.

## <a name="next-steps"></a>Další postup
### <a name="setting-up-storage-analytics"></a>Nastavení služby Storage Analytics
* [Monitorování účtu úložiště na portálu Azure](storage-monitor-storage-account.md)
* [Povolení a konfigurace úložiště analýzy](https://msdn.microsoft.com/library/hh360996.aspx)

### <a name="storage-analytics-logging"></a>Analýza protokolování úložiště
* [O protokolování Analytics úložiště](https://msdn.microsoft.com/library/hh343262.aspx)
* [Formát úložiště analýzy protokolů](https://msdn.microsoft.com/library/hh343259.aspx)
* [Analytika úložiště protokolovat stavové zprávy a operace](https://msdn.microsoft.com/library/hh343260.aspx)

### <a name="storage-analytics-metrics"></a>Metriky Analytics úložiště
* [O metriky Analytics úložiště](https://msdn.microsoft.com/library/hh343258.aspx)
* [Schéma tabulky metriky Analytics úložiště](https://msdn.microsoft.com/library/hh343264.aspx)
* [Analytika úložiště protokolovat stavové zprávy a operace](https://msdn.microsoft.com/library/hh343260.aspx)  

