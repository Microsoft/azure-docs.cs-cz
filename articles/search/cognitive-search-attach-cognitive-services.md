---
title: Přidružte prostředek služeb Cognitive Services k dovedností v Azure Search
description: Pokyny pro předplatné služeb Cognitive Services All-in-One se připojuje k kognitivních dovedností v Azure Search
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 52efa685bba330879365f56e547881d62a52a185
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000151"
---
# <a name="associate-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Přidružte prostředek služeb Cognitive Services k dovedností v Azure Search 

Kognitivní vyhledávání extrahuje a vylepšuje data tak, aby s možností vyhledávání ve službě Azure Search. Označujeme je jako kroky extrakce a rozšiřování *kognitivní dovednosti*. Sada dovedností, které jsou volány během indexování obsahu, které jsou definovány v *dovednosti*. Můžete použít dovedností [předdefinované dovednosti](cognitive-search-predefined-skills.md) nebo vlastních dovedností. Další informace najdete v tématu [příklad: vytvoření vlastních dovedností](cognitive-search-create-custom-skill-example.md).

V tomto článku se dozvíte, jak přidružit [Azure Cognitive Services ](https://azure.microsoft.com/services/cognitive-services/) prostředek s kognitivní dovednosti.

Prostředku služeb Cognitive Services, který vyberete, bude power integrované kognitivní dovednosti. Tento prostředek se taky použije pro účely fakturace. Jakékoli obohacení, které můžete provést pomocí integrované kognitivní dovednosti budou účtovat na vrub prostředku služeb Cognitive Services, kterou jste vybrali. Se bude účtovat za stejnou sazbu, jako by měli provést stejnou úlohu pomocí prostředku služeb Cognitive Services. Zobrazit [ceny služby Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).

> [!NOTE]
> Od 21. prosince 2018 přidružíte k prostředku služeb Cognitive Services k dovednosti Azure Search. To umožňuje poplatky za využití jeho dovedností. K tomuto datu také začali účtovat pro extrakci image jako součást fáze hádání dokumentu. Extrakce textu z dokumentů dál nabízet bez dalších poplatků.
>
> Provádění předdefinované dovednosti, se účtuje [přejít ceny služeb Cognitive Services, platit jako můžete](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny extrakce bitové kopie se účtuje za ceny verze preview a je popsaný na [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="limits-when-no-cognitive-services-resource-is-selected"></a>Omezení, pokud není vybraný žádný prostředek služeb Cognitive Services
Od 1. února 2019, pokud nemáte předplatné služeb Cognitive Services přidružit vaše dovednosti se bude moct rozšířit pouze malý počet dokumentů pro bezplatné (20 dokumenty za den). 

## <a name="associate-a-cognitive-services-resource-with-a-new-skillset"></a>Přidružit nové dovednosti prostředku služeb Cognitive Services

1. Jako součást [importovat data](search-import-data-portal.md) průvodce, jakmile se připojíte ke zdroji dat, přejděte **kognitivního vyhledávání přidat** volitelný krok. Toto je druhý krok v průvodci.

1. Rozbalte **připojení služeb Cognitive Services** oddílu. Tento krok ukazuje všechny služby Cognitive Services prostředky, které máte ve stejné oblasti jako služba Azure Search.

   ![Rozbalit oddíl připojení služeb Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach1.png "rozšířit připojení služeb Cognitive Services")

1. Vyberte existující prostředek služeb Cognitive Services, nebo vyberte **vytvořit nový prostředek služeb Cognitive Services**. Pokud vyberete **Free (omezená obohacení)**, budete moci rozšířit pouze malý počet dokumentů pro bezplatné (20 dokumenty za den). Pokud vyberete **vytvořit nový prostředek služeb Cognitive Services**, otevře se nová karta, kde si můžete vytvořit prostředek. 

1. Pokud jste vytvořili nový prostředek, vyberte **aktualizovat** aktualizovat seznam prostředků služeb Cognitive Services a pak vyberte prostředek. 

   ![Vybraný prostředek služeb Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach2.png "vybraný prostředek kognitivní služby")

1. Rozbalte **přidat obohacení** části vyberte konkrétní kognitivní dovednosti, které chcete spustit nad vašimi daty a pokračujte zbytku toku.

## <a name="associate-a-cognitive-services-resource-with-an-existing-skillset"></a>Přidružit stávající dovednosti prostředku služeb Cognitive Services

1. Na **Přehled služby** stránky, vyberte **dovednosti** kartu.

   ![Karta dovednosti](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "kartu dovednosti")

1. Vyberte dovedností, které chcete upravit. Tento krok otevře okno, kde můžete upravit dovedností.

1. Vyberte existující prostředek služeb Cognitive Services, nebo vyberte **vytvořit nový prostředek služeb Cognitive Services**. Pokud vyberete **Free (omezená obohacení)**, budete moci rozšířit pouze malý počet dokumentů pro bezplatné (20 dokumenty za den). Pokud vyberete **vytvořit nový prostředek služeb Cognitive Services**, otevře se nová karta, kde si můžete vytvořit prostředek.

   <n/> 
   <img src="./media/cognitive-search-attach-cognitive-services/attach-existing2.png" width="350">

1. Pokud jste vytvořili nový prostředek, vyberte **aktualizovat** aktualizovat seznam prostředků služeb Cognitive Services a pak vyberte prostředek.

1. Vyberte **OK** a potvrďte provedené změny.

## <a name="associate-a-cognitive-services-resource-programmatically"></a>Přidružte prostředek služeb Cognitive Services prostřednictvím kódu programu

Když definujete zkušenostech prostřednictvím kódu programu, přidejte `cognitiveServices` oddílu. V části zahrňte klíč prostředku služeb Cognitive Services, který chcete přidružit k zkušenostech. Také zahrnovat `@odata.type`a nastavte ho na `#Microsoft.Azure.Search.CognitiveServicesByKey`. Následující příklad ukazuje tento model.

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
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "your key goes here"
    }
}
```
## <a name="example-estimate-the-cost-of-document-cracking-and-enrichment"></a>Příklad: Odhad nákladů na dokument hádání a obohacení
Můžete chtít odhadnout, kolik to stojí rozšiřovat typ dokumentu. Následující cvičení je pouze příklad, ale může být pro vás užitečné.

Představte si, že máte 1 000 souborů PDF. Odhadnete, že v průměru každou z těchto dokumentů má 6 stránky. Jednotlivé stránky je 1 image. Existují v průměru přibližně 3 000 znaků na jednu stránku. 

Nyní se předpokládá, že chcete provést následující kroky v rámci kanálu rozšíření:
1. Jako součást hádání dokumentu extrahujte obsah a obrázky z dokumentu.
1. Jako součást rozšíření použijte optické rozpoznávání znaků (OCR) pro každý z extrahovaných stránky, sloučí text pro všechny stránky a extrahujte jednotlivých organizací všech imagí ve Spojeném textu.

Pojďme odhadnout, kolik by byly náklady na příjem těchto 1 000 dokumentů krok za krokem:

1. Pro dokument hádání by extrahovat kombinovaným počtem ze 6 000 obrázků. Za předpokladu, že 1 USD každý 1 000 obrázků extrahování, které by byly náklady můžete 6.00 $.

2. Extrahovat text by z každé z těchto 6 000 obrázků. OCR kognitivních dovedností v angličtině, používá nejlepší algoritmus (DescribeText). Za předpokladu, že náklady na 2,50 $ za 1 000 obrázků má být analyzován, zaplatíte 15,00 USD pro tento krok.

3. Extrakce entity museli jste celkem 3 textových záznamů na stránku. (Každý záznam je 1 000 znaků.) Tři textových záznamů na stránku * 6 000 stránek = 18 000 textových záznamů. Za předpokladu, že 2.00 $ za 1 000 textových záznamů, tento krok by byly náklady 36.00 $.

Vložení všechno dohromady, zaplatíte $57,00 ingestovat 1 000 dokumentů PDF z této povaha s popsané dovednosti. V tomto cvičení předpokládá jsme nejdražší cena za transakce. To mohlo být nižší z důvodu přechod ceny. Zobrazit [ceny služeb Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services).



## <a name="next-steps"></a>Další postup
+ [Stránce s cenami Azure Search](https://azure.microsoft.com/pricing/details/search/)
+ [Definování dovedností](cognitive-search-defining-skillset.md)
+ [Vytvoření dovedností (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Způsob mapování polí bohatších možností](cognitive-search-output-field-mapping.md)
