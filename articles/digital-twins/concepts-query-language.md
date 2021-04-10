---
title: Dotazovací jazyk
titleSuffix: Azure Digital Twins
description: Seznamte se se základy dotazovacího jazyka Azure pro digitální vlákna.
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: fc9cd95063f84a9af7f989af9a65ce8f99852dc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103490972"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>O dotazovacím jazyku pro digitální vlákna Azure

Odvolat, že střed digitálních vláken Azure je [dvojitým grafem](concepts-twins-graph.md)vytvořeným z digitálních vláken a vztahů. 

Tento graf se dá dotázat, aby se získaly informace o digitálních vztazích a vztazích, které obsahuje. Tyto dotazy se píšou ve vlastním dotazovacím jazyce podobném SQL, který se označuje jako **dotazovací jazyk Azure Digital Twins**. To je podobné [dotazovacímu jazyku IoT Hub](../iot-hub/iot-hub-devguide-query-language.md) s mnoha srovnatelnými funkcemi.

Tento článek popisuje základy dotazovacího jazyka a jeho schopností. Podrobnější příklady syntaxe dotazů a spuštění požadavků na dotazy naleznete v tématu [*How to: Query*](how-to-query-graph.md)a requested Graph.

## <a name="about-the-queries"></a>O dotazech

Pomocí jazyka dotazů digitálních vláken Azure můžete načíst digitální vlákna podle jejich...
* vlastnosti (včetně [vlastností značek](how-to-use-tags.md))
* modely
* relationships
  - vlastnosti relací

K odeslání dotazu do služby z klientské aplikace použijete [**rozhraní API pro dotazování**](/rest/api/digital-twins/dataplane/query)digitálních vláken Azure. Jedním ze způsobů, jak používat rozhraní API, je prostřednictvím jedné ze [sad SDK](how-to-use-apis-sdks.md#overview-data-plane-apis) pro digitální vlákna Azure.

### <a name="considerations-for-querying"></a>Pokyny pro dotazování

Při psaní dotazů pro digitální vlákna Azure mějte na paměti následující skutečnosti:
* Rozlišovat **velká a malá** písmena: u všech operací dotazů Azure Digital autoforms se rozlišují velká a malá písmena, proto je potřeba se starat o použití přesně názvů definovaných v modelech. Pokud jsou názvy vlastností špatně napsané nebo nesprávně použita, je sada výsledků prázdná a nevrátí se žádné chyby.
* **Řídicí jednoduché uvozovky**: Pokud text dotazu obsahuje v datech jeden znak uvozovky, bude nutné, aby byla tato uvozovka uvozená `\` znakem. Tady je příklad, který se zabývá hodnotou vlastnosti *D'Souza*:

  :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="EscapedSingleQuote":::

## <a name="reference-expressions-and-conditions"></a>Reference: výrazy a podmínky

Tato část popisuje operátory a funkce, které jsou k dispozici pro zápis dotazů digitálních vláken Azure. Například dotazy, které ilustrují použití těchto funkcí, naleznete v tématu [*How to: Query a Remonstred Graph*](how-to-query-graph.md).

### <a name="operators"></a>Operátory

Podporovány jsou následující operátory:

| Rodina | Operátory |
| --- | --- |
| Logické |`AND`, `OR`, `NOT` |
| Porovnání | `=`, `!=`, `<`, `>`, `<=`, `>=` |
| Contains | `IN`, `NIN` |

### <a name="functions"></a>Functions

Podporují se následující funkce kontroly a přetypování typů:

| Funkce | Description |
| -------- | ----------- |
| `IS_DEFINED` | Vrátí logickou hodnotu, která znamená, zda byla vlastnost přiřazena hodnota. To je podporováno pouze v případě, že je hodnota primitivního typu. Primitivní typy zahrnují řetězec, Boolean, Numeric nebo `null` . `DateTime`, typy objektů a pole nejsou podporovány. |
| `IS_OF_MODEL` | Vrátí logickou hodnotu, která označuje, jestli zadaný typ vlákna odpovídá zadanému typu modelu. |
| `IS_BOOL` | Vrací logickou hodnotu označující, zda je typ zadaného výrazu logická hodnota. |
| `IS_NUMBER` | Vrací logickou hodnotu označující, zda je typ zadaného výrazu číslo. |
| `IS_STRING` | Vrací logickou hodnotu označující, zda je typ zadaného výrazu řetězec. |
| `IS_NULL` | Vrací logickou hodnotu označující, zda je typ zadaného výrazu null. |
| `IS_PRIMITIVE` | Vrací logickou hodnotu označující, zda je typ zadaného výrazu primitivní (řetězec, logická hodnota, číselná hodnota nebo `null` ). |
| `IS_OBJECT` | Vrací logickou hodnotu označující, zda je typ zadaného výrazu objekt JSON. |

Podporovány jsou následující řetězcové funkce:

| Funkce | Description |
| -------- | ----------- |
| `STARTSWITH(x, y)` | Vrátí logickou hodnotu, která označuje, zda první řetězcový výraz začíná druhým. |
| `ENDSWITH(x, y)` | Vrátí logickou hodnotu, která označuje, zda první řetězcový výraz končí druhým. |

## <a name="query-limitations"></a>Omezení dotazů

V této části jsou popsána omezení dotazovacího jazyka.

* Časování: může dojít ke zpoždění až na 10 sekund, než se změny v instanci projeví v dotazech. Například pokud dokončíte operaci, jako je vytváření nebo odstraňování vláken s rozhraním API DigitalTwins, výsledek se v dotazech rozhraní API nemusí projevit okamžitě. Čekání na krátkou dobu by měla být dostačující pro vyřešení.
* V rámci příkazu nejsou podporovány žádné poddotazy `FROM` .
* `OUTER JOIN` Sémantika není podporována, což znamená, že pokud má relace hodnotu nula, pak je celý "řádek" odstraněn z výstupní sady výsledků.
* Hloubka procházení grafů je omezená na pět `JOIN` úrovní na jeden dotaz.
* Relace v rámci digitálních vláken Azure se nedají dotazovat jako nezávislé entity. také je nutné zadat informace o zdroji dat, ze kterého vztah pochází. To znamená, že k této operaci existují určitá omezení `JOIN` , která se používají k dotazování vztahů, aby se zajistilo, že dotaz deklaruje vlákna, kde začíná dotaz. Příklady najdete v tématu [*dotazování podle vztahu*](how-to-query-graph.md#query-by-relationship) v článku *Postupy: dotazování na článek s dvojitým grafem* .

## <a name="next-steps"></a>Další kroky

Naučte se zapisovat dotazy a zobrazit příklady klientského kódu v tématu [*Postupy: dotazování na nevlákenný graf*](how-to-query-graph.md).