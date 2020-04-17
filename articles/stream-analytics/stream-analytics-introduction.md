---
title: Přehled služby Azure Stream Analytics
description: Přečtěte si o službě Stream Analytics, která umožňuje v reálném čase analyzovat data streamovaná z platformy Internet věcí (IOT).
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: 673c6e2ed0aedcc186baf8c7e1884dd537f9567d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459844"
---
# <a name="what-is-azure-stream-analytics"></a>Co je Azure Stream Analytics?

Azure Stream Analytics je analytický modul v reálném čase a složitý modul pro zpracování událostí, který je navržený pro analýzu a zpracování velkých objemů rychlých streamovaných dat z více zdrojů současně. Vzory a vztahy lze identifikovat v informacích získaných z řady vstupních zdrojů, včetně zařízení, senzorů, clickstreamů, kanálů sociálních médií a aplikací. Tyto vzory lze použít k aktivaci akcí a zahájení pracovních postupů, jako je vytváření výstrah, podávání informací do nástroje pro vytváření sestav nebo ukládání transformovaných dat pro pozdější použití. Stream Analytics je taky dostupná v runtime Azure IoT Edge a podporuje stejný přesný jazyk nebo syntaxi jako cloud. 

Následující scénáře jsou příklady, kdy můžete použít Azure Stream Analytics:

* Analýza telemetrických datových proudů v reálném čase ze zařízení IoT
* Webové protokoly/analýza navštívených webových stránek
* Geoprostorová analýza pro správu vozového parku a vozidel bez řidiče
* Vzdálené monitorování a prediktivní údržba vysoce hodnotných aktiv
* Analýza dat z prodejen v reálném čase pro účely zjišťování anomálií a řízení zásob

## <a name="how-does-stream-analytics-work"></a>Jak funguje Stream Analytics?

Úloha Azure Stream Analytics se skládá ze vstupu, dotazu a výstupu. Stream Analytics ingestuje data z Azure Event Hubs, Azure IoT Hub nebo Azure Blob Storage. Dotaz, který je založen na dotazovacím jazyce SQL, lze snadno filtrovat, řadit, agregovat a spojit streamovaná data po určitou dobu. Tento jazyk SQL můžete také rozšířit pomocí uživatelem definovaných funkcí JavaScriptu a Jazyka C# (UDF). Můžete snadno upravit možnosti řazení událostí a dobu trvání časových oken při předběžném agregaci pomocí jednoduchých jazykových konstrukcí nebo konfigurací.

Každá úloha má výstup pro transformovaná data a můžete řídit, co se stane v reakci na informace, které jste analyzovali. Můžete například provést následující věci:

* Odesílejte data do služeb, jako jsou Azure Functions, Service Bus Topics nebo Queues, abyste aktivovali komunikaci nebo vlastní pracovní postupy pod proudem.
* Odesílejte data na řídicí panel Power BI pro řídicí panel v reálném čase.
* Uklápěte data v jiných službách úložiště Azure, abyste trénování modelu strojového učení na základě historických dat nebo provádět dávkové analýzy.

Následující obrázek znázorňuje, jak jsou data odesílána do Stream Analytics, analyzována a odesílána pro další akce, jako je úložiště nebo prezentace:

![Úvodní kanál Stream Analytics](./media/stream-analytics-introduction/stream-analytics-e2e-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Klíčové funkce a výhody

Služba Azure Stream Analytics byla navržena jako snadno použitelná, flexibilní, spolehlivá a škálovatelná na libovolnou velikost úlohy. Je k dispozici ve více oblastech Azure. Následující obrázek ilustruje klíčové možnosti Azure Stream Analytics:

![Klíčové funkce Stream Analytics](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>Snadný začátek

Azure Stream Analytics se snadno spustí. Připojení k více zdrojům a propadům trvá pouze několik kliknutí a vytvoření kanálu od konce. Stream Analytics se můžou připojit k [Azure Event Hubs](/azure/event-hubs/) a Azure [IoT Hub](/azure/iot-hub/) pro streamování dat a také k [úložišti objektů blob Azure](/azure/storage/common/storage-introduction) k ingestování historických dat. Vstup úlohy může také obsahovat statická nebo pomalu se měnící referenční data z úložiště objektů blob Azure nebo [databáze SQL,](stream-analytics-use-reference-data.md#azure-sql-database) ke kterým se můžete připojit k streamování dat a provádět vyhledávací operace.

Stream Analytics může směrovat výstup úlohy do mnoha úložných systémů, jako je [azure blob storage](/azure/storage/common/storage-introduction), [Azure SQL Database](/azure/sql-database/), Azure Data Lake [Store](/azure/data-lake-store/)a [Azure CosmosDB](/azure/cosmos-db/introduction). Dávkové analýzy můžete spustit na uloženém výstupu pomocí Azure HDInsight, nebo můžete výstup odeslat do jiné služby, jako jsou Centra událostí ke spotřebě nebo [Power BI](https://docs.microsoft.com/power-bi/) pro vizualizaci v reálném čase.

Celý seznam výstupů Stream Analytics najdete [v tématu Principy výstupů z Azure Stream Analytics](stream-analytics-define-outputs.md).

## <a name="programmer-productivity"></a>Produktivita programátorů

Azure Stream Analytics používá jednoduchý dotazovací jazyk založený na SQL, který byl rozšířen o výkonná časová omezení k analýze dat v pohybu. Pokud chcete definovat transformace úloh, použijete jednoduchý deklarativní [dotazovací jazyk Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference), který umožňuje vytváření složitých dočasných dotazů a analýz pomocí jednoduchých konstruktorů SQL. Vzhledem k tomu, že dotazovací jazyk Stream Analytics je konzistentní s jazykem SQL, znalost SQL je dostatečná k zahájení vytváření úloh. Můžete také vytvářet úlohy pomocí vývojářských nástrojů, jako je Azure PowerShell, [Stream Analytics Visual Studio nástroje](stream-analytics-tools-for-visual-studio-install.md), Stream [Analytics Visual Studio code extension](quick-create-vs-code.md), nebo Azure Resource Manager šablony. Použití vývojářských nástrojů umožňuje vyvíjet transformační dotazy offline a pomocí [kanálu CI/CD](stream-analytics-tools-for-visual-studio-cicd.md) odesílat úlohy do Azure.

Dotazovací jazyk Stream Analytics nabízí širokou škálu funkcí pro analýzu a zpracování streamovaných dat. Tento dotazovací jazyk podporuje jednoduchou manipulaci s daty, agregační funkce a složité geoprostorové funkce. Dotazy můžete upravovat na portálu a testovat je pomocí ukázkových dat extrahovaných z živého datového proudu.

Možnosti dotazovacího jazyka můžete rozšířit definováním a vyvoláním dalších funkcí. Můžete definovat volání funkcí v Azure Machine Learning využít výhod řešení Azure Machine Learning a integrovat JavaScript nebo C# uživatelem definované funkce (UOF) nebo uživatelem definované agregáty k provádění složitých výpočtů jako součást dotazu Stream Analytics.

## <a name="fully-managed"></a>S plnou správou

Služba Azure Stream Analytics je plně spravovaná nabídka bez serveru (PaaS) v Azure. Není třeba zřídit žádný hardware nebo spravovat clustery pro spuštění úloh. Azure Stream Analytics plně spravuje vaši úlohu nastavením složitých výpočetních clusterů v cloudu a péčí o ladění výkonu potřebné ke spuštění úlohy. Integrace s Azure Event Hubs a Azure IoT Hub umožňuje vaší úloze ingestovat miliony událostí za sekundu pocházejících z mnoha zdrojů, včetně připojených zařízení, točů kliknutí a souborů protokolu. Pomocí funkce dělení event hubů můžete rozdělit výpočty do logických kroků, z nichž každý má možnost dále rozdělit oddíly pro zvýšení škálovatelnosti.

## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>Běh v cloudu nebo na inteligentní hraně

Azure Stream Analytics můžete běžet v cloudu, pro rozsáhlé analýzy nebo spustit na IoT Edge pro analýzy s velmi nízkou latencí. Azure Stream Analytics používá stejný dotazovací jazyk v cloudu i na hraničních zařízeních, což vývojářům umožňuje vytvářet skutečně hybridní architektury pro zpracování datových proudů. 

## <a name="low-total-cost-of-ownership"></a>Nízké celkové náklady na vlastnictví

Stream Analytics je cloudová služba, takže je optimalizovaná z hlediska nákladů. Nejedná se o žádné počáteční náklady – platíte pouze za [jednotky streamování, které spotřebováváte](stream-analytics-streaming-unit-consumption.md), a množství zpracovávaných dat. Není vyžadován žádný závazek nebo zřizování clusteru a úlohu můžete škálovat nahoru nebo dolů na základě vašich obchodních potřeb.

## <a name="mission-critical-ready"></a>Kritická připravenost

Azure Stream Analytics je dostupná ve více oblastech po celém světě a je navržená tak, aby spouštěla klíčové úlohy podporou požadavků na spolehlivost, zabezpečení a dodržování předpisů.

### <a name="reliability"></a>Spolehlivost

Azure Stream Analytics zaručuje přesně jednou zpracování událostí a alespoň jednou doručení událostí, takže události se nikdy neztratí. Přesně jednou je zpracování garantováno s vybraným výstupem, jak je popsáno v [zárukách doručení událostí](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).

Azure Stream Analytics má integrované možnosti obnovení pro případ, že se doručení události nezdaří. Stream Analytics také poskytuje integrované kontrolní body pro udržení stavu úlohy a poskytuje opakovatelné výsledky.

Jako spravovaná služba stream analytics zaručuje zpracování událostí s 99,9% dostupností na minutové úrovni rozlišovací schopnosti. Další informace najdete na stránce [SLA služby Stream Analytics.](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) 

### <a name="security"></a>Zabezpečení

Pokud jde o zabezpečení, Azure Stream Analytics šifruje veškerou příchozí a odchozí komunikaci a podporuje TLS 1.2. Vestavěné kontrolní body jsou také šifrovány. Stream Analytics neukládá příchozí data, protože veškeré zpracování se provádí v paměti.

### <a name="compliance"></a>Dodržování předpisů

Azure Stream Analytics sleduje několik certifikací dodržování předpisů, jak je popsáno v [přehledu dodržování předpisů Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="performance"></a>Výkon

Stream Analytics dokáže každou sekundu zpracovat miliony událostí a může přinést výsledky s velmi nízkou latencí. Umožňuje vertikálně navýšit kapacitu a horizontální navýšení kapacity pro zpracování velkých aplikací pro zpracování událostí v reálném čase a složitých událostí. Stream Analytics podporuje vyšší výkon rozdělením, což umožňuje složité dotazy, které mají být paralelizovány a spouštěny na více uzlů streamování. Azure Stream Analytics je založený na [Trillu](https://github.com/Microsoft/Trill), vysoce výkonném analytickém modulu pro streamování v paměti vyvinutém ve spolupráci se společností Microsoft Research.

## <a name="next-steps"></a>Další kroky

Teď máte přehled o službě Azure Stream Analytics. V dalším kroku se můžete do tématu ponořit hlouběji a vytvořit si svoji první úlohu Stream Analytics:

* [Vytvořit úlohu Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)
* [Vytvořte úlohu Stream Analytics pomocí Azure PowerShellu](stream-analytics-quick-create-powershell.md).
* [Vytvořte úlohu Stream Analytics pomocí sady Visual Studio](stream-analytics-quick-create-vs.md).
* [Vytvořte úlohu Stream Analytics pomocí kódu sady Visual Studio](quick-create-vs-code.md).
