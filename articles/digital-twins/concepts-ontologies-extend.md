---
title: Rozšíření ontologie
titleSuffix: Azure Digital Twins
description: Přečtěte si o důvodech a strategiích za rozšířením Ontology.
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: e5973f58887b212919ad739232faafddcf9e735c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100561396"
---
# <a name="extending-ontologies"></a>Rozšíření ontologie 

Standardní [Ontology](concepts-ontologies.md), jako je [RealEstateCore Ontology založené na DTDL pro inteligentní budovy](https://github.com/Azure/opendigitaltwins-building), je skvělým způsobem, jak začít vytvářet řešení IoT. Oborová ontologie poskytuje bohatou sadu základních rozhraní, která jsou navržená pro vaši doménu a pracovala v oblasti služeb Azure IoT, jako jsou například digitální vlákna Azure. 

Je ale možné, že vaše řešení bude mít konkrétní potřeby, které nepokrývá obor Ontology. Například můžete chtít propojit digitální vlákna s 3D modely uloženými v samostatném systému. V takovém případě můžete jednu z těchto ontologie, abyste mohli přidat vlastní funkce a přitom zachovat všechny výhody původního Ontology.

Tento článek používá DTDL [RealEstateCore](https://www.realestatecore.io/) Ontology jako základ pro příklady rozšíření ontologie s novými vlastnostmi DTDL. Zde popsané postupy jsou obecné, ale lze je použít pro libovolnou část DTDL založenou na Ontology se všemi DTDL schopnostmi (telemetrie, vlastnost, vztah, komponenta nebo příkaz). 

## <a name="realestatecore-space-hierarchy"></a>Hierarchie prostoru RealEstateCore 

V RealEstateCore Ontology založeném na DTDL se k definování různých druhů mezer používá hierarchie prostoru: místnosti, budovy, zóna atd. Hierarchie ze všech těchto modelů rozšiřuje na definování různých druhů místností, budov a zón. 

Část hierarchie vypadá podobně jako v diagramu níže. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-original.png" alt-text="Diagram toku ilustruje část hierarchie RealEstateCore prostoru. Na nejvyšší úrovni se nachází element s názvem Space; je propojena se šipkou &quot;rozšiřuje&quot; o úroveň místnosti; Místnost je propojená dvěma šipkami &quot;rozšiřuje&quot; o úroveň až na ConferenceRoom a Office."::: 

Další informace o RealEstateCore Ontology najdete v tématu [*Koncepty: přijetí oboru standardní ontologie*](concepts-ontologies-adopt.md#realestatecore-smart-building-ontology).

## <a name="extending-the-realestatecore-space-hierarchy"></a>Rozšíření hierarchie prostoru RealEstateCore 

V některých případech má vaše řešení určité potřeby, které Ontology obor nepokrývá. V takovém případě vám rozšíření hierarchie umožní nadále používat Ontology v oboru a přitom ho přizpůsobovat vašim potřebám. 

V tomto článku probereme dva různé případy, kdy rozšíření hierarchie Ontology je užitečné: 

* Přidání nových rozhraní pro koncepty, které nejsou v oboru Ontology. 
* Přidání dalších vlastností (nebo relací, komponent, telemetrie nebo příkazů) do stávajících rozhraní.

### <a name="add-new-interfaces-for-new-concepts"></a>Přidat nová rozhraní pro nové koncepty 

V takovém případě budete chtít přidat rozhraní pro koncepty, které jsou potřeba pro vaše řešení, ale nejsou k dispozici v Ontology odvětví. Pokud má vaše řešení například jiné typy místností, které nejsou reprezentovány v DTDL RealEstateCore Ontology, můžete je přidat rozšířením přímo z rozhraní RealEstateCore. 

Níže uvedený příklad představuje řešení, které musí představovat "prostory pro výběr", které nejsou k dispozici v RealEstateCore Ontology. Ohnisková místnost je malý prostor určený pro lidi, kteří se můžou soustředit na úkol po dobu několika hodin najednou. 

K rozšíření oboru Ontology pomocí tohoto nového konceptu vytvořte nové rozhraní, které se [rozšíří z](concepts-models.md#model-inheritance) rozhraní v oboru Ontology. 

Po přidání rozhraní pro výběrovou místnost se v rozšířené hierarchii zobrazí nový typ místnosti. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-extended-1.png" alt-text="Vývojový diagram ilustruje hierarchii prostoru RealEstateCore od výše, s novým přídavkem. Na nejnižší úrovni s ConferenceRoom a Office je k dispozici nový element s názvem FocusRoom (také připojený pomocí šipky extends z místnosti)."::: 

### <a name="add-additional-capabilities-to-existing-interfaces"></a>Přidání dalších funkcí do stávajících rozhraní 

V takovém případě chcete přidat další vlastnosti (nebo relace, komponenty, telemetrie nebo příkazy) do rozhraní, která jsou v oboru Ontology.

V této části se zobrazí dva příklady: 
* Pokud vytváříte řešení, které zobrazuje 3D kresby mezer, které už máte v existujícím systému, můžete chtít přidružit jednotlivé digitální podstavy k 3D vykreslování (podle ID), aby se při zobrazení informací o daném prostoru mohl také načíst 3D výkres ze stávajícího systému. 
* Pokud vaše řešení potřebuje sledovat stav online konferenčních místností, možná budete chtít sledovat stav konferenční místnosti pro použití v zobrazení nebo dotazech. 

Oba příklady mohou být implementovány s novými vlastnostmi: `drawingId` vlastnost, která přidružuje 3D výkres k digitálnímu vlákna a vlastnost "online", která označuje, zda je konferenční místnost online nebo ne. 

Obvykle nechcete, aby se obor ontologyy přímo nezměnil, protože byste do něj mohli do řešení začlenit aktualizace v budoucnosti (což by přepsalo vaše dodatky). Místo toho je možné tyto druhy dodatků vytvořit ve vaší vlastní hierarchii rozhraní, která se rozšíří od RealEstateCore Ontology založeného na DTDL. Každé rozhraní, které vytvoříte, používá dědění více rozhraní k rozšíření jeho nadřazeného rozhraní RealEstateCore a jeho nadřazeného rozhraní z vaší rozšířené hierarchie rozhraní. Tento přístup vám umožní využít Ontology odvětví a dodatky společně. 

Chcete-li rozšířit oborovou Ontology, můžete vytvořit vlastní rozhraní, které se rozšíří z rozhraní v oboru Ontology a přidat nové funkce do rozšířených rozhraní. Pro každé rozhraní, které chcete zvětšit, vytvoříte nové rozhraní. Rozšířená rozhraní se napíší v DTDL (Další informace najdete v části DTDL pro rozšířená rozhraní). 

Po rozšíření části výše uvedené hierarchie vypadá rozšířená hierarchie jako diagram níže. Rozhraní Extended Space přidá `drawingId` vlastnost, která bude obsahovat ID, které přidružuje digitální vystavení s 3D vykreslováním. Rozhraní ConferenceRoom navíc přidá vlastnost "online", která bude obsahovat online stav konferenční místnosti. Prostřednictvím dědičnosti rozhraní ConferenceRoom obsahuje všechny funkce z rozhraní RealEstateCore ConferenceRoom a také všechny funkce z rozhraní Extended Space. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-extended-2.png" alt-text="Vývojový diagram ilustruje rozšířenou hierarchii RealEstateCore prostoru shora od výše, s dalšími novými doplňky. Místnost teď sdílí svou úroveň s prostorovým prvkem, který se připojí pomocí šipky extends na nový prvek místnosti vedle ConferenceRoom a Office.  Nové prvky jsou připojeny ke stávajícímu Ontology s větším poměrem &quot;rozšíření&quot;."::: 

## <a name="using-the-extended-space-hierarchy"></a>Použití rozšířené hierarchie prostorů 

Když vytváříte digitální vlákna pomocí rozšířené hierarchie prostorů, každý z nich bude mít model z rozšířené hierarchie (nikoli původní oborové Ontology) a bude zahrnovat všechny funkce z oboru Ontology a rozšířená rozhraní i v případě dědění rozhraní.

Každý model digitálního vlákna bude rozhraní z rozšířené hierarchie zobrazené v diagramu níže. 
 
:::image type="content" source="media/concepts-extending-ontologies/ontology-with-models.png" alt-text="Výpis z rozšířené hierarchie RealEstateCore prostoru, včetně prostoru (nejvyšší úrovně), jedné místnosti (střední úrovně) a ConferenceRoom, Office a FocusRoom (nižší úroveň). Názvy modelů jsou připojeny ke každému prvku (například místnost je připojena k modelu s názvem Room101)."::: 

Při dotazování na digitální vlákna pomocí ID modelu ( `IS_OF_MODEL` operátor) by měla být použita ID modelu z rozšířené hierarchie. Například, `SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:com:example:Office;1')`. 

## <a name="contributing-back-to-the-original-ontology"></a>Přispívání zpátky na původní Ontology 

V některých případech rozšíříte oborové Ontology způsobem, který je široce užitečný pro většinu uživatelů Ontology. V takovém případě byste měli zvážit přispívání rozšíření zpátky do původního Ontology. Každý Ontology má pro přispívání jiný proces, proto si Projděte podrobnosti o příspěvcích v úložišti GitHubu Ontology. 

## <a name="dtdl-for-new-interfaces"></a>DTDL pro nová rozhraní 

DTDL pro nová rozhraní, která se rozšířila přímo z oboru Ontology, by vypadala takto. 

```json
{
  "@id": "dtmi:com:example:FocusRoom;1", 
  "@type": "interface", 
  "extends": "dtmi:digitaltwins:rec_3_3:building:Office;1", 
  "@context": "dtmi:dtdl:context;2" 
} 
```

## <a name="dtdl-for-extended-interfaces"></a>DTDL pro rozšířená rozhraní 

DTDL pro rozšířená rozhraní omezená na část popsanou výše by vypadala takto. 

```json
[
  {
    "@id": "dtmi:com:example:Space;1",
    "@type": "Interface",
    "extends": "dtmi:digitaltwins:rec_3_3:core:Space;1",
    "contents": [
      {
        "@type": "Property",
        "name": "drawingid",
        "schema": "string"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Room;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:core:Room;1",
      "dtmi:com:example:Space;1"
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:ConferenceRoom;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:ConferenceRoom;1",
      "dtmi:com:example:Room;1"
    ],
    "contents": [
      {
        "@type": "Property",
        "name": "online",
        "schema": "boolean"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Office;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:Office;1", 
      "dtmi:com:example:Room;1" 
    ],
    "@context": "dtmi:dtdl:context;2" 
  }, 
  {
    "@id": "dtmi:com:example:FocusRoom;1", 
    "@type": "Interface", 
    "extends": "dtmi:com:example:Office;1", 
    "@context": "dtmi:dtdl:context;2" 
  }
]
``` 

## <a name="next-steps"></a>Další kroky

Pokračujte v cestě pro vývoj modelů na základě ontologie: [*použití strategií Ontology v cestě vývoje modelu*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).