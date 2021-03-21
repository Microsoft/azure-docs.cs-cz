---
title: Předvolba úlohy pro Azure Media Indexer
description: Toto téma poskytuje přehled přednastavených úloh pro Azure Media Services Media Indexer.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: c054c0aa8c58894f63f8ce8432e8d7a9e1275639
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012220"
---
# <a name="task-preset-for-azure-media-indexer"></a>Předvolba úlohy pro Azure Media Indexer

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Azure Media Indexer je multimediální procesor, který používáte k provádění následujících úloh: zpřístupnění mediálních souborů a obsahu, generování skrytých titulků a klíčových slov, indexových souborů assetů, které jsou součástí vašeho assetu.

Toto téma popisuje předdefinované úlohy, které je třeba předat úloze indexování. Úplný příklad najdete v tématu [indexování mediálních souborů pomocí Azure Media Indexer](media-services-index-content.md).

## <a name="azure-media-indexer-configuration-xml"></a>XML konfigurace Azure Media Indexer

Následující tabulka vysvětluje prvky a atributy konfiguračního XML.

|Name|Vyžadovat|Description|
|---|---|---|
|Vstup|true|Soubory prostředků, které chcete indexovat.<br/>Azure Media Indexer podporuje následující formáty mediálních souborů: MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>Můžete zadat název souboru (y) v atributu **název** nebo **seznam** **vstupního** elementu (jak je vidět níže). Pokud neurčíte, který soubor prostředků se má indexovat, je primární soubor vybrán. Pokud není nastaven žádný soubor primárního majetku, bude indexován první soubor ve vstupním prostředku.<br/><br/>Chcete-li explicitně zadat název souboru assetu, udělejte toto:<br/>```<input name="TestFile.wmv" />```<br/><br/>Můžete také indexovat více souborů prostředků najednou (až 10 souborů). Použijte následující postup:<br/>-Vytvořte textový soubor (soubor manifestu) a sdělte mu příponu. lst.<br/>– Do tohoto souboru manifestu přidejte seznam všech názvů souborů assetů ve vstupním prostředku.<br/>– Přidejte (nahrajte) soubor manifestu do assetu.<br/>-Zadejte název souboru manifestu v atributu seznamu vstupu.<br/>```<input list="input.lst">```<br/><br/>**Poznámka:** Pokud do souboru manifestu přidáte více než 10 souborů, úloha indexování se nezdaří a zobrazí se kód chyby 2006.|
|zprostředkovatele identity|false (nepravda)|Metadata pro zadaný soubor prostředků.<br/>```<metadata key="..." value="..." />```<br/><br/>Můžete zadávat hodnoty pro předdefinované klíče. <br/><br/>V současné době jsou podporovány následující klíče:<br/><br/>**název** a **Popis** – používá se k aktualizaci jazykového modelu za účelem zlepšení přesnosti rozpoznávání řeči.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**uživatelské jméno** a **heslo** – používá se k ověřování při stahování internetových souborů přes HTTP nebo HTTPS.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>Hodnoty uživatelského jména a hesla se vztahují na všechny adresy URL médií ve vstupním manifestu.|
|funkce<br/><br/>Přidáno ve verzi 1,2. V současné době je jedinou podporovanou funkcí rozpoznávání řeči (ASR).|false (nepravda)|Funkce rozpoznávání řeči má následující klíče nastavení:<br/><br/>Jazyk:<br/>– Přirozený jazyk, který se má rozpoznat v multimediálním souboru.<br/>– Angličtina, španělština<br/><br/>CaptionFormats:<br/>– středníkem oddělený seznam požadovaných formátů titulků pro výstup (pokud existují)<br/>-ttml; WebVTT<br/><br/><br/>GenerateKeywords:<br/>-Logický příznak určující, zda je požadován soubor XML s klíčovým slovem.<br/>Podmínka Chybné.|

## <a name="azure-media-indexer-configuration-xml-example"></a>Příklad XML konfigurace Azure Media Indexer

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

Viz [indexování mediálních souborů pomocí Azure Media Indexer](media-services-index-content.md).

