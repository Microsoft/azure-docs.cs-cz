---
title: azcopy bench | Dokumenty společnosti Microsoft
description: Tento článek obsahuje referenční informace pro příkaz azcopy bench.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8570bce87aeea5473b4aadf9bd30bc0a648a6f0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72518301"
---
# <a name="azcopy-bench"></a>azcopy bench

Spustí srovnávací test výkonu nahráním testovacích dat do zadaného cíle. Testovací data jsou generována automaticky.

Příkaz benchmark spustí stejný proces nahrávání jako "copy", s tím rozdílem, že:

  - Neexistuje žádný zdrojový parametr.  Příkaz vyžaduje pouze cílovou adresu URL. V aktuální verzi musí tato cílová adresa URL odkazovat na kontejner objektů blob.
  
  - Datová část je popsána parametry příkazového řádku, které řídí, kolik souborů je automaticky generováno a jak velké jsou. Proces generování probíhá výhradně v paměti. Disk není použit.
  
  - Podporováno je pouze několik volitelných parametrů, které jsou k dispozici pro příkaz kopírování.
  
  - Další diagnostika se měří a vykazuje.
  
  - Ve výchozím nastavení jsou přenesená data odstraněna na konci testovacího běhu.

Režim benchmarku se automaticky vyladí na počet paralelních připojení TCP, který poskytuje maximální propustnost. Zobrazí se toto číslo na konci. Chcete-li zabránit automatickému ladění, nastavte proměnnou prostředí AZCOPY_CONCURRENCY_VALUE na určitý počet připojení.

Podporovány jsou všechny obvyklé typy ověřování. Nejpohodlnější mandatář pro benchmarking je však obvykle vytvořit prázdný kontejner s tokenem SAS a použít ověřování SAS.

## <a name="examples"></a>Příklady

```azcopy
azcopy bench [destination] [flags]
```

Spusťte srovnávací test s výchozími parametry (vhodné pro srovnávací sítě do 1 Gb/s):"

- azcopy bench "https://[account].blob.core.windows.net/[container]? <SAS>"

Spusťte srovnávací test, který nahraje 100 souborů, z nichž každý má velikost 2 Gb: (vhodné pro benchmarking v rychlé síti, např.

- azcopy bench "https://[account].blob.core.windows.net/[container]? <SAS>" --počet souborů 100 --velikost na soubor 2G

Stejné jako výše, ale použijte 50 000 souborů, každý 8 MiB ve velikosti a vypočítat jejich MD5 hash (stejným způsobem, že --put-md5 příznak to v příkazu copy). Účelem --put-md5 při benchmarkingu je otestovat, zda výpočty MD5 ovlivňují propustnost pro vybraný počet a velikost souboru:

- azcopy bench "https://[account].blob.core.windows.net/[container]? <SAS>" --počet souborů 50000 --velikost na soubor 8M --put-md5

## <a name="options"></a>Možnosti

**--řetězec typu objektu blob** Definuje typ objektu blob v cílovém umístění. Používá se k povolení srovnávání různých typů objektů blob. Shodné s parametrem se stejným názvem v příkazu copy (výchozí "Rozpoznat").

**--blok-size-mb** float Použijte tuto velikost bloku (zadaná v MiB). Výchozí nastavení se automaticky vypočítá na základě velikosti souboru. Desetinné frakce jsou povoleny - např. Shodné s parametrem se stejným názvem v příkazu copy.

**--delete-test-data --delete-test-data --delete-test-data --delete**  Pokud je hodnota true, budou údaje o referenčníhodnotě na konci spuštění srovnávacího testu odstraněny.  Nastavte ji na hodnotu false, pokud chcete data uchovávat v cílovém umístění – například je použít pro ruční testy mimo režim srovnávacího testu (výchozí hodnota true).

**--počet souborů** uint Počet automaticky generovaných datových souborů k použití (výchozí 100).

**-h, --pomoc**  Pomoc pro lavičku

**--řetězec na úrovni protokolu** Definujte podrobnost protokolu pro soubor protokolu, dostupné úrovně: INFO(všechny požadavky/odpovědi), UPOZORNĚNÍ(pomalé odpovědi), ERROR(pouze neúspěšné požadavky) a NONE(žádné výstupní protokoly). (výchozí "INFO")

**--put-md5**  Vytvořte hash MD5 každého souboru a uložte hash jako Content-MD5 vlastnost cílového objektu blob/souboru. (Ve výchozím nastavení není hodnota hash vytvořena.) Shodné s parametrem se stejným názvem v příkazu copy.

**--velikost na řetězec souboru** Velikost každého automaticky generovaného datového souboru. Musí být číslo bezprostředně následované K, M nebo G. E.G. 12k nebo 200G (výchozí "250M").

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

**--cap-mbps uint32**  Završuje přenosovou rychlost v megabitech za sekundu. Propustnost se může mírně lišit od víčka. Pokud je tato možnost nastavena na nulu nebo je vynechána, propustnost není omezena.

**--řetězec typu výstupu** Formát výstupu příkazu. Volby zahrnují: text, json. Výchozí hodnota je text. (výchozí "text").

## <a name="see-also"></a>Viz také

- [azkopie](storage-ref-azcopy.md)
