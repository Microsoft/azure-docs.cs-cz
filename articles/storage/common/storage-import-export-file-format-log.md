---
title: Formát souboru protokolu Azure Import/Export | Microsoft Docs
description: Další informace o formátu souborů protokolu, který je vytvořen při provádění kroků pro úlohu importu/exportu služby.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 38cc16bd-ad55-4625-9a85-e1726c35fd1b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 16234ccaf13ce1d85cfd207ed4734e683070faa6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874098"
---
# <a name="azure-importexport-service-log-file-format"></a>Formát souboru protokolu služby sady Azure Import/Export
Když službu Microsoft Azure Import/Export provede akci na disku jako součást úlohy importu nebo úlohy exportu, protokoly zapisují na blok objektů BLOB v účtu úložiště přidruženého k této úlohy.  
  
Existují dva protokoly, které lze zapisovat pomocí služby importu a exportu:  
  
-   V případě chyby vždy generování protokolu chyb.  
  
-   Úplné protokolování není ve výchozím nastavení povolené, ale lze ho zapnout nastavením `EnableVerboseLog` vlastnost [Put úlohy](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) nebo [vlastnosti úlohy aktualizace](/rest/api/storageimportexport/jobs#Jobs_Update) operaci.  
  
## <a name="log-file-location"></a>Umístění souboru protokolu  
Protokoly se zapisují do bloku v kontejneru nebo virtuální adresář zadaný `ImportExportStatesPath` nastavení, které můžete nastavit `Put Job` operaci. Umístění, do které se zapisují protokoly závisí na tom, jak je ověřování zadaná pro úlohu, společně s hodnota zadaná pro `ImportExportStatesPath`. Ověření úlohy je možné zadat prostřednictvím klíč účtu úložiště nebo kontejneru SAS (sdílený přístupový podpis).  
  
Název kontejneru nebo virtuální adresář může být výchozí název `waimportexport`, nebo jiný kontejner nebo název virtuálního adresáře, který určíte.  
  
Následující tabulka uvádí možné možnosti:  
  
|Metoda ověřování|Hodnota `ImportExportStatesPath`– Element|Umístění protokolu objektů BLOB|  
|---------------------------|----------------------------------------------|---------------------------|  
|Klíče účtu úložiště.|Výchozí hodnota|Kontejner s názvem `waimportexport`, což je výchozí kontejner. Například:<br /><br /> `https://myaccount.blob.core.windows.net/waimportexport`|  
|Klíče účtu úložiště.|Uživatelem zadanou hodnotu|Kontejner s názvem uživatelem. Například:<br /><br /> `https://myaccount.blob.core.windows.net/mylogcontainer`|  
|Sdíleného přístupového podpisu kontejneru|Výchozí hodnota|Virtuální adresář s názvem `waimportexport`, což je výchozí název, pod kontejnerem zadaný v SAS.<br /><br /> Například pokud SAS zadaná pro úlohu je `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`, pak by byl umístění protokolu`https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport`|  
|Sdíleného přístupového podpisu kontejneru|Uživatelem zadanou hodnotu|Virtuální adresář s názvem uživatelem pod kontejnerem zadaný v SAS.<br /><br /> Například pokud SAS zadaná pro úlohu je `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`, a názvem zadaný virtuální adresář `mylogblobs`, pak by byl umístění protokolu `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport/mylogblobs`.|  
  
Je-li získat adresu URL pro podrobné protokoly a chyba pomocí volání [Get Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) operaci. Protokoly jsou k dispozici po dokončení zpracování jednotky.  
  
## <a name="log-file-format"></a>Formát souboru protokolu  
Formát pro oba protokoly je stejný: Objekt blob, který obsahuje XML popisy událostí, které nastaly při kopírování objekty BLOB mezi pevného disku a účtu zákazníka.  
  
Úplné protokolování obsahuje kompletní informace o stavu operace kopírování pro každý objekt blob (pro úlohy importu) nebo soubor (pro úlohy exportu), zatímco v protokolu chyb obsahuje pouze informace pro objekty BLOB nebo soubory, které se vyskytly chyby při importu nebo exportu úloze.  
  
Formát podrobného protokolování jsou uvedeny níže. V protokolu chyb má stejnou strukturu, ale filtruje úspěšné operace.  

```xml
<DriveLog Version="2014-11-01">  
  <DriveId>drive-id</DriveId>  
  [<Blob Status="blob-status">  
   <BlobPath>blob-path</BlobPath>  
   <FilePath>file-path</FilePath>  
   [<Snapshot>snapshot</Snapshot>]  
   <Length>length</Length>  
   [<LastModified>last-modified</LastModified>]  
   [<ImportDisposition Status="import-disposition-status">import-disposition</ImportDisposition>]  
   [page-range-list-or-block-list]  
   [metadata-status]  
   [properties-status]  
  </Blob>]  
  [<Blob>  
    . . .  
  </Blob>]  
  <Status>drive-status</Status>  
</DriveLog>  
  
page-range-list-or-block-list ::= 
  page-range-list | block-list  
  
page-range-list ::=   
<PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       [Hash="md5-hash"] Status="page-range-status"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       [Hash="md5-hash"] Status="page-range-status"/>]  
</PageRangeList>  
  
block-list ::=  
<BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       [Hash="md5-hash"] Status="block-status"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       [Hash="md5-hash"] Status="block-status"/>]  
</BlockList>  
  
metadata-status ::=  
<Metadata Status="metadata-status">  
   [<GlobalPath Hash="md5-hash">global-metadata-file-path</GlobalPath>]  
   [<Path Hash="md5-hash">metadata-file-path</Path>]  
</Metadata>  
  
properties-status ::=  
<Properties Status="properties-status">  
   [<GlobalPath Hash="md5-hash">global-properties-file-path</GlobalPath>]  
   [<Path Hash="md5-hash">properties-file-path</Path>]  
</Properties>  
```

Následující tabulka popisuje prvky souboru protokolu.  
  
|XML Element|Typ|Popis|  
|-----------------|----------|-----------------|  
|`DriveLog`|XML Element|Představuje jednotku protokolu.|  
|`Version`|Atribut, řetězec|Verze formátu protokolu.|  
|`DriveId`|Řetězec|Sériové číslo hardwaru na jednotku.|  
|`Status`|Řetězec|Stav zpracování jednotky. Najdete v článku `Drive Status Codes` tabulky níže Další informace.|  
|`Blob`|Vnořené – element XML|Představuje objekt blob.|  
|`Blob/BlobPath`|Řetězec|Identifikátor URI objektu blob.|  
|`Blob/FilePath`|Řetězec|Relativní cesta k souboru na disku.|  
|`Blob/Snapshot`|Data a času|Snímek aktuální verze objektu blob pro úlohu exportu.|  
|`Blob/Length`|Integer|Celková délka objektu blob v bajtech.|  
|`Blob/LastModified`|Data a času|Datum a čas poslední změny objektu blob, pro úlohu exportu.|  
|`Blob/ImportDisposition`|Řetězec|Import dispozice objektu blob pro úlohu importu.|  
|`Blob/ImportDisposition/@Status`|Atribut, řetězec|Stav importu dispozice.|  
|`PageRangeList`|Vnořené – element XML|Představuje seznam rozsahů stránek pro objekt blob stránky.|  
|`PageRange`|XML element|Představuje rozsahu stránek.|  
|`PageRange/@Offset`|Atribut, celé číslo|Počáteční odsazení rozsahu stránek v objektu blob.|  
|`PageRange/@Length`|Atribut, celé číslo|Délka v bajtech rozsahu stránek.|  
|`PageRange/@Hash`|Atribut, řetězec|Kódováním Base16 hodnotu hash MD5 rozsahu stránek.|  
|`PageRange/@Status`|Atribut, řetězec|Stav zpracování rozsahu stránek.|  
|`BlockList`|Vnořené – element XML|Představuje seznam bloků pro objekt blob bloku.|  
|`Block`|XML element|Představuje blok.|  
|`Block/@Offset`|Atribut, celé číslo|Počáteční odsazení v objektu blob bloku.|  
|`Block/@Length`|Atribut, celé číslo|Délka v bajtech bloku.|  
|`Block/@Id`|Atribut, řetězec|ID bloku.|  
|`Block/@Hash`|Atribut, řetězec|Kódováním Base16 hodnota hash MD5 bloku.|  
|`Block/@Status`|Atribut, řetězec|Stav zpracování bloku.|  
|`Metadata`|Vnořené – element XML|Představuje metadata objektu blob.|  
|`Metadata/@Status`|Atribut, řetězec|Stav zpracování metadata objektu blob.|  
|`Metadata/GlobalPath`|Řetězec|Relativní cesta k souboru globální metadat.|  
|`Metadata/GlobalPath/@Hash`|Atribut, řetězec|Kódováním Base16 hodnota hash MD5 globální metadata souboru.|  
|`Metadata/Path`|Řetězec|Relativní cesta k souboru metadat.|  
|`Metadata/Path/@Hash`|Atribut, řetězec|Kódováním Base16 hodnota hash MD5 souboru metadat.|  
|`Properties`|Vnořené – element XML|Představuje vlastnosti objektů blob.|  
|`Properties/@Status`|Atribut, řetězec|Stav zpracování objektu blob vlastnosti, například soubor nebyl nalezen, byla dokončena.|  
|`Properties/GlobalPath`|Řetězec|Relativní cesta k souboru globální vlastnosti.|  
|`Properties/GlobalPath/@Hash`|Atribut, řetězec|Kódováním Base16 hodnota hash MD5 globální vlastnosti souboru.|  
|`Properties/Path`|Řetězec|Relativní cesta k souboru vlastnosti.|  
|`Properties/Path/@Hash`|Atribut, řetězec|Kódováním Base16 hodnota hash MD5 souboru vlastnosti.|  
|`Blob/Status`|Řetězec|Stav zpracování objektu blob.|  
  
# <a name="drive-status-codes"></a>Jednotka stavové kódy  
Následující tabulka uvádí stavové kódy pro zpracování na jednotku.  
  
|Stavový kód|Popis|  
|-----------------|-----------------|  
|`Completed`|Jednotka dokončil zpracování bez chyb.|  
|`CompletedWithWarnings`|Jednotka dokončení zpracování se upozornění v jedné nebo více objektů blob na import potížemi, zadaná pro objekty BLOB.|  
|`CompletedWithErrors`|Jednotka bylo dokončeno s chybami v jedné nebo více objektů BLOB nebo bloků.|  
|`DiskNotFound`|Žádný disk nachází na jednotce.|  
|`VolumeNotNtfs`|První datový svazek na disku není ve formátu systému souborů NTFS.|  
|`DiskOperationFailed`|Při provádění operací na jednotce došlo k neznámé chybě.|  
|`BitLockerVolumeNotFound`|Nebyla nalezena žádná encryptable svazku Bitlockeru.|  
|`BitLockerNotActivated`|Nástroj BitLocker není povolen ve svazku.|  
|`BitLockerProtectorNotFound`|Ochranné zařízení klíče číselné heslo neexistuje na svazku.|  
|`BitLockerKeyInvalid`|Číselné heslo zadané nelze svazek odemknout.|  
|`BitLockerUnlockVolumeFailed`|Došlo k neznámé chybě došlo při pokusu o svazek odemknout.|  
|`BitLockerFailed`|Při provádění operací se BitLocker došlo k neznámé chybě.|  
|`ManifestNameInvalid`|Název souboru manifestu je neplatný.|  
|`ManifestNameTooLong`|Název souboru manifestu je příliš dlouhý.|  
|`ManifestNotFound`|Soubor manifestu nebyl nalezen.|  
|`ManifestAccessDenied`|Přístup k souboru manifestu byl odepřen.|  
|`ManifestCorrupted`|Je poškozený soubor manifestu (obsah neodpovídá jeho hash).|  
|`ManifestFormatInvalid`|Obsah manifestu neodpovídá požadovaný formát.|  
|`ManifestDriveIdMismatch`|ID disku v souboru manifestu neodpovídá jeden pro čtení z disku.|  
|`ReadManifestFailed`|Při čtení z manifestu došlo k chybě vstupně-výstupních operací disku.|  
|`BlobListFormatInvalid`|Objekt blob seznamu objektů blob export neodpovídá požadovaný formát.|  
|`BlobRequestForbidden`|Přístup k objektům BLOB v účtu úložiště je zakázán. Důvodem může být klíč účtu úložiště nejsou platné nebo sdíleného přístupového podpisu kontejneru.|  
|`InternalError`|A při zpracování jednotce došlo k vnitřní chybě.|  
  
## <a name="blob-status-codes"></a>Objekt BLOB stavové kódy  
Následující tabulka uvádí stavové kódy pro zpracování objektu blob.  
  
|Stavový kód|Popis|  
|-----------------|-----------------|  
|`Completed`|Objekt blob se dokončil zpracování bez chyb.|  
|`CompletedWithErrors`|Objekt blob se dokončil zpracování s chybami v jedné nebo více rozsahů stránek nebo bloky, metadata nebo vlastnosti.|  
|`FileNameInvalid`|Název souboru je neplatný.|  
|`FileNameTooLong`|Název souboru je příliš dlouhý.|  
|`FileNotFound`|Soubor nebyl nalezen.|  
|`FileAccessDenied`|Přístup k souboru byl odepřen.|  
|`BlobRequestFailed`|Žádost o služby objektů Blob pro přístup k objektu blob se nezdařilo.|  
|`BlobRequestForbidden`|Žádost o služby objektů Blob pro přístup k objektu blob je zakázáno. Důvodem může být klíč účtu úložiště nejsou platné nebo sdíleného přístupového podpisu kontejneru.|  
|`RenameFailed`|Přejmenujte soubor (pro úlohy exportu) nebo objektu blob (pro úlohy importu) se nezdařilo.|  
|`BlobUnexpectedChange`|S objektem blob (pro úlohy exportu) došlo k neočekávané chování.|  
|`LeasePresent`|Není přítomen v objektu blob zapůjčení.|  
|`IOFailed`|Při zpracování objektu blob došlo k chybě vstupně-výstupních operací disku nebo v síti.|  
|`Failed`|Při zpracování objektu blob došlo k neznámé chybě.|  
  
## <a name="import-disposition-status-codes"></a>Import dispozice stavové kódy  
Následující tabulka uvádí stavové kódy pro řešení importu dispozice.  
  
|Stavový kód|Popis|  
|-----------------|-----------------|  
|`Created`|Vytvořil se objekt blob.|  
|`Renamed`|Objekt blob byl přejmenován na přejmenování import dispozice. `Blob/BlobPath` Element obsahuje identifikátor URI objektu blob přejmenovat.|  
|`Skipped`|Objekt blob byl vynechán za `no-overwrite` importovat dispozice.|  
|`Overwritten`|Objekt blob má přepsat existující objekt blob za `overwrite` importovat dispozice.|  
|`Cancelled`|Předchozí selhání byla zastavena, další zpracování import dispozice.|  
  
## <a name="page-rangeblock-status-codes"></a>Stránka rozsah/blok stavové kódy  
Následující tabulka uvádí stavové kódy pro zpracování stránky rozsah nebo blok.  
  
|Stavový kód|Popis|  
|-----------------|-----------------|  
|`Completed`|Stránka rozsah nebo blok dokončení zpracování bez chyb.|  
|`Committed`|Blok potvrzeny, ale v bloku úplný seznam protože jiné bloky se nezdařily, nebo put samotný seznam úplné bloku nedošlo k chybě.|  
|`Uncommitted`|Blok je odeslat, ale nikoli potvrdit.|  
|`Corrupted`|Stránka rozsah nebo blok je poškozený (obsah neodpovídá jeho hash).|  
|`FileUnexpectedEnd`|Byl nalezen neočekávaný konec souboru.|  
|`BlobUnexpectedEnd`|Byl nalezen neočekávaný konec objektu blob.|  
|`BlobRequestFailed`|Žádost o služby objektů Blob pro přístup k rozsahu stránek nebo bloku selhal.|  
|`IOFailed`|Během zpracování stránky rozsah nebo blok došlo k chybě vstupně-výstupních operací disku nebo v síti.|  
|`Failed`|Během zpracování stránky rozsah nebo blok došlo k neznámé chybě.|  
|`Cancelled`|Předchozí selhání byla zastavena, další zpracování rozsahu stránek nebo bloku.|  
  
## <a name="metadata-status-codes"></a>Metadata stavové kódy  
Následující tabulka uvádí stavové kódy pro zpracování metadata objektu blob.  
  
|Stavový kód|Popis|  
|-----------------|-----------------|  
|`Completed`|Metadata se dokončil zpracování bez chyb.|  
|`FileNameInvalid`|Název souboru metadat je neplatná.|  
|`FileNameTooLong`|Název souboru metadat je příliš dlouhý.|  
|`FileNotFound`|Soubor metadat nebyl nalezen.|  
|`FileAccessDenied`|Přístup k souboru metadat byl odepřen.|  
|`Corrupted`|Je poškozený soubor metadat (obsah neodpovídá jeho hash).|  
|`XmlReadFailed`|Metadata obsahu neodpovídá požadovaný formát.|  
|`XmlWriteFailed`|Zápis metadata XML se nezdařila.|  
|`BlobRequestFailed`|Žádost o služby objektů Blob přístup k metadatům se nezdařilo.|  
|`IOFailed`|Při zpracování metadat došlo k chybě vstupně-výstupních operací disku nebo v síti.|  
|`Failed`|Při zpracování metadat došlo k neznámé chybě.|  
|`Cancelled`|Předchozí selhání byla zastavena, další zpracování metadat.|  
  
## <a name="properties-status-codes"></a>Vlastnosti stavové kódy  
Následující tabulka uvádí stavové kódy pro zpracování vlastnosti objektů blob.  
  
|Stavový kód|Popis|  
|-----------------|-----------------|  
|`Completed`|Vlastnosti dokončení zpracování bez chyb.|  
|`FileNameInvalid`|Název vlastnosti souboru je neplatný.|  
|`FileNameTooLong`|Název vlastnosti souboru je příliš dlouhý.|  
|`FileNotFound`|Vlastnosti soubor nebyl nalezen.|  
|`FileAccessDenied`|Byl odepřen přístup k vlastnosti souboru.|  
|`Corrupted`|Vlastnosti soubor je poškozený (obsah neodpovídá jeho hash).|  
|`XmlReadFailed`|Vlastnosti obsahu neodpovídá požadovaný formát.|  
|`XmlWriteFailed`|Zápis vlastností XML se nezdařila.|  
|`BlobRequestFailed`|Žádost o služby objektů Blob pro přístup k vlastnostem se nezdařilo.|  
|`IOFailed`|Při zpracování vlastnosti došlo k chybě vstupně-výstupních operací disku nebo v síti.|  
|`Failed`|Při zpracování vlastnosti došlo k neznámé chybě.|  
|`Cancelled`|Předchozí selhání byla zastavena, další zpracování vlastností.|  
  
## <a name="sample-logs"></a>Ukázka protokoly  
Následuje příklad podrobného protokolování.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveLog Version="2014-11-01">  
    <DriveId>WD-WMATV123456</DriveId>  
    <Blob Status="Completed">  
       <BlobPath>pictures/bob/wild/desert.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\wild\desert.jpg</FilePath>  
       <Length>98304</Length>  
       <ImportDisposition Status="Created">overwrite</ImportDisposition>  
       <BlockList>  
          <Block Offset="0" Length="65536" Id="AAAAAA==" Hash=" 9C8AE14A55241F98533C4D80D85CDC68" Status="Completed"/>  
          <Block Offset="65536" Length="32768" Id="AQAAAA==" Hash=" DF54C531C9B3CA2570FDDDB3BCD0E27D" Status="Completed"/>  
       </BlockList>  
       <Metadata Status="Completed">  
          <GlobalPath Hash=" E34F54B7086BCF4EC1601D056F4C7E37">\Users\bob\Pictures\wild\metadata.xml</GlobalPath>  
       </Metadata>  
    </Blob>  
    <Blob Status="CompletedWithErrors">  
       <BlobPath>pictures/bob/animals/koala.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\animals\koala.jpg</FilePath>  
       <Length>163840</Length>  
       <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
       <PageRangeList>  
          <PageRange Offset="0" Length="65536" Hash="19701B8877418393CB3CB567F53EE225" Status="Completed"/>  
          <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted"/>  
          <PageRange Offset="131072" Length="4096" Hash="9BA552E1C3EEAFFC91B42B979900A996" Status="Completed"/>  
       </PageRangeList>  
       <Properties Status="Completed">  
          <Path Hash="38D7AE80653F47F63C0222FEE90EC4E7">\Users\bob\Pictures\animals\koala.jpg.properties</Path>  
       </Properties>  
    </Blob>  
    <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
Odpovídající protokolu chyb jsou uvedeny níže.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveLog Version="2014-11-01">  
    <DriveId>WD-WMATV6965824</DriveId>  
    <Blob Status="CompletedWithErrors">  
       <BlobPath>pictures/bob/animals/koala.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\animals\koala.jpg</FilePath>  
       <Length>163840</Length>  
       <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
       <PageRangeList>  
          <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted"/>  
       </PageRangeList>  
    </Blob>  
    <Status>CompletedWithErrors</Status>  
</DriveLog>  
```

 Úlohy importu v protokolu chyb postupujte podle obsahuje chybu o soubor nebyl nalezen na disku pro import. Všimněte si, že je stav následující součásti `Cancelled`.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog Version="2014-11-01">  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="FileNotFound">  
    <BlobPath>pictures/animals/koala.jpg</BlobPath>  
    <FilePath>\animals\koala.jpg</FilePath>  
    <Length>30310</Length>  
    <ImportDisposition Status="Cancelled">rename</ImportDisposition>  
    <BlockList>  
      <Block Offset="0" Length="6062" Id="MD5/cAzn4h7VVSWXf696qp5Uaw==" Hash="700CE7E21ED55525977FAF7AAA9E546B" Status="Cancelled" />  
      <Block Offset="6062" Length="6062" Id="MD5/PEnGwYOI8LPLNYdfKr7kAg==" Hash="3C49C6C18388F0B3CB35875F2ABEE402" Status="Cancelled" />  
      <Block Offset="12124" Length="6062" Id="MD5/FG4WxqfZKuUWZ2nGTU2qVA==" Hash="146E16C6A7D92AE5166769C64D4DAA54" Status="Cancelled" />  
      <Block Offset="18186" Length="6062" Id="MD5/ZzibNDzr3IRBQENRyegeXQ==" Hash="67389B343CEBDC8441404351C9E81E5D" Status="Cancelled" />  
      <Block Offset="24248" Length="6062" Id="MD5/ZzibNDzr3IRBQENRyegeXQ==" Hash="67389B343CEBDC8441404351C9E81E5D" Status="Cancelled" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```

Následující v protokolu chyb úlohy exportu označuje, že obsah objektu blob byla úspěšně zapsána na jednotku, ale, že došlo k chybě při exportu vlastnosti objektu blob.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog Version="2014-11-01">  
  <DriveId>9WM35C3U</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
    <FilePath>\pictures\wild\canyon.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList />  
    <Properties Status="Failed" />  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
## <a name="next-steps"></a>Další kroky
 
* [REST API pro Import/Export úložiště](/rest/api/storageimportexport/)
