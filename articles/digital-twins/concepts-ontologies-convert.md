---
title: Převádění ontologie
titleSuffix: Azure Digital Twins
description: Pochopení procesu převodu standardních modelů na DTDL pro digitální vlákna Azure
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 22b41fce59bf7dbe9db1186036c5ed44f07a4aad
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484471"
---
# <a name="convert-industry-standard-ontologies-to-dtdl-for-azure-digital-twins"></a>Převod oboru standardních ontologie na DTDL pro digitální vlákna Azure

Většina [ontologie](concepts-ontologies.md) je založena na sémantických webových standardech, jako jsou [Owl](https://www.w3.org/OWL/), [RDF](https://www.w3.org/2001/sw/wiki/RDF)a [RDFS](https://www.w3.org/2001/sw/wiki/RDFS). 

Pokud chcete použít model s digitálními podmnožinami Azure, musí být ve formátu DTDL. V tomto článku najdete obecné pokyny k návrhu ve formě **vzoru převodu** pro převod RDF modelů na DTDL, aby je bylo možné použít s digitálními podprocesy Azure. 

Článek obsahuje také [**ukázkový kód převaděče**](#converter-samples) pro RDF a Owl převaděče, které lze rozšířit pro jiná schémata v budově.

## <a name="conversion-pattern"></a>Vzor převodu

K dispozici je několik knihoven třetích stran, které lze použít při převodu RDF modelů na DTDL. Některé z těchto knihoven vám umožňují načíst soubor modelu do grafu. Můžete prohledat v grafu konkrétní RDFS a konstrukce OWL a převést je na DTDL.   

Následující tabulka je příkladem, jak mohou být konstrukce RDFS a OWL mapovány na DTDL. 

| Koncept RDFS/OWL | RDFS/OWL – konstrukce | Koncept DTDL | DTDL – konstrukce |
| --- | --- | --- | --- |
| Třídy | `owl:Class`<br>Přípona IRI<br>``rdfs:label``<br>``rdfs:comment`` | Rozhraní | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Podtřídy | `owl:Class`<br>Přípona IRI<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Rozhraní | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Vlastnosti datového typu | `owl:DatatypeProperty`<br>`rdfs:label` nebo `INode`<br>`rdfs:label`<br>`rdfs:range` | Vlastnosti rozhraní | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Vlastnosti objektu | `owl:ObjectProperty`<br>`rdfs:label` nebo `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relace | `type:Relationship`<br>`name`<br>`target` (nebo vynecháno, pokud ne `rdfs:range` )<br>`comment`<br>`displayName`<br>

Následující fragment kódu jazyka C# ukazuje, jak je soubor modelu RDF načten do grafu a převeden na DTDL, pomocí knihovny [**dotNetRDF**](https://www.dotnetrdf.org/) . 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/convertRDF.cs":::

## <a name="converter-samples"></a>Ukázky konvertorů

### <a name="rdf-converter-application"></a>Aplikace převaděče RDF 

K dispozici je ukázková aplikace, která převede soubor modelu založený na RDF na [DTDL (verze 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) , který se používá ve službě Azure Digital res. Bylo ověřeno pro schéma [cihly](https://brickschema.org/ontology/) a lze je rozšířit na další schémata v stavebních oborech (například na [sestavování topologie Ontology (robot)](https://w3c-lbd-cg.github.io/bot/), [sémantické sítě senzorů](https://www.w3.org/TR/vocab-ssn/)nebo [BuildingSmart (IFC) (Industry Foundation Classes)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)).

Ukázka je aplikace příkazového řádku .NET Core s názvem **RdfToDtdlConverter**.

Ukázku můžete získat tady: [**RdfToDtdlConverter**](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Chcete-li stáhnout kód do počítače, vyberte tlačítko **Procházet kód** pod nadpisem na stránce ukázka, které vás přesměruje do úložiště GitHub pro ukázku. Vyberte tlačítko **kód** a Stáhněte si soubor **zip** a stáhněte ukázku jako *. Soubor ZIP* s názvem *RdfToDtdlConverter-main.zip*. Pak můžete soubor rozbalit a prozkoumat kód.

:::image type="content" source="media/concepts-ontologies-convert/download-repo-zip.png" alt-text="Snímek obrazovky úložiště RdfToDtdlConverter na GitHubu. Je vybráno tlačítko kód a vytvoří se malé dialogové okno, ve kterém je zvýrazněno tlačítko Stáhnout ZIP." lightbox="media/concepts-ontologies-convert/download-repo-zip.png":::

Tuto ukázku můžete použít k zobrazení vzorů převodu v kontextu a k tomu, aby jako stavební blok pro vlastní aplikace prováděl převody modelu podle vašich konkrétních potřeb.

### <a name="owl2dtdl-converter"></a>OWL2DTDL konvertor 

[**OWL2DTDL Converter**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/OWL2DTDL) je ukázka, která překládá Owl Ontology do sady deklarací rozhraní DTDL, která se dá použít se službou Azure Digital Service. Funguje taky pro Ontology sítě, které jsou vytvořené pomocí jednoho kořenového ontologyu, který znovu používá jiné ontologie prostřednictvím `owl:imports` deklarací.

Tento převaděč byl použit k překladu [základního Ontologyy reálného majetku](https://doc.realestatecore.io/3.1/full.html) na DTDL a lze ho použít pro všechny Ontology založené na Owl.

## <a name="next-steps"></a>Další kroky 

* Přečtěte si další informace o rozšíření oboru standardní ontologie, abyste splnili Vaše specifikace: [*Koncepty: rozšíření oboru ontologie*](concepts-ontologies-extend.md).

* Nebo pokračujte v cestě pro vývoj modelů na základě ontologie: [*použití strategií Ontology v cestě pro vývoj modelu*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).