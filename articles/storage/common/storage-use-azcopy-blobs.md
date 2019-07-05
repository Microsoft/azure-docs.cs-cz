---
title: Přenos dat do nebo ze služby Azure Blob storage pomocí AzCopy v10 | Dokumentace Microsoftu
description: Tento článek obsahuje sadu AzCopy příklad příkazy, které vám pomůžou vytvořit kontejnery, zkopírujte soubory a synchronizace adresářů mezi místní systémy souborů a kontejnerů.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: f95af348eb11abee5a46a89e08da5bf4eb873c42
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566131"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Přenos dat pomocí AzCopy a Blob storage

AzCopy je nástroj příkazového řádku, který můžete použít ke zkopírování dat na, z nebo mezi účty úložiště. Tento článek obsahuje příklady příkazů, které pracují s úložištěm objektů Blob.

## <a name="get-started"></a>Začínáme

Zobrazit [Začínáme s AzCopy](storage-use-azcopy-v10.md) článku ke stažení AzCopy a informace o způsobech, že můžete zadat přihlašovací údaje pro ověření do služby storage.

> [!NOTE]
> V příkladech v tomto článku se předpokládá, že jste ověřit svoji identitu pomocí `AzCopy login` příkazu. AzCopy pak používá účet Azure AD k autorizaci přístupu k datům ve službě Blob storage.
>
> Pokud byste chtěli raději použít SAS token k autorizaci přístupu k datům objektu blob, můžete připojit tento token na adresu URL prostředku v každém z nich AzCopy.
>
> Například: `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>"`.

## <a name="create-a-container"></a>Vytvoření kontejneru

Můžete použít AzCopy `make` příkaz k vytvoření kontejneru. Příklady v této části vytvoříte kontejner s názvem `mycontainer`.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy make "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>` |
| **Příklad** | `azcopy make "https://mystorageaccount.blob.core.windows.net/mycontainer"` |
| **Příklad** (hierarchického oboru názvů) | `azcopy make "https://mystorageaccount.dfs.core.windows.net/mycontainer"` |

## <a name="upload-files"></a>Nahrání souborů

Můžete použít AzCopy `copy` příkazu nahrajte soubory a adresáře ze svého místního počítače.

Tato část obsahuje následující příklady:

> [!div class="checklist"]
> * Nahrání souboru
> * Nahrát do adresáře
> * Nahrát soubory pomocí zástupných znaků

> [!NOTE]
> AzCopy není automaticky vypočítat a uložit jeho kód hash md5. Pokud chcete AzCopy k tomu, potom připojte `--put-md5` příznak pro každý příkaz kopírování. Tímto způsobem, při stažení objektu blob AzCopy vypočítá hodnotu hash MD5 pro stažených dat a ověřuje, že hodnota hash MD5 uložena v objektu blob `Content-md5` vlastnosti odpovídají vypočtený hash.

### <a name="upload-a-file"></a>Nahrání souboru

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **Příklad** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **Příklad** (hierarchického oboru názvů) | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> AzCopy ve výchozím nastavení odesílá data do objektů BLOB bloku. Pokud chcete nahrát soubory, doplňovací objekty BLOB nebo objekty BLOB stránek použijte příznak `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.

### <a name="upload-a-directory"></a>Nahrát do adresáře

Tento příklad zkopíruje adresář (a všechny soubory v tomto adresáři) do kontejneru objektů blob. Výsledkem je adresář v kontejneru se stejným názvem.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Příklad** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Příklad** (hierarchického oboru názvů) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

Ke zkopírování do adresáře v rámci kontejneru, stačí zadejte název tohoto adresáře do řetězce příkazu.

|    |     |
|--------|-----------|
| **Příklad** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |
| **Příklad** (hierarchického oboru názvů) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

Pokud zadáte název adresáře, který neexistuje v kontejneru, AzCopy vytvoří nový adresář s tímto názvem.

### <a name="upload-the-contents-of-a-directory"></a>Nahrajte obsah do adresáře

Bez kopírování obsahující adresář pomocí zástupný znak (*) můžete nahrát obsah adresáře.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "<local-directory-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>` |
| **Příklad** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory"` |
| **Příklad** (hierarchického oboru názvů) | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory"` |

> [!NOTE]
> Připojit `--recursive` příznak, který chcete nahrát soubory ve všech podadresářích.

## <a name="download-files"></a>Stažení souborů

Můžete použít AzCopy `copy` příkazu stáhněte adresáře, kontejnerů a objektů BLOB do místního počítače.

Tato část obsahuje následující příklady:

> [!div class="checklist"]
> * Stažení souboru
> * Stáhněte si do adresáře
> * Stáhnout soubory pomocí zástupných znaků

> [!NOTE]
> Pokud `Content-md5` hodnota vlastnosti objektu blob obsahuje hodnotu hash, AzCopy vypočítá hodnotu hash MD5 pro stažená data a ověřuje, že hodnota hash MD5 uložena v objektu blob `Content-md5` vlastnosti odpovídají vypočtený hash. Pokud tyto hodnoty nejsou shodné, stahování selže, pokud toto chování můžete přepsat přidáním `--check-md5=NoCheck` nebo `--check-md5=LogOnly` příkazu kopírování.

### <a name="download-a-file"></a>Stažení souboru

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **Příklad** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |
| **Příklad** (hierarchického oboru názvů) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Stáhněte si do adresáře

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>" "<local-directory-path>" --recursive` |
| **Příklad** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |
| **Příklad** (hierarchického oboru názvů) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |

V tomto příkladu vede adresář s názvem `C:\myDirectory\myBlobDirectory` , který obsahuje všechny stažené soubory.

### <a name="download-the-contents-of-a-directory"></a>Stáhněte si obsah adresáře

Bez kopírování obsahující adresář pomocí zástupný znak (*) si můžete stáhnout obsah adresáře.

> [!NOTE]
> Tento scénář se v současné době podporuje pouze pro účty, které nemají hierarchického oboru názvů.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` |
| **Příklad** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*" "C:\myDirectory"` |

> [!NOTE]
> Připojit `--recursive` příznak stahovat soubory ve všech podadresářích.

## <a name="copy-blobs-between-storage-accounts"></a>Kopírování objektů BLOB mezi účty úložiště

Pomocí AzCopy můžete kopírovat objekty BLOB na jiné účty úložiště. Operace kopírování je synchronní, tak po návratu příkazu, který označuje, že všechny soubory zkopírovaly.

> [!NOTE]
> Tento scénář se v současné době podporuje pouze pro účty, které nemají hierarchického oboru názvů. 

Využívá nástroj AzCopy [Vložit blok z adresy URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) rozhraní API, tak, aby se data zkopírovat přímo mezi servery úložiště. Operace Kopírovaní nepoužívejte šířky pásma sítě v počítači.

Tato část obsahuje následující příklady:

> [!div class="checklist"]
> * Kopírovat objekt blob do jiného účtu úložiště
> * Zkopírování adresáře do jiného účtu úložiště
> * Zkopírujte do jiného účtu úložiště kontejnery
> * Zkopírujte všechny kontejnery, adresáře a soubory do jiného účtu úložiště

> [!NOTE]
> V aktuální verzi budete muset přidat SAS token pro každou adresu URL zdroje. Pokud zadáte přihlašovací údaje pro ověření pomocí Azure Active Directory (AD), můžete vynechat token SAS pouze z cílové adrese URL. 

### <a name="copy-a-blob-to-another-storage-account"></a>Kopírovat objekt blob do jiného účtu úložiště

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **Příklad** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-directory-to-another-storage-account"></a>Zkopírování adresáře do jiného účtu úložiště

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>" --recursive` |
| **Příklad** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>Zkopírujte do jiného účtu úložiště kontejnery

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Příklad** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-directories-and-files-to-another-storage-account"></a>Zkopírujte všechny kontejnery, adresáře a soubory do jiného účtu úložiště

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **Příklad** | `azcopy cp "https://mysourceaccount.blob.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>Synchronizovat soubory

Můžete synchronizovat obsah místního souboru systému s kontejner objektů blob. Synchronizace je jednosměrná. Jinými slovy zvolíte, které tyto dva koncové body se zdroji a která z nich je cílem.

> [!NOTE]
> Tento scénář se v současné době podporuje pouze pro účty, které nemají hierarchického oboru názvů. V aktuální verzi AzCopy není synchronizace mezi ostatní zdroje a cíle (například: File storage nebo kbelíků Amazon Web Services (AWS) S3).

`sync` Příkaz porovnává názvy souborů a časové razítko poslední změny. Nastavte `--delete-destination` volitelný příznak na hodnotu `true` nebo `prompt` odstranit soubory v cílovém adresáři, pokud tyto soubory ve zdrojovém adresáři již neexistují.

Pokud jste nastavili `--delete-destination` příznak `true` AzCopy odstraní soubory bez zadání výzva. Pokud chcete výzva se zobrazí před AzCopy odstraní soubor, nastavte `--delete-destination` příznak `prompt`.

> [!NOTE]
> Aby se zabránilo nechtěnému odstranění, ujistěte se, že chcete povolit [obnovitelné odstranění](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) funkci před použitím `--delete-destination=prompt|true` příznak.

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Aktualizace kontejneru se změnami v místním systému souborů

V tomto případě kontejner je cílem a místního systému souborů je zdrojem.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy sync "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Příklad** | `azcopy sync "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Aktualizace místního systému souborů se změnami do kontejneru

V tomto případě místního systému souborů je cílem a zdrojem je kontejner.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myDirectory" --recursive` |
| **Příklad** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myDirectory" --recursive` |
|

## <a name="next-steps"></a>Další postup

Další příklady najdete v některém z těchto článků:

- [Začínáme s AzCopy](storage-use-azcopy-v10.md)

- [Kurz: Migrace místních dat do cloudového úložiště pomocí AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Přenos dat pomocí AzCopy a file storage](storage-use-azcopy-files.md)

- [Přenos dat pomocí AzCopy a Amazon S3 intervalů](storage-use-azcopy-s3.md)

- [Konfigurace, optimalizovat a řešení potíží s AzCopy](storage-use-azcopy-configure.md)