---
title: azcopyový lavic | Microsoft Docs
description: Tento článek popisuje referenční informace o příkazu AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c1028d0a4a458746c08fd6fa4f16aa952d9962a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87282003"
---
# <a name="azcopy-benchmark"></a>srovnávací AzCopy

Spustí srovnávací test výkonu odesláním nebo stažením testovacích dat do nebo ze zadaného cíle. Pro nahrání se data testu generují automaticky.

Příkaz srovnávacího testu spouští stejný proces jako "Copy" s tím rozdílem, že: 

  - Místo toho, aby vyžadovaly zdrojové i cílové parametry, srovnávací test používá pouze jeden. Toto je kontejner objektů blob, sdílená složka Azure nebo Azure Data Lake Storage Gen2 systém souborů, ze kterého chcete nahrávat nebo stahovat.

  - Parametr Mode popisuje, zda má AzCopy testovat nahrávání nebo stahování z daného cíle. Platné hodnoty jsou upload a download. Výchozí hodnota je upload (Odeslat).

  - V případě srovnávacích testů se datová část popisuje parametry příkazového řádku, které určují, kolik souborů se generuje automaticky a jak významné jsou soubory. Proces generování probíhá zcela v paměti. Disk se nepoužívá.

  - V případě souborů ke stažení se datová část skládá z libovolných souborů, které již existují ve zdroji. (Další informace o tom, jak v případě potřeby generovat soubory testů), najdete v níže uvedeném příkladu.
  
  - Jsou podporovány pouze některé volitelné parametry, které jsou k dispozici pro příkaz Kopírovat.
  
  - Další diagnostika se měří a nahlásí.
  
  - Pro nahrávání je výchozím chováním odstranění přenesených dat na konci testovacího běhu.  V případě souborů ke stažení se data nikdy neukládají místně.

Režim srovnávacích testů automaticky provede ladění na počet paralelních připojení TCP, která poskytují maximální propustnost. Zobrazí se toto číslo na konci. Pokud chcete zabránit automatickému ladění, nastavte proměnnou prostředí AZCOPY_CONCURRENCY_VALUE na určitý počet připojení. 

Podporují se všechny běžné typy ověřování. Nejpohodlnější přístup k nahrávání srovnávacích testů je ale obvykle vytvoření prázdného kontejneru s tokenem SAS a ověřováním pomocí SAS. (Režim stahování vyžaduje, aby byla v cílovém kontejneru přítomna sada testovacích dat.)

## <a name="examples"></a>Příklady

```azcopy
azcopy benchmark [destination] [flags]
```

Spustit test srovnávacího testu s výchozími parametry (vhodný pro sítě srovnávacích testů až do 1 GB/s):

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"
```
Spusťte test srovnávacího testu, který nahrává soubory 100, každý 2 GiB velikost: (vhodné pro srovnávací testy v rychlé síti, například 10 GB/s):

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"--file-count 100 --size-per-file 2G
```
Spusťte test srovnávacího testu, ale použijte soubory 50 000, velikost 8 souborů MiB a jejich výpočetních hodnot hash MD5 (stejným způsobem jako `--put-md5` příznak v příkazu Kopírovat). Účelem `--put-md5` při srovnávacím testu je testování, zda výpočet MD5 ovlivňuje propustnost pro vybraný počet a velikost souborů:

```azcopy
azcopy bench --mode='Upload' "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 50000 --size-per-file 8M --put-md5
```

Spustit test srovnávacího testu, který stáhne existující soubory z cíle

```azcopy
azcopy bench --mode='Download' "https://[account].blob.core.windows.net/[container]?<SAS?"
```

Spusťte nahrávání, které neodstraní přenesené soubory. (Tyto soubory pak můžou sloužit jako datová část pro test stahování.)

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 100 --delete-test-data=false
```

## <a name="options"></a>Možnosti

**--typ BLOB-** String definuje typ objektu BLOB v cíli. Slouží k povolení srovnávacích testů různých typů objektů BLOB. Totožný s parametrem s názvem, který je v příkazu copy (výchozí "Detect").

**--Block-size-MB** float používá tuto velikost bloku (zadanou v souboru MIB). Výchozí hodnota se automaticky vypočítá na základě velikosti souboru. Jsou povoleny desetinné zlomky, například 0,25. Stejné jako parametr s názvem v příkazu Copy.

**--check-Length**  Zkontroluje délku souboru v cíli po přenosu. Pokud dojde ke neshodě mezi zdrojem a cílem, je přenos označený jako neúspěšný. (výchozí hodnota true)

**--Delete-test-data**  Pokud má hodnotu true, data srovnávacího testu se odstraní na konci srovnávacího testu.  Nastavte na hodnotu false, pokud chcete zachovat data v cíli – například pro ruční testy mimo režim srovnávacích testů (výchozí hodnota true).

**--počet souborů** uint.  Počet automaticky vygenerovaných datových souborů, které se mají použít (výchozí 100).

**--help**  Nápovědu pro stolní

**--řetězec na úrovni protokolu** definuje podrobnosti protokolu pro soubor protokolu, dostupné úrovně: informace (všechny požadavky a odpovědi), upozornění (pomalé odezvy), chyby (pouze neúspěšné žádosti) a žádné (žádné protokoly výstupu). (výchozí "informace")

**--Mode** řetězec definuje, jestli má AzCopy testovat nahrávání nebo stahování z tohoto cíle. Platné hodnoty jsou upload a download. Výchozí možnost je upload (Odeslat). (výchozí odeslání)

**--Number-of-Folders** uint Pokud je větší než 0, vytvořte složky pro rozdělení dat.

**--Put-MD5**  Vytvořte hodnotu hash MD5 každého souboru a uložte hodnotu hash jako vlastnost content-MD5 cílového objektu BLOB nebo souboru. (Ve výchozím nastavení není hodnota hash vytvořena.) Stejné jako parametr s názvem v příkazu Copy.

**--Size –** velikost řetězce pro každý soubor automaticky generovaného datového souboru. Musí se jednat o číslo bezprostředně následované K, M nebo G. např. 12K nebo 200G (výchozí "250M").

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

**--Cap – MB/s float**  Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.

**--výstupní** formát řetězce výstupu příkazu. Mezi možnosti patří: text, JSON. Výchozí hodnota je "text". (výchozí "text").

**--Trusted – řetězec Microsoft-přípony** Určuje další přípony domén, kde se můžou odesílat přihlašovací tokeny Azure Active Directory.  Výchozí hodnota je *. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Zde uvedené jsou přidány do výchozího nastavení. Z důvodu zabezpečení byste měli sem umístit jenom Microsoft Azure domény. Více položek oddělte středníkem.


## <a name="see-also"></a>Viz také

- [AzCopy](storage-ref-azcopy.md)
