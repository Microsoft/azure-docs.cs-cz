---
title: Oprava úlohy exportu Azure Import/Export - v1 | Microsoft Docs
description: Zjistěte, jak opravit úlohu export, který byl vytvořen a spuštění pomocí služby Azure Import/Export.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 728e2a42-04ce-4be8-9375-e9e2bc6827a5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 57ab58fa1fd8371d0b6f019f94bb162bcc1e0e43
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873874"
---
# <a name="repairing-an-export-job"></a>Oprava úlohy exportu
Po dokončení úlohy exportu, můžete spustit nástroj Microsoft Azure Import/Export místní na:  
  
1.  Stáhněte všechny soubory, které služba Azure Import/Export se nepodařilo exportovat.  
  
2.  Ověření, aby byly správně exportovány soubory na disku.  
  
Musí mít připojení k Azure Storage k použití této funkce.  
  
Příkaz pro opravu úlohy importu je **RepairExport**.

## <a name="repairexport-parameters"></a>Parametry RepairExport

Mohou být zadány následující parametry s **RepairExport**:  
  
|Parametr|Popis|  
|---------------|-----------------|  
|**/ r: < RepairFile\>**|Povinná hodnota. Cesta k souboru oprava, která sleduje průběh opravu a umožňuje obnovit přerušené opravit. Každé jednotky, musí mít pouze jeden soubor opravit. Při spuštění opravy pro danou jednotku, bude předáte v cestě k oprava souboru, který ještě neexistuje. Pokud chcete obnovit přerušené opravy, by měla předávat název existující soubor opravit. Soubor opravy odpovídající cílová jednotka je vždy třeba zadat.|  
|**/logdir: < LogDirectory\>**|Volitelné. K adresáři protokolů. Souborů podrobného protokolování se zapíšou do tohoto adresáře. Pokud není zadán žádný adresář protokolu, aktuální adresář se použije jako adresář protokolu.|  
|**/ d: < TargetDirectory\>**|Povinná hodnota. Adresář, který chcete ověřit a opravit. To je obvykle kořenový adresář jednotky exportu, ale může také být do síťové sdílené složky obsahující kopii exportované soubory.|  
|**/BK: < BitLockerKey\>**|Volitelné. Pokud chcete nástroj k odemknutí zašifrované uložení exportované soubory musíte zadat klíč nástroje BitLocker.|  
|**/sn: < StorageAccountName\>**|Povinná hodnota. Název účtu úložiště pro úlohy exportu.|  
|**/Sk: < StorageAccountKey\>**|**Požadované** Pokud není zadán sdíleného přístupového podpisu kontejneru. Klíč účtu pro účet úložiště pro úlohy exportu.|  
|**/csas: < ContainerSas\>**|**Požadované** Pokud není zadaný klíč účtu úložiště. Kontejner SAS pro přístup k objektům BLOB spojené s úlohou export.|  
|**/ CopyLogFile: < DriveCopyLogFile\>**|Povinná hodnota. Cesta k souboru protokolu kopie disku. Soubor je generována pomocí služby Windows Azure Import/Export a si můžete stáhnout z úložiště objektů blob, které jsou přidružené k úloze. Kopírovat soubor protokolu obsahuje informace o selhání objektů BLOB nebo soubory, které mají být opravit.|  
|**/ ManifestFile: < DriveManifestFile\>**|Volitelné. Cesta k souboru manifestu export jednotky. Tento soubor je generována pomocí služby Windows Azure Import/Export a uložené na jednotce exportu a volitelně do objektu BLOB v účtu úložiště, které jsou přidružené k úloze.<br /><br /> Obsah souborů na jednotce exportu se ověří pomocí hodnot hash MD5, obsažené v tomto souboru. Všechny soubory, které jsou určeny poškozen bude stažena a přepisuje, aby cílový adresáře.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>Použití režimu RepairExport k opravě neúspěšné exporty  
Nástroj Azure Import/Export můžete stáhnout soubory, které se nepodařilo exportovat. Kopírování souboru protokolu bude obsahovat seznam souborů, které se nepodařilo exportovat.  
  
Příčiny chyb export patří tyto možnosti:  
  
-   Poškozené jednotky  
  
-   Změnit během procesu přenosu klíče účtu úložiště.  
  
Chcete-li spustit nástroj **RepairExport** režim, musíte nejdřív připojit jednotku, která obsahuje exportované soubory do počítače. Potom spusťte nástroj Azure Import/Export, zadání cesty na tuto jednotku s `/d` parametr. Také budete muset zadat cestu k souboru protokolu na jednotku kopie, který jste stáhli. Následující příklad následující příkazový řádek spustí nástroj a opravte všechny soubory, které se nepodařilo exportovat:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
Toto je příklad kopírovat soubor protokolu, který ukazuje, že jeden blok v objektu blob selhalo při exportu:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/desert.jpg</BlobPath>  
    <FilePath>\pictures\wild\desert.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList>  
      <Block Offset="65536" Length="65536" Id="AQAAAA==" Status="Failed" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
Kopírování souboru protokolu označuje, že selhání došlo k chybě služby Windows Azure Import/Export byl stahování jeden objekt blob bloků k souboru na jednotce export. Ostatní součásti souboru úspěšně stažen a délku souboru byla nastavena správně. V takovém případě nástroj se otevřít soubor na disku, stáhněte bloku z účtu úložiště a zapisovat do souboru rozsah od posun 65536 s délkou 65536.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>Pomocí RepairExport ověřit obsah jednotky  
Můžete také použít Azure Import/Export s **RepairExport** možnost k ověření obsahu na jednotce jsou správné. Soubor manifestu na každém disku exportu obsahuje MD5s pro obsah jednotky.  
  
Služba Azure Import/Export můžete také na účet úložiště uložit soubory manifestu během procesu exportu. Umístění manifestu souborů je k dispozici prostřednictvím [Get Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) operaci po dokončení úlohy. V tématu [službu Import/Export formát souboru manifestu](storage-import-export-file-format-metadata-and-properties.md) Další informace o formátu souboru manifestu jednotky.  
  
Následující příklad ukazuje, jak spustit nástroj Azure Import/Export s **/ManifestFile** a **/CopyLogFile** parametry:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log /ManifestFile:G:\9WM35C3U.manifest  
```  
  
Následuje příklad souboru manifestu:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveManifest Version="2011-10-01">  
  <Drive>  
    <DriveId>9WM35C3U</DriveId>  
    <ClientCreator>Windows Azure Import/Export service</ClientCreator>  
    <BlobList>
      <Blob>  
        <BlobPath>pictures/city/redmond.jpg</BlobPath>  
        <FilePath>\pictures\city\redmond.jpg</FilePath>  
        <Length>15360</Length>  
        <PageRangeList>  
          <PageRange Offset="0" Length="3584" Hash="72FC55ED9AFDD40A0C8D5C4193208416" />  
          <PageRange Offset="3584" Length="3584" Hash="68B28A561B73D1DA769D4C24AA427DB8" />  
          <PageRange Offset="7168" Length="512" Hash="F521DF2F50C46BC5F9EA9FB787A23EED" />  
        </PageRangeList>  
        <PropertiesPath Hash="E72A22EA959566066AD89E3B49020C0A">\pictures\city\redmond.jpg.properties</PropertiesPath>  
      </Blob>  
      <Blob>  
        <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
        <FilePath>\pictures\wild\canyon.jpg</FilePath>  
        <Length>10884</Length>  
        <BlockList>  
          <Block Offset="0" Length="2721" Id="AAAAAA==" Hash="263DC9C4B99C2177769C5EBE04787037" />  
          <Block Offset="2721" Length="2721" Id="AQAAAA==" Hash="0C52BAE2CC20EFEC15CC1E3045517AA6" />  
          <Block Offset="5442" Length="2721" Id="AgAAAA==" Hash="73D1CB62CB426230C34C9F57B7148F10" />  
          <Block Offset="8163" Length="2721" Id="AwAAAA==" Hash="11210E665C5F8E7E4F136D053B243E6A" />  
        </BlockList>  
        <PropertiesPath Hash="81D7F81B2C29F10D6E123D386C3A4D5A">\pictures\wild\canyon.jpg.properties</PropertiesPath>  
      </Blob> 
    </BlobList>  
 </Drive>  
</DriveManifest>  
``` 
  
Po dokončení procesu opravy, bude nástroj přečíst každý soubor v souboru manifestu odkazuje a ověří integritu souboru s hodnoty hash MD5. Pro manifest výše protože budou přenášeny pomocí následující součásti.  

```  
G:\pictures\city\redmond.jpg, offset 0, length 3584  
  
G:\pictures\city\redmond.jpg, offset 3584, length 3584  
  
G:\pictures\city\redmond.jpg, offset 7168, length 3584  
  
G:\pictures\city\redmond.jpg.properties  
  
G:\pictures\wild\canyon.jpg, offset 0, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 2721, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 5442, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 8163, length 2721  
  
G:\pictures\wild\canyon.jpg.properties  
```

Všechny součásti, které selhání ověření bude stažena nástrojem a přepsaná do stejného souboru na disku.  
  
## <a name="next-steps"></a>Další kroky
 
* [Nastavení nástroje Azure Import/Export](storage-import-export-tool-setup-v1.md)   
* [Příprava pevných disků pro úlohu importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Kontrola stavu úlohy s použitím kopií souborů protokolu](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Oprava úlohy importu](storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Řešení potíží s nástrojem Azure pro import/export](storage-import-export-tool-troubleshooting-v1.md)
