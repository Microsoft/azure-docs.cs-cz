---
title: Ukázky pro Azure Storage pomocí .NET | Dokumentace Microsoftu
description: Zobrazit, stáhnout a spustit ukázkový kód a aplikace pro službu Azure Storage. Objevte úvodními ukázkami pro objekty BLOB, fronty, tabulky a soubory, pomocí klientské knihovny úložiště .NET.
services: storage
author: seguler
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 01/12/2017
ms.author: seguler
ms.component: common
ms.openlocfilehash: 663df77a7fb574f05cfaa9378dff53ca5db21c49
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526105"
---
# <a name="azure-storage-samples-using-net"></a>Ukázky pro Azure Storage pomocí .NET

## <a name="net-sample-index"></a>.NET ukázkového indexu

Následující tabulka obsahuje přehled o našem úložišti ukázek a scénáře popsané v každém vzorku. Kliknutím na odkazy k zobrazení odpovídající vzorový kód na Githubu.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Koncový bod</th><th style="font-size:110%">Scénář</th><th style="font-size:110%">Vzorový kód</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>Objekt blob</b></td>
<td>Doplňovací objekt Blob</td> 
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
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Začínáme s využitím objektů BLOB</a></td>
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
<td>Odstranění kontejneru</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Začínáme s využitím objektů BLOB</a></td>
</tr> 
<tr> 
<td>Objekt BLOB metadat/vlastnosti/Stats</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Začínáme s využitím objektů BLOB</a></td>
</tr> 
<tr> 
<td>Seznam ACL/Metadata/vlastnosti kontejneru</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage Fotogalerie webové aplikace</a></td>
</tr> 
<tr> 
<td>Získání rozsahů stránek</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Začínáme s využitím objektů BLOB</a></td>
</tr> 
<tr> 
<td>Zapůjčení objektu Blob nebo kontejneru</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Začínáme s využitím objektů BLOB</a></td>
</tr> 
<tr> 
<td>Výpis objektu Blob nebo kontejneru</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Začínáme s využitím objektů BLOB</a></td>
</tr> 
<tr> 
<td>Objekt Blob stránky</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Začínáme s využitím objektů BLOB</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Začínáme s využitím objektů BLOB</a></td>
</tr>   
<tr> 
<td>Vlastnosti služby</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Začínáme s využitím objektů BLOB</a></td>
</tr>           
<tr> 
<td>Vytvoření snímku objektu Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs">Zálohování virtuálních počítačů Azure disků s přírůstkovými snímky</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>File</b></td>
<td>Vytvoření sdílené složky/adresářů a souborů</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Ukázkové úložiště souboru .NET služby Azure Storage</a></td> 
</tr>
<tr> 
<td>Odstranění sdílené složky/adresářů a souborů</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs">Začínáme se službou Azure File v .NET</a></td> 
</tr> 
<tr> 
<td>Adresář vlastností/metadat</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Ukázkové úložiště souboru .NET služby Azure Storage</a></td> 
</tr> 
<tr> 
<td>Stažení souborů</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Ukázkové úložiště souboru .NET služby Azure Storage</a></td> 
</tr> 
<tr> 
<td>Soubor vlastností/metadat/metriky</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Ukázkové úložiště souboru .NET služby Azure Storage</a></td> 
</tr> 
<tr> 
<td>File Service Properties</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Ukázkové úložiště souboru .NET služby Azure Storage</a></td> 
</tr> 
<tr> 
<td>Seznam adresářů a souborů</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Ukázkové úložiště souboru .NET služby Azure Storage</a></td> 
</tr>
<tr> 
<td>Zobrazit seznam sdílených složek</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Ukázkové úložiště souboru .NET služby Azure Storage</a></td> 
</tr>
<tr> 
<td>Sdílení vlastností/metadat/Stats</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Ukázkové úložiště souboru .NET služby Azure Storage</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>fronty</b></td>
<td>Přidat zprávu</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Začínáme se službou Azure Queue v .NET</a></td> 
</tr> 
<tr> 
<td>Šifrování na straně klienta</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs">Šifrování na straně klienta fronty Azure Storage .NET</a></td> 
</tr> 
<tr> 
<td>Vytvoření fronty</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Začínáme se službou Azure Queue v .NET</a></td> 
</tr> 
<tr> 
<td>Odstranit zprávu či fronty</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Začínáme se službou Azure Queue v .NET</a></td> 
</tr> 
<tr> 
<td>Náhled zprávy</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Začínáme se službou Azure Queue v .NET</a></td> 
</tr> 
<tr> 
<td>Fronty ACL/Metadata/Stats</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Začínáme se službou Azure Queue v .NET</a></td> 
</tr> 
<tr> 
<td>Vlastnosti služby Queue</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Začínáme se službou Azure Queue v .NET</a></td> 
</tr> 
<tr> 
<td>Aktualizace zprávy</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Začínáme se službou Azure Queue v .NET</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Tabulka</b></td>
<td>Vytvořit tabulku</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Správa souběžnosti pomocí služby Azure Storage – ukázková aplikace</a></td> 
</tr> 
<tr> 
<td>Odstranit entitu nebo tabulku</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Začínáme se službou Azure Table Storage pomocí rozhraní .NET</a></td> 
</tr> 
<tr> 
<td>Vložit/sloučení/nahrazení Entity</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Správa souběžnosti pomocí služby Azure Storage – ukázková aplikace</a></td> 
</tr> 
<tr> 
<td>Dotazování entit</td> 
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
<td>Aktualizace Entity</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Správa souběžnosti pomocí služby Azure Storage – ukázková aplikace</a></td> 
</tr> 
</tbody> 
</table>
<br/>

## <a name="azure-code-samples-library"></a>Knihovna ukázky kódu Azure

Chcete-li zobrazit úplnou ukázku knihovny, přejděte na [vzorových kódů Azure](https://azure.microsoft.com/resources/samples/?service=storage) knihovny, která obsahuje ukázky pro Azure Storage, který lze stáhnout a spustit místně. Ukázka knihovny kódu poskytuje ukázkový kód ve formátu ZIP. Alternativně můžete procházet a naklonujte úložiště GitHub pro každý vzorek.

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>Příručky Začínáme

Pokud hledáte pokyny o tom, jak nainstalovat a začít s klientských knihoven pro úložiště Azure, prohlédněte si tyto návody.

* [Začínáme se službou Azure Blob Service v .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Začínáme se službou Azure Queue v .NET](../storage-dotnet-how-to-use-queues.md)
* [Začínáme s Azure Table Service v prostředí .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Začínáme se službou Azure File v .NET](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Další postup

Informace o ukázky pro ostatní jazyky:

* Java: [ukázky služby Azure Storage pomocí Javy](storage-samples-java.md)
* Všechny ostatní jazyky: [ukázek pro Azure Storage](../storage-samples.md)
