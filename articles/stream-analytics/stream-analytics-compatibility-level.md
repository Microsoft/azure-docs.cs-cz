---
title: Azure Stream Analytics úrovně kompatibility
description: Naučte se nastavit úroveň kompatibility pro úlohu Azure Stream Analytics a významné změny v nejnovější úrovni kompatibility.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: a040aecbdee40832bd21256e26a140a986b65e39
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606238"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Úroveň kompatibility pro úlohy Azure Stream Analytics

Tento článek popisuje možnost úrovně kompatibility v Azure Stream Analytics.

Stream Analytics je spravovaná služba s [pravidelnými aktualizacemi funkcí a vylepšení výkonu s konstantním výkonem](https://azure.microsoft.com/updates/?product=stream-analytics). Většina aktualizací za běhu služby je automaticky dostupná koncovým uživatelům, nezávisle na úrovni kompatibility. Pokud ale nové funkce zavádí změnu v chování stávajících úloh nebo změníte způsob, jakým se data ve spuštěných úlohách spotřebovávají, zavádíme tuto změnu v rámci nové úrovně kompatibility. Stávající úlohy Stream Analytics běží bez podstatných změn, protože nastavení úrovně kompatibility zůstane nižší. Až budete připraveni na nejnovější chování modulu runtime, můžete se rozhodnout, že vyvyvoláváte úroveň kompatibility.


## <a name="choose-a-compatibility-level"></a>Zvolit úroveň kompatibility

Úroveň kompatibility řídí chování za běhu úlohy Stream Analytics.

Azure Stream Analytics aktuálně podporuje tři úrovně kompatibility:

* 1,2 – nejnovější chování s nejnovějšími vylepšeními
* 1,1 – předchozí chování
* 1,0 – původní úroveň kompatibility zavedená během obecné dostupnosti Azure Stream Analytics před několika lety. 

Když vytváříte novou Stream Analytics úlohu, je osvědčeným postupem, jak ji vytvořit pomocí nejnovější úrovně kompatibility. Zahajte návrh úlohy, který se spoléhá na nejnovější chování, abyste se později vyhnuli přidané změně a složitosti.

## <a name="set-the-compatibility-level"></a>Nastavení úrovně kompatibility

Úroveň kompatibility pro úlohu Stream Analytics můžete nastavit v Azure Portal nebo pomocí [volání funkce vytvořit úlohu REST API](/rest/api/streamanalytics/2016-03-01/streamingjobs/createorreplace#compatibilitylevel).

Postup aktualizace úrovně kompatibility úlohy v Azure Portal:

1. K vyhledání Stream Analytics úlohy použijte [Azure Portal](https://portal.azure.com) .
2. Před aktualizací úrovně kompatibility úlohu **zastavte** . Úroveň kompatibility nejde aktualizovat, pokud je vaše úloha ve spuštěném stavu.
3. V části **Konfigurovat** záhlaví vyberte **úroveň kompatibility**.
4. Vyberte hodnotu úrovně kompatibility, kterou chcete.
5. V dolní části stránky vyberte **Save (Uložit** ).

![Úroveň kompatibility Stream Analytics v Azure Portal](media/stream-analytics-compatibility-level/stream-analytics-compat-level-1-2.png)

Při aktualizaci úrovně kompatibility T-Compiler ověří úlohu se syntaxí, která odpovídá vybrané úrovni kompatibility.

## <a name="compatibility-level-12"></a>Úroveň kompatibility 1,2

V úrovni kompatibility 1,2 jsou představeny tyto hlavní změny:

###  <a name="amqp-messaging-protocol"></a>Protokol zasílání zpráv AMQP

**1,2 úroveň**: Azure Stream Analytics používá rozšířený protokol zasílání zpráv [protokolu AMQP (Message Queueing Protocol)](../service-bus-messaging/service-bus-amqp-overview.md) k zápisu do Service Busch front a témat. AMQP umožňuje sestavovat hybridní aplikace pro různé platformy s využitím otevřeného standardního protokolu.

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

**úroveň 1,2:** Nativní integrace rozhraní API s využitím výstupu CosmosDB maximalizuje propustnost a efektivně zpracovává požadavky na omezování. Další informace najdete [na stránce Azure Stream Analytics výstup do Azure Cosmos DB](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12).

Chování Upsert je *vloženo nebo nahrazeno*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset při zápisu do výstupu SQL

**Předchozí úrovně:** typy [DateTimeOffset](/sql/t-sql/data-types/datetimeoffset-transact-sql) byly upraveny na čas UTC.

**úroveň 1,2:** DateTimeOffset již není upravován.

### <a name="long-when-writing-to-sql-output"></a>Long při zápisu do výstupu SQL

**Předchozí úrovně:** Hodnoty byly zkráceny na základě cílového typu.

**úroveň 1,2:** Hodnoty, které se nevejdou do cílového typu, se zpracovávají podle zásad výstupních chyb.

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>Záznam a serializace pole při zápisu do výstupu SQL

**Předchozí úrovně:** Záznamy byly zapsány jako "Record" a pole byly zapsána jako "Array".

**úroveň 1,2:** Záznamy a pole jsou serializovány ve formátu JSON.

### <a name="strict-validation-of-prefix-of-functions"></a>Striktní ověření předpony funkcí

**Předchozí úrovně:** Nedošlo k žádné striktní kontrole prefixů funkcí.

**úroveň 1,2:** Azure Stream Analytics má striktní ověření prefixů funkcí. Přidáním předpony k předdefinované funkci dojde k chybě. Například `myprefix.ABS(…)` není podporován.

Přidání předpony k předdefinovaným agregacím také způsobí chybu. Například `myprefix.SUM(…)` není podporován.

Výsledkem použití předpony "System" pro jakékoli uživatelsky definované funkce je chyba.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Zakázat pole a objekt jako klíčové vlastnosti v Cosmos DB výstupním adaptéru

**Předchozí úrovně:** Typy polí a objektů byly podporovány jako vlastnost klíče.

**úroveň 1,2:** Typy polí a objektů již nejsou podporovány jako vlastnost klíče.

## <a name="compatibility-level-11"></a>Úroveň kompatibility 1,1

V úrovni kompatibility 1,1 jsou představeny tyto hlavní změny:

### <a name="service-bus-xml-format"></a>Service Bus formát XML

**úroveň 1,0:** Azure Stream Analytics použila DataContractSerializer, takže obsah zprávy obsahuje značky XML. Například:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**úroveň 1,1:** Obsah zprávy obsahuje datový proud přímo bez dalších značek. Příklad: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Zachování rozlišování velkých a malých písmen u názvů polí

**úroveň 1,0:** Názvy polí se změnily na malá písmena při zpracování modulem Azure Stream Analytics.

**1,1 úroveň:** rozlišování malých a velkých písmen je u názvů polí při zpracovávání modulem Azure Stream Analytics trvalé.

> [!NOTE]
> Zachování citlivosti na velká a malá písmena pro streamování analytických úloh hostovaných pomocí hraničního prostředí ještě není k dispozici. V důsledku toho jsou názvy všech polí převedeny na malá písmena, pokud je vaše úloha hostována na hraničních zařízeních.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**úroveň 1,0:** Příkaz CREATE TABLE nefiltroval události s NaN (nejedná se o číslo). Například nekonečno,-nekonečno) v typu sloupce typu FLOAT, protože jsou z dokumentovaného rozsahu pro tato čísla.

**úroveň 1,1:** CREATE TABLE umožňuje zadat silné schéma. Modul Stream Analytics ověří, že data odpovídají tomuto schématu. Pomocí tohoto modelu může příkaz Filtrovat události s hodnotami NaN.

### <a name="disable-automatic-conversion-of-datetime-strings-to-datetime-type-at-ingress-for-json"></a>Zakázat automatický převod řetězců DateTime na typ DateTime v příchozím přenosu pro JSON

**úroveň 1,0:** Analyzátor JSON automaticky převede hodnoty řetězců s informacemi o datu a času nebo zóně na typ DATETIME při příchozím přenosu, takže hodnota okamžitě ztratí původní formátování a informace o časovém pásmu. Vzhledem k tomu, že se jedná o vstup, i když se toto pole v dotazu nepoužilo, je převedeno na UTC DateTime.

**úroveň 1,1:** Neexistuje žádný automatický převod řetězcových hodnot s informacemi o datu a čase nebo zóně na typ DATETIME. V důsledku toho jsou uchovávány informace o časovém pásmu a původní formátování. Pokud je však pole NVARCHAR (MAX) použito v dotazu jako součást výrazu DATETIME (například funkce DATEADD), je převedeno na typ DATETIME pro provedení výpočtu a ztratí původní formulář.

## <a name="next-steps"></a>Další kroky

* [Řešení potíží s Azure Stream Analytics vstupy](stream-analytics-troubleshoot-input.md)
* [Stav prostředku Stream Analytics](./stream-analytics-troubleshoot-query.md)
