---
title: Odeslání pracovní postupu pomocí vstupního souboru fastq – Microsoft Genomics
titleSuffix: Azure
description: Tento článek předpokládá mají nainstalovaného klienta msgen a úspěšně zpracovali ukázková data ve službě.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 2662a8f52c58a39916e5789fa9ed7fadd91216c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60333668"
---
# <a name="submit-a-workflow-using-fastq-file-inputs-in-microsoft-genomics"></a>Odeslání pracovního postupu ve vstupních souborech FASTQ v Microsoft Genomics

Tento článek ukazuje, jak odeslat pracovní postup službě Microsoft Genomics, pokud vstupní soubory obsahuje jeden pár souborů FASTQ. Toto téma předpokládá, že jste už nainstalovali a spustili klienta `msgen` a že víte, jak používat úložiště Azure Storage. Pokud jste úspěšně odeslali pracovní postup pomocí připravená ukázková data, budete chtít pokračovat v tomto článku. 

## <a name="set-up-upload-your-fastq-files-to-azure-storage"></a>Nastavení: Odeslání souborů FASTQ do úložiště Azure
Předpokládejme, že máte dva soubory *reads_1.fq.gz* a *reads_2.fq.gz*, a že jste je nahráli do svého účtu úložiště *myaccount* v Azure jako **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/inputs/reads_1<span></span>.fq<span></span>.gz<span></span>** a **https://<span></span>myaccount.blob.core.<span></span>windows<span></span>.net/<span></span>inputs/<span></span>reads_2.fq<span></span>.gz<span></span>**. Máte adresu URL rozhraní API a přístupový klíč. Chcete mít výstupy v **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.


## <a name="submit-your-job-to-the-msgen-client"></a>Odeslání úlohy do klienta `msgen` 

Tady je minimální sadu argumentů, které budete muset poskytnout klientovi `msgen`; zalomení řádků jsou přidána pro přehlednost:

Ve Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads_1.fq.gz ^
  --input-blob-name-2 reads_2.fq.gz ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```

V Unixu:

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads_1.fq.gz \
  --input-blob-name-2 reads_2.fq.gz \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Pokud dáváte přednost použití konfiguračního souboru, měl by obsahovat:

```
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads_1.fq.gz
input_blob_name_2:                reads_2.fq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Odešlete soubor `config.txt` tímto voláním: `msgen submit -f config.txt`

## <a name="next-steps"></a>Další postup
V tomto článku jste nahráli dvojici souborů FASTQ do úložiště Azure Storage a odeslali jste pracovní postup do služby Microsoft Genomics pomocí pythonového klienta `msgen`. Další informace týkající se odesílání pracovních postupů a dalších příkazů, můžete použít se službou Microsoft Genomics, najdete v našich [nejčastější dotazy k](frequently-asked-questions-genomics.md). 
