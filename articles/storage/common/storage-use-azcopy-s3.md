---
title: Kopírování dat z Amazons3 do Služby Azure Storage pomocí AzCopy | Dokumenty společnosti Microsoft
description: Přenos dat pomocí kbelíků AzCopy a Amazon S3
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: a3180593eaf8c01c772fd761d88b5f5b9f7657ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941509"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>Kopírování dat z Amazons3 do Úložiště Azure pomocí AzCopy

AzCopy je nástroj příkazového řádku, který můžete použít ke kopírování objektů BLOB nebo souborů do nebo z účtu úložiště. Tento článek vám pomůže kopírovat objekty, adresáře a kbelíky z Amazon Web Services (AWS) S3 do úložiště objektů blob Azure pomocí AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Zvolte, jak budete zajišťovali autorizační pověření.

* Pokud chcete autorizovat pomocí Úložiště Azure, použijte Azure Active Directory (AD) nebo token sdíleného přístupového podpisu (SAS).

* Chcete-li autorizovat s AWS S3, použijte přístupový klíč AWS a tajný přístupový klíč.

### <a name="authorize-with-azure-storage"></a>Autorizace pomocí Azure Storage

Podívejte se na [článek Začínáme s AzCopy,](storage-use-azcopy-v10.md) kde si můžete stáhnout AzCopy, a zvolte, jak budete poskytovat autorizační pověření službě úložiště.

> [!NOTE]
> Příklady v tomto článku předpokládají, že jste ověřili `AzCopy login` identitu pomocí příkazu. AzCopy pak používá váš účet Azure AD k autorizaci přístupu k datům v úložišti objektů Blob.
>
> Pokud chcete raději použít token SAS k autorizaci přístupu k datům objektů blob, můžete tento token připojit k adrese URL prostředku v každém příkazu AzCopy.
>
> Například: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Autorizace s AWS S3

Shromážděte přístupový klíč AWS a tajný přístupový klíč a nastavte tyto proměnné prostředí:

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Macos** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Kopírování objektů, adresářů a bloků

AzCopy používá rozhraní [URL put block from,](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) takže data se kopírují přímo mezi AWS S3 a servery úložiště. Tyto operace kopírování nepoužívají šířku pásma sítě počítače.

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Pokud se po operaci kopírování rozhodnete odebrat data z bloků S3, před odebráním dat ověřte, zda byla data správně zkopírována do účtu úložiště.

> [!TIP]
> Příklady v této části uzavírají argumenty cesty s jednoduchými uvozovkami (''). Ve všech příkazových prostředích s výjimkou prostředí Windows Command Shell (cmd.exe) používejte jednoduché uvozovky. Pokud používáte prostředí Windows Command Shell (cmd.exe), uzavřete argumenty cesty s dvojitými uvozovkami ("") namísto jednoduchých uvozovek ('').

 Tyto příklady také pracují s účty, které mají hierarchický obor názvů. [Víceprotokolový přístup v úložišti Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) `blob.core.windows.net`umožňuje používat u těchto účtů stejnou syntaxi adresy URL ( ). 

### <a name="copy-an-object"></a>Kopírování objektu

Pro účty, které`blob.core.windows.net`mají hierarchický obor názvů, použijte stejnou syntaxi adresy URL ( ).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **Příklad** | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |

> [!NOTE]
> Příklady v tomto článku používají adresy URL ve stylu cesty pro kontejnery AWS S3 (například: `http://s3.amazonaws.com/<bucket-name>`). 
>
> Můžete také použít virtuální adresy URL hostovaného stylu `http://bucket.s3.amazonaws.com`(například: ). 
>
> Chcete-li se dozvědět více o virtuálním hostingu kbelíků, viz [Virtual Hosting of Buckets]](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html).

### <a name="copy-a-directory"></a>Kopírování adresáře

Pro účty, které`blob.core.windows.net`mají hierarchický obor názvů, použijte stejnou syntaxi adresy URL ( ).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Příklad** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Příklad** (hierarchický obor názvů)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-bucket"></a>Kopírování bloku

Pro účty, které`blob.core.windows.net`mají hierarchický obor názvů, použijte stejnou syntaxi adresy URL ( ).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true` |
| **Příklad** | `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |
| **Příklad** (hierarchický obor názvů)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Kopírování všech bloků ve všech oblastech

Pro účty, které`blob.core.windows.net`mají hierarchický obor názvů, použijte stejnou syntaxi adresy URL ( ).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Příklad** | `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Příklad** (hierarchický obor názvů)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Kopírování všech bloků v určité oblasti S3

Pro účty, které`blob.core.windows.net`mají hierarchický obor názvů, použijte stejnou syntaxi adresy URL ( ).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Příklad** | `azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Příklad** (hierarchický obor názvů)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Zpracování rozdílů v pravidlech pojmenování objektů

AWS S3 má jinou sadu konvencí pojmenování pro názvy bloků ve srovnání s kontejnery objektů blob Azure. Můžete si přečíst o nich [zde](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules). Pokud se rozhodnete zkopírovat skupinu bloků do účtu úložiště Azure, operace kopírování může selhat z důvodu rozdílů názvů.

AzCopy zpracovává dva z nejběžnějších problémů, které mohou vzniknout; kbelíky, které obsahují tečky a kbelíky, které obsahují po sobě jdoucí chlohy. Názvy bloků AWS S3 mohou obsahovat tečky a po sobě jdoucí spojovníky, ale kontejner v Azure nemůže. AzCopy nahradí tečky spojovníky a po sobě jdoucími spojovníky číslem, které představuje počet `my----bucket` `my-4-bucket`po sobě jdoucích spojovníků (například: segment s názvem se změní . 

Také, jak AzCopy kopíruje přes soubory, kontroluje pojmenování kolizí a pokusí se je vyřešit. Například pokud jsou kbelíky s `bucket-name` `bucket.name`názvem a , AzCopy `bucket.name` překládá kontejner s názvem první `bucket-name` a potom `bucket-name-2`na .

## <a name="handle-differences-in-object-metadata"></a>Zpracování rozdílů v metadatech objektu

AWS S3 a Azure umožňují různé sady znaků v názvech klíčů objektů. Můžete si přečíst o znaky, které Používá AWS S3 [zde](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys). Na straně Azure klíče objektů objektů objektů blob dodržovat pravidla pojmenování [identifikátorů Jazyka C#](https://docs.microsoft.com/dotnet/csharp/language-reference/).

Jako součást příkazu `copy` AzCopy můžete zadat hodnotu `s2s-invalid-metadata-handle` pro volitelný příznak, který určuje, jak chcete zpracovávat soubory, kde metadata souboru obsahují nekompatibilní názvy klíčů. Následující tabulka popisuje každou hodnotu příznaku.

| Hodnota příznaku | Popis  |
|--------|-----------|
| **ExcludeIfInvalid** | (Výchozí volba) Metadata nejsou zahrnuta v přeneseném objektu. AzCopy zaznamená upozornění. |
| **FailIfInvalid** | Objekty se nekopírují. AzCopy zaznamená chybu a tuto chybu zahrne do neúspěšného počtu, který se zobrazí v souhrnu přenosu.  |
| **PřejmenovatIfNeplatným**  | AzCopy vyřeší neplatný klíč metadat a zkopíruje objekt do Azure pomocí dvojice hodnot klíče vyřešených metadat. Chcete-li se přesně dozvědět, jaké kroky AzCopy provede při přejmenování klíčů objektů, přečtěte si níže oddíl [Jak AzCopy přejmenovává klíče objektů.](#rename-logic) Pokud azCopy nemůže přejmenovat klíč, objekt se nezkopíruje. |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>Jak AzCopy přejmenovává klíče objektů

AzCopy provádí tyto kroky:

1. Nahradí neplatné znaky znakem _..

2. Přidá řetězec `rename_` na začátek nového platného klíče.

   Tento klíč bude použit k uložení původní **hodnoty**metadat .

3. Přidá řetězec `rename_key_` na začátek nového platného klíče.
   Tento klíč bude použit k uložení původního neplatného **klíče**metadat .
   Tento klíč můžete použít k pokusu a obnovení metadat na straně Azure, protože klíč metadat je zachovánjako hodnota ve službě úložiště objektů blob.

## <a name="next-steps"></a>Další kroky

Další příklady najdete v některém z těchto článků:

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)

- [Přenos dat pomocí úložiště Objektů blob AzCopy a objektů blob](storage-use-azcopy-blobs.md)

- [Přenos dat pomocí AzCopy a ukládání souborů](storage-use-azcopy-files.md)

- [Konfigurace, optimalizace a řešení potíží s azcopy](storage-use-azcopy-configure.md)