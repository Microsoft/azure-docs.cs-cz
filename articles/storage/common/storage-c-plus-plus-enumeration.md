---
title: Vypsat Azure Storage prostředky pomocí klientské knihovny C++
description: Naučte se používat rozhraní API pro výpisy v Microsoft Azure Storage klientské knihovně pro C++ k zobrazení výčtu kontejnerů, objektů blob, front, tabulek a entit.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/23/2017
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.reviewer: dineshm
ms.openlocfilehash: b9ae42bb29d1273e4f0f9c25803e8cc97b56ad95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89462407"
---
# <a name="list-azure-storage-resources-in-c"></a>Výpis prostředků Azure Storage v C++

Výpis operací je klíč k mnoha vývojářským scénářům pomocí Azure Storage. Tento článek popisuje, jak efektivně vytvořit výčet objektů v Azure Storage pomocí rozhraní API pro výpisy poskytované v Microsoft Azure Storage klientské knihovně pro C++.

> [!NOTE]
> Tato příručka cílí na Azure Storage klientskou knihovnu pro C++ verze 2. x, která je k dispozici prostřednictvím [NuGet](https://www.nuget.org/packages/wastorage) nebo [GitHubu](https://github.com/Azure/azure-storage-cpp).

Klientská knihovna pro úložiště poskytuje celou řadu metod pro výpis a dotazování objektů v Azure Storage. Tento článek se zabývá následujícími scénáři:

* Výpis kontejnerů v účtu
* Vypsat objekty BLOB v kontejneru nebo ve virtuálním adresáři objektů BLOB
* Výpis front v účtu
* Výpis tabulek v účtu
* Dotazování entit v tabulce

Každá z těchto metod je zobrazena pomocí různých přetížení pro různé scénáře.

## <a name="asynchronous-versus-synchronous"></a>Asynchronní versus synchronní

Vzhledem k tomu, že klientská knihovna pro úložiště pro C++ je postavená na [knihovně REST jazyka c++](https://github.com/Microsoft/cpprestsdk), podstatně podporujeme asynchronní operace pomocí aplikace [PPLX:: Task](https://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Například:

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

Synchronní operace zabalí odpovídající asynchronní operace:

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

Pokud pracujete s více aplikacemi nebo službami vláken, doporučujeme použít asynchronní rozhraní API přímo místo vytvoření vlákna pro volání rozhraní API pro synchronizaci, což významně ovlivní váš výkon.

## <a name="segmented-listing"></a>Segmentované výpisy

Škálování cloudového úložiště vyžaduje segmentované výpisy. Můžete mít například více než milion objektů BLOB v kontejneru objektů blob Azure nebo víc než miliarda entit v tabulce Azure. Nejedná se o teoretická čísla, ale reálné případy využití zákazníků.

Proto je nepraktické zobrazit seznam všech objektů v jediné odpovědi. Místo toho můžete vypsat objekty pomocí stránkování. Každé rozhraní API pro výpisy má *segmentované* přetížení.

Odpověď pro segmentované operace výpisu zahrnuje:

* *_segment*, která obsahuje sadu výsledků vrácených pro jedno volání rozhraní API výpisu.
* *continuation_token*, která je předána dalšímu volání, aby se zobrazila další stránka výsledků. Pokud neexistují žádné další výsledky k vrácení, token pokračování má hodnotu null.

Například typické volání pro výpis všech objektů BLOB v kontejneru může vypadat jako následující fragment kódu. Kód je k dispozici v našich [ukázkách](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted.cpp):

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

Všimněte si, že počet výsledků vrácených na stránce může být řízen parametrem *max_results* v přetížení každého rozhraní API, například:

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

Pokud nezadáte parametr *max_results* , vrátí se výchozí maximální hodnota až 5000 výsledků na jednu stránku.

Všimněte si také, že dotaz na službu Azure Table Storage nemůže vracet žádné záznamy nebo méně záznamů než hodnota parametru *max_results* , který jste zadali, a to i v případě, že token pro pokračování není prázdný. Jedním z důvodů může být, že dotaz nelze dokončit za pět sekund. Pokud token pro pokračování není prázdný, dotaz by měl pokračovat a váš kód by neměl předpokládat velikost výsledků segmentů.

Doporučený vzor kódování pro většinu scénářů je segmentující výpis, který poskytuje explicitní průběh výpisu nebo dotazování a způsob, jakým služba reaguje na jednotlivé požadavky. Zejména pro aplikace nebo služby v jazyce C++ může být řízení průběhu výpisu na nižší úrovni při řízení paměti a výkonu.

## <a name="greedy-listing"></a>Hladový výpis

Starší verze klientské knihovny pro úložiště pro C++ (verze 0.5.0 Preview a starší) zahrnovaly nesegmentované rozhraní API pro tabulky a fronty, jako v následujícím příkladu:

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

Tyto metody byly implementovány jako obálky segmentovaná rozhraní API. Pro každou odpověď segmentované výpisu kód přidal výsledky do vektoru a vrátil všechny výsledky po prohledání úplných kontejnerů.

Tento přístup může fungovat, když účet úložiště nebo tabulka obsahuje malý počet objektů. U zvýšení počtu objektů se ale požadovaná paměť může zvýšit bez omezení, protože všechny výsledky zůstaly v paměti. Jedna operace výpisu může trvat velmi dlouhou dobu, během které volající nedostal žádné informace o jeho průběhu.

Tato hladce vypisující rozhraní API v sadě SDK neexistují v jazyce C#, Java nebo JavaScript Node.js prostředí. Abyste se vyhnuli potenciálním problémům s používáním těchto hladových rozhraní API, odebrali jsme je ve verzi Preview 0.6.0 Preview.

Pokud váš kód volá tato hladce rozhraní API:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

Pak byste měli kód upravit tak, aby používal rozhraní API segmentovaná v seznamu:

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

Zadáním parametru *max_results* segmentu můžete vyrovnávat počet požadavků a využití paměti, aby splňovala požadavky na výkon vaší aplikace.

Pokud navíc používáte segmentovaná rozhraní API, ale ukládáte data do místní kolekce ve stylu "hladce", důrazně doporučujeme, abyste si kód rehlásili do místní kolekce pečlivě v rozsahu.

## <a name="lazy-listing"></a>Opožděný výpis

Přestože hladový výpis vyvolal potenciální problémy, je vhodné, pokud v kontejneru není příliš mnoho objektů.

Pokud používáte také jazyky C# nebo Oracle Java SDK, měli byste být obeznámeni se Vyčíslitelném programovacím modelem, který nabízí seznam opožděného použití, kde data na určitém posunu jsou načtena pouze v případě, že jsou požadována. V jazyce C++ poskytuje šablona založená na iterátoru také podobný přístup.

Typické rozhraní API opožděného výpisu, které používá **list_blobs** jako příklad, vypadá takto:

```cpp
list_blob_item_iterator list_blobs() const;
```

Typický fragment kódu, který používá model opožděného výpisu, může vypadat takto:

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

Upozorňujeme, že opožděný výpis je k dispozici pouze v synchronním režimu.

V porovnání se hladovým seznamem načte opožděné výpisy dat pouze v případě potřeby. V rámci pokrývání načte data z Azure Storage pouze v případě, že se další iterátor přesune do dalšího segmentu. Proto je využití paměti řízeno pomocí ohraničené velikosti a operace je rychlá.

Rozhraní API pro opožděné výpisy jsou součástí knihovny klienta úložiště pro C++ ve verzi 2.2.0.

## <a name="conclusion"></a>Závěr

V tomto článku jsme probrali různá přetížení pro výpisy rozhraní API pro různé objekty v klientské knihovně pro úložiště pro C++. Shrnutí:

* Asynchronní rozhraní API se ve scénářích s více vlákny důrazně doporučuje.
* Segmentace je doporučena pro většinu scénářů.
* Opožděný výpis je v knihovně k dispozici jako vhodná obálka v synchronních scénářích.
* Hladový výpis není doporučen a byl odebrán z knihovny.

## <a name="next-steps"></a>Další kroky

Další informace o Azure Storage a klientské knihovně pro C++ najdete v následujících zdrojích informací.

* [Použití Blob Storage z C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Použití Table Storage z C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Používání úložiště Queue z C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Dokumentace klientské knihovny Azure Storage pro C++ API.](https://azure.github.io/azure-storage-cpp/)
* [Blog týmu Azure Storage](https://docs.microsoft.com/archive/blogs/windowsazurestorage/)
* [Dokumentace k Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
