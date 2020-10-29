---
title: Převod oborových standardních modelů
titleSuffix: Azure Digital Twins
description: Seznamte se s principem převodu modelů v oboru standardních (RDF/OWL) na DTDL.
author: baanders
ms.author: baanders
ms.date: 10/28/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b9e298e55f06501bbbb4271f0643012c3cb6c5fe
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92915395"
---
# <a name="convert-industry-standard-models-to-dtdl-for-azure-digital-twins"></a>Převod oborových standardních modelů na DTDL pro digitální vlákna Azure

Modely v digitálních prostředníkech Azure jsou reprezentovány v [**jazyce DTDL (Digital vlákna)**](concepts-models.md)založeném na JSON – ld. Pokud máte existující modely mimo digitální vlákna Azure, které jsou založené na oborových standardech, jako je například RDF nebo OWL, budete je muset **převést na DTDL** , abyste je mohli používat s digitálními událostmi Azure. Verze DTDL se pak stane zdrojem pravdy pro model v rámci digitálních vláken Azure.

Tento článek popisuje vzor převodu RDF nebo vlastních modelů založených na standardu na DTDL. Obsahuje:
* **Doprovodné materiály na úrovni strategie** , které je možné použít pro různé typy standardů a modelů
* [**Vzorový kód** pro RDF konvertor](#sample-converter-application)

## <a name="industry-models"></a>Modely odvětví  

Použití průmyslových modelů poskytuje bohatý výchozí bod při návrhu modelu digitálních vláken Azure. Používání průmyslových modelů pomáhá také při standardizaci a sdílení informací. 

Mezi běžné oborové modely patří:  

| Obor – svisle | Model |
| --- | --- | 
| Vytváření a Správa zařízení | [RealEstateCore](https://www.realestatecore.io/)<br>[Schéma CIHLy](https://brickschema.org/ontology/1.1/)<br>[Sestavování topologie Ontology (robot)](https://w3c-lbd-cg.github.io/bot/)<br>[Síť sémantického senzoru](https://www.w3.org/TR/vocab-ssn/)<br>[Třídy buildingSmart Industry Foundation (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/) |
| Inteligentní města | [ETSI – NGSI – LD](https://www.etsi.org/deliver/etsi_gr/CIM/001_099/008/01.01.01_60/gr_CIM008v010101p.pdf)<br>[Referenční dokumentace inteligentních aplikací (SAREF)](https://saref.etsi.org/)<br>[Fiware](https://www.fiware.org/)<br>[Otevřít & agilních inteligentních měst (OASC)](https://oascities.org/) |
| Energetická mřížka | [CIM](https://cimug.ucaiug.org/) / [IEC 61968](https://en.wikipedia.org/wiki/IEC_61968)<br>[Modely ADRM](http://www.adrm.com/) pro energetickou a komoditní obchodování |
| Automobilový průmysl | [Specifikace signálu vozidla](https://github.com/GENIVI/vehicle_signal_specification/tree/master/spec) |

V závislosti na vašich potřebách můžete použít také DTDL k přizpůsobení nebo rozšiřování průmyslových modelů nebo pro vývoj vlastního modelu od začátku. 

## <a name="create-and-edit-models"></a>Vytváření a úpravy modelů

Prvním krokem při modelování je vytváření vašich modelů. Před převodem na DTDL můžete vytvořit a dokončit úpravy vašich standardních modelů, nebo je můžete převést na DTDL ještě brzy a pokračovat v jejich úpravách jako dokumenty DTDL.

### <a name="with-industry-standards"></a>S oborovou normou

Většina průmyslových modelů (označovaných také jako **ontologie** ) jsou založené na sémantických webových standardech, jako jsou [Owl](https://www.w3.org/OWL/[), [RDF](https://www.w3.org/2001/sw/wiki/RDF)a [RDFS](https://www.w3.org/2001/sw/wiki/RDFS). 

V některých případech můžete chtít vytvořit nebo upravit model pomocí nástrojů modelu založeného na OWL. Pro návrh modelu založeného na OWL, včetně následujících, můžete použít libovolný počet nástrojů pro tvorbu modelů.
* V oblíbených příkladech jsou [WebProtégé](https://protege.stanford.edu/products.php#web-protege) a [Protégé Desktop](https://protege.stanford.edu/products.php#desktop-protege) . Modely můžete importovat v různých formátech, upravovat nebo roztáhnout model a model exportovat. 
* [WebVOWL](http://www.visualdataweb.de/webvowl/) je další oblíbený nástroj pro vizualizaci modelů. 

Pokud vytvoříte model s použitím nějakého oboru Standard, který není DTDL, budete ho muset převést na DTDL a nahrát ho do digitálních vláken Azure. 

#### <a name="common-model-file-formats"></a>Běžné formáty souborů modelu 

RDF, OWL a RDFS jsou základní stavební kameny sémantického webu. 

**RDF** poskytuje koncepční strukturu pro popis věcí, ve formě **trojí** . Tři části se skládají ze tří částí: **Subject** , **predikát** a **Object** . Objekty mohou být vytvořeny z identifikátorů URI. 

Tady jsou některé příklady RDFch trojí:

```
<LogicalDevice> <hasCapabiity> <Temperature>
<Chiller> <locatedIn> <Level1>
<Asset> <isPartOf> <Asset>  
```

Tyto příklady jsou platné RDF, ale poslední příkaz je sémanticky neplatný. V této situaci se do obrázku vloží specifikace OWL. **Owl** definuje, co můžete s RDF psát, aby bylo možné mít platný Ontology.

Tady je příklad, který využívá OWL: 

```
<Asset> <isPartOf> <Building>
<TemperatureSensor> <isType> <Sensor>
```

**RDFS** poskytuje další sémantiku slovníku, která vám umožní definovat a popsat třídy. Například jedna taková třída je `rdfs:subClassOf` :

```
<Equipment> <subClassOf> <Asset>
```

Modely je možné ukládat, importovat a exportovat v mnoha formátech souborů, včetně těchto:  
* RDF/XML 
* Želva (TTL) 
* OWL/XML 

### <a name="with-dtdl"></a>S DTDL

Digitální vlákna Azure používá k modelování **DTDL (Digital nenáročné definice)** založené na formátu JSON-ld. Libovolný model, který bude použit s digitálními akcemi Azure, bude muset být buď původně napsaný, nebo převeden na DTDL.

Při práci s modely v DTDL můžete použít [**rozšíření DTDL**](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)   dostupné pro  [Visual Studio Code](https://code.visualstudio.com/), které poskytuje ověřování syntaxe a další funkce usnadňující psaní modelů DTDL.

Můžete si přečíst další informace o modelech digitálních vláken Azure a jejich součástech v tématu [*Koncepty: vlastní modely*](concepts-models.md) a [*Postupy: Správa vlastních modelů*](how-to-manage-model.md).

## <a name="convert-models-to-dtdl"></a>Převést modely na DTDL

Pokud chcete použít model s digitálními podmnožinami Azure, musí být ve formátu DTDL. V této části se dozvíte, jak převést modely založené na RDF na DTDL, aby je bylo možné použít s digitálními vlákna Azure.

K dispozici je několik knihoven třetích stran, které lze použít při převodu RDF modelů na DTDL. Některé z těchto knihoven vám umožňují načíst soubor modelu do grafu. Můžete prohledat v grafu konkrétní RDFS a konstrukce OWL a převést je na DTDL.   

Následující tabulka je příkladem, jak mohou být konstrukce RDFS a OWL mapovány na DTDL. 

| Koncept RDFS/OWL | RDFS/OWL – konstrukce | Koncept DTDL | DTDL – konstrukce |
| --- | --- | --- | --- |
| Třídy | `owl:Class`<br>Přípona IRI<br>``rdfs:label``<br>``rdfs:comment`` | Rozhraní | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Podtřídy | `owl:Class`<br>Přípona IRI<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Rozhraní | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Vlastnosti datového typu | `owl:DatatypeProperty`<br>`rdfs:label` nebo `INode`<br>`rdfs:label`<br>`rdfs:range` | Vlastnosti rozhraní | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Vlastnosti objektu | `owl:ObjectProperty`<br>`rdfs:label` nebo `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relace | `type:Relationship`<br>`name`<br>`target` (nebo vynecháno, pokud ne `rdfs:range` )<br>`comment`<br>`displayName`<br>

Následující fragment kódu jazyka C# ukazuje, jak je soubor modelu RDF načten do grafu a převeden na DTDL, pomocí knihovny [**dotNetRDF**](https://www.dotnetrdf.org/) . 

```csharp
using VDS.RDF.Ontology; 
using VDS.RDF.Parsing; 
using Microsoft.Azure.DigitalTwins.Parser; 

//...

Console.WriteLine("Reading file..."); 

FileLoader.Load(_ontologyGraph, rdfFile.FullName); 

// Start looping through for each owl:Class 
foreach (OntologyClass owlClass in _ontologyGraph.OwlClasses) 
{ 

    // Generate a DTMI for the owl:Class 
    string Id = GenerateDTMI(owlClass); 

    if (!String.IsNullOrEmpty(Id)) 
    { 

        Console.WriteLine($"{owlClass.ToString()} -> {Id}"); 

        // Create Interface
        DtdlInterface dtdlInterface = new DtdlInterface 
        { 
            Id = Id, 
            Type = "Interface", 
            DisplayName = GetInterfaceDisplayName(owlClass), 
            Comment = GetInterfaceComment(owlClass), 
            Contents = new List<DtdlContents>() 
        }; 

        // Use DTDL 'extends' for super classes 
        IEnumerable<OntologyClass> foundSuperClasses = owlClass.DirectSuperClasses; 

        //... 
     }

     // Add interface to the list of interfaces 
     _interfaceList.Add(dtdlInterface); 
} 

// Serialize to JSON 
var json = JsonConvert.SerializeObject(_interfaceList); 

//...
``` 

## <a name="validate-and-upload-dtdl-models"></a>Ověřování a nahrání modelů DTDL

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

Jakmile se model převede a ověří, můžete **ho nahrát do instance digitálního vlákna Azure** . Další informace o tomto procesu najdete v části [*nahrání modelů*](how-to-manage-model.md#upload-models) v tématu *Postupy: Správa vlastních modelů* .

## <a name="sample-converter-application"></a>Ukázková aplikace převaděče 

K dispozici je ukázková aplikace, která převede soubor modelu založený na RDF na [DTDL (verze 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) , který se používá ve službě Azure Digital res. Ukázka je aplikace příkazového řádku .NET Core s názvem **RdfToDtdlConverter** .

Ukázku můžete získat tady: [**RdfToDtdlConverter**](https://docs.microsoft.com/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Pokud chcete stáhnout kód do počítače, stiskněte tlačítko *Stáhnout ZIP* pod nadpisem na vzorové cílové stránce. Tím se stáhne soubor *zip* pod názvem *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip* , který pak můžete rozbalit a prozkoumat.

Tuto ukázku můžete použít k zobrazení vzorů převodu v kontextu a k tomu, aby jako stavební blok pro vlastní aplikace prováděl převody modelu podle vašich konkrétních potřeb.

## <a name="next-steps"></a>Další kroky 

Přečtěte si další informace o navrhování a správě digitálních dvojitých modelů:
 
* [*Koncepty: vlastní modely*](concepts-models.md)
* [*Postupy: Správa vlastních modelů*](how-to-manage-model.md)
* [*Postupy: analýza a ověření modelů*](how-to-parse-models.md)