---
title: Známé problémy s Azure Data Lake Storage Gen2 | Dokumentace Microsoftu
description: Další informace o omezeních a známých problémech s Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 369069ef9a9c562ef6ba88a46dc0ef82c4debba1
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950686"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Známé problémy s Azure Data Lake Storage Gen2

V tomto článku jsou uvedené funkce a nástroje, které se ještě nepodporují, nebo jenom částečně podporované s účty úložiště, které mají hierarchický obor názvů (Azure Data Lake Storage Gen2).

<a id="blob-apis-disabled" />

## <a name="blob-storage-apis"></a>Úložiště objektů BLOB rozhraní API

Rozhraní API pro úložiště objektů BLOB jsou zakázaná, aby nedocházelo k problémům operability funkcí, které by mohly nastat, Blob Storage protože rozhraní API pro Azure Data Lake Gen2 ještě nejsou vzájemně interoperabilní.

> [!NOTE]
> Pokud se zaregistrujete do veřejné verze Preview přístupu k více protokolům na Data Lake Storage, rozhraní BLOB API a rozhraní API Data Lake Storage Gen2 můžou pracovat se stejnými daty. Další informace najdete v tématu [přístup k více protokolům na data Lake Storage](data-lake-storage-multi-protocol-access.md).

### <a name="what-to-do-with-existing-tools-applications-and-services"></a>Co dělat s existujícími nástroji, aplikacemi a službami

Pokud některá z těchto rozhraní používají rozhraní API objektů BLOB a chcete je použít pro práci se všemi obsahy, které nahráváte do svého účtu, máte dvě možnosti.

* **Možnost 1**: Nepovolujte hierarchický obor názvů v účtu úložiště BLOB, dokud nebudou rozhraní API BLOB vzájemně ovladatelné pomocí Azure Data Lake rozhraní API Gen2. Použití účtu úložiště bez hierarchického oboru názvů znamená, že pak nebudete mít přístup k Data Lake Storage Gen2 specifickým funkcím, jako jsou seznamy řízení přístupu k adresářům a systémům souborů.

* **Možnost 2**: Zaregistrujte se do veřejné verze Preview [přístupu k více protokolům na data Lake Storage](data-lake-storage-multi-protocol-access.md). Nástroje a aplikace, které volají rozhraní API objektů blob, stejně jako funkce úložiště BLOB, jako jsou protokoly diagnostiky, můžou pracovat s účty, které mají hierarchický obor názvů.

### <a name="what-to-do-if-you-used-blob-apis-to-load-data-before-blob-apis-were-disabled"></a>Jak postupovat v případě, že jste použili rozhraní API objektů BLOB k načtení dat před zakázáním rozhraní API objektů BLOB

Pokud jste použili tato rozhraní API k načtení dat, než byly zakázány, aby bylo produkční požadavek na přístup k datům, obraťte se Microsoft Support s následujícími informacemi:

> [!div class="checklist"]
> * ID předplatného (identifikátor GUID, nikoli název).
> * Názvy účtů úložiště.
> * Bez ohledu na to, jestli jste aktivně ovlivnili produkční prostředí, a pokud ano, pro které účty úložiště?
> * I když nejsou aktivně vliv v produkčním prostředí, dejte nám vědět, jestli tato data, které se mají zkopírovat do jiného účtu úložiště z nějakého důvodu potřebujete a pokud ano, proč?

Za těchto okolností můžeme po omezené době obnovit přístup k rozhraní BLOB API, abyste mohli tato data zkopírovat do účtu úložiště, který nemá povolenou funkci hierarchického oboru názvů.

### <a name="issues-and-limitations-with-using-blob-apis-on-accounts-that-have-a-hierarchical-namespace"></a>Problémy a omezení při používání rozhraní API objektů BLOB u účtů, které mají hierarchický obor názvů

Pokud se zaregistrujete do veřejné verze Preview přístupu k více protokolům na Data Lake Storage, rozhraní BLOB API a rozhraní API Data Lake Storage Gen2 můžou pracovat se stejnými daty.

Tato část popisuje problémy a omezení s použitím rozhraní API objektů BLOB a rozhraní API pro Data Lake Storage Gen2 pro práci se stejnými daty.

* Rozhraní API objektů BLOB a rozhraní Data Lake Storage API nemůžete použít k zápisu do stejné instance souboru.

* Pokud zapisujete do souboru pomocí Data Lake Storage Gen2 rozhraní API, pak bloky tohoto souboru nebudou viditelné pro volání rozhraní API objektů BLOB [Get Block](https://docs.microsoft.com/rest/api/storageservices/get-block-list) .

* Soubor můžete přepsat buď pomocí rozhraní API Data Lake Storage Gen2 nebo rozhraní API objektů BLOB. To nebude mít vliv na vlastnosti souboru.

* Když použijete operaci [listovat BLOBs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) bez zadání oddělovače, výsledky budou zahrnovat adresáře a objekty blob.

  Pokud se rozhodnete použít oddělovač, použijte pouze lomítko (`/`). Toto je jediný podporovaný oddělovač.

* Použijete-li k odstranění adresáře rozhraní API pro [odstranění objektů BLOB](https://docs.microsoft.com/rest/api/storageservices/delete-blob) , bude tento adresář odstraněn pouze v případě, že je prázdný.

  To znamená, že nemůžete rekurzivně odstraňovat adresáře pomocí rozhraní BLOB API.

Tato rozhraní REST API pro objekty blob nejsou podporovaná:

* [Vložit objekt BLOB (stránka)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Vložit stránku](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Získat rozsahy stránek](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Objekt BLOB přírůstkového kopírování](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Vložit stránku z adresy URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Vložit objekt BLOB (připojit)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Připojit blok](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Připojit blok z adresy URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

## <a name="issues-with-unmanaged-virtual-machine-vm-disks"></a>Problémy s nespravovanými disky virtuálního počítače (VM)

Nespravované disky virtuálních počítačů nejsou podporované v účtech, které mají hierarchický obor názvů. Pokud chcete povolit hierarchický obor názvů v účtu úložiště, umístěte nespravované disky virtuálních počítačů do účtu úložiště, který nemá povolenou funkci hierarchického oboru názvů.


## <a name="support-for-other-blob-storage-features"></a>Podpora dalších funkcí úložiště objektů BLOB

Následující tabulka obsahuje seznam všech dalších funkcí a nástrojů, které ještě nejsou podporované nebo částečně podporované s účty úložiště, které mají hierarchický obor názvů (Azure Data Lake Storage Gen2).

| Funkce/nástroj    | Další informace    |
|--------|-----------|
| **Rozhraní API pro Data Lake Storage Gen2 účty úložiště** | Částečně podporováno <br><br>přístup k více protokolům na Data Lake Storage je aktuálně ve verzi Public Preview. Tato verze Preview umožňuje používat rozhraní API objektů BLOB v sadách .NET, Java, Python SDK s účty, které mají hierarchický obor názvů.  Sady SDK zatím neobsahují rozhraní API, které vám umožní pracovat s adresáři nebo nastavit seznamy řízení přístupu (ACL). K provedení těchto funkcí můžete použít Data Lake Storage Gen2 rozhraní **REST** API. |
| **AzCopy** | Podpora specifická pro verzi <br><br>Použijte pouze nejnovější verzi AzCopy ([AzCopy v10 za účelem](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Starší verze AzCopy, jako je AzCopy v 8.1, nejsou podporovány.|
| **Zásady správy životního cyklu služby Azure Blob Storage** | Podporováno pouze v případě, že se zaregistrujete do [přístupu s více protokoly na data Lake Storage](data-lake-storage-multi-protocol-access.md) Preview. Úrovně přístupu studené a archivní jsou podporovány pouze ve verzi Preview. Odstranění snímků objektů BLOB ještě není podporováno. |
| **Azure Content Delivery Network (CDN)** | Zatím nepodporováno|
| **Hledání Azure** |Podporováno pouze v případě, že se zaregistrujete do [přístupu s více protokoly na data Lake Storage](data-lake-storage-multi-protocol-access.md) Preview.|
| **Azure Storage Explorer** | Podpora specifická pro verzi <br><br>Používejte pouze verzi `1.6.0` nebo vyšší. <br>Verze `1.6.0` je k dispozici [zdarma ke stažení](https://azure.microsoft.com/features/storage-explorer/).|
| **Seznamy ACL kontejneru objektů BLOB** |Zatím nepodporováno|
| **Blobfuse** |Zatím nepodporováno|
| **Vlastní domény** |Zatím nepodporováno|
| **Průzkumník systému souborů** | Omezená podpora |
| **Protokolování diagnostiky** |Diagnostické protokoly se podporují jenom v případě, že se zaregistrujete do [protokolu pro přístup k více protokolům ve službě Data Lake Storage](data-lake-storage-multi-protocol-access.md) Preview. <br><br>Povolení protokolů v Azure Portal není aktuálně podporováno. Tady je příklad, jak povolit protokoly pomocí PowerShellu. <br><br>`$storageAccount = Get-AzStorageAccount -ResourceGroupName <resourceGroup> -Name <storageAccountName>`<br><br>`Set-AzStorageServiceLoggingProperty -Context $storageAccount.Context -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays <days>`. <br><br>Nezapomeňte zadat `Blob` jako hodnotu `-ServiceType` parametru, jak je znázorněno v tomto příkladu. 
| **Neměnné úložiště** |Zatím nepodporováno <br><br>Neměnné úložiště poskytuje možnost ukládat data v [červech (jeden způsob zápisu, čtení mnoha)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) .|
| **Vrstvy na úrovni objektů** |Studené a archivní úrovně se podporují jenom v případě, že se zaregistrujete do [přístupu k více protokolům](data-lake-storage-multi-protocol-access.md) ve službě Data Lake Storage Preview. <br><br> Všechny ostatní úrovně přístupu ještě nejsou podporované.|
| **Podpora PowerShellu a rozhraní příkazového řádku** | Omezená funkčnost <br><br>Podporují se operace správy, jako je vytváření účtu. Operace roviny dat, jako je například nahrávání a stahování souborů, jsou ve verzi Public Preview v rámci [přístupu k více protokolům na data Lake Storage](data-lake-storage-multi-protocol-access.md). Práce s adresáři a nastavování seznamů řízení přístupu (ACL) ještě není podporovaná. |
| **Statické weby** |Zatím nepodporováno <br><br>Konkrétně možnost poskytovat soubory [statickým webům](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website).|
| **Aplikace třetích stran** | Omezená podpora <br><br>Aplikace třetích stran, které používají rozhraní REST API k práci, budou fungovat i v případě, že je použijete s Data Lake Storage Gen2. <br>Aplikace, které volají rozhraní API objektů blob, budou pravděpodobně fungovat, pokud se zaregistrujete ve verzi Public Preview [přístupu s více protokoly na data Lake Storage](data-lake-storage-multi-protocol-access.md). 
| **Funkce správy verzí** |Zatím nepodporováno <br><br>To zahrnuje [snímky](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) a [obnovitelné odstranění](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).|


