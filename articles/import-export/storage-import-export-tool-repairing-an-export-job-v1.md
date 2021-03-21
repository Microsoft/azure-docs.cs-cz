---
title: Oprava úlohy exportu importu/exportu Azure – v1 | Microsoft Docs
description: Naučte se, jak opravit úlohu exportu, která byla vytvořená a spuštěná pomocí služby Azure import/export.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: d84f26b2764a103a9b504c1480e88b58fed3c201
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98706318"
---
# <a name="repairing-an-export-job"></a>Oprava úlohy exportu
Po dokončení úlohy exportu můžete spustit nástroj Microsoft Azure Import/Export místně a:  
  
1.  Stáhněte si všechny soubory, které služba Azure import/export nemohla exportovat.  
  
2.  Ověřte, zda byly soubory na jednotce správně exportovány.  
  
Abyste mohli tuto funkci používat, musíte mít připojení k Azure Storage.  
  
Příkaz pro opravu úlohy importu je **RepairExport**.

## <a name="repairexport-parameters"></a>Parametry RepairExport

Pomocí **RepairExport** lze zadat následující parametry:  
  
|Parametr|Popis|  
|---------------|-----------------|  
|**/r: <RepairFile\>**|Povinná hodnota. Cesta k souboru opravy, který sleduje průběh opravy a umožňuje obnovit přerušenou opravu. Každá jednotka musí mít jeden a jenom jeden opravný soubor. Když zahájíte opravu dané jednotky, předáte cestu k souboru opravy, který ještě neexistuje. Chcete-li obnovit přerušenou opravu, měli byste předat název existujícího opravného souboru. Vždy zadejte soubor opravy odpovídající cílové jednotce.|  
|**/logdir: <LogDirectory\>**|Nepovinný parametr. Adresář protokolu. Podrobné soubory protokolu budou zapsány do tohoto adresáře. Pokud není zadaný žádný adresář protokolu, použije se jako adresář protokolu aktuální adresář.|  
|**/d: <TargetDirectory\>**|Povinná hodnota. Adresář, který se má ověřit a opravit Tento adresář je obvykle kořenový adresář jednotky exportu, ale může to být také síťová sdílená složka obsahující kopii exportovaných souborů.|  
|**/BK: <BitLockerKey\>**|Nepovinný parametr. Zadejte klíč BitLockeru, pokud chcete, aby nástroj Odemkl šifrované místo, kde jsou uloženy exportované soubory.|  
|**/SN: <StorageAccountName\>**|Povinná hodnota. Název účtu úložiště pro úlohu exportu|  
|**/SK: <StorageAccountKey\>**|**Vyžaduje** se, pokud a jenom v případě, že není zadané SAS kontejneru. Klíč účtu pro účet úložiště pro úlohu exportu|  
|**/csas: <ContainerSas\>**|**Vyžaduje** se jenom v případě, že není zadaný klíč účtu úložiště. SAS kontejneru pro přístup k objektům blob přidruženým k úloze exportu|  
|**/CopyLogFile: <DriveCopyLogFile\>**|Povinná hodnota. Cesta k souboru protokolu pro kopírování jednotek Soubor vygeneruje služba Windows Azure pro import/export a dá se stáhnout z úložiště objektů BLOB přidruženého k úloze. Soubor protokolu kopírování obsahuje informace o neúspěšných objektech blob nebo souborech, které se mají opravit.|  
|**/ManifestFile: <DriveManifestFile\>**|Nepovinný parametr. Cesta k souboru manifestu pro exportovou jednotku. Tento soubor je vygenerovaný službou Windows Azure import/export a uložený na jednotce pro export. Volitelně můžete v objektu BLOB v účtu úložiště přidruženého k úloze.<br /><br /> Obsah souborů na exportní jednotce se ověří pomocí hodnot hash MD5 obsažených v tomto souboru. Všechny poškozené soubory budou staženy a přepsány do cílových adresářů.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>Oprava neúspěšných exportů pomocí režimu RepairExport  
K stažení souborů, které se nepodařilo exportovat, můžete použít nástroj pro import/export Azure. Soubor protokolu kopírování bude obsahovat seznam souborů, které se nepodařilo exportovat.  
  
Příčiny selhání při exportu zahrnují následující možnosti:  
  
-   Poškozené jednotky  
  
-   Klíč účtu úložiště se během procesu přenosu změnil.  
  
Chcete-li spustit nástroj v režimu **RepairExport** , musíte nejprve připojit jednotku obsahující exportované soubory do počítače. V dalším kroku spusťte nástroj Azure import/export a určete cestu k této jednotce s `/d` parametrem. Musíte také zadat cestu k souboru protokolu pro kopírování jednotky, který jste stáhli. Následující příklad příkazového řádku níže spustí nástroj pro opravu všech souborů, které se nepodařilo exportovat:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
Následující příklad je soubor protokolu kopírování, který ukazuje, že jeden blok v objektu BLOB se nepovedlo exportovat:  
  
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
  
Soubor protokolu kopírování indikuje, že došlo k chybě, když služba Windows Azure import/export stáhla jeden z bloků objektu blob do souboru na exportní jednotce. Ostatní součásti souboru byly úspěšně staženy a délka souboru byla nastavena správně. V takovém případě nástroj otevře soubor na jednotce, stáhne z účtu úložiště blok a zapíše ho do rozsahu souborů začínajícího posunem 65536 a délkou 65536.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>Ověření obsahu jednotky pomocí RepairExport  
K ověření, jestli je obsah na disku správný, můžete taky použít Azure import/export s možností **RepairExport** . Soubor manifestu na každé jednotce pro export obsahuje MD5s pro obsah jednotky.  
  
Služba import/export v Azure může také ukládat soubory manifestu do účtu úložiště během procesu exportu. Umístění souborů manifestu je k dispozici prostřednictvím operace [získat úlohu](/rest/api/storageimportexport/jobs) po dokončení úlohy. Další informace o formátu souboru manifestu jednotky najdete v tématu [Formát souboru manifestu služby Import/export](/previous-versions/azure/storage/common/storage-import-export-file-format-metadata-and-properties).  
  
Následující příklad ukazuje, jak spustit nástroj pro import/export Azure s parametry **/MANIFESTFILE** a **/CopyLogFile** :  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log /ManifestFile:G:\9WM35C3U.manifest  
```  
  
Následující příklad ukazuje soubor manifestu:  
  
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
  
Po dokončení procesu opravy nástroj přečte každý soubor, na který se odkazuje v souboru manifestu, a ověří integritu souboru pomocí hodnot hash MD5. V případě manifestu výše se přejdou tyto komponenty.  

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

Všechny součásti, které selžou ověření, budou staženy nástrojem a přepsány do stejného souboru na disku.  
  
## <a name="next-steps"></a>Další kroky
 
* [Nastavení nástroje Azure pro import/export](storage-import-export-tool-setup-v1.md)   
* [Příprava pevných disků pro úlohu importu](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)   
* [Kontrola stavu úlohy s použitím kopií souborů protokolu](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Oprava úlohy importu](storage-import-export-tool-repairing-an-import-job-v1.md)