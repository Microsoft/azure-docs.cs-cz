---
title: Konfigurace, optimalizovat a řešení potíží s AzCopy s Azure Storage | Dokumentace Microsoftu
description: Konfigurace, optimalizovat a řešení potíží s AzCopy.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 18dc3e224df18c900653e4549badcdd93f0df6ec
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688018"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurace, optimalizovat a řešení potíží s AzCopy

AzCopy je nástroj příkazového řádku, který vám pomůže zkopírovat objekty BLOB nebo souborů do nebo z účtu úložiště. Tento článek vám umožní provádět pokročilou konfiguraci úlohy a vám pomůže řešit problémy se můžou objevit, jak pomocí nástroje AzCopy.

> [!NOTE]
> Pokud hledáte obsah tak, aby vám pomůžou začít s AzCopy, naleznete v některém z následujících článcích:
> - [Začínáme s AzCopy](storage-use-azcopy-v10.md)
> - [Přenos dat pomocí AzCopy a blob storage](storage-use-azcopy-blobs.md)
> - [Přenos dat pomocí AzCopy a file storage](storage-use-azcopy-files.md)
> - [Přenos dat pomocí AzCopy a Amazon S3 intervalů](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Konfigurace nastavení proxy serveru

Chcete-li konfigurovat nastavení proxy serveru pro AzCopy, nastavte `https_proxy` proměnné prostředí.

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | `set https_proxy=<proxy IP>:<proxy port>` |
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

AzCopy v současné době nepodporuje proxy servery, které vyžadují ověřování pomocí protokolu NTLM nebo Kerberos.

## <a name="optimize-throughput"></a>Optimalizace propustnosti

Nastavte `AZCOPY_CONCURRENCY_VALUE` proměnné prostředí, chcete-li konfigurovat počet souběžných požadavků a k řízení spotřeby propustnost výkonu a prostředků. Pokud má počítač méně než 5 procesory, pak hodnota Tato proměnná je nastavená na `32`. V opačném případě výchozí hodnota je rovno 16 počtem procesorů. Výchozí maximální hodnotu této proměnné je `300`, ale můžete ručně nastavit tuto hodnotu vyšší nebo nižší.

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Použití `azcopy env` ke kontrole aktuální hodnotu této proměnné.  Pokud je hodnota prázdná, pak bude `AZCOPY_CONCURRENCY_VALUE` proměnná je nastavená na výchozí hodnotu `300`.

## <a name="change-the-location-of-the-log-files"></a>Změnit umístění souboru protokolu

Ve výchozím nastavení, soubory protokolu jsou umístěny v `%USERPROFILE\\.azcopy` na Windows nebo v adresáři `$HOME\\.azcopy` adresáře na Mac a Linux. Toto umístění můžete změnit, pokud je potřeba pomocí těchto příkazů.

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Použití `azcopy env` ke kontrole aktuální hodnotu této proměnné. Pokud je hodnota prázdná, protokoly se zapisují do výchozího umístění.

## <a name="change-the-default-log-level"></a>Změnit výchozí úroveň protokolování

Ve výchozím nastavení je nástroj AzCopy úroveň protokolu nastavena na `INFO`. Pokud chcete snížit úroveň podrobností protokolu k ušetřit místo na disku, přepsat pomocí tohoto nastavení ``--log-level`` možnost. 

Jsou dostupné úrovně: `DEBUG`, `INFO`, `WARNING`, `ERROR`, `PANIC`, a `FATAL`.

## <a name="troubleshoot-issues"></a>Řešení potíží

AzCopy vytvoří soubory protokolu a plán pro každou úlohu. Protokoly můžete prozkoumat a vyřešit potenciální problémy. 

Tyto protokoly budou obsahovat stav selhání (`UPLOADFAILED`, `COPYFAILED`, a `DOWNLOADFAILED`), úplnou cestu a důvod selhání.

Ve výchozím nastavení, jsou umístěny soubory protokolu a plán v `%USERPROFILE\\.azcopy` ve Windows nebo `$HOME\\.azcopy` adresáře na Mac a Linux.

> [!IMPORTANT]
> Při odesílání požadavku na Microsoft Support (nebo řešení potíží týkajících se třetí stranu), sdílet zrevidovaně verzi příkazu, kterou chcete spustit. Tím se zajistí, že SAS není omylem sdílet s kýmkoli. Můžete najít zrevidovaně verzi na začátku souboru protokolu.

### <a name="review-the-logs-for-errors"></a>Zkontrolujte protokoly chyb

Následující příkaz zobrazí všechny chyby s `UPLOADFAILED` stavu `04dc9ca9-158f-7945-5933-564021086c79` protokolu:

**Windows**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Zobrazit a obnovit úlohy

Každé operace přenosu vytvoří úlohu AzCopy. Chcete-li zobrazit historii úloh použijte následující příkaz:

```
azcopy jobs list
```

Chcete-li zobrazit statistiky úlohy, použijte následující příkaz:

```
azcopy jobs show <job-id>
```

Chcete-li filtrovat přenosy podle stavu, použijte následující příkaz:

```
azcopy jobs show <job-id> --with-status=Failed
```

Použijte následující příkaz k obnovení úlohy se nezdařilo nebo bylo zrušeno. Tento příkaz používá jeho identifikátor spolu s tokenem SAS není trvalé z bezpečnostních důvodů:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

Při obnovení úlohy AzCopy zjistí soubor plánu úlohy. Souboru s plánem seznam všech souborů, které byly určeny pro zpracování při prvním vytvoření úlohy. Při obnovení úlohy AzCopy se pokusí přenést všechny soubory, které jsou uvedeny v souboru plán, který již nebyly převedeny.