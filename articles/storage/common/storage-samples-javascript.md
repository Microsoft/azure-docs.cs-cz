---
title: Azure Storage ukázky pomocí JavaScriptu | Microsoft Docs
description: Zobrazit, stáhnout a spustit vzorový kód a aplikace pro Azure Storage. Seznamte se s úvodními ukázkami objektů blob, front, tabulek a souborů pomocí klientských knihoven pro úložiště pro JavaScript nebo Node.js.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 02/19/2020
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.custom: devx-track-js
ms.openlocfilehash: 208e06e9bd8fcf26674a11f79d2bd6878ca4f633
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331552"
---
# <a name="azure-storage-samples-using-v12-javascript-client-libraries"></a>Azure Storage ukázky pomocí klientských knihoven V12 JavaScript

Následující tabulky obsahují přehled našich ukázkových úložišť a scénářů popsaných v jednotlivých ukázkách. Kliknutím na odkazy zobrazíte odpovídající vzorový kód v GitHubu.

> [!NOTE]
> Tyto ukázky používají nejnovější knihovnu V12 JavaScriptu pro Azure Storage. Starší verzi kódu V11 naleznete v tématu [Začínáme with Azure Blob Service v Node.js](https://github.com/Azure-Samples/storage-blob-node-getting-started) v úložišti GitHub.

## <a name="blob-samples"></a>Ukázky objektů BLOB

### <a name="authentication"></a>Authentication

:::row:::
   :::column span="":::
      [Ověřit pomocí připojovacího řetězce](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/withConnString.js#L14)
   :::column-end:::
   :::column span="":::
      [Ověření pomocí připojovacího řetězce SAS](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/withConnString.js#L14)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Ověřit pomocí pověření sdíleného klíče](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/sharedKeyCred.js#L5)
   :::column-end:::
   :::column span="":::
      [Ověřování pomocí AnonymousCredential](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/anonymousCred.js#L18)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Ověření pomocí služby Azure Active Directory](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/azureAdAuth.js#L47)
   :::column-end:::
   :::column span="":::
      [Ověřování pomocí proxy](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/proxyAuth.js#L28)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Připojení pomocí vlastního kanálu](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/customPipeline.js#L26)
   :::column-end:::
:::row-end:::

### <a name="blob-service"></a>Blob service

:::row:::
   :::column span="2":::
      [Vytvoření klienta služby BLOB pomocí adresy URL SAS](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/advanced.js#L39)
   :::column-end:::
:::row-end:::

### <a name="container"></a>Kontejner

:::row:::
   :::column span="":::
      [Vytvoření kontejneru](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/basic.js#L53)
   :::column-end:::
   :::column span="":::
      [Vytvoření kontejneru pomocí pověření sdíleného klíče](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/readingSnapshot.js#L38)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Výpis kontejnerů](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/basic.js#L48)
   :::column-end:::
   :::column span="":::
      [Výpis kontejnerů pomocí iterátoru](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/iterators-containers.js#L28)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Vypsat kontejnery podle stránky](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/iterators-containers.js#L53)
   :::column-end:::
   :::column span="":::
      [Odstranění kontejneru](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/basic.js#L82)
   :::column-end:::
:::row-end:::

### <a name="blob"></a>Blob

:::row:::
   :::column span="":::
      [Vytvoření objektu blob](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/basic.js#L60)
   :::column-end:::
   :::column span="":::
      [Výpis objektů blob](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/basic.js#L67)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Stažení objektu blob](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/basic.js#L73)
   :::column-end:::
   :::column span="":::
      [Výpis objektů BLOB pomocí iterátoru](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/iterators-blobs.js#L41)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Výpis objektů BLOB podle stránky](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/iterators-blobs.js#L66)
   :::column-end:::
   :::column span="":::
      [Výpis objektů BLOB podle hierarchie](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/iterators-blobs-hierarchy.js#L70)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Výpis objektů BLOB bez použití operátoru await](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/iterators-without-await.js#L42)
   :::column-end:::
   :::column span="":::
      [Vytvoření snímku objektu blob](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/readingSnapshot.js#L56)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Stažení snímku objektu BLOB](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/readingSnapshot.js#L61)
   :::column-end:::
   :::column span="":::
      [Paralelní nahrání datového proudu do objektu BLOB](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/advanced.js#L74)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Paralelní stažení bloku objektů BLOB](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/advanced.js#L99)
   :::column-end:::
   :::column span="":::
      [Nastavení úrovně přístupu u objektu BLOB](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/advanced.js#L118)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Řešení potíží

:::row:::
   :::column span="2":::
      [Aktivace obnovitelné chyby pomocí klienta kontejneru](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/samples/javascript/errorsAndResponses.js#L33)
   :::column-end:::
:::row-end:::


## <a name="data-lake-storage-gen2-samples"></a>Ukázky Data Lake Storage Gen2

:::row:::
   :::column span="":::
      [Vytvoření klienta služby Data Lake](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-datalake/samples/javascript/basic.js#L36)
   :::column-end:::
   :::column span="":::
      [Vytvoření systému souborů](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-datalake/samples/javascript/basic.js#L47)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Seznam systémů souborů](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-datalake/samples/javascript/basic.js#L42)
   :::column-end:::
   :::column span="":::
      [Vytvořit soubor](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-datalake/samples/javascript/basic.js#L54)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Seznam cest v systému souborů](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-datalake/samples/javascript/basic.js#L63)
   :::column-end:::
   :::column span="":::
      [Stažení souboru](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-datalake/samples/javascript/basic.js#L69)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Odstranění systému souborů](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-datalake/samples/javascript/basic.js#L78)
   :::column-end:::
:::row-end:::

## <a name="azure-files-samples"></a>Ukázky souborů Azure

### <a name="authentication"></a>Authentication

:::row:::
   :::column span="":::
      [Ověřování pomocí připojovacího řetězce](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/withConnString.js)
   :::column-end:::
   :::column span="":::
      [Ověřování pomocí přihlašovacích údajů sdíleného klíče](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/sharedKeyCred.js)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Ověřování pomocí AnonymousCredential](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/anonymousCred.js)
   :::column-end:::
   :::column span="":::
      [Připojení pomocí vlastního kanálu](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/customPipeline.js)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Připojit pomocí proxy](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/proxyAuth.js)
   :::column-end:::
:::row-end:::

### <a name="share"></a>Sdílet

:::row:::
   :::column span="":::
      [Vytvoření sdílené složky](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/advanced.js#L48)
   :::column-end:::
   :::column span="":::
      [Vypsat sdílené složky](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/iterators-shares.js#L27)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Zobrazit seznam sdílených složek podle stránky](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/iterators-shares.js#L51)
   :::column-end:::
   :::column span="":::
      [Odstranění sdílené složky](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/advanced.js#L104)
   :::column-end:::
:::row-end:::

### <a name="directory"></a>Adresář

:::row:::
   :::column span="":::
      [Vytvoření adresáře](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/advanced.js#L54)
   :::column-end:::
   :::column span="":::
      [Výpis souborů a adresářů](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/iterators-files-and-directories.js#L56)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Výpis souborů a adresářů podle stránky](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/iterators-files-and-directories.js#L90)
   :::column-end:::
:::row-end:::

### <a name="file"></a>Soubor

:::row:::
   :::column span="":::
      [Paralelní nahrání souboru](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/advanced.js#L65)
   :::column-end:::
   :::column span="":::
      [Paralelní nahrání čitelného datového proudu](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/advanced.js#L74)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Paralelní stažení souboru](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/advanced.js#L93)
   :::column-end:::
   :::column span="":::
      [Seznam popisovačů souborů](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/iterators-handles.js#L43)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Seznam popisovačů souborů podle stránky](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-file-share/samples/javascript/iterators-handles.js#L79)
   :::column-end:::
:::row-end:::

## <a name="queue-samples"></a>Ukázky front

### <a name="authentication"></a>Authentication

:::row:::
   :::column span="":::
      [Ověřování pomocí připojovacího řetězce](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/withConnString.js)
   :::column-end:::
   :::column span="":::
      [Ověřování pomocí přihlašovacích údajů sdíleného klíče](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/sharedKeyCred.js)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Ověřování pomocí AnonymousCredential](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/anonymousCred.js)
   :::column-end:::
   :::column span="":::
      [Připojení pomocí vlastního kanálu](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/customPipeline.js)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Připojit pomocí proxy](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/proxyAuth.js)
   :::column-end:::
   :::column span="":::
      [Ověření pomocí služby Azure Active Directory](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/azureAdAuth.js)
   :::column-end:::
:::row-end:::

### <a name="queue-service"></a>Služba front

:::row:::
   :::column span="2":::
      [Vytvoření klienta služby front](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/basic.js#L42)
   :::column-end:::
:::row-end:::

### <a name="queue"></a>Fronta

:::row:::
   :::column span="":::
      [Vytvořit novou frontu](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/basic.js#L54)
   :::column-end:::
   :::column span="":::
      [Výpis front](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/iterators.js#L27)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Vypsat fronty podle stránky](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/iterators.js#L51)
   :::column-end:::
   :::column span="":::
      [Odstranění fronty](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/basic.js#L89)
   :::column-end:::
:::row-end:::

### <a name="message"></a>Zpráva

:::row:::
   :::column span="":::
      [Odeslat zprávu do fronty](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/basic.js#L62)
   :::column-end:::
   :::column span="":::
      [Prohlížet zprávy](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/basic.js#L68)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Příjem zpráv](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/basic.js#L76)
   :::column-end:::
   :::column span="":::
      [Odstranit zprávy](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-queue/samples/javascript/basic.js#L80)
   :::column-end:::
:::row-end:::

## <a name="table-samples-v11"></a>Ukázky tabulek (V11)

:::row:::
   :::column span="":::
      [Entity Batch](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L87)
   :::column-end:::
   :::column span="":::
      [Vytvořit tabulku](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L41)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Odstranit entitu/tabulku](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L67)
   :::column-end:::
   :::column span="":::
      [Vložit/sloučit/nahradit entitu](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L49)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Seznam tabulek](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L63)
   :::column-end:::
   :::column span="":::
      [Entity dotazu](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L59)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Dotazy na tabulky](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L140)
   :::column-end:::
   :::column span="":::
      [Dotaz na rozsah](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L102)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Sdílený přístupový podpis (SAS)](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L87)
   :::column-end:::
   :::column span="":::
      [Seznam ACL tabulky](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L255)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Pravidla sdílení prostředků mezi zdroji (CORS) v tabulce](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L149)
   :::column-end:::
   :::column span="":::
      [Vlastnosti tabulky](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L188)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Statistiky tabulek](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L243)
   :::column-end:::
   :::column span="":::
      [Aktualizovat entitu](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L49)
   :::column-end:::
:::row-end:::

## <a name="azure-code-sample-libraries"></a>Knihovny ukázek kódu Azure

Chcete-li zobrazit kompletní ukázkové knihovny jazyka JavaScript, přejít na:

* [Ukázky kódu Azure Blob](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples/javascript)
* [Ukázky kódu Azure Data Lake](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples/javascript)
* [Ukázky kódu souborů Azure](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-share/samples/javascript)
* [Ukázky kódu fronty Azure](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples/javascript)

Úložiště GitHubu můžete procházet a klonovat pro každou knihovnu.

## <a name="getting-started-guides"></a>Příručky Začínáme

Pokud hledáte pokyny k instalaci a Začínáme s klientskými knihovnami Azure Storage, přečtěte si následující příručky.

* [Začínáme se službou Azure Blob Service v JavaScriptu](../blobs/storage-quickstart-blobs-nodejs.md)
* [Začínáme ve službě Azure Queue Service v JavaScriptu](../queues/storage-quickstart-queues-nodejs.md)
* [Začínáme se službou Azure Table Service v JavaScriptu](../../cosmos-db/table-storage-how-to-use-nodejs.md)

## <a name="next-steps"></a>Další kroky

Informace o ukázkách pro jiné jazyky:

* .NET: [Azure Storage ukázky pomocí .NET](storage-samples-dotnet.md)
* Java: [Azure Storage ukázky pomocí jazyka Java](storage-samples-java.md)
* Python: [Azure Storage ukázky pomocí Pythonu](storage-samples-python.md)
* Všechny ostatní jazyky: [ukázky Azure Storage](storage-samples.md)
