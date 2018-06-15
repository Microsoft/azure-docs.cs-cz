---
title: Formát souboru manifestu Azure Import/Export | Microsoft Docs
description: Další informace o formátu souboru manifestu jednotku, která popisuje mapování mezi objekty BLOB v Azure Blob storage a souborů na disku v importu nebo exportu úlohy ve službě importu a exportu.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: f3119e1c-2c25-48ad-8752-a6ed4adadbb0
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: c1857eb94fba13c30e7f07669616f5d0ab9953f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873902"
---
# <a name="azure-importexport-service-manifest-file-format"></a>Azure formát souboru manifestu služby importu a exportu
Soubor manifestu jednotky popisuje mapování mezi objekty BLOB v Azure Blob storage a souborů na jednotce, která obsahuje úlohu import nebo export. Operace importu souboru manifestu je vytvořen jako součást procesu přípravy jednotky a je uložená na disku před odesláním jednotku do datového centra Azure. Během operace exportu do manifestu se vytvoří a uloží na jednotce pomocí služby Azure Import/Export.  
  
Pro obě importovat a exportovat úlohy, v souboru manifestu jednotky je uložená na disku import nebo export; není přenesen service pomocí všechny operace rozhraní API.  
  
Následující text popisuje obecný formát souboru manifestu jednotky:  
  
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

V následující tabulce je určen datové prvky, atributy ve formátu jednotka manifestu XML.  
  
|XML Element|Typ|Popis|  
|-----------------|----------|-----------------|  
|`DriveManifest`|Kořenový element|Kořenový element souboru manifestu. Všechny elementy v souboru jsou pod tohoto elementu.|  
|`Version`|Atribut, řetězec|Verze souboru manifestu.|  
|`Drive`|Vnořené – element XML|Obsahuje manifest pro každou jednotku.|  
|`DriveId`|Řetězec|Jednotky jedinečný identifikátor pro jednotku. Identifikátor disku nachází pomocí dotazu na disku pro jeho sériového čísla. Sériové číslo jednotky je obvykle vytištěno na vnější také disku. `DriveID` Element musí být před spuštěním `BlobList` element v souboru manifestu.|  
|`StorageAccountKey`|Řetězec|Požadovaný pro import úlohy Pokud a pouze v případě `ContainerSas` není zadán. Klíč účtu pro účet úložiště Azure přidružený k úloze.<br /><br /> Tento element je vynechaný manifest pro operace exportu.|  
|`ContainerSas`|Řetězec|Požadovaný pro import úlohy Pokud a pouze v případě `StorageAccountKey` není zadán. Kontejner SAS pro přístup k objektům BLOB přidružený k úloze. V tématu [Put úlohy](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) pro formát. Tento element je vynechaný manifest pro operace exportu.|  
|`ClientCreator`|Řetězec|Určuje klienta, který vytvoří soubor XML. Tato hodnota není interpretovat službu Import/Export.|  
|`BlobList`|Vnořené – element XML|Obsahuje seznam objektů BLOB, které jsou součástí import nebo export úlohy. Každý objekt blob v seznamu objektů blob sdílí stejné vlastnosti a metadata.|  
|`BlobList/MetadataPath`|Řetězec|Volitelné. Určuje relativní cestu k souboru na disku, který obsahuje výchozí metadata, která bude nastavena na objekty BLOB v seznamu objektů blob pro operace importu. Tato metadata lze volitelně přepsat na základě objektů blob blob.<br /><br /> Tento element je vynechaný manifest pro operace exportu.|  
|`BlobList/MetadataPath/@Hash`|Atribut, řetězec|Určuje hodnotu hash MD5 kódováním Base16 soubor metadat.|  
|`BlobList/PropertiesPath`|Řetězec|Volitelné. Určuje relativní cestu k souboru na disku, který obsahuje výchozí vlastnosti, které budou nastaveny na objekty BLOB v seznamu objektů blob pro operace importu. Tyto vlastnosti lze volitelně přepsat na základě objektů blob blob.<br /><br /> Tento element je vynechaný manifest pro operace exportu.|  
|`BlobList/PropertiesPath/@Hash`|Atribut, řetězec|Určuje hodnotu hash MD5 kódováním Base16 vlastnosti souboru.|  
|`Blob`|Vnořené – element XML|Obsahuje informace o jednotlivých objektů blob v každém seznamu objektů blob.|  
|`Blob/BlobPath`|Řetězec|Relativní identifikátor URI objektu blob, počínaje název kontejneru. Pokud objekt blob je v kořenovém kontejneru, musí začínat `$root`.|  
|`Blob/FilePath`|Řetězec|Určuje relativní cestu k souboru na disku. Pro úlohy exportu cesta blobu se použije pro danou cestu k souboru, pokud je to možné; *například*, `pictures/bob/wild/desert.jpg` se vyexportují do `\pictures\bob\wild\desert.jpg`. Z důvodu omezení názvů systému souborů NTFS, však může objekt blob exportovat do souboru s cestu, která není vypadat cesta blobu.|  
|`Blob/ClientData`|Řetězec|Volitelné. Obsahuje poznámky od zákazníka. Tato hodnota není interpretovat službu Import/Export.|  
|`Blob/Snapshot`|Data a času|Volitelné pro úlohy exportu. Určuje identifikátor snímku pro exportovaný blob snímku.|  
|`Blob/Length`|Integer|Určuje celková délka objektu blob v bajtech. Hodnota může být až 200 GB pro objekt blob bloku a až 1 TB pro objekt blob stránky. Pro objekt blob stránky tato hodnota musí být násobkem 512.|  
|`Blob/ImportDisposition`|Řetězec|Volitelné pro úlohy import, export úloh tento parametr vynechán. Určuje, jak službu Import/Export pracovat případ úlohy importu kde objekt blob se stejným názvem již existuje. Pokud tato hodnota je vynechán z manifestu import, výchozí hodnota je `rename`.<br /><br /> Hodnoty pro tento element:<br /><br /> -   `no-overwrite`: Pokud se stejným názvem již existuje cílový objekt blob, přeskočí operace importu import tohoto souboru.<br />-   `overwrite`: Žádné existující cílový objekt blob je zcela přepsány nově importovaného souboru.<br />-   `rename`: Nový objekt blob, nebude odesílat s upravený název.<br /><br /> Přejmenování pravidlo vypadá takto:<br /><br /> -Li název objektu blob neobsahuje tečku, generuje se nový název připojením `(2)` původní název objektu blob; Pokud je tento nový název taky v konfliktu s existujícím názvem objektu blob, pak `(3)` se připojí místě `(2)`; a tak dále.<br />– Pokud je název objektu blob obsahuje tečku, považuje za část následující poslední tečky název rozšíření. Podobně jako výše uvedeného postupu `(2)` je vložen před poslední tečky vygenerovat nový název; Pokud blob nový název stále je v konfliktu s existujícím názvem a pak službu pokusí `(3)`, `(4)`a tak dále, dokud nebude nalezen název konfliktní.<br /><br /> Několik příkladů:<br /><br /> Objekt blob `BlobNameWithoutDot` bude přejmenován na:<br /><br /> `BlobNameWithoutDot (2)  // if BlobNameWithoutDot exists`<br /><br /> `BlobNameWithoutDot (3)  // if both BlobNameWithoutDot and BlobNameWithoutDot (2) exist`<br /><br /> Objekt blob `Seattle.jpg` bude přejmenován na:<br /><br /> `Seattle (2).jpg  // if Seattle.jpg exists`<br /><br /> `Seattle (3).jpg  // if both Seattle.jpg and Seattle (2).jpg exist`|  
|`PageRangeList`|Vnořené – element XML|Vyžaduje se pro objekt blob stránky.<br /><br /> Operace, určuje pro importu seznam rozsahů bajtů souboru určených k importu. Každý rozsahu stránek je popsán posun a délka ve zdrojovém souboru, který popisuje rozsahu stránek, společně s hodnotu hash MD5 oblasti. `Hash` Atribut rozsahu stránek je povinný. Služba ověří, zda hodnota hash dat do objektu BLOB odpovídá vypočtená hodnota hash MD5 z rozsahu stránek. Libovolný počet rozsahů stránek lze popisují soubor k importu, s celková velikost až 1 TB. Jsou povoleny žádné překrytí a všechny rozsahy stránky musí být seřazené podle posun.<br /><br /> Operace, o export určuje sadu rozsahů bajtů objektu blob, které byly vyexportovány do jednotky.<br /><br /> Rozsahů stránek společně se může vztahovat pouze dílčí rozsahy objektů blob nebo souboru.  Očekává se zbývající část souboru není uvedena v jakémkoli rozsahu stránky a její obsah může být definovaný.|  
|`PageRange`|XML element|Představuje rozsahu stránek.|  
|`PageRange/@Offset`|Atribut, celé číslo|Určuje posunutí v přenosu souborů a objektů blob, které začíná určeném rozsahu. Tato hodnota musí být násobkem 512.|  
|`PageRange/@Length`|Atribut, celé číslo|Určuje délku rozsahu stránek. Tato hodnota musí být násobkem 512 a více než 4 MB.|  
|`PageRange/@Hash`|Atribut, řetězec|Určuje hodnotu hash MD5 kódováním Base16 rozsahu stránek.|  
|`BlockList`|Vnořené – element XML|Vyžaduje se pro objekt blob bloku pomocí pojmenovaných bloků.<br /><br /> Pro operace importu do seznamu zakázaných položek Určuje sadu bloků, které bude naimportován do úložiště Azure. Pro operace exportu do seznamu zakázaných položek Určuje, kde každý blok byla uložena v souboru na disku pro export. Každý blok je popsán posun v souboru a délka bloku; Každý blok je kromě s názvem atributem ID bloku a obsahuje hodnotu hash MD5 pro blok. Až 50 000 bloků lze popsat objekt blob.  Všechny bloky musejí být seřazeny podle posun a společně by mělo zahrnovat kompletní rozsah souboru *tj*, nesmí být mezera mezi bloky. Pokud objekt blob je více než 64 MB, ID bloku pro každého bloku musí být všechny chybějící nebo všechny existuje. ID bloku musí být řetězce s kódováním Base64. V tématu [Put bloku](/rest/api/storageservices/put-block) další požadavky pro ID bloku.|  
|`Block`|XML element|Představuje blok.|  
|`Block/@Offset`|Atribut, celé číslo|Určuje posun, kde začíná zadaný blok.|  
|`Block/@Length`|Atribut, celé číslo|Určuje počet bajtů v bloku; Tato hodnota musí být delší než 4 MB volného místa.|  
|`Block/@Id`|Atribut, řetězec|Určuje řetězec představující ID bloku pro blok.|  
|`Block/@Hash`|Atribut, řetězec|Určuje algoritmus hash MD5 kódováním Base16 bloku.|  
|`Blob/MetadataPath`|Řetězec|Volitelné. Určuje relativní cestu k souboru metadat. Během importu metadata nastavena na cílový objekt blob. Během operace exportu metadata objektu blob je uloženo v souboru metadat na jednotce.|  
|`Blob/MetadataPath/@Hash`|Atribut, řetězec|Určuje algoritmus hash MD5 Base16 kódovaný soubor metadat objektu blob.|  
|`Blob/PropertiesPath`|Řetězec|Volitelné. Určuje relativní cestu k souboru vlastnosti. Během importu že jsou nastavené vlastnosti na cílový objekt blob. Během operace exportu jsou vlastnosti objektů blob uložené v souboru vlastnosti na jednotce.|  
|`Blob/PropertiesPath/@Hash`|Atribut, řetězec|Určuje algoritmus hash MD5 kódováním Base16 objektu blob vlastnosti souboru.|  
  
## <a name="next-steps"></a>Další kroky
 
* [REST API pro Import/Export úložiště](/rest/api/storageimportexport/)
