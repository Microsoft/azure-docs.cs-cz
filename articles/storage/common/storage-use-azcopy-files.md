---
title: Přenos dat do nebo ze souborů Azure s použitím AzCopy v10 | Dokumentace Microsoftu
description: Přenos dat pomocí AzCopy a file storage.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 5d11d5c0da350b9abf2e2b06a11a1690cf3f6922
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247106"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Přenos dat pomocí AzCopy a file storage 

AzCopy je nástroj příkazového řádku, který vám pomůže zkopírovat objekty BLOB nebo souborů do nebo z účtu úložiště. Tento článek obsahuje příklady příkazů, které fungují s Azure Files.

Než začnete, podívejte se [Začínáme s AzCopy](storage-use-azcopy-v10.md) článku ke stažení AzCopy a seznamte se s nástroji.

## <a name="create-file-shares"></a>Vytvoření sdílené složky

Můžete použít AzCopy `make` příkaz pro vytvoření sdílené složky. V příkladu v této části se vytvoří sdílené složky s názvem `myfileshare`.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy make "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>"` |
| **Příklad** | `azcopy make "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

## <a name="upload-files"></a>Nahrání souborů

Můžete použít AzCopy `copy` příkaz pro nahrávání souborů a složek ze svého místního počítače.

Tato část obsahuje následující příklady:

> [!div class="checklist"]
> * Nahrání souboru
> * Nahrání složky
> * Nahrát soubory pomocí zástupných znaků

> [!NOTE]
> AzCopy není automaticky vypočítat a uložit jeho kód hash md5. Pokud chcete AzCopy k tomu, potom připojte `--put-md5` příznak pro každý příkaz kopírování. Tímto způsobem staženého souboru AzCopy vypočítá hodnotu hash MD5 pro stažených dat a ověřuje, že hodnota hash MD5 uložené v souboru `Content-md5` vlastnosti odpovídají vypočtený hash.

### <a name="upload-a-file"></a>Nahrání souboru

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **Příklad** | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-folder"></a>Nahrání složky

Tento příklad zkopíruje do sdílené složky (a všechny soubory v této složce). Výsledkem je do složky ve sdílené složce se stejným názvem.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "<local-folder-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **Příklad** | `azcopy copy "C:\myFolder" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Pokud chcete zkopírovat do složky ve sdílené složce, stačí zadejte název této složky do řetězce příkazu.

|    |     |
|--------|-----------|
| **Příklad** | `azcopy copy "C:\myFolder" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Pokud zadáte název složky, která neexistuje ve sdílené složce, AzCopy vytvoří novou složku s tímto názvem.

### <a name="upload-the-contents-of-a-folder"></a>Odešlete obsah složky

Odešlete obsah složky bez kopírování samotné složce obsahující pomocí zástupný znak (*).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "<local-folder-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<folder-path>?<SAS-token>` |
| **Příklad** | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Připojit `--recursive` příznak k nahrání souborů do všechny podsložky.

## <a name="download-files"></a>Stažení souborů

Můžete použít AzCopy `copy` příkaz ke stažení souborů, složek a souborů sdílených složek do místního počítače.

Tato část obsahuje následující příklady:

> [!div class="checklist"]
> * Stažení souboru
> * Stáhněte si složku
> * Stáhnout soubory pomocí zástupných znaků

> [!NOTE]
> Pokud `Content-md5` hodnota vlastnosti souboru obsahuje hodnotu hash, AzCopy vypočítá hodnotu hash MD5 pro stažená data a ověřuje, že hodnota hash MD5 uložené v souboru `Content-md5` vlastnosti odpovídají vypočtený hash. Pokud tyto hodnoty nejsou shodné, stahování selže, pokud toto chování můžete přepsat přidáním `--check-md5=NoCheck` nebo `--check-md5=LogOnly` příkazu kopírování.

### <a name="download-a-file"></a>Stažení souboru

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **Příklad** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder\myTextFile.txt"` |

### <a name="download-a-folder"></a>Stáhněte si složku

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<folder-path>?<SAS-token>" "<local-folder-path>" --recursive` |
| **Příklad** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder"  --recursive` |

V tomto příkladu vede složku s názvem `C:\myFolder\myFileShareFolder` , který obsahuje všechny stažené soubory.

### <a name="download-the-contents-of-a-folder"></a>Stáhněte si obsah složky

Stažení obsahu složky bez kopírování samotné složce obsahující pomocí zástupný znak (*).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-folder-path>/"` |
| **Příklad** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder"` |

> [!NOTE]
> Připojit `--recursive` příznak ke stahování souborů v všechny podsložky.

## <a name="next-steps"></a>Další postup

Další příklady najdete v některém z těchto článků:

- [Začínáme s AzCopy](storage-use-azcopy-v10.md)

- [Přenos dat pomocí AzCopy a blob storage](storage-use-azcopy-blobs.md)

- [Přenos dat pomocí AzCopy a Amazon S3 intervalů](storage-use-azcopy-s3.md)

- [Konfigurace, optimalizovat a řešení potíží s AzCopy](storage-use-azcopy-configure.md)