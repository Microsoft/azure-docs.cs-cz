---
title: LogDownloader - kognitivní služby Azure | Microsoft Docs
description: Stáhněte soubory protokolů, které se vytváří služba Azure vlastního rozhodnutí.
services: cognitive-services
author: marco-rossi29
manager: marco-rossi29
ms.service: cognitive-services
ms.topic: article
ms.date: 05/09/2018
ms.author: marossi
ms.openlocfilehash: 783b534b3b3f4bb7f5d9f073f491690759edfea5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343421"
---
# <a name="logdownloader"></a>LogDownloader

Stáhnout soubory protokolů, které se vytváří služba Azure vlastní rozhodnutí a generovat *.gz* soubory, které jsou používány experimenty.

## <a name="prerequisites"></a>Požadavky

- Jazyk Python 3: Nainstalován a na trase. Doporučujeme, abyste 64bitovou verzi pro zpracování velkých souborů.
- *Microsoft/mwt-ds* úložiště: [Naklonujte úložiště](https://github.com/Microsoft/mwt-ds).
- *Azure storage blob* balíčku: pro podrobné informace o instalaci, přejděte na [knihovny pro úložiště Microsoft Azure pro jazyk Python](https://github.com/Azure/azure-storage-python#option-1-via-pypi).
- Zadejte připojovací řetězec úložiště Azure v *mwt-ds/DataScience/ds.config*: postupujte podle *my_app_id: my_connectionString* šablony. Můžete určit více `app_id`. Při spuštění `LogDownloader.py`, pokud vstupní `app_id` nebyl nalezen v `ds.config`, `LogDownloader.py` používá `$Default` připojovací řetězec.

## <a name="usage"></a>Využití

Přejděte na `mwt-ds/DataScience` a spusťte `LogDownloader.py` s relevantní argumenty, jak je podrobně uvedeno v následujícím kódu:

```cmd
python LogDownloader.py [-h] -a APP_ID -l LOG_DIR [-s START_DATE]
                        [-e END_DATE] [-o OVERWRITE_MODE] [--dry_run]
                        [--create_gzip] [--delta_mod_t DELTA_MOD_T]
                        [--verbose] [-v VERSION]
```

### <a name="parameters"></a>Parametry

| Vstup | Popis | Výchozí |
| --- | --- | --- |
| `-h`, `--help` | Zobrazte zprávu nápovědy a ukončení. | |
| `-a APP_ID`, `--app_id APP_ID` | Identifikátor aplikace (to znamená, že úložiště Azure blob název kontejneru). | Požaduje se |
| `-l LOG_DIR`, `--log_dir LOG_DIR` | Základní adresář pro stahování dat (je vytvořena podsložka).  | Požaduje se |
| `-s START_DATE`, `--start_date START_DATE` | Stažení počáteční datum (zahrnout), v *rrrr-MM-DD* formátu. | `None` |
| `-e END_DATE`, `--end_date END_DATE` | Při stahování koncové datum (je součástí balíčku), ve *rrrr-MM-DD* formátu. | `None` |
| `-o OVERWRITE_MODE`, `--overwrite_mode OVERWRITE_MODE` | Režim přepisování používat. | |
| | `0`: Přepsat nikdy; Požádejte uživatele, jestli se aktuálně používá objekty BLOB. | Výchozí | |
| | `1`: Požádejte uživatele, jak postupovat, pokud se soubory mají různé velikosti nebo pokud objekty BLOB jsou právě používány. | |
| | `2`: Přepsat vždy; stažení aktuálně používá objekty BLOB. | |
| | `3`: Nikdy přepsat a připojte, pokud je větší, bez předchozího; stažení aktuálně používá objekty BLOB. | |
| | `4`: Nikdy přepsat a připojte, pokud je větší, bez předchozího; Přeskočit aktuálně používá objekty BLOB. | |
| `--dry_run` | Tisk, které objekty BLOB by byly staženy, aniž byste museli stáhnout. | `False` |
| `--create_gzip` | Vytvoření *gzip* souboru Vowpal k dispozici. | `False` |
| `--delta_mod_t DELTA_MOD_T` | Časový interval v sekundách pro zjišťování, zda soubor je aktuálně používán. | `3600` doba (`1` hodiny) |
| `--verbose` | Tisk více podrobností. | `False` |
| `-v VERSION`, `--version VERSION` | Verze programu protokolu se má použít. | |
| | `1`: Pro nevařené protokoly (pouze pro zpětnou kompatibilitu). | Zastaralé |
| | `2`: Pro vařené protokoly. | Výchozí |

### <a name="examples"></a>Příklady

Ke spuštění suchého stahování všechna data v kontejnerech objektů blob Azure Storage použijte následující kód:
```cmd
python LogDownloader.py -a your_app_id -l d:\data --dry_run
```

Chcete-li stáhnout pouze protokoly vytvořit, protože 1. ledna 2018 s `overwrite_mode=4`, použijte následující kód:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4
```

Chcete-li vytvořit *gzip* souboru slučování všechny stažené soubory, použijte následující kód:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4 --create_gzip
```