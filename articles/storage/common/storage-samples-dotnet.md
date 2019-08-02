---
title: Ukázky Azure Storage pomocí .NET | Microsoft Docs
description: Zobrazit, stáhnout a spustit vzorový kód a aplikace pro Azure Storage. Seznamte se s úvodními ukázkami objektů blob, front, tabulek a souborů pomocí klientských knihoven pro úložiště .NET.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/03/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: cec6b0498adf55428fade7ae00db6550496a20bd
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721859"
---
# <a name="azure-storage-samples-using-net"></a>Ukázky Azure Storage pomocí .NET

## <a name="net-sample-index"></a>Vzorový index .NET

Následující tabulka poskytuje přehled našich ukázek úložiště a scénářů popsaných v jednotlivých ukázkách. Kliknutím na odkazy zobrazíte odpovídající vzorový kód v GitHubu.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Koncový bod</th><th style="font-size:110%">Scénář</th><th style="font-size:110%">Vzorový kód</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>Objekt blob</b></td>
<td>Připojit objekt blob</td> 
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs#L1144">Začínáme s objekty blob</a></td> 
</tr> 
<tr> 
<td>Objekt blob bloku</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Webová aplikace Fotogalerie pro Azure Blob Storage</a></td>
</tr> 
<tr> 
<td>Šifrování na straně klienta</td>
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs">Ukázky šifrování objektů BLOB</a></td>
</tr> 
<tr> 
<td>Zkopírování objektu Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Začínáme s objekty blob</a></td>
</tr> 
<tr> 
<td>Vytvoření kontejneru</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Webová aplikace Fotogalerie pro Azure Blob Storage</a></td>
</tr> 
<tr> 
<td>Odstranit objekt Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Webová aplikace Fotogalerie pro Azure Blob Storage</a></td>
</tr> 
<tr> 
<td>Odstranění kontejneru</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Začínáme s objekty blob</a></td>
</tr> 
<tr> 
<td>Metadata, vlastnosti/statistiky objektu BLOB</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Začínáme s objekty blob</a></td>
</tr> 
<tr> 
<td>Seznam ACL/metadata/vlastnosti kontejneru</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Webová aplikace Fotogalerie pro Azure Blob Storage</a></td>
</tr> 
<tr> 
<td>Získat rozsahy stránek</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Začínáme s objekty blob</a></td>
</tr> 
<tr> 
<td>Objekt BLOB nebo kontejner zapůjčení</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Začínáme s objekty blob</a></td>
</tr> 
<tr> 
<td>Výpis objektu BLOB nebo kontejneru</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Začínáme s objekty blob</a></td>
</tr> 
<tr> 
<td>Objekt blob stránky</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Začínáme s objekty blob</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Začínáme s objekty blob</a></td>
</tr>   
<tr> 
<td>Vlastnosti služby</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Začínáme s objekty blob</a></td>
</tr>           
<tr> 
<td>Objekt BLOB snímku</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs">Zálohování disků virtuálních počítačů Azure pomocí přírůstkových snímků</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>File</b></td>
<td>Vytváření sdílených složek, adresářů a souborů</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Ukázka File Storage Azure Storage .NET</a></td> 
</tr>
<tr> 
<td>Odstranit sdílené složky/adresáře/soubory</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs">Začínáme se službou Azure File Service v .NET</a></td> 
</tr> 
<tr> 
<td>Vlastnosti nebo metadata adresáře</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Ukázka File Storage Azure Storage .NET</a></td> 
</tr> 
<tr> 
<td>Stažení souborů</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Ukázka File Storage Azure Storage .NET</a></td> 
</tr> 
<tr> 
<td>Vlastnosti souboru/metadata/metriky</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Ukázka File Storage Azure Storage .NET</a></td> 
</tr> 
<tr> 
<td>Vlastnosti souborové služby</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Ukázka File Storage Azure Storage .NET</a></td> 
</tr> 
<tr> 
<td>Výpis adresářů a souborů</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Ukázka File Storage Azure Storage .NET</a></td> 
</tr>
<tr> 
<td>Vypsat sdílené složky</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Ukázka File Storage Azure Storage .NET</a></td> 
</tr>
<tr> 
<td>Vlastnosti sdílené složky/metadata/statistiky</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Ukázka File Storage Azure Storage .NET</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>Provedených</b></td>
<td>Přidat zprávu</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Začínáme se službou Azure Queue v .NET</a></td> 
</tr> 
<tr> 
<td>Šifrování na straně klienta</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs">Azure Storage šifrování na straně klienta .NET Queue</a></td> 
</tr> 
<tr> 
<td>Vytvořit fronty</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Začínáme se službou Azure Queue v .NET</a></td> 
</tr> 
<tr> 
<td>Odstranit zprávu/frontu</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Začínáme se službou Azure Queue v .NET</a></td> 
</tr> 
<tr> 
<td>Prohlížet zprávu</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Začínáme se službou Azure Queue v .NET</a></td> 
</tr> 
<tr> 
<td>Seznam ACL fronty/metadata/statistiky</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Začínáme se službou Azure Queue v .NET</a></td> 
</tr> 
<tr> 
<td>Vlastnosti služby Queue</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Začínáme se službou Azure Queue v .NET</a></td> 
</tr> 
<tr> 
<td>Aktualizovat zprávu</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Začínáme se službou Azure Queue v .NET</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Tabulka</b></td>
<td>Vytvořit tabulku</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Správa souběžnosti pomocí Azure Storage ukázkové aplikace</a></td> 
</tr> 
<tr> 
<td>Odstranit entitu/tabulku</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Začínáme se službou Azure Table Storage pomocí rozhraní .NET</a></td> 
</tr> 
<tr> 
<td>Vložit/sloučit/nahradit entitu</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Správa souběžnosti pomocí Azure Storage ukázkové aplikace</a></td> 
</tr> 
<tr> 
<td>Entity dotazu</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Začínáme se službou Azure Table Storage pomocí rozhraní .NET</a></td> 
</tr> 
<tr> 
<td>Tabulky dotazů</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Začínáme se službou Azure Table Storage pomocí rozhraní .NET</a></td> 
</tr> 
<tr> 
<td>Seznam ACL/vlastnosti tabulky</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs">Začínáme se službou Azure Table Storage pomocí rozhraní .NET</a></td> 
</tr> 
<tr> 
<td>Aktualizovat entitu</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Správa souběžnosti pomocí Azure Storage ukázkové aplikace</a></td> 
</tr> 
</tbody> 
</table>
<br/>

## <a name="azure-code-samples-library"></a>Knihovna ukázek kódu Azure

Kompletní ukázkovou knihovnu zobrazíte tak, že přejdete do knihovny [ukázek kódu Azure](https://azure.microsoft.com/resources/samples/?service=storage) , která obsahuje ukázky pro Azure Storage, které můžete stáhnout a spustit místně. Knihovna ukázek kódu poskytuje vzorový kód ve formátu ZIP. Případně můžete procházet a klonovat úložiště GitHub pro každou ukázku.

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>Úvodní příručky

Pokud hledáte pokyny k instalaci a Začínáme s klientskými knihovnami Azure Storage, přečtěte si následující příručky.

* [Začínáme se službou Azure Blob Service v .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Začínáme se službou Azure Queue v .NET](../storage-dotnet-how-to-use-queues.md)
* [Začínáme se službou Azure Table Service v .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Začínáme se službou Azure File Service v .NET](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Další postup

Informace o ukázkách pro jiné jazyky:

* Java: [Ukázky pro Azure Storage s použitím Javy](storage-samples-java.md)
* Všechny ostatní jazyky: [Ukázky Azure Storage](../storage-samples.md)
