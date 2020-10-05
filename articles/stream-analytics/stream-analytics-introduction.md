---
title: Přehled služby Azure Stream Analytics
description: Přečtěte si o službě Stream Analytics, která umožňuje v reálném čase analyzovat data streamovaná z platformy Internet věcí (IOT).
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 07/6/2020
ms.openlocfilehash: c6c878aa0e4a9d5caa22f7cd94c438f22a81d2ab
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "90881878"
---
# <a name="what-is-azure-stream-analytics"></a>Co je Azure Stream Analytics?

Azure Stream Analytics je analýza v reálném čase a komplexní modul pro zpracování událostí, který je určený k analýze a zpracování vysokého objemu rychlých streamování dat z více zdrojů současně. Modely a vztahy se dají identifikovat v informacích extrahovaných z řady vstupních zdrojů, včetně zařízení, senzorů, stránkáchů, informačních kanálů sociálních médií a aplikací. Pomocí těchto vzorů můžete aktivovat akce a iniciovat pracovní postupy, jako je vytváření výstrah, zakládání informací do nástroje pro generování sestav nebo ukládání transformovaných dat pro pozdější použití. Stream Analytics je také k dispozici v modulu Azure IoT Edge runtime a umožňuje zpracovávat data na zařízeních IoT. 

Následující scénáře jsou příklady, kdy můžete použít Azure Stream Analytics:

* Analýza streamů telemetrie v reálném čase ze zařízení IoT
* Webové protokoly/analýza navštívených webových stránek
* Geoprostorová analýza pro správu vozového parku a vozidel bez řidiče
* Vzdálené monitorování a prediktivní údržba prostředků s vysokou hodnotou
* Analýza dat z prodejen v reálném čase pro účely zjišťování anomálií a řízení zásob

## <a name="how-does-stream-analytics-work"></a>Jak funguje Stream Analytics?

Azure Stream Analytics úloha se skládá ze vstupu, dotazu a výstupu. Stream Analytics ingestuje data z Azure Event Hubs (včetně Azure Event Hubs ze Apache Kafka), Azure IoT Hub nebo Azure Blob Storage. Dotaz, který je založený na jazyku dotazů SQL, se dá použít k snadnému filtrování, řazení, agregaci a připojení streamování dat v časovém intervalu. Tento jazyk SQL můžete také roztáhnout pomocí uživatelem definovaných funkcí jazyka JavaScript a jazyka C# (UDF). Můžete snadno upravit možnosti řazení událostí a dobu trvání časových oken při předzpracování agregačních operací prostřednictvím jednoduchých jazykových konstrukcí a/nebo konfigurací.

Každá úloha obsahuje jeden nebo několik výstupů pro transformovaná data a můžete řídit, co se stane v reakci na informace, které jste analyzovali. Můžete například:

* Odesílání dat do služeb, jako jsou Azure Functions, Service Bus témat nebo front pro aktivaci komunikace nebo vlastních pracovních postupů.
* Odešlete data na řídicí panel Power BI pro řídicí panel v reálném čase.
* Ukládejte data do jiných služeb úložiště Azure (například Azure Data Lake, Azure synapse Analytics atd.) a Naučte se model strojového učení na základě historických dat nebo provádění služby Batch Analytics.

Následující obrázek ukazuje, jak jsou odesílána data Stream Analytics, analyzována a odeslána pro jiné akce, jako je například ukládání nebo prezentace:

![Stream Analytics úvodní kanál](./media/stream-analytics-introduction/stream-analytics-e2e-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Klíčové funkce a výhody

Služba Azure Stream Analytics byla navržena jako snadno použitelná, flexibilní, spolehlivá a škálovatelná na libovolnou velikost úlohy. Je k dispozici napříč několika oblastmi Azure. Následující obrázek znázorňuje klíčové funkce Azure Stream Analytics:

![Klíčové funkce Stream Analytics](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>Snadný začátek

Azure Stream Analytics se snadno spouští. Připojovat se k více zdrojům a jímkam jenom několik kliknutí a vytvoří kompletní kanál. Stream Analytics se můžou připojit k [azure Event Hubs](/azure/event-hubs/) a [Azure IoT Hub](/azure/iot-hub/) pro ingestování datových proudů a [Azure Blob Storage](/azure/storage/common/storage-introduction) a ingestovat historická data. Vstup úlohy může také zahrnovat statická nebo pomalá referenční data ze služby Azure Blob Storage nebo [SQL Database](stream-analytics-use-reference-data.md#azure-sql-database) , že se můžete připojit k datovým proudům a provádět operace vyhledávání.

Stream Analytics může směrovat výstup úlohy do mnoha systémů úložišť, jako je [Azure Blob Storage](/azure/storage/common/storage-introduction), [Azure SQL Database](/azure/sql-database/), [Azure Data Lake Store](/azure/data-lake-store/)a [Azure CosmosDB](/azure/cosmos-db/introduction). Dávkovou analýzu můžete spustit pro uložený výstup pomocí Azure HDInsight, nebo můžete výstup poslat do jiné služby, jako je Event Hubs pro využití nebo [Power BI](https://docs.microsoft.com/power-bi/) pro vizualizaci v reálném čase.

Úplný seznam výstupů Stream Analytics naleznete v tématu [Principy výstupů z Azure Stream Analytics](stream-analytics-define-outputs.md).

## <a name="programmer-productivity"></a>Produktivita programátorů

Azure Stream Analytics používá jednoduchý dotazovací jazyk založený na jazyce SQL, který byl rozšířen o výkonná dočasná omezení pro analýzu dat při pohybu. Pokud chcete definovat transformace úloh, použijete jednoduchý deklarativní [dotazovací jazyk Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference), který umožňuje vytváření složitých dočasných dotazů a analýz pomocí jednoduchých konstruktorů SQL. Vzhledem k tomu, že jazyk Stream Analytics dotazů je konzistentní s jazykem SQL, je znalost jazyka SQL dostačující pro zahájení vytváření úloh. Úlohy můžete vytvářet také pomocí vývojářských nástrojů, jako je Azure PowerShell, [Stream Analytics nástrojů sady Visual Studio](stream-analytics-tools-for-visual-studio-install.md), [rozšíření Stream Analytics Visual Studio Code](quick-create-visual-studio-code.md)nebo šablon Azure Resource Manager. Použití vývojářských nástrojů umožňuje vyvíjet transformační dotazy offline a pomocí [kanálu CI/CD](stream-analytics-tools-for-visual-studio-cicd.md) odesílat úlohy do Azure.

Dotazovací jazyk Stream Analytics nabízí nejrůznější funkce pro analýzu a zpracování streamních dat. Tento dotazovací jazyk podporuje jednoduché funkce pro manipulaci s daty, agregace a analýzy, [geoprostorové funkce](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-geospatial-functions), [porovnávání vzorů](https://docs.microsoft.com/stream-analytics-query/match-recognize-stream-analytics) a [detekci anomálií](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-machine-learning-anomaly-detection). Dotazy můžete upravovat na portálu a testovat je pomocí ukázkových dat extrahovaných z živého datového proudu.

Možnosti dotazovacího jazyka můžete rozšířit definováním a vyvoláním dalších funkcí. Můžete definovat volání funkcí v Azure Machine Learning pro využití výhod Azure Machine Learning řešení a integraci uživatelsky definovaných funkcí jazyka JavaScript nebo C# (UDF) nebo uživatelsky definovaných agregací k provádění složitých výpočtů jako součást Stream Analyticsho dotazu.

## <a name="fully-managed"></a>S plnou správou

Služba Azure Stream Analytics je plně spravovaná nabídka bez serveru (PaaS) v Azure. Nemusíte zřizovat žádný hardware, spravovat clustery pro spouštění vašich úloh nebo aktualizovat operační systém nebo software. Azure Stream Analytics plně spravuje vaši úlohu, takže se můžete soustředit na obchodní logiku, a ne na infrastrukturu.

## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>Spuštění v cloudu nebo na inteligentním hraničním zařízení

Azure Stream Analytics může běžet v cloudu, v případě rozsáhlých analýz, nebo běžet na IoT Edge pro analýzy extrémně nízké latence. Azure Stream Analytics používá stejné nástroje a dotazovací jazyk jak v cloudu, tak i v hraničních zařízeních, což vývojářům umožňuje vytvářet skutečně hybridní architektury pro zpracování datových proudů. 

## <a name="low-total-cost-of-ownership"></a>Nízké celkové náklady na vlastnictví

Stream Analytics je cloudová služba, takže je optimalizovaná z hlediska nákladů. Neúčtují se žádné náklady na předem – platíte jenom za [jednotky streamování, které spotřebováváte](stream-analytics-streaming-unit-consumption.md). Není vyžadován žádný závazek ani zřízení clusteru a můžete škálovat úlohu nahoru nebo dolů podle potřeb vaší firmy.

## <a name="mission-critical-ready"></a>Připraveno pro klíčové úkoly

Azure Stream Analytics je k dispozici napříč několika oblastmi po celém světě a je navržena tak, aby spouštěla kritické úlohy, a to díky podpoře spolehlivosti, zabezpečení a dodržování předpisů.

### <a name="reliability"></a>Spolehlivost

Azure Stream Analytics garantuje právě jednou zpracování událostí a nejméně jedno doručení událostí, takže události nebudou nikdy ztraceny. Zpracování právě jednou je zaručeno vybraným výstupem, jak je popsáno v tématu [záruky doručení událostí](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).

Azure Stream Analytics má integrované možnosti obnovení pro případ, že se doručení události nepovede. Stream Analytics také nabízí integrované kontrolní body, které udržují stav úlohy a umožňují opakované výsledky.

Jako spravovaná služba Stream Analytics garantuje zpracování událostí s dostupností 99,9% v minutové úrovni členitosti. Další informace najdete na stránce věnované [smlouvě SLA Stream Analytics](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) . 

### <a name="security"></a>Zabezpečení

V souvislosti s zabezpečením Azure Stream Analytics šifruje veškerou příchozí a odchozí komunikaci a podporuje TLS 1,2. Vestavěné kontrolní body jsou také šifrované. Stream Analytics neukládá příchozí data, protože veškeré zpracování je provedeno v paměti.

### <a name="compliance"></a>Dodržování předpisů

Azure Stream Analytics sleduje více certifikace dodržování předpisů, jak je popsáno v tématu [Přehled dodržování předpisů Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="performance"></a>Výkon

Stream Analytics může zpracovávat miliony událostí každou sekundu a může doručovat výsledky s extrémně nízkými latencemi. Umožňuje horizontální navýšení a škálování na více instancí a zpracování složitých aplikací pro zpracování událostí v reálném čase. Stream Analytics podporuje vyšší výkon pomocí dělení, což umožňuje paralelní dotazování a spouštění složitých dotazů na více uzlech streamování. Azure Stream Analytics je postavená na [Trill](https://github.com/Microsoft/Trill), vysoce výkonný modul pro streamování streamování v paměti vyvinutý ve spolupráci s Microsoft Research.

## <a name="next-steps"></a>Další kroky

Teď máte přehled o službě Azure Stream Analytics. V dalším kroku se můžete do tématu ponořit hlouběji a vytvořit si svoji první úlohu Stream Analytics:

* [Vytvořit úlohu Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)
* [Vytvořte Stream Analyticsovou úlohu pomocí Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Pomocí sady Visual Studio vytvořte Stream Analyticsovou úlohu](stream-analytics-quick-create-vs.md).
* [Vytvořte Stream Analyticsovou úlohu pomocí Visual Studio Code](quick-create-visual-studio-code.md).
