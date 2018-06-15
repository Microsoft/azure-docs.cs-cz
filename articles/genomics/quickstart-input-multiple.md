---
title: 'Rychlý start: Odeslání pracovního postupu pomocí více vstupů | Dokumentace Microsoftu'
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
ms.date: 02/05/2018
ms.openlocfilehash: 7aeb4d5ad939cefcf8300b78b4afcc9d91ca0624
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
ms.locfileid: "29119991"
---
# <a name="submit-a-workflow-using-multiple-inputs-from-the-same-sample"></a>Odeslání pracovního postupu pomocí více vstupů ze stejného vzorku

Tento rychlý start předvádí, jak odeslat pracovní postup službě Microsoft Genomics, pokud váš vstupní soubor obsahuje několik souborů FASTQ nebo BAM **pocházejících ze stejného vzorku**. Pokud jste například v sekvenceru spustili **stejný vzorek** na více řádcích, sekvencer může pro každý řádek vypsat dvojici souborů FASTQ. Místo řetězení těchto souborů FASTQ před porovnáváním sekvencí a detekcí variant můžete všechny tyto vstupy odeslat přímo do klienta `msgen`. Výstupem z klienta `msgen` bude jedna **sada souborů**, včetně souborů .bam, .bai a .vcf. 

Mějte ale na paměti, že v jednom odeslání **nelze** kombinovat soubory FASTQ a BAM. Navíc **nelze** odeslat více souborů FASTQ nebo BAM od více jednotlivců. 

Tento článek předpokládá, že jste už nainstalovali a spustili klienta `msgen` a že víte, jak používat službu Azure Storage. Pokud jste úspěšně odeslali pracovní postup obsahující připravená ukázková data, jste připraveni pokračovat v tomto postupu. 


## <a name="multiple-bam-files"></a>Více souborů BAM

### <a name="upload-your-input-files-to-azure-storage"></a>Odeslání vstupních souborů do Azure Storage
Předpokládejme, že máte jako vstup několik souborů BAM, *reads.bam*, *additional_reads.bam* a *yet_more_reads.bam*, a odeslali jste je do vašeho účtu úložiště *myaccount* v Azure. Máte adresu URL rozhraní API a přístupový klíč. Chcete mít výstupy v **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.


### <a name="submit-your-job-to-the-msgen-client"></a>Odeslání úlohy do klienta `msgen` 

Můžete odeslat více souborů BAM tak, že jejich názvy zadáte v parametru --input-blob-name-1. Všimněte si, že všechny soubory musí pocházet ze stejného vzorku, ale jejich pořadí není důležité. Následující část obsahuje podrobný popis příkladů odesílání z příkazového řádku ve Windows, v Unixu a pomocí konfiguračního souboru. Konce řádků jsou přidány pro přehlednost:


Ve Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam ^
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
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Pokud dáváte přednost použití konfiguračního souboru, měl by obsahovat:

``` config.txt
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads.bam additional_reads.bam yet_more_reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Odešlete soubor `config.txt` tímto voláním: `msgen submit -f config.txt`


## <a name="multiple-paired-fastq-files"></a>Více párovaných souborů FASTQ

### <a name="upload-your-input-files-to-azure-storage"></a>Odeslání vstupních souborů do Azure Storage
Předpokládejme, máte na vstupu několik párovaných souborů FASTQ, *reads_1.fq.gz* a *reads_2.fq.gz*, *additional_reads_1.fq.gz* a *additional_ reads_2.fq.gz* a *yet_more_reads_1.fq.gz* a *yet_more_reads_2.fq.gz*. Nahráli jste je do svého účtu úložiště *myaccount* v Azure a máte adresu URL rozhraní API a přístupový klíč. Chcete mít výstupy v **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.


### <a name="submit-your-job-to-the-msgen-client"></a>Odeslání úlohy do klienta `msgen` 

Párované FASTQ soubory nejenom musí pocházet ze stejného vzorku, ale také musí být zpracovány současně.  Na pořadí názvů souborů záleží, když jsou předávány jako argumenty pomocí --input-blob-name-1 a --input-blob-name-2. 

Následující část obsahuje podrobný popis příkladů odesílání z příkazového řádku ve Windows, v Unixu a pomocí konfiguračního souboru. Konce řádků jsou přidány pro přehlednost:


Ve Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz ^
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz ^
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
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz \
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz \
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
input_blob_name_1:                reads_1.fq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz
input_blob_name_2:                reads_2.fq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Odešlete soubor `config.txt` tímto voláním: `msgen submit -f config.txt`

## <a name="next-steps"></a>Další kroky
V tomto článku jste nahráli několik souborů BAM spárovaných souborů FASTQ do úložiště Azure Storage a odeslali jste pracovní postup do služby Microsoft Genomics pomocí pythonového klienta `msgen`. Další informace týkající se odesílání pracovních postupů a dalších příkazů, které můžete použít se službou Microsoft Genomics, najdete v [nejčastějších dotazech](frequently-asked-questions-genomics.md). 