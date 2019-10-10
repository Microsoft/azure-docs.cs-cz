---
title: Odeslání pracovního postupu s použitím více vstupů
titleSuffix: Microsoft Genomics
description: Tento článek ukazuje, jak odeslat pracovní postup službě Microsoft Genomics, pokud je vstupním souborem více souborů souborů fastq nebo BAM ze stejné ukázky.
services: genomics
ms.service: genomics
author: grhuynh
manager: cgronlund
ms.author: grhuynh
ms.topic: conceptual
ms.date: 02/05/2018
ms.openlocfilehash: b426015906a8e17674123c0c3ad2fccb9c43798f
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248569"
---
# <a name="submit-a-workflow-using-multiple-inputs-from-the-same-sample"></a>Odeslání pracovního postupu pomocí více vstupů ze stejné ukázky

Tento článek ukazuje, jak odeslat pracovní postup službě Microsoft Genomics, pokud je vstupním souborem více souborů souborů fastq nebo BAM, **které pocházejí ze stejné ukázky**. Pokud jste například spustili **stejnou ukázku** na více koridorech v sekvenci, může Sequencer vytvořit dvojici souborů fastq souborů pro každou dráhu. Namísto zřetězení těchto souborů souborů fastq před zarovnáním a voláním variant můžete přímo odeslat všechny tyto vstupy do klienta `msgen`. Výstupem z klienta `msgen` je **jedna sada** souborů, včetně souboru. BAM,. Bai,. vcf. 

Mějte ale na paměti, že **nemůžete** kombinovat soubory souborů fastq a BAM ve stejném odeslání. Dále **nemůžete** odeslat více souborů souborů fastq nebo BAM z více jednotlivců. 

Tento článek předpokládá, že jste už nainstalovali a spustili klienta `msgen` a že jste obeznámeni s používáním Azure Storage. Pokud jste úspěšně odeslali pracovní postup pomocí poskytnutých ukázkových dat, budete připraveni pokračovat v tomto článku. 


## <a name="multiple-bam-files"></a>Více souborů BAM

### <a name="upload-your-input-files-to-azure-storage"></a>Nahrání vstupních souborů do služby Azure Storage
Předpokládejme, že máte několik souborů BAM jako vstup, *přečte. BAM*, *additional_reads. BAM*a *yet_more_reads. BAM*a nahráli jste je do svého účtu úložiště *MyAccount* v Azure. Máte adresu URL rozhraní API a přístupový klíč. Chcete mít výstupy v **<span></span>https://myaccount. blob. Core<span></span>. Windows<span></span>.NET<span></span>/Outputs<span></span>** .


### <a name="submit-your-job-to-the-msgen-client"></a>Odeslání úlohy do klienta `msgen` 

Můžete odeslat více souborů BAM předáním všech jejich názvů do argumentu--INPUT-BLOB-Name-1. Všimněte si, že všechny soubory by měly pocházet ze stejné ukázky, ale jejich pořadí není důležité. Následující část popisuje příklady odeslání z příkazového řádku v systému Windows, v systému UNIX a pomocí konfiguračního souboru. Bylo přidáno zalomení řádků pro přehlednost:


Pro Windows:

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


Pro UNIX

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


Pokud dáváte přednost použití konfiguračního souboru, bude obsahovat:

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

Odeslat soubor `config.txt` s tímto voláním: `msgen submit -f config.txt`


## <a name="multiple-paired-fastq-files"></a>Několik spárovaných souborů souborů fastq

### <a name="upload-your-input-files-to-azure-storage"></a>Nahrání vstupních souborů do služby Azure Storage
Předpokládejme, že máte několik spárovaných souborů souborů fastq jako vstup, *reads_1. fq. gz* a *reads_2. fq. gz*, *additional_reads_1. fq. gz* a *additional_reads_2. fq. gz*a *yet_more_reads_1. fq. gz* a *yet_more_reads_ 2. fq. gz*. Nahráli jste je do svého účtu úložiště *MyAccount* v Azure a vy. Máte adresu URL rozhraní API a přístupový klíč. Chcete mít výstupy v **<span></span>https://myaccount. blob. Core<span></span>. Windows<span></span>.NET<span></span>/Outputs<span></span>** .


### <a name="submit-your-job-to-the-msgen-client"></a>Odeslání úlohy do klienta `msgen` 

Spárované soubory souborů fastq nemusejí pocházet jenom ze stejné ukázky, ale musí se taky zpracovávat společně.  Pořadí názvů souborů je v případě, že jsou předány jako argumenty typu--Input-BLOB-Name-1 a--Input-BLOB-Name-2. 

Následující část popisuje příklady odeslání z příkazového řádku v systému Windows, v systému UNIX a pomocí konfiguračního souboru. Bylo přidáno zalomení řádků pro přehlednost:


Pro Windows:

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


Pro UNIX:

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


Pokud dáváte přednost použití konfiguračního souboru, bude obsahovat:

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

Odeslat soubor `config.txt` s tímto voláním: `msgen submit -f config.txt`

## <a name="next-steps"></a>Další kroky
V tomto článku jste nahráli několik souborů BAM nebo spárovaných souborů souborů fastq do Azure Storage a odeslali jste pracovní postup do služby Microsoft Genomics prostřednictvím klienta Pythonu `msgen`. Další informace týkající se odesílání pracovních postupů a dalších příkazů, které můžete použít se službou Microsoft Genomics, najdete v části [Nejčastější dotazy](frequently-asked-questions-genomics.md). 