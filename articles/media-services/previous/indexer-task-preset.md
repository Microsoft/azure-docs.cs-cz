---
title: Přednastavení úloh pro Azure Media Indexer
description: Toto téma poskytuje přehled přednastavení úloh pro Azure Media Services Media Indexer.
services: media-services
documentationcenter: ''
author: Asolanki
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 29753759af341f82429f12b6710ae9c32dcb4103
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896028"
---
# <a name="task-preset-for-azure-media-indexer"></a>Přednastavení úloh pro Azure Media Indexer 

Azure Media Indexer je mediální procesor, který slouží k provádění následujících úloh: zpřístupnit mediální soubory a obsah prohledávatelné, generovat stopy skrytých titulků a klíčová slova, indexovat soubory datových zdrojů, které jsou součástí vašeho datového zdroje.

Toto téma popisuje přednastavení úlohy, které je třeba předat úlohě indexování. Úplný příklad najdete [v tématu Indexování mediálních souborů pomocí Azure Media Indexer](media-services-index-content.md).

## <a name="azure-media-indexer-configuration-xml"></a>Konfigurační XML nástroje Azure Media Indexer

Následující tabulka vysvětluje prvky a atributy konfiguračního XML.

|Name (Název)|Vyžadovat|Popis|
|---|---|---|
|Vstup|true|Soubory majetku, které chcete indexovat.<br/>Azure Media Indexer podporuje následující formáty mediálních souborů: MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>Název souboru (y) můžete zadat v atributu **názvu** nebo **seznamu** **vstupního** prvku (jak je znázorněno níže). Pokud neurčíte, který soubor datového zdroje má být indexován, bude primární soubor vybrán. Pokud není nastaven žádný primární soubor datového zdroje, je indexován první soubor ve vstupním datovém zdroji.<br/><br/>Chcete-li explicitně zadat název souboru datového zdroje, proveďte následující:<br/>```<input name="TestFile.wmv" />```<br/><br/>Můžete také indexovat více souborů datových zdrojů najednou (až 10 souborů). Použijte následující postup:<br/>- Vytvořte textový soubor (soubor manifestu) a dejte mu příponu .lst.<br/>- Přidejte seznam všech názvů souborů datových zdrojů ve vstupním podkladu do tohoto souboru manifestu.<br/>- Přidejte (nahrát) soubor manifestu do datového zdroje.<br/>- Zadejte název souboru manifestu v atributu seznamu vstupu.<br/>```<input list="input.lst">```<br/><br/>**Poznámka:** Pokud do souboru manifestu přidáte více než 10 souborů, úloha indexování se nezdaří s kódem chyby 2006.|
|zprostředkovatele identity|false (nepravda)|Metadata pro zadané soubory datových zdrojů.<br/>```<metadata key="..." value="..." />```<br/><br/>Můžete zadat hodnoty pro předdefinované klíče. <br/><br/>V současné době jsou podporovány následující klíče:<br/><br/>**název** a **popis** - slouží k aktualizaci jazykového modelu za účelem zlepšení přesnosti rozpoznávání řeči.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**uživatelské jméno** a **heslo** - používá se pro ověřování při stahování internetových souborů přes http nebo https.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>Hodnoty uživatelského jména a hesla platí pro všechny adresy URL médií ve vstupním manifestu.|
|funkce<br/><br/>Přidáno ve verzi 1.2. V současné době je jedinou podporovanou funkcí rozpoznávání řeči ("ASR").|false (nepravda)|Funkce Rozpoznávání řeči má následující klávesy nastavení:<br/><br/>Jazyk:<br/>- Přirozený jazyk, který má být rozpoznán v multimediálním souboru.<br/>- Anglicky, španělsky<br/><br/>CaptionFormats:<br/>- středník-oddělený seznam požadovaných formátů výstupní titulek (pokud existuje)<br/>- ttml;webvtt<br/><br/><br/>GenerateKeywords:<br/>- Logický příznak určující, zda je vyžadován soubor XML klíčového slova.<br/>- Pravda; False.|

## <a name="azure-media-indexer-configuration-xml-example"></a>Příklad XML konfigurace nástroje Azure Media Indexer

``` 
<?xml version="1.0" encoding="utf-8"?>  
<configuration version="2.0">  
  <input>  
    <metadata key="title" value="[Title of the media file]" />  
    <metadata key="description" value="[Description of the media file]" />  
  </input>  
  <settings>  
  </settings>  
  
  <features>  
    <feature name="ASR">    
      <settings>  
        <add key="Language" value="English"/>  
        <add key="GenerateKeywords" value ="true" />  
      </settings>  
    </feature>  
  </features>  
  
</configuration>  
```
  
## <a name="next-steps"></a>Další kroky

Viz [Indexování mediálních souborů pomocí Azure Media Indexer](media-services-index-content.md).

