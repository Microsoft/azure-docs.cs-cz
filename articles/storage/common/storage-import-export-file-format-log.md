---
title: Formát souborů protokolu služby Azure Import/Export | Dokumentace Microsoftu
description: Další informace o formátu souborů protokolu, který je vytvořen při provádění kroků pro úlohu importu/exportu služby.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: b842a80762989c34ae278a397cc49c088ff77fb2
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525514"
---
# <a name="azure-importexport-service-log-file-format"></a>Azure formát souborů protokolu služby Import/Export
Když služba Microsoft Azure Import/Export provede akci na disk jako součást úlohy importu nebo úlohy exportu, protokoly se zapisují do bloku, objekty BLOB v účtu úložiště přidruženého k této úlohy.  
  
Existují dva protokoly, které jde zapsat pomocí služby Import/Export:  
  
-   V protokolu chyb je vždy generována v případě chyby.  
  
-   Podrobný protokol není ve výchozím nastavení povolené, ale může být povoleno tak, že nastavíte `EnableVerboseLog` vlastnosti [úlohy umístit](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) nebo [aktualizovat vlastnosti úlohy](/rest/api/storageimportexport/jobs#Jobs_Update) operace.  
  
## <a name="log-file-location"></a>Umístění souboru protokolu  
Protokoly se zapisují do objekty BLOB v kontejneru nebo virtuálního adresáře určeného bloku `ImportExportStatesPath` nastavení, které můžete nastavit na `Put Job` operace. Umístění, do kterého se protokoly zapisují závisí na způsobu ověřování určena pro úlohy, společně s hodnota zadaná pro `ImportExportStatesPath`. Ověřování úlohy je možné zadat pomocí klíče účtu úložiště nebo kontejneru sdílený přístupový podpis (SAS).  
  
Název kontejneru nebo virtuální adresář může být výchozí název `waimportexport`, nebo jiný kontejner nebo název virtuálního adresáře, který zadáte.  
  
Následující tabulka uvádí dostupné možnosti:  
  
|Metoda ověřování|Hodnota `ImportExportStatesPath`– Element|Umístění objekty BLOB protokolů|  
|---------------------------|----------------------------------------------|---------------------------|  
|Klíč účtu úložiště|Výchozí hodnota|Kontejner s názvem `waimportexport`, což je výchozí kontejner. Příklad:<br /><br /> `https://myaccount.blob.core.windows.net/waimportexport`|  
|Klíč účtu úložiště|Uživatelem zadanou hodnotu|Kontejner s názvem uživatelem. Příklad:<br /><br /> `https://myaccount.blob.core.windows.net/mylogcontainer`|  
|Sdíleného přístupového podpisu kontejneru|Výchozí hodnota|Virtuální adresář s názvem `waimportexport`, což je výchozí název, pod podle sdíleného přístupového podpisu kontejneru.<br /><br /> Například pokud SAS zadaný pro úlohy je `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`, pak by byl umístění protokolu `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport`|  
|Sdíleného přístupového podpisu kontejneru|Uživatelem zadanou hodnotu|Virtuální adresář s názvem uživatelem pod podle sdíleného přístupového podpisu kontejneru.<br /><br /> Například pokud SAS zadaný pro úlohy je `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`, a je pojmenován zadaný virtuální adresář `mylogblobs`, by to být umístění protokolu `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport/mylogblobs`.|  
  
Adresa URL pro chyby a podrobné protokoly můžete načíst pomocí volání [Get Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) operace. Po dokončení zpracování jednotky jsou k dispozici protokoly.  
  
## <a name="log-file-format"></a>Formát souboru protokolu  
Formát pro oba protokoly je stejný: Objekt blob, který obsahuje XML popisy událostí, ke kterým došlo při kopírování objektů BLOB mezi pevného disku a účtu zákazníka.  
  
Podrobný protokol obsahuje úplné informace o stavu operace kopírování pro každý objekt blob (pro úlohy importu) nebo souboru (pro úlohu exportu), že obsahuje pouze informace pro objekty BLOB nebo soubory, které se vyskytly chyby při importu v protokolu chyb nebo Úloha exportu.  
  
Formát podrobný protokol je uveden níže. V protokolu chyb má stejnou strukturu, ale filtruje úspěšné operace.  

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
  
|– Element XML|Typ|Popis|  
|-----------------|----------|-----------------|  
|`DriveLog`|– Element XML|Představuje jednotku protokolu.|  
|`Version`|Atribut řetězců|Verze formátu protokolu.|  
|`DriveId`|Řetězec|Sériové číslo jednotky hardwaru.|  
|`Status`|Řetězec|Stav zpracování jednotky. Zobrazit `Drive Status Codes` tabulka níže pro další informace.|  
|`Blob`|Vnořené – XML element|Představuje objekt blob.|  
|`Blob/BlobPath`|Řetězec|Identifikátor URI objektu blob.|  
|`Blob/FilePath`|Řetězec|Relativní cesta k souboru na disku.|  
|`Blob/Snapshot`|DateTime|Verze snímku objektu blob pro úlohu exportu.|  
|`Blob/Length`|Integer|Celková délka objektu blob v bajtech.|  
|`Blob/LastModified`|DateTime|Datum/čas poslední změny objektu blob, pro úlohu exportu.|  
|`Blob/ImportDisposition`|Řetězec|Import dispozice objektu blob pro úlohu importu.|  
|`Blob/ImportDisposition/@Status`|Atribut řetězců|Stav importu dispozice.|  
|`PageRangeList`|Vnořené – XML element|Představuje seznam rozsahů stránek pro objekt blob stránky.|  
|`PageRange`|– Element XML|Představuje celou stránku.|  
|`PageRange/@Offset`|Atribut, celé číslo|Počáteční odsazení rozsahu stránek v objektu blob.|  
|`PageRange/@Length`|Atribut, celé číslo|Délka v bajtech rozsahu stránek.|  
|`PageRange/@Hash`|Atribut řetězců|Kódování Base16 hodnoty hash MD5 rozsahu stránek.|  
|`PageRange/@Status`|Atribut řetězců|Stav zpracování rozsahu stránek.|  
|`BlockList`|Vnořené – XML element|Představuje seznam bloků pro objekt blob bloku.|  
|`Block`|– Element XML|Představuje blok.|  
|`Block/@Offset`|Atribut, celé číslo|Počáteční posun v objektu blob bloku.|  
|`Block/@Length`|Atribut, celé číslo|Délku v bajtech bloku.|  
|`Block/@Id`|Atribut řetězců|ID bloku.|  
|`Block/@Hash`|Atribut řetězců|Kódování Base16 hodnota hash MD5 bloku.|  
|`Block/@Status`|Atribut řetězců|Stav zpracování bloku.|  
|`Metadata`|Vnořené – XML element|Představuje metadata objektu blob.|  
|`Metadata/@Status`|Atribut řetězců|Stav zpracování metadata objektu blob.|  
|`Metadata/GlobalPath`|Řetězec|Relativní cesta k souboru globální metadata.|  
|`Metadata/GlobalPath/@Hash`|Atribut řetězců|Kódování Base16 hodnota hash MD5 globální metadata souboru.|  
|`Metadata/Path`|Řetězec|Relativní cesta k souboru metadat.|  
|`Metadata/Path/@Hash`|Atribut řetězců|Kódování Base16 hodnota hash MD5 souboru metadat.|  
|`Properties`|Vnořené – XML element|Představuje vlastnosti objektu blob.|  
|`Properties/@Status`|Atribut řetězců|Stav zpracování vlastnosti objektu blob, například soubor nebyl nalezen, dokončeno.|  
|`Properties/GlobalPath`|Řetězec|Relativní cesta k souboru globální vlastnosti.|  
|`Properties/GlobalPath/@Hash`|Atribut řetězců|Kódování Base16 hodnota hash MD5 globální vlastnosti souboru.|  
|`Properties/Path`|Řetězec|Relativní cesta k vlastnosti souboru.|  
|`Properties/Path/@Hash`|Atribut řetězců|Kódování Base16 hodnota hash MD5 vlastnosti souboru.|  
|`Blob/Status`|Řetězec|Stav zpracování objektu blob.|  
  
# <a name="drive-status-codes"></a>Drive stavové kódy  
Následující tabulka uvádí stavové kódy pro zpracování jednotku.  
  
|Kód stavu|Popis|  
|-----------------|-----------------|  
|`Completed`|Na jednotce se dokončil zpracování bez chyb.|  
|`CompletedWithWarnings`|Na jednotce se dokončil zpracování s upozorněními v jedné nebo více objektů BLOB po importu potížemi zadaná pro objekty BLOB.|  
|`CompletedWithErrors`|Na jednotce se dokončil s chybami v jedné nebo více objektů BLOB nebo bloky dat.|  
|`DiskNotFound`|Na jednotce se nenašel žádný disk.|  
|`VolumeNotNtfs`|První datový svazek na disku není ve formátu systému souborů NTFS.|  
|`DiskOperationFailed`|Při provádění operací na jednotce došlo k neznámému selhání.|  
|`BitLockerVolumeNotFound`|Nebyl nalezen žádný svazek encryptable Bitlockeru.|  
|`BitLockerNotActivated`|Na svazku není zapnutý BitLocker.|  
|`BitLockerProtectorNotFound`|Ochranné zařízení klíče číselné heslo neexistuje na svazku.|  
|`BitLockerKeyInvalid`|Svazek nejde odemknout číselné heslo, které jsou k dispozici.|  
|`BitLockerUnlockVolumeFailed`|Došlo k neznámé chybě došlo při pokusu o tento svazek nejdříve odemknout.|  
|`BitLockerFailed`|Při provádění operací nástroje BitLocker došlo k neznámému selhání.|  
|`ManifestNameInvalid`|Název souboru manifestu je neplatný.|  
|`ManifestNameTooLong`|Název souboru manifestu je příliš dlouhý.|  
|`ManifestNotFound`|Soubor manifestu nebyl nalezen.|  
|`ManifestAccessDenied`|Přístup k souboru manifestu byl odepřen.|  
|`ManifestCorrupted`|Poškozený soubor manifestu (obsah se neshoduje s jeho hodnota hash).|  
|`ManifestFormatInvalid`|Obsah manifestu není v souladu s požadovaným formátem.|  
|`ManifestDriveIdMismatch`|ID disku v souboru manifestu neodpovídá jeden pro čtení z disku.|  
|`ReadManifestFailed`|Při čtení z manifestu došlo k chybě vstupně-výstupní operace disku.|  
|`BlobListFormatInvalid`|Výpis objektu blob exportu objektů blob není v souladu s požadovaným formátem.|  
|`BlobRequestForbidden`|Přístup k objektům BLOB v účtu úložiště je zakázán. To může být způsobeno klíč účtu úložiště nejsou platné nebo sdíleného přístupového podpisu kontejneru.|  
|`InternalError`|A při zpracování na jednotce došlo k vnitřní chybě.|  
  
## <a name="blob-status-codes"></a>Objekt BLOB stavové kódy  
Následující tabulka uvádí stavové kódy pro zpracování objektu blob.  
  
|Kód stavu|Popis|  
|-----------------|-----------------|  
|`Completed`|Objekt blob se dokončil zpracování bez chyb.|  
|`CompletedWithErrors`|Objekt blob se dokončil zpracování chyb v jedné nebo více rozsahů nebo bloky, metadat nebo vlastnosti.|  
|`FileNameInvalid`|Název souboru je neplatný.|  
|`FileNameTooLong`|Název souboru je příliš dlouhý.|  
|`FileNotFound`|Soubor nebyl nalezen.|  
|`FileAccessDenied`|Přístup k souboru byl odepřen.|  
|`BlobRequestFailed`|Žádost o službu objektu Blob pro přístup k objektu blob se nezdařilo.|  
|`BlobRequestForbidden`|Žádost o službu objektu Blob pro přístup k objektu blob je zakázaná. To může být způsobeno klíč účtu úložiště nejsou platné nebo sdíleného přístupového podpisu kontejneru.|  
|`RenameFailed`|Nepovedlo se přejmenovat objekt blob (pro úlohy importu) nebo souboru (pro úlohu exportu).|  
|`BlobUnexpectedChange`|Neočekávaná změna došlo k chybě s objektem blob (pro úlohu exportu).|  
|`LeasePresent`|Existuje zapůjčení v objektu blob.|  
|`IOFailed`|Při zpracování objektu blob došlo k chybě vstupně-výstupních operací disku nebo sítě.|  
|`Failed`|Při zpracování objektu blob došlo k neznámé chybě.|  
  
## <a name="import-disposition-status-codes"></a>Import dispozice stavové kódy  
Následující tabulka uvádí stavové kódy pro řešení importu dispozice.  
  
|Kód stavu|Popis|  
|-----------------|-----------------|  
|`Created`|Vytvoření objektu blob.|  
|`Renamed`|Objekt blob se přejmenoval na přejmenování import dispozice. `Blob/BlobPath` Prvek obsahuje identifikátor URI pro přejmenovaného objektu blob.|  
|`Skipped`|Objekt blob se přeskočila na `no-overwrite` importovat dispozice.|  
|`Overwritten`|Objekt blob má přepsat existující objekt blob na `overwrite` importovat dispozice.|  
|`Cancelled`|Předchozí selhání byla zastavena další zpracování import dispozice.|  
  
## <a name="page-rangeblock-status-codes"></a>Stránka rozsah/blok stavové kódy  
Následující tabulka uvádí stavové kódy pro zpracování stránky rozsah nebo blok.  
  
|Kód stavu|Popis|  
|-----------------|-----------------|  
|`Completed`|Stránka rozsah nebo blok bylo dokončeno zpracování bez chyb.|  
|`Committed`|Blok byl nejdříve zapsán, ale v bloku úplný seznam protože jiných bloků se nezdařily, nebo vložit samotný seznam úplný blok nedošlo k chybě.|  
|`Uncommitted`|Blok je nahraný, ale nikoli potvrdit.|  
|`Corrupted`|Stránka rozsah nebo blok je poškozený (obsah se neshoduje s jeho hodnota hash).|  
|`FileUnexpectedEnd`|Zjistil se neočekávaný konec souboru.|  
|`BlobUnexpectedEnd`|Zjistil se neočekávaný konec objektu blob.|  
|`BlobRequestFailed`|Žádost o službu objektu Blob pro přístup k rozsahu stránka nebo blok se nezdařilo.|  
|`IOFailed`|Při zpracování stránky rozsah nebo blok došlo k chybě vstupně-výstupních operací disku nebo sítě.|  
|`Failed`|Při zpracování stránky rozsah nebo blok došlo k neznámému selhání.|  
|`Cancelled`|Předchozí selhání byla zastavena, další zpracování stránky rozsah nebo blok.|  
  
## <a name="metadata-status-codes"></a>Metadata stavové kódy  
Následující tabulka uvádí stavové kódy pro zpracování metadata objektu blob.  
  
|Kód stavu|Popis|  
|-----------------|-----------------|  
|`Completed`|Metadata se dokončil zpracování bez chyb.|  
|`FileNameInvalid`|Název souboru metadat je neplatný.|  
|`FileNameTooLong`|Název souboru metadat je příliš dlouhý.|  
|`FileNotFound`|Nenašel se soubor metadat.|  
|`FileAccessDenied`|Přístup k souboru metadat byl odepřen.|  
|`Corrupted`|Poškozený soubor metadat (obsah se neshoduje s jeho hodnota hash).|  
|`XmlReadFailed`|Metadata obsah není v souladu s požadovaným formátem.|  
|`XmlWriteFailed`|Zápis metadat XML se nezdařila.|  
|`BlobRequestFailed`|Přístup k metadatům žádosti o službu objektu Blob se nezdařilo.|  
|`IOFailed`|Při zpracování metadat došlo k chybě vstupně-výstupních operací disku nebo sítě.|  
|`Failed`|Při zpracování metadat došlo k neznámému selhání.|  
|`Cancelled`|Předchozí selhání byla zastavena, další zpracování metadat.|  
  
## <a name="properties-status-codes"></a>Vlastnosti stavové kódy  
Následující tabulka uvádí stavové kódy pro zpracování vlastnosti objektu blob.  
  
|Kód stavu|Popis|  
|-----------------|-----------------|  
|`Completed`|Vlastnosti dokončení zpracování bez chyb.|  
|`FileNameInvalid`|Název vlastnosti souboru je neplatný.|  
|`FileNameTooLong`|Název vlastnosti souboru je příliš dlouhý.|  
|`FileNotFound`|Soubor vlastností se nenašel.|  
|`FileAccessDenied`|Přístup k vlastnosti souboru byl odepřen.|  
|`Corrupted`|Poškozený soubor vlastností (obsah se neshoduje s jeho hodnota hash).|  
|`XmlReadFailed`|Vlastnosti obsah není v souladu s požadovaným formátem.|  
|`XmlWriteFailed`|Zápis vlastností XML se nezdařila.|  
|`BlobRequestFailed`|Žádost o službu objektu Blob pro přístup k vlastnosti se nezdařila.|  
|`IOFailed`|Při zpracování vlastnosti došlo k chybě vstupně-výstupních operací disku nebo sítě.|  
|`Failed`|Při zpracování vlastnosti došlo k neznámé chybě.|  
|`Cancelled`|Předchozí selhání byla zastavena, další zpracování z vlastností.|  
  
## <a name="sample-logs"></a>Ukázky protokolů  
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
  
Odpovídající protokol chyb je uveden níže.  
  
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

 V protokolu chyb použijte pro úlohy importu obsahuje chybu, soubor se nenašel na jednotce importu. Všimněte si, že je stav dalších součástí `Cancelled`.  
  
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

Následující protokol chyb pro úlohy exportu označuje, že obsah objektu blob se úspěšně zapsala do jednotky, ale, že došlo k chybě při exportu vlastnosti objektu blob.  
  
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
  
## <a name="next-steps"></a>Další postup
 
* [REST API pro Import/Export úložiště](/rest/api/storageimportexport/)
