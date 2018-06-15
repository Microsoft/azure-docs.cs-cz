---
title: 'Rychlý start: Odeslání pracovního postupu pomocí vstupního souboru FASTQ | Dokumentace Microsoftu'
titleSuffix: Azure
description: Tento článek předpokládá, že máte nainstalovaného klienta msgen a úspěšně jste ve službě zpracovali ukázková data.
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: quickstart
ms.date: 12/07/2017
ms.openlocfilehash: f093397803f21c023a2c32e42709ecfcd0e3aec7
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2017
ms.locfileid: "26684479"
---
# <a name="submit-a-workflow-using-fastq-file-inputs"></a>Odeslání pracovní postupu pomocí vstupního souboru FASTQ

Tento rychlý start předvádí, jak odeslat pracovní postup službě Microsoft Genomics, pokud váš vstupní soubor obsahuje jeden pár souborů FASTQ. Toto téma předpokládá, že jste už nainstalovali a spustili klienta `msgen` a že víte, jak používat úložiště Azure Storage. Pokud jste úspěšně odeslali pracovní postup obsahující připravená ukázková data, jste připraveni pokračovat v tomto postupu. 

## <a name="set-up-upload-your-fastq-files-to-azure-storage"></a>Příprava: Odeslání souborů FASTQ do úložiště Azure
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

## <a name="next-steps"></a>Další kroky
V tomto článku jste nahráli dvojici souborů FASTQ do úložiště Azure Storage a odeslali jste pracovní postup do služby Microsoft Genomics pomocí pythonového klienta `msgen`. Další informace týkající se odesílání pracovních postupů a dalších příkazů, které můžete použít se službou Microsoft Genomics, najdete v [nejčastějších dotazech](frequently-asked-questions-genomics.md). 
