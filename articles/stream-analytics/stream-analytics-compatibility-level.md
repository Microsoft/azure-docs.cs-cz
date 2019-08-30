---
title: Vysvětlení úroveň kompatibility pro úlohy Azure Stream Analytics
description: Zjistěte, jak nastavit úroveň kompatibility pro úlohy Azure Stream Analytics a hlavní změny v nejnovější úroveň kompatibility
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/02/2019
ms.openlocfilehash: d6d31506a13656a954c48dfee00f14d8ab381fd5
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173247"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Úroveň kompatibility pro úlohy Azure Stream Analytics

Tento článek popisuje možnost úrovně kompatibility v Azure Stream Analytics. Stream Analytics je spravovaná služba s pravidelnými aktualizacemi funkcí a vylepšení výkonu. Většina aktualizací za běhu služby je automaticky dostupná pro koncové uživatele. 

Některé nové funkce ve službě však mohou způsobit zásadní změnu, například změnu chování existující úlohy nebo změnu způsobu, jakým se data ve spuštěných úlohách spotřebovávají. Stávající úlohy Stream Analytics běží bez podstatných změn, protože nastavení úrovně kompatibility zůstane nižší. Až budete připraveni na nejnovější chování modulu runtime, můžete se rozhodnout, že vyvyvoláváte úroveň kompatibility. 

## <a name="choose-a-compatibility-level"></a>Zvolit úroveň kompatibility

Úroveň kompatibility řídí chování za běhu úlohy stream analytics. 

Azure Stream Analytics aktuálně podporuje tři úrovně kompatibility:

* 1,0 – předchozí chování
* 1,1 – výchozí chování
* 1,2 (Preview) – nejnovější chování s nejnovějšími vylepšeními ve vyhodnocování

Původní úroveň kompatibility 1,0 byla zavedena během obecné dostupnosti Azure Stream Analytics před několika lety.

Když vytváříte novou Stream Analytics úlohu, je osvědčeným postupem, jak ji vytvořit pomocí nejnovější úrovně kompatibility. Zahajte návrh úlohy, který se spoléhá na nejnovější chování, abyste se později vyhnuli přidané změně a složitosti.

## <a name="set-the-compatibility-level"></a>Nastavení úrovně kompatibility

Úroveň kompatibility pro úlohu Stream Analytics můžete nastavit v Azure Portal nebo pomocí [volání funkce vytvořit úlohu REST API](/rest/api/streamanalytics/stream-analytics-job).

Postup aktualizace úrovně kompatibility úlohy v Azure Portal:

1. K vyhledání Stream Analytics úlohy použijte [Azure Portal](https://portal.azure.com) .
2. Před aktualizací úrovně kompatibility úlohu zastavte. Úroveň kompatibility nelze aktualizovat, pokud vaše úloha není ve spuštěném stavu.
3. V části **Konfigurovat** záhlaví vyberte **úroveň kompatibility**.
4. Vyberte hodnotu úrovně kompatibility, kterou chcete.
5. V dolní části stránky vyberte **Save (Uložit** ).

![Úroveň kompatibility Stream Analytics na webu Azure portal](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

Při aktualizaci úrovní kompatibility T-SQL compiler ověří úlohy pomocí syntaxe, která odpovídá úroveň kompatibility vybrané.

## <a name="compatibility-level-12-preview"></a>Úroveň kompatibility 1,2 (Preview)

V úrovni kompatibility 1,2 jsou představeny tyto hlavní změny:

### <a name="geospatial-functions"></a>Geoprostorové funkce

**Předchozí úrovně:** Azure Stream Analytics použití geografických výpočtů.

**úroveň 1,2:** Azure Stream Analytics umožňuje výpočet geometrických geografických souřadnic. Signatura geoprostorových funkcí se nijak nemění. Jejich sémantika je však mírně odlišná a umožňuje přesnější výpočet než předtím.

Azure Stream Analytics podporuje indexování geoprostorových referenčních dat. Referenční data obsahující geoprostorové prvky lze indexovat pro rychlejší výpočet spojení.

Aktualizované geoprostorové funkce přinášejí úplný expresivity geoprostorového formátu dobře známého textu (Well). Můžete určit další geoprostorové komponenty, které nebyly dříve podporovány s funkcí injson.

Další informace najdete v tématu [aktualizace geoprostorových funkcí v Azure Stream Analytics – Cloud a IoT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Paralelní provádění dotazů pro vstupní zdroje s více oddíly

**Předchozí úrovně:** Azure Stream Analytics dotazy vyžadovaly použití klauzule PARTITION BY k paralelizovat zpracování dotazů přes vstupní zdrojové oddíly.

**úroveň 1,2:** Pokud je možné logiku dotazů paralelně rozdělit mezi vstupní zdrojové oddíly, Azure Stream Analytics vytvoří samostatné instance dotazů a souběžné spouštění výpočtů.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Nativní integrace rozhraní API s využitím výstupu CosmosDB

**Předchozí úrovně:** Chování Upsert bylo *vloženo nebo sloučeno*.

**úroveň 1,2:** Nativní integrace rozhraní API s využitím výstupu CosmosDB maximalizuje propustnost a efektivně zpracovává požadavky na omezování. Další informace najdete [na stránce Azure Stream Analytics výstup do Azure Cosmos DB](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12).

Chování Upsert je *vloženo nebo nahrazeno*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset při zápisu do výstupu SQL

**Předchozí úrovně:** Typy [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) byly upraveny na čas UTC.

**úroveň 1,2:** DateTimeOffset již není upravován.

### <a name="long-when-writing-to-sql-output"></a>Long při zápisu do výstupu SQL

**Předchozí úrovně:** Hodnoty byly zkráceny na základě cílového typu.

**úroveň 1,2:** Hodnoty, které se nevejdou do cílového typu, se zpracovávají podle zásad výstupních chyb.

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>Záznam a serializace pole při zápisu do výstupu SQL

**Předchozí úrovně:** Záznamy byly zapsány jako "Record" a pole byly zapsána jako "Array".

**úroveň 1,2:** Záznamy a pole jsou serializovány ve formátu JSON.

### <a name="strict-validation-of-prefix-of-functions"></a>Striktní ověření předpony funkcí

**Předchozí úrovně:** Nedošlo k žádné striktní kontrole prefixů funkcí.

**úroveň 1,2:** Azure Stream Analytics má striktní ověření prefixů funkcí. Přidáním předpony k předdefinované funkci dojde k chybě. Například`myprefix.ABS(…)` není podporován.

Přidání předpony k předdefinovaným agregacím také způsobí chybu. Například `myprefix.SUM(…)` není podporován.

Výsledkem použití předpony "System" pro jakékoli uživatelsky definované funkce je chyba.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Zakázat pole a objekt jako klíčové vlastnosti v Cosmos DB výstupním adaptéru

**Předchozí úrovně:** Typy polí a objektů byly podporovány jako vlastnost klíče.

**úroveň 1,2:** Typy polí a objektů již nejsou podporovány jako vlastnost klíče.

## <a name="compatibility-level-11"></a>Úroveň kompatibility 1,1

V úrovni kompatibility 1.1 byly zavedeny následující hlavní změny:

### <a name="service-bus-xml-format"></a>Service Bus formát XML

**úroveň 1,0:** Azure Stream Analytics použila DataContractSerializer, takže obsah zprávy obsahuje značky XML. Příklad:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**úroveň 1,1:** Obsah zprávy obsahuje datový proud přímo bez dalších značek. Příklad: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Zachování rozlišování velkých a malých písmen u názvů polí

**úroveň 1,0:** Názvy polí se změnily na malá písmena při zpracování modulem Azure Stream Analytics.

**1,1 úroveň:** rozlišování malých a velkých písmen je u názvů polí při zpracovávání modulem Azure Stream Analytics trvalé.

> [!NOTE]
> Zachování rozlišování ještě není k dispozici pro Stream analytických úloh, které jsou hostované pomocí hraničním prostředí. Názvy všech polí v důsledku toho jsou převedeny na malá písmena, pokud vaše úloha je hostovaná na hraničních zařízeních.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**úroveň 1,0:** Příkaz CREATE TABLE nefiltroval události s NaN (nejedná se o číslo). Například nekonečno, - nekonečno) ve sloupci PLOVOUCÍ typ, protože jsou mimo rozsah zdokumentovaných pro tato čísla.

**úroveň 1,1:** CREATE TABLE umožňuje zadat silné schéma. Modul Stream Analytics ověří, že toto schéma odpovídá data. V tomto modelu můžete příkaz Filtrovat události s hodnoty NaN.

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>Zakázat automatické přetypování pro řetězce DateTime ve formátu JSON

**úroveň 1,0:** Analyzátor JSON automaticky přesměruje řetězcové hodnoty s informacemi o datu a času nebo zóně na typ DateTime a pak je převede na UTC. Výsledkem tohoto chování je ztráta informací o časovém pásmu.

**úroveň 1,1:** Neexistují žádné další automatické přetypování řetězcových hodnot s informacemi o datu a času nebo zóně k typu DateTime. V důsledku toho se ukládají informace o časovém pásmu.

## <a name="next-steps"></a>Další postup

* [Řešení potíží s Azure Stream Analytics vstupy](stream-analytics-troubleshoot-input.md)
* [Stav prostředku Stream Analytics](stream-analytics-resource-health.md)
