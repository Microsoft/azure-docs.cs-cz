---
title: LogDownloader – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Stáhněte si soubory protokolů, které jsou vytvořené pomocí služby Azure Custom Decision Service.
services: cognitive-services
author: marco-rossi29
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: marossi
ms.openlocfilehash: c03278c0d0faced20f4e02fcc1f61531c88ae141
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869666"
---
# <a name="logdownloader"></a>LogDownloader

Stáhněte si soubory protokolů, které jsou vytvořené pomocí služby Azure Custom Decision Service a vygenerujte *.gz* soubory, které používají služby experimentování ve službě.

## <a name="prerequisites"></a>Požadavky

- Python 3: Nainstalovaný a na vaší cestě. Doporučujeme, abyste 64bitovou verzi pro zpracování velkých souborů.
- *Microsoft/mwt-ds* úložiště: [Naklonujte úložiště](https://github.com/Microsoft/mwt-ds).
- *Azure storage blob* balíčku: Pro podrobné informace o instalaci, přejděte na [knihovna Microsoft Azure Storage pro Python](https://github.com/Azure/azure-storage-python#option-1-via-pypi).
- Zadejte svůj připojovací řetězec služby Azure storage v *mwt-ds/DataScience/ds.config*: Postupujte podle *my_app_id: my_connectionString* šablony. Lze zadat více `app_id`. Při spuštění `LogDownloader.py`, pokud vstupní `app_id` nebyl nalezen v `ds.config`, `LogDownloader.py` používá `$Default` připojovací řetězec.

## <a name="usage"></a>Využití

Přejděte na `mwt-ds/DataScience` a spusťte `LogDownloader.py` s příslušnými argumenty, jak je uvedeno v následujícím kódu:

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
| `-a APP_ID`, `--app_id APP_ID` | ID aplikace (to znamená, Azure blob název kontejneru úložiště). | Požaduje se |
| `-l LOG_DIR`, `--log_dir LOG_DIR` | Základní adresář pro stahování dat (podsložku se vytvoří).  | Požaduje se |
| `-s START_DATE`, `--start_date START_DATE` | Stažení počátečního data (zahrnout), v *rrrr-MM-DD* formátu. | `None` |
| `-e END_DATE`, `--end_date END_DATE` | Stahování koncové datum (je součástí), v *rrrr-MM-DD* formátu. | `None` |
| `-o OVERWRITE_MODE`, `--overwrite_mode OVERWRITE_MODE` | Režim přepisu, který chcete použít. | |
| | `0`: Nikdy přepsat; Požádejte uživatele, zda objekty BLOB se aktuálně používají. | Výchozí | |
| | `1`: Požádejte uživatele, jak pokračovat dál, pokud se soubory mají různé velikosti nebo pokud objekty BLOB se aktuálně používá. | |
| | `2`: Vždy přepsat; aktuálně používané objekty BLOB můžete stáhněte. | |
| | `3`: Nikdy přepsat a přidat, pokud je větší, aniž by požádal; aktuálně používané objekty BLOB můžete stáhněte. | |
| | `4`: Nikdy přepsat a přidat, pokud je větší, aniž by požádal; přeskočte aktuálně používané objekty BLOB. | |
| `--dry_run` | Tisk, které objekty BLOB by byly staženy, bez stažení. | `False` |
| `--create_gzip` | Vytvoření *gzip* souboru Vowpal Wabbit. | `False` |
| `--delta_mod_t DELTA_MOD_T` | Časový interval v sekundách pro zjištění, zda soubor je aktuálně používán. | `3600` doby (`1` hodiny) |
| `--verbose` | Tisk více podrobností. | `False` |
| `-v VERSION`, `--version VERSION` | Verze pro stahování protokolů se má použít. | |
| | `1`: Pro nevařené protokoly (pouze z důvodu zpětné kompatibility). | Zastaralé |
| | `2`: Vařené protokolů. | Výchozí |

### <a name="examples"></a>Příklady

Spuštění zkušební stažením všechna data v kontejnerech objektů blob Azure Storage použijte následující kód:
```cmd
python LogDownloader.py -a your_app_id -l d:\data --dry_run
```

Chcete-li stáhnout pouze protokoly, které byly vytvořeny od 1. ledna 2018 se `overwrite_mode=4`, použijte následující kód:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4
```

Chcete-li vytvořit *gzip* soubor sloučení všechny stažené soubory, použijte následující kód:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4 --create_gzip
```
