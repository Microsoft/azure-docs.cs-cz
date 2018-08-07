---
title: Příprava pevných disků pro úlohu importu Azure Import/Export - v1 | Dokumentace Microsoftu
description: Zjistěte, jak pro přípravu pevných disků v nástroji v1 WAImportExport k vytvoření úlohy importu pro službu Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 861b3302e065689a4ea9c0df0879f9c0df12e619
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526942"
---
# <a name="preparing-hard-drives-for-an-import-job"></a>Příprava pevných disků pro úlohu importu
Pro přípravu jeden nebo více pevných disků pro úlohu importu, postupujte podle těchto kroků:

-   Identifikovat data, která mají naimportovat do služby Blob service

-   Určení cílové virtuální adresáře a objekty BLOB ve službě Blob service

-   Určit, kolik jednotek, které budete potřebovat

-   Kopírovat data na všech pevných disků

 Ukázkový pracovní postup v tématu [ukázkový pracovní postup pro přípravu pevných disků pro úlohu importu](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md).

## <a name="identify-the-data-to-be-imported"></a>Identifikovat data, která mají být importována
 Prvním krokem k vytvoření úlohy importu je určit, které adresářů a souborů, které chcete importovat. To může být seznam adresářů, seznam jedinečných souborů nebo kombinaci těchto dvou. Po zahrnuté adresáře se budou všechny soubory v adresáři a jeho podadresářích součástí úlohy importu.

> [!NOTE]
>  Jelikož podadresářích jsou zahrnuty rekurzivně, když nadřazený adresář je součástí, zadejte nadřazený adresář. Také neurčují žádná z jeho podadresářů.
>
>  V současné době nástroje Microsoft Azure Import/Export má následující omezení: jestli adresář obsahuje více dat, než může obsahovat pevného disku, pak adresář je potřeba rozdělit na menší adresáře. Například pokud adresář obsahuje 2,5 TB dat a kapacita pevného disku je pouze 2TB, musíte přerušit menší adresáře adresáři 2,5 TB. Toto omezení bude vyřešen v pozdější verzi nástroje.

## <a name="identify-the-destination-locations-in-the-blob-service"></a>Určit cílové umístění ve službě blob service
 Pro každý adresář nebo soubor, který bude importován je potřeba identifikovat cílový virtuální adresář nebo objektů blob ve službě Azure Blob service. Tyto cíle se použít jako vstupy nástroje Import/Export Azure. Všimněte si, že by měl být adresáře oddělené znakem lomítka "/".

 V následující tabulce jsou uvedeny příklady cílů objektů blob:

|Zdrojový soubor nebo adresář|Cílový objekt blob nebo virtuálního adresáře|
|------------------------------|-------------------------------------------|
|H:\Video|https://mystorageaccount.blob.core.windows.net/video|
|H:\Photo|https://mystorageaccount.blob.core.windows.net/photo|
|K:\Temp\FavoriteVideo.ISO|https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO|
|\\\myshare\john\music|https://mystorageaccount.blob.core.windows.net/music|

## <a name="determine-how-many-drives-are-needed"></a>Určit, kolik jednotek jsou potřeba.
 Dále je třeba určit:

-   Počet pevných disků potřebě ukládat data.

-   Adresářů a/nebo samostatné soubory, které budou zkopírovány do všech pevném disku.

 Ujistěte se, že máte počet pevných disků, které potřebujete k ukládání dat, která jsou přenosu.

## <a name="copy-data-to-your-hard-drive"></a>Kopírování dat na pevném disku
 Tato část popisuje, jak volat nástrojem Import/Export Azure ke zkopírování dat na jeden nebo více pevných disků. Pokaždé, když voláte nástroje Import/Export Azure, vytvoříte nový *zkopírujte relace*. Vytvoření relace alespoň jedna kopie pro každou jednotku, ke kterému je zkopírovat data. v některých případech můžete potřebovat více než jedna relace kopírování pro kopírování všech vašich dat na jeden disk. Zde jsou některé důvody, že potřebujete více relací kopírování:

-   Je třeba vytvořit samostatná kopie relace pro každou jednotku, kterou chcete zkopírovat.

-   Kopírování relace můžete zkopírovat jeden adresář nebo jeden objekt blob na jednotku. Pokud kopírujete více adresářů, víc objektů BLOB nebo kombinaci obou, bude nutné vytvořit více relací kopírování.

-   Můžete určit vlastnosti a metadata, která bude nastavena na objekty BLOB importován jako součást úlohy importu. Vlastnosti nebo metadata, která zadáte pro relaci kopie bude vztahovat na všechny objekty BLOB v určeném kopírování relace. Pokud chcete určit různé vlastnosti nebo metadata pro některé objekty BLOB, budete muset vytvořit relaci samostatná kopie. Zobrazit [nastavení vlastností a metadat během procesu importu](storage-import-export-tool-setting-properties-metadata-import-v1.md)Další informace.

> [!NOTE]
>  Pokud máte víc počítačů, které splňují požadavky uvedené v [nastavení si Azure Import/Export nástroj](storage-import-export-tool-setup-v1.md), může kopírovat data na několik pevných disků současně spuštěním instance tohoto nástroje na každém počítači.

 Pro každý pevný disk připravte pomocí nástroje Azure Import/Export nástroj vytvoří soubor jeden deníku. Budete potřebovat soubory deníku ze všech jednotek k vytvoření úlohy importu. Soubor deníku lze také obnovit Příprava jednotky, pokud dojde k přerušení nástroj.

### <a name="azure-importexport-tool-syntax-for-an-import-job"></a>Azure syntaxe nástroje Import/Export pro úlohu importu
 Chcete-li přípravu disků pro úlohu importu, zavolejte nástroj Azure Import/Export s **PrepImport** příkazu. Parametry, které zahrnete, závisí na, zda je toto první relace kopie nebo kopie následné relace.

 První relace kopírování pro jednotku vyžaduje některé další parametry, zadejte klíč účtu úložiště; cíl písmeno jednotky. Určuje, zda musí být ve formátu jednotka; zda jednotka musí být zašifrován a pokud ano, klíč Bitlockeru; a adresář protokolu. Tady je syntaxe pro relaci počáteční kopii ke zkopírování do adresáře nebo do jednoho souboru:

 **Nejprve zkopírovat relace zkopírovat jeden adresář**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Nejprve zkopírovat relace zkopírovat jeden soubor**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 V další kopie relace není potřeba zadat počáteční parametry. Tady je syntaxe pro následné kopie relaci ke zkopírování do adresáře nebo do jednoho souboru:

 **Následné kopie relace zkopírovat jeden adresář**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Následné kopie relace zkopírovat jeden soubor**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

### <a name="parameters-for-the-first-copy-session-for-a-hard-drive"></a>Parametry pro první kopie relace pro pevný disk
 Pokaždé, když spustíte nástroj Azure Import/Export kopírovat soubory na pevný disk, nástroj vytvoří relaci kopírování. Každá relace kopírování kopíruje jeden adresář nebo jeden soubor na pevný disk. Stav relace kopírování je zapsán do souboru deníku. Pokud dojde k přerušení relace kopie (například kvůli výpadku napájení systému), můžete obnovit nástroj znovu spustit a zadáním soubor deníku, na příkazovém řádku.

> [!WARNING]
>  Pokud zadáte **/formátování** parametr pro první relace kopírování jednotka bude naformátováno a všechna data na disku se vymaže. Doporučuje se, že používáte prázdné disky pouze pro relaci kopírování.

 Příkaz pro první kopie relace pro každou jednotku vyžaduje jiné parametry než příkazy pro následné kopie relace. V následující tabulce jsou uvedeny další parametry, které jsou k dispozici pro první kopie relace:

|Parametr příkazového řádku|Popis|
|-----------------------------|-----------------|
|**/Sk:**< StorageAccountKey\>|`Optional.` Klíč účtu úložiště pro účet úložiště, do kterého se data naimportují. Musí obsahovat buď **/sk:**< StorageAccountKey\> nebo **/csas:**< ContainerSas\> v příkazu.|
|**/csas:**< ContainerSas\>|`Optional`. Kontejner SAS použijte pro import dat do účtu úložiště. Musí obsahovat buď **/sk:**< StorageAccountKey\> nebo **/csas:**< ContainerSas\> v příkazu.<br /><br /> Hodnota tohoto parametru musí začínat název kontejneru, za nímž následuje otazník (?) a tokenu SAS. Příklad:<br /><br /> `mycontainer?sv=2014-02-14&sr=c&si=abcde&sig=LiqEmV%2Fs1LF4loC%2FJs9ZM91%2FkqfqHKhnz0JM6bqIqN0%3D&se=2014-11-20T23%3A54%3A14Z&sp=rwdl`<br /><br /> Oprávnění, zda zadaný na adresu URL nebo uložené zásady přístupu, musí zahrnovat čtení, zápis a odstranění pro úlohy importu a pro čtení, zápisu a seznamu pro úlohy exportu.<br /><br /> Pokud tento parametr zadán, všechny objekty BLOB na importovaná nebo exportovaná musí být v rámci zadané v sdílený přístupový podpis kontejneru.|
|**t:**< TargetDriveLetter\>|`Required.` Písmeno jednotky pevný disk cílového pro aktuální relaci kopie bez koncové dvojtečka.|
|**všechny**|`Optional.` Tento parametr zadán, když se jednotka musí být ve formátu; v opačném případě ji vynechte. Předtím, než nástroj formáty jednotky, se zobrazí výzva k potvrzení z konzoly. Chcete-li potlačit potvrzení, zadejte parametr /silentmode.|
|**/silentmode**|`Optional.` Zadejte tento parametr můžete potlačit potvrzení pro formátování targert jednotky.|
|**/ šifrování**|`Optional.` Tento parametr zadaný při jednotka nebyl dosud zašifruje pomocí Bitlockeru a musí být šifrované nástrojem. Pokud jednotka již byla zašifrována pomocí Bitlockeru, pak tento parametr vynecháte a `/bk` parametr poskytuje existující klíč Bitlockeru.<br /><br /> Pokud zadáte `/format` parametr, pak je nutné zadat také `/encrypt` parametru.|
|**/BK:**< BitLockerKey\>|`Optional.` Pokud `/encrypt` je tento parametr zadán, vynechat. Pokud `/encrypt` je tento parametr vynechán, je třeba již mít šifrované jednotky pomocí Bitlockeru. Tento parametr použijte k určení klíče Bitlockeru. Šifrování nástrojem BitLocker je vyžadován pro všechny pevné disky pro úlohy importu.|
|**/logdir:**< LogDirectory\>|`Optional.` Adresář protokolu Určuje adresář, který se má použít k ukládání podrobné protokoly, stejně jako dočasné soubory manifestu. Pokud není zadán, použije aktuální adresář jako adresář protokolu.|

### <a name="parameters-required-for-all-copy-sessions"></a>Parametry požadované pro všechny relace kopírování
 Soubor deníku obsahuje stav pro všechny relace kopírování pro pevný disk. Také obsahuje informace potřebné k vytvoření úlohy importu. Vždy je nutné zadat soubor deníku při spuštění nástroje Import/Export Azure, stejně jako kopie ID relace:

|||
|-|-|
|Parametr příkazového řádku|Popis|
|**/j:**< JournalFile\>|`Required.` Cesta k souboru deníku. Každá jednotka musí mít přesně jeden soubor deníku. Všimněte si, že soubor deníku nesmí být na cílové jednotce. Přípona souboru deníku `.jrn`.|
|**/ID:**< SessionId\>|`Required.` ID relace identifikuje kopírování relace. Používá se k zajištění přesných obnovení relace přerušené kopírování. Soubory, které jsou zkopírovány v relaci kopie jsou uloženy v adresáři s názvem po ID relace na cílové jednotce.|

### <a name="parameters-for-copying-a-single-directory"></a>Parametry pro kopírování jeden adresář
 Při kopírování jeden adresář, platí následující povinných a volitelných parametrů:

|Parametr příkazového řádku|Popis|
|----------------------------|-----------------|
|**/srcdir:**< SourceDirectory\>|`Required.` Zdrojový adresář, který obsahuje soubory, které se mají zkopírovat na cílový disk. Cesta k adresáři musí být absolutní cesta (nikoli relativní cestu).|
|**/dstdir:**< DestinationBlobVirtualDirectory\>|`Required.` Cesta k cílové virtuální adresář ve vašem účtu úložiště Windows Azure. Virtuální adresář může nebo nemusí již existují.<br /><br /> Můžete zadat kontejner nebo objekt blob předpony, jako jsou `music/70s/`. Cílový adresář musí začínat název kontejneru, za nímž následuje lomítko "/" a může volitelně zahrnovat blob virtuální adresář, který končí na "/".<br /><br /> Pokud cílový kontejner je kořenový kontejner, je nutné explicitně zadat kořenový kontejner, včetně lomítkem, jako `$root/`. Protože objekty BLOB v kořenovém kontejneru nemůže obsahovat "/" v jejich názvy, nebude všech podadresářích ve zdrojovém adresáři kopírovat, pokud cílový adresář je kořenový kontejner.<br /><br /> Nezapomeňte použít názvy platný kontejner při zadání cílové virtuální adresáře nebo objekty BLOB. Uvědomte si, že názvy kontejnerů musí obsahovat malá písmena. Pravidla pojmenování kontejneru, naleznete v tématu [pojmenování a odkazování na kontejnerů, objektů BLOB a metadat](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).|
|**/ Dispozice:**< přejmenovat&#124;přepsat bez&#124;přepsat >|`Optional.` Určuje chování při objekt blob se zadanou adresou už existuje. Platné hodnoty tohoto parametru jsou: `rename`, `no-overwrite` a `overwrite`. Všimněte si, že tyto hodnoty jsou malá a velká písmena. Pokud není zadána žádná hodnota, výchozí hodnota je `rename`.<br /><br /> Hodnota zadaná pro tento parametr má vliv na všechny soubory v adresáři určeném argumentem `/srcdir` parametru.|
|**/ BlobType:**< BlockBlob&#124;PageBlob >|`Optional.` Určuje typ objektu blob pro cílové objektů BLOB. Platné hodnoty jsou: `BlockBlob` a `PageBlob`. Všimněte si, že tyto hodnoty jsou malá a velká písmena. Pokud není zadána žádná hodnota, výchozí hodnota je `BlockBlob`.<br /><br /> Ve většině případů `BlockBlob` se doporučuje. Pokud zadáte `PageBlob`, délka každého souboru v adresáři musí být násobkem hodnoty 512, velikost stránky pro objekty BLOB stránky.|
|**/ PropertyFile:**< PropertyFile\>|`Optional.` Cesta k souboru vlastnost cílové objektů BLOB. Zobrazit [metadat a formát souboru vlastností služby Import/Export](../storage-import-export-file-format-metadata-and-properties.md) Další informace.|
|**/ MetadataFile:**< MetadataFile\>|`Optional.` Cesta k souboru metadat pro cílové objektů BLOB. Zobrazit [metadat a formát souboru vlastností služby Import/Export](../storage-import-export-file-format-metadata-and-properties.md) Další informace.|

### <a name="parameters-for-copying-a-single-file"></a>Parametry pro kopírování do jednoho souboru
 Při kopírování jeden soubor, platí následující povinných a volitelných parametrů:

|Parametr příkazového řádku|Popis|
|----------------------------|-----------------|
|**/srcfile:**< zdrojový soubor\>|`Required.` Úplná cesta k souboru, který má být zkopírován. Cesta k adresáři musí být absolutní cesta (nikoli relativní cestu).|
|**/dstblob:**< DestinationBlobPath\>|`Required.` Cesta k cílové objektů blob v účtu úložiště Windows Azure. Objekt blob může nebo nemusí již existují.<br /><br /> Zadejte počáteční název objektu blob s názvem kontejneru. Název objektu blob nesmí začínat "/" nebo název účtu úložiště. Pravidla pojmenování objektů blob najdete v části [pojmenování a odkazování na kontejnerů, objektů BLOB a metadat](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).<br /><br /> Pokud cílový kontejner je kořenový kontejner, je nutné explicitně zadat `$root` jako kontejner, jako například `$root/sample.txt`. Všimněte si, že v kořenovém kontejneru objektů BLOB nemůže obsahovat "/" v názvu.|
|**/ Dispozice:**< přejmenovat&#124;přepsat bez&#124;přepsat >|`Optional.` Určuje chování při objekt blob se zadanou adresou už existuje. Platné hodnoty tohoto parametru jsou: `rename`, `no-overwrite` a `overwrite`. Všimněte si, že tyto hodnoty jsou malá a velká písmena. Pokud není zadána žádná hodnota, výchozí hodnota je `rename`.|
|**/ BlobType:**< BlockBlob&#124;PageBlob >|`Optional.` Určuje typ objektu blob pro cílové objektů BLOB. Platné hodnoty jsou: `BlockBlob` a `PageBlob`. Všimněte si, že tyto hodnoty jsou malá a velká písmena. Pokud není zadána žádná hodnota, výchozí hodnota je `BlockBlob`.<br /><br /> Ve většině případů `BlockBlob` se doporučuje. Pokud zadáte `PageBlob`, délka každého souboru v adresáři musí být násobkem hodnoty 512, velikost stránky pro objekty BLOB stránky.|
|**/ PropertyFile:**< PropertyFile\>|`Optional.` Cesta k souboru vlastnost cílové objektů BLOB. Zobrazit [metadat a formát souboru vlastností služby Import/Export](../storage-import-export-file-format-metadata-and-properties.md) Další informace.|
|**/ MetadataFile:**< MetadataFile\>|`Optional.` Cesta k souboru metadat pro cílové objektů BLOB. Zobrazit [metadat a formát souboru vlastností služby Import/Export](../storage-import-export-file-format-metadata-and-properties.md) Další informace.|

### <a name="resuming-an-interrupted-copy-session"></a>Pokračování v relaci přerušené kopírování
 Pokud z nějakého důvodu dojde k přerušení relace kopírování, můžete ho obnovit pomocí zadaný pouze soubor deníku spustí nástroj:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession
```

 Pouze poslední relace kopírovat, pokud byl ukončen neobvyklým způsobem, lze obnovit.

> [!IMPORTANT]
>  Když budete pokračovat v kopírování relace, neprovádějte žádné změny zdrojových datových souborů a adresářů přidáním nebo odebráním soubory.

### <a name="aborting-an-interrupted-copy-session"></a>Přerušování relaci přerušené kopírování
 Pokud dojde k přerušení relace kopírovat a není možné obnovit (např. Pokud zdrojový adresář dokázaly nedostupná), musíte zrušit aktuální relaci, tak, že může být vrácena zpět a dá se spouštět nové kopie relace:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession
```

 Pouze poslední relace kopírovat, pokud byl ukončen neobvyklým způsobem, může být přerušena. Všimněte si, že není možné přerušit prvního kopírování relace pro jednotku. Místo toho je nutné restartovat relaci kopírování se nový soubor deníku.

## <a name="next-steps"></a>Další postup

* [Nastavení nástroje Azure Import/Export](storage-import-export-tool-setup-v1.md)
* [Nastavení vlastností a metadat během procesu importu](storage-import-export-tool-setting-properties-metadata-import-v1.md)
* [Ukázkový pracovní postup pro přípravu pevných disků pro úlohu importu](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
* [Stručná referenční příručka pro často používané příkazy](storage-import-export-tool-quick-reference-v1.md) 
* [Kontrola stavu úlohy s použitím kopií souborů protokolu](storage-import-export-tool-reviewing-job-status-v1.md)
* [Oprava úlohy importu](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Oprava úlohy exportu](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Řešení potíží s nástrojem Azure pro import/export](storage-import-export-tool-troubleshooting-v1.md)
