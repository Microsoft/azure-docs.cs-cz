---
title: Přenos dat do nebo z úložiště objektů BLOB v Azure pomocí AzCopy v10 za účelem | Microsoft Docs
description: Tento článek obsahuje kolekci AzCopy ukázkových příkazů, které vám pomůžou vytvářet kontejnery, kopírovat soubory a synchronizovat adresáře mezi místními systémy souborů a kontejnery.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: daf31c382f2b6d6e164092d587eb65afa25323f1
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534760"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Přenos dat pomocí AzCopy a BLOB Storage

AzCopy je nástroj příkazového řádku, který můžete použít ke kopírování dat do, z nebo mezi účty úložiště. Tento článek obsahuje příklady příkazů, které fungují s úložištěm objektů BLOB.

## <a name="get-started"></a>Začínáme

V článku Začínáme [s AzCopy](storage-use-azcopy-v10.md) si můžete stáhnout AzCopy a seznamte se s různými způsoby, jak můžete službě úložiště poskytnout autorizační přihlašovací údaje.

> [!NOTE]
> V příkladech v tomto článku se předpokládá, že jste ověřili vaši identitu `AzCopy login` pomocí příkazu. AzCopy pak použije účet Azure AD k autorizaci přístupu k datům v úložišti objektů BLOB.
>
> Pokud místo toho chcete použít token SAS k autorizaci přístupu k datům objektu blob, můžete tento token připojit k adrese URL prostředku v každém příkazu AzCopy.
>
> Například: `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>"`.

## <a name="create-a-container"></a>Vytvoření kontejneru

Pomocí příkazu AzCopy `make` můžete vytvořit kontejner. Příklady v této části vytvoří kontejner s názvem `mycontainer`.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy make "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>` |
| **Příklad** | `azcopy make "https://mystorageaccount.blob.core.windows.net/mycontainer"` |
| **Příklad** (hierarchický obor názvů) | `azcopy make "https://mystorageaccount.dfs.core.windows.net/mycontainer"` |

## <a name="upload-files"></a>Nahrání souborů

K nahrání souborů a `copy` adresářů z místního počítače můžete použít příkaz AzCopy.

Tato část obsahuje následující příklady:

> [!div class="checklist"]
> * Nahrát soubor
> * Odeslat adresář
> * Nahrání souborů pomocí zástupných znaků

> [!NOTE]
> AzCopy automaticky nepočítá a neukládá kód hash MD5 souboru. Pokud to chcete provést v AzCopy, přidejte `--put-md5` příznak ke každému příkazu Copy. Tímto způsobem AzCopy při stažení objektu BLOB vypočítá hodnotu hash MD5 pro stažená data a ověří, že hodnota hash MD5 uložená ve `Content-md5` vlastnosti objektu BLOB odpovídá počítané hodnotě hash.

### <a name="upload-a-file"></a>Nahrát soubor

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **Příklad** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> AzCopy ve výchozím nastavení nahrává data do objektů blob bloku. Chcete-li odeslat soubory jako doplňovací objekty blob nebo objekty blob stránky `--blob-type=[BlockBlob|PageBlob|AppendBlob]`, použijte příznak.

### <a name="upload-a-directory"></a>Odeslat adresář

Tento příklad zkopíruje adresář (a všechny soubory v tomto adresáři) do kontejneru objektů BLOB. Výsledkem je adresář v kontejneru se stejným názvem.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Příklad** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

Pokud chcete kopírovat do adresáře v rámci kontejneru, stačí zadat název tohoto adresáře do řetězce příkazu.

|    |     |
|--------|-----------|
| **Příklad** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

Pokud zadáte název adresáře, který v kontejneru neexistuje, vytvoří AzCopy nový adresář s tímto názvem.

### <a name="upload-the-contents-of-a-directory"></a>Nahrajte obsah adresáře.

Můžete nahrát obsah adresáře bez kopírování samotného nadřazeného adresáře pomocí zástupného znaku (*).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "<local-directory-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>` |
| **Příklad** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory"` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory"` |

> [!NOTE]
> `--recursive` Připojí příznak pro nahrání souborů ve všech podadresářích.

## <a name="download-files"></a>Stažení souborů

Pomocí příkazu AzCopy `copy` můžete stáhnout objekty blob, adresáře a kontejnery do místního počítače.

Tato část obsahuje následující příklady:

> [!div class="checklist"]
> * Stáhnout soubor
> * Stáhnout adresář
> * Stáhnout soubory pomocí zástupných znaků

> [!NOTE]
> Pokud hodnota `Content-md5` vlastnosti objektu BLOB obsahuje hodnotu hash, AzCopy vypočítá hodnotu hash MD5 pro stažená data a ověří, že hodnota hash MD5 uložená ve vlastnosti objektu BLOB odpovídá počítané hodnotě hash. `Content-md5` Pokud se tyto hodnoty neshodují, stažení se nezdaří, pokud toto chování neprovedete `--check-md5=LogOnly` připojením `--check-md5=NoCheck` nebo zkopírováním příkazu Kopírovat.

### <a name="download-a-file"></a>Stáhnout soubor

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **Příklad** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Stáhnout adresář

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>" "<local-directory-path>" --recursive` |
| **Příklad** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |

Tento příklad vede k adresáři s názvem `C:\myDirectory\myBlobDirectory` , který obsahuje všechny stažené soubory.

### <a name="download-the-contents-of-a-directory"></a>Stažení obsahu adresáře

Obsah adresáře si můžete stáhnout bez zkopírování samotného obsahujícího adresáře pomocí zástupného znaku (*).

> [!NOTE]
> V současné době je tento scénář podporován pouze pro účty, které nemají hierarchický obor názvů.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` |
| **Příklad** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*" "C:\myDirectory"` |

> [!NOTE]
> `--recursive` Přidejte příznak pro stažení souborů ve všech podadresářích.

## <a name="copy-blobs-between-storage-accounts"></a>Kopírování objektů BLOB mezi účty úložiště

Pomocí AzCopy můžete kopírovat objekty blob do jiných účtů úložiště. Operace kopírování je synchronní, takže když příkaz vrátí, znamená to, že byly zkopírovány všechny soubory.

AzCopy používá [](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) [rozhraní API](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)pro servery, takže se data zkopírují přímo mezi úložnými servery. Tyto operace kopírování nepoužívají šířku pásma sítě vašeho počítače. Propustnost těchto operací můžete zvýšit nastavením hodnoty `AZCOPY_CONCURRENCY_VALUE` proměnné prostředí. Další informace najdete v tématu [optimalizace propustnosti](storage-use-azcopy-configure.md#optimize-throughput).

> [!NOTE]
> Tento scénář má v aktuální verzi následující omezení.
>
> - Podporují se jenom účty, které nemají hierarchický obor názvů.
> - Je nutné připojit token SAS ke každé zdrojové adrese URL. Pokud přihlašovací údaje pro autorizaci zadáte pomocí Azure Active Directory (AD), můžete token SAS vynechat jenom z cílové adresy URL.
>-  Účty úložiště blob bloku úrovně Premium nepodporují úrovně přístupu. Vynechejte úroveň přístupu objektu BLOB z operace kopírování nastavením `s2s-preserve-access-tier` na `false` (například: `--s2s-preserve-access-tier=false`).

Tato část obsahuje následující příklady:

> [!div class="checklist"]
> * Kopírování objektu blob do jiného účtu úložiště
> * Zkopírování adresáře do jiného účtu úložiště
> * Kopírování kontejnerů do jiného účtu úložiště
> * Kopírování všech kontejnerů, adresářů a souborů do jiného účtu úložiště

### <a name="copy-a-blob-to-another-storage-account"></a>Kopírování objektu blob do jiného účtu úložiště

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **Příklad** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-directory-to-another-storage-account"></a>Zkopírování adresáře do jiného účtu úložiště

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Příklad** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>Kopírování kontejnerů do jiného účtu úložiště

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Příklad** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Kopírování všech kontejnerů, adresářů a objektů blob do jiného účtu úložiště

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **Příklad** | `azcopy cp "https://mysourceaccount.blob.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>Synchronizovat soubory

Obsah místního systému souborů můžete synchronizovat s kontejnerem objektů BLOB. Synchronizace je jednosměrná. Jinými slovy, jste si zvolili, který z těchto dvou koncových bodů je zdroj a který je cílový.

> [!NOTE]
> V současné době je tento scénář podporován pouze pro účty, které nemají hierarchický obor názvů. Aktuální verze AzCopy se nesynchronizuje mezi ostatními zdroji a cíli (například: Úložiště souborů nebo Amazon Web Services (AWS) S3).

`sync` Příkaz porovná názvy souborů a poslední upravená časová razítka. Nastavením volitelného příznaku na `true` hodnotu nebo `prompt` odstraňte soubory v cílovém adresáři, pokud už tyto soubory ve zdrojovém adresáři neexistují. `--delete-destination`

Pokud jste `--delete-destination` příznak nastavili `true` tak, aby AzCopy odstranit soubory bez zadání výzvy. Pokud chcete, aby se zobrazila výzva před tím, než AzCopy odstraní soubor `--delete-destination` , nastavte `prompt`příznak na.

> [!NOTE]
> Chcete-li zabránit nechtěnému odstranění, před použitím `--delete-destination=prompt|true` příznaku zajistěte, aby byla funkce obnovitelného [odstranění](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) povolena.

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Aktualizace kontejneru se změnami v místním systému souborů

V tomto případě je kontejnerem cíl a místní systém souborů je zdroj.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy sync "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Příklad** | `azcopy sync "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Aktualizace místního systému souborů se změnami kontejneru

V tomto případě je místní systém souborů cílový a kontejner je zdroj.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myDirectory" --recursive` |
| **Příklad** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myDirectory" --recursive` |
|

## <a name="next-steps"></a>Další postup

Další příklady najdete v některém z těchto článků:

- [Začínáme s AzCopy](storage-use-azcopy-v10.md)

- [Kurz: Migrace místních dat do cloudového úložiště pomocí AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Přenos dat pomocí AzCopy a úložiště souborů](storage-use-azcopy-files.md)

- [Přenos dat pomocí kontejnerů AzCopy a Amazon S3](storage-use-azcopy-s3.md)

- [Konfigurace, optimalizace a řešení potíží s AzCopy](storage-use-azcopy-configure.md)
