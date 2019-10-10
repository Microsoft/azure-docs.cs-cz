---
title: Konfigurace, optimalizace a řešení potíží s AzCopy pomocí Azure Storage | Microsoft Docs
description: Konfigurace, optimalizace a řešení potíží s AzCopy.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 3843eb2e906e3fb8d390e509e17117b7849ac220
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244703"
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

## <a name="optimize-throughput"></a>Optimalizace propustnosti

Pomocí příznaku `cap-mbps` můžete umístit strop pro míru propustnosti dat. Například následující příkaz CAPS propustnosti `10` megabitů (MB) za sekundu.

```azcopy
azcopy cap-mbps 10
```

Při přenosu malých souborů se propustnost může snížit. Propustnost můžete zvýšit nastavením proměnné prostředí `AZCOPY_CONCURRENCY_VALUE`. Tato proměnná Určuje počet souběžných požadavků, které mohou nastat.  Pokud má počítač méně než 5 procesorů, hodnota této proměnné je nastavená na `32`. V opačném případě se výchozí hodnota rovná 16 vynásobenému počtem procesorů. Maximální výchozí hodnota této proměnné je `300`, ale tuto hodnotu můžete ručně nastavit na vyšší nebo nižší.

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Pro kontrolu aktuální hodnoty této proměnné použijte `azcopy env`.  Pokud je hodnota prázdná, proměnná `AZCOPY_CONCURRENCY_VALUE` je nastavena na výchozí hodnotu `300`.

## <a name="change-the-location-of-the-log-files"></a>Změna umístění souborů protokolu

Ve výchozím nastavení se soubory protokolu nacházejí v adresáři `%USERPROFILE%\.azcopy` ve Windows nebo v adresáři `$HOME\\.azcopy` v systému Mac a Linux. Toto umístění můžete změnit, pokud potřebujete pomocí těchto příkazů.

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Pro kontrolu aktuální hodnoty této proměnné použijte `azcopy env`. Pokud je hodnota prázdná, protokoly se zapisují do výchozího umístění.

## <a name="change-the-default-log-level"></a>Změna výchozí úrovně protokolu

Ve výchozím nastavení je úroveň protokolu AzCopy nastavena na hodnotu `INFO`. Pokud chcete snížit podrobnosti protokolu, aby se ušetřilo místo na disku, přepište toto nastavení pomocí možnosti ``--log-level``. 

Dostupné úrovně protokolu jsou: `DEBUG`, `INFO`, `WARNING`, `ERROR`, `PANIC` a `FATAL`.

## <a name="troubleshoot-issues"></a>Řešení potíží

AzCopy vytvoří soubory protokolů a plánů pro každou úlohu. Protokoly můžete použít k prozkoumání a odstraňování potíží s případnými problémy. 

Protokoly budou obsahovat stav selhání (`UPLOADFAILED`, `COPYFAILED` a `DOWNLOADFAILED`), úplnou cestu a důvod selhání.

Ve výchozím nastavení se soubory protokolů a plánů nacházejí v adresáři `%USERPROFILE\\.azcopy` ve Windows nebo v adresáři `$HOME\\.azcopy` v systému Mac a Linux.

> [!IMPORTANT]
> Při odesílání žádosti o podpora Microsoftu (nebo řešení potíží, které se týkají jakékoli třetí strany) nastavte navýšení verze příkazu, který chcete spustit. Tím se zajistí, že se SAS nebude náhodně sdílet s kdokoli. Navýšení verze se dá najít na začátku souboru protokolu.

### <a name="review-the-logs-for-errors"></a>Zkontrolujte chyby v protokolech.

Následující příkaz zobrazí všechny chyby se stavem @no__t 0 z protokolu `04dc9ca9-158f-7945-5933-564021086c79`:

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
