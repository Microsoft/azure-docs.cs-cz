---
title: Oprava úlohy exportu Azure Import/Export - v1 | Dokumentace Microsoftu
description: Zjistěte, jak opravit, který jste vytvořili a pomocí služby Azure Import/Export spustit úlohy exportu.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: ef5a5f81c5eb3994f62469139c6e835bd802eaa9
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522719"
---
# <a name="repairing-an-export-job"></a>Oprava úlohy exportu
Po dokončení úlohy exportu, můžete spustit nástroj Microsoft Azure Import/Export místní pro:  
  
1.  Stáhněte si všechny soubory, které služba Azure Import/Export se nepodařilo exportovat.  
  
2.  Ověření, aby byly správně exportovány souborů na jednotce.  
  
Musí mít připojení k Azure Storage k použití této funkce.  
  
Příkaz pro oprava úlohy importu **RepairExport**.

## <a name="repairexport-parameters"></a>Parametry RepairExport

Je možné zadat následující parametry při **RepairExport**:  
  
|Parametr|Popis|  
|---------------|-----------------|  
|**/ r: < RepairFile\>**|Povinná hodnota. Cesta k souboru opravit, který sleduje průběh opravy a umožňuje obnovit přerušené opravu, která. Každá jednotka musí mít jeden a pouze jeden soubor opravy. Když spustíte opravu pro daný disk, předáte v cestě opravit soubor, který ještě neexistuje. Pokud chcete obnovit přerušené opravit, je třeba předat název existující soubor opravy. Vždy třeba zadat soubor opravy odpovídající cílové jednotce.|  
|**/logdir: < LogDirectory\>**|Volitelné. Adresář protokolu. Podrobné soubory protokolů se zapíšou do tohoto adresáře. Pokud není zadán žádný adresář protokolu, se použije aktuální adresář jako adresář protokolu.|  
|**/ d: < TargetDirectory\>**|Povinná hodnota. Adresář, který má ověření a oprava. To je obvykle kořenový adresář export disku, ale může také být sdíleného síťového umístění obsahující kopii exportované soubory.|  
|**/BK: < BitLockerKey\>**|Volitelné. Pokud chcete, aby nástroj k odemknutí zašifrované uložení exportované soubory je třeba zadat klíč Bitlockeru.|  
|**/sn: < StorageAccountName\>**|Povinná hodnota. Název účtu úložiště pro úlohu exportu.|  
|**/Sk: < StorageAccountKey\>**|**Vyžaduje** pouze v případě sdíleného přístupového podpisu kontejneru není zadán. Klíč účtu pro účet úložiště pro úlohu exportu.|  
|**/csas: < ContainerSas\>**|**Vyžaduje** pouze v případě není zadaný klíč účtu úložiště. Kontejner SAS pro přístup k objektům BLOB spojené s úlohou export.|  
|**/ CopyLogFile: < DriveCopyLogFile\>**|Povinná hodnota. Cesta k souboru protokolu kopie disku. Soubor je vygenerován pomocí služby Windows Azure Import/Export a si můžete stáhnout z úložiště objektů blob, které jsou přidružené k úloze. Kopírovat soubor protokolu obsahuje informace o neúspěšných objekty BLOB nebo soubory, které mají být opraven.|  
|**/ ManifestFile: < DriveManifestFile\>**|Volitelné. Cesta k souboru manifestu export disku. Tento soubor je vygenerované službou Windows Azure Import/Export a uložené na jednotce exportu a volitelně do objektu BLOB v účtu úložiště, které jsou přidružené k úloze.<br /><br /> Obsah souborů na jednotce exportu se ověří pomocí hodnoty hash MD5 obsažené v tomto souboru. Všechny soubory, které jsou určeny je poškozený, bude stažena a přepsané téma, které cílového adresáře.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>Použití režimu RepairExport k opravě selhání exporty  
Nástroje Azure Import/Export můžete použít ke stahování souborů, které se nepodařilo exportovat. Kopírovat soubor protokolu bude obsahovat seznam souborů, které se nepodařilo exportovat.  
  
Mezi příčiny selhání exportu patří tyto možnosti:  
  
-   Poškozené jednotky  
  
-   Klíč účtu úložiště změnit během procesu převodu  
  
Chcete-li spustit nástroj **RepairExport** režim, musíte nejprve připojte jednotku, která obsahuje exportované soubory do počítače. Následně spusťte nástroj Azure Import/Export, zadání cesty na tuto jednotku s `/d` parametru. Také je nutné zadat cestu k souboru protokolu kopírování disku, který jste stáhli. Následující příklad příkazového řádku níže spustí nástroj opravit všechny soubory, které se nepodařilo exportovat:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
Následující je příklad kopírování souboru protokolu, který ukazuje, že jeden blok v objektu blob selhalo při exportu:  
  
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
  
Kopírování souboru protokolu označuje, že došlo k chybě při služby Windows Azure Import/Export byl stahování jednoho objektu blob bloků do souboru na disku pro export. Ostatní součásti soubor se úspěšně stáhnul a délka souboru byla nastavena správně. Nástroj se v tomto případě otevřete soubor na disku, stáhnout bloku z účtu úložiště a zapisovat do souboru rozsah od posun 65536 s délkou 65536.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>Pomocí RepairExport ověřit obsah jednotky  
Můžete použít také Azure Import/Export s **RepairExport** možnost ověřit obsah na disku jsou správné. Soubor manifestu na každou jednotku exportu obsahuje MD5s pro obsah jednotky.  
  
Služba Azure Import/Export můžete také uložit soubory manifestu do účtu úložiště během procesu exportu. Umístění souborů manifestu je k dispozici prostřednictvím [Get Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) operaci po dokončení úlohy. Zobrazit [služby Import/Export formát souboru manifestu](storage-import-export-file-format-metadata-and-properties.md) Další informace o formátu souboru manifestu jednotky.  
  
Následující příklad ukazuje, jak spustit nástroje Azure Import/Export s **manifestfile** a **/CopyLogFile** parametry:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log /ManifestFile:G:\9WM35C3U.manifest  
```  
  
Následuje příklad soubor manifestu:  
  
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
  
Po dokončení procesu opravy, bude nástroj pročtěte každý soubor odkazovaný v souboru manifestu a ověření integrity souboru se hodnoty hash MD5. Pro manifest výše projdou následující součásti.  

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

Libovolné součásti selhání ověření bude stažena nástrojem a přepsané téma, které stejný soubor na disku.  
  
## <a name="next-steps"></a>Další postup
 
* [Nastavení nástroje Azure Import/Export](storage-import-export-tool-setup-v1.md)   
* [Příprava pevných disků pro úlohu importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Kontrola stavu úlohy s použitím kopií souborů protokolu](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Oprava úlohy importu](storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Řešení potíží s nástrojem Azure pro import/export](storage-import-export-tool-troubleshooting-v1.md)
