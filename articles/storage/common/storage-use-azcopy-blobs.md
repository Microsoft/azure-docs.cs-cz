---
title: Přenos dat do úložiště objektů Blob Azure nebo z nich pomocí AzCopy v10 | Dokumenty společnosti Microsoft
description: Tento článek obsahuje kolekci příkazů AzCopy příklad, které vám pomohou vytvářet kontejnery, kopírovat soubory a synchronizovat adresáře mezi místními systémy souborů a kontejnery.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 73685f124f93bb541f33b3b70727d90ce22b3cdd
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263433"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Přenos dat pomocí úložiště AzCopy a Blob

AzCopy je nástroj příkazového řádku, který můžete použít ke kopírování dat do účtů úložiště, z nich nebo mezi nimi. Tento článek obsahuje ukázkové příkazy, které fungují s úložištěm objektů Blob.

> [!TIP]
> Příklady v tomto článku uzavírají argumenty cesty s jednoduchými uvozovkami (''). Ve všech příkazových prostředích s výjimkou prostředí Windows Command Shell (cmd.exe) používejte jednoduché uvozovky. Pokud používáte prostředí Windows Command Shell (cmd.exe), uzavřete argumenty cesty s dvojitými uvozovkami ("") namísto jednoduchých uvozovek ('').

## <a name="get-started"></a>Začínáme

Podívejte se na [článek Začínáme s AzCopy,](storage-use-azcopy-v10.md) kde si můžete stáhnout AzCopy a dozvědět se o způsobech, jak můžete službě úložiště poskytnout pověření k autorizaci.

> [!NOTE]
> Příklady v tomto článku předpokládají, že jste ověřili `AzCopy login` identitu pomocí příkazu. AzCopy pak používá váš účet Azure AD k autorizaci přístupu k datům v úložišti objektů Blob.
>
> Pokud chcete raději použít token SAS k autorizaci přístupu k datům objektů blob, můžete tento token připojit k adrese URL prostředku v každém příkazu AzCopy.
>
> Například: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Vytvoření kontejneru

Můžete použít [příkaz azcopy make](storage-ref-azcopy-make.md) k vytvoření kontejneru. Příklady v této části vytvoří `mycontainer`kontejner s názvem .

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Příklad** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Příklad** (hierarchický obor názvů) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

Podrobné referenční dokumenty viz [azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Nahrání souborů

Příkaz [azcopy copy](storage-ref-azcopy-copy.md) můžete použít k nahrání souborů a adresářů z místního počítače.

Tato část obsahuje následující příklady:

> [!div class="checklist"]
> * Nahrání souboru
> * Nahrání adresáře
> * Nahrání obsahu adresáře 
> * Nahrání konkrétních souborů

> [!TIP]
> Operaci nahrávání můžete vyladit pomocí volitelných příznaků. Zde je několik příkladů.
>
> |Scénář|Příznak|
> |---|---|
> |Nahrajte soubory jako objekty BLOB pro připojení nebo objekty BLOB stránky.|**--blob-type**=\[BlockBlob\|PageBlob\|AppendBlob --blob\]|
> |Nahrajte se na určitou úroveň přístupu (například archivní vrstvu).|**--block-blob-tier**=\[\|None\|\|Hot Cool Archiv\]|
> |Automaticky dekomprimovat soubory.|**--dekomprimovat**=\[gzip\|vyfouknout\]|
> 
> Úplný seznam naleznete v tématu [možnosti](storage-ref-azcopy-copy.md#options).

### <a name="upload-a-file"></a>Nahrání souboru

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Příklad** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

Soubor můžete také nahrát pomocí zástupný symbol (*) kdekoli v cestě k souboru nebo názvu souboru. Například: `'C:\myDirectory\*.txt'`, `C:\my*\*.txt`nebo .

### <a name="upload-a-directory"></a>Nahrání adresáře

Tento příklad zkopíruje adresář (a všechny soubory v tomto adresáři) do kontejneru objektů blob. Výsledkem je adresář v kontejneru se stejným názvem.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Příklad** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Chcete-li kopírovat do adresáře v kontejneru, zadejte název tohoto adresáře v příkazovém řetězci.

|    |     |
|--------|-----------|
| **Příklad** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

Pokud zadáte název adresáře, který v kontejneru neexistuje, AzCopy vytvoří nový adresář s tímto názvem.

### <a name="upload-the-contents-of-a-directory"></a>Nahrání obsahu adresáře

Obsah adresáře můžete nahrát bez kopírování samotného obsahujícího adresáře pomocí zástupného symbolu (*).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Příklad** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> Připojte `--recursive` příznak pro nahrání souborů ve všech podadresářích.

### <a name="upload-specific-files"></a>Nahrání konkrétních souborů

Můžete zadat úplné názvy souborů nebo použít částečné názvy se zástupnými znaky (*).

#### <a name="specify-multiple-complete-file-names"></a>Určení více úplných názvů souborů

Použijte příkaz [azcopy](storage-ref-azcopy-copy.md) copy `--include-path` s volbou. Oddělte jednotlivé názvy souborů`;`pomocí středníku ( ).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Příklad** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

V tomto příkladu AzCopy `C:\myDirectory\photos` přenáší `C:\myDirectory\documents\myFile.txt` adresář a soubor. Je třeba zahrnout `--recursive` možnost přenosu všech `C:\myDirectory\photos` souborů v adresáři.

Soubory můžete také vyloučit `--exclude-path` pomocí možnosti. Další informace najdete [v tématu azcopy copy copy](storage-ref-azcopy-copy.md) reference docs.

#### <a name="use-wildcard-characters"></a>Použití zástupných znaků

Použijte příkaz [azcopy](storage-ref-azcopy-copy.md) copy `--include-pattern` s volbou. Zadejte částečné názvy, které obsahují zástupné znaky. Samostatné názvy pomocí semicolin (`;`). 

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Příklad** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

Soubory můžete také vyloučit `--exclude-pattern` pomocí možnosti. Další informace najdete [v tématu azcopy copy copy](storage-ref-azcopy-copy.md) reference docs.

`--include-pattern` Volby `--exclude-pattern` a platí pouze pro názvy souborů a nikoli na cestu.  Pokud chcete zkopírovat všechny textové soubory, které existují ve `–recursive` stromu adresářů, použijte možnost `–include-pattern` získat `*.txt` celý adresářový strom a pak použijte a určete získat všechny textové soubory.

## <a name="download-files"></a>Stažení souborů

Příkaz [azcopy copy](storage-ref-azcopy-copy.md) můžete použít ke stažení objektů BLOB, adresářů a kontejnerů do místního počítače.

Tato část obsahuje následující příklady:

> [!div class="checklist"]
> * Stažení souboru
> * Stažení adresáře
> * Stažení obsahu adresáře
> * Stažení konkrétních souborů

> [!TIP]
> Operaci stahování můžete vyladit pomocí volitelných příznaků. Zde je několik příkladů.
>
> |Scénář|Příznak|
> |---|---|
> |Automaticky dekomprimovat soubory.|**--dekomprimovat**=\[gzip\|vyfouknout\]|
> |Určete, jak podrobné mají být položky protokolu související s kopírováním.|**--log-level**=\[\|WARNING\|\|ERROR INFO NONE --log-level WARNING ERROR INFO NONE --log-level WARNING ERROR INFO NONE --log\]|
> |Určete, zda a jak přepsat konfliktní soubory a objekty BLOB v cílovém umístění.|**--přepsat**=\[true\|\|false ifSourceNewer\|prompt\]|
> 
> Úplný seznam naleznete v tématu [možnosti](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Pokud `Content-md5` hodnota vlastnosti objektu blob obsahuje hodnotu hash, AzCopy vypočítá hodnotu hash MD5 pro stažená data a `Content-md5` ověří, zda hodnota hash MD5 uložená ve vlastnosti objektu blob odpovídá vypočtené hodnotě hash. Pokud se tyto hodnoty neshodují, stahování se nezdaří, `--check-md5=NoCheck` pokud `--check-md5=LogOnly` toto chování nepřepíšete připojením nebo příkazem copy.

### <a name="download-a-file"></a>Stažení souboru

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Stažení adresáře

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

Výsledkem tohoto příkladu `C:\myDirectory\myBlobDirectory` je adresář s názvem, který obsahuje všechny stažené soubory.

### <a name="download-the-contents-of-a-directory"></a>Stažení obsahu adresáře

Obsah adresáře můžete stáhnout bez kopírování samotného obsahujícího adresáře pomocí zástupného symbolu (*).

> [!NOTE]
> V současné době je tento scénář podporován pouze pro účty, které nemají hierarchický obor názvů.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> Připojte `--recursive` příznak pro stahování souborů ve všech podadresářích.

### <a name="download-specific-files"></a>Stažení konkrétních souborů

Můžete zadat úplné názvy souborů nebo použít částečné názvy se zástupnými znaky (*).

#### <a name="specify-multiple-complete-file-names"></a>Určení více úplných názvů souborů

Použijte příkaz [azcopy](storage-ref-azcopy-copy.md) copy `--include-path` s volbou. Samostatné názvy jednotlivých souborů`;`pomocí semicolin ( ).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

V tomto příkladu AzCopy `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` přenáší `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` adresář a soubor. Je třeba zahrnout `--recursive` možnost přenosu všech `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` souborů v adresáři.

Soubory můžete také vyloučit `--exclude-path` pomocí možnosti. Další informace najdete [v tématu azcopy copy copy](storage-ref-azcopy-copy.md) reference docs.

#### <a name="use-wildcard-characters"></a>Použití zástupných znaků

Použijte příkaz [azcopy](storage-ref-azcopy-copy.md) copy `--include-pattern` s volbou. Zadejte částečné názvy, které obsahují zástupné znaky. Samostatné názvy pomocí semicolin (`;`).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Soubory můžete také vyloučit `--exclude-pattern` pomocí možnosti. Další informace najdete [v tématu azcopy copy copy](storage-ref-azcopy-copy.md) reference docs.

`--include-pattern` Volby `--exclude-pattern` a platí pouze pro názvy souborů a nikoli na cestu.  Pokud chcete zkopírovat všechny textové soubory, které existují ve `–recursive` stromu adresářů, použijte možnost `–include-pattern` získat `*.txt` celý adresářový strom a pak použijte a určete získat všechny textové soubory.

## <a name="copy-blobs-between-storage-accounts"></a>Kopírování objektů blob mezi účty úložiště

AzCopy můžete použít ke kopírování objektů BLOB do jiných účtů úložiště. Operace kopírování je synchronní, takže když příkaz vrátí, který označuje, že všechny soubory byly zkopírovány. 

AzCopy používá [api](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url) [mezi servery](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) , takže data jsou zkopírována přímo mezi servery úložiště. Tyto operace kopírování nepoužívají šířku pásma sítě počítače. Propustnost těchto operací můžete zvýšit nastavením `AZCOPY_CONCURRENCY_VALUE` hodnoty proměnné prostředí. Další informace naleznete v [tématu Optimalizace propustnost .](storage-use-azcopy-configure.md#optimize-throughput)

> [!NOTE]
> Tento scénář má následující omezení v aktuální verzi.
>
> - Ke každé zdrojové adrese URL je třeba připojit token SAS. Pokud zadáte pověření autorizace pomocí služby Azure Active Directory (AD), můžete vynechat token SAS pouze z cílové adresy URL.
>-  Účty úložiště objektů blob s prémiovými bloky nepodporují úrovně přístupu. Vynechte úroveň přístupu objektu blob `s2s-preserve-access-tier` z `false` operace kopírování `--s2s-preserve-access-tier=false`nastavením na (Například: ).

Tato část obsahuje následující příklady:

> [!div class="checklist"]
> * Kopírování objektu blob do jiného účtu úložiště
> * Kopírování adresáře do jiného účtu úložiště
> * Kopírování kontejneru do jiného účtu úložiště
> * Kopírování všech kontejnerů, adresářů a souborů do jiného účtu úložiště

Tyto příklady také pracují s účty, které mají hierarchický obor názvů. [Víceprotokolový přístup v úložišti Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) `blob.core.windows.net`umožňuje používat u těchto účtů stejnou syntaxi adresy URL ( ).

> [!TIP]
> Operaci kopírování můžete vyladit pomocí volitelných příznaků. Zde je několik příkladů.
>
> |Scénář|Příznak|
> |---|---|
> |Zkopírujte soubory jako objekty BLOB pro připojení nebo objekty BLOB stránky.|**--blob-type**=\[BlockBlob\|PageBlob\|AppendBlob --blob\]|
> |Zkopírujte do určité úrovně přístupu (například archivní vrstvy).|**--block-blob-tier**=\[\|None\|\|Hot Cool Archiv\]|
> |Automaticky dekomprimovat soubory.|**--dekomprimovat**=\[gzip\|vyfouknout\]|
> 
> Úplný seznam naleznete v tématu [možnosti](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-blob-to-another-storage-account"></a>Kopírování objektu blob do jiného účtu úložiště

Pro účty, které`blob.core.windows.net`mají hierarchický obor názvů, použijte stejnou syntaxi adresy URL ( ).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Příklad** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Kopírování adresáře do jiného účtu úložiště

Pro účty, které`blob.core.windows.net`mají hierarchický obor názvů, použijte stejnou syntaxi adresy URL ( ).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Příklad** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Příklad** (hierarchický obor názvů)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>Kopírování kontejneru do jiného účtu úložiště

Pro účty, které`blob.core.windows.net`mají hierarchický obor názvů, použijte stejnou syntaxi adresy URL ( ).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Příklad** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Příklad** (hierarchický obor názvů)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Kopírování všech kontejnerů, adresářů a objektů BLOB do jiného účtu úložiště

Pro účty, které`blob.core.windows.net`mají hierarchický obor názvů, použijte stejnou syntaxi adresy URL ( ).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Příklad** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **Příklad** (hierarchický obor názvů)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Synchronizace souborů

Obsah místního systému souborů můžete synchronizovat s kontejnerem objektů blob. Můžete také vzájemně synchronizovat kontejnery a virtuální adresáře. Synchronizace je jednosměrná. Jinými slovy, můžete zvolit, který z těchto dvou koncových bodů je zdroj a který z nich je cíl. Synchronizace také používá server k serveru API. Příklady uvedené v této části také pracovat s účty, které mají hierarchický obor názvů. 

> [!NOTE]
> Aktuální verze AzCopy se nesynchronizuje mezi jinými zdroji a cíli (například: Úložiště souborů nebo Kontejnery S3 (Amazon Web Services ( AWS).

Příkaz [synchronizace](storage-ref-azcopy-sync.md) porovnává názvy souborů a naposledy změněné časová razítka. Pokud `--delete-destination` tyto soubory ve `true` zdrojovém adresáři již neexistují, nastavte volitelný příznak na hodnotu nebo `prompt` odstraňte soubory v cílovém adresáři.

Pokud nastavíte `--delete-destination` `true` příznak AzCopy odstraní soubory bez zadání výzvy. Pokud chcete, aby se výzva objevila před odstraněním `--delete-destination` souboru `prompt`azCopy, nastavte příznak na .

> [!NOTE]
> Chcete-li zabránit nechtěnému odstranění, před použitím příznaku `--delete-destination=prompt|true` nezapomeňte povolit funkci [obnovitelného odstranění.](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)

> [!TIP]
> Operaci synchronizace můžete vyladit pomocí volitelných příznaků. Zde je několik příkladů.
>
> |Scénář|Příznak|
> |---|---|
> |Určete, jak přísně md5 hashe má být ověřena při stahování.|**--check-md5**=\[NoCheck LogOnly FailIfDifferent FailIfDifferentOrMissing --check-md5 NoCheck LogOnly FailIfDifferentIfDifferentOrMissing --check-md5 NoCheck\|LogOnly\|FailIfDifferent\|FailIfDifferentOrMissing --check-\]|
> |Vylučte soubory na základě vzorku.|**--vyloučit-cesta**|
> |Určete, jak podrobné mají být položky protokolu související se synchronizací.|**--log-level**=\[\|WARNING\|\|ERROR INFO NONE --log-level WARNING ERROR INFO NONE --log-level WARNING ERROR INFO NONE --log\]|
> 
> Úplný seznam naleznete v tématu [možnosti](storage-ref-azcopy-sync.md#options).

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Aktualizace kontejneru se změnami v místním systému souborů

V tomto případě je cílem kontejneru a zdrojem je místní systém souborů. 

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Příklad** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Aktualizace místního systému souborů se změnami kontejneru

V tomto případě je cílem místní systém souborů a zdroj je kontejner.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Příklad** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>Aktualizace kontejneru se změnami v jiném kontejneru

První kontejner, který se zobrazí v tomto příkazu je zdroj. Druhý je cíl.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Příklad** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Aktualizace adresáře se změnami v adresáři v jiné sdílené složce

První adresář, který se zobrazí v tomto příkazu je zdroj. Druhý je cíl.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Příklad** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>Další kroky

Další příklady najdete v některém z těchto článků:

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)

- [Kurz: Migrace místních dat do cloudového úložiště pomocí AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Přenos dat pomocí AzCopy a ukládání souborů](storage-use-azcopy-files.md)

- [Přenos dat pomocí kbelíků AzCopy a Amazon S3](storage-use-azcopy-s3.md)

- [Konfigurace, optimalizace a řešení potíží s azcopy](storage-use-azcopy-configure.md)
