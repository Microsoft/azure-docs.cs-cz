---
title: Seznam prostředků služby Azure Storage s klientskou knihovnu pro úložiště pro C++ | Dokumentace Microsoftu
description: Další informace o použití seznamu rozhraní API v Microsoft Azure Storage Client Library pro jazyk C++ k vytvoření výčtu kontejnerů, objektů BLOB, fronty, tabulky a entity.
services: storage
author: dineshmurthy
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: dineshm
ms.subservice: common
ms.openlocfilehash: 51ee5362a01a846378228a42343cc0e2b9f72497
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55470208"
---
# <a name="list-azure-storage-resources-in-c"></a>Seznam prostředků služby Azure Storage v C++
Operace výpisu jsou klíčem k mnoha vývojové scénáře se službou Azure Storage. Tento článek popisuje, jak co nejefektivněji vytvořit výčet objektů ve službě Azure Storage pomocí seznamu rozhraní API poskytovaných v Microsoft Azure Storage Client Library jazyka C++.

> [!NOTE]
> Tento průvodce, zaměřuje Klientská knihovna Azure Storage pro C++ verze 2.x, která je k dispozici prostřednictvím [NuGet](http://www.nuget.org/packages/wastorage) nebo [Githubu](https://github.com/Azure/azure-storage-cpp).
> 
> 

Klientská knihovna pro úložiště poskytuje celou řadu metod na objekty seznamu nebo dotazu ve službě Azure Storage. Tento článek se zabývá následující scénáře:

* Seznam kontejnerů v účtu služby
* Výpis objektů BLOB v kontejneru nebo objektu blob virtuálního adresáře
* Seznam front v účtu služby
* Seznam tabulek v účtu služby
* Dotazování entit v tabulce

Každá z těchto metod je zobrazit díky jiné přetížení pro různé scénáře.

## <a name="asynchronous-versus-synchronous"></a>Asynchronní a synchronní
Protože klientskou knihovnu pro úložiště pro C++ je postavený na [knihovny C++ REST](https://github.com/Microsoft/cpprestsdk), podporujeme ze své podstaty asynchronní operace s použitím [pplx::task](http://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Příklad:

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

Pokud pracujete s více podprocesů aplikace nebo služby, doporučujeme použít asynchronní rozhraní API přímo, bez vytváření vlákna volání rozhraní API, která významně ovlivňuje výkon synchronizace.

## <a name="segmented-listing"></a>Segmentované výpis
Škálování cloudové úložiště vyžaduje segmentovaným výpis. Například můžete mít přes miliónů objektů BLOB v kontejneru objektů blob v Azure nebo přes miliardu entity v tabulce Azure. Ty nejsou teoretické čísla, ale případy použití skutečných uživatelů.

Proto je nepraktické, chcete-li vypsat všechny objekty v jedné odezvě. Místo toho můžete vytvořit seznam objektů pomocí stránkování. Obsahují seznam rozhraní API *segmentované* přetížení.

Odpověď operace segmentovaným seznam obsahuje:

* <i>_segment</i>, který obsahuje sadu výsledků pro jednoho volání rozhraní API pro výpis.
* *continuation_token*, který je předán do příštího volání, pokud chcete získat další stránky výsledků. Pokud neexistují žádné další výsledky vrátit, token pro pokračování není null.

Typické volání k vypsání všech objektů BLOB v kontejneru může například vypadat jako následující fragment kódu. Kód je k dispozici v našich [ukázky](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp):

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
        process_directory(it->as_directory());
    }
}

    token = segment.continuation_token();
}
while (!token.empty());
```

Všimněte si, že počet výsledků vrácených na stránce mohou být řízena parametr *max_results* v přetížení každé rozhraní API, například:

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

Pokud nezadáte *max_results* parametr, výchozí maximální hodnoty až 5000 výsledky se vrátí na jedné stránce.

Všimněte si také, že dotaz využívající službu Azure Table storage může vrátit žádné záznamy nebo méně záznamů než hodnota *max_results* parametr, který jste zadali, i v případě, že token pro pokračování není prázdný. Jedním z důvodů může být, že dotaz nelze dokončit do pěti sekund. Tak dlouho, dokud token pro pokračování není prázdný, dotaz by měl pokračovat a váš kód by neměl předpokládá velikost segmentu výsledky.

Doporučený model kódování pro většinu scénářů je segmentované výpisu, který poskytuje explicitní průběh výpis nebo dotazování a jak se služba reaguje na každý požadavek. Zejména pro C++ aplikace nebo služby může pomoct nižší úrovně ovládacího prvku průběhu výpis výkon a paměť ovládacího prvku.

## <a name="greedy-listing"></a>Výpis greedy
Starší verze klientskou knihovnu pro úložiště pro C++ (verze si verzi 0.5.0 ve verzi Preview a starší) zahrnuté nesegmentovaný výpis rozhraní API pro tabulky a fronty, jako v následujícím příkladu:

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

Tyto metody byly implementovány jako obálky segmentovaným rozhraní API. Pro každou odpověď segmentovaným výpis kód připojí výsledky do vektoru a vrátí všechny výsledky po skenování úplné kontejnery.

Tento přístup může fungovat, pokud účet úložiště nebo tabulka obsahuje malý počet objektů. Však větší počet objektů, paměť požadovanou může zvýšit bez omezení, protože všechny výsledky zůstala v paměti. Jedné operace výpisu může trvat velmi dlouho, během které volající měl žádné informace o jeho průběh.

Tyto greedy výpis rozhraní API v sadě SDK v C#, Java nebo JavaScript Node.js prostředí neexistují. Aby se zabránilo potenciální problémy pomocí těchto rozhraní API greedy, jsme je odebrali ve verzi 0.6.0 Preview.

Pokud váš kód volá tyto greedy rozhraní API:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

Potom byste měli upravit kód Refaktorovat pro použití segmentovaným výpis rozhraní API:

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

Zadáním *max_results* parametr segmentu vyrovnávat mezi počty požadavků a využití paměti podle důležité informace o výkonu pro vaši aplikaci.

Kromě toho pokud používáte segmentovaným výpis rozhraní API, ale ukládání dat v kolekci místní ve stylu "chamtivého", také důrazně doporučujeme Refaktorujte již svůj kód pro zpracování, ukládání dat v kolekci místní pečlivě ve velkém měřítku.

## <a name="lazy-listing"></a>Opožděné výpis
I když greedy výpis vyvolána potenciální problémy, je vhodné, pokud již nejsou moc velký počet objektů v kontejneru.

Pokud také používáte C# nebo sady Oracle Java SDK, měli byste se seznámit s vyčíslitelné programovací model, který nabízí opožděné – styl výpisu, kde data určité posunem je pouze fetched, pokud je vyžadován. V jazyce C++ na základě iterátoru Šablona také nabízí podobný přístup.

Typické opožděné výpis rozhraní API, pomocí **list_blobs** jako příklad vypadá přibližně takto:

```cpp
list_blob_item_iterator list_blobs() const;
```

Typické kódu, která používá vzor opožděné výpis může vypadat takto:

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

Všimněte si, že opožděná výpis je k dispozici pouze v synchronním režimu.

Ve srovnání s metodou greedy výpis, opožděné výpis načte data pouze v případě potřeby. Pod pokličkou načte data ze služby Azure Storage pouze v případě, že další iterátor přesune do dalšího segmentu. Proto je řízen využití paměti a omezená velikost a je rychlá operace.

Opožděné výpis rozhraní API jsou součástí klientskou knihovnu pro úložiště pro C++ ve verzi 2.2.0.

## <a name="conclusion"></a>Závěr
V tomto článku jsme probírali jiné přetížení pro výpis rozhraní API pro různé objekty v klientské knihovně pro úložiště pro jazyk C++. Shrnutí:

* Asynchronní rozhraní API jsou důrazně doporučujeme v části scénářích s více vláken.
* Segmentované výpisu se doporučuje pro většinu scénářů.
* Opožděné výpis je k dispozici v knihovně jako obálka vhodné v synchronní scénáře.
* Greedy výpisu se nedoporučuje a byla odebrána z knihovny.

## <a name="next-steps"></a>Další postup
Další informace o Azure Storage a Klientská knihovna pro C++ naleznete v tématu následující prostředky.

* [Používání úložiště Blob z jazyka C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Používání úložiště Table z jazyka C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Používání úložiště Queue z jazyka C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Azure Klientská knihovna pro úložiště pro dokumentaci k rozhraní API jazyka C++.](http://azure.github.io/azure-storage-cpp/)
* [Blog týmu Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/)
* [Dokumentace k Azure Storage](https://azure.microsoft.com/documentation/services/storage/)

