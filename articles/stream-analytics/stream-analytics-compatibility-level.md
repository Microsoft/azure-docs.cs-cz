---
title: Vysvětlení úroveň kompatibility pro úlohy Azure Stream Analytics
description: Zjistěte, jak nastavit úroveň kompatibility pro úlohy Azure Stream Analytics a hlavní změny v nejnovější úroveň kompatibility
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: e4bbfdbcf7a295089570d4c8b77b07fd7270b3fd
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59998256"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Úroveň kompatibility pro úlohy Azure Stream Analytics

Tento článek popisuje možnosti úrovně kompatibility ve službě Azure Stream Analytics. Stream Analytics je spravovaná služba, se aktualizace regulární funkcí a vylepšení výkonu. Většinu aktualizací služby moduly runtime jsou automaticky k dispozici koncovým uživatelům. 

Ale některé nové funkce ve službě může představovat velkou změnu, jako je například změna v chování existující úlohy, nebo změny v datech způsob, jak se spotřebovává v běžící úlohy. Můžete zachovat existující úlohy Stream Analytics spustíte bez významné změny se zaškrtnutým políčkem nastavení sníženou úrovní kompatibility. Až budete připravení nejnovější chování za běhu, které můžete vyjádřit výslovný souhlas zvýšením úrovně kompatibility. 

## <a name="choose-a-compatibility-level"></a>Zvolte úroveň kompatibility

Úroveň kompatibility řídí chování za běhu úlohy stream analytics. 

Azure Stream Analytics aktuálně podporuje tři úrovně kompatibility:

* 1.0 – výchozí úroveň
* 1.1 – současné chování při vydání verze
* 1.2 (preview) – nejnovější chování pomocí nejnovější vylepšení ve vyhodnocování

Původní 1.0 úroveň kompatibility byla zavedena v období všeobecné dostupnosti služby Azure Stream Analytics už před několika lety.

Při vytváření nové úlohy Stream Analytics, je osvědčeným postupem je vytvoření s použitím nejnovější úroveň kompatibility. Spuštění úlohy návrhu importovaným nejnovější chování, aby se zabránilo přidání změn a složitosti později.

## <a name="set-the-compatibility-level"></a>Nastavit úroveň kompatibility

Můžete nastavit úroveň kompatibility pro úlohu Stream Analytics na portálu Azure portal nebo pomocí [vytvořit úlohu volání rozhraní REST API](/rest/api/streamanalytics/stream-analytics-job).

Chcete-li aktualizovat úroveň kompatibility úlohy na webu Azure Portal:

1. Použití [webu Azure portal](https://portal.azure.com) najít vaší úlohy Stream Analytics.
2. **Zastavit** úlohy před aktualizací úroveň kompatibility. Úroveň kompatibility nelze aktualizovat, pokud vaše úloha není ve spuštěném stavu.
3. V části **konfigurovat** záhlaví, vyberte **úroveň kompatibility**.
4. Zvolte požadovanou hodnotu úroveň kompatibility.
5. Vyberte **Uložit** v dolní části stránky.

![Úroveň kompatibility Stream Analytics na webu Azure portal](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

Při aktualizaci úrovní kompatibility T-SQL compiler ověří úlohy pomocí syntaxe, která odpovídá úroveň kompatibility vybrané.

## <a name="compatibility-level-12"></a>Úroveň kompatibility 1.2

V úrovni kompatibility 1.2 byly zavedeny následující hlavní změny:

### <a name="geospatial-functions"></a>Geoprostorové funkce

**Předchozí úrovně:** Azure Stream Analytics používá výpočty zeměpisné oblasti.

**1.2 úroveň:** Azure Stream Analytics umožňuje compute geometrické předpokládané geografické souřadnice. Není žádná změna v podpisu geoprostorové funkce. Je však poněkud lišit, a umožňuje přesnější výpočty než před jejich sémantiku.

Azure Stream Analytics podporuje geoprostorové indexování dat k odkazu. Referenční Data obsahující prvky geoprostorové můžete možné indexovat pro rychlejší připojení k výpočtu.

Aktualizované geoprostorové funkce přineste úplné expresivity formátu geoprostorové dobře známé Text (Well-Known text). Zadejte další geoprostorové komponenty, které dříve nebyly podporovány s GeoJson.

Další informace najdete v tématu [aktualizuje geoprostorové funkce ve službě Azure Stream Analytics – cloudem a hraničními zařízeními IoT](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Paralelní provádění dotazů pro vstupní zdroje s více oddílů

**Předchozí úrovně:** Azure Stream Analytics dotazů vyžaduje použití klauzule PARTITION BY paralelizovat dotaz zpracování napříč oddíly vstupní zdroj.

**1.2 úroveň:** Pokud logiku dotazu může být paralelizována přes vstupní zdrojové oddíly, Azure Stream Analytics vytváří samostatný dotaz instance a spustí paralelní výpočty.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Nativní integrace rozhraní API hromadného s výkonem CosmosDB

**Předchozí úrovně:** Chování upsert byl *vložit nebo sloučit*.

**1.2 úroveň:** Nativní integrace rozhraní API hromadného s výkonem CosmosDB maximalizuje propustnost a efektivně zvládne omezování požadavků. Další informace najdete v tématu [výstupu Azure Stream Analytics na stránku služby Azure Cosmos DB](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12).

Chování upsert je *vložení nebo nahrazení*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset při zápisu do výstupu SQL

**Předchozí úrovně:** [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) typy byly upraveny na čas UTC.

**1.2 úroveň:** Už se upraví DateTimeOffset.

### <a name="strict-validation-of-prefix-of-functions"></a>Přísný ověřovací předpony funkcí

**Předchozí úrovně:** Došlo k dispozici žádné přísný ověřovací funkce předpony.

**1.2 úroveň:** Azure Stream Analytics má přísný ověřovací funkce předpony. Přidání předpony předdefinované funkce způsobí chybu. Například`myprefix.ABS(…)` se nepodporuje.

Přidání předpony vestavěné agregace také výsledkem chyba. Například `myprefix.SUM(…)` se nepodporuje.

Pomocí předpony "systém" pro všechny uživatelem definované funkce výsledkem chyba.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Zakázat pole a objektu jako vlastnosti klíče v adaptér pro výstup služby Cosmos DB

**Předchozí úrovně:** Jako klíčová vlastnost nebyly podporovány typy pole a objektu.

**1.2 úroveň:** Typy pole a objekt již nejsou podporovány jako klíčová vlastnost.

## <a name="compatibility-level-11"></a>Úroveň kompatibility 1.1

V úrovni kompatibility 1.1 byly zavedeny následující hlavní změny:

### <a name="service-bus-xml-format"></a>Formát XML služby Service Bus

**úroveň 1.0:** Azure Stream Analytics používá DataContractSerializer, takže obsah zprávy zahrnout tagů XML. Příklad:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**1.1 úroveň:** Obsah zprávy obsahuje datovým proudem přímo s žádné další značky. Příklad: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Zachování rozlišování názvů polí

**úroveň 1.0:** Názvy polí byly změněny na malá písmena, když modul Azure Stream Analytics zpracovává.

**1.1 úroveň:** rozlišování se ukládají pro názvy polí při jejich zpracování pomocí modulu Azure Stream Analytics.

> [!NOTE]
> Zachování rozlišování ještě není k dispozici pro Stream analytických úloh, které jsou hostované pomocí hraničním prostředí. Názvy všech polí v důsledku toho jsou převedeny na malá písmena, pokud vaše úloha je hostovaná na hraničních zařízeních.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**úroveň 1.0:** Příkaz CREATE TABLE není filtrovat události s NaN (Not a Number. Například nekonečno, - nekonečno) ve sloupci PLOVOUCÍ typ, protože jsou mimo rozsah zdokumentovaných pro tato čísla.

**1.1 úroveň:** VYTVOŘIT tabulku můžete zadat silné schématu. Modul Stream Analytics ověří, že toto schéma odpovídá data. V tomto modelu můžete příkaz Filtrovat události s hodnoty NaN.

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>Zakázat automatické přetypování nahoru pro řetězce data a času ve formátu JSON

**úroveň 1.0:** Analyzátor JSON by automaticky upcast řetězcové hodnoty s informacemi o datum/čas/pásmo na typ DateTime a převeďte jej na UTC. Toto chování za následek ztrátu informace o časovém pásmu.

**1.1 úroveň:** Neexistuje žádné další automaticky upcast řetězcových hodnot s informacemi o datum/čas/pásmo na typ DateTime. V důsledku toho se ukládají informace o časovém pásmu.

## <a name="next-steps"></a>Další postup

* [Řešení potíží s Azure Stream Analytics vstupy](stream-analytics-troubleshoot-input.md)
* [Stream Analytics Resource health](stream-analytics-resource-health.md)
