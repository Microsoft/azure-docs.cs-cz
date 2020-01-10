---
title: Azure Storage ukázky pomocí JavaScriptu | Microsoft Docs
description: Zobrazit, stáhnout a spustit vzorový kód a aplikace pro Azure Storage. Objevte úvodní ukázky pro objekty blob, fronty, tabulky a soubory pomocí klientských knihoven pro úložiště JavaScript/Node. js.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/26/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 44fe68b8b04a1192c928e04c7d2a9d147f400130
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748288"
---
# <a name="azure-storage-samples-using-javascript"></a>Ukázky Azure Storage pomocí JavaScriptu

Následující tabulky obsahují přehled našich ukázkových úložišť a scénářů popsaných v jednotlivých ukázkách. Kliknutím na odkazy zobrazíte odpovídající vzorový kód v GitHubu.

> [!NOTE]
> Tyto ukázky používají knihovnu Azure Storage JavaScript v10 za účelem Library. V12 Code najdete v tématu [ukázky](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples) v úložišti GitHub.

## <a name="blob-samples-v10"></a>Ukázky objektů BLOB (v10 za účelem)

| **Scénář** | **Ukázkový kód** |
|--------------|-----------------|
| Objekt blob bloku | [Začínáme se službou Azure Blob Service v JavaScriptu](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L43) |
| Šifrování na straně klienta | [Správa klíčů účtu úložiště v hodnotě klíče Azure pomocí JavaScriptu](https://github.com/Azure-Samples/key-vault-node-storage-accounts) |
| Zkopírování objektu Blob | [Začínáme se službou Azure Blob Service v JavaScriptu](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L73) |
| Vytvoření kontejneru | [Začínáme se službou Azure Blob Service v JavaScriptu](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L54) |
| Odstranit objekt Blob | [Začínáme se službou Azure Blob Service v JavaScriptu](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L103) |
| Odstranění kontejneru | [Začínáme se službou Azure Blob Service v JavaScriptu](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L110) |
| Metadata objektu BLOB | [Začínáme se službou Azure Blob Service v JavaScriptu](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L538) |
| Vlastnosti objektu BLOB | [Začínáme se službou Azure Blob Service v JavaScriptu](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L478) |
| Seznam ACL kontejneru | [Začínáme se službou Azure Blob Service v JavaScriptu](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L444) |
| Metadata kontejneru | [Začínáme se službou Azure Blob Service v JavaScriptu](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L409) |
| Vlastnosti kontejneru | [Začínáme se službou Azure Blob Service v JavaScriptu](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L377) |
| Získat rozsahy stránek | [Začínáme se službou Azure Blob Service v JavaScriptu](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L170) |
| Operace Lease Blob | [Začínáme se službou Azure Blob Service v JavaScriptu](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L216) |
| Kontejner zapůjčení | [Začínáme se službou Azure Blob Service v JavaScriptu](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L185) |
| Výpis objektu BLOB nebo kontejneru | [Začínáme se službou Azure Blob Service v JavaScriptu](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L134) |
| Objekt blob stránky | [Začínáme se službou Azure Blob Service v JavaScriptu](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L129) |
| SAS | [Sdílený přístupový podpis v JavaScriptu](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L257) |
| Vlastnosti služby | [Začínáme se službou Azure Blob Service v JavaScriptu](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L308) |
| Nastavení pravidel CORS | [Začínáme se službou Azure Blob Service v JavaScriptu](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L152) |
| Objekt BLOB snímku | [Začínáme se službou Azure Blob Service v JavaScriptu](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L79) |

## <a name="file-samples-v10"></a>Ukázky souborů (v10 za účelem)

| **Scénář** | **Ukázkový kód** |
|--------------|-----------------|
| Vytváření sdílených složek, adresářů a souborů | [Začínáme se službou Azure File Service v JavaScriptu](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L97) |
| Odstranit sdílené složky/adresáře/soubory | [Začínáme se službou Azure File Service v JavaScriptu](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L135) |
| Stažení souborů | [Začínáme se službou Azure File Service v JavaScriptu](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L128) |
| Výpis adresářů a souborů | [Začínáme se službou Azure File Service v JavaScriptu](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L115) |
| Vypsat sdílené složky | [Začínáme se službou Azure File Service v JavaScriptu](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L187) |

## <a name="queue-samples-v10"></a>Ukázky front (v10 za účelem)

| **Scénář** | **Ukázkový kód** |
|--------------|-----------------|
| Přidat zprávu | [Začínáme ve službě Azure Queue Service v JavaScriptu](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L142) |
| Šifrování na straně klienta | [Správa klíčů účtu úložiště v Azure Key Vault pomocí JavaScriptu](https://github.com/Azure-Samples/key-vault-node-storage-accounts) |
| Vytvořit fronty | [Začínáme ve službě Azure Queue Service v JavaScriptu](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L57) |
| Odstranit zprávu | [Začínáme ve službě Azure Queue Service v JavaScriptu](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L164) |
| Odstranit frontu | [Začínáme ve službě Azure Queue Service v JavaScriptu](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L203) |
| Výpis front | [Začínáme ve službě Azure Queue Service v JavaScriptu](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L111) |
| Prohlížet zprávu | [Začínáme ve službě Azure Queue Service v JavaScriptu](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L170) |
| Seznam ACL fronty | [Začínáme ve službě Azure Queue Service v JavaScriptu](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L192) |
| Queue – pravidla pro CORS | [Začínáme ve službě Azure Queue Service v JavaScriptu](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L55) |
| Metadata fronty | [Začínáme ve službě Azure Queue Service v JavaScriptu](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L161) |
| Vlastnosti služby Queue | [Začínáme ve službě Azure Queue Service v JavaScriptu](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L94) |
| Statistika fronty | [Začínáme ve službě Azure Queue Service v JavaScriptu](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L149) |
| Aktualizovat zprávu | [Začínáme ve službě Azure Queue Service v JavaScriptu](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L176) |

## <a name="table-samples-v10"></a>Ukázky tabulek (v10 za účelem)

| **Scénář** | **Ukázkový kód** |
|--------------|-----------------|
| Entity Batch | [Začínáme se službou Azure Table Service v JavaScriptu](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L87) |
| Create Table | [Začínáme se službou Azure Table Service v JavaScriptu](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L41) |
| Odstranit entitu/tabulku | [Začínáme se službou Azure Table Service v JavaScriptu](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L67) |
| Vložit/sloučit/nahradit entitu | [Začínáme se službou Azure Table Service v JavaScriptu](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L49) |
| Seznam tabulek | [Začínáme se službou Azure Table Service v JavaScriptu](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L63) |
| Entity dotazu | [Začínáme se službou Azure Table Service v JavaScriptu](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L59) |
| Tabulky dotazů | [Začínáme se službou Azure Table Service v JavaScriptu](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L140) |
| Dotaz na rozsah | [Začínáme se službou Azure Table Service v JavaScriptu](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L102) |
| SAS | [Sdílený přístupový podpis v JavaScriptu](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L87) |
| Seznam ACL tabulky | [Začínáme se službou Azure Table Service v JavaScriptu](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L255) |
| Pravidla pro tabulku CORS | [Začínáme se službou Azure Table Service v JavaScriptu](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L149) |
| Vlastnosti tabulky | [Začínáme se službou Azure Table Service v JavaScriptu](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L188) |
| Statistiky tabulek | [Začínáme se službou Azure Table Service v JavaScriptu](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L243) |
| Aktualizovat entitu | [Začínáme se službou Azure Table Service v JavaScriptu](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L49) |

## <a name="azure-code-samples-library"></a>Knihovna ukázek kódu Azure

Kompletní ukázkovou knihovnu zobrazíte tak, že přejdete do knihovny [ukázek kódu Azure](https://azure.microsoft.com/resources/samples/?service=storage) , která obsahuje ukázky pro Azure Storage, které můžete stáhnout a spustit místně. Knihovna ukázek kódu poskytuje vzorový kód ve formátu ZIP. Případně můžete procházet a klonovat úložiště GitHub pro každou ukázku.

[!INCLUDE [storage-node-samples-include](../../../includes/storage-node-samples-include.md)]

## <a name="getting-started-guides"></a>Úvodní příručky

Pokud hledáte pokyny k instalaci a Začínáme s klientskými knihovnami Azure Storage, přečtěte si následující příručky.

* [Začínáme se službou Azure Blob Service v JavaScriptu](../blobs/storage-quickstart-blobs-nodejs.md)
* [Začínáme ve službě Azure Queue Service v JavaScriptu](../queues/storage-nodejs-how-to-use-queues.md)
* [Začínáme se službou Azure Table Service v JavaScriptu](../../cosmos-db/table-storage-how-to-use-nodejs.md)

## <a name="next-steps"></a>Další kroky

Informace o ukázkách pro jiné jazyky:

* .NET: [Azure Storage ukázky pomocí .NET](storage-samples-dotnet.md)
* Java: [Azure Storage ukázky pomocí jazyka Java](storage-samples-java.md)
* Python: [Azure Storage ukázky pomocí Pythonu](storage-samples-python.md)
* Všechny ostatní jazyky: [ukázky Azure Storage](storage-samples.md)
