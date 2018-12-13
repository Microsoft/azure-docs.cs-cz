---
title: Připojení služeb Cognitive Services k vaše dovednosti – Azure Search
description: Pokyny pro předplatné služeb Cognitive Services All-in-One se připojuje k kognitivních dovedností
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7f604d1b94e51db11e6d6992b7f070d64ae869dd
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317232"
---
# <a name="associate-cognitive-services-resource-with-a-skillset"></a>Přidružit dovedností prostředku služeb Cognitive Services 

> [!NOTE]
> Od 21. prosince 2018 se budou moct přidružit dovednosti Azure Search prostředku služeb Cognitive Services. To vám umožní spouštění poplatků za využití jeho dovedností. K tomuto datu také začneme pro extrakci image jako součást fáze hádání dokumentu. Extrakce textu z dokumentů se bude dál nabízet bez dalších poplatků.
>
> Provádění předdefinované dovednosti budou účtovat stávající [přejít ceny služeb Cognitive Services, platit jako můžete](https://azure.microsoft.com/pricing/details/cognitive-services/) . Ceny za extrakce Image se bude účtovat ceny verze preview a je popsaný na [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


Kognitivní vyhledávání extrahuje a vylepšuje data tak, aby s možností vyhledávání ve službě Azure Search. Označujeme je jako kroky extrakce a rozšiřování *kognitivní dovednosti*. Sada dovedností, které jsou volány během indexování obsahu, které jsou definovány v *dovednosti*. Můžete použít dovedností [předdefinované dovednosti](cognitive-search-predefined-skills.md) nebo vlastních dovedností (naleznete v tématu [příklad: vytvoření vlastních dovedností](cognitive-search-create-custom-skill-example.md) Další informace).

V tomto článku se dozvíte, jak přidružit [služeb Cognitive Services ](https://azure.microsoft.com/services/cognitive-services/) prostředek pro kognitivní dovednosti.

Prostředku služeb Cognitive Services, kterou vyberete, bude power integrované kognitivní dovednosti. Tento prostředek se taky použije pro účely fakturace. Jakékoli obohacení, které můžete provádět pomocí integrované kognitivní dovednosti budou účtovat na vrub prostředku služeb Cognitive Services, které vyberete. Se bude účtovat za stejnou sazbu, jako by měli provést stejnou úlohu pomocí prostředku služeb Cognitive Services. Zobrazit [ceny služby Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).

## <a name="limits-when-no-cognitive-services-resource-is-selected"></a>Omezení, pokud není vybraný žádný prostředek služeb Cognitive Services
Od 1. února 2019, pokud nechcete přiřazovat předplatné služeb Cognitive Services k vaše dovednosti se jen bude možné rozšířit malý počet dokumentů pro bezplatné (20 dokumenty za den). 

## <a name="associating-a-cognitive-services-resource-with-a-new-skillset"></a>Přidružení nové dovednosti prostředku služeb Cognitive Services

1. Jako součást *importovat data* prostředí po připojení ke zdroji dat přejdete *kognitivního vyhledávání přidat* volitelný krok. 

1. Rozbalte *připojení služeb Cognitive Services* oddílu. Zobrazí se všechny prostředky služby Cognitive Services, které máte ve stejné oblasti jako vaše vyhledávací služba. 
![Rozbalit připojení služby Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach1.png "rozšířit připojení služeb Cognitive Services")

1. Vyberte existující prostředek služeb Cognitive Services, nebo *vytvořit nový prostředek služeb Cognitive Services*. Pokud vyberete *Free (omezená obohacení) prostředků*, pouze moct rozšířit malý počet dokumentů pro bezplatné (20 dokumenty za den). Pokud jste klikli na *vytvořit nový prostředek služeb Cognitive Services*, na nové kartě se otevře, který vám umožní vytvoření prostředku služeb Cognitive Services. 

1. Pokud jste vytvořili nový prostředek, klikněte na tlačítko *aktualizovat* aktualizovat seznam prostředků služeb Cognitive Services a vyberte prostředek. 
![Vybraný prostředek služby Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach2.png "vybraný prostředek služby Cognitive Services")

1. Jakmile to uděláte, můžete rozšířit *přidat obohacení* části vyberte konkrétní kognitivní dovednosti, které chcete spouštět vaše data a pokračovat se zbytkem pracovního toku.

## <a name="associating-a-cognitive-services-resource-with-an-existing-skillset"></a>Přidružení stávající dovednosti prostředku služeb Cognitive Services

1. Na stránce s přehledem služby, vyberte *dovednosti* kartu. ![Karta dovednosti](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "kartu dovednosti")

1. *Klikněte na tlačítko* na dovednosti, které chcete upravit. Otevře se okno, které vám umožní upravit dovedností.

1. Vyberte existující prostředek služeb Cognitive Services, nebo *vytvořit nový prostředek služeb Cognitive Services*. Pokud vyberete *Free (omezená obohacení) prostředků*, pouze moct rozšířit malý počet dokumentů pro bezplatné (20 dokumenty za den). Pokud jste klikli na *vytvořit nový prostředek služeb Cognitive Services*, na nové kartě se otevře, který vám umožní vytvoření prostředku služeb Cognitive Services. <n/> 
<img src="./media/cognitive-search-attach-cognitive-services/attach-existing2.png" width="350">

1. Pokud jste vytvořili nový prostředek, klikněte na tlačítko *aktualizovat* aktualizovat seznam prostředků služeb Cognitive Services a vyberte prostředek.
1. Klikněte na tlačítko *OK* pro potvrzení změn

## <a name="associating-a-cognitive-services-resource-programmatically"></a>Přiřazení prostředku služeb Cognitive Services prostřednictvím kódu programu

Při definování zkušenostech prostřednictvím kódu programu, přidejte `cognitiveServices` oddílu. V části by měl obsahovat klíč prostředku služeb Cognitive Services, který chcete přidružit k dovednosti, stejně jako @odata.type, který by měl být nastaven na "#Microsoft.Azure.Search.CognitiveServicesByKey". Tento model je znázorněno v následujícím příkladu.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey"
        "description": "mycogsvcs",
        "key": "your key goes here"
    }
}
```
## <a name="example-estimating-the-cost-of-document-cracking-and-enrichment"></a>Příklad: Provádí se odhad nákladů hádání dokumentu a obohacení
Můžete chtít odhadnout, kolik to stojí rozšiřovat daného typu dokumentu. Na konci cvičení je pouze příklad, ale může být pro vás užitečné.

Představte si, že máme 1000 PDF a odhadneme, že v průměru každou z těchto dokumentů mají 6 stránky každý. Řekněme, že každý z nich má jednu image pro každou stránku pro účely tohoto cvičení. Také Představte si, že v průměru jsou přibližně 3 000 znaků na jednu stránku. 

Nyní předpokládejme, že má být v rámci kanálu obohacování proveďte následující kroky:
1. Jako součást hádání dokumentu extrahujte obsah a obrázky z dokumentu.
1. Jako součást rozšíření extrahovat OCR stránek, sloučí text pro všechny stránky a extrahujte jednotlivých organizací všech imagí ve Spojeném textu.

Pojďme odhadnout, kolik by byly náklady na příjem těchto dokumentů, krok za krokem.

1000 dokumentů:

1. Zdokumentujte hádání, jsme by extrahovat kombinovaným počtem ze 6 000 obrázků. Za předpokladu, že 1 USD každý 1 000 obrázků extrahování, které by byly náklady nám 6.00 $.

2. Jsme by extrahovat text z každé z těchto 6 000 obrázků. OCR kognitivních dovedností v angličtině, používá nejlepší algoritmus (DescribeText). Za předpokladu, že náklady na 2,50 $ za 1 000 obrázků má být analyzován, nám domácnosti platíte 15,00 USD za tento krok.

3. Extrakce entity měli jsme celkem 3 textových záznamů na jedné stránce (každý záznam je 1 000 znaků). záznamy/stránka 3 text * 6 000 stránek = 18 000 textových záznamů. Za předpokladu, že 2.00 $ / 1000 textových záznamů, tento krok by byly náklady nám 36.00 $.

Vložení všechno dohromady, abychom by platili $57,00 ingestovat 1 000 dokumentů pdf z této povaha pomocí dovedností, které je popsáno.  V tomto cvičení jsme předpokládá, že nejdražší cena za transakce, to může mít nižší z důvodu přechod ceny. Zobrazit [ceny služeb Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services).



## <a name="next-steps"></a>Další postup
+ [Azure Search ceník](https://azure.microsoft.com/pricing/details/search/)
+ [Definování dovedností](cognitive-search-defining-skillset.md)
+ [Vytvoření dovedností (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Způsob mapování polí bohatších možností](cognitive-search-output-field-mapping.md)
