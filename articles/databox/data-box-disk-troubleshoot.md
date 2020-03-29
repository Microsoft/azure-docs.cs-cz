---
title: Azure Data Box Disk – řešení potíží | Microsoft Docs
description: Popisuje, jak řešit potíže s Azure Data Box Diskem.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: f8116ec0836623adf803991017950ddc7f960923
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67805711"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>Řešení problémů s ověřením na disku Azure Data Box Disk pomocí protokolů

Tento článek se týká disku datové schránky Microsoft Azure. Článek popisuje, jak pomocí protokolů k řešení problémů s ověřením, které se mohou zobrazit při nasazení tohoto řešení.

## <a name="validation-tool-log-files"></a>Soubory protokolu nástroje pro ověřování

Při ověřování dat na discích pomocí [ověřovacího nástroje](data-box-disk-deploy-copy-data.md#validate-data)je generován soubor *error.xml,* který zaznamenává všechny chyby. Soubor protokolu je umístěn `Drive:\DataBoxDiskImport\logs` ve složce jednotky. Při spuštění ověření je k dispozici odkaz na protokol chyb.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

Pokud spustíte více relací pro ověření, je vygenerován jeden protokol chyb na jednu relaci.

- Zde je ukázka protokolu chyb, když `PageBlob` data načtená do složky nejsou zarovnána 512 bajtů. Všechna data odeslaná do objektu PageBlob musí mít zarovnané 512 bajtů, například VHD nebo VHDX. Chyby v tomto souboru jsou v `<Errors>` a upozornění v `<Warnings>`.

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

- Zde je ukázka protokolu chyb, pokud název kontejneru není platný. Složka, kterou vytvoříte `PageBlob`v `AzureFile` rámci `BlockBlob`aplikace , nebo složky na disku se stane kontejnerem v účtu služby Azure Storage. Název kontejneru musí dodržovat [konvence pojmenování Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

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

## <a name="validation-tool-errors"></a>Chyby ověřovacího nástroje

Chyby obsažené v *souboru error.xml* s odpovídajícími doporučenými akcemi jsou shrnuty v následující tabulce.

| Kód chyby| Popis                       | Doporučené akce               |
|------------|--------------------------|-----------------------------------|
| `None` | Data byla úspěšně ověřena. | Nevyžaduje se žádná akce. |
| `InvalidXmlCharsInPath` |Nelze vytvořit soubor manifestu, protože cesta k souboru obsahuje neplatné znaky. | Chcete-li pokračovat, odeberte tyto znaky.  |
| `OpenFileForReadFailed`| Soubor nelze zpracovat. To může být způsobeno problémem s přístupem nebo poškozením systému souborů.|Soubor nelze přečíst z důvodu chyby. Podrobnosti o chybě jsou ve výjimce. |
| `Not512Aligned` | Tento soubor není v platném formátu pro složku PageBlob.| Nahrajte pouze data, která jsou 512 bajtů zarovnaná do `PageBlob` složky. Odeberte soubor ze složky PageBlob nebo ho přesuňte do složky BlockBlob. Opakujte ověření.|
| `InvalidBlobPath` | Cesta k souboru není mapovat na platnou cestu objektu blob v cloudu podle azure blob konvence pojmenování.|Podle pokynů pro pojmenování Azure přejmenujte cestu k souboru. |
| `EnumerationError` | Nelze vytvořit výčet souboru pro ověření. |Může být několik důvodů pro tuto chybu. Nejpravděpodobnějším důvodem je přístup k souboru. |
| `ShareSizeExceeded` | Tento soubor způsobil, že velikost sdílené složky Azure překročila limit Azure 5 TB.|Zmenšete velikost dat ve sdílené složce tak, aby odpovídala [omezení velikosti objektu Azure](data-box-disk-limits.md#azure-object-size-limits). Opakujte ověření. |
| `AzureFileSizeExceeded` | Velikost souboru překračuje limity velikosti souboru Azure.| Zmenšete velikost souboru nebo dat tak, aby odpovídala [omezením velikosti objektu Azure](data-box-disk-limits.md#azure-object-size-limits). Opakujte ověření.|
| `BlockBlobSizeExceeded` | Velikost souboru překračuje limity velikosti objektů blob azure bloku. | Zmenšete velikost souboru nebo dat tak, aby odpovídala [omezením velikosti objektu Azure](data-box-disk-limits.md#azure-object-size-limits). Opakujte ověření. |
| `ManagedDiskSizeExceeded` | Velikost souboru překračuje limity velikosti spravovaného disku Azure. | Zmenšete velikost souboru nebo dat tak, aby odpovídala [omezením velikosti objektu Azure](data-box-disk-limits.md#azure-object-size-limits). Opakujte ověření. |
| `PageBlobSizeExceeded` | Velikost souboru překračuje limity velikosti spravovaného disku Azure. | Zmenšete velikost souboru nebo dat tak, aby odpovídala [omezením velikosti objektu Azure](data-box-disk-limits.md#azure-object-size-limits). Opakujte ověření. |
| `InvalidShareContainerFormat`  |Názvy adresářů neodpovídají konvencím pojmenování Azure pro kontejnery nebo sdílené složky.         |První složka vytvořená pod existujícími složkami na disku se stane kontejnerem v účtu úložiště. Tento název sdílené složky nebo kontejneru neodpovídá konvencím pojmenování Azure. Přejmenujte soubor tak, aby odpovídal [konvencím pojmenování Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Opakujte ověření.   |
| `InvalidBlobNameFormat` | Cesta k souboru není mapovat na platnou cestu objektu blob v cloudu podle azure blob konvence pojmenování.|Přejmenujte soubor tak, aby odpovídal [konvencím pojmenování Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Opakujte ověření. |
| `InvalidFileNameFormat` | Cesta k souboru se nemapuje na platnou cestu k souboru v cloudu podle konvencí pojmenování souborů Azure. |Přejmenujte soubor tak, aby odpovídal [konvencím pojmenování Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Opakujte ověření. |
| `InvalidDiskNameFormat` | Cesta k souboru není mapována na platný název disku v cloudu podle konvencí pojmenování spravovaného disku Azure. |Přejmenujte soubor tak, aby odpovídal [konvencím pojmenování Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Opakujte ověření.       |
| `NotPartOfFileShare` | Cesta k nahrání souborů není platná. Nahrajte soubory do složky v Azure Files.   | Odeberte soubory omylem a nahrajte je do předem vytvořené složky. Opakujte ověření. |
| `NonVhdFileNotSupportedForManagedDisk` | Soubor bez virtuálního pevného disku nelze odeslat jako spravovaný disk. |Odeberte soubory, které `ManagedDisk` nejsou v pevném rozlišení, ze `PageBlob` složky, protože nejsou podporovány, nebo je přesuňte do složky. Opakujte ověření. |


## <a name="next-steps"></a>Další kroky

- Poradce při potížích s [chybami při odesílání dat](data-box-disk-troubleshoot-upload.md).
