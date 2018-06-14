---
title: Příprava pevné disky pro úlohy importu Azure Import/Export | Microsoft Docs
description: Zjistěte, jak připravit pevných disků pomocí nástroje WAImportExport k vytvoření úlohy importu do služby Azure Import/Export.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: muralikk
ms.openlocfilehash: 2854822907e818297c8d2f74cab48b0afa0d646c
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2017
ms.locfileid: "23931720"
---
# <a name="preparing-hard-drives-for-an-import-job"></a>Příprava úlohy importu pevných disků

Nástroj WAImportExport je jednotka přípravy a opravit nástroj, který můžete použít s [služby Microsoft Azure Import/Export](../storage-import-export-service.md). Tento nástroj můžete použít ke zkopírování dat do pevné disky, které budete pro odeslání do datového centra Azure. Po dokončení úlohy importu, můžete tento nástroj a opravte všechny objekty BLOB, které došlo k poškození, chyběl nebo konflikt s další objekty BLOB. Po obdržení jednotky z úlohy dokončené export, můžete tento nástroj a opravte všechny soubory, které byly poškozené nebo chybí na jednotkách. V tomto článku jsme projít použití tohoto nástroje.

## <a name="prerequisites"></a>Požadavky

### <a name="requirements-for-waimportexportexe"></a>Požadavky pro WAImportExport.exe

- **Konfigurace počítače**
  - Windows 7, Windows Server 2008 R2 nebo novější operační systém Windows
  - Musí být nainstalované rozhraní .NET framework 4. V tématu [– nejčastější dotazy](#faq) o tom, jak zkontrolovat, zda rozhraní .net Framework je v počítači nainstalován.
- **Klíč účtu úložiště** -potřebujete aspoň jeden z klíčů účtu pro účet úložiště.

### <a name="preparing-disk-for-import-job"></a>Příprava disku úlohy importu

- **BitLocker –** BitLocker musí být povolená na počítač se spouští nástroj WAImportExport. Najdete v článku [– nejčastější dotazy](#faq) pro povolení nástroje BitLocker.
- **Disky** přístupné z počítače, na kterém je spuštěn nástroj WAImportExport. V tématu [– nejčastější dotazy](#faq) pro specifikaci disku.
- **Zdrojové soubory** – soubory, které chcete importovat musí být přístupné z počítače kopírování, ať už jsou sdílené síťové složky nebo místním pevném disku.

### <a name="repairing-a-partially-failed-import-job"></a>Oprava úlohy částečně selhání importu

- **Kopírování souboru protokolu** který se vygeneruje, když služba Azure Import/Export zkopíruje data mezi účtem úložiště a disku. Nachází se ve cílový účet úložiště.

### <a name="repairing-a-partially-failed-export-job"></a>Oprava úlohu částečně selhání exportu

- **Kopírování souboru protokolu** který se vygeneruje, když služba Azure Import/Export zkopíruje data mezi účtem úložiště a disku. Nachází se ve vašem účtu úložiště zdroje.
- **Soubor manifestu** -[Nepovinné] umístěný na exportovaný disku, který byl vrácen společností Microsoft.

## <a name="download-and-install-waimportexport"></a>Stáhněte a nainstalujte WAImportExport

Stažení [nejnovější verzi WAImportExport.exe](https://www.microsoft.com/download/details.aspx?id=55280). Extrahujte komprimované obsah do adresáře v počítači.

Svůj další úkol je vytvoření souborů CSV.

## <a name="prepare-the-dataset-csv-file"></a>Připravte si soubor CSV datové sady

### <a name="what-is-dataset-csv"></a>Co je datová sada sdíleného svazku clusteru

Soubor CSV datové sady je že příznak /dataset hodnotu soubor CSV, který obsahuje seznam adresářů a/nebo seznam souborů ke zkopírování na cílové jednotky. Prvním krokem k vytvoření úlohy importu je určit, které adresářů a souborů, které chcete importovat. To může být seznam adresářů, seznam souborů, jedinečné nebo kombinaci těchto dvou. Po zahrnuté adresáře se budou všechny soubory v adresáři a jejích podadresářů součástí úlohy importu.

Pro každý adresář nebo soubor k importu musí identifikovat cílový virtuální adresář nebo objekt blob ve službě Azure Blob. Jako vstupy pro nástroj WAImportExport použijete tyto cíle. Adresáře musí být oddělen s lomítkem znakem "/".

V následující tabulce jsou uvedeny příklady cílů objektů blob:

| Zdrojový soubor nebo adresář | Cílový objekt blob nebo virtuální adresář |
| --- | --- |
| H:\Video | https://mystorageaccount.BLOB.Core.Windows.NET/video |
| H:\Photo | https://mystorageaccount.BLOB.Core.Windows.NET/Photo |
| K:\Temp\FavoriteVideo.ISO | https://mystorageaccount.BLOB.Core.Windows.NET/Favorite/FavoriteVideo.ISO |
| \\myshare\john\music | https://mystorageaccount.BLOB.Core.Windows.NET/Music |

### <a name="sample-datasetcsv"></a>Ukázka dataset.csv

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
"F:\50M_original\100M_1.csv.txt","containername/100M_1.csv.txt",BlockBlob,rename,"None",None
"F:\50M_original\","containername/",BlockBlob,rename,"None",None
```

### <a name="dataset-csv-file-fields"></a>Polí v souboru CSV datové sady

| Pole | Popis |
| --- | --- |
| BasePath | **[Vyžaduje]**<br/>Hodnota tohoto parametru představuje zdroj, kde jsou umístěna data pro import. Tento nástroj se rekurzivnímu kopírování všech dat umístěných v této cestě.<br><br/>**Povolené hodnoty**: to musí být platnou cestu v místním počítači nebo cestu ke sdílené složce platná a musí být pro uživatele přístupný. Cesta k adresáři musí být absolutní cesta (není relativní cesta). Pokud cesta končí textem "\\", představuje cestu k ukončení bez jiný adresář"\\" představuje soubor.<br/>V tomto poli je povolené žádné regulární výraz. Pokud cesta obsahuje mezery, dejte ho do "".<br><br/>**Příklad**: "c:\Directory\c\Directory\File.txt"<br>"\\\\FBaseFilesharePath.domain.net\sharename\directory\"  |
| DstBlobPathOrPrefix | **[Vyžaduje]**<br/> Cesta k cílové virtuální adresář ve vašem účtu úložiště služby Windows Azure. Virtuální adresář může nebo již neexistuje. Pokud neexistuje, vytvoří jednu službu Import/Export.<br/><br/>Je nutné použít platný kontejner názvy při zadávání cílové virtuální adresáře nebo objekty BLOB. Uvědomte si, že názvy kontejnerů musí být malými písmeny. Pravidla pojmenování kontejneru, najdete v části [pojmenování a odkazování na kontejnerů, objektů BLOB a metadat](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata). Pokud jenom kořenový není zadaný, strukturu adresáře zdroje se replikují v cílový kontejner objektů blob. Pokud se požaduje jinou adresářovou strukturu než ten, který ve zdroji, více řádků mapování ve sdíleném svazku clusteru<br/><br/>Můžete zadat kontejner nebo objekt blob předpona jako Hudba nebo 70s /. Cílový adresář musí začínat název kontejneru, za nímž následuje lomítkem "/" a může volitelně obsahovat blob virtuální adresáře, který končí textem "/".<br/><br/>Pokud cílový kontejner je kořenový kontejner, je třeba explicitně zadat kořenový kontejner, včetně lomítkem, jako $root /. Vzhledem k tomu, že nesmí obsahovat objekty BLOB v kořenovém kontejneru "/" v jejich názvy, pokud cílový adresář je kořenový kontejner se nezkopírují jakéhokoliv podadresáře v adresáři zdroje.<br/><br/>**Příklad**<br/>Pokud cílová cesta objektu blob je https://mystorageaccount.blob.core.windows.net/video, hodnota v tomto poli může být video nebo  |
| BlobType | **[Nepovinné]**  bloku &#124; stránky<br/>Aktuálně podporuje Import/Export služby 2 typy objektů BLOB. Stránka objekty BLOB a výchozí BlobsBy blokovat všechny soubory se naimportují jako objekty BLOB bloku. A \*.vhd a \*.vhdx se naimportovat, protože stránka BlobsThere je limit na objekt blob bloku a – objekt blob stránky povolenou velikost. V tématu [cíle škálovatelnosti úložiště](storage-scalability-targets.md) Další informace.  |
| Dispozice | **[Nepovinné]**  přejmenovat &#124; ne přepsat &#124; přepsání <br/> Toto pole určuje chování kopie během importu jednofaktorovému data se nahrává při k účtu úložiště z disku. Dostupné možnosti jsou: přejmenování &#124; chcete ho přepsat &#124; ne přepsat. "" Pokud přejmenujte nic zadat výchozí nastavení. <br/><br/>**Přejmenujte**: Pokud se nachází objekt se stejným názvem, vytvoří se kopie v cílovém umístění.<br/>Přepsat: přepíše soubor novější souboru. Soubor se poslední úpravy wins.<br/>**Přepsat ne**: přeskočení zápisu do souboru, pokud již existuje.|
| MetadataFile | **[Nepovinné]** <br/>Hodnota, která má toto pole je soubor metadat, které lze zadat, pokud je tu potřeba zachovat metadata objektů, nebo zadejte vlastní metadata. Cesta k souboru metadat pro cílové objekty BLOB. V tématu [importu/exportu služby Metadata a formát vlastností souboru](../storage-import-export-file-format-metadata-and-properties.md) Další informace |
| PropertiesFile | **[Nepovinné]** <br/>Cesta k souboru vlastností pro cílové objekty BLOB. V tématu [importu/exportu služby Metadata a formát vlastností souboru](../storage-import-export-file-format-metadata-and-properties.md) Další informace. |

## <a name="prepare-initialdriveset-or-additionaldriveset-csv-file"></a>Připravte si soubor InitialDriveSet nebo AdditionalDriveSet sdíleného svazku clusteru

### <a name="what-is-driveset-csv"></a>Co je driveset sdíleného svazku clusteru

Hodnota příznaku /InitialDriveSet nebo /AdditionalDriveSet je soubor CSV, který obsahuje seznam disků, na které jsou namapované písmena jednotek, aby tento nástroj můžete vybrat správně seznam disků, abyste byli připraveni. Pokud velikost dat je větší než velikost jednoho disku, nástroj WAImportExport zajistěte distribuci dat na více disků v optimálního uvedené v tomto souboru CSV.

Neexistuje žádné omezení počtu disky, které můžete zapisovat data v rámci jedné relace. Nástroj provede distribuci dat na základě velikosti disku a velikost složky. Vybere disk, který je většina optimalizované pro velikost objektu. Data po odeslání k účtu úložiště bude konvergované zpět do struktury adresářů, která byla zadaná v souboru datové sady. Chcete-li vytvořit driveset sdíleného svazku clusteru, postupujte podle následujících kroků.

### <a name="create-basic-volume-and-assign-drive-letter"></a>Vytvoření základního svazku a přiřadit písmeno jednotky

K vytvoření základního svazku a přiřadit písmeno jednotky, postupujte podle pokynů pro "Jednodušší oddílu Vytvoření" uveden na [přehled správy disků](https://technet.microsoft.com/library/cc754936.aspx).

### <a name="sample-initialdriveset-and-additionaldriveset-csv-file"></a>Ukázka InitialDriveSet a AdditionalDriveSet CSV souboru

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
H,Format,SilentMode,Encrypt,
```

### <a name="driveset-csv-file-fields"></a>Polí v souboru Driveset CSV

| Pole | Hodnota |
| --- | --- |
| Písmeno_jednotky | **[Vyžaduje]**<br/> Každé jednotky, které se k nástroji poskytuje jako cíl musí mít jednoduchý svazek NTFS na ho a písmeno jednotky přiřazené.<br/> <br/>**Příklad**: R nebo r |
| FormatOption | **[Vyžaduje]**  Formátu &#124; AlreadyFormatted<br/><br/> **Formát**: určení to naformátuje všechna data na disku. <br/>**AlreadyFormatted**: nástroj přeskočí, formátování, pokud je zadána tato hodnota. |
| SilentOrPromptOnFormat | **[Vyžaduje]**  SilentMode &#124; PromptOnFormat<br/><br/>**SilentMode**: poskytuje tuto hodnotu umožní uživatelům spouštět nástroj v bezobslužném režimu. <br/>**PromptOnFormat**: nástroj vyzve uživatele k potvrzení, jestli je akce skutečně určeno v každé formátu.<br/><br/>Pokud není nastavená, příkaz k přerušení a zobrazit chybová zpráva: "Nesprávná hodnota pro SilentOrPromptOnFormat: žádné" |
| Šifrování | **[Vyžaduje]**  Šifrování &#124; AlreadyEncrypted<br/> Hodnota tohoto pole rozhodne není na disku, který má šifrování a který. <br/><br/>**Šifrování**: nástroj bude formátování disku. Pokud je hodnota "FormatOption" pole "Format" pak tato hodnota musí být "Šifrovat". Pokud v tomto případě je zadán "AlreadyEncrypted", způsobí to došlo k chybě "Při zadání formát je šifrování musí být zadaná také".<br/>**AlreadyEncrypted**: nástroj bude dešifrování jednotky pomocí BitLockerKey zadané v poli "ExistingBitLockerKey". Pokud je hodnota "FormatOption" pole "AlreadyFormatted", pak tato hodnota může být buď "Šifrovat" nebo "AlreadyEncrypted" |
| ExistingBitLockerKey | **[Vyžaduje]**  Pokud je hodnota "Šifrování" pole "AlreadyEncrypted"<br/> Hodnota tohoto pole je klíč nástroje BitLocker, která souvisí s konkrétním diskem. <br/><br/>Toto pole by měl být ponecháno prázdné, pokud je hodnota "Šifrování" pole "Šifrovat".  Pokud v tomto případě není zadaný klíč nástroje BitLocker, způsobí to došlo k chybě "By neměl být určen klíč nástroje Bitlocker".<br/>  **Příklad**: 060456-014509-132033-080300-252615-584177-672089-411631|

##  <a name="preparing-disk-for-import-job"></a>Příprava disku úlohy importu

Příprava jednotky pro úlohy importu, volání nástroje WAImportExport s **PrepImport** příkaz. Parametry, které zahrnete závisí na tom, jestli je to první kopie relaci nebo relaci další kopie.

### <a name="first-session"></a>První relaci

První relaci kopírování ke kopírování Single/Multiple adresář na jeden nebo více WAImportExport disku (v závislosti na tom, co je určena v souboru CSV) nástroj PrepImport příkaz pro první relaci kopírování ke kopírování adresářů a souborů s novou relací kopie:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
```

**Příklad:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:\*\*\*\*\*\*\*\*\*\*\*\*\* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

### <a name="add-data-in-subsequent-session"></a>Přidat data v následných relace

V další kopie relace není potřeba zadat počáteční parametry. Budete muset použít stejný soubor deníku v pořadí pro nástroj pro mějte na paměti, místa v předchozí relace. Stav relace kopírování se zapisují do souboru deníku. Tady je syntaxe pro relaci a následné kopírování ke kopírování a další adresáře nebo soubory:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<DifferentSessionId>  [DataSet:<differentdataset.csv>]
```

**Příklad:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

### <a name="add-drives-to-latest-session"></a>Přidejte disky do nejnovější relace

Pokud data nevejdou do jednotky v InitialDriveset, jeden nástrojem přidejte další disky do stejné kopie relace. 

>[!NOTE] 
>Id relace shodovat s id předchozí relace. Soubor deníku by měl odpovídat účtu zadanému v předchozí relaci.
>
```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AdditionalDriveSet:<newdriveset.csv>
```

**Příklad:**

```
WAImportExport.exe PrepImport /j:SameJournalTest.jrn /id:session#2  /AdditionalDriveSet:driveset-2.csv
```

### <a name="abort-the-latest-session"></a>Abort – nejnovější relace:

Pokud dojde k přerušení relace kopírování a není možné obnovit (například pokud je zdrojový adresář prokázat nedostupné), musíte přerušit aktuální relaci, tak, aby ji možné vrátit zpět a nové kopie relací lze spustit:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AbortSession
```

**Příklad:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /AbortSession
```

Pouze poslední relace kopie, pokud byl ukončen neobvyklým způsobem, můžete přerušena. Všimněte si, že nelze přerušit má první relace kopie pro jednotku. Místo toho je třeba restartovat relaci kopírování pomocí nového souboru deníku.

### <a name="resume-a-latest-interrupted-session"></a>Obnovte nejnovější dojde k přerušení relace

Pokud z nějakého důvodu dojde k přerušení relace kopírování, můžete ho obnovit spuštěním nástroje s zadaný pouze soubor deníku:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /ResumeSession
```

**Příklad:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /ResumeSession
```

> [!IMPORTANT] 
> Při obnovení relace kopie neměňte přidáním nebo odebráním soubory zdrojových datových souborů a adresářů.

## <a name="waimportexport-parameters"></a>Parametry WAImportExport

| Parametry | Popis |
| --- | --- |
|     /j:&lt;JournalFile&gt;  | **Požadované**<br/> Cesta k souboru deníku. Soubor deníku sadu jednotky sleduje a zaznamenává průběh při přípravě tyto disky. Je vždy třeba zadat soubor deníku.  |
|     /logdir:&lt;LogDirectory&gt;  | **Volitelné**. K adresáři protokolů.<br/> Podrobné soubory protokolů, jakož i některé dočasné soubory budou zapisovat do tohoto adresáře. Pokud není zadaný, aktuální adresář se použije jako adresář protokolu. Adresář protokolu lze zadat jen jednou u stejného souboru deníku.  |
|     /ID:&lt;SessionId&gt;  | **Požadované**<br/> Relace, které Id slouží k identifikaci relaci kopírování. Slouží k zajištění přesných obnovení kopírování dojde k přerušení relace.  |
|     / ResumeSession  | Volitelné. Pokud má poslední relace kopie byl ukončen neobvyklým způsobem, tento parametr lze obnovit relace.   |
|     / AbortSession  | Volitelné. Pokud má poslední relace kopie byl ukončen neobvyklým způsobem, tento parametr lze zadat k přerušení relace.  |
|     /sn:&lt;StorageAccountName&gt;  | **Požadované**<br/> Platí pouze pro RepairImport a RepairExport. Název účtu úložiště.  |
|     /Sk:&lt;StorageAccountKey&gt;  | **Požadované**<br/> Klíč účtu úložiště. |
|     / InitialDriveSet:&lt;driveset.csv&gt;  | **Požadované** při spuštění prvního kopírování relace<br/> Soubor CSV, který obsahuje seznam jednotek připravit.  |
|     / AdditionalDriveSet:&lt;driveset.csv&gt; | **Požadované**. Při přidávání disků do aktuální relace kopírování. <br/> Soubor CSV, který obsahuje seznam další jednotky, které chcete přidat.  |
|      / r:&lt;RepairFile&gt; | **Požadované** platí pouze pro RepairImport a RepairExport.<br/> Cesta k souboru pro sledování průběhu opravy. Každé jednotky, musí mít pouze jeden soubor opravit.  |
|     / d:&lt;TargetDirectories&gt; | **Požadované**. Platí pouze pro RepairImport a RepairExport. Pro RepairImport jeden nebo více oddělených středníkem adresáře k opravě; RepairExport, jeden adresář k opravě, například kořenový adresář jednotky.  |
|     / CopyLogFile:&lt;DriveCopyLogFile&gt; | **Požadované** platí pouze pro RepairImport a RepairExport. Cesta k souboru protokolu kopie disku (podrobný nebo chyba).  |
|     / ManifestFile:&lt;DriveManifestFile&gt; | **Požadované** platí pouze pro RepairExport.<br/> Cesta k souboru manifestu jednotky.  |
|     / PathMapFile:&lt;DrivePathMapFile&gt; | **Volitelné**. Platí pouze pro RepairImport.<br/> Cesta k souboru, který obsahuje mapování cesty k souborům relativní vůči kořenovému adresáři jednotky do umístění skutečné soubory (oddělený tabulátory). Pokud nejprve zadána, ho bude možné naplnit cesty k souborům s prázdnou cíle, to znamená buď nebyly nalezeny v TargetDirectories přístup odepřen s neplatným názvem, nebo existují v několika adresářích. Souboru s mapováním cestu můžete ručně upraveno pro správné cílové cesty zahrnutí a zadat znovu pro nástroj správně přeložit cesty k souborům.  |
|     / ExportBlobListFile:&lt;ExportBlobListFile&gt; | **Požadované**. Platí pouze pro PreviewExport.<br/> Cesta k souboru XML soubor obsahující seznam objektů blob cesty nebo objektu blob předpony cestu pro export objektů BLOB. Formát souboru je stejný jako formát objektů blob seznamu objektů blob v operaci Put úlohu importu/exportu služby REST API.  |
|     / DriveSize:&lt;DriveSize&gt; | **Požadované**. Platí pouze pro PreviewExport.<br/>  Velikost jednotek, který se má použít pro export. Například 500 GB, 1,5 TB. Poznámka: 1 GB = 1 000 000 000 bytes1 TB = 1,000,000,000,000 bajtů  |
|     Nebo datové sady:&lt;dataset.csv&gt; | **Požadované**<br/> Soubor CSV, který obsahuje seznam adresářů a/nebo seznam souborů ke zkopírování na cílové jednotky.  |
|     /silentmode  | **Volitelné**.<br/> Pokud není zadáno, bude poznáte, požadavek na jednotek a potřebovat vaše potvrzení pokračujte.  |

## <a name="tool-output"></a>Výstup nástroje

### <a name="sample-drive-manifest-file"></a>Ukázku souboru manifestu jednotky

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

### <a name="sample-journal-file-jrn-for-session-that-records-the-trail-of-sessions"></a>Ukázkový soubor deníku (JRN) pro relace, který zaznamenává záznam relací

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

Nástroj WAImportExport je jednotka přípravy a opravit nástroj, který můžete použít se službou Microsoft Azure Import/Export. Tento nástroj můžete použít ke zkopírování dat do pevné disky, které budete pro odeslání do datové centrum Azure. Po dokončení úlohy importu, můžete tento nástroj a opravte všechny objekty BLOB, které došlo k poškození, chyběl nebo konflikt s další objekty BLOB. Po obdržení jednotky z úlohy dokončené export, můžete tento nástroj a opravte všechny soubory, které byly poškozené nebo chybí na jednotkách.

#### <a name="how-does-the-waimportexport-tool-work-on-multiple-source-dir-and-disks"></a>Jak funguje nástroj WAImportExport u více dir zdroje a disky?

Pokud velikost dat je větší než velikost disku, nástroj WAImportExport distribuovat optimálního data mezi disky. Kopírování dat na několik disků lze provést v paralelní nebo postupně. Neexistuje žádné omezení počtu disky, které data je možné zapsat do současně. Nástroj provede distribuci dat na základě velikosti disku a velikost složky. Vybere disk, který je většina optimalizované pro velikost objektu. Data po odeslání k účtu úložiště bude konvergované zpět na strukturu zadaný adresář.

#### <a name="where-can-i-find-previous-version-of-waimportexport-tool"></a>Kde najdu předchozí verzi nástroje WAImportExport?

WAImportExport nástroj obsahuje všechny funkce, které měl nástroj WAImportExport V1. WAImportExport nástroj umožňuje uživatelům zadat více zdrojů a zapisovat do několika jednotkách. Kromě toho jeden, můžete snadno spravovat více zdrojových umístění, ze kterých data musí být zkopírovali v jednom souboru CSV. V případě však potřebujete na podporu SAS nebo chcete zkopírovat jeden zdroj na jeden disk, můžete [nástroj můžete stáhnout WAImportExport V1] (http://go.microsoft.com/fwlink/?LinkID=301900&amp;clcid = 0x409) a odkazovat na [WAImportExport V1 odkaz](storage-import-export-tool-how-to-v1.md) nápovědu k použití WAImportExport V1.

#### <a name="what-is-a-session-id"></a>Co je ID relace?

Nástroj očekává relace kopii (nebo id) parametr bude stejná, pokud je cílem rozprostření dat na více disků. Zachování stejný název relace kopie povolí uživatele ke zkopírování dat z jedné nebo více zdrojových umístění do jedné nebo více cílové disky nebo adresáře. Zachování stejné id relace umožňuje nástroje vyzvedne, až o kopírování souborů ze které bylo naposledy.

Stejné relace kopie však nelze použít k importu dat do jiného úložiště účtů.

Pokud název kopie relace je stejný napříč více spustí nástroj souboru protokolu (/ logdir) a klíč účtu úložiště (nebo sk) musí být stejné.

ID relace se může skládat z písmen, 0 ~ 9, understore (\_), pomlčky (-) nebo hodnotu hash (#), a jeho délka musí být 3 ~ 30.

například relace 1 nebo č. 1 nebo relaci\_1

#### <a name="what-is-a-journal-file"></a>Co je soubor deníku?

Pokaždé, když spustíte nástroj WAImportExport kopírovat soubory na pevný disk, nástroj vytvoří relaci kopírování. Stav relace kopírování se zapisují do souboru deníku. Pokud dojde k přerušení relaci kopírování (například kvůli výpadku napájení systému), můžete obnovit nástroj znovu spustit a zadáním deníku soubor na příkazovém řádku.

Pro každý pevný disk, který připravit pomocí nástroje Azure Import/Export, nástroj vytvoří deníku jeden soubor s názvem "&lt;ID jednotky&gt;.xml" kde jednotka Id je sériové číslo přidružené k na jednotku, která nástroj čtení z disku. Je nutné soubory deníku ze všech jednotky pro vytvoření úlohy importu. Soubor deníku lze také obnovit přípravy jednotky, pokud bude přerušen přívod nástroj.

#### <a name="what-is-a-log-directory"></a>Co je adresář protokolu?

Adresář protokolu Určuje adresář, který se má použít k uložení podrobné protokoly, jakož i dočasné soubory manifestu. Pokud není zadaný, aktuální adresář se použije jako adresář protokolu. Protokoly jsou podrobné protokoly.

### <a name="prerequisites"></a>Požadavky

#### <a name="what-are-the-specifications-of-my-disk"></a>Jaké jsou specifikace Moje disku?

Jeden nebo více prázdný 2,5 nebo 3,5 SATAII o rychlosti nebo III nebo SSD pevné disky připojené k počítači kopie.

#### <a name="how-can-i-enable-bitlocker-on-my-machine"></a>Jak můžete povolit nástroj BitLocker na můj počítač?

Jednoduchý způsob, jak zkontrolovat, je pravým tlačítkem myši na systémové jednotce. Zobrazí se možnosti pro nástroj Bitlocker Pokud je zapnutá možnost. Pokud je vypnuto, se nebude zobrazovat.

![Zkontrolujte nástroj BitLocker](./media/storage-import-export-tool-preparing-hard-drives-import/BitLocker.png)

Tady je článek na [jak zapnout BitLocker](https://technet.microsoft.com/library/cc766295.aspx)

Je možné, že váš počítač nemá čip TPM. Pokud se nezobrazí pomocí tpm.msc výstupu, podívejte se na další – nejčastější dotazy.

#### <a name="how-to-disable-trusted-platform-module-tpm-in-bitlocker"></a>Postup zakázání Trusted Platform Module (TPM) v nástroji BitLocker?
> [!NOTE]
> Pouze v případě, že je jejich serverech bez čipu TPM, musíte zakázat zásadu TPM. Není nutné zakázat TPM, pokud je v serveru uživatele důvěryhodné TPM. 
> 

Aby bylo možné zakázat TPM v nástroji BitLocker, projděte následující kroky:<br/>
1. Spusťte **Editor zásad skupiny** zadáním gpedit.msc na příkazovém řádku. Pokud **Editor zásad skupiny** pravděpodobně není k dispozici pro první povolení nástroje BitLocker. Viz předchozí – nejčastější dotazy.
2. Otevřete **zásady místního počítače &gt; konfigurace počítače &gt; šablony pro správu &gt; součásti systému Windows&gt; nástroj BitLocker Drive Encryption &gt; jednotky operačního systému**.
3. Upravit **vyžadovat další ověření při spuštění** zásad.
4. Nastavení zásady pro **povoleno** a zajistěte, aby **povolit nástroj BitLocker bez kompatibilním čipem TPM** je zaškrtnuté.

####  <a name="how-to-check-if-net-4-or-higher-version-is-installed-on-my-machine"></a>Jak zkontrolujte, zda je na počítači nainstalován .NET 4 nebo vyšší verzi?

V následujícím adresáři jsou nainstalovány všechny verze rozhraní Microsoft .NET Framework: %windir%\Microsoft.NET\Framework\

Přejděte do části výše uvedené ve vašem cílový počítač, kde je potřeba spustit nástroj. Vyhledejte název složky počínaje "v4". Neexistence takový adresář znamená, že v počítači není nainstalován .NET 4. Rozhraní .net 4 si můžete stáhnout na váš počítač pomocí [rozhraní Microsoft .NET Framework 4 (Webová instalační služba)](https://www.microsoft.com/download/details.aspx?id=17851).

### <a name="limits"></a>Omezení

#### <a name="how-many-drives-can-i-preparesend-at-the-same-time"></a>Kolik jednotek můžete I příprava nebo odeslání ve stejnou dobu?

Neexistuje žádné omezení počtu disky, které tento nástroj můžete připravit. Nástroj však očekává písmena jednotek jako vstupy. Který jej omezuje na 25 přípravy disků současně. Jediné úlohy může zpracovávat maximálně 10 disků současně. Pokud připravujete víc než 10 disky cílení na stejném účtu úložiště, disky mohou být distribuovány na více úloh.

#### <a name="can-i-target-more-than-one-storage-account"></a>Můžete vybrat více než jeden účet úložiště?

Pouze jeden účet úložiště jde odeslat na úlohu a v relaci jedna kopie.

### <a name="capabilities"></a>Možnosti

#### <a name="does-waimportexportexe-encrypt-my-data"></a>Moje data šifrovat WAImportExport.exe?

Ano. Šifrování nástrojem BitLocker je povolen a požadované pro tento proces.

#### <a name="what-will-be-the-hierarchy-of-my-data-when-it-appears-in-the-storage-account"></a>Co když se objeví v účtu úložiště bude hierarchii svá data?

I když distribuci dat mezi disky dat, když nahrán do účtu úložiště bude konvergované zpět do struktury adresářů zadaný v souboru CSV datovou sadu.

#### <a name="how-many-of-the-input-disks-will-have-active-io-in-parallel-when-copy-is-in-progress"></a>Kolik vstupu disky paralelně, bude mít aktivní vstupně-výstupní operace, když probíhá kopírování?

Nástroj rozděluje data mezi vstupní disky na základě velikosti vstupní soubory. Ale nutné dodat, počet active disků paralelně úplně delends na povaze vstupní data. V závislosti na velikosti jednotlivých souborů v vstupní datovou sadu může jeden nebo více disků zobrazovat active vstupně-výstupní operace paralelně. Najdete v části Další otázka další podrobnosti.

#### <a name="how-does-the-tool-distribute-the-files-across-the-disks"></a>Jak nástroj distribuovat soubory mezi disky?

Nástroj WAImportExport čte a zapisuje soubory batch batch, jeden batch obsahuje maximální počet souborů 100000. To znamená, že maximální 100000 lze zapisovat soubory současně. Více disků se zapisují do současně případě tyto 100000 soubory jsou distribuovány do více jednotek. Ale jestli nástroj zapisuje do více disků současně nebo na jediný disk závisí na kumulativní velikost dávky. Například v případě menších souborů, pokud jsou všechny soubory 10,0000 moct nevejde se do jedné jednotky, nástroj zapíše do jenom jeden disk během zpracování tuto dávku.

### <a name="waimportexport-output"></a>Výstup WAImportExport

#### <a name="there-are-two-journal-files-which-one-should-i-upload-to-azure-portal"></a>Existují dva soubory deníku, která by měla I nahrát do portálu Azure?

**.xml** – pro každý pevný disk, který připravit pomocí nástroje WAImportExport, nástroj vytvoří deníku jeden soubor s názvem `<DriveID>.xml` kde ID jednotky je sériové číslo přidružené k na jednotku, která nástroj čtení z disku. Je nutné soubory deníku ze všech jednotky pro vytvoření úlohy importu v portálu Azure. Tento soubor deníku lze také obnovit přípravy jednotky, pokud bude přerušen přívod nástroj.

**.jrn** -deníku soubor s příponou `.jrn` obsahuje stav pro všechny relace kopie pro pevný disk. Také obsahuje informace potřebné k vytvoření úlohy importu. Vždy musíte zadat soubor deníku při spuštění nástroje WAImportExport, jakož i ID relace. kopie

## <a name="next-steps"></a>Další kroky

* [Nastavení nástroje Azure Import/Export](storage-import-export-tool-setup.md)
* [Nastavení vlastností a metadat během procesu importu](storage-import-export-tool-setting-properties-metadata-import.md)
* [Ukázkový pracovní postup pro přípravu pevných disků pro úlohu importu](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
* [Stručná referenční příručka pro často používané příkazy](storage-import-export-tool-quick-reference.md) 
* [Kontrola stavu úlohy s použitím kopií souborů protokolu](storage-import-export-tool-reviewing-job-status-v1.md)
* [Oprava úlohy importu](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Oprava úlohy exportu](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Řešení potíží s nástrojem Azure pro import/export](storage-import-export-tool-troubleshooting-v1.md)
