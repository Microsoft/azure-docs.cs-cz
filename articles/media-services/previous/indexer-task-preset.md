---
title: Úloha předvolby pro Azure Media Indexer
description: Toto téma obsahuje základní informace o úkolu předvolby pro Azure Media Indexer.
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
ms.date: 02/08/2019
ms.author: adsolank;juliako;
ms.openlocfilehash: 8790a5dd596a98409db9a3470b17461359882a38
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55994349"
---
# <a name="task-preset-for-azure-media-indexer"></a>Úloha předvolby pro Azure Media Indexer 

Azure Media Indexer je procesor multimédií, který používáte k provádění následujících úloh: daly prohledávat mediální soubory a obsah, generovat uzavřené stopy titulků a klíčová slova, indexování souborů prostředků, které jsou součástí vašeho prostředku.

Toto téma popisuje úlohy přednastavení, že je potřeba předat indexovací úlohou. Kompletní příklad naleznete v tématu [indexováním mediálních souborů pomocí Azure Media Indexer](media-services-index-content.md).

## <a name="azure-media-indexer-configuration-xml"></a>Azure Media Indexer konfiguračním souboru XML.

Následující tabulka popisuje elementy a atributy konfigurace XML.

|Název|Vyžadovat|Popis|
|---|---|---|
|Vstup|true (pravda)|Soubory prostředků, kterou chcete indexu.<br/>Azure Media Indexer podporuje následující formáty souborů médií: MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>Můžete zadat název souboru (s) v **název** nebo **seznamu** atribut **vstupní** – element (jak je vidět níže). Pokud nezadáte soubor, který prostředek se index, vyberou se primární soubor. Pokud není nastaven žádný soubor primární prostředek, je prvním souboru v vstupní asset indexovat.<br/><br/>Chcete-li explicitně zadat název souboru prostředku, proveďte:<br/>```<input name="TestFile.wmv" />```<br/><br/>Můžete také indexu více souborů prostředků najednou (až 10 soubory). Použijte následující postup:<br/>-Vytvořit textový soubor (soubor manifestu) a nastavte ho .lst příponu.<br/>-Přidáte seznam všech názvů souborů prostředků v vstupní asset pro tento soubor manifestu.<br/>-Přidáte soubor manifestu (nahrát) k assetu.<br/>-Zadejte název souboru manifestu v atributu seznamu vstupu.<br/>```<input list="input.lst">```<br/><br/>**Poznámka:** Pokud chcete přidat více než 10 souborů do souboru manifestu, úloha indexování selže s kódem chyby 2006.|
|zprostředkovatele identity|false (nepravda)|Metadata pro soubory zadaný prostředek.<br/>```<metadata key="..." value="..." />```<br/><br/>Můžete zadat hodnoty pro předdefinované klíče. <br/><br/>V současné době jsou podporovány následující klíče:<br/><br/>**název** a **popis** – používá se aktualizovat jazykový model zlepšit přesnost rozpoznávání řeči.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**uživatelské jméno** a **heslo** – slouží k ověřování při stahování souborů Internetu přes protokol http nebo https.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>Uživatelské jméno a heslo hodnoty platí pro všechny adresy URL médií ve vstupu manifestu.|
|Database<br/><br/>Byly přidány ve verzi 1.2. V současné době je jedinou podporovanou funkcí rozpoznávání řeči ("ASR").|false (nepravda)|Rozpoznávání řeči funkce má následující nastavení klíče:<br/><br/>Jazyk:<br/>-Přirozeného jazyka pro rozpoznat v multimediálním souboru.<br/>-Angličtina, španělština<br/><br/>CaptionFormats:<br/>-středníkem oddělený seznam titulek požadovaný výstupní formáty (pokud existuje)<br/>-ttml; sami; webvtt<br/><br/><br/>GenerateAIB:<br/>-Příznak typu boolean určující, zda soubor AIB vyžádáním (pro použití s SQL serverem a Indexer IFilter zákazníka). Další informace najdete v tématu pomocí AIB souborů pomocí Azure Media Indexer a systému SQL Server.<br/>-True; False<br/><br/>GenerateKeywords:<br/>-Logický příznak určující, zda soubor XML klíčových slov je povinný.<br/>-True; False.|

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
  
## <a name="next-steps"></a>Další postup

Zobrazit [indexováním mediálních souborů pomocí Azure Media Indexer](media-services-index-content.md).

