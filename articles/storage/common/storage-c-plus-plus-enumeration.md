---
title: Seznam prostředků úložiště Azure s klientskou knihovnou C++
description: Zjistěte, jak pomocí výpisu rozhraní API v klientské knihovně úložiště Microsoft Azure pro C++ vytvořit výčet kontejnerů, objektů BLOB, front, tabulek a entit.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/23/2017
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: dineshm
ms.openlocfilehash: 0f9e80aff20c1b2663491f6d6ceb99aaec58230f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74269453"
---
# <a name="list-azure-storage-resources-in-c"></a>Výpis prostředků Azure Storage v C++

Výpis operace jsou klíčem k mnoha scénářů vývoje s Azure Storage. Tento článek popisuje, jak efektivně vytvořit výčet objektů ve službě Azure pomocí výpisu rozhraní API k dispozici v knihovně klienta úložiště Microsoft Azure pro C++.

> [!NOTE]
> Tato příručka se zaměřuje na klientskou knihovnu úložiště Azure pro C++ verze 2.x, která je dostupná přes [NuGet](https://www.nuget.org/packages/wastorage) nebo [GitHub](https://github.com/Azure/azure-storage-cpp).

Klientská knihovna úložiště poskytuje různé metody pro seznam nebo dotaz objektů ve službě Azure Storage. Tento článek řeší následující scénáře:

* Seznam kontejnerů v účtu
* Seznam objektů BLOB v kontejneru nebo virtuálním adresáři objektů blob
* Seznam front v účtu
* Seznam tabulek v účtu
* Dotazovat se entit v tabulce

Každá z těchto metod je zobrazen pomocí různých přetížení pro různé scénáře.

## <a name="asynchronous-versus-synchronous"></a>Asynchronní versus synchronní

Vzhledem k tomu, že klientská knihovna úložiště pro C++ je postavena na knihovně [C++ REST](https://github.com/Microsoft/cpprestsdk), neodmyslitelně podporujeme asynchronní operace pomocí [pplx::task](https://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Například:

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

Synchronní operace zabalit odpovídající asynchronní operace:

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

Pokud pracujete s více aplikací nebo služeb zřetězení, doporučujeme použít asynchronní rozhraní API přímo namísto vytvoření vlákna pro volání synchronizačních rozhraní API, což významně ovlivňuje váš výkon.

## <a name="segmented-listing"></a>Segmentovaný výpis

Škálování cloudového úložiště vyžaduje segmentovaný výpis. Můžete mít například více než milion objektů BLOB v kontejneru objektů blob Azure nebo více než miliardu entit v tabulce Azure. Nejedná se o teoretická čísla, ale o skutečné případy použití zákazníka.

Je proto nepraktické vypsat všechny objekty v jedné odpovědi. Místo toho můžete seznam objektů pomocí stránkování. Každý výpis API má *segmentované* přetížení.

Odpověď pro operaci segmentovaného výpisu zahrnuje:

* *_segment*, který obsahuje sadu výsledků vrácených pro jedno volání do úložiště ROZHRANÍ API výpisu.
* *continuation_token*, který je předán dalšímu hovoru, aby získal další stránku výsledků. Pokud neexistují žádné další výsledky vrátit, token pokračování je null.

Například typické volání seznamu všech objektů BLOB v kontejneru může vypadat jako následující fragment kódu. Kód je k dispozici v našich [vzorcích](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp):

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

Všimněte si, že počet výsledků vrácených na stránce lze řídit parametrem *max_results* v přetížení každého rozhraní API, například:

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

Pokud nezadáte *parametr max_results,* bude na jedné stránce vrácena výchozí maximální hodnota až 5000 výsledků.

Všimněte si také, že dotaz proti úložišti Tabulky Azure může vrátit žádné záznamy nebo méně záznamů, než je hodnota *parametru max_results,* který jste zadali, i v případě, že token pokračování není prázdný. Jedním z důvodů může být, že dotaz nelze dokončit za pět sekund. Tak dlouho, dokud token pokračování není prázdný, dotaz by měl pokračovat a váš kód by neměl předpokládat velikost výsledků segmentu.

Doporučený vzor kódování pro většinu scénářů je segmentovaný výpis, který poskytuje explicitní průběh výpisu nebo dotazování a jak služba reaguje na každý požadavek. Zejména pro aplikace nebo služby jazyka C++ může řízení o průběhu výpisu nižší úrovně pomoci řídit paměť a výkon.

## <a name="greedy-listing"></a>Chamtivý výpis

Dřívější verze klientské knihovny úložiště pro C++ (verze 0.5.0 Preview a starší) zahrnovaly nesegmentovaná seznam ovacích api pro tabulky a fronty, jako v následujícím příkladu:

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

Tyto metody byly implementovány jako obálky segmentovaných api. Pro každou odpověď segmentovaného výpisu kód připojil výsledky k vektoru a vrátil všechny výsledky po naskenování celých kontejnerů.

Tento přístup může fungovat, pokud účet úložiště nebo tabulka obsahuje malý počet objektů. Však s nárůstem počtu objektů, požadovaná paměť může zvýšit bez omezení, protože všechny výsledky zůstaly v paměti. Jedna operace výpisu může trvat velmi dlouho, během níž volající neměl žádné informace o jeho průběhu.

Tyto nenasytné výpis API v sadě SDK neexistují v jazyce C#, Java nebo java node.js prostředí. Abychom se vyhnuli možným problémům s používáním těchto nenasytných api, odebrali jsme je ve verzi 0.6.0 Preview.

Pokud váš kód volá tyto chamtivé API:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

Pak byste měli upravit kód tak, aby používal segmentovaná seznam API:

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

Zadáním *max_results* parametru segmentu můžete vyvážit počet požadavků a využití paměti, aby bylo možné vyhovět aspekty výkonu pro vaši aplikaci.

Navíc pokud používáte segmentované výpis api, ale ukládat data v místní kolekci ve stylu "chamtivý", doporučujeme také refaktorovat kód pro zpracování dat v místní kolekci pečlivě ve velkém měřítku.

## <a name="lazy-listing"></a>Opožděný výpis

Přestože chamtivý výpis vyvolal potenciální problémy, je vhodné, pokud není příliš mnoho objektů v kontejneru.

Pokud používáte také sady C# nebo Oracle Java SDK, měli byste být obeznámeni s programovacím modelem Výčtu, který nabízí výpis ve stylu líný, kde jsou data s určitým posunem načtena pouze v případě, že je požadována. V jazyce C++ šablona založená na iterátoru také poskytuje podobný přístup.

Typické opožděné výpis rozhraní API, pomocí **list_blobs** jako příklad, vypadá takto:

```cpp
list_blob_item_iterator list_blobs() const;
```

Typický fragment kódu, který používá opožděný vzor výpisu, může vypadat takto:

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

Všimněte si, že opožděný výpis je k dispozici pouze v synchronním režimu.

Ve srovnání s nenasytný výpis, opožděný výpis načte data pouze v případě potřeby. Pod kryty načte data z Azure Storage pouze v případě, že další iterátor přesune do dalšího segmentu. Proto využití paměti je řízens ohraničenou velikost a operace je rychlá.

Opožděné výpis API jsou zahrnuty v klientské knihovně úložiště pro C++ ve verzi 2.2.0.

## <a name="conclusion"></a>Závěr

V tomto článku jsme diskutovali o různých přetížení pro výpis API pro různé objekty v klientské knihovně úložiště pro C++ . Shrnutí:

* Asynchronní rozhraní API se důrazně doporučuje ve více scénářích zřetězení.
* Segmentovaný výpis se doporučuje pro většinu scénářů.
* Opožděný výpis je k dispozici v knihovně jako pohodlné obálky v synchronních scénářích.
* Nenasytný výpis se nedoporučuje a byl odstraněn z knihovny.

## <a name="next-steps"></a>Další kroky

Další informace o Azure Storage a klientské knihovny pro C++, najdete v následujících prostředků.

* [Použití úložiště objektů blob z C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Použití úložiště tabulek z jazyka C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Používání úložiště Queue z C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Klientská knihovna azure úložiště pro dokumentaci rozhraní API pro C++.](https://azure.github.io/azure-storage-cpp/)
* [Blog týmu Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/)
* [Dokumentace k Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
