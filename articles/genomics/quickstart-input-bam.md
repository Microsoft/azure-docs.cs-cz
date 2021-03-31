---
title: Odeslání pracovního postupu pomocí vstupu souboru BAM
titleSuffix: Microsoft Genomics
description: Tento článek ukazuje, jak odeslat pracovní postup službě Microsoft Genomics, pokud je vstupním souborem jeden soubor BAM.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 5145aa0ffdc4095f178a214f63433e5bcece83b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "72249167"
---
# <a name="submit-a-workflow-using-a-bam-file-input"></a>Odeslání pracovní postupu pomocí vstupního souboru BAM

Tento článek ukazuje, jak odeslat pracovní postup službě Microsoft Genomics, pokud je vstupním souborem jeden soubor BAM. Toto téma předpokládá, že jste už nainstalovali a spustili klienta `msgen` a že víte, jak používat úložiště Azure Storage. Pokud jste úspěšně odeslali pracovní postup pomocí poskytnutých ukázkových dat, budete připraveni pokračovat v tomto článku. 

## <a name="set-up-upload-your-bam-file-to-azure-storage"></a>Příprava: Odeslání souboru BAM do úložiště Azure
Předpokládejme, že máte jeden soubor BAM, *reads.bam*, a že jste ho nahráli do svého účtu úložiště *myaccount* v Azure jako **https://<span></span>myaccount.blob.core <span></span>.windows <span></span>.net <span></span>/inputs/reads <span></span>.bam <span></span>**. Máte adresu URL rozhraní API a přístupový klíč. Chcete mít výstupy v **https://<span></span>myaccount.blob.core <span></span>.windows <span></span>.net <span></span>/outputs <span></span>**.



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
  --input-blob-name-1 reads.bam ^
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
  --input-blob-name-1 reads.bam \
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
input_blob_name_1:                reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Odešlete soubor `config.txt` tímto voláním: `msgen submit -f config.txt`

## <a name="next-steps"></a>Další kroky
V tomto článku jste nahráli soubor BAM do úložiště Azure Storage a odeslali jste pracovní postup do služby Microsoft Genomics pomocí pythonového klienta `msgen`. Další informace týkající se odesílání pracovních postupů a dalších příkazů, které můžete použít se službou Microsoft Genomics, najdete v [nejčastějších dotazech](frequently-asked-questions-genomics.md). 
