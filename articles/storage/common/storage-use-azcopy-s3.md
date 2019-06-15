---
title: Přenos dat do služby Azure Storage z kontejnerů Amazon S3 s použitím AzCopy v10 | Dokumentace Microsoftu
description: Přenos dat pomocí AzCopy a Amazon S3 intervalů
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 04/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 414573a90e5a7b7de845b65e6e88715a26024f10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67125793"
---
# <a name="copy-data-from-amazon-s3-buckets-by-using-azcopy"></a>Kopírování dat z Amazon S3 kontejnerů pomocí AzCopy

AzCopy je nástroj příkazového řádku, který vám pomůže zkopírovat objekty BLOB nebo souborů do nebo z účtu úložiště. Tento článek vám pomůže zkopírovat objekty, adresářů a kontejnerů z Amazon Web Services (AWS) S3 do Azure blob storage pomocí AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Zvolte, jak vám poskytnout přihlašovací údaje pro autorizaci

* K autorizaci pomocí služby Azure Storage pomocí Azure Active Directory (AD), nebo token sdíleného přístupového podpisu (SAS).

* Chcete-li autorizovat s AWS S3, použijte AWS přístupový klíč a tajný přístupový klíč.

### <a name="authorize-with-azure-storage"></a>Autorizovat s Azure Storage

Najdete v článku [Začínáme s AzCopy](storage-use-azcopy-v10.md) článku stáhnout AzCopy a zvolte, jak vám poskytnout přihlašovací údaje pro ověření do služby storage.

> [!NOTE]
> V příkladech v tomto článku se předpokládá, že jste ověřit svoji identitu pomocí `AzCopy login` příkazu. AzCopy pak používá účet Azure AD k autorizaci přístupu k datům ve službě Blob storage.
>
> Pokud byste chtěli raději použít SAS token k autorizaci přístupu k datům objektu blob, můžete připojit tento token na adresu URL prostředku v každém z nich AzCopy.
>
> Například: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Autorizovat s AWS S3

Shromažďovat AWS přístupový klíč a tajný přístupový klíč a pak nastavte tyto proměnné prostředí:

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **MacOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Kopírování objektů, adresářů a kontejnerů

Využívá nástroj AzCopy [Vložit blok z adresy URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) rozhraní API, tak, aby se data zkopírovat přímo mezi AWS S3 a servery úložiště. Operace Kopírovaní nepoužívejte šířky pásma sítě v počítači.

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Pokud se rozhodnete k odebrání dat z vašich kbelíků S3 po operaci kopírování, nezapomeňte ověřit, že data byla správně zkopírována do vašeho účtu úložiště odebrat data.

### <a name="copy-an-object"></a>Kopírování objektu

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<object-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>"` |
| **Příklad** | `azcopy cp "https://s3.amazonaws.com/mybucket/myobject" "https://mystorageaccount.blob.core.windows.net/mycontainer/myblob"` |

> [!NOTE]
> Příklady v tomto článku použít styl cestu adresy URL pro intervalů AWS S3 (například: `http://s3.amazonaws.com/<bucket-name>`). 
>
> Můžete také použít virtuální adresy URL hostovaného stylu i (například: `http://bucket.s3.amazonaws.com`). 
>
> Další informace o virtuálního hostování kontejnerů najdete v tématu [virtuálního hostování z kontejnerů]] (https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html).

### <a name="copy-a-directory"></a>Kopírování adresáře

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<directory-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>" --recursive=true` |
| **Příklad** | `azcopy cp "https://s3.amazonaws.com/mybucket/mydirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory" --recursive=true` |

### <a name="copy-a-bucket"></a>Zkopírujte kontejneru

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true` |
| **Příklad** | `azcopy cp "https://s3.amazonaws.com/mybucket" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Zkopírujte všechny kbelíky ve všech oblastech

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "https://s3.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **Příklad** | `azcopy cp "https://s3.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Zkopírujte všechny kbelíky v určité oblasti S3

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "https://s3-<region-name>.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **Příklad** | `azcopy cp "https://s3-rds.eu-north-1.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Zpracovávat rozdíly v objektu pravidla pojmenování

AWS S3 má jinou sadu zásady vytváření názvů pro názvy kbelíku porovnání s kontejnery objektů blob v Azure. Si můžete přečíst o nich [tady](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules). Pokud se rozhodnete kopírovat skupinu kontejnerů do účtu služby Azure storage, operace kopírování může selhat z důvodu pojmenování rozdíly.

Dva z nejběžnějších problémů, které mohou nastat; zpracovává AzCopy bloků, které obsahují období a bloků, které obsahují po sobě jdoucí pomlčky. AWS S3 kbelíku názvy můžou obsahovat tečky a pomlčky, ale nikoli kontejner ve službě Azure. AzCopy nahrazuje tečky, spojovníky a číslem, který představuje počet po sobě jdoucí spojovníky po sobě jdoucí pomlčky (Příklad: kontejneru s názvem `my----bucket` stane `my-4-bucket`. 

Jak AzCopy se zkopíruje soubory, také kontroluje pro kolizím a pokusy o jejich řešení. Například, pokud existují bloky s názvem `bucket-name` a `bucket.name`, AzCopy řeší kontejneru s názvem `bucket.name` prvního k `bucket-name` a potom do `bucket-name-2`.

## <a name="handle-differences-in-object-metadata"></a>Zpracovávat rozdíly v metadata objektu

AWS S3 a Azure umožňují různé sady znaků v názvech klíčů objektu. Si můžete přečíst o znacích, AWS S3 používá [tady](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys). Na straně Azure klíče objektu blob dodržovat pravidla pojmenování [ C# identifikátory](https://docs.microsoft.com/dotnet/csharp/language-reference/).

Jako součást AzCopy `copy` příkazu, můžete zadat hodnotu pro volitelný `s2s-invalid-metadata-handle` příznak, který určuje, jak byste chtěli zpracování souborů, kde metadata souboru obsahuje nekompatibilní názvy klíčů. Následující tabulka popisuje každý hodnota příznaku.

| Hodnota příznaku | Popis  |
|--------|-----------|
| **ExcludeIfInvalid** | (Výchozí možnost) Metadata není součástí převedený objekt. AzCopy zaprotokoluje upozornění. |
| **FailIfInvalid** | Objekty nejsou zkopírovány. AzCopy zaznamená chybu do protokolu a zahrne tuto chybu počet selhání, který se zobrazí v souhrnu přenosu.  |
| **RenameIfInvalid**  | AzCopy řeší klíč neplatná metadata a zkopíruje objekt do Azure pomocí dvojice klíč-hodnota přeložit metadat. Informace o tom právě popíše, co přebírá AzCopy k přejmenování klíče objektu, najdete v článku [jak AzCopy přejmenuje klíče objektu](#rename-logic) níže v části. Pokud nástroj AzCopy nelze přejmenovat klíč, nebudou zkopírovány objektu. |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>Jak AzCopy přejmenuje klíče objektu

AzCopy provádí tyto kroky:

1. Nahradí neplatné znaky s podtržítko (_).

2. Přidá řetězec `rename_` začátek nový klíč platný.

   Tento klíč se použije k uložení původní metadata **hodnota**.

3. Přidá řetězec `rename_key_` začátek nový klíč platný.
   Tento klíč se použije k uložení původní metadata neplatný **klíč**.
   Pokusí se obnovit metadata v Azure na straně, protože klíč metadat se zachová jako hodnotu na službu Blob storage můžete použít tento klíč.

## <a name="next-steps"></a>Další postup

Další příklady najdete v některém z těchto článků:

- [Začínáme s AzCopy](storage-use-azcopy-v10.md)

- [Přenos dat pomocí AzCopy a blob storage](storage-use-azcopy-blobs.md)

- [Přenos dat pomocí AzCopy a file storage](storage-use-azcopy-files.md)

- [Konfigurace, optimalizovat a řešení potíží s AzCopy](storage-use-azcopy-configure.md)