---
title: Příprava pevné disky pro úlohy importu Azure Import/Export - v1 | Microsoft Docs
description: Zjistěte, jak připravit pevných disků v nástroji v1 WAImportExport k vytvoření úlohy importu do služby Azure Import/Export.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 3d818245-8b1b-4435-a41f-8e5ec1f194b2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 361e16262e528c7dea1bab4b9d945a28af8be399
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874091"
---
# <a name="preparing-hard-drives-for-an-import-job"></a>Příprava pevných disků pro úlohu importu
Příprava jeden nebo více pevných disků pro úlohy importu, postupujte takto:

-   Identifikovat data určená k importu do služby objektů Blob

-   Určete cíl virtuální adresáře a objekty BLOB ve službě Blob

-   Určit, kolik jednotek, které budete potřebovat

-   Zkopírujte data do jednotlivých pevných disků

 Ukázkový pracovní postup, najdete v části [ukázkový pracovní postup k přípravě pevné disky pro úlohy importu](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md).

## <a name="identify-the-data-to-be-imported"></a>Identifikaci dat určených k importu
 Prvním krokem k vytvoření úlohy importu je určit, které adresářů a souborů, které chcete importovat. To může být seznam adresářů, seznam souborů, jedinečné nebo kombinaci těchto dvou. Po zahrnuté adresáře se budou všechny soubory v adresáři a jejích podadresářů součástí úlohy importu.

> [!NOTE]
>  Vzhledem k tomu, že nadřazený adresář je zahrnuté podadresáře jsou zahrnuty rekurzivně, zadejte pouze nadřazený adresář. Také nezadávejte žádné z jejích podadresářů.
>
>  V současné době nástroj Microsoft Azure Import/Export má následující omezení: Pokud adresář obsahuje více dat, než může obsahovat pevného disku, pak adresář musí rozdělit na menší adresáře. Například pokud adresář obsahuje 2,5 TB dat a kapacita pevného disku je pouze 2TB, pak budete muset rozdělit na menší adresáře adresáři 2,5 TB. Toto omezení bude vyřešen v novější verzi nástroje.

## <a name="identify-the-destination-locations-in-the-blob-service"></a>Určení cílového umístění v rámci služby objektů blob
 Pro každý adresář nebo soubor, který bude importován musíte určit cílový virtuální adresář nebo objekt blob ve službě Azure Blob. Tyto cíle použije jako vstupy pro nástroj Azure Import/Export. Všimněte si, že adresáře musí být oddělen s lomítkem znakem "/".

 V následující tabulce jsou uvedeny příklady cílů objektů blob:

|Zdrojový soubor nebo adresář|Cílový objekt blob nebo virtuální adresář|
|------------------------------|-------------------------------------------|
|H:\Video|https://mystorageaccount.BLOB.Core.Windows.NET/video|
|H:\Photo|https://mystorageaccount.BLOB.Core.Windows.NET/Photo|
|K:\Temp\FavoriteVideo.ISO|https://mystorageaccount.BLOB.Core.Windows.NET/Favorite/FavoriteVideo.ISO|
|\\\myshare\john\music|https://mystorageaccount.BLOB.Core.Windows.NET/Music|

## <a name="determine-how-many-drives-are-needed"></a>Určit, kolik jednotek jsou potřeba.
 Dále je nutné určit:

-   Počet pevných disků potřebě ukládat data.

-   Adresáře nebo samostatné soubory, které se zkopírují na každý z pevného disku.

 Ujistěte se, že máte počet pevných disků, které potřebujete k ukládání dat, který přenášíte.

## <a name="copy-data-to-your-hard-drive"></a>Kopírování dat na pevném disku
 Tato část popisuje, jak volat nástroj Azure Import/Export ke kopírování dat na jeden nebo více pevných disků. Pokaždé, když zavoláte nástroj Azure Import/Export, můžete vytvořit nový *zkopírujte relace*. Vytvoření relace alespoň jedna kopie pro každou jednotku, ke kterému je zkopírovat data; v některých případech může být nutné více než jedna relace kopírování ke kopírování všech dat na jednu jednotku. Zde jsou některé důvody, bude pravděpodobně vyžadovat více relací kopie:

-   Je třeba vytvořit relaci samostatná kopie pro každou jednotku, kterou chcete zkopírovat.

-   Kopírování relace můžete zkopírovat jeden adresář nebo jediného objektu blob na jednotku. Pokud kopírujete několik adresářů, více objektů BLOB nebo jejich kombinaci, budete muset vytvořit více relací kopírování.

-   Můžete zadat vlastnosti a metadata, která bude nastavena na objekty BLOB importován jako součást úlohy importu. Vlastnosti nebo metadata, která zadáte pro relaci a kopírování bude použita pro určeného této relaci kopie všech objektů BLOB. Pokud chcete určit různé vlastnosti nebo metadata pro některé objekty BLOB, budete muset vytvořit relaci samostatná kopie. V tématu [vlastnosti nastavení a metadat během procesu importu](storage-import-export-tool-setting-properties-metadata-import-v1.md)Další informace.

> [!NOTE]
>  Pokud máte více počítačů, které splňují požadavky uvedené v [nastavení si Azure Import/Export nástroj](storage-import-export-tool-setup-v1.md), spuštěním instance tohoto nástroje na každý počítač může kopírovat data do více pevných disků současně.

 Pro každý pevný disk, který připravit pomocí nástroje Azure Import/Export nástroj vytvoří soubor jednoho deníku. Je nutné soubory deníku ze všech jednotky pro vytvoření úlohy importu. Soubor deníku lze také obnovit přípravy jednotky, pokud bude přerušen přívod nástroj.

### <a name="azure-importexport-tool-syntax-for-an-import-job"></a>Syntaxe Azure Import/Export nástroje pro úlohy importu
 Příprava jednotky pro úlohy importu, volání nástroje Azure Import/Export s **PrepImport** příkaz. Parametry, které zahrnete závisí na tom, jestli je to první kopie relaci nebo relaci další kopie.

 První relaci kopie pro jednotku vyžaduje některé další parametry zadat klíč účtu úložiště; písmeno jednotky cíl; jestli musí být ve formátu jednotka; ať už jednotka musí být zašifrovaný a pokud ano, klíč nástroje BitLocker; a adresář protokolu. Tady je syntaxe pro relaci počáteční kopii pro kopírování adresářů nebo jenom jeden soubor:

 **Nejdříve zkopírovat relace zkopírovat jeden adresář**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Nejdříve zkopírovat relace zkopírovat jeden soubor**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 V další kopie relace není potřeba zadat počáteční parametry. Tady je syntaxe pro relaci a následné kopírování ke kopírování adresářů nebo jenom jeden soubor:

 **Relace následné kopírování zkopírovat jeden adresář**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Další kopie relací zkopírovat jeden soubor**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

### <a name="parameters-for-the-first-copy-session-for-a-hard-drive"></a>Parametry pro první relaci kopie pro pevný disk
 Pokaždé, když spustíte nástroj Azure Import/Export kopírovat soubory na pevný disk, nástroj vytvoří relaci kopírování. Každou relaci kopie zkopíruje na pevný disk do jednoho adresáře nebo jeden soubor. Stav relace kopírování se zapisují do souboru deníku. Pokud dojde k přerušení relaci kopírování (například kvůli výpadku napájení systému), můžete obnovit nástroj znovu spustit a zadáním deníku soubor na příkazovém řádku.

> [!WARNING]
>  Pokud zadáte **/formátu** parametr pro první relaci kopírování, jednotka se bude formátovat a vymažou se všechna data na disku. Doporučuje se, že používáte prázdné disky pouze pro relaci kopírování.

 Příkaz pro první relaci kopie pro každou jednotku vyžaduje různé parametry než příkazy pro kopírování následné relace. Následující tabulka uvádí další parametry, které jsou k dispozici pro první relaci kopie:

|Parametr příkazového řádku|Popis|
|-----------------------------|-----------------|
|**/Sk:**< StorageAccountKey\>|`Optional.`Klíč účtu úložiště pro účet úložiště, do kterého se data importovat. Musí obsahovat buď **/sk:**< StorageAccountKey\> nebo **/csas:**< ContainerSas\> v příkazu.|
|**/csas:**< ContainerSas\>|`Optional`. Kontejner SAS sloužící k importování dat do účtu úložiště. Musí obsahovat buď **/sk:**< StorageAccountKey\> nebo **/csas:**< ContainerSas\> v příkazu.<br /><br /> Hodnota tohoto parametru musí začínat název kontejneru, za nímž následuje otazník (?) a tokenu SAS. Například:<br /><br /> `mycontainer?sv=2014-02-14&sr=c&si=abcde&sig=LiqEmV%2Fs1LF4loC%2FJs9ZM91%2FkqfqHKhnz0JM6bqIqN0%3D&se=2014-11-20T23%3A54%3A14Z&sp=rwdl`<br /><br /> Oprávnění, zda zadaný na adresu URL nebo v zásadách uložené přístupu, musí obsahovat pro čtení, zápisu a odstraňování pro import úlohy a pro čtení, zápisu a seznamu pro úlohy exportu.<br /><br /> Pokud tento parametr zadán, všech objektů blob pro import nebo export musí být zadaný v sdílený přístupový podpis kontejneru uvnitř tohoto kontejneru.|
|**/ t:**< TargetDriveLetter\>|`Required.`Písmeno jednotky pevného disku cílového pro aktuální relaci kopírování, bez koncové dvojtečkou.|
|**/ Format**|`Optional.`Zadejte tento parametr, pokud jednotka musí být ve formátu; jinak vynechejte. Předtím, než nástroj formáty jednotku, se zobrazí výzva k potvrzení z konzoly. Chcete-li potlačit potvrzení, zadejte parametr /silentmode.|
|**/silentmode**|`Optional.`Zadejte tento parametr k potlačení potvrzení pro formátování targert jednotku.|
|**/ šifrování**|`Optional.`Tento parametr zadán, při jednotka nebyla dosud pomocí Bitlockeru šifrovat a musí být šifrované nástrojem. Pokud jednotka již byla zašifrována pomocí nástroje BitLocker, parametr vynechejte a zadejte `/bk` parametr poskytování existující klíč nástroje BitLocker.<br /><br /> Pokud zadáte `/format` parametr a potom musíte zadat také `/encrypt` parametr.|
|**/BK:**< BitLockerKey\>|`Optional.`Pokud `/encrypt` je tento parametr zadán, vynechejte. Pokud `/encrypt` je tento parametr vynechán, musíte mít již mít šifrované jednotky pomocí nástroje BitLocker. Tento parametr použijte k určení klíče nástroje BitLocker. Šifrování nástrojem BitLocker je vyžadována pro všechny pevné disky pro úlohy importu.|
|**/logdir:**< LogDirectory\>|`Optional.`Adresář protokolu Určuje adresář, který se má použít k uložení podrobné protokoly, jakož i dočasné soubory manifestu. Pokud není zadaný, aktuální adresář se použije jako adresář protokolu.|

### <a name="parameters-required-for-all-copy-sessions"></a>Parametrů požadovaných pro všechny relace kopie
 Soubor deníku obsahuje stav pro všechny relace kopie pro pevný disk. Také obsahuje informace potřebné k vytvoření úlohy importu. Vždy je nutné zadat soubor deníku při spuštění nástroje Azure Import/Export, jakož i ID relace kopie:

|||
|-|-|
|Parametr příkazového řádku|Popis|
|**/j:**< JournalFile\>|`Required.`Cesta k souboru deníku. Každé jednotky, musí mít přesně jeden soubor deníku. Všimněte si, že soubor deníku nesmí nacházet na cílové jednotce. Přípona souboru deníku `.jrn`.|
|**/ID:**< ID relace\>|`Required.`ID relace identifikuje relaci kopírování. Slouží k zajištění přesných obnovení kopírování dojde k přerušení relace. Soubory, které jste zkopírovali v relaci kopie jsou uloženy v adresáři s názvem po ID relace na cílové jednotce.|

### <a name="parameters-for-copying-a-single-directory"></a>Parametry pro kopírování jeden adresář
 Při kopírování jeden adresář, platí následující požadované a volitelné parametry:

|Parametr příkazového řádku|Popis|
|----------------------------|-----------------|
|**/srcdir:**< SourceDirectory\>|`Required.`Zdrojový adresář, který obsahuje soubory budou zkopírovány do cílové jednotky. Cesta k adresáři musí být absolutní cesta (není relativní cesta).|
|**/dstdir:**< DestinationBlobVirtualDirectory\>|`Required.`Cesta k cílové virtuální adresář ve vašem účtu úložiště služby Windows Azure. Virtuální adresář může nebo již neexistuje.<br /><br /> Můžete zadat kontejner, nebo jako předpona objektu blob `music/70s/`. Cílový adresář musí začínat název kontejneru, za nímž následuje lomítkem "/" a může volitelně obsahovat blob virtuální adresáře, který končí textem "/".<br /><br /> Pokud cílový kontejner je kořenový kontejner, je třeba explicitně zadat kořenový kontejner, včetně lomítkem, jako `$root/`. Vzhledem k tomu, že nesmí obsahovat objekty BLOB v kořenovém kontejneru "/" v jejich názvy, pokud cílový adresář je kořenový kontejner se nezkopírují jakéhokoliv podadresáře v adresáři zdroje.<br /><br /> Je nutné použít platný kontejner názvy při zadávání cílové virtuální adresáře nebo objekty BLOB. Uvědomte si, že názvy kontejnerů musí být malými písmeny. Pravidla pojmenování kontejneru, najdete v části [pojmenování a odkazování na kontejnerů, objektů BLOB a metadat](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).|
|**/ Dispozice:**< přejmenovat &#124; ne přepsat &#124; přepsání >|`Optional.`Určuje chování, když objekt blob se zadanou adresou už existuje. Platné hodnoty tohoto parametru jsou: `rename`, `no-overwrite` a `overwrite`. Všimněte si, že jsou tyto hodnoty malá a velká písmena. Pokud není zadaná žádná hodnota, výchozí hodnota je `rename`.<br /><br /> Hodnota zadaná pro tento parametr ovlivňuje všechny soubory v adresáři určeného `/srcdir` parametr.|
|**/ BlobType:**< BlockBlob &#124; PageBlob >|`Optional.`Určuje typ objektu blob pro cílové objekty BLOB. Platné hodnoty jsou: `BlockBlob` a `PageBlob`. Všimněte si, že jsou tyto hodnoty malá a velká písmena. Pokud není zadaná žádná hodnota, výchozí hodnota je `BlockBlob`.<br /><br /> Ve většině případů `BlockBlob` se doporučuje. Pokud zadáte `PageBlob`, délka každý soubor v adresáři musí být násobkem 512, velikost stránky pro objekty BLOB stránky.|
|**/ PropertyFile:**< PropertyFile\>|`Optional.`Cesta k souboru vlastností pro cílové objekty BLOB. V tématu [importu/exportu služby Metadata a formát vlastností souboru](../storage-import-export-file-format-metadata-and-properties.md) Další informace.|
|**/ MetadataFile:**< MetadataFile\>|`Optional.`Cesta k souboru metadat pro cílové objekty BLOB. V tématu [importu/exportu služby Metadata a formát vlastností souboru](../storage-import-export-file-format-metadata-and-properties.md) Další informace.|

### <a name="parameters-for-copying-a-single-file"></a>Parametry pro kopírování jeden soubor
 Při kopírování jeden soubor, platí následující požadované a volitelné parametry:

|Parametr příkazového řádku|Popis|
|----------------------------|-----------------|
|**/srcfile:**< zdrojový soubor\>|`Required.`Úplná cesta k souboru, který se má zkopírovat. Cesta k adresáři musí být absolutní cesta (není relativní cesta).|
|**/dstblob:**< DestinationBlobPath\>|`Required.`Cesta k cílový objekt blob v účtu úložiště služby Windows Azure. Objekt blob může nebo již neexistuje.<br /><br /> Zadejte počáteční název objektu blob s názvem kontejneru. Název objektu blob nemůže začínat "/" nebo název účtu úložiště. Pravidla pojmenování objektů blob, najdete v části [pojmenování a odkazování na kontejnerů, objektů BLOB a metadat](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).<br /><br /> Pokud cílový kontejner je kořenový kontejner, je třeba explicitně zadat `$root` jako kontejneru, například `$root/sample.txt`. Všimněte si, že v kořenovém kontejneru objektů BLOB nesmí obsahovat "/" v jejich názvy.|
|**/ Dispozice:**< přejmenovat &#124; ne přepsat &#124; přepsání >|`Optional.`Určuje chování, když objekt blob se zadanou adresou už existuje. Platné hodnoty tohoto parametru jsou: `rename`, `no-overwrite` a `overwrite`. Všimněte si, že jsou tyto hodnoty malá a velká písmena. Pokud není zadaná žádná hodnota, výchozí hodnota je `rename`.|
|**/ BlobType:**< BlockBlob &#124; PageBlob >|`Optional.`Určuje typ objektu blob pro cílové objekty BLOB. Platné hodnoty jsou: `BlockBlob` a `PageBlob`. Všimněte si, že jsou tyto hodnoty malá a velká písmena. Pokud není zadaná žádná hodnota, výchozí hodnota je `BlockBlob`.<br /><br /> Ve většině případů `BlockBlob` se doporučuje. Pokud zadáte `PageBlob`, délka každý soubor v adresáři musí být násobkem 512, velikost stránky pro objekty BLOB stránky.|
|**/ PropertyFile:**< PropertyFile\>|`Optional.`Cesta k souboru vlastností pro cílové objekty BLOB. V tématu [importu/exportu služby Metadata a formát vlastností souboru](../storage-import-export-file-format-metadata-and-properties.md) Další informace.|
|**/ MetadataFile:**< MetadataFile\>|`Optional.`Cesta k souboru metadat pro cílové objekty BLOB. V tématu [importu/exportu služby Metadata a formát vlastností souboru](../storage-import-export-file-format-metadata-and-properties.md) Další informace.|

### <a name="resuming-an-interrupted-copy-session"></a>Pokračování v relaci dojde k přerušení kopie
 Pokud z nějakého důvodu dojde k přerušení relace kopírování, můžete ho obnovit spuštěním nástroje s zadaný pouze soubor deníku:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession
```

 Pouze poslední relace kopie, pokud byl ukončen neobvyklým způsobem, můžete obnovit.

> [!IMPORTANT]
>  Při obnovení relace kopie neměňte přidáním nebo odebráním soubory zdrojových datových souborů a adresářů.

### <a name="aborting-an-interrupted-copy-session"></a>Přerušení relaci dojde k přerušení kopie
 Pokud dojde k přerušení relace kopírování a není možné obnovit (například pokud je zdrojový adresář prokázat nedostupné), musíte přerušit aktuální relaci, tak, aby ji možné vrátit zpět a nové kopie relací lze spustit:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession
```

 Pouze poslední relace kopie, pokud byl ukončen neobvyklým způsobem, můžete přerušena. Všimněte si, že nelze přerušit má první relace kopie pro jednotku. Místo toho je třeba restartovat relaci kopírování pomocí nového souboru deníku.

## <a name="next-steps"></a>Další kroky

* [Nastavení nástroje Azure Import/Export](storage-import-export-tool-setup-v1.md)
* [Nastavení vlastností a metadat během procesu importu](storage-import-export-tool-setting-properties-metadata-import-v1.md)
* [Ukázkový pracovní postup pro přípravu pevných disků pro úlohu importu](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
* [Stručná referenční příručka pro často používané příkazy](storage-import-export-tool-quick-reference-v1.md) 
* [Kontrola stavu úlohy s použitím kopií souborů protokolu](storage-import-export-tool-reviewing-job-status-v1.md)
* [Oprava úlohy importu](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Oprava úlohy exportu](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Řešení potíží s nástrojem Azure pro import/export](storage-import-export-tool-troubleshooting-v1.md)
