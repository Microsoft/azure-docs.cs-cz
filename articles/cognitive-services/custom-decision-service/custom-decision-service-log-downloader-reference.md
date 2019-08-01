---
title: LogDownloader – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Stažení souborů protokolu vytvořených službou Azure Custom Decision Service.
services: cognitive-services
author: marco-rossi29
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: marossi
ROBOTS: NOINDEX
ms.openlocfilehash: 33cc0d0dcf16ff82ac128507566427e123020236
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707227"
---
# <a name="logdownloader"></a>LogDownloader

Stáhněte soubory protokolu vytvořené službou Azure Custom Decision Service a vygenerujte soubory *. gz* , které se používají v experimentování.

## <a name="prerequisites"></a>Požadavky

- Python 3: Nainstalováno a do vaší cesty. Pro zpracování velkých souborů doporučujeme 64 verzi.
- Úložiště *Microsoft/MWT-DS* : [Naklonujte úložiště](https://github.com/Microsoft/mwt-ds).
- Balíček *Azure-Storage-BLOB* : Podrobnosti o instalaci najdete v [Microsoft Azure Storage knihovně pro Python](https://github.com/Azure/azure-storage-python#option-1-via-pypi).
- Zadejte připojovací řetězec úložiště Azure v *MWT-DS/datavěda/DS. config*: Postupujte podle šablony *my_app_id: my_connectionString* . Můžete zadat více `app_id`. Při `LogDownloader.py`spuštění, pokud se vstup `ds.config` `app_id` nenalezne`LogDownloader.py` v, používá připojovacířetězec.`$Default`

## <a name="usage"></a>Použití

Přejít na `mwt-ds/DataScience` a spustit `LogDownloader.py` s příslušnými argumenty, jak je popsáno v následujícím kódu:

```cmd
python LogDownloader.py [-h] -a APP_ID -l LOG_DIR [-s START_DATE]
                        [-e END_DATE] [-o OVERWRITE_MODE] [--dry_run]
                        [--create_gzip] [--delta_mod_t DELTA_MOD_T]
                        [--verbose] [-v VERSION]
```

### <a name="parameters"></a>Parametry

| Vstup | Popis | Výchozí |
| --- | --- | --- |
| `-h`, `--help` | Zobrazí zprávu o nápovědě a ukončí činnost. | |
| `-a APP_ID`, `--app_id APP_ID` | ID aplikace (tj. Azure Storage název kontejneru objektů BLOB). | Požadováno |
| `-l LOG_DIR`, `--log_dir LOG_DIR` | Základní adresář pro stahování dat (podsložka je vytvořena).  | Požadováno |
| `-s START_DATE`, `--start_date START_DATE` | Počáteční datum stahování (zahrnuté) ve formátu *RRRR-MM-DD* . | `None` |
| `-e END_DATE`, `--end_date END_DATE` | Datum ukončení stahování (zahrnuto) ve formátu *RRRR-MM-DD* . | `None` |
| `-o OVERWRITE_MODE`, `--overwrite_mode OVERWRITE_MODE` | Režim přepsání, který se má použít. | |
| | `0`: Nikdy Nepřepisovat; Požádejte uživatele, aby se objekty BLOB aktuálně používaly. | Výchozí |
| | `1`: Požádejte uživatele, aby postup pokračoval, pokud mají soubory různé velikosti nebo když se objekty BLOB aktuálně používají. | |
| | `2`: Vždycky přepsat; stáhne aktuálně používané objekty blob. | |
| | `3`: Nikdy nepřepište a připojit, pokud je velikost větší, bez výzvy. stáhne aktuálně používané objekty blob. | |
| | `4`: Nikdy nepřepište a připojit, pokud je velikost větší, bez výzvy. Přeskočit aktuálně používané objekty blob | |
| `--dry_run` | Vytiskněte, které objekty BLOB se stáhly bez stažení. | `False` |
| `--create_gzip` | Vytvořte soubor *gzip* pro pro dostupné. | `False` |
| `--delta_mod_t DELTA_MOD_T` | Časový interval v sekundách pro zjištění, zda je soubor aktuálně používán. | `3600`SEC (`1` hodina) |
| `--verbose` | Vytisknout další podrobnosti. | `False` |
| `-v VERSION`, `--version VERSION` | Verze nástroje pro stažení protokolů, která se má použít. | |
| | `1`: Pro neopracované protokoly (pouze z důvodu zpětné kompatibility). | Zastaralé |
| | `2`: Pro vařené protokoly. | Výchozí |

### <a name="examples"></a>Příklady

Pro suchou dobu stahování všech dat v Azure Storage kontejneru objektů BLOB použijte následující kód:
```cmd
python LogDownloader.py -a your_app_id -l d:\data --dry_run
```

Chcete-li stáhnout pouze protokoly vytvořené od 1. ledna `overwrite_mode=4`2018 s, použijte následující kód:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4
```

Chcete-li vytvořit soubor *gzip* spojující všechny stažené soubory, použijte následující kód:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4 --create_gzip
```
