---
title: Připojit prostředek služeb Cognitive Services dovedností – Azure Search
description: Pokyny pro připojení ke kanálu kognitivní rozšíření ve službě Azure Search All-in-One předplatné služeb Cognitive Services.
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 09695f764ff71b274e125e90835f5314eb25c980
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683966"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Připojit prostředek služeb Cognitive Services dovedností v Azure Search 

Jednotky algoritmů AI [cognitive indexování kanály](cognitive-search-concept-intro.md) použity pro zpracování nestrukturovaných dat ve službě Azure Search. Tyto algoritmy jsou založeny na [prostředků služeb Cognitive Services](https://azure.microsoft.com/services/cognitive-services/), včetně [pro počítačové zpracování obrazu](https://azure.microsoft.com/services/cognitive-services/computer-vision/) image analýzy a optického rozpoznávání znaků (OCR) a [rozhraní Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/)pro rozpoznávání entit, extrakce klíčových frází a dalších obohacení.

Můžete zdarma obohatit omezený počet dokumentů, nebo v případě větších a častějších úloh připojit fakturovatelný prostředek Cognitive Services. V tomto článku najdete informace o prostředku služeb Cognitive Services přidružit vaše kognitivní dovednosti a obohacení dat během [indexování Azure Search](search-what-is-an-index.md).

Pokud váš kanál se skládá z dovednosti, které nesouvisí se rozhraní API služeb Cognitive Services, by měl pořád připojit prostředek služeb Cognitive Services. To tedy přepsání **Free** prostředek, který omezuje k malé množství obohacení za den. Neplatí žádné poplatky pro dovednosti, které nejsou vázány na rozhraní API služeb Cognitive Services. Zahrnout tyto dovednosti: [vlastních dovedností](cognitive-search-create-custom-skill-example.md), [sloučení textu](cognitive-search-skill-textmerger.md), [text rozdělovač](cognitive-search-skill-textsplit.md), a [shaper](cognitive-search-skill-shaper.md).

> [!NOTE]
> Jak rozbalit obor zvýšením počtu zpracování, přidáním více dokumentů nebo přidání další algoritmy AI, musíte připojit fakturovatelné prostředku služeb Cognitive Services. Poplatky se účtují při volání rozhraní API ve službě Cognitive Services a extrakci image jako součást fáze hádání dokumentu ve službě Azure Search. Neúčtují žádné poplatky pro extrakci textu z dokumentů.
>
> Provádění [integrované kognitivní dovednosti](cognitive-search-predefined-skills.md) spuštění, se účtuje [přejít ceny služeb Cognitive Services, platit jako můžete](https://azure.microsoft.com/pricing/details/cognitive-services), na stejná sazba jako by měl provést úlohu přímo. Extrakce Image je poplatek za Azure Search, projeví na [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="use-free-resources"></a>Použití uvolnění prostředků

K dokončení kurzu a rychlý start cvičení kognitivního vyhledávání můžete použít možnost omezená, bez zpracování. 

**Free (omezená obohacení)** jsou omezeny na 20 dokumenty za den na jedno předplatné.

1. Otevřít **importovat data** průvodce.

   ![Příkaz pro import dat](media/search-get-started-portal/import-data-cmd2.png "příkaz pro Import dat")

1. Vyberte zdroj dat a nadále **přidat kognitivní vyhledávání (volitelné)**. Podrobný návod tohoto průvodce najdete v tématu [Import, index a dotazování pomocí nástroje portálu](search-get-started-portal.md).

1. Rozbalte **připojení služeb Cognitive Services** a vyberte **Free (omezená obohacení)**.

   ![Rozbalit oddíl připojení služeb Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach1.png "rozšířit připojení služeb Cognitive Services")

Pokračovat k dalšímu kroku, **přidat obohacení**. Popis dovednosti, které jsou k dispozici na portálu najdete v tématu ["krok 2: Přidat kognitivní dovednosti"](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) v tomto rychlém startu kognitivního vyhledávání.

## <a name="use-billable-resources"></a>Fakturovatelné zdroje

Pro úlohy s více než 20 obohacení číslování denně budete muset připojit fakturovatelné prostředku služeb Cognitive Services. 

Pouze bude vám účtována dovednosti, které volají rozhraní API služeb Cognitive Services. Na rozhraní API bez znalosti, jako jsou [vlastních dovedností](cognitive-search-create-custom-skill-example.md), [sloučení textu](cognitive-search-skill-textmerger.md), [text rozdělovač](cognitive-search-skill-textsplit.md), a [shaper](cognitive-search-skill-shaper.md) dovednosti se nic neúčtuje.

1. Otevřít **importovat data** průvodce, vyberte zdroj dat a nadále **přidat kognitivní vyhledávání (volitelné)**. 

1. Rozbalte **připojení služeb Cognitive Services** a pak vyberte **vytvořit nový prostředek služeb Cognitive Services**. Otevře se nová karta, aby mohl vytvořit prostředek. 

   ![Vytvoření prostředku služeb Cognitive Services](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "vytvoření prostředku služeb Cognitive Services")

1. V umístění zvolte stejné oblasti jako Azure Search se vyhnout poplatkům za odchozí šířka pásma mezi oblastmi.

1. V cenová úroveň vyberte **S0** získat kolekci All-in-one funkcí služeb Cognitive Services, včetně funkcí pro zpracování obrazu a jazyk, které zálohují předdefinované dovednosti, které používá služba Azure Search. 

   Pro úroveň S0 můžete najít kurzy pro konkrétní úlohy na [stránce s cenami služby Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + V **vyberte nabízejí**, ujistěte se, že *služeb Cognitive Services* zaškrtnuto.
   + V části funkce jazyka, sazby za *Text Analytics standardní* použít k indexování AI. 
   + V části funkce pro zpracování obrazu, sazby za *S1 pro zpracování obrazu počítače* jsou použity.

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
