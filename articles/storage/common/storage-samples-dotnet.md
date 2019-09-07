---
title: Ukázky Azure Storage pomocí .NET | Microsoft Docs
description: Zobrazit, stáhnout a spustit vzorový kód a aplikace pro Azure Storage. Seznamte se s úvodními ukázkami objektů blob, front, tabulek a souborů pomocí klientských knihoven pro úložiště .NET.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 57249bd2fa4d3c8aefe19a85ec9a2b6b584b00d2
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743958"
---
# <a name="azure-storage-samples-using-net"></a>Ukázky Azure Storage pomocí .NET

Následující tabulka poskytuje přehled našich ukázek úložiště a scénářů popsaných v jednotlivých ukázkách. Kliknutím na odkazy zobrazíte odpovídající vzorový kód v GitHubu.

## <a name="blob-samples"></a>Ukázky objektů BLOB

| **Scénář** | **Vzorový kód** |
|--------------|-----------------|
| Připojit objekt blob | [Začínáme s objekty blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs#L1144) |
| Objekt blob bloku | [Webová aplikace Fotogalerie pro Azure Blob Storage](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Šifrování na straně klienta | [Ukázky šifrování objektů BLOB](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs) |
| Zkopírování objektu Blob | [Začínáme s objekty blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Vytvoření kontejneru | [Webová aplikace Fotogalerie pro Azure Blob Storage](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Odstranit objekt Blob | [Webová aplikace Fotogalerie pro Azure Blob Storage](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Odstranění kontejneru | [Začínáme s objekty blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Metadata, vlastnosti/statistiky objektu BLOB | [Začínáme s objekty blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Seznam ACL/metadata/vlastnosti kontejneru | [Webová aplikace Fotogalerie pro Azure Blob Storage](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Získat rozsahy stránek | [Začínáme s objekty blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Objekt BLOB nebo kontejner zapůjčení | [Začínáme s objekty blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Výpis objektu BLOB nebo kontejneru | [Začínáme s objekty blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs) |
| Objekt blob stránky | [Začínáme s objekty blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs) |
| SAS | [Začínáme s objekty blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Vlastnosti služby | [Začínáme s objekty blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Objekt BLOB snímku | [Zálohování disků virtuálních počítačů Azure pomocí přírůstkových snímků](https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs) |

## <a name="file-samples"></a>Ukázky souborů

| **Scénář** | **Vzorový kód** |
|--------------|-----------------|
| Vytváření sdílených složek, adresářů a souborů | [Ukázka File Storage Azure Storage .NET](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| Odstranit sdílené složky/adresáře/soubory | [Začínáme se službou Azure File Service v .NET](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs) |
| Vlastnosti nebo metadata adresáře | [Ukázka File Storage Azure Storage .NET](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Stažení souborů | [Ukázka File Storage Azure Storage .NET](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| Vlastnosti souboru/metadata/metriky | [Ukázka File Storage Azure Storage .NET](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Vlastnosti souborové služby | [Ukázka File Storage Azure Storage .NET](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Výpis adresářů a souborů | [Ukázka File Storage Azure Storage .NET](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| Vypsat sdílené složky | [Ukázka File Storage Azure Storage .NET](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Vlastnosti sdílené složky/metadata/statistiky | [Ukázka File Storage Azure Storage .NET](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |

## <a name="queue-samples"></a>Ukázky front

| **Scénář** | **Vzorový kód** |
|--------------|-----------------|
| Přidat zprávu | [Začínáme se službou Azure Queue v .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Šifrování na straně klienta | [Azure Storage šifrování na straně klienta .NET Queue](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs) |
| Vytvořit fronty | [Začínáme se službou Azure Queue v .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Odstranit zprávu/frontu | [Začínáme se službou Azure Queue v .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Prohlížet zprávu | [Začínáme se službou Azure Queue v .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Seznam ACL fronty/metadata/statistiky | [Začínáme se službou Azure Queue v .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs) |
| Vlastnosti služby Queue | [Začínáme se službou Azure Queue v .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs) |
| Aktualizovat zprávu | [Začínáme se službou Azure Queue v .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |

## <a name="table-samples"></a>Ukázky tabulek

| **Scénář** | **Vzorový kód** |
|--------------|-----------------|
| Vytvořit tabulku | [Správa souběžnosti pomocí Azure Storage ukázkové aplikace](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |
| Odstranit entitu/tabulku | [Začínáme se službou Azure Table Storage pomocí rozhraní .NET](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| Vložit/sloučit/nahradit entitu | [Správa souběžnosti pomocí Azure Storage ukázkové aplikace](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |
| Entity dotazu | [Začínáme se službou Azure Table Storage pomocí rozhraní .NET](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| Tabulky dotazů | [Začínáme se službou Azure Table Storage pomocí rozhraní .NET](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| Seznam ACL/vlastnosti tabulky | [Začínáme se službou Azure Table Storage pomocí rozhraní .NET](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs) |
| Aktualizovat entitu | [Správa souběžnosti pomocí Azure Storage ukázkové aplikace](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |

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
* Python: [Ukázky Azure Storage pomocí Pythonu](storage-samples-python.md)
* Všechny ostatní jazyky: [Ukázky Azure Storage](../storage-samples.md)
