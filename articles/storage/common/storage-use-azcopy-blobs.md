---
title: Přenos dat do nebo z úložiště objektů BLOB v Azure pomocí AzCopy v10 za účelem | Microsoft Docs
description: Tento článek obsahuje kolekci AzCopy ukázkových příkazů, které vám pomůžou vytvářet kontejnery, kopírovat soubory a synchronizovat adresáře mezi místními systémy souborů a kontejnery.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 294adce3dc312003d72336bd0752ba3aba5eaace
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792850"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Přenos dat pomocí AzCopy a BLOB Storage

AzCopy je nástroj příkazového řádku, který můžete použít ke kopírování dat do, z nebo mezi účty úložiště. Tento článek obsahuje příklady příkazů, které fungují s úložištěm objektů BLOB.

> [!TIP]
> Příklady v tomto článku jsou argumenty cesty s jednoduchými uvozovkami (' '). Použijte jednoduché uvozovky ve všech příkazových prostředích s výjimkou příkazového prostředí systému Windows (cmd.exe). Pokud používáte příkazové prostředí systému Windows (cmd.exe), uzavřete argumenty cesty pomocí dvojitých uvozovek ("") místo jednoduchých uvozovek (' ').

## <a name="get-started"></a>Začínáme

V článku Začínáme [s AzCopy](storage-use-azcopy-v10.md) si můžete stáhnout AzCopy a seznamte se s různými způsoby, jak můžete službě úložiště poskytnout autorizační přihlašovací údaje.

> [!NOTE]
> V příkladech v tomto článku se předpokládá, že jste ověřili vaši identitu pomocí `AzCopy login` příkazu. AzCopy pak použije účet Azure AD k autorizaci přístupu k datům v úložišti objektů BLOB.
>
> Pokud místo toho chcete použít token SAS k autorizaci přístupu k datům objektu blob, můžete tento token připojit k adrese URL prostředku v každém příkazu AzCopy.
>
> Příklad: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Vytvoření kontejneru

K vytvoření kontejneru můžete použít příkaz [AzCopy vytvořit](storage-ref-azcopy-make.md) . Příklady v této části vytvoří kontejner s názvem `mycontainer` .

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Příklad** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Příklad** (hierarchický obor názvů) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

Podrobné referenční dokumentaci najdete v tématu [AzCopy](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Nahrání souborů

Příkaz [AzCopy Copy](storage-ref-azcopy-copy.md) můžete použít k nahrání souborů a adresářů z místního počítače.

Tato část obsahuje následující příklady:

> [!div class="checklist"]
> * Nahrání souboru
> * Odeslat adresář
> * Nahrajte obsah adresáře. 
> * Odeslat konkrétní soubory

> [!TIP]
> Operaci nahrávání můžete upravit pomocí volitelných příznaků. Tady je několik příkladů.
>
> |Scénář|Příznak|
> |---|---|
> |Nahrání souborů jako doplňovacích objektů blob nebo objektů blob stránky:|**--BLOB-typ** = \[ BlockBlob \| PageBlob \| AppendBlob\]|
> |Nahrání do konkrétní úrovně přístupu (například do archivní úrovně):|**--Block-– úroveň** = \[ objektu BLOB Žádný \| horká \| studená \| archiv\]|
> 
> Úplný seznam najdete v tématu [Možnosti](storage-ref-azcopy-copy.md#options).

### <a name="upload-a-file"></a>Nahrání souboru

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Příklad** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

Soubor můžete také nahrát pomocí zástupného symbolu (*) kdekoli v cestě k souboru nebo v názvu souboru. Například: `'C:\myDirectory\*.txt'` nebo `C:\my*\*.txt` .

### <a name="upload-a-directory"></a>Odeslat adresář

Tento příklad zkopíruje adresář (a všechny soubory v tomto adresáři) do kontejneru objektů BLOB. Výsledkem je adresář v kontejneru se stejným názvem.

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Příklad** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Pokud chcete kopírovat do adresáře v rámci kontejneru, stačí zadat název tohoto adresáře do řetězce příkazu.

|    |     |
|--------|-----------|
| **Příklad** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

Pokud zadáte název adresáře, který v kontejneru neexistuje, vytvoří AzCopy nový adresář s tímto názvem.

### <a name="upload-the-contents-of-a-directory"></a>Nahrajte obsah adresáře.

Můžete nahrát obsah adresáře bez kopírování samotného nadřazeného adresáře pomocí zástupného znaku (*).

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Příklad** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> Připojí `--recursive` příznak pro nahrání souborů ve všech podadresářích.

### <a name="upload-specific-files"></a>Odeslat konkrétní soubory

Konkrétní soubory můžete nahrát pomocí úplných názvů souborů, částečných názvů se zástupnými znaky (*) nebo pomocí data a času.

#### <a name="specify-multiple-complete-file-names"></a>Zadat více úplných názvů souborů

Použijte příkaz [AzCopy Copy](storage-ref-azcopy-copy.md) s `--include-path` možností. Jednotlivé názvy souborů oddělte pomocí středníku ( `;` ).

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Příklad** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

V tomto příkladu AzCopy přenáší `C:\myDirectory\photos` adresář a `C:\myDirectory\documents\myFile.txt` soubor. Je nutné zahrnout `--recursive` možnost pro přenos všech souborů v `C:\myDirectory\photos` adresáři.

Soubory můžete také vyloučit pomocí `--exclude-path` Možnosti. Další informace najdete v tématu [kopírování](storage-ref-azcopy-copy.md) referenčních dokumentů AzCopy.

#### <a name="use-wildcard-characters"></a>Použít zástupné znaky

Použijte příkaz [AzCopy Copy](storage-ref-azcopy-copy.md) s `--include-pattern` možností. Zadejte částečné názvy, které obsahují zástupné znaky. Oddělte názvy pomocí semicolin ( `;` ). 

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Příklad** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

Soubory můžete také vyloučit pomocí `--exclude-pattern` Možnosti. Další informace najdete v tématu [kopírování](storage-ref-azcopy-copy.md) referenčních dokumentů AzCopy.

`--include-pattern`Možnosti a se `--exclude-pattern` vztahují pouze na názvy souborů, nikoli na cestu.  Chcete-li zkopírovat všechny textové soubory, které existují ve stromové struktuře, použijte `–recursive` možnost pro získání celého adresářového stromu a pak použijte příkaz `–include-pattern` a zadejte, `*.txt` Chcete-li získat všechny textové soubory.

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>Nahrání souborů, které byly upraveny po datu a čase 

Použijte příkaz [AzCopy Copy](storage-ref-azcopy-copy.md) s `--include-after` možností. Zadejte datum a čas ve formátu ISO-8601 (například: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **Příklad** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'` |

Podrobné informace najdete v referenčních dokumentech ke [kopírování AzCopy](storage-ref-azcopy-copy.md) .

## <a name="download-files"></a>Stažení souborů

K stažení objektů blob, adresářů a kontejnerů do místního počítače můžete použít příkaz [AzCopy Copy](storage-ref-azcopy-copy.md) .

Tato část obsahuje následující příklady:

> [!div class="checklist"]
> * Stažení souboru
> * Stáhnout adresář
> * Stažení obsahu adresáře
> * Stáhnout konkrétní soubory

> [!TIP]
> Operaci stahování můžete upravit pomocí volitelných příznaků. Tady je několik příkladů.
>
> |Scénář|Příznak|
> |---|---|
> |Automatické dekomprimace souborů|**--dekomprese**|
> |Určete, jak chcete, aby byly položky protokolu související s kopírováním.|**--úroveň protokolu** = \[ \| \| informace o chybě upozornění \| none\]|
> |Určete, zda a jak přepsat konfliktní soubory a objekty BLOB v cíli.|**--přepsat** = \[ true \| false \| ifSourceNewer – \| příkazový řádek\]|
> 
> Úplný seznam najdete v tématu [Možnosti](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Pokud `Content-md5` hodnota vlastnosti objektu BLOB obsahuje hodnotu hash, AzCopy vypočítá hodnotu hash MD5 pro stažená data a ověří, že hodnota hash MD5 uložená ve vlastnosti objektu BLOB `Content-md5` odpovídá počítané hodnotě hash. Pokud se tyto hodnoty neshodují, stažení se nezdaří, pokud toto chování neprovedete připojením `--check-md5=NoCheck` nebo `--check-md5=LogOnly` zkopírováním příkazu Kopírovat.

### <a name="download-a-file"></a>Stažení souboru

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Stáhnout adresář

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

Tento příklad vede k adresáři s názvem `C:\myDirectory\myBlobDirectory` , který obsahuje všechny stažené soubory.

### <a name="download-the-contents-of-a-directory"></a>Stažení obsahu adresáře

Obsah adresáře si můžete stáhnout bez zkopírování samotného obsahujícího adresáře pomocí zástupného znaku (*).

> [!NOTE]
> V současné době je tento scénář podporován pouze pro účty, které nemají hierarchický obor názvů.

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> Přidejte `--recursive` příznak pro stažení souborů ve všech podadresářích.

### <a name="download-specific-files"></a>Stáhnout konkrétní soubory

Konkrétní soubory si můžete stáhnout pomocí úplných názvů souborů, částečných názvů se zástupnými znaky (*) nebo pomocí data a času. 

#### <a name="specify-multiple-complete-file-names"></a>Zadat více úplných názvů souborů

Použijte příkaz [AzCopy Copy](storage-ref-azcopy-copy.md) s `--include-path` možností. Jednotlivé názvy souborů oddělte pomocí semicolin ( `;` ).

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

V tomto příkladu AzCopy přenáší `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` adresář a `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` soubor. Je nutné zahrnout `--recursive` možnost pro přenos všech souborů v `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` adresáři.

Soubory můžete také vyloučit pomocí `--exclude-path` Možnosti. Další informace najdete v tématu [kopírování](storage-ref-azcopy-copy.md) referenčních dokumentů AzCopy.

#### <a name="use-wildcard-characters"></a>Použít zástupné znaky

Použijte příkaz [AzCopy Copy](storage-ref-azcopy-copy.md) s `--include-pattern` možností. Zadejte částečné názvy, které obsahují zástupné znaky. Oddělte názvy pomocí semicolin ( `;` ).

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Soubory můžete také vyloučit pomocí `--exclude-pattern` Možnosti. Další informace najdete v tématu [kopírování](storage-ref-azcopy-copy.md) referenčních dokumentů AzCopy.

`--include-pattern`Možnosti a se `--exclude-pattern` vztahují pouze na názvy souborů, nikoli na cestu.  Chcete-li zkopírovat všechny textové soubory, které existují ve stromové struktuře, použijte `–recursive` možnost pro získání celého adresářového stromu a pak použijte příkaz `–include-pattern` a zadejte, `*.txt` Chcete-li získat všechny textové soubory.

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>Stáhnout soubory, které byly změněny po datu a čase 

Použijte příkaz [AzCopy Copy](storage-ref-azcopy-copy.md) s `--include-after` možností. Zadejte datum a čas ve formátu ISO-8601 (například: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>` |
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

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-directory-path>' --list-of-versions '<list-of-versions-file>'`|
| **Příklad** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt' --list-of-versions 'D:\\list-of-versions.txt'` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt' --list-of-versions 'D:\\list-of-versions.txt'` |

Název každého staženého souboru začíná ID verze následovaný názvem objektu BLOB. 

## <a name="copy-blobs-between-storage-accounts"></a>Kopírování objektů blob mezi účty úložiště

Pomocí nástroje AzCopy můžete kopírovat objekty blob do jiných účtů úložiště. Operace kopírování je synchronní, takže když příkaz vrátí výsledek, znamená to, že se zkopírovaly všechny soubory. 

AzCopy používá [server-to-server](/rest/api/storageservices/put-block-from-url) [rozhraní API](/rest/api/storageservices/put-page-from-url)pro servery, takže se data zkopírují přímo mezi úložnými servery. Tyto operace kopírování nepoužívají šířku pásma sítě vašeho počítače. Propustnost těchto operací můžete zvýšit nastavením hodnoty `AZCOPY_CONCURRENCY_VALUE` proměnné prostředí. Další informace najdete v tématu [optimalizace propustnosti](storage-use-azcopy-configure.md#optimize-throughput).

> [!NOTE]
> Tento scénář má v aktuální verzi následující omezení.
>
> - Je nutné připojit token SAS ke každé zdrojové adrese URL. Pokud přihlašovací údaje pro autorizaci zadáte pomocí Azure Active Directory (AD), můžete token SAS vynechat jenom z cílové adresy URL. Ujistěte se, že jste ve svém cílovém účtu nastavili správné role. Viz [možnost 1: použijte Azure Active Directory](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory).
>-  Účty úložiště blob bloku úrovně Premium nepodporují úrovně přístupu. Vynechejte úroveň přístupu objektu BLOB z operace kopírování nastavením `s2s-preserve-access-tier` na `false` (například: `--s2s-preserve-access-tier=false` ).

Tato část obsahuje následující příklady:

> [!div class="checklist"]
> * Kopírování objektu blob do jiného účtu úložiště
> * Zkopírování adresáře do jiného účtu úložiště
> * Kopírování kontejneru do jiného účtu úložiště
> * Kopírování všech kontejnerů, adresářů a souborů do jiného účtu úložiště

Tyto příklady také fungují s účty, které mají hierarchický obor názvů. [Přístup k více protokolům na data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) umožňuje použít stejnou syntaxi URL ( `blob.core.windows.net` ) na těchto účtech.

> [!TIP]
> Operaci kopírování můžete upravit pomocí volitelných příznaků. Tady je několik příkladů.
>
> |Scénář|Příznak|
> |---|---|
> |Zkopírujte objekty BLOB jako blok, stránku nebo doplňovací objekty blob.|**--BLOB-typ** = \[ BlockBlob \| PageBlob \| AppendBlob\]|
> |Zkopírujte do konkrétní úrovně přístupu (například do archivní vrstvy).|**--Block-– úroveň** = \[ objektu BLOB Žádný \| horká \| studená \| archiv\]|
> |Automatické dekomprimace souborů|**--dekomprese** = \[ GZIP – \| uprostřed\]|
> 
> Úplný seznam najdete v tématu [Možnosti](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-blob-to-another-storage-account"></a>Kopírování objektu blob do jiného účtu úložiště

Použijte stejnou syntaxi adresy URL ( `blob.core.windows.net` ) pro účty, které mají hierarchický obor názvů.

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Příklad** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Zkopírování adresáře do jiného účtu úložiště

Použijte stejnou syntaxi adresy URL ( `blob.core.windows.net` ) pro účty, které mají hierarchický obor názvů.

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Příklad** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Příklad** (hierarchický obor názvů)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>Kopírování kontejneru do jiného účtu úložiště

Použijte stejnou syntaxi adresy URL ( `blob.core.windows.net` ) pro účty, které mají hierarchický obor názvů.

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Příklad** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Příklad** (hierarchický obor názvů)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Kopírování všech kontejnerů, adresářů a objektů blob do jiného účtu úložiště

Použijte stejnou syntaxi adresy URL ( `blob.core.windows.net` ) pro účty, které mají hierarchický obor názvů.

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Příklad** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **Příklad** (hierarchický obor názvů)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Synchronizovat soubory

Obsah místního systému souborů můžete synchronizovat s kontejnerem objektů BLOB. Můžete také synchronizovat kontejnery a virtuální adresáře mezi sebou. Synchronizace je jednosměrná. Jinými slovy, jste si zvolili, který z těchto dvou koncových bodů je zdroj a který je cílový. Synchronizace používá také server k rozhraní API serveru. Příklady uvedené v této části také fungují s účty, které mají hierarchický obor názvů. 

> [!NOTE]
> Aktuální verze AzCopy se nesynchronizuje mezi ostatními zdroji a cíli (například: File Storage nebo Amazon Web Services (AWS) S3).

Příkaz [synchronizovat](storage-ref-azcopy-sync.md) porovná názvy souborů a poslední upravená časová razítka. Nastavením `--delete-destination` volitelného příznaku na hodnotu `true` nebo `prompt` odstraňte soubory v cílovém adresáři, pokud už tyto soubory ve zdrojovém adresáři neexistují.

Pokud jste příznak nastavili `--delete-destination` tak, aby `true` AzCopy odstranit soubory bez zadání výzvy. Pokud chcete, aby se zobrazila výzva před tím, než AzCopy odstraní soubor, nastavte `--delete-destination` příznak na `prompt` .

> [!NOTE]
> Chcete-li zabránit nechtěnému odstranění, před použitím příznaku zajistěte, aby byla funkce [obnovitelného odstranění](../blobs/soft-delete-blob-overview.md) povolena `--delete-destination=prompt|true` .

> [!TIP]
> Operaci synchronizace můžete upravit pomocí volitelných příznaků. Tady je několik příkladů.
>
> |Scénář|Příznak|
> |---|---|
> |Určete, jak mají být při stahování ověřovány striktní hodnoty hash MD5.|**--check-MD5** = \[ Kontrolujte \| \| FailIfDifferent FailIfDifferentOrMissing přihlášení \|\]|
> |Vyloučení souborů na základě vzoru.|**--Exclude-Path**|
> |Zadejte, jak chcete, aby byly položky protokolu související s synchronizací.|**--úroveň protokolu** = \[ \| \| informace o chybě upozornění \| none\]|
> 
> Úplný seznam najdete v tématu [Možnosti](storage-ref-azcopy-sync.md#options).

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Aktualizace kontejneru se změnami v místním systému souborů

V tomto případě je cílem kontejner a zdrojem je místní systém souborů. 

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Příklad** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Aktualizace místního systému souborů s použitím změn kontejneru

V tomto případě je místní systém souborů cílový a kontejner je zdroj.

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Příklad** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>Aktualizace kontejneru se změnami v jiném kontejneru

Prvním kontejnerem, který se zobrazí v tomto příkazu, je zdroj. Druhá je cílová.

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Příklad** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Aktualizace adresáře se změnami v adresáři v jiné sdílené složce

Prvním adresářem, který se zobrazí v tomto příkazu, je zdroj. Druhá je cílová.

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Příklad** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>Další kroky

Další příklady najdete v některém z těchto článků:

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)

- [Kurz: Migrace místních dat do cloudového úložiště pomocí AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Přenos dat s použitím AzCopy a úložiště souborů](storage-use-azcopy-files.md)

- [Přenos dat pomocí kontejnerů AzCopy a Amazon S3](storage-use-azcopy-s3.md)

- [Konfigurace, optimalizace a řešení potíží s AzCopy](storage-use-azcopy-configure.md)