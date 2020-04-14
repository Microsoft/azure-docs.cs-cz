---
title: Přenos dat do nebo ze souborů Azure pomocí AzCopy v10 | Dokumenty společnosti Microsoft
description: Přenos dat pomocí AzCopy a ukládání souborů.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 59f5733009424c60f2b9c48e68d70bbc29ad7095
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263365"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Přenos dat pomocí AzCopy a ukládání souborů 

AzCopy je nástroj příkazového řádku, který můžete použít ke kopírování objektů BLOB nebo souborů do nebo z účtu úložiště. Tento článek obsahuje ukázkové příkazy, které fungují se soubory Azure.

Než začnete, podívejte se na [článek Začínáme s AzCopy](storage-use-azcopy-v10.md) a stáhněte si AzCopy a seznamte se s nástrojem.

> [!TIP]
> Příklady v tomto článku uzavírají argumenty cesty s jednoduchými uvozovkami (''). Ve všech příkazových prostředích s výjimkou prostředí Windows Command Shell (cmd.exe) používejte jednoduché uvozovky. Pokud používáte prostředí Windows Command Shell (cmd.exe), uzavřete argumenty cesty s dvojitými uvozovkami ("") namísto jednoduchých uvozovek ('').

## <a name="create-file-shares"></a>Vytvoření sdílených složek

Příkaz [azcopy make](storage-ref-azcopy-make.md) můžete použít k vytvoření sdílené složky. Příklad v této části vytvoří `myfileshare`sdílenou složku s názvem .

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>'` |
| **Příklad** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Podrobné referenční dokumenty viz [azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Nahrání souborů

Příkaz [azcopy copy](storage-ref-azcopy-copy.md) můžete použít k nahrání souborů a adresářů z místního počítače.

Tato část obsahuje následující příklady:

> [!div class="checklist"]
> * Nahrání souboru
> * Nahrání adresáře
> * Nahrání obsahu adresáře
> * Nahrání konkrétního souboru

> [!TIP]
> Operaci nahrávání můžete vyladit pomocí volitelných příznaků. Zde je několik příkladů.
>
> |Scénář|Příznak|
> |---|---|
> |Zkopírujte seznamy řízení přístupu (ACL) spolu se soubory.|**--preserve-smb-permissions true false --preserve-smb-permissions true false --preserve-smb-permissions**=\[true\|false --preserve\]|
> |Zkopírujte informace o vlastnostech SMB spolu se soubory.|**--preserve-smb-info true false --preserve-smb-info true false --preserve-smb-info**=\[true\|false --preserve\]|
> |Nahrajte soubory jako objekty BLOB pro připojení nebo objekty BLOB stránky.|**--blob-type**=\[BlockBlob\|PageBlob\|AppendBlob --blob\]|
> |Nahrajte se na určitou úroveň přístupu (například archivní vrstvu).|**--block-blob-tier**=\[\|None\|\|Hot Cool Archiv\]|
> 
> Úplný seznam naleznete v tématu [možnosti](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> AzCopy automaticky nevypočítává a neukládá kód hash md5 souboru. Pokud chcete, aby to azCopy udělal, přidejte příznak ke každému příkazu `--put-md5` kopírování. Tímto způsobem, při stažení souboru AzCopy vypočítá hash MD5 pro stažená data a ověří, že hash MD5 uložený ve `Content-md5` vlastnosti souboru odpovídá vypočtené hash.

### <a name="upload-a-file"></a>Nahrání souboru

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'` |
| **Příklad** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Soubor můžete také nahrát pomocí zástupný symbol (*) kdekoli v cestě k souboru nebo názvu souboru. Například: `'C:\myDirectory\*.txt'`, `C:\my*\*.txt`nebo .

### <a name="upload-a-directory"></a>Nahrání adresáře

Tento příklad zkopíruje adresář (a všechny soubory v tomto adresáři) do sdílené složky. Výsledkem je adresář ve sdílené složce se stejným názvem.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Příklad** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Chcete-li kopírovat do adresáře ve sdílené složce, zadejte název tohoto adresáře v příkazovém řetězci.

|    |     |
|--------|-----------|
| **Příklad** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Pokud zadáte název adresáře, který ve sdílené složce neexistuje, AzCopy vytvoří nový adresář s tímto názvem.

### <a name="upload-the-contents-of-a-directory"></a>Nahrání obsahu adresáře

Obsah adresáře můžete nahrát bez kopírování samotného obsahujícího adresáře pomocí zástupného symbolu (*).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>` |
| **Příklad** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Připojte `--recursive` příznak pro nahrání souborů ve všech podadresářích.

### <a name="upload-specific-files"></a>Nahrání konkrétních souborů

Můžete zadat úplné názvy souborů nebo použít částečné názvy se zástupnými znaky (*).

#### <a name="specify-multiple-complete-file-names"></a>Určení více úplných názvů souborů

Použijte příkaz [azcopy](storage-ref-azcopy-copy.md) copy `--include-path` s volbou. Oddělte jednotlivé názvy souborů`;`pomocí středníku ( ).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>` |
| **Příklad** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

V tomto příkladu AzCopy `C:\myDirectory\photos` přenáší `C:\myDirectory\documents\myFile.txt` adresář a soubor. Je třeba zahrnout `--recursive` možnost přenosu všech `C:\myDirectory\photos` souborů v adresáři.

Soubory můžete také vyloučit `--exclude-path` pomocí možnosti. Další informace najdete [v tématu azcopy copy copy](storage-ref-azcopy-copy.md) reference docs.

#### <a name="use-wildcard-characters"></a>Použití zástupných znaků

Použijte příkaz [azcopy](storage-ref-azcopy-copy.md) copy `--include-pattern` s volbou. Zadejte částečné názvy, které obsahují zástupné znaky. Samostatné názvy pomocí semicolin (`;`).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Příklad** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

Soubory můžete také vyloučit `--exclude-pattern` pomocí možnosti. Další informace najdete [v tématu azcopy copy copy](storage-ref-azcopy-copy.md) reference docs.

`--include-pattern` Volby `--exclude-pattern` a platí pouze pro názvy souborů a nikoli na cestu.  Pokud chcete zkopírovat všechny textové soubory, které existují ve `–recursive` stromu adresářů, použijte možnost `–include-pattern` získat `*.txt` celý adresářový strom a pak použijte a určete získat všechny textové soubory.

## <a name="download-files"></a>Stažení souborů

Příkaz [azcopy copy](storage-ref-azcopy-copy.md) můžete použít ke stažení souborů, adresářů a sdílených složek do místního počítače.

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
> |Zkopírujte seznamy řízení přístupu (ACL) spolu se soubory.|**--preserve-smb-permissions true false --preserve-smb-permissions true false --preserve-smb-permissions**=\[true\|false --preserve\]|
> |Zkopírujte informace o vlastnostech SMB spolu se soubory.|**--preserve-smb-info true false --preserve-smb-info true false --preserve-smb-info**=\[true\|false --preserve\]|
> |Automaticky dekomprimovat soubory.|**--dekomprimovat**=\[gzip\|vyfouknout\]|
> 
> Úplný seznam naleznete v tématu [možnosti](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Pokud `Content-md5` hodnota vlastnosti souboru obsahuje hodnotu hash, AzCopy vypočítá hodnotu hash MD5 pro stažená data a `Content-md5` ověří, zda hodnota hash MD5 uložená ve vlastnosti souboru odpovídá vypočtené hodnotě hash. Pokud se tyto hodnoty neshodují, stahování se nezdaří, `--check-md5=NoCheck` pokud `--check-md5=LogOnly` toto chování nepřepíšete připojením nebo příkazem copy.

### <a name="download-a-file"></a>Stažení souboru

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Stažení adresáře

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

Výsledkem tohoto příkladu `C:\myDirectory\myFileShareDirectory` je adresář s názvem, který obsahuje všechny stažené soubory.

### <a name="download-the-contents-of-a-directory"></a>Stažení obsahu adresáře

Obsah adresáře můžete stáhnout bez kopírování samotného obsahujícího adresáře pomocí zástupného symbolu (*).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> Připojte `--recursive` příznak pro stahování souborů ve všech podadresářích.

### <a name="download-specific-files"></a>Stažení konkrétních souborů

Můžete zadat úplné názvy souborů nebo použít částečné názvy se zástupnými znaky (*).

#### <a name="specify-multiple-complete-file-names"></a>Určení více úplných názvů souborů

Použijte příkaz [azcopy](storage-ref-azcopy-copy.md) copy `--include-path` s volbou. Samostatné názvy jednotlivých souborů`;`pomocí semicolin ( ).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

V tomto příkladu AzCopy `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` přenáší `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` adresář a soubor. Je třeba zahrnout `--recursive` možnost přenosu všech `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` souborů v adresáři.

Soubory můžete také vyloučit `--exclude-path` pomocí možnosti. Další informace najdete [v tématu azcopy copy copy](storage-ref-azcopy-copy.md) reference docs.

#### <a name="use-wildcard-characters"></a>Použití zástupných znaků

Použijte příkaz [azcopy](storage-ref-azcopy-copy.md) copy `--include-pattern` s volbou. Zadejte částečné názvy, které obsahují zástupné znaky. Samostatné názvy pomocí semicolin (`;`).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Soubory můžete také vyloučit `--exclude-pattern` pomocí možnosti. Další informace najdete [v tématu azcopy copy copy](storage-ref-azcopy-copy.md) reference docs.

`--include-pattern` Volby `--exclude-pattern` a platí pouze pro názvy souborů a nikoli na cestu.  Pokud chcete zkopírovat všechny textové soubory, které existují ve `–recursive` stromu adresářů, použijte možnost `–include-pattern` získat `*.txt` celý adresářový strom a pak použijte a určete získat všechny textové soubory.

## <a name="copy-files-between-storage-accounts"></a>Kopírovat soubory mezi účty úložiště

AzCopy můžete použít ke kopírování souborů do jiných účtů úložiště. Operace kopírování je synchronní, takže když příkaz vrátí, který označuje, že všechny soubory byly zkopírovány.

AzCopy používá [api](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url) [mezi servery](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) , takže data jsou zkopírována přímo mezi servery úložiště. Tyto operace kopírování nepoužívají šířku pásma sítě počítače. Propustnost těchto operací můžete zvýšit nastavením `AZCOPY_CONCURRENCY_VALUE` hodnoty proměnné prostředí. Další informace naleznete v [tématu Optimalizace propustnost .](storage-use-azcopy-configure.md#optimize-throughput)

Tato část obsahuje následující příklady:

> [!div class="checklist"]
> * Kopírování souboru do jiného účtu úložiště
> * Kopírování adresáře do jiného účtu úložiště
> * Kopírování sdílené složky do jiného účtu úložiště
> * Kopírování všech sdílených složek, adresářů a souborů do jiného účtu úložiště

> [!TIP]
> Operaci kopírování můžete vyladit pomocí volitelných příznaků. Zde je několik příkladů.
>
> |Scénář|Příznak|
> |---|---|
> |Zkopírujte seznamy řízení přístupu (ACL) spolu se soubory.|**--preserve-smb-permissions true false --preserve-smb-permissions true false --preserve-smb-permissions**=\[true\|false --preserve\]|
> |Zkopírujte informace o vlastnostech SMB spolu se soubory.|**--preserve-smb-info true false --preserve-smb-info true false --preserve-smb-info**=\[true\|false --preserve\]|
> |Zkopírujte soubory jako objekty BLOB pro připojení nebo objekty BLOB stránky.|**--blob-type**=\[BlockBlob\|PageBlob\|AppendBlob --blob\]|
> |Zkopírujte do určité úrovně přístupu (například archivní vrstvy).|**--block-blob-tier**=\[\|None\|\|Hot Cool Archiv\]|
> 
> Úplný seznam naleznete v tématu [možnosti](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-file-to-another-storage-account"></a>Kopírování souboru do jiného účtu úložiště

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'` |
| **Příklad** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Kopírování adresáře do jiného účtu úložiště

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Příklad** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>Kopírování sdílené složky do jiného účtu úložiště

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Příklad** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Kopírování všech sdílených složek, adresářů a souborů do jiného účtu úložiště

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'` |
| **Příklad** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="synchronize-files"></a>Synchronizace souborů

Obsah sdílené složky můžete synchronizovat s jinou sdílenou složkou. Obsah adresáře můžete také synchronizovat ve sdílené složce s obsahem adresáře, který je umístěn v jiné sdílené složce. Synchronizace je jednosměrná. Jinými slovy, můžete zvolit, který z těchto dvou koncových bodů je zdroj a který z nich je cíl. Synchronizace také používá server k serveru API.

> [!NOTE]
> V současné době je tento scénář podporován pouze pro účty, které nemají hierarchický obor názvů. Aktuální verze AzCopy se nesynchronizuje mezi soubory Azure a úložištěm objektů blob.

Příkaz [synchronizace](storage-ref-azcopy-sync.md) porovnává názvy souborů a naposledy změněné časová razítka. Pokud `--delete-destination` tyto soubory ve `true` zdrojovém adresáři již neexistují, nastavte volitelný příznak na hodnotu nebo `prompt` odstraňte soubory v cílovém adresáři.

Pokud nastavíte `--delete-destination` `true` příznak AzCopy odstraní soubory bez zadání výzvy. Pokud chcete, aby se výzva objevila před odstraněním `--delete-destination` souboru `prompt`azCopy, nastavte příznak na .

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

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Aktualizace sdílené složky se změnami v jiné sdílené složce

První sdílená složka, která se zobrazí v tomto příkazu, je zdrojem. Druhý je cíl.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Příklad** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Aktualizace adresáře se změnami v adresáři v jiné sdílené složce

První adresář, který se zobrazí v tomto příkazu je zdroj. Druhý je cíl.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive` |
| **Příklad** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-file-share-to-match-the-contents-of-a-share-snapshot"></a>Aktualizace sdílené složky tak, aby odpovídala obsahu snímku sdílené složky

První sdílená složka, která se zobrazí v tomto příkazu, je zdrojem. Na konci identifikátoru URI připojujte řetězec `&sharesnapshot=` následovaný hodnotou **DateTime** snímku. 

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>&sharesnapsot<snapshot-ID>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Příklad** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-03-03T20%3A24%3A13.0000000Z' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

Další informace o snímcích sdílené složky najdete v [tématu Přehled snímků sdílené složky pro soubory Azure](https://docs.microsoft.com/azure/storage/files/storage-snapshots-files).

## <a name="next-steps"></a>Další kroky

Další příklady najdete v některém z těchto článků:

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)

- [Přenos dat pomocí úložiště Objektů blob AzCopy a objektů blob](storage-use-azcopy-blobs.md)

- [Přenos dat pomocí kbelíků AzCopy a Amazon S3](storage-use-azcopy-s3.md)

- [Konfigurace, optimalizace a řešení potíží s azcopy](storage-use-azcopy-configure.md)
