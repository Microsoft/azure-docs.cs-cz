---
title: Konfigurace, optimalizace a řešení potíží s AzCopy pomocí Azure Storage | Microsoft Docs
description: Konfigurace, optimalizace a řešení potíží s AzCopy.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: c3ee0f335741c171c3a7ee1df3eea6dea9c4b728
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176154"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurace, optimalizace a řešení potíží s AzCopy

AzCopy je nástroj příkazového řádku, který můžete použít ke kopírování objektů BLOB nebo souborů do nebo z účtu úložiště. Tento článek vám pomůže provádět pokročilé konfigurační úlohy a pomáhá řešit problémy, které mohou nastat při používání AzCopy.

> [!NOTE]
> Pokud hledáte obsah, který vám pomůžete začít s AzCopy, přečtěte si některé z následujících článků:
> - [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
> - [Přenos dat pomocí AzCopy a BLOB Storage](storage-use-azcopy-blobs.md)
> - [Přenos dat pomocí AzCopy a úložiště souborů](storage-use-azcopy-files.md)
> - [Přenos dat pomocí kontejnerů AzCopy a Amazon S3](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Konfigurace nastavení proxy serveru

Chcete-li nakonfigurovat nastavení proxy serveru pro AzCopy, `https_proxy` nastavte proměnnou prostředí. Pokud spustíte AzCopy ve Windows, AzCopy automaticky detekuje nastavení proxy serveru, takže toto nastavení nemusíte používat v systému Windows. Pokud se rozhodnete použít toto nastavení ve Windows, přepíše se automatické zjišťování.

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | V příkazovém řádku použijte:`set https_proxy=<proxy IP>:<proxy port>`<br> V prostředí PowerShell použijte:`$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

AzCopy v současné době nepodporuje proxy servery, které vyžadují ověřování pomocí protokolu NTLM nebo Kerberos.

## <a name="optimize-performance"></a>Optimalizace výkonu

Můžete použít srovnávací testy výkonu a pak pomocí příkazů a proměnných prostředí najít optimální kompromisy mezi výkonem a spotřebou prostředků.

Tato část vám pomůže provést tyto optimalizační úlohy:

> [!div class="checklist"]
> * Spustit testy srovnávacích testů
> * Optimalizace propustnosti
> * Optimalizace využití paměti 
> * Optimalizovat synchronizaci souborů

### <a name="run-benchmark-tests"></a>Spustit testy srovnávacích testů

Test srovnávacího testu výkonu můžete spustit pro konkrétní kontejnery objektů BLOB a zobrazit tak obecná statistiku výkonu a kritická místa výkonu identity. 

Pomocí následujícího příkazu spusťte test srovnávacího testu výkonu.

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy bench 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **Případě** | `azcopy bench 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

> [!TIP]
> Tento příklad uzavře argumenty cesty s jednoduchými uvozovkami (' '). Použijte jednoduché uvozovky ve všech příkazových prostředích s výjimkou příkazového prostředí systému Windows (cmd. exe). Pokud používáte příkazové prostředí systému Windows (cmd. exe), uzavřete argumenty cesty pomocí dvojitých uvozovek ("") místo jednoduchých uvozovek (' ').

Tento příkaz spustí srovnávací test výkonu odesláním testovacích dat do zadaného cíle. Testovací data jsou generována v paměti, odeslána do cíle a poté po dokončení testu odstraněna z cílového umístění. Můžete určit, kolik souborů se má vygenerovat a jakou velikost byste chtěli použít při použití volitelných parametrů příkazu.

Podrobné referenční dokumentace najdete na [AzCopy](storage-ref-azcopy-bench.md).

Chcete-li zobrazit podrobné pokyny pro nápovědu k tomuto `azcopy bench -h` příkazu, zadejte a stiskněte klávesu ENTER.

### <a name="optimize-throughput"></a>Optimalizace propustnosti

Pomocí `cap-mbps` příznaku v příkazech můžete umístit strop pro míru propustnosti dat. Například následující příkaz obnoví úlohu a stejnou propustnost do `10` megabajtů (MB) za sekundu. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

Při přenosu malých souborů se propustnost může snížit. Propustnost můžete zvýšit nastavením proměnné `AZCOPY_CONCURRENCY_VALUE` prostředí. Tato proměnná Určuje počet souběžných požadavků, které mohou nastat.  

Pokud má počítač méně než 5 procesorů, pak je hodnota této proměnné nastavena na `32`. V opačném případě se výchozí hodnota rovná 16 vynásobenému počtem procesorů. Maximální výchozí hodnota této proměnné je `3000`, ale tuto hodnotu můžete nastavit ručně nebo dolů. 

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

`azcopy env` Pro kontrolu aktuální hodnoty této proměnné použijte. Pokud je hodnota prázdná, můžete si přečíst, která hodnota se používá, a to na začátku libovolného souboru protokolu AzCopy. Je zde uvedena vybraná hodnota a důvod, proč byla vybrána.

Před nastavením této proměnné doporučujeme spustit test testu výkonnosti. Proces testování srovnávacích testů bude hlásit doporučenou hodnotu souběžnosti. Případně platí, že pokud se síťové podmínky a datové části liší, nastavte tuto proměnnou na `AUTO` slovo místo na konkrétní číslo. To způsobí, že AzCopy vždy spustí stejný proces automatického ladění, který používá testy srovnávacích testů.

### <a name="optimize-memory-use"></a>Optimalizace využití paměti

Nastavte proměnnou `AZCOPY_BUFFER_GB` prostředí tak, aby určovala maximální velikost systémové paměti, kterou má AzCopy použít při stahování a nahrávání souborů.
Vyjádřete tuto hodnotu v gigabajtech (GB).

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **MacOS** | `export AZCOPY_BUFFER_GB=<value>` |

### <a name="optimize-file-synchronization"></a>Optimalizovat synchronizaci souborů

Příkaz [synchronizovat](storage-ref-azcopy-sync.md) identifikuje všechny soubory v cílovém umístění a pak porovná názvy souborů a poslední změněná časová razítka před zahájením operace synchronizace. Pokud máte velký počet souborů, můžete zvýšit výkon tím, že tento proces předem vynecháte. 

Pokud to chcete provést, použijte místo toho příkaz pro [kopírování AzCopy](storage-ref-azcopy-copy.md) a nastavte `--overwrite` příznak na `ifSourceNewer`. AzCopy bude porovnávat soubory při jejich kopírování bez provedení jakýchkoli kontrol a porovnání předem. V případech, kdy je k dispozici velký počet souborů k porovnání, poskytujeme hraniční výkon.

Příkaz [AzCopy Copy](storage-ref-azcopy-copy.md) neodstraní soubory z cílového umístění, takže pokud chcete odstranit soubory v cílovém umístění, když už na zdroji neexistují, použijte příkaz [AzCopy Sync](storage-ref-azcopy-sync.md) s `--delete-destination` příznakem nastaveným na hodnotu `true` nebo. `prompt` 

## <a name="troubleshoot-issues"></a>Řešení potíží

AzCopy vytvoří soubory protokolů a plánů pro každou úlohu. Protokoly můžete použít k prozkoumání a odstraňování potíží s případnými problémy. 

Protokoly budou obsahovat stav selhání (`UPLOADFAILED`, `COPYFAILED`, a `DOWNLOADFAILED`), úplnou cestu a důvod selhání.

Ve výchozím nastavení se soubory protokolů a plánů nacházejí v `%USERPROFILE%\.azcopy` adresáři ve Windows nebo `$HOME$\.azcopy` adresáři v počítačích Mac a Linux, ale pokud chcete, můžete toto umístění změnit.

Relevantní chyba není nutně první chyba, která se zobrazí v souboru. V případě chyb, jako jsou chyby sítě, vypršení časového limitu a chyby zaneprázdnění serveru, bude AzCopy opakovat až 20 krát a obvykle se proces opakování zdaří.  První chyba, kterou vidíte, může být neškodná, která byla úspěšně opakována.  Takže místo toho, aby se v souboru prohledala první chyba, vyhledejte chyby, které jsou `UPLOADFAILED`blízko `COPYFAILED`, nebo `DOWNLOADFAILED`. 

> [!IMPORTANT]
> Při odesílání žádosti o podpora Microsoftu (nebo řešení potíží, které se týkají jakékoli třetí strany) nastavte navýšení verze příkazu, který chcete spustit. Tím se zajistí, že se SAS nebude náhodně sdílet s kdokoli. Navýšení verze se dá najít na začátku souboru protokolu.

### <a name="review-the-logs-for-errors"></a>Zkontrolujte chyby v protokolech.

Následující příkaz zobrazí všechny chyby se `UPLOADFAILED` stavem z `04dc9ca9-158f-7945-5933-564021086c79` protokolu:

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

> [!TIP]
> Uzavřete argumenty cesty, jako je token SAS, na jednoduché uvozovky (' '). Použijte jednoduché uvozovky ve všech příkazových prostředích s výjimkou příkazového prostředí systému Windows (cmd. exe). Pokud používáte příkazové prostředí systému Windows (cmd. exe), uzavřete argumenty cesty pomocí dvojitých uvozovek ("") místo jednoduchých uvozovek (' ').

Když úlohu obnovíte, AzCopy se podívá na soubor plánu úlohy. Soubor plánu obsahuje seznam všech souborů, které byly identifikovány pro zpracování při prvním vytvoření úlohy. Když obnovíte úlohu, AzCopy se pokusí přenést všechny soubory, které jsou uvedené v souboru plánu, který se už nepřenesl.

## <a name="change-the-location-of-the-plan-and-log-files"></a>Změna umístění plánu a souborů protokolu

Ve výchozím nastavení se soubory schématu a protokolu nacházejí v `%USERPROFILE%\.azcopy` adresáři ve Windows nebo v `$HOME$\.azcopy` adresáři v systému Mac a Linux. Toto umístění můžete změnit.

### <a name="change-the-location-of-plan-files"></a>Změna umístění souborů plánu

Použijte některý z těchto příkazů.

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

`azcopy env` Pro kontrolu aktuální hodnoty této proměnné použijte. Pokud je hodnota prázdná, pak se soubory plánu zapisují do výchozího umístění.

### <a name="change-the-location-of-log-files"></a>Změna umístění souborů protokolu

Použijte některý z těchto příkazů.

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

`azcopy env` Pro kontrolu aktuální hodnoty této proměnné použijte. Pokud je hodnota prázdná, protokoly se zapisují do výchozího umístění.

## <a name="change-the-default-log-level"></a>Změna výchozí úrovně protokolu

Ve výchozím nastavení je úroveň protokolu AzCopy nastavena na `INFO`hodnotu. Pokud chcete snížit podrobnosti protokolu, aby se ušetřilo místo na disku, přepište toto nastavení pomocí ``--log-level`` možnosti. 

Dostupné úrovně protokolu jsou: `NONE`, `DEBUG`, `INFO`, `WARNING`, `ERROR`, `PANIC`a `FATAL`.

## <a name="remove-plan-and-log-files"></a>Odebrat soubory plánu a protokolu

Pokud chcete ze svého místního počítače odebrat všechny soubory plánu a protokolu, aby se ušetřilo místo na disku, `azcopy jobs clean` použijte příkaz.

Chcete-li odebrat plán a soubory protokolu spojené pouze s jednou úlohou `azcopy jobs rm <job-id>`, použijte. `<job-id>` Zástupný symbol v tomto příkladu nahraďte ID úlohy úlohy.


