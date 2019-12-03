---
title: Konfigurace, optimalizace a řešení potíží s AzCopy pomocí Azure Storage | Microsoft Docs
description: Konfigurace, optimalizace a řešení potíží s AzCopy.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 7097faa64319a46b1efc91233e30ea992d064246
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687644"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurace, optimalizace a řešení potíží s AzCopy

AzCopy je nástroj příkazového řádku, který můžete použít ke kopírování objektů BLOB nebo souborů do nebo z účtu úložiště. Tento článek vám pomůže provádět pokročilé konfigurační úlohy a pomáhá řešit problémy, které mohou nastat při používání AzCopy.

> [!NOTE]
> Pokud hledáte obsah, který vám pomůžete začít s AzCopy, přečtěte si některé z následujících článků:
> - [Začínáme s AzCopy](storage-use-azcopy-v10.md)
> - [Přenos dat pomocí AzCopy a BLOB Storage](storage-use-azcopy-blobs.md)
> - [Přenos dat pomocí AzCopy a úložiště souborů](storage-use-azcopy-files.md)
> - [Přenos dat pomocí kontejnerů AzCopy a Amazon S3](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Konfigurace nastavení proxy serveru

Pokud chcete nakonfigurovat nastavení proxy serveru pro AzCopy, nastavte proměnnou prostředí `https_proxy`. Pokud spustíte AzCopy ve Windows, AzCopy automaticky detekuje nastavení proxy serveru, takže toto nastavení nemusíte používat v systému Windows. Pokud se rozhodnete použít toto nastavení ve Windows, přepíše se automatické zjišťování.

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | V příkazovém řádku použijte: `set https_proxy=<proxy IP>:<proxy port>`<br> V prostředí PowerShell použijte: `$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

AzCopy v současné době nepodporuje proxy servery, které vyžadují ověřování pomocí protokolu NTLM nebo Kerberos.

## <a name="optimize-performance"></a>Optimalizace výkonu

Můžete použít srovnávací testy výkonu a pak pomocí příkazů a proměnných prostředí najít optimální kompromisy mezi výkonem a spotřebou prostředků.

### <a name="run-benchmark-tests"></a>Spustit testy srovnávacích testů

Test srovnávacího testu výkonu můžete spustit pro konkrétní kontejnery objektů BLOB a zobrazit tak obecná statistiku výkonu a kritická místa výkonu identity. 

> [!NOTE]
> V aktuální verzi je tato funkce dostupná jenom pro kontejnery Blob Storage.

Pomocí následujícího příkazu spusťte test srovnávacího testu výkonu.

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy bench 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **Příklad** | `azcopy bench 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/'` |

Tento příkaz spustí srovnávací test výkonu odesláním testovacích dat do zadaného cíle. Testovací data jsou generována v paměti, odeslána do cíle a poté po dokončení testu odstraněna z cílového umístění. Můžete určit, kolik souborů se má vygenerovat a jakou velikost byste chtěli použít při použití volitelných parametrů příkazu.

Chcete-li zobrazit podrobné pokyny pro nápovědu k tomuto příkazu, zadejte příkaz `azcopy bench -h` a stiskněte klávesu ENTER.

### <a name="optimize-throughput"></a>Optimalizace propustnosti

Pomocí příznaku `cap-mbps` můžete umístit strop pro míru propustnosti dat. Například následující funkce CAPS propustnosti `10` megabitů (MB) za sekundu.

```azcopy
azcopy --cap-mbps 10
```

Při přenosu malých souborů se propustnost může snížit. Propustnost můžete zvýšit nastavením proměnné prostředí `AZCOPY_CONCURRENCY_VALUE`. Tato proměnná Určuje počet souběžných požadavků, které mohou nastat.  

Pokud má počítač méně než 5 procesorů, hodnota této proměnné je nastavená na `32`. V opačném případě se výchozí hodnota rovná 16 vynásobenému počtem procesorů. Maximální výchozí hodnota této proměnné je `3000`, ale tuto hodnotu můžete ručně nastavit na vyšší nebo nižší. 

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Pro kontrolu aktuální hodnoty této proměnné použijte `azcopy env`. Pokud je hodnota prázdná, můžete si přečíst, která hodnota se používá, a to na začátku libovolného souboru protokolu AzCopy. Je zde uvedena vybraná hodnota a důvod, proč byla vybrána.

Před nastavením této proměnné doporučujeme spustit test testu výkonnosti. Proces testování srovnávacích testů bude hlásit doporučenou hodnotu souběžnosti. Případně platí, že pokud se síťové podmínky a datové části liší, nastavte tuto proměnnou na slovo `AUTO` místo na konkrétní číslo. To způsobí, že AzCopy vždy spustí stejný proces automatického ladění, který používá testy srovnávacích testů.

### <a name="optimize-memory-use"></a>Optimalizace využití paměti

Nastavením proměnné prostředí `AZCOPY_BUFFER_GB` určete maximální velikost systémové paměti, kterou má AzCopy použít při stahování a nahrávání souborů.
Vyjádřete tuto hodnotu v gigabajtech (GB).

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **MacOS** | `export AZCOPY_BUFFER_GB=<value>` |

## <a name="troubleshoot-issues"></a>Řešení potíží

AzCopy vytvoří soubory protokolů a plánů pro každou úlohu. Protokoly můžete použít k prozkoumání a odstraňování potíží s případnými problémy. 

Protokoly budou obsahovat stav selhání (`UPLOADFAILED`, `COPYFAILED`a `DOWNLOADFAILED`), úplnou cestu a důvod selhání.

Ve výchozím nastavení se soubory protokolů a plánů nacházejí v adresáři `%USERPROFILE$\.azcopy` ve Windows nebo v adresáři `$HOME$\.azcopy` v systémech Mac a Linux, ale pokud chcete, můžete toto umístění změnit.

> [!IMPORTANT]
> Při odesílání žádosti o podpora Microsoftu (nebo řešení potíží, které se týkají jakékoli třetí strany) nastavte navýšení verze příkazu, který chcete spustit. Tím se zajistí, že se SAS nebude náhodně sdílet s kdokoli. Navýšení verze se dá najít na začátku souboru protokolu.

### <a name="review-the-logs-for-errors"></a>Zkontrolujte chyby v protokolech.

Následující příkaz zobrazí všechny chyby se stavem `UPLOADFAILED` z protokolu `04dc9ca9-158f-7945-5933-564021086c79`:

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Zobrazení a obnovení úloh

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

Když úlohu obnovíte, AzCopy se podívá na soubor plánu úlohy. Soubor plánu obsahuje seznam všech souborů, které byly identifikovány pro zpracování při prvním vytvoření úlohy. Když obnovíte úlohu, AzCopy se pokusí přenést všechny soubory, které jsou uvedené v souboru plánu, který se už nepřenesl.

## <a name="change-the-location-of-the-plan-and-log-files"></a>Změna umístění plánu a souborů protokolu

Ve výchozím nastavení se soubory schématu a protokolu nacházejí v adresáři `%USERPROFILE$\.azcopy` ve Windows nebo v adresáři `$HOME$\.azcopy` v systému Mac a Linux. Toto umístění můžete změnit.

### <a name="change-the-location-of-plan-files"></a>Změna umístění souborů plánu

Použijte některý z těchto příkazů.

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Pro kontrolu aktuální hodnoty této proměnné použijte `azcopy env`. Pokud je hodnota prázdná, pak se soubory plánu zapisují do výchozího umístění.

### <a name="change-the-location-of-log-files"></a>Změna umístění souborů protokolu

Použijte některý z těchto příkazů.

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Pro kontrolu aktuální hodnoty této proměnné použijte `azcopy env`. Pokud je hodnota prázdná, protokoly se zapisují do výchozího umístění.

## <a name="change-the-default-log-level"></a>Změna výchozí úrovně protokolu

Ve výchozím nastavení je úroveň protokolu AzCopy nastavená na `INFO`. Pokud chcete snížit podrobnosti protokolu, aby se ušetřilo místo na disku, přepište toto nastavení pomocí možnosti ``--log-level``. 

Dostupné úrovně protokolu jsou: `NONE`, `DEBUG`, `INFO`, `WARNING`, `ERROR`, `PANIC`a `FATAL`.

## <a name="remove-plan-and-log-files"></a>Odebrat soubory plánu a protokolu

Pokud chcete ze svého místního počítače odebrat všechny soubory plánu a protokolu, abyste ušetřili místo na disku, použijte příkaz `azcopy jobs clean`.

Chcete-li odebrat plán a soubory protokolu spojené pouze s jednou úlohou, použijte `azcopy jobs rm <job-id>`. V tomto příkladu nahraďte zástupný symbol `<job-id>` číslem ID úlohy.


