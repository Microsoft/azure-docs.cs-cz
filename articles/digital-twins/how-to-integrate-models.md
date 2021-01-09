---
title: Integrace oborových standardních modelů
titleSuffix: Azure Digital Twins
description: Naučte se integrovat standardní modely do DTDL pro digitální vlákna Azure, a to buď pomocí speciálních DTDL ontologie, nebo převedením stávajících ontologie.
author: baanders
ms.author: baanders
ms.date: 11/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 3376f5d5e207a33ad39cd7506998e2ee90e084ad
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051543"
---
# <a name="integrate-industry-standard-models-with-dtdl-for-azure-digital-twins"></a>Integrace standardních modelů s DTDL pro digitální vlákna Azure

Používání modelů založených na oborových standardech nebo použití standardní reprezentace Ontology, jako je například RDF nebo OWL, poskytuje bohatý výchozí bod při návrhu modelů digitálních vláken Azure. Používání průmyslových modelů pomáhá také při standardizaci a sdílení informací.

Aby bylo možné použít s digitálními podmnožinami Azure, musí být model reprezentovaný v [**jazyce DTDL (Digital vlákna)**](concepts-models.md)založeném na JSON – ld. Proto tento článek popisuje, jak znázornit standardní modely v DTDL a integraci stávajících konceptů v oboru se sémantikou DTDL, aby je mohli používat digitální vlákna Azure. Model DTDL pak slouží jako zdroj pravdy pro model v rámci digitálních vláken Azure.

Existují tři možné cesty k integraci standardních modelů s DTDL:
* **Přijmout**: řešení můžete začít s open-source DTDL Ontology, která je sestavená na široce přijímaných oborových standardech. 
* **Convert**: Pokud už máte existující modely, budete je muset převést na DTDL.
* **Autor**: můžete vždy vyvíjet vlastní modely DTDL od začátku, jak je popsáno v tématu [*Postupy: Správa vlastních modelů*](how-to-manage-model.md).

## <a name="adopt-an-open-source-dtdl-ontology"></a>Přijetí Open Source Ontology DTDL

Často je snazší začít s open-source DTDL Ontology, než začnete od prázdné stránky. 

Například řešení inteligentních budov můžou využívat open source [**DTDL RealEstateCore Ontology**](https://github.com/Azure/opendigitaltwins-building), která poskytuje běžný základ pro modelování inteligentních budov a zároveň používá oborové standardy, které zabraňují vynálezu. 

Tyto open source DTDL ontologie také poskytují osvědčené postupy pro využívání a správné rozšiřování modelů. 

## <a name="convert-existing-models-to-dtdl"></a>Převod stávajících modelů na DTDL

Většina průmyslových modelů (označovaných také jako **ontologie**) jsou založené na sémantických webových standardech, jako jsou [Owl](https://www.w3.org/OWL/), [RDF](https://www.w3.org/2001/sw/wiki/RDF)a [RDFS](https://www.w3.org/2001/sw/wiki/RDFS). 

Pokud chcete použít model s digitálními podmnožinami Azure, musí být ve formátu DTDL. Tato část popisuje obecné pokyny k návrhu ve formě **vzoru převodu** pro převod RDFch modelů na DTDL, aby je bylo možné použít s digitálními podprocesy Azure. 

Obsahuje také [ **ukázkový kód převaděče** pro RDF konvertor](#sample-converter-application), který byl ověřen pro schéma [cihly](https://brickschema.org/ontology/) a je možné ho rozšířit pro ostatní schémata v rámci stavebního průmyslu.

### <a name="conversion-pattern"></a>Vzor převodu

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

### <a name="sample-converter-application"></a>Ukázková aplikace převaděče 

K dispozici je ukázková aplikace, která převede soubor modelu založený na RDF na [DTDL (verze 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) , který se používá ve službě Azure Digital res. Bylo ověřeno pro schéma [cihly](https://brickschema.org/ontology/) a lze je rozšířit na další schémata v stavebních oborech (například na [sestavování topologie Ontology (robot)](https://w3c-lbd-cg.github.io/bot/), [sémantické sítě senzorů](https://www.w3.org/TR/vocab-ssn/)nebo [BuildingSmart (IFC) (Industry Foundation Classes)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)).

Ukázka je aplikace příkazového řádku .NET Core s názvem **RdfToDtdlConverter**.

Ukázku můžete získat tady: [**RdfToDtdlConverter**](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Pokud chcete stáhnout kód do počítače, stiskněte tlačítko *Stáhnout ZIP* pod nadpisem na vzorové cílové stránce. Tím se stáhne soubor *zip* pod názvem *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip*, který pak můžete rozbalit a prozkoumat.

Tuto ukázku můžete použít k zobrazení vzorů převodu v kontextu a k tomu, aby jako stavební blok pro vlastní aplikace prováděl převody modelu podle vašich konkrétních potřeb.

## <a name="validate-and-upload-dtdl-models"></a>Ověřování a nahrání modelů DTDL

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

Jakmile se model převede a ověří, můžete **ho nahrát do instance digitálního vlákna Azure**. Další informace o tomto procesu najdete v části [*nahrání modelů*](how-to-manage-model.md#upload-models) v tématu *Postupy: Správa vlastních modelů*.

## <a name="next-steps"></a>Další kroky 

Přečtěte si další informace o navrhování a správě digitálních dvojitých modelů:
 
* [*Koncepty: vlastní modely*](concepts-models.md)
* [*Postupy: Správa vlastních modelů*](how-to-manage-model.md)
* [*Postupy: analýza a ověření modelů*](how-to-parse-models.md)