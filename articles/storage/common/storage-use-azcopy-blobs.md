---
title: Přenos dat do nebo ze služby Azure Blob storage pomocí AzCopy v10 | Dokumentace Microsoftu
description: Tento článek obsahuje sadu AzCopy příklad příkazy, které vám pomůžou vytvořit kontejnery, zkopírujte soubory a synchronizovat složky mezi místní systémy souborů a kontejnerů.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 98e33f838ee9b6f506bf1dc01e1dd61ad587aa05
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299407"
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

Můžete použít AzCopy `copy` příkaz pro nahrávání souborů a složek ze svého místního počítače.

Tato část obsahuje následující příklady:

> [!div class="checklist"]
> * Nahrání souboru
> * Nahrání složky
> * Nahrát soubory pomocí zástupných znaků

> [!NOTE]
> AzCopy není automaticky vypočítat a uložit jeho kód hash md5. Pokud chcete AzCopy k tomu, potom připojte `--put-md5` příznak pro každý příkaz kopírování. Tímto způsobem, při stažení objektu blob AzCopy vypočítá hodnotu hash MD5 pro stažených dat a ověřuje, že hodnota hash MD5 uložena v objektu blob `Content-md5` vlastnosti odpovídají vypočtený hash.

### <a name="upload-a-file"></a>Nahrání souboru

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **Příklad** | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **Příklad** (hierarchického oboru názvů) | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> AzCopy ve výchozím nastavení odesílá data do objektů BLOB bloku. Pokud chcete nahrát soubory, doplňovací objekty BLOB nebo objekty BLOB stránek použijte příznak `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.

### <a name="upload-a-folder"></a>Nahrání složky

Tento příklad zkopíruje do složky (a všechny soubory v této složce) do kontejneru objektů blob. Výsledkem je složka v kontejneru se stejným názvem.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Příklad** | `azcopy copy "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Příklad** (hierarchického oboru názvů) | `azcopy copy "C:\myFolder" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

Pokud chcete zkopírovat do složky v rámci kontejneru, stačí zadejte název této složky do řetězce příkazu.

|    |     |
|--------|-----------|
| **Příklad** | `azcopy copy "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder" --recursive` |
| **Příklad** (hierarchického oboru názvů) | `azcopy copy "C:\myFolder" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder" --recursive` |

Pokud zadáte název složky, která neexistuje v kontejneru, AzCopy vytvoří novou složku s tímto názvem.

### <a name="upload-the-contents-of-a-folder"></a>Odešlete obsah složky

Odešlete obsah složky bez kopírování samotné složce obsahující pomocí zástupný znak (*).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "<local-folder-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<folder-path>` |
| **Příklad** | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder"` |
| **Příklad** (hierarchického oboru názvů) | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder"` |

> [!NOTE]
> Připojit `--recursive` příznak k nahrání souborů do všechny podsložky.

## <a name="download-files"></a>Stažení souborů

Můžete použít AzCopy `copy` příkazu stáhněte objekty BLOB, složky a kontejnery do místního počítače.

Tato část obsahuje následující příklady:

> [!div class="checklist"]
> * Stažení souboru
> * Stáhněte si složku
> * Stáhnout soubory pomocí zástupných znaků

> [!NOTE]
> Pokud `Content-md5` hodnota vlastnosti objektu blob obsahuje hodnotu hash, AzCopy vypočítá hodnotu hash MD5 pro stažená data a ověřuje, že hodnota hash MD5 uložena v objektu blob `Content-md5` vlastnosti odpovídají vypočtený hash. Pokud tyto hodnoty nejsou shodné, stahování selže, pokud toto chování můžete přepsat přidáním `--check-md5=NoCheck` nebo `--check-md5=LogOnly` příkazu kopírování.

### <a name="download-a-file"></a>Stažení souboru

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **Příklad** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myFolder\myTextFile.txt"` |
| **Příklad** (hierarchického oboru názvů) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myFolder\myTextFile.txt"` |

### <a name="download-a-folder"></a>Stáhněte si složku

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<folder-path>" "<local-folder-path>" --recursive` |
| **Příklad** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder "C:\myFolder"  --recursive` |
| **Příklad** (hierarchického oboru názvů) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder "C:\myFolder"  --recursive` |

V tomto příkladu vede složku s názvem `C:\myFolder\myBlobFolder` , který obsahuje všechny stažené soubory.

### <a name="download-the-contents-of-a-folder"></a>Stáhněte si obsah složky

Stažení obsahu složky bez kopírování samotné složce obsahující pomocí zástupný znak (*).

> [!NOTE]
> Tento scénář se v současné době podporuje pouze pro účty, které nemají hierarchického oboru názvů.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-folder-path>/"` |
| **Příklad** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder/*" "C:\myFolder"` |

> [!NOTE]
> Připojit `--recursive` příznak ke stahování souborů v všechny podsložky.

## <a name="copy-blobs-between-storage-accounts"></a>Kopírování objektů BLOB mezi účty úložiště

Pomocí AzCopy můžete kopírovat objekty BLOB na jiné účty úložiště. Operace kopírování je synchronní, tak po návratu příkazu, který označuje, že všechny soubory zkopírovaly.

> [!NOTE]
> Tento scénář se v současné době podporuje pouze pro účty, které nemají hierarchického oboru názvů.

Využívá nástroj AzCopy [Vložit blok z adresy URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) rozhraní API, tak, aby se data zkopírovat přímo mezi servery úložiště. Operace Kopírovaní nepoužívejte šířky pásma sítě v počítači.

Tato část obsahuje následující příklady:

> [!div class="checklist"]
> * Kopírovat objekt blob do jiného účtu úložiště
> * Zkopírujte složku do jiného účtu úložiště
> * Zkopírujte do jiného účtu úložiště kontejnery
> * Zkopírujte všechny kontejnery, složky a soubory do jiného účtu úložiště

### <a name="copy-a-blob-to-another-storage-account"></a>Kopírovat objekt blob do jiného účtu úložiště

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **Příklad** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-folder-to-another-storage-account"></a>Zkopírujte složku do jiného účtu úložiště

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<folder-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<folder-path>" --recursive` |
| **Příklad** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobFolder" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myBlobFolder" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>Zkopírujte do jiného účtu úložiště kontejnery

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Příklad** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-folders-and-files-to-another-storage-account"></a>Zkopírujte všechny kontejnery, složky a soubory do jiného účtu úložiště

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **Příklad** | `azcopy cp "https://mysourceaccount.blob.core.windows.net" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>Synchronizovat soubory

Můžete synchronizovat obsah místního systému souborů do kontejneru objektů blob. Také můžete synchronizovat kontejner objektů blob místního systému souborů ve vašem počítači. Synchronizace je jednosměrná. Jinými slovy zvolíte, které tyto dva koncové body se zdroji a která z nich je cílem.

> [!NOTE]
> V aktuální verzi AzCopy není synchronizace mezi ostatní zdroje a cíle (například: File storage nebo kbelíků Amazon Web Services (AWS) S3).

`sync` Příkaz porovnává názvy souborů a časové razítko poslední změny. Nastavte `--delete-destination` volitelný příznak na hodnotu `true` nebo `prompt` odstranit soubory v cílové složce, pokud tyto soubory ve zdrojové složce již neexistuje.

Pokud jste nastavili `--delete-destination` příznak `true` AzCopy odstraní soubory bez zadání výzva. Pokud chcete výzva se zobrazí před AzCopy odstraní soubor, nastavte `--delete-destination` příznak `prompt`.

> [!NOTE]
> Aby se zabránilo nechtěnému odstranění, ujistěte se, že chcete povolit [obnovitelné odstranění](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) funkci před použitím `--delete-destination=prompt|true` příznak.

### <a name="synchronize-a-container-to-a-local-file-system"></a>Synchronizovat místního systému souborů kontejneru

V takovém případě místního systému souborů se stane zdroj a cíl je kontejner.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy sync "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Příklad** | `azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Příklad** (hierarchického oboru názvů) | `azcopy sync "C:\myFolder" "https://<storage-account-name>.dfs.core.windows.net/mycontainer" --recursive` |


### <a name="synchronize-a-local-file-system-to-a-container"></a>Synchronizovat místního systému souborů do kontejneru

V tomto případě kontejner stane zdroje a místního systému souborů je cílem.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myFolder" --recursive` |
| **Příklad** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myFolder" --recursive` |
| **Příklad** (hierarchického oboru názvů) | `azcopy sync "https://mystorageaccount.dfs.core.windows.net/mycontainer" "C:\myFolder" --recursive` |

## <a name="next-steps"></a>Další postup

Další příklady najdete v některém z těchto článků:

- [Začínáme s AzCopy](storage-use-azcopy-v10.md)

- [Kurz: Migrace místních dat do cloudového úložiště pomocí AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Přenos dat pomocí AzCopy a file storage](storage-use-azcopy-files.md)

- [Přenos dat pomocí AzCopy a Amazon S3 intervalů](storage-use-azcopy-s3.md)

- [Konfigurace, optimalizovat a řešení potíží s AzCopy](storage-use-azcopy-configure.md)