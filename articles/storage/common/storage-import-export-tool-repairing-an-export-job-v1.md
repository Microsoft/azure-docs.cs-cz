---
title: Oprava úlohy exportu importu a exportu Azure - v1 | Dokumenty společnosti Microsoft
description: Zjistěte, jak opravit úlohu exportu, která byla vytvořena a spuštěna pomocí služby Import/Export Azure.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: b2ba30bddfc6364c79e1bb01d30cde63b261a07f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978011"
---
# <a name="repairing-an-export-job"></a>Oprava úlohy exportu
Po dokončení úlohy exportu můžete spustit místní nástroj Microsoft Azure Import/Export Tool na:  
  
1.  Stáhněte si všechny soubory, které služba Import a export Azure nemohla exportovat.  
  
2.  Ověřte, zda byly soubory na jednotce správně exportovány.  
  
Chcete-li tuto funkci používat, musíte mít připojení k Azure Storage.  
  
Příkaz pro opravu úlohy importu je **RepairExport**.

## <a name="repairexport-parameters"></a>Parametry RepairExport

Následující parametry lze zadat s **RepairExport**:  
  
|Parametr|Popis|  
|---------------|-----------------|  
|**/r:soubor opravy <\>**|Povinná hodnota. Cesta k souboru opravy, který sleduje průběh opravy a umožňuje pokračovat v přerušené opravě. Každá jednotka musí mít jeden a pouze jeden soubor opravy. Při spuštění opravy pro danou jednotku, předáte v cestě k souboru opravy, který ještě neexistuje. Chcete-li pokračovat v přerušené opravě, měli byste předat název existujícího souboru opravy. Vždy musí být zadán soubor opravy odpovídající cílové jednotce.|  
|**/logdir:<LogDirectory\>**|Nepovinný parametr. Adresář protokolu. Podrobné soubory protokolu budou zapsány do tohoto adresáře. Pokud není zadán žádný adresář protokolu, bude jako adresář protokolu použit aktuální adresář.|  
|**/d:<TargetDirectory\>**|Povinná hodnota. Adresář k ověření a opravě. Obvykle se jedná o kořenový adresář exportní jednotky, ale může se také jedná o sdílenou síťovou složku obsahující kopii exportovaných souborů.|  
|**/bk:<nástroj BitLockerKey\>**|Nepovinný parametr. Chcete-li, aby nástroj odemkl šifrovaný soubor, ve kterém jsou exportované soubory uloženy, měli byste zadat klíč nástroje BitLocker.|  
|**/sn:<StorageAccountName\>**|Povinná hodnota. Název účtu úložiště pro úlohu exportu.|  
|**/sk:<AccountAccountKey\>**|**Povinné,** pokud a pouze v případě, že není zadán kontejner SAS. Klíč účtu pro účet úložiště pro úlohu exportu.|  
|**/csas:<Kontejnery\>**|**Povinné,** pokud a pouze v případě, že není zadán klíč účtu úložiště. Kontejner SAS pro přístup k objektům BLOB přidružených k úloze exportu.|  
|**/CopyLogFile:<DriveCopyLogFile\>**|Povinná hodnota. Cesta k souboru protokolu kopírování jednotky. Soubor je generován službou Import a export Windows Azure a lze jej stáhnout z úložiště objektů blob přidruženého k úloze. Soubor protokolu kopírování obsahuje informace o neúspěšných objektech BLOB nebo souborech, které mají být opraveny.|  
|**/ManifestFile:<DriveManifestFile\>**|Nepovinný parametr. Cesta k souboru manifestu exportní jednotky. Tento soubor je generován službou Import/Export Windows Azure a uložen na exportní jednotce a volitelně v objektu blob v účtu úložiště přidruženém k úloze.<br /><br /> Obsah souborů na exportní jednotce bude ověřen pomocí hash MD5 obsažených v tomto souboru. Všechny soubory, které jsou určeny k poškození, budou staženy a přepsány do cílových adresářů.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>Oprava neúspěšných exportů pomocí režimu RepairExport  
Nástroj pro import a export Azure můžete použít ke stažení souborů, které se nepodařilo exportovat. Soubor protokolu kopírování bude obsahovat seznam souborů, které se nepodařilo exportovat.  
  
Příčiny selhání exportu zahrnují následující možnosti:  
  
-   Poškozené pohony  
  
-   Klíč účtu úložiště se změnil během procesu převodu  
  
Chcete-li nástroj spustit v režimu **RepairExport,** musíte nejprve připojit jednotku obsahující exportované soubory k počítači. Dále spusťte nástroj pro import a export Azure a `/d` určete cestu k této jednotce s parametrem. Je také třeba zadat cestu ke staženému souboru protokolu kopírování jednotky. Následující příklad příkazového řádku níže spustí nástroj pro opravu všech souborů, které se nepodařilo exportovat:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
Následuje příklad souboru protokolu kopírování, který ukazuje, že jeden blok v objektu blob se nepodařilo exportovat:  
  
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
  
Soubor protokolu kopírování označuje, že došlo k chybě, když služba Import a export Windows Azure stahovala jeden z bloků objektu blob do souboru na exportní jednotce. Ostatní součásti souboru byly úspěšně staženy a délka souboru byla správně nastavena. V takovém případě nástroj otevře soubor na jednotce, stáhne blok z účtu úložiště a zapíše jej do rozsahu souborů od posunu 65536 s délkou 65536.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>Ověření obsahu jednotky pomocí funkce RepairExport  
Můžete také použít Azure Import/Export s **RepairExport** možnost ověřit obsah na jednotce jsou správné. Soubor manifestu na každé exportní jednotce obsahuje md5s pro obsah jednotky.  
  
Služba Import a export Azure může také uložit soubory manifestu do účtu úložiště během procesu exportu. Umístění souborů manifestu je k dispozici prostřednictvím operace [Získat úlohu](/rest/api/storageimportexport/jobs) po dokončení úlohy. Další informace o formátu souboru manifestu jednotky naleznete v tématu [Import/export služby Manifest File Format.](storage-import-export-file-format-metadata-and-properties.md)  
  
Následující příklad ukazuje, jak spustit nástroj pro import a export Azure s parametry **/ManifestFile** a **/CopyLogFile:**  
  
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
  
Po dokončení procesu opravy nástroj pročte každý soubor odkazovaný v souboru manifestu a ověří integritu souboru pomocí hash MD5. Pro výše uvedený manifest projde následujícími součástmi.  

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

Všechny součásti, které ověření nepodaří, budou nástrojem staženy a přepsány do stejného souboru na jednotce.  
  
## <a name="next-steps"></a>Další kroky
 
* [Nastavení nástroje pro import a export Azure](storage-import-export-tool-setup-v1.md)   
* [Příprava pevných disků pro úlohu importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Kontrola stavu úlohy s použitím kopií souborů protokolu](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Oprava úlohy importu](storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Řešení potíží s nástrojem Azure pro import/export](storage-import-export-tool-troubleshooting-v1.md)
