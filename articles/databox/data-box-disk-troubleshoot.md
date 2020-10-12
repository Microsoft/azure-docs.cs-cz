---
title: Azure Data Box Disk – řešení potíží | Microsoft Docs
description: Naučte se používat protokoly k řešení problémů s ověřováním, které se můžou při nasazení Azure Data Box Disk nasadit.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 1c1b38c4021660b9f59098f8442d16bfd0ecc582
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87925538"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>Řešení potíží s ověřováním v Azure Data Box Disk pomocí protokolů

Tento článek se týká Microsoft Azure Data Box Disk. Tento článek popisuje, jak používat protokoly k řešení potíží s ověřováním, které vidíte při nasazení tohoto řešení.

## <a name="validation-tool-log-files"></a>Soubory protokolu nástroje pro ověření

Když ověříte data na discích pomocí [Nástroje pro ověření](data-box-disk-deploy-copy-data.md#validate-data), vygeneruje se *error.xml* pro protokolování chyb. Soubor protokolu je umístěný ve  `Drive:\DataBoxDiskImport\logs` složce vaší jednotky. Odkaz na protokol chyb je k dispozici při spuštění ověřování.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

Pokud spustíte více relací pro ověření, je pro každou relaci vygenerován jeden protokol chyb.

- Zde je ukázka protokolu chyb, když data načtená do `PageBlob` složky nejsou 512 – zarovnaná bajty. Všechna data nahraná do PageBlob musí být 512 – zarovnaná bajty, například VHD nebo VHDX. Chyby v tomto souboru jsou v `<Errors>` upozorněních a `<Warnings>` .

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
            <SessionId>session#1</SessionId>
            <ItemType>PageBlob</ItemType>
            <SourceDirectory>D:\Dataset\TestDirectory</SourceDirectory>
            <Errors>
                <Error Code="Not512Aligned">
                    <Description>The file is not 512 bytes aligned.</Description>
                    <List>
                        <File Path="\Practice\myScript.ps1" />
                    </List>
                    <Count>1</Count>
                </Error>
            </Errors>
            <Warnings />
        </ErrorLog>
    ```

- Tady je ukázka protokolu chyb, pokud název kontejneru není platný. Složka, kterou vytvoříte v adresáři `BlockBlob` , `PageBlob` nebo `AzureFile` složky na disku, se v účtu Azure Storage stal kontejnerem. Název kontejneru musí splňovat [zásady vytváření názvů pro Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
          <SessionId>bbsession</SessionId>
          <ItemType>BlockBlob</ItemType>
          <SourceDirectory>E:\BlockBlob</SourceDirectory>
          <Errors>
            <Error Code="InvalidShareContainerFormat">
              <List>
                <Container Name="Azu-reFile" />
                <Container Name="bbcont ainer1" />
              </List>
              <Count>2</Count>
            </Error>
          </Errors>
          <Warnings />
    </ErrorLog>
    ```

## <a name="validation-tool-errors"></a>Chyby nástroje pro ověření

Chyby obsažené v *error.xml* s odpovídajícími doporučenými akcemi jsou shrnuty v následující tabulce.

| Kód chyby| Description                       | Doporučené akce               |
|------------|--------------------------|-----------------------------------|
| `None` | Data byla úspěšně ověřena. | Nevyžaduje se žádná akce. |
| `InvalidXmlCharsInPath` |Nelze vytvořit soubor manifestu, protože cesta k souboru obsahuje neplatné znaky. | Chcete-li pokračovat, odeberte tyto znaky.  |
| `OpenFileForReadFailed`| Soubor nelze zpracovat. Důvodem může být problém s přístupem nebo poškození systému souborů.|Kvůli chybě se nepovedlo přečíst soubor. Podrobnosti o chybě jsou ve výjimce. |
| `Not512Aligned` | Tento soubor nemá platný formát pro složku PageBlob.| Nahrávat pouze data, která jsou 512 bajtů zarovnaných do `PageBlob` složky. Odeberte soubor ze složky PageBlob nebo ho přesuňte do složky BlockBlob. Opakujte ověření.|
| `InvalidBlobPath` | Cesta k souboru není namapována na platnou cestu objektu BLOB v cloudu podle zásad vytváření názvů objektů BLOB v Azure.|Pokud chcete přejmenovat cestu k souboru, postupujte podle pokynů pro pojmenování Azure. |
| `EnumerationError` | Nepodařilo se vytvořit výčet souboru pro ověření. |Tato chyba může mít několik příčin. Nejpravděpodobnějším důvodem je přístup k souboru. |
| `ShareSizeExceeded` | Tento soubor způsobil, že velikost sdílené složky Azure překračuje limit Azure 5 TB.|Zmenšete velikost dat ve sdílené složce tak, aby splňovala [omezení velikosti objektů Azure](data-box-disk-limits.md#azure-object-size-limits). Opakujte ověření. |
| `AzureFileSizeExceeded` | Velikost souboru překračuje limity velikosti souborů Azure.| Zmenšete velikost souboru nebo dat tak, aby splňovala [omezení velikosti objektů Azure](data-box-disk-limits.md#azure-object-size-limits). Opakujte ověření.|
| `BlockBlobSizeExceeded` | Velikost souboru překračuje limity omezení velikosti objektů blob bloku Azure. | Zmenšete velikost souboru nebo dat tak, aby splňovala [omezení velikosti objektů Azure](data-box-disk-limits.md#azure-object-size-limits). Opakujte ověření. |
| `ManagedDiskSizeExceeded` | Velikost souboru překračuje limity velikosti spravovaného disku Azure. | Zmenšete velikost souboru nebo dat tak, aby splňovala [omezení velikosti objektů Azure](data-box-disk-limits.md#azure-object-size-limits). Opakujte ověření. |
| `PageBlobSizeExceeded` | Velikost souboru překračuje limity velikosti spravovaného disku Azure. | Zmenšete velikost souboru nebo dat tak, aby splňovala [omezení velikosti objektů Azure](data-box-disk-limits.md#azure-object-size-limits). Opakujte ověření. |
| `InvalidShareContainerFormat`  |Názvy adresářů neodpovídají konvencím názvů Azure pro kontejnery nebo sdílené složky.         |První složka vytvořená v rámci existujících složek na disku se v účtu úložiště stal kontejnerem. Tato sdílená složka nebo název kontejneru neodpovídají konvencím pojmenování Azure. Přejmenujte soubor tak, aby splňoval [konvence vytváření názvů Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Opakujte ověření.   |
| `InvalidBlobNameFormat` | Cesta k souboru není namapována na platnou cestu objektu BLOB v cloudu podle zásad vytváření názvů objektů BLOB v Azure.|Přejmenujte soubor tak, aby splňoval [konvence vytváření názvů Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Opakujte ověření. |
| `InvalidFileNameFormat` | Cesta k souboru není namapována na platnou cestu k souboru v cloudu podle konvencí pro pojmenovávání souborů Azure. |Přejmenujte soubor tak, aby splňoval [konvence vytváření názvů Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Opakujte ověření. |
| `InvalidDiskNameFormat` | Cesta k souboru není v cloudu namapována na platný název disku podle konvencí pojmenování na spravovaném disku Azure. |Přejmenujte soubor tak, aby splňoval [konvence vytváření názvů Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Opakujte ověření.       |
| `NotPartOfFileShare` | Cesta pro odeslání souborů není platná. Nahrajte soubory do složky ve službě soubory Azure.   | Odstraňte soubory z chyb a nahrajte tyto soubory do předvytvořené složky. Opakujte ověření. |
| `NonVhdFileNotSupportedForManagedDisk` | Soubor, který není VHD, se nedá nahrát jako spravovaný disk. |Odeberte ze složky soubory, které nejsou VHD `ManagedDisk` , protože tyto soubory nejsou podporované, nebo je přesuňte do `PageBlob` složky. Opakujte ověření. |


## <a name="next-steps"></a>Další kroky

- Řešení [chyb při nahrávání dat](data-box-disk-troubleshoot-upload.md)
