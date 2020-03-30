---
title: Úrovně kompatibility Azure Stream Analytics
description: Přečtěte si, jak nastavit úroveň kompatibility pro úlohu Azure Stream Analytics a hlavní změny v nejnovější úrovni kompatibility
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 8f22b1ff97826dc318794aca58973b1276e74209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087861"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Úroveň kompatibility pro úlohy Azure Stream Analytics

Tento článek popisuje možnost úrovně kompatibility ve službě Azure Stream Analytics. Stream Analytics je spravovaná služba s pravidelnými aktualizacemi funkcí a vylepšením výkonu. Většina aktualizací runtimes služby je automaticky k dispozici koncovým uživatelům. 

Některé nové funkce ve službě však může zavést zásadní změny, jako je například změna chování existující úlohy nebo změna způsobu, jakým jsou data spotřebovávána v spuštěných úlohách. Stávající úlohy Služby Stream Analytics můžete ponechat spuštěné bez větších změn tím, že necháte snížit úroveň kompatibility. Až budete připraveni na nejnovější chování za běhu, můžete se přihlásit zvýšením úrovně kompatibility. 

## <a name="choose-a-compatibility-level"></a>Zvolte úroveň kompatibility

Úroveň kompatibility řídí chování za běhu úlohy analýzy datového proudu. 

Azure Stream Analytics momentálně podporuje tři úrovně kompatibility:

* 1.0 – Původní úroveň kompatibility, která byla zavedena během obecné dostupnosti Azure Stream Analytics před několika lety.
* 1.1 - Předchozí chování
* 1.2 - Nejnovější chování s nejnovějšími vylepšeními

Když vytvoříte novou úlohu Stream Analytics, je vhodné ji vytvořit pomocí nejnovější úrovně kompatibility. Začněte pracovat s návrhem a budete se spoléhat na nejnovější chování, abyste se později vyhnuli další mši a složitosti.

## <a name="set-the-compatibility-level"></a>Nastavení úrovně kompatibility

Úroveň kompatibility pro úlohu Stream Analytics můžete nastavit na webu Azure Portal nebo pomocí [volání rozhraní REST API pro vytvoření úlohy](/rest/api/streamanalytics/stream-analytics-job).

Aktualizace úrovně kompatibility úlohy na webu Azure Portal:

1. Pomocí [portálu Azure](https://portal.azure.com) vyhledejte úlohu Stream Analytics.
2. Před aktualizací úrovně kompatibility **zastavte** úlohu. Úroveň kompatibility nelze aktualizovat, pokud je úloha ve spuštěném stavu.
3. V části **Konfigurovat** nadpis vyberte **úroveň kompatibility**.
4. Zvolte požadovanou hodnotu úrovně kompatibility.
5. V dolní části stránky vyberte **Uložit.**

![Úroveň kompatibility Stream Analytics na webu Azure Portal](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

Při aktualizaci úrovně kompatibility kompilátor T ověří úlohu syntaxí, která odpovídá vybrané úrovni kompatibility.

## <a name="compatibility-level-12"></a>Úroveň kompatibility 1.2

V úrovni kompatibility 1.2 jsou zavedeny následující významné změny:

###  <a name="amqp-messaging-protocol"></a>Protokol zasílání zpráv AMQP

**1.2 úroveň**: Azure Stream Analytics používá protokol zasílání zpráv [Advanced Message Queueing Protocol (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md) k zápisu do front a témat sběrnice. AMQP umožňuje vytvářet hybridní aplikace napříč platformami pomocí otevřeného standardního protokolu.

### <a name="geospatial-functions"></a>Geoprostorové funkce

**Předchozí úrovně:** Azure Stream Analytics používá výpočty zeměpisu.

**1.2 úroveň:** Azure Stream Analytics umožňuje vypočítat geometrické promítnuté geografické souřadnice. Podpis geoprostorových funkcí se nemění. Jejich sémantika je však mírně odlišná, což umožňuje přesnější výpočty než dříve.

Azure Stream Analytics podporuje indexování geoprostorových referenčních dat. Referenční data obsahující geoprostorové prvky lze indexovat pro rychlejší výpočty spojení.

Aktualizované geoprostorové funkce přinášejí plnou expresivitu geoprostorového formátu Známého textu (WKT). Můžete určit další geoprostorové součásti, které nebyly dříve podporovány pomocí geojsonu.

Další informace najdete [v tématu Aktualizace geoprostorových funkcí ve službě Azure Stream Analytics – Cloud a IoT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Paralelní spuštění dotazu pro vstupní zdroje s více oddíly

**Předchozí úrovně:** Azure Stream Analytics dotazy vyžaduje použití PARTITION BY klauzule paralelizovat zpracování dotazů napříč vstupní zdrojové oddíly.

**1.2 úroveň:** Pokud logiku dotazu lze paralelizovat mezi vstupní zdrojové oddíly, Azure Stream Analytics vytvoří samostatné instance dotazu a spustí výpočty paralelně.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Nativní integrace rozhraní API pro hromadné vysílání s výstupem CosmosDB

**Předchozí úrovně:** Chování upsert bylo *vložit nebo sloučit*.

**1.2 úroveň:** Nativní integrace rozhraní API hromadného rozhraní s výstupem CosmosDB maximalizuje propustnost a efektivně zpracovává požadavky na omezení. Další informace najdete [na webu Výstup Azure Stream Analytics na stránku Azure Cosmos DB](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12).

Chování upsert je *vložit nebo nahradit*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset při zápisu do výstupu SQL

**Předchozí úrovně:** [Typy DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) byly upraveny na Čas UTC.

**1.2 úroveň:** DateTimeOffset již není upraven.

### <a name="long-when-writing-to-sql-output"></a>Dlouho při zápisu do výstupu SQL

**Předchozí úrovně:** Hodnoty byly zkráceny na základě typu cíle.

**1.2 úroveň:** Hodnoty, které se nevejdou do cílového typu, jsou zpracovány podle zásady chyb výstupu.

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>Záznam a serializace pole při zápisu do výstupu SQL

**Předchozí úrovně:** Záznamy byly zapsány jako "Záznam" a pole byla zapsána jako "Array".

**1.2 úroveň:** Záznamy a pole jsou serializovány ve formátu JSON.

### <a name="strict-validation-of-prefix-of-functions"></a>Přísná validace předpony funkcí

**Předchozí úrovně:** Neexistovalo žádné přísné ověření předpon funkcí.

**1.2 úroveň:** Azure Stream Analytics má přísné ověření předpon funkcí. Přidání předpony k vestavěné funkci způsobí chybu. Například`myprefix.ABS(…)` není podporována.

Přidání předpony do předdefinovaných agregací má také za následek chybu. Například `myprefix.SUM(…)` není podporována.

Použití předpony "systém" pro všechny uživatelem definované funkce má za následek chybu.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Zakázat pole a objekt jako klíčové vlastnosti ve výstupním adaptéru Cosmos DB

**Předchozí úrovně:** Typy polí a objektů byly podporovány jako vlastnost klíče.

**1.2 úroveň:** Typy polí a objektů již nejsou podporovány jako vlastnost klíče.

## <a name="compatibility-level-11"></a>Úroveň kompatibility 1.1

V úrovni kompatibility 1.1 jsou zavedeny následující významné změny:

### <a name="service-bus-xml-format"></a>Formát XML sběrnice služby Service Bus

**úroveň 1.0:** Azure Stream Analytics používá DataContractSerializer, takže obsah zprávy zahrnuty XML značky. Například:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**1.1 úroveň:** Obsah zprávy obsahuje datový proud přímo bez dalších značek. Příklad: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Trvalá rozlišování malých a velkých písmen pro názvy polí

**úroveň 1.0:** Názvy polí byly změněny na malá písmena při zpracování motorem Azure Stream Analytics.

**1.1 úroveň:** rozlišování malých a velkých písmen je zachována pro názvy polí, když jsou zpracovány strojem Azure Stream Analytics.

> [!NOTE]
> Trvalá rozlišování velkých a malých písmen ještě není k dispozici pro úlohy Stream Analytic hostované pomocí prostředí Edge. V důsledku toho jsou všechny názvy polí převedeny na malá písmena, pokud je vaše úloha hostována na okraji.

### <a name="floatnandeserializationdisabled"></a>Serializace FloatNaNDeje zakázána.

**úroveň 1.0:** Příkaz VYTVOŘIT TABULKU nefiltrovali události pomocí nan (Not-a-Number. Například Nekonečno, -Nekonečno) v typu sloupce FLOAT, protože jsou mimo zdokumentovaný rozsah pro tato čísla.

**1.1 úroveň:** VYTVOŘIT TABULKU umožňuje zadat silné schéma. Modul Stream Analytics ověří, že data odpovídají tomuto schématu. Pomocí tohoto modelu může příkaz filtrovat události s hodnotami NaN.

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>Zakázat automatické přesměrování pro řetězce datetime v JSON

**úroveň 1.0:** Analyzátor JSON by automaticky upcast řetězcové hodnoty s datem/ časem/ zónou informace dateTime typu a pak převést na UTC. Toto chování vedlo ke ztrátě informací o časovém pásmu.

**1.1 úroveň:** Neexistuje žádné další automatické upcast hodnot řetězce s datem/ časem / zónou informace dateTime typu. V důsledku toho jsou uchovávány informace o časovém pásmu.

## <a name="next-steps"></a>Další kroky

* [Poradce při potížích se vstupy Azure Stream Analytics](stream-analytics-troubleshoot-input.md)
* [Stav zdrojů Služby Stream Analytics](stream-analytics-resource-health.md)
