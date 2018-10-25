---
title: Příprava pevných disků pro úlohu importu Azure Import/Export | Dokumentace Microsoftu
description: Zjistěte, jak pro přípravu pevných disků pomocí nástroje WAImportExport k vytvoření úlohy importu pro službu Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/29/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 0be0d6c9613b4160a6c4c5cc3b63336fcf67d5c2
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025614"
---
# <a name="preparing-hard-drives-for-an-import-job"></a>Příprava pevných disků pro úlohu importu

Nástroj WAImportExport je jednotka přípravy a opravit nástroj, který vám pomůže s [služby Microsoft Azure Import/Export](../storage-import-export-service.md). Tento nástroj můžete použít ke zkopírování dat do pevné disky, které chcete k odeslání do datacentra Azure. Po dokončení úlohy importu můžete použít tento nástroj opravit všechny objekty BLOB, které došlo k poškození, chyběl nebo konfliktu s další objekty BLOB. Po přijetí jednotky z úlohy dokončené export můžete použít tento nástroj opravit všechny soubory, které byly poškozeny nebo chybí na jednotkách. V tomto článku budeme za použití tohoto nástroje.

## <a name="prerequisites"></a>Požadavky

### <a name="requirements-for-waimportexportexe"></a>Požadavky pro WAImportExport.exe

- **Konfigurace počítače**
  - Windows 7, Windows Server 2008 R2 nebo novějším operačním systému Windows
  - Musí být nainstalované rozhraní .NET framework 4. Zobrazit [nejčastější dotazy k](#faq) o tom, jak zkontrolovat, jestli rozhraní .net Framework je na počítači nainstalovaný.
- **Klíč účtu úložiště** – budete potřebovat alespoň jeden z klíčů účtu pro účet úložiště.

### <a name="preparing-disk-for-import-job"></a>Příprava disku pro úlohu importu

- **BitLocker –** musí na daném počítači používají nástroj WAImportExport zapnutý BitLocker. Najdete v článku [nejčastější dotazy k](#faq) pro povolení nástroje BitLocker.
- **Disky** přístupné z počítače, na kterém je nástroj WAImportExport spustit. Zobrazit [nejčastější dotazy k](#faq) pro specifikaci disku.
- **Zdrojové soubory** – soubory, které chcete importovat musí být přístupné z počítače kopírování, ať už jsou sdílené síťové složky nebo místní pevný disk.

### <a name="repairing-a-partially-failed-import-job"></a>Oprava úlohy importu selhala

- **Kopírování souboru protokolu** , který se vygeneruje, když služba Azure Import/Export, kopíruje data mezi účet úložiště a disku. Nachází se ve vašem účtu cílového úložiště.

### <a name="repairing-a-partially-failed-export-job"></a>Oprava úlohy exportu bylo částečně neúspěšné

- **Kopírování souboru protokolu** , který se vygeneruje, když služba Azure Import/Export, kopíruje data mezi účet úložiště a disku. Nachází se ve vašem účtu zdrojového úložiště.
- **Soubor manifestu** -[Nepovinné] umístěný na exportované disku, který byl vrácen společností Microsoft.

## <a name="download-and-install-waimportexport"></a>Stáhněte a nainstalujte WAImportExport

Stáhněte si [nejnovější verzi WAImportExport.exe](https://www.microsoft.com/download/details.aspx?id=55280). Extrahujte obsah ZIP do adresáře v počítači.

Dalším úkolem je postup vytvoření souborů CSV.

## <a name="prepare-the-dataset-csv-file"></a>Příprava sdíleného svazku clusteru soubor datové sady

### <a name="what-is-dataset-csv"></a>Co je datová sada sdíleného svazku clusteru

Soubor CSV datová sada je, že hodnota příznaku parametr/DataSet je soubor CSV obsahující seznam adresářů a/nebo seznam souborů, které mají být zkopírován do cílové jednotky. Prvním krokem k vytvoření úlohy importu je určit, které adresářů a souborů, které chcete importovat. To může být seznam adresářů, seznam jedinečných souborů nebo kombinaci těchto dvou. Po zahrnuté adresáře se budou všechny soubory v adresáři a jeho podadresářích součástí úlohy importu.

Pro každý adresář nebo soubor k importu je nutné určit cílový virtuální adresář nebo objektů blob ve službě Azure Blob service. Tyto cíle se použít jako vstupy nástroje WAImportExport. Adresáře by měly být oddělené znakem lomítka "/".

V následující tabulce jsou uvedeny příklady cílů objektů blob:

| Zdrojový soubor nebo adresář | Cílový objekt blob nebo virtuálního adresáře |
| --- | --- |
| H:\Video | https://mystorageaccount.blob.core.windows.net/video |
| H:\Photo | https://mystorageaccount.blob.core.windows.net/photo |
| K:\Temp\FavoriteVideo.ISO | https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO |
| \\myshare\john\music | https://mystorageaccount.blob.core.windows.net/music |

### <a name="sample-datasetcsv"></a>Ukázka dataset.csv

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
"F:\50M_original\100M_1.csv.txt","containername/100M_1.csv.txt",BlockBlob,rename,"None",None
"F:\50M_original\","containername/",BlockBlob,rename,"None",None
```

### <a name="dataset-csv-file-fields"></a>Pole souborů CSV datové sady

| Pole | Popis |
| --- | --- |
| BasePath | **[Povinné]**<br/>Hodnota tohoto parametru představuje zdroj, kde se nachází data, která mají být importována. Nástroj se rekurzivně kopírovat všechna data nachází v rámci této cesty.<br><br/>**Povolené hodnoty**: to musí být platná cesta na místním počítači nebo cesta platné sdílené složky a musí být přístupné pro uživatele. Cesta k adresáři musí být absolutní cesta (nikoli relativní cestu). Pokud cesta končí "\\", představuje jiný adresář cesty končí bez"\\" představuje soubor.<br/>V tomto poli je povolený žádný regulární výraz. Pokud cesta obsahuje mezery, vložit ho do "".<br><br/>**Příklad**: "c:\Directory\c\Directory\File.txt"<br>"\\\\FBaseFilesharePath.domain.net\sharename\directory\"  |
| DstBlobPathOrPrefix | **[Povinné]**<br/> Cesta k cílové virtuální adresář ve vašem účtu úložiště Windows Azure. Virtuální adresář může nebo nemusí již existují. Pokud neexistuje, služba Import/Export ho vytvoří.<br/><br/>Nezapomeňte použít názvy platný kontejner při zadání cílové virtuální adresáře nebo objekty BLOB. Uvědomte si, že názvy kontejnerů musí obsahovat malá písmena. Pravidla pojmenování kontejneru, naleznete v tématu [pojmenování a odkazování na kontejnerů, objektů BLOB a metadat](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata). Pokud pouze root není zadána, struktura adresářů zdroje jsou replikovány v cílový kontejner objektů blob. Pokud struktura jiný adresář než ten, který ve zdroji více řádků mapování ve sdíleném svazku clusteru<br/><br/>Můžete zadat kontejner nebo objekt blob předpony jako je hudba/70s /. Cílový adresář musí začínat název kontejneru, za nímž následuje lomítko "/" a může volitelně zahrnovat blob virtuální adresář, který končí na "/".<br/><br/>Pokud cílový kontejner je kořenový kontejner, je nutné explicitně zadat kořenový kontejner, včetně lomítka jako $root /. Protože objekty BLOB v kořenovém kontejneru nemůže obsahovat "/" v jejich názvy, nebude všech podadresářích ve zdrojovém adresáři kopírovat, pokud cílový adresář je kořenový kontejner.<br/><br/>**Příklad**<br/>Pokud je cesta k cílovému objektu blob https://mystorageaccount.blob.core.windows.net/video, hodnota tohoto pole může být videa /  |
| BlobType | **[Volitelné]**  bloku &#124; stránky<br/>Služba Import/Export momentálně podporuje 2 typy objektů BLOB. Stránka objekty BLOB a výchozí BlobsBy bloku, které budou importovány všechny soubory jako objekty BLOB bloku. A \*VHD a \*.vhdx se naimportují, jak BlobsThere stránky, je omezený na objekt blob bloku a povolenou velikost stránky – objekt blob. Zobrazit [cíle škálovatelnosti úložiště](storage-scalability-targets.md) Další informace.  |
| Dispozice | **[Volitelné]**  přejmenovat &#124; přepsat bez &#124; přepsat <br/> Toto pole určuje chování kopírování během importu tj Když se nahrávaných dat do účtu úložiště z disku. Dostupné možnosti: přejmenování&#124;přepsal&#124;přepsat č. Výchozí hodnota je "přejmenovat", pokud není nic zadané. <br/><br/>**Přejmenovat**: Pokud se nachází objekt se stejným názvem, vytvoří kopii v cílovém umístění.<br/>Přepsat: soubor novější soubor přepíše. Soubor se last-modified wins.<br/>**Ne přepsat**: přeskočení zápisu do souboru, pokud již existuje.|
| MetadataFile | **[Volitelné]** <br/>Hodnota, která má toto pole je soubor metadat, které lze zadat, pokud je potřeba zachovat metadata objektů nebo zadat vlastní metadata. Cesta k souboru metadat pro cílové objektů BLOB. Zobrazit [metadat a formát souboru vlastností služby Import/Export](../storage-import-export-file-format-metadata-and-properties.md) Další informace |
| PropertiesFile | **[Volitelné]** <br/>Cesta k souboru vlastnost cílové objektů BLOB. Zobrazit [metadat a formát souboru vlastností služby Import/Export](../storage-import-export-file-format-metadata-and-properties.md) Další informace. |

## <a name="prepare-initialdriveset-or-additionaldriveset-csv-file"></a>Příprava souboru InitialDriveSet nebo AdditionalDriveSet sdíleného svazku clusteru

### <a name="what-is-driveset-csv"></a>Co je driveset sdíleného svazku clusteru

Hodnota příznaku /InitialDriveSet nebo /AdditionalDriveSet je soubor CSV obsahující seznam disků, na které jsou mapovány písmena jednotek tak, aby nástroj správně vybrat seznamu disků, abyste byli připraveni. Pokud velikost dat je větší než velikost jednoho disku, bude nástroj WAImportExport distribucí dat napříč více disků optimálního uveden v tomto souboru CSV.

Neexistuje žádné omezení počtu disků, které je možné zapisovat data na v jedné relaci. Nástroj bude distribuovat data podle velikosti disku a velikost složky. Vybere disk, který je většina optimalizovaná pro velikost objektu. Data, když se nahraje do účtu úložiště bude konvergované zpět do struktury adresářů, který byl zadán v souboru datové sady. Chcete-li vytvořit driveset sdíleného svazku clusteru, postupujte podle následujících kroků.

### <a name="create-basic-volume-and-assign-drive-letter"></a>Vytvoření základního svazku a přiřadit písmeno jednotky

K vytvoření základního svazku a přiřadit písmeno jednotky, postupujte podle pokynů pro "Jednodušší vytváření oddílů" přiřazen při [přehled správy disků](https://technet.microsoft.com/library/cc754936.aspx).

### <a name="sample-initialdriveset-and-additionaldriveset-csv-file"></a>Ukázka InitialDriveSet a sdíleného svazku clusteru AdditionalDriveSet souboru

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
H,Format,SilentMode,Encrypt,
```

### <a name="driveset-csv-file-fields"></a>Pole souborů Driveset sdíleného svazku clusteru

| Fields (Pole) | Hodnota |
| --- | --- |
| Písmeno_jednotky | **[Povinné]**<br/> Každé jednotky, který se právě poskytuje nástroje, jako cíl musí mít jednoduchý svazek NTFS na ně a písmeno jednotky přiřazené k němu.<br/> <br/>**Příklad**: R nebo r |
| FormatOption | **[Povinné]**  Formátu &#124; AlreadyFormatted<br/><br/> **Formát**: určení tohoto naformátuje všechna data na disku. <br/>**AlreadyFormatted**: nástroje se přeskočí, formátování, pokud je zadána tato hodnota. |
| SilentOrPromptOnFormat | **[Povinné]**  SilentMode &#124; PromptOnFormat<br/><br/>**SilentMode**: zadání této hodnoty se povolení uživatele chcete nástroj spustit v bezobslužném režimu. <br/>**PromptOnFormat**: nástroje se zobrazí výzva k potvrzení, jestli akce je ve skutečnosti určený v každé formátu.<br/><br/>Pokud není nastavený, příkaz přerušit, který se zobrazí chybová zpráva: "Nesprávná hodnota pro SilentOrPromptOnFormat: žádná" |
| Šifrování | **[Povinné]**  Šifrování &#124; AlreadyEncrypted<br/> Hodnotou tohoto pole rozhodne není na disku, který k šifrování a které. <br/><br/>**Šifrování**: nástroje se formátování disku. Pokud je hodnota "FormatOption" pole "Formát" pak tato hodnota musí být "Šifrovat". -Li zadána v tomto případě "AlreadyEncrypted", jinak dojde k chybě "Když je určený formát šifrování musí být zadaná také".<br/>**AlreadyEncrypted**: nástroj dešifruje jednotku s použitím BitLockerKey zadané v poli "ExistingBitLockerKey". Pokud je hodnota "FormatOption" pole "AlreadyFormatted", pak tato hodnota může být "Šifrovat" nebo "AlreadyEncrypted" |
| ExistingBitLockerKey | **[Povinné]**  Pokud je hodnota "Šifrování" pole "AlreadyEncrypted"<br/> Hodnota tohoto pole je klíč Bitlockeru, který je spojen s konkrétním diskem. <br/><br/>Toto pole by měla být ponecháno prázdné, pokud je hodnota "Šifrování" pole "Šifrovat".  Pokud v tomto případě je zadaný klíč nástroje BitLocker, jinak dojde k chybě "By neměl být zadaný klíč Bitlockeru".<br/>  **Příklad**: 060456-014509-132033-080300-252615-584177-672089-411631|

##  <a name="preparing-disk-for-import-job"></a>Příprava disku pro úlohu importu

Chcete-li přípravu disků pro úlohu importu, zavolejte nástroj WAImportExport s **PrepImport** příkazu. Parametry, které zahrnete, závisí na, zda je toto první relace kopie nebo kopie následné relace.

### <a name="first-session"></a>První relace

První relace kopírování ke kopírování Single/Multiple adresáře do jednoho nebo více WAImportExport disku (v závislosti na tom, co je zadané v souboru CSV) nástroj PrepImport příkaz pro první relace kopírování pro kopírování adresářů a/nebo soubory s novou relací kopírování:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] /DataSet:<dataset.csv>
```

**Příklad:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:\*\*\*\*\*\*\*\*\*\*\*\*\* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

### <a name="add-data-in-subsequent-session"></a>Přidání dat v následné relace

V další kopie relace není potřeba zadat počáteční parametry. Budete muset použít stejný soubor deníku mohl nástroj na paměti, místa v předchozí relaci. Stav relace kopírování je zapsán do souboru deníku. Tady je syntaxe pro následné kopie relaci ke kopírování a další adresáře souborů nebo souborů:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<DifferentSessionId>  [DataSet:<differentdataset.csv>]
```

**Příklad:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

### <a name="add-drives-to-latest-session"></a>Přidejte disky do nejnovější relace

Pokud data se nevejdou jednotky v InitialDriveset, jeden použijte nástroj přidejte další disky do stejné relace kopírování. 

>[!NOTE] 
>Id relace musí odpovídat id předchozí relace. Soubor deníku by měl odpovídat účtu zadanému v předchozí relaci.
>
```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AdditionalDriveSet:<newdriveset.csv>
```

**Příklad:**

```
WAImportExport.exe PrepImport /j:SameJournalTest.jrn /id:session#2  /AdditionalDriveSet:driveset-2.csv
```

### <a name="abort-the-latest-session"></a>Přerušit nejnovější relace:

Pokud dojde k přerušení relace kopírovat a není možné obnovit (např. Pokud zdrojový adresář dokázaly nedostupná), musíte zrušit aktuální relaci, tak, že může být vrácena zpět a dá se spouštět nové kopie relace:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AbortSession
```

**Příklad:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /AbortSession
```

Pouze poslední relace kopírovat, pokud byl ukončen neobvyklým způsobem, může být přerušena. Všimněte si, že není možné přerušit prvního kopírování relace pro jednotku. Místo toho je nutné restartovat relaci kopírování se nový soubor deníku.

### <a name="resume-a-latest-interrupted-session"></a>Nejnovější přerušené relaci pokračovat

Pokud z nějakého důvodu dojde k přerušení relace kopírování, můžete ho obnovit pomocí zadaný pouze soubor deníku spustí nástroj:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /ResumeSession
```

**Příklad:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /ResumeSession
```

> [!IMPORTANT] 
> Když budete pokračovat v kopírování relace, neprovádějte žádné změny zdrojových datových souborů a adresářů přidáním nebo odebráním soubory.

## <a name="waimportexport-parameters"></a>Parametry WAImportExport

| Parametry | Popis |
| --- | --- |
|     /j:&lt;JournalFile&gt;  | **Vyžaduje**<br/> Cesta k souboru deníku. Soubor deníku Sada disků sleduje a zaznamenává průběh při přípravě těchto jednotek. Soubor deníku musí být vždy zadán.  |
|     /logdir:&lt;LogDirectory&gt;  | **Volitelné**. Adresář protokolu.<br/> Podrobné soubory protokolů, jakož i některé dočasné soubory, se zapíšou do tohoto adresáře. Pokud není zadaný, aktuální adresář se použije jako adresář protokolu. Adresář protokolu se dá nastavit jenom jednou pro stejný soubor deníku.  |
|     /ID:&lt;SessionId&gt;  | **Vyžaduje**<br/> Relace, Id se používá k identifikaci kopírování relace. Používá se k zajištění přesných obnovení relace přerušené kopírování.  |
|     / ResumeSession  | Volitelné. Pokud poslední relace kopie byla neobvykle ukončena, můžete tento parametr zadán k obnovení relace.   |
|     / AbortSession  | Volitelné. Pokud poslední relace kopie byla neobvykle ukončena, tento parametr lze zadat pro přerušení relace.  |
|     /sn:&lt;StorageAccountName&gt;  | **Vyžaduje**<br/> Platí jenom pro RepairImport a RepairExport. Název účtu úložiště.  |
|     /Sk:&lt;StorageAccountKey&gt;  | **Vyžaduje**<br/> Klíč účtu úložiště. |
|     / InitialDriveSet:&lt;driveset.csv&gt;  | **Vyžaduje** při spuštění prvního kopírování relace<br/> Soubor CSV obsahující seznam jednotek přípravy.  |
|     / AdditionalDriveSet:&lt;driveset.csv&gt; | **Vyžaduje**. Při přidávání disků do aktuální relace kopírování. <br/> Soubor CSV obsahující seznam dalších jednotek, které mají být přidány.  |
|      / r:&lt;RepairFile&gt; | **Vyžaduje** platí jenom pro RepairImport a RepairExport.<br/> Cesta k souboru pro sledování pokroku opravit. Každá jednotka musí mít jeden a pouze jeden soubor opravy.  |
|     / d:&lt;TargetDirectories&gt; | **Vyžaduje**. Platí jenom pro RepairImport a RepairExport. Pro RepairImport jeden nebo více oddělených středníkem adresářů opravit; RepairExport, jeden adresář, pokud chcete opravit, například kořenový adresář jednotky.  |
|     / CopyLogFile:&lt;DriveCopyLogFile&gt; | **Vyžaduje** platí jenom pro RepairImport a RepairExport. Cesta k souboru protokolu kopie disku (podrobný nebo chyba).  |
|     / ManifestFile:&lt;DriveManifestFile&gt; | **Vyžaduje** platí jenom pro RepairExport.<br/> Cesta k souboru manifestu jednotky.  |
|     / PathMapFile:&lt;DrivePathMapFile&gt; | **Volitelné**. Platí jenom pro RepairImport.<br/> Cesta k souboru, který obsahuje mapování cesty k souborům relativně vzhledem k kořen jednotky do umístění skutečné souborech (oddělený tabulátory). Při první zadaný ji naplní cesty k souborům s prázdnou cíle, což znamená, že nebyly nalezeny v TargetDirectories přístup byl odepřen, s neplatným názvem, nebo že existují ve více adresářů. Cesta k souboru mapy můžete ručně upravit tak, aby správné cílové cesty zahrnutí a zadat znovu pro nástroj, který správně přeložit cesty k souborům.  |
|     / ExportBlobListFile:&lt;ExportBlobListFile&gt; | **Vyžaduje**. Platí jenom pro PreviewExport.<br/> Cesta k souboru XML soubor obsahující seznam cesty k objektům blob nebo objekt blob předpony cesty pro objekty BLOB nelze exportovat. Formát souboru je stejný jako formát objektů blob v seznamu objektů blob v operaci Put úlohy z rozhraní REST API služby Import/Export.  |
|     / DriveSize:&lt;DriveSize&gt; | **Vyžaduje**. Platí jenom pro PreviewExport.<br/>  Velikost disků se použije pro export. Například 500 GB, 1,5 TB. Poznámka: 1 GB = 1 000 000 000 bytes1 TB = 1,000,000,000,000 bajtů  |
|     / DataSet:&lt;dataset.csv&gt; | **Vyžaduje**<br/> Soubor CSV obsahující seznam adresářů a/nebo seznam souborů, které mají být zkopírován do cílové jednotky.  |
|     /silentmode  | **Volitelné**.<br/> Pokud není zadán, bude vás upozorní na požadavek jednotky a potřebovat vaše potvrzení, abyste mohli pokračovat.  |

## <a name="tool-output"></a>Výstup nástroje

### <a name="sample-drive-manifest-file"></a>Ukázkový soubor manifestu jednotky

```xml
<?xml version="1.0" encoding="UTF-8"?>
<DriveManifest Version="2011-MM-DD">
   <Drive>
      <DriveId>drive-id</DriveId>
      <StorageAccountKey>storage-account-key</StorageAccountKey>
      <ClientCreator>client-creator</ClientCreator>
      <!-- First Blob List -->
      <BlobList Id="session#1-0">
         <!-- Global properties and metadata that applies to all blobs -->
         <MetadataPath Hash="md5-hash">global-metadata-file-path</MetadataPath>
         <PropertiesPath Hash="md5-hash">global-properties-file-path</PropertiesPath>
         <!-- First Blob -->
         <Blob>
            <BlobPath>blob-path-relative-to-account</BlobPath>
            <FilePath>file-path-relative-to-transfer-disk</FilePath>
            <ClientData>client-data</ClientData>
            <Length>content-length</Length>
            <ImportDisposition>import-disposition</ImportDisposition>
            <!-- page-range-list-or-block-list -->
            <!-- page-range-list -->
            <PageRangeList>
               <PageRange Offset="1073741824" Length="512" Hash="md5-hash" />
               <PageRange Offset="1073741824" Length="512" Hash="md5-hash" />
            </PageRangeList>
            <!-- block-list -->
            <BlockList>
               <Block Offset="1073741824" Length="4194304" Id="block-id" Hash="md5-hash" />
               <Block Offset="1073741824" Length="4194304" Id="block-id" Hash="md5-hash" />
            </BlockList>
            <MetadataPath Hash="md5-hash">metadata-file-path</MetadataPath>
            <PropertiesPath Hash="md5-hash">properties-file-path</PropertiesPath>
         </Blob>
      </BlobList>
   </Drive>
</DriveManifest>
```

### <a name="sample-journal-file-xml-for-each-drive"></a>Ukázkový soubor deníku (XML) pro každou jednotku

```xml
[BeginUpdateRecord][2016/11/01 21:22:25.379][Type:ActivityRecord]
ActivityId: DriveInfo
DriveState: [BeginValue]
<?xml version="1.0" encoding="UTF-8"?>
<Drive>
   <DriveId>drive-id</DriveId>
   <BitLockerKey>*******</BitLockerKey>
   <ManifestFile>\DriveManifest.xml</ManifestFile>
   <ManifestHash>D863FE44F861AE0DA4DCEAEEFFCCCE68</ManifestHash> </Drive>
[EndValue]
SaveCommandOutput: Completed
[EndUpdateRecord]
```

### <a name="sample-journal-file-jrn-for-session-that-records-the-trail-of-sessions"></a>Ukázkový soubor deníku (JRN) pro relaci, která zaznamenává záznam relace

```
[BeginUpdateRecord][2016/11/02 18:24:14.735][Type:NewJournalFile]
VocabularyVersion: 2013-02-01
[EndUpdateRecord]
[BeginUpdateRecord][2016/11/02 18:24:14.749][Type:ActivityRecord]
ActivityId: PrepImportDriveCommandContext
LogDirectory: F:\logs
[EndUpdateRecord]
[BeginUpdateRecord][2016/11/02 18:24:14.754][Type:ActivityRecord]
ActivityId: PrepImportDriveCommandContext
StorageAccountKey: *******
[EndUpdateRecord]
```

## <a name="faq"></a>Nejčastější dotazy

### <a name="general"></a>Obecné

#### <a name="what-is-waimportexport-tool"></a>Co je nástroj WAImportExport?

Nástroj WAImportExport je jednotka přípravy a opravit nástroj, který vám pomůže se služby Microsoft Azure Import/Export. Tento nástroj můžete použít ke zkopírování dat do pevné disky, které se chystáte odešlete ji do datového centra Azure. Po dokončení úlohy importu můžete použít tento nástroj opravit všechny objekty BLOB, které došlo k poškození, chyběl nebo konfliktu s další objekty BLOB. Po přijetí jednotky z úlohy dokončené export můžete použít tento nástroj opravit všechny soubory, které byly poškozeny nebo chybí na jednotkách.

#### <a name="how-does-the-waimportexport-tool-work-on-multiple-source-dir-and-disks"></a>Jak funguje nástroj WAImportExport na více disků a zdrojový adresář?

Pokud velikost dat je větší než velikost disku, nástroj WAImportExport distribucí dat napříč disky optimalizovaná způsobem. Kopírování dat na více disků můžete udělat sekvenčně nebo paralelně. Neexistuje žádné omezení počtu disků, které data je možné zapisovat na současně. Nástroj bude distribuovat data podle velikosti disku a velikost složky. Vybere disk, který je většina optimalizovaná pro velikost objektu. Data, když se nahraje do účtu úložiště bude konvergované zpět do struktury zadaný adresář.

#### <a name="where-can-i-find-previous-version-of-waimportexport-tool"></a>Kde můžu najít předchozí verzi nástroje WAImportExport?

Nástroj WAImportExport má všechny funkce, do kterých se nástroj WAImportExport V1. Nástroj WAImportExport umožňuje uživatelům zadat více zdrojů a zápis do několika jednotkách. Kromě toho jeden moct snadno spravovat více zdrojová umístění, ze kterých se data, musí zkopírovat do jednoho souboru CSV. V případě, že budete potřebovat podporu SAS nebo chcete zkopírovat jeden zdroj na jeden disk, můžete si ale [stáhněte si nástroj V1 WAImportExport](http://go.microsoft.com/fwlink/?LinkID=301900&amp;clcid=0x409) věnované [WAImportExport V1 odkaz](storage-import-export-tool-how-to-v1.md) nápovědu k používání WAImportExport V1 .

#### <a name="what-is-a-session-id"></a>Co je ID relace?

Nástroj očekává, že relace kopie (/ id) parametr bude stejný, pokud je cílem rozložit data na několik disků. Zachování stejný název jako kopie relace vám umožní uživatele ke zkopírování dat z jednoho nebo více zdrojových umístění do jednoho nebo více cílové disky/adresáře. Zachování stejné id relace povolí nástroj ke sbírání kopírování souborů z byl místa posledního.

Stejné relace kopie však nelze použít pro import dat do různých účtů úložiště.

Pokud název kopie relace je stejný během různých spuštění nástroje souboru protokolu (/ logdir) a klíč účtu úložiště (/ sk) také má být stejné.

ID relace se může skládat z písmen, 0 ~ 9, understore (\_), pomlčky (-) nebo hodnoty hash (#), a jeho délka musí být 3 ~ 30.

například relace 1 nebo 1 nebo relaci\_1

#### <a name="what-is-a-journal-file"></a>Co je soubor deníku?

Pokaždé, když spustíte nástroj WAImportExport kopírovat soubory na pevný disk, nástroj vytvoří relaci kopírování. Stav relace kopírování je zapsán do souboru deníku. Pokud dojde k přerušení relace kopie (například kvůli výpadku napájení systému), můžete obnovit nástroj znovu spustit a zadáním soubor deníku, na příkazovém řádku.

Pro každý pevný disk připravte pomocí nástroje Azure Import/Export, nástroj vytvoří soubor deníku jeden s názvem "&lt;ID jednotky&gt;.xml" kde jednotka Id je sériové číslo přidružené k jednotce, která nástroj načte z disku. Budete potřebovat soubory deníku ze všech jednotek k vytvoření úlohy importu. Soubor deníku lze také obnovit Příprava jednotky, pokud dojde k přerušení nástroj.

#### <a name="what-is-a-log-directory"></a>Co je adresář protokolu?

Adresář protokolu Určuje adresář, který se má použít k ukládání podrobné protokoly, stejně jako dočasné soubory manifestu. Pokud není zadán, použije aktuální adresář jako adresář protokolu. Podrobné protokoly jsou protokoly.

### <a name="prerequisites"></a>Požadavky

#### <a name="what-are-the-specifications-of-my-disk"></a>Jaké jsou specifikace Moje disku?

Nejmíň jeden prázdný 2,5 nebo 3, 5palcová SATAII o rychlosti nebo III nebo SSD disky připojené k počítači kopírování.

#### <a name="how-can-i-enable-bitlocker-on-my-machine"></a>Jak můžu povolit nástroj BitLocker na mém počítači?

Kliknutím pravým tlačítkem myši na systémové jednotce je jednoduchý způsob, jak zkontrolovat. Zobrazí se možnosti pro nástroje Bitlocker v případě, že je zapnutá možnost. Pokud má hodnotu off, nezobrazí se.

![Zkontrolujte nástroj BitLocker](./media/storage-import-export-tool-preparing-hard-drives-import/BitLocker.png)

Tady je článek [jak zapnout BitLocker](https://technet.microsoft.com/library/cc766295.aspx)

Je možné, že váš počítač nemá čipu TPM. Pokud nelze získat výstup pomocí tpm.msc, podívejte se na další nejčastější dotazy.

#### <a name="how-to-disable-trusted-platform-module-tpm-in-bitlocker"></a>Jak zakázat Trusted Platform Module (TPM) v nástroji BitLocker?
> [!NOTE]
> Pouze v případě, že v jejich serverech není bez TPM, je nutné zakázat zásadu TPM. Není nutné zakázat čipu TPM, pokud je v serveru uživatele důvěryhodný čipu TPM. 
> 

Aby bylo možné zakázat TPM v Bitlockeru, projděte si následující kroky:<br/>
1. Spuštění **Editor zásad skupiny** tak, že na příkazovém řádku zadáte příkaz gpedit.msc. Pokud **Editor zásad skupiny** se zdá být k dispozici, nejprve povolení nástroje BitLocker. Viz předchozí – nejčastější dotazy.
2. Otevřít **zásady místního počítače &gt; konfigurace počítače &gt; šablony pro správu &gt; součásti Windows&gt; nástroj BitLocker Drive Encryption &gt; jednotkyoperačníhosystému**.
3. Upravit **vyžadovat další ověření při spuštění** zásad.
4. Nastavte zásady na **povoleno** a ujistěte se, že **povolit nástroj BitLocker bez kompatibilního čipu TPM** je zaškrtnuté políčko.

####  <a name="how-to-check-if-net-4-or-higher-version-is-installed-on-my-machine"></a>Návod k ověření, pokud je nainstalované rozhraní .NET 4 nebo vyšší verzi rozhraní .NET na mém počítači?

Všechny verze rozhraní Microsoft .NET Framework jsou nainstalovány v následujícím adresáři: %windir%\Microsoft.NET\Framework\

Přejděte do části výše uvedené na vaší cílovém počítači, kde je potřeba spustit nástroj. Vyhledejte název složky počínaje "v4". Absence takových adresáře znamená, že rozhraní .NET 4 není na vašem počítači nainstalovaný. Můžete si stáhnout rozhraní .net 4 počítače [rozhraní Microsoft .NET Framework 4 (Webová instalační služba)](https://www.microsoft.com/download/details.aspx?id=17851).

### <a name="limits"></a>Omezení

#### <a name="how-many-drives-can-i-preparesend-at-the-same-time"></a>Kolik jednotek můžete I připravit/odesílání ve stejnou dobu?

Neexistuje žádné omezení počtu disků, které můžete připravit nástroj. Ale nástroj očekává, že písmena jednotek jako vstupy. Která omezuje na 25 přípravy disků současně. Jedné úlohy může zpracovávat maximálně 10 disků najednou. Pokud připravujete více než 10 disky, které cílí na stejný účet úložiště, disky lze distribuovat mezi několika úlohami.

#### <a name="can-i-target-more-than-one-storage-account"></a>Můžete cílit více než jeden účet úložiště?

Pouze jeden účet úložiště můžete odeslat na jednu úlohu a v relaci jedna kopie.

### <a name="capabilities"></a>Možnosti

#### <a name="does-waimportexportexe-encrypt-my-data"></a>Šifruje WAImportExport.exe Moje data?

Ano. Šifrování nástrojem BitLocker je povolen a vyžaduje se pro tento proces.

#### <a name="what-will-be-the-hierarchy-of-my-data-when-it-appears-in-the-storage-account"></a>Co když se objeví v účtu úložiště bude hierarchie Moje data?

I když jsou data distribuovaná napříč disky, data, když se nahraje do účtu úložiště bude konvergované zpět do struktury adresářů zadané v souboru CSV datové sady.

#### <a name="how-many-of-the-input-disks-will-have-active-io-in-parallel-when-copy-is-in-progress"></a>Kolik vstupu disků budou mít aktivní vstupně-výstupní operace paralelně, když probíhá kopírování?

Nástroj rozděluje data mezi vstupu disků na základě velikosti vstupních souborů. Ale nutné dodat, počet aktivních disků současně zcela delends na povaze vstupní data. V závislosti na velikosti jednotlivých souborů ve vstupní sadě jeden nebo více disků se můžou zobrazovat active vstupně-výstupní operace paralelně. Podívejte se na další otázky pro další podrobnosti.

#### <a name="how-does-the-tool-distribute-the-files-across-the-disks"></a>Jak nástroj distribuovat soubory mezi disky?

Nástroj WAImportExport čte a zapisuje soubory služby batch pomocí služby batch, jednu dávku obsahuje maximální počet souborů 100000. To znamená, že maximální 100000 lze zapisovat soubory současně. Více disků se zapisují do současně Pokud tyto soubory 100000 nejsou distribuovány do více jednotek. Nicméně, zda nástroj zapisuje na více disků současně nebo jeden disk, závisí na souhrnná velikost dávky. Například v případě menší soubory, pokud všechny 10,0000 souborů vejít do jedné jednotky, nástroj bude údaje zapisovat jenom jeden disk během zpracování této služby batch.

### <a name="waimportexport-output"></a>Výstup WAImportExport

#### <a name="there-are-two-journal-files-which-one-should-i-upload-to-azure-portal"></a>Existují dva soubory deníku, které z nich by měl nahraji na webu Azure portal?

**XML** – pro každý pevný disk připravte nástrojem WAImportExport, nástroj vytvoří soubor deníku jeden s názvem `<DriveID>.xml` kde ID jednotky je sériové číslo přidružené k jednotce, která nástroj načte z disku. Budete potřebovat soubory deníku ze všech jednotek k vytvoření úlohy importu na webu Azure Portal. Tento soubor deníku lze také obnovit Příprava jednotky, pokud dojde k přerušení nástroj.

**.jrn** – soubor deníku s příponou `.jrn` obsahuje stav pro všechny relace kopírování pro pevný disk. Také obsahuje informace potřebné k vytvoření úlohy importu. Vždy třeba zadat soubor deníku při spuštění nástroje WAImportExport, jakož i ID relace. kopie

## <a name="next-steps"></a>Další postup

* [Nastavení nástroje Azure Import/Export](storage-import-export-tool-setup.md)
* [Nastavení vlastností a metadat během procesu importu](storage-import-export-tool-setting-properties-metadata-import.md)
* [Ukázkový pracovní postup pro přípravu pevných disků pro úlohu importu](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
* [Stručná referenční příručka pro často používané příkazy](storage-import-export-tool-quick-reference.md) 
* [Kontrola stavu úlohy s použitím kopií souborů protokolu](storage-import-export-tool-reviewing-job-status-v1.md)
* [Oprava úlohy importu](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Oprava úlohy exportu](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Řešení potíží s nástrojem Azure pro import/export](storage-import-export-tool-troubleshooting-v1.md)
