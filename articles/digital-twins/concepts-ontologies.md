---
title: Co je Ontology?
titleSuffix: Azure Digital Twins
description: Přečtěte si o DTDL oboru ontologie pro modelování v určité doméně
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 3393856b25040cff603ea2ef51e8adbcba78dc26
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034689"
---
# <a name="what-is-an-ontology"></a>Co je Ontology? 

Slovník řešení digitálních vláken Azure je definován pomocí [modelů](concepts-models.md), které popisují typy entit, které existují ve vašem prostředí.

Pokud je vaše řešení vázané na konkrétní odvětví, může být někdy snazší a efektivnější začít se sadou modelů pro daný obor, který už existuje, namísto vytváření vlastních modelů od začátku. Tyto předem existující sady modelů se nazývají **ontologie**. 

Obecně platí, že Ontology je sada modelů pro danou doménu, jako je například struktura budovy, systém IoT, inteligentní město, energetická mřížka, webový obsah atd. Ontologie se často používají jako schémata pro znalostní grafy, protože umožňují:
* Harmonizace softwarových komponent, dokumentace, knihoven dotazů atd.
* Snížená investice do koncepčního modelování a vývoje systémů
* Jednodušší interoperabilita dat na sémantické úrovni
* Opakované použití osvědčených postupů místo začátku nebo "rezásobení kol"

Tento článek vysvětluje, proč a jak používat ontologie pro modely digitálních vláken Azure a také jaké ontologie a nástroje pro ně jsou k dispozici ještě dnes.

## <a name="using-ontologies-for-azure-digital-twins"></a>Použití ontologie pro digitální vlákna Azure

Ontologie poskytují skvělý výchozí bod pro digitální vlákna. Zahrnují sadu modelů specifických pro doménu a vztahy mezi entitami pro návrh, vytváření a analýzu digitálního grafu s dvojitou čárkou. Ontologie umožňují vývojářům řešení zahájit digitální vypuštění z prověřeného počátečního bodu a soustředit se na řešení obchodních problémů. Ontologie od Microsoftu je také navržena tak, aby byla snadno rozšiřitelná, takže je můžete přizpůsobit pro vaše řešení. 

Použití těchto ontologie ve vašich řešeních ale může je nastavit tak, aby se zajistila plynulá integrace mezi různými partnery a dodavateli, protože ontologie může poskytovat společný slovník v rámci řešení.

Vzhledem k tomu, že modely v digitálních proskutečnostech Azure jsou reprezentovány v DTDL [(Digital DTDLing Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md), ontologie pro použití s digitálními vlákna Azure se také zapisují v. 

## <a name="strategies-for-integrating-ontologies"></a>Strategie pro integraci ontologie

Existují tři možné strategie pro integraci oboru standardních ontologie s DTDL. Můžete si vybrat ten, který vám nejlépe vyhovuje v závislosti na vašich potřebách:

| Strategie | Popis | Zdroje informací |
| --- | --- | --- |
| **Přijetí** | Své řešení můžete začít s open source DTDL Ontology, který je založený na široce přijímaných oborových standardech. Můžete buď použít tyto sady modelů předem, nebo je roztáhnout s vlastními dodatky pro vlastní řešení. | [*Koncepty: &nbsp; přijetí &nbsp; oboru &nbsp; standardních ontologie*](concepts-ontologies-adopt.md)<br><br>[*Koncepty: &nbsp; rozšíření &nbsp; ontologie*](concepts-ontologies-extend.md) |
| **Převést** | Pokud už máte stávající modely reprezentované v jiném standardním formátu, můžete je převést na DTDL, aby je bylo možné použít u digitálních vláken Azure. | [*Koncepty: &nbsp; převod &nbsp; ontologie*](concepts-ontologies-convert.md)<br><br>[*Koncepty: &nbsp; rozšíření &nbsp; ontologie*](concepts-ontologies-extend.md) |
| **Autor** | Můžete vždy vyvíjet vlastní modely DTDL od začátku, a to pomocí příslušných oborových standardů jako inspiraci. | [*Koncepty: modely DTDL*](concepts-models.md) |

### <a name="using-ontology-strategies-in-a-model-development-path"></a>Použití strategií Ontology v cestě vývoje modelu

Bez ohledu na to, jakou strategii si zvolíte pro integraci Ontology do digitálních vláken Azure, můžete postupovat podle níže uvedené cesty, která vás provede vytvořením a nahráním Ontology jako modelů DTDL.

1. Začněte kontrolou a pochopením [modelování DTDL v Azure Digital Revlákens](concepts-models.md).
1. Pokračujte ve zvolené strategii Ontology Integration [**výše: v**](concepts-ontologies-adopt.md)závislosti na vašich ontologych proveďte, [**převeďte**](concepts-ontologies-convert.md)nebo [**vytvořte**](concepts-models.md) své modely.
    1. V případě potřeby [rozšíříte](concepts-ontologies-extend.md) své Ontology, abyste ho mohli přizpůsobit vašim potřebám.
1. [Ověřte své modely](how-to-parse-models.md) , abyste ověřili, že fungují DTDL dokumenty.
1. Nahrajte dokončené modely do digitálních vláken Azure pomocí [rozhraní API](how-to-manage-model.md#upload-models) nebo ukázky, jako je odeslání [modelu digitálních vláken Azure](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/ModelUploader).

V takovém případě byste měli být schopni používat vaše modely v instanci digitálních vláken Azure. 

Můžete je vizualizovat pomocí ukázek, jako je [Průzkumník digitálních vláken Azure](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) nebo [Vizualizér digitálních vláken Azure](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer), nebo můžete přejít na jeho použití k vytváření [digitálních vláken](concepts-twins-graph.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o strategiích přijetí, převodu a vytváření ontologie:
* [*Koncepty: přijetí oboru standardní ontologie*](concepts-ontologies-adopt.md)
* [*Koncepty: převod ontologie*](concepts-ontologies-convert.md)
* [*Postupy: Správa modelů DTDL*](how-to-manage-model.md)

Nebo se dozvíte, jak se používají modely k vytváření digitálních vláken: [*Koncepty: digitální vlákna a Dvojitá graf*](concepts-twins-graph.md).