---
title: Konfigurace, optimalizace a řešení potíží s AzCopy pomocí Azure Storage | Dokumenty společnosti Microsoft
description: Konfigurace, optimalizace a řešení potíží s AzCopy.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 87a335f44a31436de735395adbee9035493cbbd2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263416"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurace, optimalizace a řešení potíží s azcopy

AzCopy je nástroj příkazového řádku, který můžete použít ke kopírování objektů BLOB nebo souborů do nebo z účtu úložiště. Tento článek vám pomůže provádět pokročilé úlohy konfigurace a pomůže vám vyřešit problémy, které mohou vzniknout při používání AzCopy.

> [!NOTE]
> Pokud hledáte obsah, který vám pomůže začít s AzCopy, podívejte se na některý z následujících článků:
> - [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
> - [Přenos dat pomocí úložiště Objektů blob AzCopy a objektů blob](storage-use-azcopy-blobs.md)
> - [Přenos dat pomocí AzCopy a ukládání souborů](storage-use-azcopy-files.md)
> - [Přenos dat pomocí kbelíků AzCopy a Amazon S3](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Konfigurace nastavení proxy serveru

Chcete-li nakonfigurovat nastavení proxy serveru `https_proxy` pro AzCopy, nastavte proměnnou prostředí. Pokud spustíte AzCopy v systému Windows, AzCopy automaticky detekuje nastavení proxy serveru, takže nemusíte používat toto nastavení v systému Windows. Pokud se rozhodnete toto nastavení použít v systému Windows, přepíše automatické zjišťování.

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | V příkazovém řádku použijte:`set https_proxy=<proxy IP>:<proxy port>`<br> V PowerShellu použití:`$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **Macos** | `export https_proxy=<proxy IP>:<proxy port>` |

V současné době AzCopy nepodporuje proxy servery, které vyžadují ověření pomocí PROTOKOLU NTLM nebo Kerberos.

## <a name="optimize-performance"></a>Optimalizace výkonu

Můžete porovnat výkon a potom pomocí příkazů a proměnných prostředí najít optimální kompromis mezi výkonem a spotřebou prostředků.

Tato část vám pomůže provádět tyto úlohy optimalizace:

> [!div class="checklist"]
> * Spuštění srovnávacích testů
> * Optimalizace propustnost
> * Optimalizace využití paměti 
> * Optimalizace synchronizace souborů

### <a name="run-benchmark-tests"></a>Spuštění srovnávacích testů

Můžete spustit test benchmark výkonu na konkrétní kontejnery objektů blob zobrazit obecné statistiky výkonu a problémová místa výkonu identity. 

Pomocí následujícího příkazu spusťte test výkonnostní ho testu benchmarku.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy bench 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **Příklad** | `azcopy bench 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

> [!TIP]
> Tento příklad uzavírá argumenty cesty s jednoduchými uvozovkami (''). Ve všech příkazových prostředích s výjimkou prostředí Windows Command Shell (cmd.exe) používejte jednoduché uvozovky. Pokud používáte prostředí Windows Command Shell (cmd.exe), uzavřete argumenty cesty s dvojitými uvozovkami ("") namísto jednoduchých uvozovek ('').

Tento příkaz spustí srovnávací test výkonu nahráním testovacích dat do zadaného cíle. Testovací data jsou generována v paměti, odeslána do cíle a po dokončení testu odstraněna z cíle. Pomocí volitelných parametrů příkazu můžete určit, kolik souborů chcete generovat a jakou velikost chcete mít.

Podrobné referenční dokumenty viz [azcopy bench](storage-ref-azcopy-bench.md).

Chcete-li zobrazit podrobné pokyny `azcopy bench -h` nápovědy k tomuto příkazu, zadejte a stiskněte klávesu ENTER.

### <a name="optimize-throughput"></a>Optimalizace propustnost

Pomocí příznaku `cap-mbps` v příkazech můžete umístit strop na rychlost dat propustnosti. Například následující příkaz obnoví úlohu a zastírá propustnost megabitů `10` (MB) za sekundu. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

Propustnost může snížit při přenosu malých souborů. Propustnost můžete zvýšit nastavením proměnné `AZCOPY_CONCURRENCY_VALUE` prostředí. Tato proměnná určuje počet souběžných požadavků, které mohou nastat.  

Pokud má počítač méně než 5 procesorů, je hodnota `32`této proměnné nastavena na hodnotu . V opačném případě je výchozí hodnota rovna 16 vynásobená počtem procesorů. Maximální výchozí hodnota této `3000`proměnné je , ale tuto hodnotu můžete nastavit ručně vyšší nebo nižší. 

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Macos** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Slouží `azcopy env` ke kontrole aktuální hodnoty této proměnné. Pokud je hodnota prázdná, můžete si přečíst, která hodnota je používána, a to tak, že se podíváte na začátek libovolného souboru protokolu AzCopy. Vybraná hodnota a důvod, proč byla vybrána, jsou uvedeny zde.

Před nastavením této proměnné doporučujeme spustit test srovnávacího testu. Proces srovnávacího testu oznámí doporučenou hodnotu souběžnosti. Případně pokud se podmínky sítě a datové části liší, `AUTO` nastavte tuto proměnnou na slovo namísto na konkrétní číslo. To způsobí, že AzCopy vždy spustí stejný proces automatického ladění, který používá v testech benchmarku.

### <a name="optimize-memory-use"></a>Optimalizace využití paměti

Nastavte `AZCOPY_BUFFER_GB` proměnnou prostředí tak, aby určovala maximální velikost systémové paměti, kterou má AzCopy použít při stahování a nahrávání souborů.
Tuto hodnotu vyjádříte v gigabajtech (GB).

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **Macos** | `export AZCOPY_BUFFER_GB=<value>` |

### <a name="optimize-file-synchronization"></a>Optimalizace synchronizace souborů

Příkaz [synchronizace](storage-ref-azcopy-sync.md) identifikuje všechny soubory v cílovém umístění a potom porovná názvy souborů a naposledy změněná časová razítka před zahájením operace synchronizace. Pokud máte velký počet souborů, můžete zlepšit výkon odstraněním tohoto počátečního zpracování. 

Chcete-li to provést, použijte příkaz [azcopy copy](storage-ref-azcopy-copy.md) a nastavte `--overwrite` příznak na `ifSourceNewer`. AzCopy bude porovnávat soubory tak, jak jsou zkopírovány, aniž by prováděla jakékoli prohledávání a porovnávání předem. To poskytuje výhodu výkonu v případech, kdy existuje velký počet souborů k porovnání.

Příkaz [azcopy copy](storage-ref-azcopy-copy.md) neodstraní soubory z cíle, takže pokud chcete odstranit soubory v cílovém umístění, když již ve zdroji neexistují, použijte příkaz `true` `prompt` [azcopy sync](storage-ref-azcopy-sync.md) s příznakem `--delete-destination` nastaveným na hodnotu nebo . 

## <a name="troubleshoot-issues"></a>Řešení potíží

AzCopy vytváří soubory protokolu a plánování pro každou úlohu. Protokoly můžete použít k prozkoumání a řešení případných problémů. 

Protokoly budou obsahovat stav`UPLOADFAILED`selhání `COPYFAILED`( `DOWNLOADFAILED`, , a ), úplnou cestu a důvod selhání.

Ve výchozím nastavení jsou soubory protokolu `%USERPROFILE%\.azcopy` a plánu `$HOME$\.azcopy` umístěny v adresáři v systému Windows nebo adresáři na počítačích Mac a Linux, ale toto umístění můžete změnit, pokud chcete.

Příslušná chyba nemusí být nutně první chybou, která se zobrazí v souboru. U chyb, jako jsou chyby sítě, časové tok a chyby zaneprázdněného serveru, azCopy bude opakovat až 20krát a obvykle proces opakování proběhne úspěšně.  První chyba, která se zobrazí, může být něco neškodného, který byl úspěšně zopakován.  Takže místo toho, aby při pohledu na první chybu `UPLOADFAILED` `COPYFAILED`v `DOWNLOADFAILED`souboru, vyhledejte chyby, které jsou v blízkosti , , nebo . 

> [!IMPORTANT]
> Při odesílání žádosti na podporu společnosti Microsoft (nebo řešení problému týkajícího se jakékoli třetí strany) sdílejte redigovanou verzi příkazu, který chcete spustit. Tím zajistíte, že SAS nebude omylem sdílena s nikým. Redigovanou verzi najdete na začátku souboru protokolu.

### <a name="review-the-logs-for-errors"></a>Zkontrolujte, že protokoly nepociťují chyby

Následující příkaz získá všechny `UPLOADFAILED` chyby `04dc9ca9-158f-7945-5933-564021086c79` se stavem z protokolu:

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Zobrazit a pokračovat v úlohách

Každá operace přenosu vytvoří úlohu AzCopy. K zobrazení historie úloh použijte následující příkaz:

```
azcopy jobs list
```

Chcete-li zobrazit statistiku úloh, použijte následující příkaz:

```
azcopy jobs show <job-id>
```

Chcete-li filtrovat přenosy podle stavu, použijte následující příkaz:

```
azcopy jobs show <job-id> --with-status=Failed
```

Chcete-li pokračovat v neúspěšné/zrušené úloze, použijte následující příkaz. Tento příkaz používá svůj identifikátor spolu s tokenem SAS, protože není trvalý z bezpečnostních důvodů:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> Uzavřete argumenty cesty, jako je například token SAS s jednoduchými uvozovkami (''). Ve všech příkazových prostředích s výjimkou prostředí Windows Command Shell (cmd.exe) používejte jednoduché uvozovky. Pokud používáte prostředí Windows Command Shell (cmd.exe), uzavřete argumenty cesty s dvojitými uvozovkami ("") namísto jednoduchých uvozovek ('').

Když obnovíte úlohu, AzCopy se podívá na soubor plánu úlohy. Soubor plánu obsahuje seznam všech souborů, které byly identifikovány pro zpracování při prvním vytvoření úlohy. Když obnovíte úlohu, AzCopy se pokusí přenést všechny soubory, které jsou uvedeny v souboru plánu, které ještě nebyly přeneseny.

## <a name="change-the-location-of-the-plan-and-log-files"></a>Změna umístění souborů plánu a protokolu

Ve výchozím nastavení jsou soubory plánu `%USERPROFILE%\.azcopy` a protokolu umístěny `$HOME$\.azcopy` v adresáři v systému Windows nebo v adresáři na Počítačích Mac a Linux. Toto umístění můžete změnit.

### <a name="change-the-location-of-plan-files"></a>Změna umístění souborů plánu

Použijte některý z těchto příkazů.

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Macos** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Slouží `azcopy env` ke kontrole aktuální hodnoty této proměnné. Pokud je hodnota prázdná, pak se soubory plánu zapisují do výchozího umístění.

### <a name="change-the-location-of-log-files"></a>Změna umístění souborů protokolu

Použijte některý z těchto příkazů.

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **Macos** | `export AZCOPY_LOG_LOCATION=<value>` |

Slouží `azcopy env` ke kontrole aktuální hodnoty této proměnné. Pokud je hodnota prázdná, protokoly jsou zapsány do výchozího umístění.

## <a name="change-the-default-log-level"></a>Změna výchozí úrovně protokolu

Ve výchozím nastavení je úroveň protokolu `INFO`AzCopy nastavena na hodnotu . Chcete-li snížit podrobnostprotokolu a ušetřit tak místo na disku, přepište ``--log-level`` toto nastavení pomocí této možnosti. 

Dostupné úrovně protokolu `NONE` `DEBUG`jsou: , `PANIC`, `FATAL` `INFO` `WARNING`, `ERROR`, , a .

## <a name="remove-plan-and-log-files"></a>Odebrání souborů plánu a protokolu

Chcete-li odebrat všechny soubory plánu a protokolu z místního `azcopy jobs clean` počítače, abyste ušetřili místo na disku, použijte příkaz.

Chcete-li odebrat soubory plánu a protokolu `azcopy jobs rm <job-id>`přidružené pouze k jedné úloze, použijte . Nahraďte `<job-id>` zástupný symbol v tomto příkladu id úlohy.


