---
title: Kopírování ze služby Google Cloud Storage do Azure Storage s využitím AzCopy | Microsoft Docs
description: Pomocí AzCopy můžete kopírovat data z Google Cloud Storage do Azure Storage. AzCopy je nástroj příkazového řádku, pomocí kterého můžete kopírovat objekty blob nebo soubory do nebo z účtu úložiště.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 3b2ad11abb7d1a3e64deef1ca49d9f84f03e5879
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498335"
---
# <a name="copy-data-from-google-cloud-storage-to-azure-storage-by-using-azcopy-preview"></a>Kopírování dat ze služby Google Cloud Storage do Azure Storage pomocí AzCopy (Preview)

AzCopy je nástroj příkazového řádku, pomocí kterého můžete kopírovat objekty blob nebo soubory do nebo z účtu úložiště. Tento článek vám pomůže zkopírovat objekty, adresáře a bloky z Google Cloud Storage do Azure Blob Storage pomocí AzCopy.

> [!IMPORTANT]
> Kopírování dat z Google Cloud Storage do Azure Storage je v současnosti ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="choose-how-youll-provide-authorization-credentials"></a>Výběr způsobu zadávání přihlašovacích údajů pro autorizaci

* K autorizaci pomocí Azure Storage použijte token Azure Active Directory (AD) nebo Shared Access Signature (SAS).

* K autorizaci se službou Google Cloud Storage použijte klíč účtu služby.

### <a name="authorize-with-azure-storage"></a>Autorizovat pomocí Azure Storage

V článku Začínáme [s AzCopy](storage-use-azcopy-v10.md) si můžete stáhnout AzCopy a seznamte se s různými způsoby, jak můžete službě úložiště poskytnout autorizační přihlašovací údaje.

> [!NOTE] 
> V příkladech v tomto článku se předpokládá, že jste zadali autorizační přihlašovací údaje pomocí Azure Active Directory (Azure AD).
>
> Pokud místo toho chcete použít token SAS k autorizaci přístupu k datům objektu blob, můžete tento token připojit k adrese URL prostředku v každém příkazu AzCopy. Příklad: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

### <a name="authorize-with-google-cloud-storage"></a>Autorizace pomocí Google Cloud Storage

K autorizaci se službou Google Cloud Storage použijete klíč účtu služby. Informace o tom, jak vytvořit klíč účtu služby, najdete v tématu [vytváření a Správa klíčů účtu služby](https://cloud.google.com/iam/docs/creating-managing-service-account-keys).

Po získání klíče služby nastavte `GOOGLE_APPLICATION_CREDENTIALS` proměnnou prostředí na absolutní cestu k souboru klíče účtu služby:

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | `set GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **Linux** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **macOS** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |

## <a name="copy-objects-directories-and-buckets"></a>Kopírování objektů, adresářů a kontejnerů

AzCopy používá [blok Put z](/rest/api/storageservices/put-block-from-url) rozhraní API URL, takže se data zkopírují přímo mezi cloudovým úložištěm Google a úložnými servery. Tyto operace kopírování nepoužívají šířku pásma sítě vašeho počítače.

> [!TIP]
> Příklady v této části uzavírají argumenty cesty s jednoduchými uvozovkami (' '). Použijte jednoduché uvozovky ve všech příkazových prostředích s výjimkou příkazového prostředí systému Windows (cmd.exe). Pokud používáte příkazové prostředí systému Windows (cmd.exe), uzavřete argumenty cesty pomocí dvojitých uvozovek ("") místo jednoduchých uvozovek (' ').

 Tyto příklady také fungují s účty, které mají hierarchický obor názvů. [Přístup k více protokolům na data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) umožňuje použít stejnou syntaxi URL ( `blob.core.windows.net` ) na těchto účtech. 

### <a name="copy-an-object"></a>Zkopírování objektu

Použijte stejnou syntaxi adresy URL ( `blob.core.windows.net` ) pro účty, které mají hierarchický obor názvů.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'`

**Příklad**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'
```


### <a name="copy-a-directory"></a>Kopírování adresáře

Použijte stejnou syntaxi adresy URL ( `blob.core.windows.net` ) pro účty, které mají hierarchický obor názvů.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Příklad**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

> [!NOTE]
> Tento příklad připojí `--recursive` příznak ke zkopírování souborů ve všech podadresářích.

### <a name="copy-the-contents-of-a-directory"></a>Kopírování obsahu adresáře

Obsah adresáře můžete zkopírovat bez zkopírování samotného adresáře, který obsahuje zástupný symbol (*).

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Příklad**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

### <a name="copy-a-cloud-storage-bucket"></a>Kopírování kontejneru cloudového úložiště

Použijte stejnou syntaxi adresy URL ( `blob.core.windows.net` ) pro účty, které mají hierarchický obor názvů.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Příklad**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-all-buckets-in-a-google-cloud-project"></a>Kopírování všech kontejnerů v projektu Google Cloud 

Nejprve nastavte `GOOGLE_CLOUD_PROJECT` ID projektu pro Google Cloud Project.

Použijte stejnou syntaxi adresy URL ( `blob.core.windows.net` ) pro účty, které mají hierarchický obor názvů.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Příklad**

```azcopy
azcopy copy 'https://storage.cloud.google.com/' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-a-subset-of-buckets-in-a-google-cloud-project"></a>Kopírování podmnožiny kontejnerů v projektu Google Cloud 

Nejprve nastavte `GOOGLE_CLOUD_PROJECT` ID projektu pro Google Cloud Project.

Zkopírujte podmnožinu kontejnerů pomocí zástupného znaku (*) v názvu kontejneru. Použijte stejnou syntaxi adresy URL ( `blob.core.windows.net` ) pro účty, které mají hierarchický obor názvů.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/<bucket*name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Příklad**

```azcopy
azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

## <a name="handle-differences-in-bucket-naming-rules"></a>Zpracování rozdílů v pravidlech pojmenovávání sad

Google Cloud Storage má různé sady zásad pro pojmenovávání názvů sad, které jsou ve srovnání s kontejnery objektů blob Azure. O nich si můžete přečíst [tady](https://cloud.google.com/storage/docs/naming-buckets). Pokud se rozhodnete zkopírovat skupinu kontejnerů do účtu služby Azure Storage, operace kopírování může selhat kvůli rozdílům v pojmenování.

AzCopy zpracovává tři nejběžnější problémy, které mohou nastat; sady, které obsahují tečky, intervaly obsahující po sobě jdoucí spojovníky a bloky obsahující podtržítka. Názvy kontejnerů Google Cloud Storage můžou obsahovat tečky a po sobě jdoucí pomlčky, ale kontejner v Azure nemůže. AzCopy nahrazuje tečky pomlčkami a po sobě jdoucích spojovníků číslo představující počet po sobě jdoucích spojovníků (například: sada s názvem `my----bucket` se stala `my-4-bucket` . Pokud má název intervalu podtržítko ( `_` ), pak AzCopy nahradí podtržítko spojovníkem (například: sada s názvem `my_bucket` se stala `my-bucket` . 

## <a name="handle-differences-in-object-naming-rules"></a>Zpracování rozdílů v pravidlech pojmenovávání objektů

Google Cloud Storage má různé sady zásad vytváření názvů pro názvy objektů ve srovnání s objekty blob Azure. O nich si můžete přečíst [tady](https://cloud.google.com/storage/docs/naming-objects).

Azure Storage nepovoluje, aby názvy objektů (nebo žádný segment v cestě virtuálního adresáře) končily koncovými tečkami (například `my-bucket...` ). Koncové tečky jsou při provádění operace kopírování zkráceny. 

## <a name="handle-differences-in-object-metadata"></a>Zpracování rozdílů v metadatech objektů

Služba Google Cloud Storage a Azure povoluje v názvech klíčů objektů různé sady znaků. Další informace o metadatech najdete v Google Cloud [Storage.](https://cloud.google.com/storage/docs/metadata) Na straně Azure se klíče objektů objektů BLOB řídí pravidly pro pojmenování [identifikátorů C#](/dotnet/csharp/language-reference/).

Jako součást `copy` příkazu AzCopy můžete zadat hodnotu volitelného `s2s-handle-invalid-metadata` příznaku, který určuje, jak chcete zpracovat soubory, kde metadata souboru obsahují nekompatibilní názvy klíčů. V následující tabulce jsou popsány všechny hodnoty příznaků.

| Hodnota příznaku | Popis  |
|--------|-----------|
| **ExcludeIfInvalid** | (Výchozí možnost) Tato metadata nejsou obsažena v přenesených objektech. AzCopy zaznamená upozornění. |
| **FailIfInvalid** | Objekty nejsou kopírovány. AzCopy zaznamená chybu a zahrne tuto chybu do neúspěšného počtu, který se zobrazí v souhrnu přenosu.  |
| **RenameIfInvalid**  | AzCopy přeloží Neplatný klíč metadat a zkopíruje objekt do Azure s použitím dvojice hodnot klíče přeložené metadat. Informace o tom, jaké kroky AzCopy přejmenovává klíče objektů, najdete v části [jak AzCopy přejmenuje klíče objektů](#rename-logic) níže. Pokud AzCopy nemůže klíč přejmenovat, objekt se nezkopíruje. |

<a id="rename-logic"></a>

### <a name="how-azcopy-renames-object-keys"></a>Jak AzCopy přejmenuje klíče objektů

AzCopy provádí tyto kroky:

1. Nahradí neplatné znaky znakem _.

2. Přidá řetězec `rename_` na začátek nového platného klíče.

   Tento klíč se použije k uložení původní **hodnoty** metadat.

3. Přidá řetězec `rename_key_` na začátek nového platného klíče.
   Tento klíč se použije k uložení původních metadat neplatného **klíče**.
   Tento klíč můžete použít k pokusu o obnovení metadat v Azure vedle sebe, protože klíč metadat se uchová jako hodnota ve službě BLOB Storage.

## <a name="next-steps"></a>Další kroky

Další příklady najdete v těchto článcích:

- [Příklady: Odeslat](storage-use-azcopy-blobs-upload.md)
- [Příklady: Stáhnout](storage-use-azcopy-blobs-download.md)
- [Příklady: Kopírování mezi účty](storage-use-azcopy-blobs-copy.md)
- [Příklady: Synchronizace](storage-use-azcopy-blobs-synchronize.md)
- [Příklady: Kbelíky Amazon S3](storage-use-azcopy-s3.md)
- [Příklady: soubory Azure](storage-use-azcopy-files.md)
- [Kurz: Migrace místních dat do cloudového úložiště pomocí AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

V těchto článcích najdete postup konfigurace nastavení, optimalizace výkonu a řešení potíží:

- [Nastavení konfigurace AzCopy](storage-ref-azcopy-configuration-settings.md)
- [Optimalizace výkonu AzCopy](storage-use-azcopy-optimize.md)
- [Řešení potíží s AzCopy v10 za účelem v Azure Storage pomocí souborů protokolu](storage-use-azcopy-configure.md)