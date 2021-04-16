---
title: Optimalizujte výkon AzCopy v10 za účelem s Azure Storage | Microsoft Docs
description: Tento článek vám pomůže optimalizovat výkon AzCopy v10 za účelem s Azure Storage.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: b60fc4b1fc20c455c2c409f544a8af16f1dbf8d1
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509056"
---
# <a name="optimize-the-performance-of-azcopy-with-azure-storage"></a>Optimalizujte výkon AzCopy pomocí Azure Storage

AzCopy je nástroj příkazového řádku, pomocí kterého můžete kopírovat objekty blob nebo soubory do nebo z účtu úložiště. Tento článek vám pomůže s optimalizací výkonu.

> [!NOTE]
> Pokud hledáte obsah, který vám pomůžete začít s AzCopy, přečtěte si téma Začínáme [s AzCopy](storage-use-azcopy-v10.md) .

Můžete použít srovnávací testy výkonu a pak pomocí příkazů a proměnných prostředí najít optimální kompromisy mezi výkonem a spotřebou prostředků.

## <a name="run-benchmark-tests"></a>Spustit testy srovnávacích testů

Test srovnávacího testu výkonu můžete spustit pro konkrétní kontejnery objektů BLOB nebo sdílené složky pro zobrazení obecných statistik výkonu a k identifikaci kritických bodů výkonu. Test můžete spustit nahráním nebo stažením vygenerovaných testovacích dat. 

Pomocí následujícího příkazu spusťte test srovnávacího testu výkonu.

**Syntax**

`azcopy benchmark 'https://<storage-account-name>.blob.core.windows.net/<container-name>'`

**Příklad**

```azcopy
azcopy benchmark 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

> [!TIP]
> Tento příklad uzavře argumenty cesty s jednoduchými uvozovkami (' '). Použijte jednoduché uvozovky ve všech příkazových prostředích s výjimkou příkazového prostředí systému Windows (cmd.exe). Pokud používáte příkazové prostředí systému Windows (cmd.exe), uzavřete argumenty cesty pomocí dvojitých uvozovek ("") místo jednoduchých uvozovek (' ').

Tento příkaz spustí srovnávací test výkonu odesláním testovacích dat do zadaného cíle. Testovací data jsou generována v paměti, odeslána do cíle a poté po dokončení testu odstraněna z cílového umístění. Můžete určit, kolik souborů se má vygenerovat a jakou velikost byste chtěli použít při použití volitelných parametrů příkazu.

Pokud dáváte přednost spuštění tohoto testu stažením dat, nastavte `mode` parametr na `download` . Podrobné referenční dokumentace najdete v tématu [AzCopy Testing](storage-ref-azcopy-bench.md). 

## <a name="optimize-for-large-numbers-of-small-files"></a>Optimalizovat pro velký počet malých souborů

Při přenosu malých souborů se může propustnost snížit, zejména při přenosu velkých čísel. Pro maximalizaci výkonu zmenšete velikost každé úlohy. Pro operace stažení a nahrání zvyšte možnost souběžnosti, snižte aktivitu protokolu a vypněte funkce, které účtují vysoké náklady na výkon.

#### <a name="reduce-the-size-of-each-job"></a>Zmenšení velikosti každé úlohy

Chcete-li dosáhnout optimálního výkonu, zajistěte, aby každá úloha přenáší méně než 10 000 000 souborů. Úlohy, které přenášejí více než 50 000 000 souborů, můžou být špatně fungovat, protože mechanismus sledování úloh AzCopy vzniká značnému množství režie. Pokud chcete snížit režijní náklady, zvažte rozdělení velkých úloh na menší. 

Jedním ze způsobů, jak zmenšit velikost úlohy, je omezit počet souborů ovlivněných úlohou. K tomu můžete použít parametry příkazu. Úloha může například zkopírovat pouze podmnožinu adresářů pomocí `include path` parametru jako součást příkazu [AzCopy Copy](storage-ref-azcopy-copy.md) . 

Použijte `include-pattern` parametr ke zkopírování souborů, které mají konkrétní příponu (například: `*.pdf` ). V samostatné úloze použijte `exclude-pattern` parametr ke zkopírování všech souborů, které nemají `*.pdf` příponu. Příklady najdete v tématu [nahrání specifických souborů](storage-use-azcopy-blobs-upload.md#upload-specific-files) a [stažení specifických objektů BLOB](storage-use-azcopy-blobs-download.md#download-specific-blobs) .

Až se rozhodnete, jak rozdělit velké úlohy na menší, zvažte spouštění úloh ve více než jednom virtuálním počítači.

#### <a name="increase-concurrency"></a>Zvýšit souběžnost

Pokud odesíláte nebo stahujete soubory, použijte `AZCOPY_CONCURRENCY_VALUE` proměnnou prostředí a zvyšte počet souběžných požadavků, které se můžou na vašem počítači vyskytnout. Nastavte tuto proměnnou co nejvyšší, aniž by to ohrozilo výkon počítače. Další informace o této proměnné naleznete v části [zvýšení počtu souběžných požadavků](#increase-the-number-of-concurrent-requests) v tomto článku.

Pokud provádíte kopírování objektů BLOB mezi účty úložiště, zvažte nastavení hodnoty `AZCOPY_CONCURRENCY_VALUE` proměnné prostředí na hodnotu větší než `1000` . Tuto proměnnou můžete nastavit na vysoké úrovni, protože AzCopy používá rozhraní API pro servery, takže se data zkopírují přímo mezi úložnými servery a nepoužívají výpočetní výkon vašeho počítače.  

#### <a name="decrease-the-number-of-logs-generated"></a>Snížit počet generovaných protokolů

Můžete zvýšit výkon snížením počtu položek protokolu, které AzCopy vytvoří při dokončení operace. Služba AzCopy ve výchozím nastavení protokoluje všechny aktivity související s operací. Chcete-li dosáhnout optimálního výkonu, zvažte nastavení `log-level` parametru příkazu Kopírovat, synchronizovat nebo odebrat na `ERROR` . Tímto způsobem AzCopy protokoluje pouze chyby. Ve výchozím nastavení je úroveň protokolu hodnota nastavena na `INFO` . 

#### <a name="turn-off-length-checking"></a>Vypnout kontrolu délky 

Pokud odesíláte nebo stahujete soubory, zvažte nastavení `--check-length` příkazů pro kopírování a synchronizaci na `false` . Tím zabráníte AzCopy ověření délky souboru po přenosu. Ve výchozím nastavení AzCopy zkontroluje délku, aby se zajistilo, že se zdrojové a cílové soubory po dokončení přenosu shodují. AzCopy provádí tuto kontrolu po přenosu každého souboru. Tato kontrolu může snížit výkon, když úlohy přenášejí velké počty malých souborů. 

#### <a name="turn-on-concurrent-local-scanning-linux"></a>Zapnout souběžnou místní kontrolu (Linux)

Kontroly souborů v některých systémech Linux se nespouštějí rychle, aby se nasycení všechna paralelní síťová připojení. V těchto případech můžete nastavit na `AZCOPY_CONCURRENT_SCAN` `true` . 

## <a name="increase-the-number-of-concurrent-requests"></a>Zvýšit počet souběžných požadavků

Propustnost můžete zvýšit nastavením `AZCOPY_CONCURRENCY_VALUE` proměnné prostředí. Tato proměnná určuje povolený počet souběžných požadavků.  

Pokud má počítač méně než 5 procesorů, pak je hodnota této proměnné nastavena na `32` . Jinak se výchozí hodnota rovná 16násobku počtu CPU. Maximální výchozí hodnota této proměnné je `3000` , ale tuto hodnotu můžete nastavit ručně nebo dolů. 

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **macOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

`azcopy env`Pro kontrolu aktuální hodnoty této proměnné použijte. Pokud je hodnota prázdná, můžete si přečíst, která hodnota se používá, a to na začátku libovolného souboru protokolu AzCopy. Je zde uvedena vybraná hodnota a důvod, proč byla vybrána.

Před nastavením této proměnné doporučujeme spustit test testu výkonnosti. Proces testování srovnávacích testů bude hlásit doporučenou hodnotu souběžnosti. Případně platí, že pokud se síťové podmínky a datové části liší, nastavte tuto proměnnou na slovo `AUTO` místo na konkrétní číslo. To způsobí, že AzCopy vždy spustí stejný proces automatického ladění, který používá testy srovnávacích testů.

## <a name="limit-the-throughput-data-rate"></a>Omezení rychlosti propustnosti dat

Pomocí `cap-mbps` příznaku v příkazech můžete umístit strop pro míru propustnosti dat. Například následující příkaz obnoví úlohu a nestejnou propustnost na `10` megabity (MB) za sekundu. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

## <a name="optimize-memory-use"></a>Optimalizace využití paměti

Nastavte `AZCOPY_BUFFER_GB` proměnnou prostředí tak, aby určovala maximální velikost systémové paměti, kterou má AzCopy použít k ukládání do vyrovnávací paměti při stahování a nahrávání souborů. Vyjádřete tuto hodnotu v gigabajtech (GB).

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **macOS** | `export AZCOPY_BUFFER_GB=<value>` |

> [!NOTE]
> Sledování úloh vždy způsobí další režii při využití paměti. Velikost se liší v závislosti na počtu přenosů v rámci úlohy. Vyrovnávací paměti představují největší součást využití paměti. K řízení režie můžete využít `AZCOPY_BUFFER_GB` přibližně splnění vašich požadavků, není ale k dispozici žádný příznak pro striktní využití paměti.

## <a name="optimize-file-synchronization"></a>Optimalizovat synchronizaci souborů

Příkaz [synchronizovat](storage-ref-azcopy-sync.md) identifikuje všechny soubory v cílovém umístění a pak porovná názvy souborů a poslední změněná časová razítka před zahájením operace synchronizace. Pokud máte velký počet souborů, můžete zvýšit výkon tím, že tento proces předem vynecháte. 

Pokud to chcete provést, použijte místo toho příkaz pro [kopírování AzCopy](storage-ref-azcopy-copy.md) a nastavte `--overwrite` příznak na `ifSourceNewer` . AzCopy bude porovnávat soubory při jejich kopírování bez provedení jakýchkoli kontrol a porovnání předem. V případech, kdy je k dispozici velký počet souborů k porovnání, poskytujeme hraniční výkon.

Příkaz [AzCopy Copy](storage-ref-azcopy-copy.md) neodstraní soubory z cílového umístění, takže pokud chcete odstranit soubory v cílovém umístění, když už na zdroji neexistují, použijte příkaz [AzCopy Sync](storage-ref-azcopy-sync.md) s `--delete-destination` příznakem nastaveným na hodnotu `true` nebo `prompt` .

## <a name="see-also"></a>Viz také

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)