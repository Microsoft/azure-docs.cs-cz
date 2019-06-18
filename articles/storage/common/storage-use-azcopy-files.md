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
ms.openlocfilehash: 69d7136396c3d989e63b8956d3e703cc7f9666c8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66687932"
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

Můžete použít AzCopy `copy` příkazu nahrajte soubory a adresáře ze svého místního počítače.

Tato část obsahuje následující příklady:

> [!div class="checklist"]
> * Nahrání souboru
> * Nahrát do adresáře
> * Nahrát soubory pomocí zástupných znaků

> [!NOTE]
> AzCopy není automaticky vypočítat a uložit jeho kód hash md5. Pokud chcete AzCopy k tomu, potom připojte `--put-md5` příznak pro každý příkaz kopírování. Tímto způsobem staženého souboru AzCopy vypočítá hodnotu hash MD5 pro stažených dat a ověřuje, že hodnota hash MD5 uložené v souboru `Content-md5` vlastnosti odpovídají vypočtený hash.

### <a name="upload-a-file"></a>Nahrání souboru

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **Příklad** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-directory"></a>Nahrát do adresáře

Tento příklad zkopíruje adresář (a všechny soubory v tomto adresáři) do sdílené složky. Výsledkem je adresáře ve sdílené složce se stejným názvem.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **Příklad** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Pokud chcete zkopírovat do adresáře ve sdílené složce, stačí zadejte název tohoto adresáře do řetězce příkazu.

|    |     |
|--------|-----------|
| **Příklad** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Pokud zadáte název adresáře, který není ve sdílené složce, AzCopy vytvoří nový adresář s tímto názvem.

### <a name="upload-the-contents-of-a-directory"></a>Nahrajte obsah do adresáře

Bez kopírování obsahující adresář pomocí zástupný znak (*) můžete nahrát obsah adresáře.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "<local-directory-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **Příklad** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Připojit `--recursive` příznak, který chcete nahrát soubory ve všech podadresářích.

## <a name="download-files"></a>Stažení souborů

Můžete použít AzCopy `copy` příkazu pro stažení souborů, adresářů a souborů sdílených složek do místního počítače.

Tato část obsahuje následující příklady:

> [!div class="checklist"]
> * Stažení souboru
> * Stáhněte si do adresáře
> * Stáhnout soubory pomocí zástupných znaků

> [!NOTE]
> Pokud `Content-md5` hodnota vlastnosti souboru obsahuje hodnotu hash, AzCopy vypočítá hodnotu hash MD5 pro stažená data a ověřuje, že hodnota hash MD5 uložené v souboru `Content-md5` vlastnosti odpovídají vypočtený hash. Pokud tyto hodnoty nejsou shodné, stahování selže, pokud toto chování můžete přepsat přidáním `--check-md5=NoCheck` nebo `--check-md5=LogOnly` příkazu kopírování.

### <a name="download-a-file"></a>Stažení souboru

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **Příklad** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Stáhněte si do adresáře

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>" "<local-directory-path>" --recursive` |
| **Příklad** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"  --recursive` |

V tomto příkladu vede adresář s názvem `C:\myDirectory\myFileShareDirectory` , který obsahuje všechny stažené soubory.

### <a name="download-the-contents-of-a-directory"></a>Stáhněte si obsah adresáře

Bez kopírování obsahující adresář pomocí zástupný znak (*) si můžete stáhnout obsah adresáře.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-directory-path>/"` |
| **Příklad** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"` |

> [!NOTE]
> Připojit `--recursive` příznak stahovat soubory ve všech podadresářích.

## <a name="next-steps"></a>Další postup

Další příklady najdete v některém z těchto článků:

- [Začínáme s AzCopy](storage-use-azcopy-v10.md)

- [Přenos dat pomocí AzCopy a blob storage](storage-use-azcopy-blobs.md)

- [Přenos dat pomocí AzCopy a Amazon S3 intervalů](storage-use-azcopy-s3.md)

- [Konfigurace, optimalizovat a řešení potíží s AzCopy](storage-use-azcopy-configure.md)