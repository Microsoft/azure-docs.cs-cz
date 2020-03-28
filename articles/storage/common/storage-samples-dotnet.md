---
title: Ukázky úložiště Azure pomocí rozhraní .NET | Dokumenty společnosti Microsoft
description: Zobrazení, stažení a spuštění ukázkového kódu a aplikací pro Azure Storage. Zjistěte, jak začít ukázky pro objekty BLOB, fronty, tabulky a soubory pomocí knihoven klienta úložiště .NET.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 02/13/2020
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: a4c9188c8f1315a3af452cd0c1fb5cf45ab82081
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77486113"
---
# <a name="azure-storage-samples-using-v12-net-client-libraries"></a>Ukázky úložiště Azure pomocí klientských knihoven v12 .NET

Následující tabulka obsahuje přehled úložiště našich ukázek a scénáře uvedené v každé ukázce. Kliknutím na odkazy zobrazíte odpovídající ukázkový kód na GitHubu.

> [!NOTE]
> Tyto ukázky používají nejnovější knihovnu Azure Storage .NET v12. Starší verze kódu v11 najdete v [tématu Ukázky úložiště objektů blob Azure pro rozhraní .NET](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started) v úložišti GitHub.

## <a name="blob-samples"></a>Vzorky objektů blob

### <a name="authentication"></a>Ověřování

:::row:::
   :::column span="":::
      [Ověření pomocí připojovacího řetězce](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L27)
   :::column-end:::
   :::column span="":::
      [Ověření pomocí pověření sdíleného klíče](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L91)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Ověření pomocí Azure Identity](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01a_HelloWorld.cs#L210)
   :::column-end:::
   :::column span="":::
      [Ověření pomocí tokenu služby Active Directory](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L177)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Anonymní přístup k veřejné objektu blob](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L55)
   :::column-end:::
:::row-end:::

### <a name="batching"></a>Dávkování

:::row:::
   :::column span="":::
      [Odstranění několika objektů BLOB v jednom požadavku](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample03b_BatchingAsync.cs#L22)
   :::column-end:::
   :::column span="":::
      [Nastavení několika úrovní přístupu k objektům blob v jednom požadavku](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample03b_BatchingAsync.cs#L56)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Jemně odstupňovaná kontrola v požadavku na dávku](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample03b_BatchingAsync.cs#L90)
   :::column-end:::
   :::column span="":::
      [Zachytit chyby z neúspěšné dílčí operace](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample03b_BatchingAsync.cs#L136)
   :::column-end:::
:::row-end:::

### <a name="blob"></a>Objekt blob

:::row:::
   :::column span="":::
      [Nahrání souboru do objektu blob](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L21)
   :::column-end:::
   :::column span="":::
      [Stažení objektu blob do souboru](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L66)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Stažení obrázku](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L109)
   :::column-end:::
   :::column span="":::
      [Vypsat všechny objekty BLOB v kontejneru](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L128)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Řešení potíží
:::row:::
   :::column span="2":::
      [Aktivace obnovitelné chyby pomocí klienta kontejneru](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L166)
   :::column-end:::
:::row-end:::

## <a name="data-lake-storage-gen2-samples"></a>Vzorky Data Lake Storage Gen2

### <a name="authentication"></a>Ověřování

:::row:::
   :::column span="":::
      [Anonymní přístup k veřejnému souboru](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L28)
   :::column-end:::
   :::column span="":::
      [Ověření pomocí pověření sdíleného klíče](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L79)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Ověření pomocí sdíleného přístupového podpisu (SAS)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L114)
   :::column-end:::
   :::column span="":::
      [Ověření pomocí tokenu služby Active Directory](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L164)
   :::column-end:::
:::row-end:::

### <a name="file-system"></a>Systém souborů
:::row:::
   :::column span="":::
      [Vytvoření souboru pomocí klienta systému souborů](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L22)
   :::column-end:::
   :::column span="":::
      [Získání vlastností souboru a adresáře](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L560)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Přejmenování souboru a adresáře](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L511)
   :::column-end:::
:::row-end:::

### <a name="directory"></a>Adresář

:::row:::
   :::column span="":::
      [Vytvoření adresáře](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L93)
   :::column-end:::
   :::column span="":::
      [Vytvoření souboru pomocí klienta adresáře](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L55)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Seznam adresářů](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L275)
   :::column-end:::
   :::column span="":::
      [Soubory a adresáře procházení](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L318)
   :::column-end:::
:::row-end:::

### <a name="file"></a>File
:::row:::
   :::column span="":::
      [Nahrání souboru](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L126)
   :::column-end:::
   :::column span="":::
      [Nahrát připojením k souboru](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L169)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Stažení souboru](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L224)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Nastavení a získání seznamu řízení přístupu k souborům](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L468)
   :::column-end:::
   :::column span="":::
      [Nastavení a získání oprávnění k souboru](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L426)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Řešení potíží

:::row:::
   :::column span="2":::
      [Spuštění obnovitelné chyby](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L389)
   :::column-end:::
:::row-end:::

## <a name="azure-files-samples"></a>Ukázky souborů Azure

### <a name="authentication"></a>Ověřování

:::row:::
   :::column span="":::
      [Ověření pomocí připojovacího řetězce](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L24)
   :::column-end:::
   :::column span="":::
      [Ověření pomocí pověření sdíleného klíče](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L52)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Ověření pomocí sdíleného přístupového podpisu (SAS))](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L86)
   :::column-end:::
:::row-end:::

### <a name="file-shares"></a>Sdílené složky

:::row:::
   :::column span="":::
      [Vytvoření sdílené složky a nahrání souboru](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L21)
   :::column-end:::
   :::column span="":::
      [Stažení souboru](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L68)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Soubory a adresáře procházení](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L107)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Řešení potíží

:::row:::
   :::column span="2":::
      [Aktivace obnovitelné chyby pomocí sdíleného klienta](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L141)
   :::column-end:::
:::row-end:::

## <a name="queue-samples"></a>Ukázky fronty

### <a name="authentication"></a>Ověřování

:::row:::
   :::column span="":::
      [Ověření pomocí služby Azure Active Directory](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L167)
   :::column-end:::
   :::column span="":::
      [Ověření pomocí připojovacího řetězce](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L24)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Ověření pomocí pověření sdíleného klíče](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L52)
   :::column-end:::
   :::column span="":::
      [Ověření pomocí sdíleného přístupového podpisu (SAS))](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L86)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Ověření pomocí tokenu služby Active Directory](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L140)
   :::column-end:::
:::row-end:::

### <a name="queue"></a>Fronta

:::row:::
   :::column span="2":::
      [Vytvoření fronty a přidání zprávy](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L24)
   :::column-end:::
:::row-end:::

### <a name="message"></a>Zpráva

:::row:::
   :::column span="":::
      [Příjem a zpracování zpráv](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L61)
   :::column-end:::
   :::column span="":::
      [Náhled na zprávy](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L90)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Příjem zpráv a aktualizace časového limitu viditelnosti](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L115)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Řešení potíží 
:::row:::
   :::column span="2":::
      [Aktivace obnovitelné chyby pomocí klienta fronty](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L188)
   :::column-end:::
:::row-end:::

## <a name="table-samples-v11"></a>Ukázky tabulky (v11)

:::row:::
   :::column span="":::
      [Vytvoření tabulky](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/Common.cs#L40)
   :::column-end:::
   :::column span="":::
      [Odstranit entitu/tabulku](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Entita Vložení/sloučení/nahrazení](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/SamplesUtils.cs#L41)
   :::column-end:::
   :::column span="":::
      [Entity dotazu](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs#L672)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Dotazy na tabulky](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs)
   :::column-end:::
   :::column span="":::
      [Seznam ACL/vlastnosti tabulky](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs#L224)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Aktualizovat entitu](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs#L51)
   :::column-end:::
:::row-end:::

## <a name="azure-code-sample-libraries"></a>Ukázkové knihovny kódu Azure

Chcete-li zobrazit kompletní ukázkové knihovny .NET, přejděte na:

* [Ukázky kódu objektu blob Azure](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)
* [Ukázky kódu datového jezera Azure](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples)
* [Ukázky kódu souborů Azure](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples)
* [Ukázky kódu fronty Azure](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples)

Můžete procházet a klonovat úložiště GitHub pro každou knihovnu.

## <a name="getting-started-guides"></a>Příručky Začínáme

Pokud hledáte pokyny k instalaci a zahájení instalace s klientskými knihovnami úložiště Azure, podívejte se na následující příručky.

* [Začínáme se službou Azure Blob Service v rozhraní .NET](../blobs/storage-quickstart-blobs-dotnet.md)
* [Začínáme se službou Azure Queue Service v rozhraní .NET](../queues/storage-quickstart-queues-dotnet.md)
* [Začínáme se službou Azure Table Service v rozhraní .NET](../../cosmos-db/tutorial-develop-table-dotnet.md)
* [Začínáme se službou Azure File Service v rozhraní .NET](../files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Další kroky

Informace o vzorcích pro jiné jazyky:

* Java: [Ukázky azure úložiště pomocí Javy](storage-samples-java.md)
* Python: [Ukázky úložiště Azure pomocí Pythonu](storage-samples-python.md)
* JavaScript/Node.js: [Ukázky úložiště Azure pomocí JavaScriptu](storage-samples-javascript.md)
* Všechny ostatní jazyky: [Ukázky azure storage](storage-samples.md)
