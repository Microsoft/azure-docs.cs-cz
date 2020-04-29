---
title: Přenos dat do nebo z úložiště objektů BLOB v Azure pomocí AzCopy v10 za účelem | Microsoft Docs
description: Tento článek obsahuje kolekci AzCopy ukázkových příkazů, které vám pomůžou vytvářet kontejnery, kopírovat soubory a synchronizovat adresáře mezi místními systémy souborů a kontejnery.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: b676c2647fbf7c93d271e1d7f68653452125e39b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82137191"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Přenos dat pomocí AzCopy a BLOB Storage

AzCopy je nástroj příkazového řádku, který můžete použít ke kopírování dat do, z nebo mezi účty úložiště. Tento článek obsahuje příklady příkazů, které fungují s úložištěm objektů BLOB.

> [!TIP]
> Příklady v tomto článku jsou argumenty cesty s jednoduchými uvozovkami (' '). Použijte jednoduché uvozovky ve všech příkazových prostředích s výjimkou příkazového prostředí systému Windows (cmd. exe). Pokud používáte příkazové prostředí systému Windows (cmd. exe), uzavřete argumenty cesty pomocí dvojitých uvozovek ("") místo jednoduchých uvozovek (' ').

## <a name="get-started"></a>Začínáme

V článku Začínáme [s AzCopy](storage-use-azcopy-v10.md) si můžete stáhnout AzCopy a seznamte se s různými způsoby, jak můžete službě úložiště poskytnout autorizační přihlašovací údaje.

> [!NOTE]
> V příkladech v tomto článku se předpokládá, že jste ověřili vaši identitu `AzCopy login` pomocí příkazu. AzCopy pak použije účet Azure AD k autorizaci přístupu k datům v úložišti objektů BLOB.
>
> Pokud místo toho chcete použít token SAS k autorizaci přístupu k datům objektu blob, můžete tento token připojit k adrese URL prostředku v každém příkazu AzCopy.
>
> Například: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Vytvoření kontejneru

K vytvoření kontejneru můžete použít příkaz [AzCopy vytvořit](storage-ref-azcopy-make.md) . Příklady v této části vytvoří kontejner s názvem `mycontainer`.

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Případě** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
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
> |Nahrajte soubory jako doplňovací objekty blob nebo objekty blob stránky.|**--blob-typ**=\[BlockBlob\|PageBlob\|AppendBlob\]|
> |Nahrajte na konkrétní úroveň přístupu (například na archivní úroveň).|**--Block-------úroveň**=\[objektu BLOB\|Hot\|studená\|\]|
> 
> Úplný seznam najdete v tématu [Možnosti](storage-ref-azcopy-copy.md#options).

### <a name="upload-a-file"></a>Nahrání souboru

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Případě** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

Soubor můžete také nahrát pomocí zástupného symbolu (*) kdekoli v cestě k souboru nebo v názvu souboru. Například: `'C:\myDirectory\*.txt'`nebo `C:\my*\*.txt`.

### <a name="upload-a-directory"></a>Odeslat adresář

Tento příklad zkopíruje adresář (a všechny soubory v tomto adresáři) do kontejneru objektů BLOB. Výsledkem je adresář v kontejneru se stejným názvem.

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Případě** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Pokud chcete kopírovat do adresáře v rámci kontejneru, stačí zadat název tohoto adresáře do řetězce příkazu.

|    |     |
|--------|-----------|
| **Případě** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

Pokud zadáte název adresáře, který v kontejneru neexistuje, vytvoří AzCopy nový adresář s tímto názvem.

### <a name="upload-the-contents-of-a-directory"></a>Nahrajte obsah adresáře.

Můžete nahrát obsah adresáře bez kopírování samotného nadřazeného adresáře pomocí zástupného znaku (*).

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Případě** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> Připojí `--recursive` příznak pro nahrání souborů ve všech podadresářích.

### <a name="upload-specific-files"></a>Odeslat konkrétní soubory

Můžete zadat úplný název souboru nebo použít částečné názvy se zástupnými znaky (*).

#### <a name="specify-multiple-complete-file-names"></a>Zadat více úplných názvů souborů

Použijte příkaz [AzCopy Copy](storage-ref-azcopy-copy.md) s `--include-path` možností. Jednotlivé názvy souborů oddělte pomocí středníku (`;`).

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Případě** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

V tomto příkladu AzCopy přenáší `C:\myDirectory\photos` adresář a `C:\myDirectory\documents\myFile.txt` soubor. Je nutné zahrnout `--recursive` možnost pro přenos všech souborů v `C:\myDirectory\photos` adresáři.

Soubory můžete také vyloučit pomocí `--exclude-path` možnosti. Další informace najdete v tématu [kopírování](storage-ref-azcopy-copy.md) referenčních dokumentů AzCopy.

#### <a name="use-wildcard-characters"></a>Použít zástupné znaky

Použijte příkaz [AzCopy Copy](storage-ref-azcopy-copy.md) s `--include-pattern` možností. Zadejte částečné názvy, které obsahují zástupné znaky. Oddělte názvy pomocí semicolin (`;`). 

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Případě** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

Soubory můžete také vyloučit pomocí `--exclude-pattern` možnosti. Další informace najdete v tématu [kopírování](storage-ref-azcopy-copy.md) referenčních dokumentů AzCopy.

Možnosti `--include-pattern` a `--exclude-pattern` se vztahují pouze na názvy souborů, nikoli na cestu.  Chcete-li zkopírovat všechny textové soubory, které existují ve stromové struktuře, použijte `–recursive` možnost pro získání celého adresářového stromu a pak použijte příkaz `–include-pattern` a zadejte `*.txt` , chcete-li získat všechny textové soubory.

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
> |Určete, jak chcete, aby byly položky protokolu související s kopírováním.|**--**=\[informace o\|\|chybě\|upozornění na úrovni protokolu None\]|
> |Určete, zda a jak přepsat konfliktní soubory a objekty BLOB v cíli.|**--přepsat**=\[true\|false\|ifSourceNewer\|Prompt\]|
> 
> Úplný seznam najdete v tématu [Možnosti](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Pokud hodnota `Content-md5` vlastnosti objektu BLOB obsahuje hodnotu hash, AzCopy vypočítá hodnotu hash MD5 pro stažená data a ověří, že hodnota hash MD5 uložená ve `Content-md5` vlastnosti objektu BLOB odpovídá počítané hodnotě hash. Pokud se tyto hodnoty neshodují, stažení se nezdaří, pokud toto chování `--check-md5=NoCheck` neprovedete `--check-md5=LogOnly` připojením nebo zkopírováním příkazu Kopírovat.

### <a name="download-a-file"></a>Stažení souboru

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Případě** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Stáhnout adresář

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Případě** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

Tento příklad vede k adresáři s názvem `C:\myDirectory\myBlobDirectory` , který obsahuje všechny stažené soubory.

### <a name="download-the-contents-of-a-directory"></a>Stažení obsahu adresáře

Obsah adresáře si můžete stáhnout bez zkopírování samotného obsahujícího adresáře pomocí zástupného znaku (*).

> [!NOTE]
> V současné době je tento scénář podporován pouze pro účty, které nemají hierarchický obor názvů.

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Případě** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> Přidejte `--recursive` příznak pro stažení souborů ve všech podadresářích.

### <a name="download-specific-files"></a>Stáhnout konkrétní soubory

Můžete zadat úplný název souboru nebo použít částečné názvy se zástupnými znaky (*).

#### <a name="specify-multiple-complete-file-names"></a>Zadat více úplných názvů souborů

Použijte příkaz [AzCopy Copy](storage-ref-azcopy-copy.md) s `--include-path` možností. Jednotlivé názvy souborů oddělte pomocí semicolin (`;`).

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Případě** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

V tomto příkladu AzCopy přenáší `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` adresář a `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` soubor. Je nutné zahrnout `--recursive` možnost pro přenos všech souborů v `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` adresáři.

Soubory můžete také vyloučit pomocí `--exclude-path` možnosti. Další informace najdete v tématu [kopírování](storage-ref-azcopy-copy.md) referenčních dokumentů AzCopy.

#### <a name="use-wildcard-characters"></a>Použít zástupné znaky

Použijte příkaz [AzCopy Copy](storage-ref-azcopy-copy.md) s `--include-pattern` možností. Zadejte částečné názvy, které obsahují zástupné znaky. Oddělte názvy pomocí semicolin (`;`).

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Případě** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Soubory můžete také vyloučit pomocí `--exclude-pattern` možnosti. Další informace najdete v tématu [kopírování](storage-ref-azcopy-copy.md) referenčních dokumentů AzCopy.

Možnosti `--include-pattern` a `--exclude-pattern` se vztahují pouze na názvy souborů, nikoli na cestu.  Chcete-li zkopírovat všechny textové soubory, které existují ve stromové struktuře, použijte `–recursive` možnost pro získání celého adresářového stromu a pak použijte příkaz `–include-pattern` a zadejte `*.txt` , chcete-li získat všechny textové soubory.

## <a name="copy-blobs-between-storage-accounts"></a>Kopírování objektů blob mezi účty úložiště

Pomocí AzCopy můžete kopírovat objekty blob do jiných účtů úložiště. Operace kopírování je synchronní, takže když příkaz vrátí, znamená to, že byly zkopírovány všechny soubory. 

AzCopy používá [server-to-server](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) [rozhraní API](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)pro servery, takže se data zkopírují přímo mezi úložnými servery. Tyto operace kopírování nepoužívají šířku pásma sítě vašeho počítače. Propustnost těchto operací můžete zvýšit nastavením hodnoty proměnné `AZCOPY_CONCURRENCY_VALUE` prostředí. Další informace najdete v tématu [optimalizace propustnosti](storage-use-azcopy-configure.md#optimize-throughput).

> [!NOTE]
> Tento scénář má v aktuální verzi následující omezení.
>
> - Je nutné připojit token SAS ke každé zdrojové adrese URL. Pokud přihlašovací údaje pro autorizaci zadáte pomocí Azure Active Directory (AD), můžete token SAS vynechat jenom z cílové adresy URL.
>-  Účty úložiště blob bloku úrovně Premium nepodporují úrovně přístupu. Vynechejte úroveň přístupu objektu BLOB z operace kopírování nastavením `s2s-preserve-access-tier` na `false` (například: `--s2s-preserve-access-tier=false`).

Tato část obsahuje následující příklady:

> [!div class="checklist"]
> * Kopírování objektu blob do jiného účtu úložiště
> * Zkopírování adresáře do jiného účtu úložiště
> * Kopírování kontejneru do jiného účtu úložiště
> * Kopírování všech kontejnerů, adresářů a souborů do jiného účtu úložiště

Tyto příklady také fungují s účty, které mají hierarchický obor názvů. [Přístup k více protokolům na data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) umožňuje použít stejnou syntaxi URL (`blob.core.windows.net`) na těchto účtech.

> [!TIP]
> Operaci kopírování můžete upravit pomocí volitelných příznaků. Tady je několik příkladů.
>
> |Scénář|Příznak|
> |---|---|
> |Zkopírujte soubory jako doplňovací objekty blob nebo objekty blob stránky.|**--blob-typ**=\[BlockBlob\|PageBlob\|AppendBlob\]|
> |Zkopírujte do konkrétní úrovně přístupu (například do archivní vrstvy).|**--Block-------úroveň**=\[objektu BLOB\|Hot\|studená\|\]|
> |Automatické dekomprimace souborů|**--dekomprese**=\[gzip\|a zúžené\]|
> 
> Úplný seznam najdete v tématu [Možnosti](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-blob-to-another-storage-account"></a>Kopírování objektu blob do jiného účtu úložiště

Použijte stejnou syntaxi adresy URL (`blob.core.windows.net`) pro účty, které mají hierarchický obor názvů.

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Případě** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Zkopírování adresáře do jiného účtu úložiště

Použijte stejnou syntaxi adresy URL (`blob.core.windows.net`) pro účty, které mají hierarchický obor názvů.

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Případě** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Příklad** (hierarchický obor názvů)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>Kopírování kontejneru do jiného účtu úložiště

Použijte stejnou syntaxi adresy URL (`blob.core.windows.net`) pro účty, které mají hierarchický obor názvů.

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Případě** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Příklad** (hierarchický obor názvů)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Kopírování všech kontejnerů, adresářů a objektů blob do jiného účtu úložiště

Použijte stejnou syntaxi adresy URL (`blob.core.windows.net`) pro účty, které mají hierarchický obor názvů.

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Případě** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **Příklad** (hierarchický obor názvů)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Synchronizovat soubory

Obsah místního systému souborů můžete synchronizovat s kontejnerem objektů BLOB. Můžete také synchronizovat kontejnery a virtuální adresáře mezi sebou. Synchronizace je jednosměrná. Jinými slovy, jste si zvolili, který z těchto dvou koncových bodů je zdroj a který je cílový. Synchronizace používá také server k rozhraní API serveru. Příklady uvedené v této části také fungují s účty, které mají hierarchický obor názvů. 

> [!NOTE]
> Aktuální verze AzCopy se nesynchronizuje mezi ostatními zdroji a cíli (například: File Storage nebo Amazon Web Services (AWS) S3).

Příkaz [synchronizovat](storage-ref-azcopy-sync.md) porovná názvy souborů a poslední upravená časová razítka. Nastavením `--delete-destination` volitelného příznaku na hodnotu `true` nebo `prompt` odstraňte soubory v cílovém adresáři, pokud už tyto soubory ve zdrojovém adresáři neexistují.

Pokud jste `--delete-destination` příznak nastavili `true` tak, aby AzCopy odstranit soubory bez zadání výzvy. Pokud chcete, aby se zobrazila výzva před tím, než AzCopy odstraní soubor `--delete-destination` , nastavte `prompt`příznak na.

> [!NOTE]
> Chcete-li zabránit nechtěnému odstranění, před použitím `--delete-destination=prompt|true` příznaku zajistěte, aby byla funkce [obnovitelného odstranění](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) povolena.

> [!TIP]
> Operaci synchronizace můžete upravit pomocí volitelných příznaků. Tady je několik příkladů.
>
> |Scénář|Příznak|
> |---|---|
> |Určete, jak mají být při stahování ověřovány striktní hodnoty hash MD5.|**--check-MD5**=\[\|\|FailIfDifferent\|Logon-check FailIfDifferentOrMissing\]|
> |Vyloučení souborů na základě vzoru.|**--Exclude-Path**|
> |Zadejte, jak chcete, aby byly položky protokolu související s synchronizací.|**--**=\[informace o\|\|chybě\|upozornění na úrovni protokolu None\]|
> 
> Úplný seznam najdete v tématu [Možnosti](storage-ref-azcopy-sync.md#options).

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Aktualizace kontejneru se změnami v místním systému souborů

V tomto případě je kontejnerem cíl a místní systém souborů je zdroj. 

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Případě** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Aktualizace místního systému souborů se změnami kontejneru

V tomto případě je místní systém souborů cílový a kontejner je zdroj.

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Případě** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>Aktualizace kontejneru se změnami v jiném kontejneru

Prvním kontejnerem, který se zobrazí v tomto příkazu, je zdroj. Druhá je cílová.

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Případě** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Aktualizace adresáře se změnami v adresáři v jiné sdílené složce

Prvním adresářem, který se zobrazí v tomto příkazu, je zdroj. Druhá je cílová.

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Případě** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>Další kroky

Další příklady najdete v některém z těchto článků:

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)

- [Kurz: Migrace místních dat do cloudového úložiště pomocí AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Přenos dat pomocí AzCopy a úložiště souborů](storage-use-azcopy-files.md)

- [Přenos dat pomocí kontejnerů AzCopy a Amazon S3](storage-use-azcopy-s3.md)

- [Konfigurace, optimalizace a řešení potíží s AzCopy](storage-use-azcopy-configure.md)
