---
title: Přenos dat do nebo ze souborů Azure pomocí AzCopy v10 za účelem | Microsoft Docs
description: Přenos dat pomocí AzCopy a úložiště souborů
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 361b16ff074baaf0118ccfe6d3c2a20f0e66c623
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273906"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Přenos dat pomocí AzCopy a úložiště souborů 

AzCopy je nástroj příkazového řádku, který můžete použít ke kopírování objektů BLOB nebo souborů do nebo z účtu úložiště. Tento článek obsahuje příklady příkazů, které fungují se soubory Azure.

Než začnete, přečtěte si článek Začínáme [s AzCopy](storage-use-azcopy-v10.md) ke stažení AzCopy a seznámení s nástrojem.

## <a name="create-file-shares"></a>Vytvoření sdílených složek

K vytvoření sdílené složky můžete použít příkaz [AzCopy vytvořit](storage-ref-azcopy-make.md) . V příkladu v této části se vytvoří sdílená složka s názvem `myfileshare`.

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>'` |
| **Příklad** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Podrobné referenční dokumentaci najdete v tématu [AzCopy](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Nahrání souborů

Příkaz [AzCopy Copy](storage-ref-azcopy-copy.md) můžete použít k nahrání souborů a adresářů z místního počítače.

Tato část obsahuje následující příklady:

> [!div class="checklist"]
> * Nahrání souboru
> * Odeslat adresář
> * Nahrajte obsah adresáře.
> * Nahrání konkrétního souboru

> [!NOTE]
> AzCopy automaticky nepočítá a neukládá kód hash MD5 souboru. Pokud to chcete provést v AzCopy, přidejte k jednotlivým příkazům kopírování příznak `--put-md5`. Tímto způsobem AzCopy při stažení souboru vypočítá hodnotu hash MD5 pro stažená data a ověří, že hodnota hash MD5 uložená ve vlastnosti-0 souboru @no__t je shodná s vypočtenou hodnotou hash.

Podrobné referenční dokumentace najdete v tématu [AzCopy Copy](storage-ref-azcopy-copy.md).

### <a name="upload-a-file"></a>Nahrání souboru

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>'` |
| **Příklad** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Soubor můžete také nahrát pomocí zástupného symbolu (*) kdekoli v cestě k souboru nebo v názvu souboru. Například: `'C:\myDirectory\*.txt'` nebo `C:\my*\*.txt`.

### <a name="upload-a-directory"></a>Odeslat adresář

Tento příklad zkopíruje adresář (a všechny soubory v tomto adresáři) do sdílené složky. Výsledkem je adresář ve sdílené složce se stejným názvem.

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **Příklad** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Pokud chcete kopírovat do adresáře ve sdílené složce, stačí zadat název tohoto adresáře do řetězce příkazu.

|    |     |
|--------|-----------|
| **Příklad** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Pokud zadáte název adresáře, který ve sdílené složce neexistuje, vytvoří AzCopy nový adresář s tímto názvem.

### <a name="upload-the-contents-of-a-directory"></a>Nahrajte obsah adresáře.

Můžete nahrát obsah adresáře bez kopírování samotného nadřazeného adresáře pomocí zástupného znaku (*).

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **Příklad** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Pokud chcete nahrávat soubory ve všech podadresářích, přidejte příznak `--recursive`.

### <a name="upload-specific-files"></a>Odeslat konkrétní soubory

Můžete zadat úplný název souboru nebo použít částečné názvy se zástupnými znaky (*).

#### <a name="specify-multiple-complete-file-names"></a>Zadat více úplných názvů souborů

Použijte příkaz [AzCopy Copy](storage-ref-azcopy-copy.md) s možností `--include-path`. Jednotlivé názvy souborů oddělte středníkem (`;`).

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>' --include-path <semicolon-separated-file-list>` |
| **Příklad** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare' --include-path 'photos;documents\myFile.txt'` |

V tomto příkladu AzCopy přenáší adresář `C:\myDirectory\photos` a soubor `C:\myDirectory\documents\myFile.txt`. Aby bylo možné přenést všechny soubory v adresáři `C:\myDirectory\photos`, je nutné zahrnout možnost `--recursive`.

Soubory můžete také vyloučit pomocí možnosti `--exclude-path`. Další informace najdete v tématu [kopírování](storage-ref-azcopy-copy.md) referenčních dokumentů AzCopy.

#### <a name="use-wildcard-characters"></a>Použít zástupné znaky

Použijte příkaz [AzCopy Copy](storage-ref-azcopy-copy.md) s možností `--include-pattern`. Zadejte částečné názvy, které obsahují zástupné znaky. Oddělte názvy pomocí semicolin (`;`).

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Příklad** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare' --include-pattern 'myFile*.txt;*.pdf*'` |

Soubory můžete také vyloučit pomocí možnosti `--exclude-pattern`. Další informace najdete v tématu [kopírování](storage-ref-azcopy-copy.md) referenčních dokumentů AzCopy.

Možnosti `--include-pattern` a `--exclude-pattern` se vztahují pouze na názvy souborů, nikoli na cestu.  Pokud chcete zkopírovat všechny textové soubory, které existují ve stromové struktuře, použijte možnost `–recursive` pro získání celého adresářového stromu a pak použijte `–include-pattern` a zadáním `*.txt` získáte všechny textové soubory.

## <a name="download-files"></a>Stažení souborů

Pomocí příkazu [AzCopy Copy](storage-ref-azcopy-copy.md) můžete stáhnout soubory, adresáře a sdílené složky do místního počítače.

Tato část obsahuje následující příklady:

> [!div class="checklist"]
> * Stažení souboru
> * Stáhnout adresář
> * Stažení obsahu adresáře
> * Stáhnout konkrétní soubory

> [!NOTE]
> Pokud hodnota vlastnosti `Content-md5` souboru obsahuje hodnotu hash, nástroj AzCopy vypočítá hodnotu hash MD5 pro stažená data a ověří, že hodnota hash MD5 uložená v vlastnosti `Content-md5` souboru odpovídá počítané hodnotě hash. Pokud se tyto hodnoty neshodují, stažení se nezdaří, pokud toto chování neprovedete připojením `--check-md5=NoCheck` nebo `--check-md5=LogOnly` do příkazu Copy.

Podrobné referenční dokumentace najdete v tématu [AzCopy Copy](storage-ref-azcopy-copy.md).

### <a name="download-a-file"></a>Stažení souboru

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' '<local-file-path>'` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Stáhnout adresář

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' '<local-directory-path>' --recursive` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

V tomto příkladu je výsledkem adresář s názvem `C:\myDirectory\myFileShareDirectory`, který obsahuje všechny stažené soubory.

### <a name="download-the-contents-of-a-directory"></a>Stažení obsahu adresáře

Obsah adresáře si můžete stáhnout bez zkopírování samotného obsahujícího adresáře pomocí zástupného znaku (*).

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>' '<local-directory-path>/'` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> Pokud chcete stáhnout soubory ve všech podadresářích, přidejte příznak `--recursive`.

### <a name="download-specific-files"></a>Stáhnout konkrétní soubory

Můžete zadat úplný název souboru nebo použít částečné názvy se zástupnými znaky (*).

#### <a name="specify-multiple-complete-file-names"></a>Zadat více úplných názvů souborů

Použijte příkaz [AzCopy Copy](storage-ref-azcopy-copy.md) s možností `--include-path`. Jednotlivé názvy souborů oddělte pomocí semicolin (`;`).

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

V tomto příkladu AzCopy přenáší adresář `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` a soubor `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt`. Aby bylo možné přenést všechny soubory v adresáři `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos`, je nutné zahrnout možnost `--recursive`.

Soubory můžete také vyloučit pomocí možnosti `--exclude-path`. Další informace najdete v tématu [kopírování](storage-ref-azcopy-copy.md) referenčních dokumentů AzCopy.

#### <a name="use-wildcard-characters"></a>Použít zástupné znaky

Použijte příkaz [AzCopy Copy](storage-ref-azcopy-copy.md) s možností `--include-pattern`. Zadejte částečné názvy, které obsahují zástupné znaky. Oddělte názvy pomocí semicolin (`;`).

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Příklad** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Soubory můžete také vyloučit pomocí možnosti `--exclude-pattern`. Další informace najdete v tématu [kopírování](storage-ref-azcopy-copy.md) referenčních dokumentů AzCopy.

Možnosti `--include-pattern` a `--exclude-pattern` se vztahují pouze na názvy souborů, nikoli na cestu.  Pokud chcete zkopírovat všechny textové soubory, které existují ve stromové struktuře, použijte možnost `–recursive` pro získání celého adresářového stromu a pak použijte `–include-pattern` a zadáním `*.txt` získáte všechny textové soubory.

## <a name="copy-files-between-storage-accounts"></a>Kopírování souborů mezi účty úložiště

Pomocí AzCopy můžete kopírovat soubory do jiných účtů úložiště. Operace kopírování je synchronní, takže když příkaz vrátí, znamená to, že byly zkopírovány všechny soubory.

AzCopy používá [](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) [rozhraní API](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)pro servery, takže se data zkopírují přímo mezi úložnými servery. Tyto operace kopírování nepoužívají šířku pásma sítě vašeho počítače. Propustnost těchto operací můžete zvýšit nastavením hodnoty proměnné prostředí `AZCOPY_CONCURRENCY_VALUE`. Další informace najdete v tématu [optimalizace propustnosti](storage-use-azcopy-configure.md#optimize-throughput).

Tato část obsahuje následující příklady:

> [!div class="checklist"]
> * Kopírování souboru do jiného účtu úložiště
> * Zkopírování adresáře do jiného účtu úložiště
> * Kopírování sdílené složky do jiného účtu úložiště
> * Kopírování všech sdílených složek, adresářů a souborů do jiného účtu úložiště

Podrobné referenční dokumentaci najdete v tématu [AzCopy Copy](storage-ref-azcopy-copy.md).

### <a name="copy-a-file-to-another-storage-account"></a>Kopírování souboru do jiného účtu úložiště

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>'` |
| **Příklad** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Zkopírování adresáře do jiného účtu úložiště

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>' --recursive` |
| **Příklad** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>Kopírování sdílené složky do jiného účtu úložiště

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>' --recursive` |
| **Příklad** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Kopírování všech sdílených složek, adresářů a souborů do jiného účtu úložiště

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/' --recursive'` |
| **Příklad** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Synchronizovat soubory

Můžete synchronizovat obsah místního systému souborů se sdílenou složkou. Synchronizace je jednosměrná. Jinými slovy, jste si zvolili, který z těchto dvou koncových bodů je zdroj a který je cílový. Synchronizace používá také server k rozhraní API serveru.

> [!NOTE]
> V současné době je tento scénář podporován pouze pro účty, které nemají hierarchický obor názvů. Aktuální verze AzCopy se nesynchronizuje mezi soubory Azure a Blob Storage.

Příkaz [synchronizovat](storage-ref-azcopy-sync.md) porovná názvy souborů a poslední upravená časová razítka. Nastavte volitelný příznak `--delete-destination` na hodnotu `true` nebo `prompt`, chcete-li odstranit soubory v cílovém adresáři, pokud tyto soubory již neexistují ve zdrojovém adresáři.

Pokud nastavíte příznak `--delete-destination` na hodnotu `true` AzCopy odstraní soubory bez zadání výzvy. Pokud chcete, aby se zobrazila výzva, než AzCopy odstraní soubor, nastavte příznak `--delete-destination` na `prompt`.

Podrobné referenční dokumentaci najdete v tématu [AzCopy Sync](storage-ref-azcopy-sync.md).

### <a name="update-a-file-share-with-changes-to-a-local-file-system"></a>Aktualizace sdílené složky se změnami v místním systému souborů

V tomto případě je sdílená složka cílovou složkou a místním systémem souborů je zdroj.

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>' --recursive` |
| **Příklad** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-file-share"></a>Aktualizace místního systému souborů se změnami ve sdílené složce

V tomto případě je místní systém souborů cílový a sdílená složka je zdroj.

|    |     |
|--------|-----------|
| **Syntaktick** | `azcopy sync 'https://<storage-account-name>.file.core.windows.net/<file-share-name>' 'C:\myDirectory' --recursive` |
| **Příklad** | `azcopy sync 'https://mystorageaccount.file.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

## <a name="next-steps"></a>Další kroky

Další příklady najdete v některém z těchto článků:

- [Začínáme s AzCopy](storage-use-azcopy-v10.md)

- [Přenos dat pomocí AzCopy a BLOB Storage](storage-use-azcopy-blobs.md)

- [Přenos dat pomocí kontejnerů AzCopy a Amazon S3](storage-use-azcopy-s3.md)

- [Konfigurace, optimalizace a řešení potíží s AzCopy](storage-use-azcopy-configure.md)