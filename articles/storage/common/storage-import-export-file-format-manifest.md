---
title: Formát souborů manifestu služby Azure Import/Export | Dokumentace Microsoftu
description: Další informace o formátu souboru manifestu jednotky, který popisuje mapování mezi objekty BLOB ve službě Azure Blob storage a souborů na disku v importu nebo exportu úlohy ve službě Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 920f350ab5ba1e9e1703ffcc32dc8c7153624c0b
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525150"
---
# <a name="azure-importexport-service-manifest-file-format"></a>Formát souborů manifestu služby Azure Import/Export
Soubor manifestu jednotky popisuje mapování mezi objekty BLOB ve službě Azure Blob storage a souborů na jednotce, která obsahuje úlohu importu nebo exportu. Operace importu souboru manifestu je vytvořen jako součást procesu přípravy jednotky a je uložená na disku před odesláním jednotky datového centra Azure. Během operace exportu se manifest vytvoří a uloží na disk pomocí služby Azure Import/Export.  
  
Pro obě import a export projektů, soubor manifestu disk je uložený na import nebo export disku; se nebudou přenášet do služby prostřednictvím jakoukoli operaci rozhraní API.  
  
Následující část popisuje obecný formát souboru manifestu jednotky:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveManifest Version="2014-11-01">  
  <Drive>  
    <DriveId>drive-id</DriveId>  
    import-export-credential  
  
    <!-- First Blob List -->  
    <BlobList>  
      <!-- Global properties and metadata that applies to all blobs -->  
      [<MetadataPath Hash="md5-hash">global-metadata-file-path</MetadataPath>]  
      [<PropertiesPath   
        Hash="md5-hash">global-properties-file-path</PropertiesPath>]  
  
      <!-- First Blob -->  
      <Blob>  
        <BlobPath>blob-path-relative-to-account</BlobPath>  
        <FilePath>file-path-relative-to-transfer-disk</FilePath>  
        [<ClientData>client-data</ClientData>]  
        [<Snapshot>snapshot</Snapshot>]  
        <Length>content-length</Length>  
        [<ImportDisposition>import-disposition</ImportDisposition>]  
        page-range-list-or-block-list          
        [<MetadataPath Hash="md5-hash">metadata-file-path</MetadataPath>]  
        [<PropertiesPath Hash="md5-hash">properties-file-path</PropertiesPath>]  
      </Blob>  
  
      <!-- Second Blob -->  
      <Blob>  
      . . .  
      </Blob>  
    </BlobList>  
  
    <!-- Second Blob List -->  
    <BlobList>  
    . . .  
    </BlobList>  
  </Drive>  
</DriveManifest>  
  
import-export-credential ::=   
  <StorageAccountKey>storage-account-key</StorageAccountKey> | <ContainerSas>container-sas</ContainerSas>  
  
page-range-list-or-block-list ::=   
  page-range-list | block-list  
  
page-range-list ::=   
    <PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
    </PageRangeList>  
  
block-list ::=  
    <BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       Hash="md5-hash"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       Hash="md5-hash"/>]  
    </BlockList>  

```

## <a name="manifest-xml-elements-and-attributes"></a>Manifestu XML elementů a atributů

Datové prvky a atributy ve formátu jednotka manifestu XML jsou uvedeny v následující tabulce.  
  
|– Element XML|Typ|Popis|  
|-----------------|----------|-----------------|  
|`DriveManifest`|Kořenový element|Kořenový element souboru manifestu. Všechny elementy v souboru jsou pod tímto prvkem.|  
|`Version`|Atribut řetězců|Verze souboru manifestu.|  
|`Drive`|Vnořené – XML element|Obsahuje manifest pro každou jednotku.|  
|`DriveId`|Řetězec|Identifikátor jedinečný jednotky pro jednotku. Identifikátor disku najdete pomocí dotazu na jednotce z důvodu jeho sériového čísla. Sériové číslo jednotky je obvykle vytištěno na straně mimo i na jednotce. `DriveID` Elementu musí být uvedena před jakoukoli `BlobList` element v souboru manifestu.|  
|`StorageAccountKey`|Řetězec|Vyžadováno pro import úlohách Pokud a pouze v případě `ContainerSas` není zadán. Klíč účtu pro účet úložiště Azure přidruženou k úloze.<br /><br /> Tento element je vynecháno z manifestu pro operace exportu.|  
|`ContainerSas`|Řetězec|Vyžadováno pro import úlohách Pokud a pouze v případě `StorageAccountKey` není zadán. Kontejner SAS pro přístup k objektům BLOB přidruženou k úloze. Zobrazit [úlohy umístit](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) pro jeho formát. Tento element je vynecháno z manifestu pro operace exportu.|  
|`ClientCreator`|Řetězec|Určuje klienta, který vytvořil XML soubor. Tato hodnota není interpretována služby Import/Export.|  
|`BlobList`|Vnořené – XML element|Obsahuje seznam objektů BLOB, které jsou součástí import nebo export úloh. Každý objekt blob v seznamu objektů blob sdílí stejné metadat a vlastností.|  
|`BlobList/MetadataPath`|Řetězec|Volitelné. Určuje relativní cestu souboru na disku, který obsahuje výchozí metadata, která bude nastavena na objekty BLOB v seznamu objektů blob pro operaci importu. Tato metadata lze volitelně můžete přepsat na základě objektů blob pomocí objektů blob.<br /><br /> Tento element je vynecháno z manifestu pro operace exportu.|  
|`BlobList/MetadataPath/@Hash`|Atribut řetězců|Určuje hodnotu hash MD5 Base16 kódovaný soubor metadat.|  
|`BlobList/PropertiesPath`|Řetězec|Volitelné. Určuje relativní cestu souboru na disku, který obsahuje výchozí vlastnosti, které se nastaví na objekty BLOB v seznamu objektů blob pro operaci importu. Tyto vlastnosti lze volitelně můžete přepsat na základě objektů blob pomocí objektů blob.<br /><br /> Tento element je vynecháno z manifestu pro operace exportu.|  
|`BlobList/PropertiesPath/@Hash`|Atribut řetězců|Určuje hodnotu hash MD5 kódováním Base16 vlastnosti souboru.|  
|`Blob`|Vnořené – XML element|Obsahuje informace o jednotlivých objektů blob v jednotlivých objektů blob v seznamu.|  
|`Blob/BlobPath`|Řetězec|Relativní identifikátor URI objektu blob, počínaje název kontejneru. Pokud se objekt blob je v kořenovém kontejneru, musí začínat `$root`.|  
|`Blob/FilePath`|Řetězec|Určuje relativní cestu k souboru na disku. Pro úlohy exportu cesta objektu blob se použije pro danou cestu k souboru, pokud je to možné, *například*, `pictures/bob/wild/desert.jpg` se exportují do `\pictures\bob\wild\desert.jpg`. Vzhledem k omezením název systému souborů NTFS, může být objekt blob, vyexportovali do souboru za cestu, která nebude vypadat podobně jako cesta objektu blob.|  
|`Blob/ClientData`|Řetězec|Volitelné. Obsahuje komentáře od zákazníka. Tato hodnota není interpretována služby Import/Export.|  
|`Blob/Snapshot`|DateTime|Volitelné pro export úloh. Určuje identifikátor snímku snímek objektu blob exportované.|  
|`Blob/Length`|Integer|Určuje celková délka objektu blob v bajtech. Hodnota může být až 200 GB pro objekt blob bloku a až 1 TB pro objekty blob stránky. Pro objekt blob stránky tato hodnota musí být násobkem 512.|  
|`Blob/ImportDisposition`|Řetězec|Volitelné pro úlohy importu, tento parametr vynechán, export úloh. Určuje způsob, jakým služba Import/Export pracovat v případě úlohy importu kde objekt blob se stejným názvem už existuje. Pokud je tato hodnota vynechána z importu manifestu, výchozí hodnota je `rename`.<br /><br /> Hodnoty pro tento element:<br /><br /> -   `no-overwrite`: Pokud se stejným názvem již existuje cílový objekt blob, se operace importu se přeskočí, import tohoto souboru.<br />-   `overwrite`: Všechny stávající cílový objekt blob je úplně přepsán nově importovaného souboru.<br />-   `rename`: Tento nový objekt blob se odeslaly s upravený název.<br /><br /> Přejmenování pravidla vypadá takto:<br /><br /> – Pokud je název objektu blob nemůže obsahovat tečku, nový název je generován připojení `(2)` k původnímu názvu objektu blob; Pokud tento nový název také je v konfliktu s existujícím názvem objektu blob, pak `(3)` se připojí místo `(2)`; a tak dále.<br />– Pokud je název objektu blob obsahuje tečku, část po poslední bod je považován za název rozšíření. Podobně jako výše uvedeného postupu `(2)` je vložen před poslední tečku a vygenerovat nový název; v případě, že nový název stále je v konfliktu s existujícím objektu blob, název a potom služba se pokusí `(3)`, `(4)`, a tak dále, dokud nebude nalezen název jiné konfliktní.<br /><br /> Několik příkladů:<br /><br /> Objekt blob `BlobNameWithoutDot` se přejmenují na:<br /><br /> `BlobNameWithoutDot (2)  // if BlobNameWithoutDot exists`<br /><br /> `BlobNameWithoutDot (3)  // if both BlobNameWithoutDot and BlobNameWithoutDot (2) exist`<br /><br /> Objekt blob `Seattle.jpg` se přejmenují na:<br /><br /> `Seattle (2).jpg  // if Seattle.jpg exists`<br /><br /> `Seattle (3).jpg  // if both Seattle.jpg and Seattle (2).jpg exist`|  
|`PageRangeList`|Vnořené – XML element|Vyžaduje se pro objekt blob stránky.<br /><br /> Pro import určuje operaci, seznam rozsahů bajtů souboru k importu. Rozsah jednotlivých stránek je popsána posun a délka ve zdrojovém souboru, který popisuje rozsah stránek, společně s hodnotu hash MD5 oblasti. `Hash` Je vyžadován atribut celou stránku. Služba se ověří, že hodnota hash dat v objektu blob odpovídá Vypočítaný algoritmus hash MD5 z rozsahu stránek. Libovolný počet rozsahů slouží k popisu soubor pro import, s celkovou velikost až 1 TB. Všechny rozsahy stránek musí být seřazené podle posun a nejsou povoleny žádné překrytí.<br /><br /> Pro export operace, určuje sadu rozsahů bajtů objektu blob, které byly vyexportovány do jednotky.<br /><br /> Rozsahy stránek společně se může vztahovat pouze dílčí rozsahy objektu blob nebo souboru.  Zbývající část souboru, se nevztahuje libovolnou oblast stránky se očekává a její obsah může nedefinovaný.|  
|`PageRange`|– Element XML|Představuje celou stránku.|  
|`PageRange/@Offset`|Atribut, celé číslo|Určuje posun v přenosu souborů a objektů blob, kde začíná zadanou stránku rozsahu. Tato hodnota musí být násobkem 512.|  
|`PageRange/@Length`|Atribut, celé číslo|Určuje délku rozsahu stránek. Tato hodnota musí být násobkem 512 a více než 4 MB.|  
|`PageRange/@Hash`|Atribut řetězců|Určuje hodnotu hash MD5 kódováním Base16 rozsahu stránek.|  
|`BlockList`|Vnořené – XML element|Vyžaduje se pro objekt blob bloku se pojmenovaných bloků.<br /><br /> Pro operace importu do seznamu zakázaných položek Určuje sadu bloků, které budou importovány do služby Azure Storage. Pro operace exportu do seznamu zakázaných položek Určuje, kde jsou uložená každý blok v souboru na disku pro export. Každý blok je popsán posun v souboru a délka bloku; Každý blok se dále s názvem atributu ID bloku a obsahuje hodnotu hash MD5 pro blok. Až 50 000 bloků slouží k popisu objektu blob.  Všechny bloky musejí být seřazeny podle posun a společně by měly pokrývat celý rozsah souboru *tedy*, nesmí být mezera mezi bloky. Pokud objekt blob je více než 64 MB, ID bloku pro každý blok musí být všechny chybějící nebo všechny k dispozici. ID bloku musí být řetězce s kódováním Base64. Zobrazit [Vložit blok](/rest/api/storageservices/put-block) pro další požadavky pro ID bloku.|  
|`Block`|– Element XML|Představuje blok.|  
|`Block/@Offset`|Atribut, celé číslo|Určuje posun, kde začíná zadaný blok.|  
|`Block/@Length`|Atribut, celé číslo|Určuje počet bajtů v bloku; Tato hodnota musí být více než 4MB.|  
|`Block/@Id`|Atribut řetězců|Určuje řetězec představující ID bloku pro blok.|  
|`Block/@Hash`|Atribut řetězců|Určuje algoritmus hash MD5 kódováním Base16 bloku.|  
|`Blob/MetadataPath`|Řetězec|Volitelné. Určuje relativní cestu k souboru metadat. Během importu metadata nastavená na cílový objekt blob. Během operace exportu metadat objektu blob je uložen v souboru metadat na jednotce.|  
|`Blob/MetadataPath/@Hash`|Atribut řetězců|Určuje algoritmus hash MD5 Base16 kódovaný soubor metadat objektu blob.|  
|`Blob/PropertiesPath`|Řetězec|Volitelné. Určuje relativní cestu k vlastnosti souboru. Během importu vlastnosti nastavují na cílový objekt blob. Během operace exportu jsou vlastnosti objektu blob uložené ve vlastnosti souboru na disku.|  
|`Blob/PropertiesPath/@Hash`|Atribut řetězců|Určuje algoritmus hash MD5 Base16 kódovaný soubor vlastností objektu blob.|  
  
## <a name="next-steps"></a>Další postup
 
* [REST API pro Import/Export úložiště](/rest/api/storageimportexport/)
