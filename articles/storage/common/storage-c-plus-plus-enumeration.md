---
title: Seznam prostředků úložiště Azure s knihovnou klienta úložiště pro jazyk C++ | Microsoft Docs
description: Naučte se používat rozhraní API seznamu v Microsoft Azure Klientská knihovna pro úložiště pro jazyk C++ výčet kontejnerů, objektů BLOB, fronty, tabulky a entity.
documentationcenter: .net
services: storage
author: dineshmurthy
manager: jahogg
editor: tysonn
ms.assetid: 33563639-2945-4567-9254-bc4a7e80698f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: dineshm
ms.openlocfilehash: 9844412739f4f6f95416f81347f0f2eeeca62bea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873867"
---
# <a name="list-azure-storage-resources-in-c"></a>Seznam prostředků úložiště Azure v jazyce C++
Seznam operací jsou klíčem k mnoha vývojové scénáře s Azure Storage. Tento článek popisuje postup nejvíce efektivně zobrazení výčtu objektů ve službě Azure Storage pomocí rozhraní API, které jsou součástí Klientská knihovna pro úložiště Microsoft Azure pro jazyk C++ výpis.

> [!NOTE]
> Tato příručka cílem Klientská knihovna pro úložiště Azure pro C++ verze 2.x, která je k dispozici prostřednictvím [NuGet](http://www.nuget.org/packages/wastorage) nebo [Githubu](https://github.com/Azure/azure-storage-cpp).
> 
> 

Klientská knihovna pro úložiště poskytuje celou řadu metod na seznam nebo dotaz, objekty ve službě Azure Storage. Tento článek řeší následující scénáře:

* Seznam kontejnery v účtu
* Seznam objektů BLOB v kontejneru nebo objekt blob virtuální adresář
* Seznam front v účtu
* Seznam tabulek v účtu
* Dotaz entity v tabulce

Každá z těchto metod se zobrazí při použití různých přetížení pro různé scénáře.

## <a name="asynchronous-versus-synchronous"></a>Asynchronní a synchronní
Vzhledem k tomu, že klientská knihovna pro úložiště pro jazyk C++ je postavený na [knihovny C++ REST](https://github.com/Microsoft/cpprestsdk), podporujeme ze své podstaty asynchronních operací s použitím [pplx::task](http://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Například:

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

Synchronní operace zabalení odpovídající asynchronních operací:

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

Pokud pracujete s více vláken aplikacím nebo službám, doporučujeme použít asynchronní rozhraní API přímo místo vytvoření vlákna k volání rozhraní API, což významně ovlivňuje výkon synchronizace.

## <a name="segmented-listing"></a>Segmentované výpis
Škálování cloudové úložiště vyžaduje segmentovaným výpis. Například můžete mít přes milion objekty BLOB v kontejneru objektů blob v Azure nebo přes miliardu entity v Azure Table. Tyto nejsou teoretické čísla, ale případy využití skutečné oddělení.

Je proto nepraktické seznam všech objektů v odpověď o jedné. Místo toho můžete vytvořit seznam objektů s použitím stránkování. Každý výpis rozhraní API má *segmentované* přetížení.

Odpověď operace segmentovaným výpis obsahuje:

* <i>_segment</i>, který obsahuje sadu výsledků vrácených pro jednoho volání rozhraní API seznamu.
* *continuation_token*, který je předat do dalšího hovoru, chcete-li získat další stránky výsledků. Pokud nejsou žádné další výsledky vrátí, token pro pokračování není null.

Například typický volání k vypsání všech objektů BLOB v kontejneru vypadat jako následující fragment kódu. Kód je k dispozici v našem [ukázky](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp):

```cpp
// List blobs in the blob container
azure::storage::continuation_token token;
do
{
    azure::storage::list_blob_item_segment segment = container.list_blobs_segmented(token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_diretory(it->as_directory());
    }
}

    token = segment.continuation_token();
}
while (!token.empty());
```

Všimněte si, že počet výsledků vrácených na stránce je řízena parametr *max_results* v přetížení každé rozhraní API, například:

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

Pokud nezadáte *max_results* parametr, bude výchozí maximální hodnota, která až 5000 výsledky se vrátí v jediné stránce.

Všimněte si, že dotaz Azure Table storage může vrátit žádné záznamy nebo méně záznamy než hodnota *max_results* parametr, který jste zadali, i když token pro pokračování není prázdný. Jedním z důvodů může být, že dotaz nelze dokončit v pět sekund. Také token pro pokračování není prázdná, dotaz by měly pokračovat ve a váš kód by neměl předpokládá velikost segmentu výsledky.

Doporučené kódování vzor pro většinu scénářů je segmentované výpis, který poskytuje explicitní průběh výpis nebo dotazování a jak službu reagují na každý požadavek. Platí to hlavně o služby nebo aplikace C++ mohou pomoci nižší úrovně ovládací prvek průběh výpis řízení paměti a výkon.

## <a name="greedy-listing"></a>Výpis typu greedy
Starší verze Klientská knihovna pro úložiště pro jazyk C++ (verze 0.5.0 Preview a starší) zahrnuté nesegmentovaný výpis rozhraní API pro tabulky a fronty, jako v následujícím příkladu:

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

Tyto metody byly implementovány jako obálky segmentovaným rozhraní API. Pro každou odpověď segmentovaným výpis kód připojí výsledky na vektor a vrátí všechny výsledky po skenování úplné kontejnery.

Tento přístup může fungovat, pokud účet úložiště nebo tabulka obsahuje malý počet objektů. Však větší počet objektů, požadované paměti může zvýšit bez omezení, protože všechny výsledky zůstává v paměti. Jedné operace výpisu může trvat velmi dlouhou dobu, během které měl volající žádné informace o jejím průběhu.

Tyto typu greedy výpis rozhraní API v sadě SDK neexistují v C#, Java nebo prostředí JavaScript Node.js. Aby se zabránilo potenciálních problémů pomocí těchto typu greedy rozhraní API, jsme je v verzi odebrána 0.6.0 Preview.

Pokud váš kód volá tyto typu greedy rozhraní API:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

Potom upravte kód pro použití na segmentovaným výpis rozhraní API:

```cpp
azure::storage::continuation_token token;
do
{
    azure::storage::table_query_segment segment = table.execute_query_segmented(query, token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
    {
        process_entity(*it);
    }

    token = segment.continuation_token();
} while (!token.empty());
```

Zadáním *max_results* parametr segmentu, můžou vyrovnávat mezi počet požadavků a využití paměti pro splnění důležité informace o výkonu pro vaši aplikaci.

Kromě toho pokud používáte segmentovaným výpis rozhraní API, ale ukládání dat do místního shromažďování ve stylu "chamtivého", také důrazně doporučujeme, aby zrefaktorujete kód pro zpracování, ukládání dat v kolekci místní pečlivě ve velkém měřítku.

## <a name="lazy-listing"></a>Opožděné výpis
I když typu greedy výpis vyvolá potenciální problémy, je vhodné, pokud v kontejneru nejsou k dispozici příliš mnoho objektů.

Pokud používáte také jazyka C# nebo Java SDK Oracle, měli byste se seznámit s vyčíslitelná programovací model, který nabízí opožděné stylu výpis, kde na určité posunu pouze načítání v případě potřeby. V jazyce C++ šablonu na základě iterator také poskytuje podobné přístup.

V typické opožděné seznamu rozhraní API, pomocí **list_blobs** jako příklad vypadá podobně jako tento:

```cpp
list_blob_item_iterator list_blobs() const;
```

Fragment kódu typické, která používá vzor opožděné výpis může vypadat například takto:

```cpp
// List blobs in the blob container
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}
```

Všimněte si, že opožděné výpis je k dispozici pouze v synchronním režimu.

Porovnání s typu greedy výpis, opožděné výpis načte data jenom v případě potřeby. V pozadí ho načte data ze služby Azure Storage jenom v případě, že další iterator posouvá do další segment. Proto s ohraničenou velikost je řízena využití paměti a tuto operaci je rychlé.

Opožděné výpis rozhraní API jsou součástí Klientská knihovna pro úložiště pro jazyk C++ ve verzi 2.2.0.

## <a name="conclusion"></a>Závěr
V tomto článku jsme probrali různých přetížení pro výpis rozhraní API pro různé objekty v Klientská knihovna pro úložiště pro jazyk C++. Shrnutí:

* Rozhraní API pro asynchronní důrazně doporučujeme v části scénářích s více vláken.
* Segmentovaným výpis se doporučuje pro většinu scénářů.
* Opožděné výpis je k dispozici v knihovně jako vhodnou obálku v synchronní scénáře.
* Výpis typu greedy se nedoporučuje a byla odebrána z knihovny.

## <a name="next-steps"></a>Další kroky
Další informace o Azure Storage a klientské knihovny jazyka C++ najdete v následujících materiálech.

* [Používání úložiště Blob z jazyka C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Postup používání úložiště Table z jazyka C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Postup používání úložiště Queue z jazyka C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Azure Klientská knihovna pro úložiště pro dokumentaci k rozhraní API jazyka C++.](http://azure.github.io/azure-storage-cpp/)
* [Blog týmu Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/)
* [Dokumentace k Azure Storage](https://azure.microsoft.com/documentation/services/storage/)

