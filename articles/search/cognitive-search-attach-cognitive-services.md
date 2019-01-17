---
title: Připojit prostředek služeb Cognitive Services dovedností – Azure Search
description: Pokyny pro připojení ke kanálu kognitivní rozšíření ve službě Azure Search All-in-One předplatné služeb Cognitive Services.
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 5bffeacaa07f90a11c374061eb6c0d36fc8f86a9
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54351454"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Připojit prostředek služeb Cognitive Services dovedností v Azure Search 

Algoritmy AI řízení [kognitivního vyhledávání kanály](cognitive-search-concept-intro.md) pro zpracovávání nestrukturovaných dat, které jsou založeny na [ **prostředků služeb Cognitive Services**](https://azure.microsoft.com/services/cognitive-services/). Zdroje, jako jsou [ **pro počítačové zpracování obrazu** ](https://azure.microsoft.com/services/cognitive-services/computer-vision/) poskytují analýzu obrázků a optického rozpoznávání znaků (OCR) pro extrahování textu a struktura mimo soubory obrázků, zatímco [ **Text Analytics** ](https://azure.microsoft.com/services/cognitive-services/text-analytics/) poskytuje například entity rozpoznávání a extrakci klíčových frází, další zpracování přirozeného jazyka.

Můžete zdarma rozšiřovat omezený počet dokumentů nebo připojit fakturovatelné prostředku služeb Cognitive Services pro větší a častější úlohy. V tomto článku najdete informace o prostředku služeb Cognitive Services přidružit vaše kognitivní dovednosti a obohacení dat během [indexování Azure Search](search-what-is-an-index.md).

Pokud váš kanál se skládá pouze z [vlastních dovedností](cognitive-search-create-custom-skill-example.md), není potřeba připojení prostředku služeb Cognitive Services.

> [!NOTE]
> Od 21. prosince 2018 přidružíte k prostředku služeb Cognitive Services k dovednosti Azure Search. To umožňuje poplatky za využití jeho dovedností. K tomuto datu také začali účtovat pro extrakci image jako součást fáze hádání dokumentu. Extrakce textu z dokumentů dál nabízet bez dalších poplatků.
>
> Provedení příkazu [integrované kognitivní dovednosti](cognitive-search-predefined-skills.md) se účtuje [přejít ceny služeb Cognitive Services, platit jako můžete](https://azure.microsoft.com/pricing/details/cognitive-services), na stejná sazba jako by měl provést úlohu přímo. Extrakce Image je Azure Search účtovaných událostí, na ceny verze preview v současné době nabízena. Podrobnosti najdete v tématu [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400) nebo [fakturace](search-sku-tier.md#how-billing-works).


## <a name="use-free-resources"></a>Použití uvolnění prostředků

K dokončení kurzu a rychlý start cvičení kognitivního vyhledávání můžete použít možnost omezená, bez zpracování. 

> [!Important]
> Od 1. února 2019 **Free (omezená obohacení)** bude omezený na 20 dokumenty za den. 

1. Otevřít **importovat data** průvodce.

   ![Příkaz pro import dat](media/search-get-started-portal/import-data-cmd2.png "příkaz pro Import dat")

1. Vyberte zdroj dat a nadále **přidat kognitivní vyhledávání (volitelné)**. Podrobný návod tohoto průvodce najdete v tématu [Import, index a dotazování pomocí nástroje portálu](search-get-started-portal.md).

1. Rozbalte **připojení služeb Cognitive Services** a vyberte **Free (omezená obohacení)**.

   ![Rozbalit oddíl připojení služeb Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach1.png "rozšířit připojení služeb Cognitive Services")

Pokračovat k dalšímu kroku, **přidat obohacení**. Popis dovednosti, které jsou k dispozici na portálu najdete v tématu ["krok 2: Přidat kognitivní dovednosti"](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) v tomto rychlém startu kognitivního vyhledávání.

## <a name="use-billable-resources"></a>Fakturovatelné zdroje

Pro úlohy s více než 20 dokumenty číslování denně budete potřebovat fakturovatelné prostředku služeb Cognitive Services.

1. V **importovat data** v průvodci **připojení služeb Cognitive Services**, vyberte existující prostředek, nebo klikněte na tlačítko **vytvořit nový prostředek služeb Cognitive Services**.

1. Pro **vytvořit nový prostředek služeb Cognitive Services**, otevře se nová karta, aby mohl vytvořit prostředek. Zadejte jedinečný název prostředku.

1. Zvolte stejné umístění jako Azure Search. Kognitivní dovednosti indexování v současné době se podporuje v těchto oblastech:

  * Západní střed USA
  * Středojižní USA
  * USA – východ
  * Východní USA 2
  * Západní USA 2
  * Kanada – střed
  * Západní Evropa
  * Velká Británie – jih
  * Severní Evropa
  * Brazílie – jih
  * Jihovýchodní Asie
  * Střed Indie
  * Austrálie – východ

1. Zvolte cenovou úroveň All-in-one, **S0**. Tato úroveň poskytuje funkce pro zpracování obrazu a jazyk, které zálohují předdefinované dovednosti v kognitivního vyhledávání.

    ![Vytvořit nový prostředek služeb Cognitive Services](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "vytvořit nový prostředek služeb Cognitive Services")

1. Klikněte na tlačítko **vytvořit** zřídit nový prostředek služeb Cognitive Services. 

1. Vrátit na předchozí kartu obsahující **importovat data** průvodce. Klikněte na tlačítko **aktualizovat** zobrazte prostředku služeb Cognitive Services a pak vyberte prostředek.

   ![Vybraný prostředek služeb Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach2.png "vybraný prostředek kognitivní služby")

1. Rozbalte **přidat obohacení** části vyberte konkrétní kognitivní dovednosti, které chcete spustit nad vašimi daty a pokračujte zbytku toku. Popis dovednosti, které jsou k dispozici na portálu najdete v tématu ["krok 2: Přidat kognitivní dovednosti"](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) v tomto rychlém startu kognitivního vyhledávání.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Připojit stávající dovednosti k prostředku služeb Cognitive Services

Pokud máte stávající dovednosti, abyste ho připojili k nového nebo jiného prostředku služeb Cognitive Services.

1. Na **Přehled služby** klikněte na **dovednosti**.

   ![Karta dovednosti](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "kartu dovednosti")

1. Klikněte na název dovednosti a potom vyberte existující prostředek nebo vytvořte novou. Klikněte na tlačítko **OK** a potvrďte provedené změny. 

   ![Seznam prostředků dovednosti](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "seznam prostředků dovedností")

Vzpomeňte si, že **Free (omezená obohacení)** je omezený na 20 dokumenty každý den a že **vytvořit nový prostředek služeb Cognitive Services** slouží ke zřízení nového fakturovatelné prostředku. Pokud vytvoříte nový prostředek, vyberte **aktualizovat** aktualizovat seznam prostředků služeb Cognitive Services a pak vyberte prostředek.

## <a name="attach-cognitive-services-programmatically"></a>Připojení služeb Cognitive Services prostřednictvím kódu programu

Když definujete zkušenostech prostřednictvím kódu programu, přidejte `cognitiveServices` části zkušenostech. V části zahrňte klíč prostředku služeb Cognitive Services, který chcete přidružit k zkušenostech. Připomínáme, že prostředek musí být ve stejné oblasti jako Azure Search. Také zahrnovat `@odata.type`a nastavte ho na `#Microsoft.Azure.Search.CognitiveServicesByKey`. 

Následující příklad ukazuje tento model. Všimněte si části cognitiveServices v dolní části definice

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
        "key": "<your key goes here>"
    }
}
```

## <a name="example-estimate-costs"></a>Příklad: Odhad nákladů

Pokud chcete odhadnout náklady spojené s využitím kognitivního vyhledávání, indexování, začněte s představu o dokument průměrné vypadá tak, aby mohly běžet některá čísla. Pro účely odhadů, vám může Přibližná například:

+ 1000 PDFs
+ Šest stránek
+ Jednu image pro každou stránku (6000 imagí)
+ 3000 znaků na jednu stránku

Předpokládejme kanálu, který se skládá z dokumentu analýzy každého souboru PDF s obrázků a textu extrakce, optické rozpoznávání znaků (OCR) imagí a s názvem rozpoznávání entit organizací. 

V tomto cvičení používáme nejdražší cena za transakce. Skutečné náklady by mohla být nižší z důvodu jsou odstupňované ceny. Zobrazit [ceny služeb Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services).

1. Pro analýzy s obsahem textových a obrázkových dokumentu extrakce textu je aktuálně zdarma. 6000 imagí předpokládají každých 1 000 obrázků extrahování, ocenění 6.00 pro tento krok 1 USD.

2. Kognitivní dovednosti OCR pro OCR 6 000 obrázků v angličtině, používá nejlepší algoritmus (DescribeText). Za předpokladu, že náklady na 2,50 $ za 1 000 obrázků má být analyzován, zaplatíte 15,00 USD pro tento krok.

3. Extrakce entity museli jste celkem 3 textových záznamů na stránku. Každý záznam je 1 000 znaků. Tři textových záznamů na stránku * 6 000 stránek = 18 000 textových záznamů. Za předpokladu, že 2.00 $ za 1 000 textových záznamů, tento krok by byly náklady 36.00 $.

Vložení všechno dohromady, zaplatíte přibližně $57,00 ingestovat 1 000 dokumentů PDF z této povaha s popsané dovednosti. 

## <a name="next-steps"></a>Další postup
+ [Stránce s cenami Azure Search](https://azure.microsoft.com/pricing/details/search/)
+ [Definování dovedností](cognitive-search-defining-skillset.md)
+ [Vytvoření dovedností (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Způsob mapování polí bohatších možností](cognitive-search-output-field-mapping.md)
