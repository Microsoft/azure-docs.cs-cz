---
title: Odeslání pracovního postupu pomocí vstupních souborů souborů fastq
titleSuffix: Microsoft Genomics
description: Tento článek ukazuje, jak odeslat pracovní postup službě Microsoft Genomics, pokud jsou vstupní soubory jedním párem souborů fastq souborů.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 3806b165e5abb661e53c6a315650d025fd42e17f
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248547"
---
# <a name="submit-a-workflow-using-fastq-file-inputs-in-microsoft-genomics"></a>Odeslání pracovního postupu pomocí vstupních souborů souborů fastq v Microsoft Genomics

Tento článek ukazuje, jak odeslat pracovní postup službě Microsoft Genomics, pokud jsou vstupní soubory jedním párem souborů fastq souborů. Toto téma předpokládá, že jste už nainstalovali a spustili klienta `msgen` a že máte zkušenosti s používáním Azure Storage. Pokud jste úspěšně odeslali pracovní postup pomocí poskytnutých ukázkových dat, budete připraveni pokračovat v tomto článku. 

## <a name="set-up-upload-your-fastq-files-to-azure-storage"></a>Nastavení: nahrání souborů souborů fastq do služby Azure Storage
Předpokládejme, že máte dva soubory *reads_1. fq. gz* a *reads_2. fq. gz*a nahráli jste je do svého účtu úložiště *MyAccount* v Azure jako **https://<span></span>myaccount. blob. Core<span></span>. Windows<span></span>.NET/<span></span> Inputs/<span></span>reads_1.<span></span>fq.<span> </span> GZ** a **https://<span></span>myaccount. blob. Core<span> </span> . Windows<span></span>.NET/<span></span>Inputs<span></span>/reads_2.<span></span>fq.<span></span>GZ**. Máte adresu URL rozhraní API a přístupový klíč. Chcete mít výstupy v **<span></span>https://myaccount. blob. Core<span></span>. Windows<span></span>.NET<span></span>/Outputs<span></span>** .


## <a name="submit-your-job-to-the-msgen-client"></a>Odeslání úlohy do klienta `msgen` 

Tady je minimální sada argumentů, které budete muset poskytnout klientovi `msgen`. bylo přidáno zalomení řádků pro přehlednost:

Pro Windows:

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

Pro UNIX:

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


Pokud dáváte přednost použití konfiguračního souboru, bude obsahovat:

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

Odeslat soubor `config.txt` s tímto voláním: `msgen submit -f config.txt`

## <a name="next-steps"></a>Další kroky
V tomto článku jste nahráli pár souborů souborů fastq do Azure Storage a odeslali pracovní postup do služby Microsoft Genomics prostřednictvím klienta Pythonu `msgen`. Další informace o odesílání pracovních postupů a dalších příkazech, které můžete použít se službou Microsoft Genomics, najdete v části [Nejčastější dotazy](frequently-asked-questions-genomics.md). 
