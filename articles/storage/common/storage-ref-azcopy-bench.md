---
title: azcopyový lavic | Microsoft Docs
description: Tento článek popisuje referenční informace o příkazu AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8570bce87aeea5473b4aadf9bd30bc0a648a6f0f
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518301"
---
# <a name="azcopy-bench"></a>AzCopy

Spustí srovnávací test výkonu odesláním testovacích dat do zadaného cíle. Testovací data se generují automaticky.

Příkaz srovnávacího testu spouští stejný proces nahrávání jako "Copy" s tím rozdílem, že:

  - Neexistuje parametr zdroje.  Příkaz vyžaduje pouze cílovou adresu URL. V aktuální verzi musí tato cílová adresa URL odkazovat na kontejner objektů BLOB.
  
  - Datová část je popsána v parametrech příkazového řádku, který určuje, kolik souborů se automaticky generuje a jak velká jsou. Proces generování probíhá zcela v paměti. Disk se nepoužívá.
  
  - Jsou podporovány pouze některé volitelné parametry, které jsou k dispozici pro příkaz Kopírovat.
  
  - Další diagnostika se měří a nahlásí.
  
  - Ve výchozím nastavení se přenesená data na konci testovacího běhu odstraní.

Režim srovnávacích testů automaticky provede ladění na počet paralelních připojení TCP, která poskytují maximální propustnost. Zobrazí se toto číslo na konci. Pokud chcete zabránit automatickému ladění, nastavte proměnnou prostředí AZCOPY_CONCURRENCY_VALUE na určitý počet připojení.

Podporují se všechny běžné typy ověřování. Nejpohodlnější přístup k srovnávacím testům je ale obvykle vytvoření prázdného kontejneru s tokenem SAS a ověřování pomocí SAS.

## <a name="examples"></a>Příklady

```azcopy
azcopy bench [destination] [flags]
```

Spustit test srovnávacího testu s výchozími parametry (vhodný pro sítě srovnávacích testů až do 1 GB/s):

- AzCopy je "https://[účet]. blob. Core. Windows. NET/[kontejner]? <SAS> "

Spusťte test srovnávacího testu, který nahrává soubory 100, každý 2 GiB velikost: (vhodné pro srovnávací testy v rychlé síti, např. 10 GB/s):

- AzCopy je "https://[účet]. blob. Core. Windows. NET/[kontejner]? <SAS> " --počet souborů: 100--Size-pro soubor 2G

Stejné jako výše, ale použijte soubory 50 000, velikost 8 souborů MiB a jejich výpočetních hodnot hash MD5 (stejným způsobem jako příznak--Put-MD5 to dělá v příkazu copy). Účelem--Put-MD5 při srovnávacích testech je testovat, zda výpočet MD5 ovlivňuje propustnost pro vybraný počet a velikost souborů:

- AzCopy je "https://[účet]. blob. Core. Windows. NET/[kontejner]? <SAS> " --počet souborů: 50000--Size-podle souboru 8 min--Put-MD5

## <a name="options"></a>Možnosti

**--typ BLOB-** String definuje typ objektu BLOB v cíli. Slouží k povolení srovnávacích testů různých typů objektů BLOB. Totožný s parametrem s názvem, který je v příkazu copy (výchozí "Detect").

**--Block-size-MB** float používá tuto velikost bloku (zadanou v souboru MIB). Výchozí hodnota se automaticky vypočítá na základě velikosti souboru. Jsou povoleny desetinné zlomky – např. 0,25. Stejné jako parametr s názvem v příkazu Copy.

**--Delete-test-data**  Pokud má hodnotu true, data srovnávacího testu se odstraní na konci srovnávacího testu.  Nastavte na hodnotu false, pokud chcete zachovat data v cílovém umístění, např. Pokud je chcete použít pro manuální testy mimo režim srovnávacích testů (výchozí hodnota true).

**--počet souborů** uint: počet automaticky generovaných datových souborů, které se mají použít (výchozí 100).

**-h,--help**  Nápovědu pro stolní

**--řetězec na úrovni protokolu** definuje podrobnosti protokolu pro soubor protokolu, dostupné úrovně: informace (všechny požadavky a odpovědi), upozornění (pomalé odezvy), chyby (pouze neúspěšné žádosti) a žádné (žádné protokoly výstupu). (výchozí "informace")

**--Put-MD5**  Vytvořte hodnotu hash MD5 každého souboru a uložte hodnotu hash jako vlastnost content-MD5 cílového objektu BLOB nebo souboru. (Ve výchozím nastavení není hodnota hash vytvořena.) Stejné jako parametr s názvem v příkazu Copy.

**--Size –** velikost řetězce pro každý soubor automaticky generovaného datového souboru. Musí se jednat o číslo ihned následované K, M nebo G. např. 12K nebo 200G (výchozí "250M").

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

**--Cap – Mbps**  Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.

**--výstupní** formát řetězce výstupu příkazu. Mezi možnosti patří: text, JSON. Výchozí hodnota je "text". (výchozí "text").

## <a name="see-also"></a>Další informace najdete v tématech

- [AzCopy](storage-ref-azcopy.md)
