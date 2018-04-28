---
title: Ukázek Azure Storage pomocí rozhraní .NET | Microsoft Docs
description: Zobrazit, stáhnout a spustit ukázkový kód a aplikace pro Azure Storage. Umožňuje zjistit Začínáme ukázky pro objekty BLOB, fronty, tabulky a soubory, pomocí knihovny klienta úložiště .NET.
services: storage
documentationcenter: na
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 01/12/2017
ms.author: seguler
ms.openlocfilehash: 1e6973f0decc448657d869afb8823dd03c62d272
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="azure-storage-samples-using-net"></a>Ukázek Azure Storage pomocí rozhraní .NET

## <a name="net-sample-index"></a>Ukázka index rozhraní .NET

Následující tabulka obsahuje přehled o našem úložišti ukázky a pokryté v každé ukázkové scénáře. Kliknutím na odkazy k zobrazení odpovídající ukázkový kód v Githubu.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Koncový bod</th><th style="font-size:110%">Scénář</th><th style="font-size:110%">Vzorový kód</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>Objekt BLOB</b></td>
<td>Append – objekt Blob</td> 
<td><a href="https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.getappendblobreference.aspx">Příklad CloudBlobContainer.GetAppendBlobReference – metoda</a></td> 
</tr> 
<tr> 
<td>Objekt Blob bloku</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage Fotogalerie webové aplikace</a></td>
</tr> 
<tr> 
<td>Šifrování na straně klienta</td>
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs">Ukázky šifrování objektů BLOB</a></td>
</tr> 
<tr> 
<td>Zkopírování objektu Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Začínáme se službou objektů BLOB</a></td>
</tr> 
<tr> 
<td>Vytvoření kontejneru</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage Fotogalerie webové aplikace</a></td>
</tr> 
<tr> 
<td>Odstranit objekt Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage Fotogalerie webové aplikace</a></td>
</tr> 
<tr> 
<td>Odstranit kontejner</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Začínáme se službou objektů BLOB</a></td>
</tr> 
<tr> 
<td>Objekt BLOB Metadata nebo vlastnosti nebo statistiky</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Začínáme se službou objektů BLOB</a></td>
</tr> 
<tr> 
<td>Seznam ACL nebo Metadata nebo vlastnosti kontejneru</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage Fotogalerie webové aplikace</a></td>
</tr> 
<tr> 
<td>Get rozsahů stránek</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Začínáme se službou objektů BLOB</a></td>
</tr> 
<tr> 
<td>Zapůjčení nebo kontejner objektů Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Začínáme se službou objektů BLOB</a></td>
</tr> 
<tr> 
<td>Seznam objektů Blob nebo kontejneru</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Začínáme se službou objektů BLOB</a></td>
</tr> 
<tr> 
<td>Objekt Blob stránky</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Začínáme se službou objektů BLOB</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Začínáme se službou objektů BLOB</a></td>
</tr>   
<tr> 
<td>Vlastnosti služby</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Začínáme se službou objektů BLOB</a></td>
</tr>           
<tr> 
<td>Objekt Blob snímku</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs">Disky zálohování virtuálních počítačů Azure s přírůstkové snímky</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>File</b></td>
<td>Vytvoření sdílené složky, adresáře nebo soubory</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Ukázka úložiště souboru .NET úložiště Azure</a></td> 
</tr>
<tr> 
<td>Odstranění sdílené složky, adresáře nebo soubory</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs">Začínáme se službou Azure souborů v rozhraní .NET</a></td> 
</tr> 
<tr> 
<td>Directory vlastnosti nebo Metadata</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Ukázka úložiště souboru .NET úložiště Azure</a></td> 
</tr> 
<tr> 
<td>Stažení souborů</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Ukázka úložiště souboru .NET úložiště Azure</a></td> 
</tr> 
<tr> 
<td>Soubor vlastnosti nebo Metadata nebo metriky</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Ukázka úložiště souboru .NET úložiště Azure</a></td> 
</tr> 
<tr> 
<td>Vlastnosti souboru služby</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Ukázka úložiště souboru .NET úložiště Azure</a></td> 
</tr> 
<tr> 
<td>Seznam adresářů a souborů</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Ukázka úložiště souboru .NET úložiště Azure</a></td> 
</tr>
<tr> 
<td>Zobrazit seznam sdílených složek</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Ukázka úložiště souboru .NET úložiště Azure</a></td> 
</tr>
<tr> 
<td>Sdílené složky vlastnosti nebo Metadata nebo statistiky</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Ukázka úložiště souboru .NET úložiště Azure</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>Fronty</b></td>
<td>Přidat zprávu</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Začínáme se službou Azure fronty v rozhraní .NET</a></td> 
</tr> 
<tr> 
<td>Šifrování na straně klienta</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs">Šifrování na straně klienta úložiště Azure .NET fronty</a></td> 
</tr> 
<tr> 
<td>Vytvoření fronty</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Začínáme se službou Azure fronty v rozhraní .NET</a></td> 
</tr> 
<tr> 
<td>Odstranit nebo fronta zpráv</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Začínáme se službou Azure fronty v rozhraní .NET</a></td> 
</tr> 
<tr> 
<td>Prohlížení zpráv</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Začínáme se službou Azure fronty v rozhraní .NET</a></td> 
</tr> 
<tr> 
<td>Fronty seznamu ACL nebo Metadata nebo statistiky</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Začínáme se službou Azure fronty v rozhraní .NET</a></td> 
</tr> 
<tr> 
<td>Vlastnosti fronty služby</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Začínáme se službou Azure fronty v rozhraní .NET</a></td> 
</tr> 
<tr> 
<td>Zpráva aktualizace</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Začínáme se službou Azure fronty v rozhraní .NET</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Tabulka</b></td>
<td>Vytvořit tabulku</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Správa souběžnosti použití služby Azure Storage – ukázková aplikace</a></td> 
</tr> 
<tr> 
<td>Odstranit Entity nebo tabulku</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Začínáme se službou Azure Table Storage pomocí rozhraní .NET</a></td> 
</tr> 
<tr> 
<td>Vložení a sloučení nebo nahrazení Entity</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Správa souběžnosti použití služby Azure Storage – ukázková aplikace</a></td> 
</tr> 
<tr> 
<td>Dotaz entity</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Začínáme se službou Azure Table Storage pomocí rozhraní .NET</a></td> 
</tr> 
<tr> 
<td>Dotazu na tabulky</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Začínáme se službou Azure Table Storage pomocí rozhraní .NET</a></td> 
</tr> 
<tr> 
<td>Tabulka seznamu ACL nebo vlastnosti</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs">Začínáme se službou Azure Table Storage pomocí rozhraní .NET</a></td> 
</tr> 
<tr> 
<td>Aktualizace Entity</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Správa souběžnosti použití služby Azure Storage – ukázková aplikace</a></td> 
</tr> 
</tbody> 
</table>
<br/>

## <a name="azure-code-samples-library"></a>Knihovna Azure ukázky kódu

Chcete-li zobrazit knihovně ucelenou ukázku, přejděte na [ukázky kódu Azure](https://azure.microsoft.com/resources/samples/?service=storage) knihovny, která obsahuje ukázky pro úložiště Azure, které můžete stáhnout a spustit místně. Knihovna ukázka kódu obsahuje ukázkový kód ve formátu ZIP. Alternativně můžete procházet a naklonujte úložiště GitHub pro jednotlivé vzorky.

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>Získávání příručky Začínáme

Pokud hledáte pokyny, jak nainstalovat a začít pracovat s knihovny klienta Azure Storage, projděte si následující příručky.

* [Začínáme se službou objektů Blob v Azure v rozhraní .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Začínáme se službou Azure fronty v rozhraní .NET](../storage-dotnet-how-to-use-queues.md)
* [Začínáme se službou Azure tabulky v rozhraní .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Začínáme se službou Azure souborů v rozhraní .NET](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Další postup

Informace o ukázky pro jiné jazyky:

* Java: [ukázky úložiště Azure se používá Java](storage-samples-java.md)
* Všechny ostatní jazyky: [ukázky Azure Storage](../storage-samples.md)
