---
title: "Úloha předvolby pro Azure Media Indexer"
description: "Toto téma poskytuje přehled úloh předvolby pro Azure Media Indexer."
services: media-services
documentationcenter: 
author: Asolanki
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/03/2017
ms.author: adsolank;juliako;
ms.openlocfilehash: ae6c4da189cd6637b4e1fa9274473b62f6664e51
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
---
# <a name="task-preset-for-azure-media-indexer"></a>Úloha předvolby pro Azure Media Indexer

Azure Media Indexer je procesor médií, které umožňují provádět následující úlohy: Zkontrolujte soubory médií a obsah, s možností vyhledávání, generování uzavřené titulků sleduje a klíčová slova, indexu soubory prostředků, které jsou součástí asset.

Toto téma popisuje úlohy předvolby, je potřeba předat indexování úlohu. Úplný příklad najdete v tématu [indexování mediálních souborů pomocí Azure Media Indexer](media-services-index-content.md).

## <a name="azure-media-indexer-configuration-xml"></a>Azure Media Indexer konfigurace XML

Následující tabulka popisuje elementy a atributy konfigurace XML.

|Name (Název)|Vyžadovat|Popis|
|---|---|---|
|Vstup|Hodnota TRUE|Soubory Asset, který chcete indexu.<br/>Azure Media Indexer podporuje následující formáty souborů: MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>Můžete zadat název souboru (s) v **název** nebo **seznamu** atribut **vstupní** – element (jak je znázorněno níže). Pokud nezadáte, který soubor asset má index, primární soubor vydán. Pokud není nastaven žádný soubor primární asset, je indexovaný první soubor v vstupní prostředku.<br/><br/>Chcete-li explicitně zadat název souboru prostředku, proveďte:<br/>```<input name="TestFile.wmv" />```<br/><br/>Můžete také indexu více asset soubory najednou (až 10). Použijte následující postup:<br/>-Vytvořte textový soubor (soubor manifestu) a dejte mu .lst rozšíření.<br/>-Přidáte seznam všech názvů souborů asset ve vstupní asset do tohoto souboru manifestu.<br/>-Přidáte soubor thanifest (nahrávání) pro daný prostředek.<br/>-Zadejte název souboru manifestu v atributu seznamu vstupu.<br/>```<input list="input.lst">```<br/><br/>**Poznámka:** Pokud přidáte více než 10 souborů k souboru manifestu, indexování úloha selže s kódem chyby 2006.|
|Metadata|False|Metadata pro soubory, které daný prostředek.<br/>```<metadata key="..." value="..." />```<br/><br/>Můžete zadat hodnoty pro předdefinované klíče. <br/><br/>V současné době jsou podporovány následující klíče:<br/><br/>**název** a **popis** – použít k aktualizaci modelu jazyk a zvyšte tak přesnost rozpoznávání řeči.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**uživatelské jméno** a **heslo** – používá pro ověřování při stahování souborů Internetu prostřednictvím protokolu http nebo https.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>Uživatelské jméno a heslo hodnoty platí pro všechny adresy URL média do vstupní manifestu.|
|Database<br/><br/>Přidaná do verze 1.2. V současné době je jedinou podporovanou funkcí rozpoznávání řeči ("ASR").|False|Rozpoznávání řeči funkce má následující nastavení klíče:<br/><br/>Jazyk:<br/>-Přirozeného jazyka rozpoznat v multimediálních souborů.<br/>-Angličtina, španělština<br/><br/>CaptionFormats:<br/>-středníky oddělený seznam titulek požadovaný výstupní formáty (pokud existuje)<br/>-ttml; sami; webvtt<br/><br/><br/>GenerateAIB:<br/>-A logický příznak určující, zda je soubor AIB požadované (pro použití se službou SQL Server a zákazník Indexer IFilter). Další informace najdete v tématu pomocí AIB souborů pomocí Azure Media Indexer a SQL Server.<br/>-True; False<br/><br/>GenerateKeywords:<br/>-A logický příznak určující, zda je požadovaný soubor XML – klíčové slovo.<br/>-True; False.|

## <a name="azure-media-indexer-configuration-xml-example"></a>Ukázkový kód XML konfigurace služby Azure Media Indexer

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
        <add key="CaptionFormats" value="ttml;sami;webvtt"/>  
        <add key="GenerateAIB" value ="true" />  
        <add key="GenerateKeywords" value ="true" />  
      </settings>  
    </feature>  
  </features>  
  
</configuration>  
```
  
## <a name="next-steps"></a>Další kroky

V tématu [indexování mediálních souborů pomocí Azure Media Indexer](media-services-index-content.md).

