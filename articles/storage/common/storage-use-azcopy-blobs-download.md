---
title: Stažení objektů BLOB ze služby Azure Blob Storage pomocí AzCopy v10 za účelem | Microsoft Docs
description: Tento článek obsahuje kolekci AzCopy ukázkových příkazů, které vám pomůžou stahovat objekty BLOB z úložiště objektů BLOB v Azure.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 0a2490a104d18f77a0ec326933f463eb4ebb4339
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96923957"
---
# <a name="download-blobs-from-azure-blob-storage-by-using-azcopy-v10"></a>Stažení objektů BLOB ze služby Azure Blob Storage pomocí AzCopy v10 za účelem

Objekty BLOB a adresáře můžete stáhnout ze služby Blob Storage pomocí nástroje příkazového řádku AzCopy v10 za účelem. 

Pokud chcete zobrazit příklady pro jiné typy úloh, jako je například nahrávání souborů, synchronizace s úložištěm objektů BLOB nebo kopírování objektů BLOB mezi účty, podívejte se na odkazy v části [Další kroky](#next-steps) v tomto článku.

## <a name="get-started"></a>Začínáme

V článku Začínáme [s AzCopy](storage-use-azcopy-v10.md) si můžete stáhnout AzCopy a seznamte se s různými způsoby, jak můžete službě úložiště poskytnout autorizační přihlašovací údaje.

> [!NOTE] 
> V příkladech v tomto článku se předpokládá, že jste zadali autorizační přihlašovací údaje pomocí Azure Active Directory (Azure AD).
>
> Pokud místo toho chcete použít token SAS k autorizaci přístupu k datům objektu blob, můžete tento token připojit k adrese URL prostředku v každém příkazu AzCopy. Příklad: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="download-a-blob"></a>Stažení objektu blob

Pomocí příkazu pro [kopírování AzCopy](storage-ref-azcopy-copy.md) Stáhněte objekt BLOB.

> [!TIP]
> Tento příklad uzavře argumenty cesty s jednoduchými uvozovkami (' '). Použijte jednoduché uvozovky ve všech příkazových prostředích s výjimkou příkazového prostředí systému Windows (cmd.exe). Pokud používáte příkazové prostředí systému Windows (cmd.exe), uzavřete argumenty cesty pomocí dvojitých uvozovek ("") místo jednoduchých uvozovek (' ').

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

> [!NOTE]
> Pokud `Content-md5` hodnota vlastnosti objektu BLOB obsahuje hodnotu hash, AzCopy vypočítá hodnotu hash MD5 pro stažená data a ověří, že hodnota hash MD5 uložená ve vlastnosti objektu BLOB `Content-md5` odpovídá počítané hodnotě hash. Pokud se tyto hodnoty neshodují, stažení se nezdaří, pokud toto chování neprovedete připojením `--check-md5=NoCheck` nebo `--check-md5=LogOnly` zkopírováním příkazu Kopírovat.

## <a name="download-a-directory"></a>Stáhnout adresář

Stáhněte adresář pomocí příkazu [AzCopy Copy](storage-ref-azcopy-copy.md) .

> [!TIP]
> Tento příklad uzavře argumenty cesty s jednoduchými uvozovkami (' '). Použijte jednoduché uvozovky ve všech příkazových prostředích s výjimkou příkazového prostředí systému Windows (cmd.exe). Pokud používáte příkazové prostředí systému Windows (cmd.exe), uzavřete argumenty cesty pomocí dvojitých uvozovek ("") místo jednoduchých uvozovek (' ').

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

Tento příklad vede k adresáři s názvem `C:\myDirectory\myBlobDirectory` , který obsahuje všechny stažené objekty blob.

## <a name="download-directory-contents"></a>Stáhnout obsah adresáře

S použitím zástupného znaku (*) můžete stáhnout obsah adresáře bez kopírování samotného adresáře.

> [!TIP]
> Tento příklad uzavře argumenty cesty s jednoduchými uvozovkami (' '). Použijte jednoduché uvozovky ve všech příkazových prostředích s výjimkou příkazového prostředí systému Windows (cmd.exe). Pokud používáte příkazové prostředí systému Windows (cmd.exe), uzavřete argumenty cesty pomocí dvojitých uvozovek ("") místo jednoduchých uvozovek (' ').

> [!NOTE]
> V současné době je tento scénář podporován pouze pro účty, které nemají hierarchický obor názvů.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

Přidejte `--recursive` příznak pro stažení souborů ve všech podadresářích.

## <a name="download-specific-blobs"></a>Stáhnout konkrétní objekty blob

Můžete stáhnout konkrétní objekty BLOB pomocí úplných názvů souborů, částečných názvů se zástupnými znaky (*) nebo pomocí data a času. 

> [!TIP]
> Tyto příklady uzavírají argumenty cesty s jednoduchými uvozovkami (' '). Použijte jednoduché uvozovky ve všech příkazových prostředích s výjimkou příkazového prostředí systému Windows (cmd.exe). Pokud používáte příkazové prostředí systému Windows (cmd.exe), uzavřete argumenty cesty pomocí dvojitých uvozovek ("") místo jednoduchých uvozovek (' ').

#### <a name="specify-multiple-complete-blob-names"></a>Zadat více úplných názvů objektů BLOB

Použijte příkaz [AzCopy Copy](storage-ref-azcopy-copy.md) s `--include-path` možností. Jednotlivé názvy objektů BLOB oddělte pomocí semicolin ( `;` ).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

V tomto příkladu AzCopy přenáší `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` adresář a `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` soubor. Zahrňte `--recursive` možnost pro přenos všech objektů BLOB v `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` adresáři.

Objekty blob můžete také vyloučit pomocí `--exclude-path` Možnosti. Další informace najdete v tématu [kopírování](storage-ref-azcopy-copy.md) referenčních dokumentů AzCopy.

#### <a name="use-wildcard-characters"></a>Použít zástupné znaky

Použijte příkaz [AzCopy Copy](storage-ref-azcopy-copy.md) s `--include-pattern` možností. Zadejte částečné názvy, které obsahují zástupné znaky. Oddělte názvy pomocí semicolin ( `;` ).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Objekty blob můžete také vyloučit pomocí `--exclude-pattern` Možnosti. Další informace najdete v tématu [kopírování](storage-ref-azcopy-copy.md) referenčních dokumentů AzCopy.

`--include-pattern`Možnosti a `--exclude-pattern` platí pouze pro názvy objektů blob, nikoli pro cestu.  Chcete-li zkopírovat všechny textové soubory (objekty BLOB), které existují ve stromové struktuře, použijte `–recursive` možnost pro získání celého adresářového stromu a pak použijte příkaz `–include-pattern` a zadejte, `*.txt` Chcete-li získat všechny textové soubory.

#### <a name="download-blobs-that-were-modified-after-a-date-and-time"></a>Stáhnout objekty blob, které byly upraveny po datu a čase 

Použijte příkaz [AzCopy Copy](storage-ref-azcopy-copy.md) s `--include-after` možností. Zadejte datum a čas ve formátu ISO-8601 (například: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |

Podrobné informace najdete v referenčních dokumentech ke [kopírování AzCopy](storage-ref-azcopy-copy.md) .

#### <a name="download-previous-versions-of-a-blob"></a>Stáhnout předchozí verze objektu BLOB

Pokud jste povolili [správu verzí objektů BLOB](../blobs/versioning-enable.md), můžete stáhnout jednu nebo více předchozích verzí objektu BLOB. 

Nejdřív vytvořte textový soubor, který obsahuje seznam [ID verzí](../blobs/versioning-overview.md). Každé ID verze se musí nacházet na samostatném řádku. Příklad: 

```
2020-08-17T05:50:34.2199403Z
2020-08-17T05:50:34.5041365Z
2020-08-17T05:50:36.7607103Z
```

Pak použijte příkaz [AzCopy Copy](storage-ref-azcopy-copy.md) s `--list-of-versions` možností. Zadejte umístění textového souboru, který obsahuje seznam verzí (například: `D:\\list-of-versions.txt` ).  

#### <a name="download-a-blob-snapshot"></a>Stažení snímku objektu BLOB

[Snímek objektu BLOB](/azure/storage/blobs/snapshots-overview.md) si můžete stáhnout tak, že odkazujete na hodnotu **DateTime** snímku objektu BLOB. 

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>?sharesnapshot=<DateTime-of-snapshot>' '<local-file-path>'` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |

> [!NOTE]
> Pokud k autorizaci přístupu k datům objektu BLOB používáte token SAS, potom po tokenu SAS přidejte **hodnotu DateTime** snímku. Příklad: `'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z'`.

## <a name="download-with-optional-flags"></a>Stáhnout s volitelnými příznaky

Operaci stahování můžete upravit pomocí volitelných příznaků. Tady je několik příkladů.

|Scénář|Příznak|
|---|---|
|Automatické dekomprimace souborů|**--dekomprese**|
|Určete, jak chcete, aby byly položky protokolu související s kopírováním.|**--úroveň protokolu** = \[ \| \| informace o chybě upozornění \| none\]|
|Určete, zda a jak přepsat konfliktní soubory a objekty BLOB v cíli.|**--přepsat** = \[ true \| false \| ifSourceNewer – \| příkazový řádek\]|

Úplný seznam najdete v tématu [Možnosti](storage-ref-azcopy-copy.md#options).

## <a name="next-steps"></a>Další kroky

Další příklady najdete v těchto článcích:

- [Příklady: nahrání](storage-use-azcopy-blobs-upload.md)
- [Příklady: kopírování mezi účty](storage-use-azcopy-blobs-copy.md)
- [Příklady: synchronizace](storage-use-azcopy-blobs-synchronize.md)
- [Příklady: sady Amazon S3](storage-use-azcopy-s3.md)
- [Příklady: soubory Azure](storage-use-azcopy-files.md)
- [Kurz: Migrace místních dat do cloudového úložiště pomocí AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Konfigurace, optimalizace a řešení potíží s AzCopy](storage-use-azcopy-configure.md)