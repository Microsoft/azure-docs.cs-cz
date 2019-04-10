---
title: Vysvětlení úroveň kompatibility pro úlohy Azure Stream Analytics
description: Zjistěte, jak nastavit úroveň kompatibility pro úlohy Azure Stream Analytics a hlavní změny v nejnovější úroveň kompatibility
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: 6fb93152263d253de983b17d25f02f4c68a172fd
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361399"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Úroveň kompatibility pro úlohy Azure Stream Analytics
 
Úroveň kompatibility odkazuje na chování specifické pro verzi služby Azure Stream Analytics. Azure Stream Analytics je spravovaná služba, se aktualizace regulární funkcí a vylepšení výkonu. Obvykle jsou automaticky provedeny aktualizace k dispozici koncovým uživatelům. Některé nové funkce, může však zavést zásadní změny těchto jako změnu v chování existující úlohy, změňte procesy využívající data z těchto úloh atd. Úroveň kompatibility se používá k reprezentování zásadní změny zavedené ve službě Stream Analytics. Důležité změny jsou vždy zavedená s novou úrovní kompatibility. 

Úroveň kompatibility je zajištěno, že stávající úlohy spustit bez jakékoli chyby. Při vytváření nové úlohy Stream Analytics, je osvědčeným postupem je vytvoření s použitím nejnovější úroveň kompatibility. 
 
## <a name="set-a-compatibility-level"></a>Nastavit úroveň kompatibility 

Úroveň kompatibility řídí chování za běhu úlohy stream analytics. Můžete nastavit úroveň kompatibility pro úlohu Stream Analytics pomocí portálu nebo pomocí [vytvořit úlohu volání rozhraní REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Azure Stream Analytics v současné době podporuje dva kompatibility úrovně – "1.0" a "1.1". Ve výchozím nastavení je úroveň kompatibility nastavena na "1.0", která byla zavedena v období všeobecné dostupnosti služby Azure Stream Analytics. Pokud chcete aktualizovat výchozí hodnotu, přejděte do vaší existující úlohy Stream Analytics > vyberte **úroveň kompatibility** možnost **konfigurovat** části a změňte hodnotu. 

Ujistěte se, že zastavení úlohy před aktualizací úroveň kompatibility. Úroveň kompatibility nelze aktualizovat, pokud vaše úloha není ve spuštěném stavu. 

![Úroveň kompatibility Stream Analytics na webu Azure portal](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

 
Při aktualizaci úrovní kompatibility T-SQL compiler ověří úlohy pomocí syntaxe, která odpovídá úroveň kompatibility vybrané. 

## <a name="major-changes-in-the-latest-compatibility-level-12"></a>Hlavní změny v nejnovější úroveň kompatibility (1.2)

V úrovni kompatibility 1.2 byly zavedeny následující hlavní změny:

### <a name="geospatial-functions"></a>Geoprostorové funkce 

**Předchozí verze:** Azure Stream Analytics používá výpočty zeměpisné oblasti.

**aktuální verze:** Azure Stream Analytics umožňuje compute geometrické předpokládané geografické souřadnice. Není žádná změna v podpisu geoprostorové funkce. Je však poněkud lišit, a umožňuje přesnější výpočty než před jejich sémantiku.

Azure Stream Analytics podporuje geoprostorové indexování dat k odkazu. Referenční Data obsahující prvky geoprostorové můžete možné indexovat pro rychlejší připojení k výpočtu.

Aktualizované geoprostorové funkce přineste úplné expresivity formátu geoprostorové dobře známé Text (Well-Known text). Zadejte další geoprostorové komponenty, které dříve nebyly podporovány s GeoJson.

Další informace najdete v tématu [aktualizuje geoprostorové funkce ve službě Azure Stream Analytics – cloudem a hraničními zařízeními IoT](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Paralelní provádění dotazů pro vstupní zdroje s více oddílů 

**Předchozí verze:** Azure Stream Analytics dotazů vyžaduje použití klauzule PARTITION BY paralelizovat dotaz zpracování napříč oddíly vstupní zdroj.

**aktuální verze:** Pokud logiku dotazu může být paralelizována přes vstupní zdrojové oddíly, Azure Stream Analytics vytváří samostatný dotaz instance a spustí paralelní výpočty.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Nativní integrace rozhraní API hromadného s výkonem CosmosDB

**Předchozí verze:** Chování upsert byl *vložit nebo sloučit*.

**aktuální verze:** Nativní integrace rozhraní API hromadného s výkonem CosmosDB maximalizuje propustnost a efektivně zvládne omezování požadavků.

Chování upsert je *vložení nebo nahrazení*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset při zápisu do výstupu SQL

**Předchozí verze:** [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) typy byly upraveny na čas UTC.

**aktuální verze:** Už se upraví DateTimeOffset.

### <a name="strict-validation-of-prefix-of-functions"></a>Přísný ověřovací předpony funkcí

**Předchozí verze:** Došlo k dispozici žádné přísný ověřovací funkce předpony.

**aktuální verze:** Azure Stream Analytics má přísný ověřovací funkce předpony. Přidání předpony předdefinované funkce způsobí chybu. Například`myprefix.ABS(…)` se nepodporuje.

Přidání předpony vestavěné agregace také výsledkem chyba. Například `myprefix.SUM(…)` se nepodporuje.

Pomocí předpony "systém" pro všechny uživatelem definované funkce výsledkem chyba.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Zakázat pole a objektu jako vlastnosti klíče v adaptér pro výstup služby Cosmos DB

**Předchozí verze:** Jako klíčová vlastnost nebyly podporovány typy pole a objektu.

**aktuální verze:** Typy pole a objekt již nejsou podporovány jako klíčová vlastnost.


## <a name="next-steps"></a>Další postup
* [Řešení potíží s Azure Stream Analytics vstupy](stream-analytics-troubleshoot-input.md)
* [Stream Analytics Resource health](stream-analytics-resource-health.md)
