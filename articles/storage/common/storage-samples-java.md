---
title: Ukázky Azure Storage pomocí Java | Microsoft Docs
description: Zobrazit, stáhnout a spustit vzorový kód a aplikace pro Azure Storage. Seznamte se s úvodními ukázkami objektů blob, front, tabulek a souborů pomocí klientských knihoven pro úložiště Java.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/03/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 4348400e66e4b975d1eeb899cab8bb5024260149
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721839"
---
# <a name="azure-storage-samples-using-java"></a>Ukázky Azure Storage pomocí jazyka Java

## <a name="java-sample-index"></a>Vzorový index Java

Následující tabulka poskytuje přehled našich ukázek úložiště a scénářů popsaných v jednotlivých ukázkách. Kliknutím na odkazy zobrazíte odpovídající vzorový kód v GitHubu.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Koncový bod</th><th style="font-size:110%">Scénář</th><th style="font-size:110%">Vzorový kód</th></tr></thead><tbody>
<tr>
<td rowspan="16"><b>Objekt blob</b></td>
<td>Připojit objekt blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Začínáme se službou Azure Blob Service v jazyce Java</a></td>
</tr>
<tr>
<td>Objekt blob bloku</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Začínáme se službou Azure Blob Service v jazyce Java</a></td>
</tr>
<tr>
<td>Šifrování na straně klienta</td>
<td><a href="https://github.com/Azure-Samples/storage-java-client-side-encryption">Začínáme se šifrováním na straně klienta Azure v jazyce Java</a></td>
</tr>
<tr>
<td>Zkopírování objektu Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Začínáme se službou Azure Blob Service v jazyce Java</a></td>
</tr>
<tr>
<td>Vytvoření kontejneru</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Začínáme se službou Azure Blob Service v jazyce Java</a></td>
</tr>
<tr>
<td>Odstranit objekt Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Začínáme se službou Azure Blob Service v jazyce Java</a></td>
</tr>
<tr>
<td>Odstranění kontejneru</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Začínáme se službou Azure Blob Service v jazyce Java</a></td>
</tr>
<tr>
<td>Metadata, vlastnosti/statistiky objektu BLOB</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Začínáme se službou Azure Blob Service v jazyce Java</a></td>
</tr>
<tr>
<td>Seznam ACL/metadata/vlastnosti kontejneru</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Začínáme se službou Azure Blob Service v jazyce Java</a></td>
</tr>
<tr>
<td>Získat rozsahy stránek</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java#L399">Začínáme se službou Azure Blob Service v jazyce Java</a></td>
</tr>
<tr>
<td>Objekt BLOB nebo kontejner zapůjčení</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Začínáme se službou Azure Blob Service v jazyce Java</a></td>
</tr>
<tr>
<td>Výpis objektu BLOB nebo kontejneru</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Začínáme se službou Azure Blob Service v jazyce Java</a></td>
</tr>
<tr>
<td>Objekt blob stránky</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Začínáme se službou Azure Blob Service v jazyce Java</a></td>
</tr>
<tr>
<td>SAS</td>
<td><a href="https://github.com/Azure/azure-storage-java/blob/89540f018f1160ce55619c6fe7b5f5ff57d0ce10/src/test/java/com/microsoft/azure/storage/Samples.java#L513">Ukázka testů SAS</a></td>
</tr>   
<tr>
<td>Vlastnosti služby</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Začínáme se službou Azure Blob Service v jazyce Java</a></td>
</tr>
<tr>
<td>Objekt BLOB snímku</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Začínáme se službou Azure Blob Service v jazyce Java</a></td>
</tr>
<tr>
<td rowspan="9"><b>File</b></td>
<td>Vytváření sdílených složek, adresářů a souborů</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Začínáme se službou Azure File Service v jazyce Java</a></td>
</tr>
<tr>
<td>Odstranit sdílené složky/adresáře/soubory</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Začínáme se službou Azure File Service v jazyce Java</a></td>
</tr>
<tr>
<td>Vlastnosti nebo metadata adresáře</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Začínáme se službou Azure File Service v jazyce Java</a></td>
</tr>
<tr>
<td>Stažení souborů</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Začínáme se službou Azure File Service v jazyce Java</a></td>
</tr>
<tr>
<td>Vlastnosti souboru/metadata/metriky</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Začínáme se službou Azure File Service v jazyce Java</a></td>
</tr>
<tr>
<td>Vlastnosti souborové služby</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Začínáme se službou Azure File Service v jazyce Java</a></td>
</tr>
<tr>
<td>Výpis adresářů a souborů</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Začínáme se službou Azure File Service v jazyce Java</a></td>
</tr>
<tr>
<td>Vypsat sdílené složky</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Začínáme se službou Azure File Service v jazyce Java</a></td>
</tr>
<tr>
<td>Vlastnosti sdílené složky/metadata/statistiky</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Začínáme se službou Azure File Service v jazyce Java</a></td>
</tr>
<tr>
<td rowspan="8"><b>Provedených</b></td>
<td>Přidat zprávu</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java#L63">Začínáme se službou Azure Queue v jazyce Java</a></td>
</tr>
<tr>
<td>Šifrování na straně klienta</td>
<td><a href="https://github.com/Azure-Samples/storage-java-client-side-encryption/blob/master/src/gettingstarted/KeyVaultGettingStarted.java">Začínáme se šifrováním na straně klienta Azure v jazyce Java</a></td>
</tr>
<tr>
<td>Vytvořit fronty</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Začínáme se službou Azure Queue v jazyce Java</a></td>
</tr>
<tr>
<td>Odstranit zprávu/frontu</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Začínáme se službou Azure Queue v jazyce Java</a></td>
</tr>
<tr>
<td>Prohlížet zprávu</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Začínáme se službou Azure Queue v jazyce Java</a></td>
</tr>
<tr>
<td>Seznam ACL fronty/metadata/statistiky</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Začínáme se službou Azure Queue v jazyce Java</a></td>
</tr>
<tr>
<td>Vlastnosti služby Queue</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Začínáme se službou Azure Queue v jazyce Java</a></td>
</tr>
<tr>
<td>Aktualizovat zprávu</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Začínáme se službou Azure Queue v jazyce Java</a></td>
</tr>
<tr>
<td rowspan="7"><b>Tabulka</b></td>
<td>Vytvořit tabulku</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Začínáme se službou Azure Table Storage v Javě</a></td>
</tr>
<tr>
<td>Odstranit entitu/tabulku</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Začínáme se službou Azure Table Storage v Javě</a></td>
</tr>
<tr>
<td>Vložit/sloučit/nahradit entitu</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Začínáme se službou Azure Table Storage v Javě</a></td>
</tr>
<tr>
<td>Entity dotazu</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Začínáme se službou Azure Table Storage v Javě</a></td>
</tr>
<tr>
<td>Tabulky dotazů</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Začínáme se službou Azure Table Storage v Javě</a></td>
</tr>
<tr>
<td>Seznam ACL/vlastnosti tabulky</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableAdvanced.java">Začínáme se službou Azure Table Storage v Javě</a></td>
</tr>
<tr>
<td>Aktualizovat entitu</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Začínáme se službou Azure Table Storage v Javě</a></td>
</tr>
</tbody>
</table>
<br/>

## <a name="azure-code-samples-library"></a>Knihovna ukázek kódu Azure

Kompletní ukázkovou knihovnu zobrazíte tak, že přejdete do knihovny [ukázek kódu Azure](https://azure.microsoft.com/resources/samples/?service=storage) , která obsahuje ukázky pro Azure Storage, které můžete stáhnout a spustit místně. Knihovna ukázek kódu poskytuje vzorový kód ve formátu ZIP. Případně můžete procházet a klonovat úložiště GitHub pro každou ukázku.

[!INCLUDE [storage-java-samples-include](../../../includes/storage-java-samples-include.md)]

## <a name="getting-started-guides"></a>Úvodní příručky

Pokud hledáte pokyny k instalaci a Začínáme s klientskými knihovnami Azure Storage, přečtěte si následující příručky.

* [Začínáme se službou Azure Blob Service v jazyce Java](../blobs/storage-quickstart-blobs-java.md)
* [Začínáme se službou Azure Queue v jazyce Java](../queues/storage-java-how-to-use-queue-storage.md)
* [Začínáme se službou Azure Table Storage v Javě](../../cosmos-db/table-storage-how-to-use-java.md)
* [Začínáme se službou Azure File Service v jazyce Java](../files/storage-java-how-to-use-file-storage.md)

## <a name="next-steps"></a>Další postup

Informace o ukázkách pro jiné jazyky:

* .NET: [Ukázky pro Azure Storage s použitím .NET](storage-samples-dotnet.md)
* Všechny ostatní jazyky: [Ukázky Azure Storage](storage-samples.md)
