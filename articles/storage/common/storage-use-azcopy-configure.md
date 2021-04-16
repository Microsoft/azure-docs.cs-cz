---
title: Vyhledá chyby & pokračování úloh pomocí protokolů v AzCopy (Azure Storage) | Microsoft Docs
description: Naučte se používat protokoly k diagnostice chyb a k pokračování úloh, které se pozastaví pomocí souborů plánu.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: d3b956803e9a796c49288f90873e88c3b69f1c7b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502891"
---
# <a name="find-errors-and-resume-jobs-by-using-log-and-plan-files-in-azcopy"></a>Vyhledání chyb a obnovení úloh pomocí souborů protokolů a plánů v AzCopy

AzCopy je nástroj příkazového řádku, pomocí kterého můžete kopírovat objekty blob nebo soubory do nebo z účtu úložiště. Tento článek vám pomůže s použitím protokolů pro diagnostiku chyb a následného použití souborů plánu k obnovení úloh. Tento článek také ukazuje, jak nakonfigurovat soubory protokolů a plánů změnou jejich úrovně podrobností a výchozího umístění, kde jsou uložená.

> [!NOTE]
> Pokud hledáte obsah, který vám pomůžete začít s AzCopy, přečtěte si téma Začínáme [s AzCopy](storage-use-azcopy-v10.md).

## <a name="log-and-plan-files"></a>Soubory protokolů a plánu

AzCopy vytvoří soubory *protokolů* a *plánů* pro každou úlohu. Tyto protokoly můžete použít k prozkoumání a řešení potíží s případnými problémy. 

Protokoly budou obsahovat stav selhání ( `UPLOADFAILED` , `COPYFAILED` , a `DOWNLOADFAILED` ), úplnou cestu a důvod selhání.

Ve výchozím nastavení se soubory protokolů a plánů nacházejí v adresáři ve `%USERPROFILE%\.azcopy` Windows nebo `$HOME$\.azcopy` adresáři v počítačích Mac a Linux, ale můžete toto umístění změnit. 

Relevantní chyba není nutně první chyba, která se zobrazí v souboru. V případě chyb, jako jsou chyby sítě, vypršení časového limitu a chyby zaneprázdnění serveru, bude AzCopy opakovat až 20 krát a obvykle se proces opakování zdaří.  První chyba, kterou vidíte, může být neškodná, která byla úspěšně opakována.  Takže místo toho, aby se v souboru prohledala první chyba, vyhledejte chyby, které jsou blízko `UPLOADFAILED` , `COPYFAILED` nebo `DOWNLOADFAILED` . 

> [!IMPORTANT]
> Při odesílání žádosti o podpora Microsoftu (nebo řešení potíží, které se týkají jakékoli třetí strany) nastavte navýšení verze příkazu, který chcete spustit. Tím se zajistí, že se SAS nebude náhodně sdílet s kdokoli. Navýšení verze se dá najít na začátku souboru protokolu.

## <a name="review-the-logs-for-errors"></a>Zkontrolujte chyby v protokolech.

Následující příkaz zobrazí všechny chyby se `UPLOADFAILED` stavem z `04dc9ca9-158f-7945-5933-564021086c79` protokolu:

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

## <a name="view-and-resume-jobs"></a>Zobrazení a obnovení úloh

Každá operace přenosu vytvoří úlohu AzCopy. Historii úloh zobrazíte pomocí následujícího příkazu:

```
azcopy jobs list
```

Chcete-li zobrazit statistiku úlohy, použijte následující příkaz:

```
azcopy jobs show <job-id>
```

Chcete-li filtrovat převody podle stavu, použijte následující příkaz:

```
azcopy jobs show <job-id> --with-status=Failed
```

Pomocí následujícího příkazu obnovíte neúspěšnou/zrušenou úlohu. Tento příkaz používá svůj identifikátor spolu s tokenem SAS, protože není trvalý z důvodů zabezpečení:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> Uzavřete argumenty cesty, jako je token SAS, na jednoduché uvozovky (' '). Použijte jednoduché uvozovky ve všech příkazových prostředích s výjimkou příkazového prostředí systému Windows (cmd.exe). Pokud používáte příkazové prostředí systému Windows (cmd.exe), uzavřete argumenty cesty pomocí dvojitých uvozovek ("") místo jednoduchých uvozovek (' ').

Když úlohu obnovíte, AzCopy se podívá na soubor plánu úlohy. Soubor plánu obsahuje seznam všech souborů, které byly identifikovány pro zpracování při prvním vytvoření úlohy. Když obnovíte úlohu, AzCopy se pokusí přenést všechny soubory, které jsou uvedené v souboru plánu, který se už nepřenesl.

## <a name="change-the-location-of-plan-files"></a>Změna umístění souborů plánu

Použijte některý z těchto příkazů.

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | Prostředí`$env:AZCOPY_JOB_PLAN_LOCATION="<value>"` <br> V příkazovém řádku použijte:: `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **macOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

`azcopy env`Pro kontrolu aktuální hodnoty této proměnné použijte. Pokud je hodnota prázdná, pak se soubory plánu zapisují do výchozího umístění.

## <a name="change-the-location-of-log-files"></a>Změna umístění souborů protokolu

Použijte některý z těchto příkazů.

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | Prostředí`$env:AZCOPY_LOG_LOCATION="<value>"` <br> V příkazovém řádku použijte:: `set AZCOPY_LOG_LOCATION=<value>`|
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **macOS** | `export AZCOPY_LOG_LOCATION=<value>` |

`azcopy env`Pro kontrolu aktuální hodnoty této proměnné použijte. Pokud je hodnota prázdná, protokoly se zapisují do výchozího umístění.

## <a name="change-the-default-log-level"></a>Změna výchozí úrovně protokolu

Ve výchozím nastavení je úroveň protokolu AzCopy nastavena na hodnotu `INFO` . Pokud chcete snížit podrobnosti protokolu, aby se ušetřilo místo na disku, přepište toto nastavení pomocí ``--log-level`` Možnosti. 

Dostupné úrovně protokolu jsou: `NONE` , `DEBUG` , `INFO` , `WARNING` , `ERROR` , a `PANIC` `FATAL` .

## <a name="remove-plan-and-log-files"></a>Odebrat soubory plánu a protokolu

Pokud chcete ze svého místního počítače odebrat všechny soubory plánu a protokolu, aby se ušetřilo místo na disku, použijte `azcopy jobs clean` příkaz.

Chcete-li odebrat plán a soubory protokolu spojené pouze s jednou úlohou, použijte `azcopy jobs rm <job-id>` . `<job-id>`Zástupný symbol v tomto příkladu nahraďte ID úlohy úlohy.

## <a name="see-also"></a>Viz také

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
