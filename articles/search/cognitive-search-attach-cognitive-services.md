---
title: Připojit prostředek služeb Cognitive Services dovedností – Azure Search
description: Pokyny pro připojení ke kanálu kognitivní rozšíření ve službě Azure Search All-in-one předplatné služeb Cognitive Services.
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: ffce8a2bd8a04e73acdeac037be0b10ba1a9a887
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672388"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Připojit prostředek služeb Cognitive Services dovedností v Azure Search 

Jednotky algoritmů AI [cognitive indexování kanály](cognitive-search-concept-intro.md) použitý pro dokument rozšíření ve službě Azure Search. Tyto algoritmy jsou založeny na prostředky služeb Azure Cognitive Services, včetně [pro počítačové zpracování obrazu](https://azure.microsoft.com/services/cognitive-services/computer-vision/) pro analýzu obrázků a optického rozpoznávání znaků (OCR) a [rozhraní Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) pro rozpoznávání entit Extrakce klíčových frází a dalších obohacení. Používá služba Azure Search pro účely rozšíření dokumentu, algoritmy jsou zabaleny v *dovednosti*, umístí se do *dovednosti*a odkazuje *indexer* během indexování.

Omezený počet dokumentů můžete obohatit zdarma. Nebo můžete připojit fakturovatelné prostředku služeb Cognitive Services *dovednosti* pro větší a častější úlohy. V tomto článku se dozvíte víc o připojení účtovaných prostředku služeb Cognitive Services během Azure Search rozšiřuje dokumenty [indexování](search-what-is-an-index.md).

> [!NOTE]
> Účtovaných událostí zahrnout volání rozhraní API služeb Cognitive Services a image extrakce jako součást fáze hádání dokumentu ve službě Azure Search. Neplatí žádné poplatky pro extrakci textu z dokumentů nebo dovednosti, které nejsou volat služby Cognitive Services.
>
> Provádění fakturovatelné dovednosti je na [přejít ceny služeb Cognitive Services, platit jako můžete](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny extrakce image, najdete v článku [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="same-region-requirement"></a>Požadavek na stejné oblasti

Požadujeme, aby, že existují ve stejné oblasti Azure Search a Azure Cognitive Services. V době běhu, jinak, se zobrazí tato zpráva: `"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

Neexistuje žádný způsob, jak přesunout služby napříč oblastmi. Pokud se zobrazí tato chyba, měli byste vytvořit nový prostředek služeb Cognitive Services ve stejné oblasti jako Azure Search.

## <a name="use-free-resources"></a>Použití uvolnění prostředků

K dokončení kurzu a rychlý start cvičení kognitivního vyhledávání můžete použít možnost omezená, bez zpracování.

Free (omezená obohacení) prostředků jsou omezené na 20 dokumenty za den na jedno předplatné.

1. Otevřete Průvodce importem dat:

   ![Otevřete Průvodce importem dat](media/search-get-started-portal/import-data-cmd.png "otevřete Průvodce importem dat")

1. Vyberte zdroj dat a nadále **přidat kognitivní vyhledávání (volitelné)** . Podrobný návod tohoto průvodce najdete v tématu [Import, index a dotazování pomocí nástroje portálu](search-get-started-portal.md).

1. Rozbalte **připojení služeb Cognitive Services** a pak vyberte **Free (omezená obohacení)** :

   ![Rozbalit oddíl připojení služeb Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach1.png "část rozbalený připojení služeb Cognitive Services")

1. Pokračovat k dalšímu kroku, **přidat obohacení**. Popis dovednosti, které jsou k dispozici na portálu najdete v tématu [krok 2: Přidat kognitivní dovednosti](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) v tomto rychlém startu kognitivního vyhledávání.

## <a name="use-billable-resources"></a>Fakturovatelné zdroje

Pro úlohy, které vytvoří více než 20 obohacení za den Ujistěte se, že připojení účtovaných prostředku služeb Cognitive Services. Doporučujeme vždy připojení účtovaných prostředku služeb Cognitive Services, i v případě, že chcete nikdy volat rozhraní API služeb Cognitive Services. Denní limit připojení prostředku přepíše.

Platíte jenom za dovednosti, které volají rozhraní API služeb Cognitive Services. Pro se nic neúčtuje [vlastních dovedností](cognitive-search-create-custom-skill-example.md), nebo jako dovednosti [sloučení textu](cognitive-search-skill-textmerger.md), [rozdělovač text](cognitive-search-skill-textsplit.md), a [shaper](cognitive-search-skill-shaper.md), které nejsou založené na rozhraní API.

1. Otevřete Průvodce importem dat, vyberte zdroj dat a nadále **přidat kognitivní vyhledávání (volitelné)** .

1. Rozbalte **připojení služeb Cognitive Services** a pak vyberte **vytvořit nový prostředek služeb Cognitive Services**. Otevře se nová karta, aby mohl vytvořit prostředek:

   ![Vytvoření prostředku služeb Cognitive Services](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "vytvoření prostředku služeb Cognitive Services")

1. V **umístění** vyberte oblast, ve kterém se nachází vaši službu Azure Search. Je nutné použít tuto oblast z důvodů výkonu. Pomocí této oblasti také anuluje poplatky za odchozí šířku pásma mezi oblastmi.

1. V **cenová úroveň** seznamu vyberte **S0** získat kolekci All-in-one funkcí služeb Cognitive Services, včetně funkcí pro zpracování obrazu a jazyk, které zálohují předdefinované dovednosti, které používá služba Azure Search.

   Pro úroveň S0 můžete najít kurzy pro konkrétní úlohy na [stránce s cenami služby Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + V **vyberte nabízejí** seznamu, ujistěte se, že **služeb Cognitive Services** zaškrtnuto.
   + V části **jazyk** funkce sazby za **Text Analytics standardní** použít k indexování AI.
   + V části **vizi** funkce sazby za **S1 pro zpracování obrazu počítače** použít.

1. Vyberte **vytvořit** zřídit nový prostředek služeb Cognitive Services.

1. Vrátit na předchozí kartu, která obsahuje Průvodce importem dat. Vyberte **aktualizovat** zobrazte prostředku služeb Cognitive Services a pak vyberte prostředek:

   ![Vyberte prostředek služeb Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach2.png "vyberte prostředek služeb Cognitive Services")

1. Rozbalte **přidat obohacení** části vyberte konkrétní kognitivní dovednosti, které chcete spouštět na vašich datech. Dokončete průvodce. Popis dovednosti, které jsou k dispozici na portálu najdete v tématu [krok 2: Přidat kognitivní dovednosti](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) v tomto rychlém startu kognitivního vyhledávání.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Připojit stávající dovednosti k prostředku služeb Cognitive Services

Pokud máte stávající dovednosti, abyste ho připojili k nového nebo jiného prostředku služeb Cognitive Services.

1. Na **Přehled služby** stránce **dovednosti**:

   ![Karta dovednosti](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "kartu dovednosti")

1. Vyberte název dovednosti a potom vyberte existující prostředek nebo vytvořte novou. Vyberte **OK** a potvrďte provedené změny.

   ![Seznam prostředků dovednosti](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "seznam prostředků dovedností")

   Nezapomeňte, že **Free (omezená obohacení)** možnost, jste omezeni na 20 dokumenty každý den a, který vám pomůže **vytvořit nový prostředek služeb Cognitive Services** ke zřízení nového fakturovatelné prostředku. Pokud vytvoříte nový prostředek, vyberte **aktualizovat** aktualizovat seznam prostředků služeb Cognitive Services a pak vyberte prostředek.

## <a name="attach-cognitive-services-programmatically"></a>Připojení služeb Cognitive Services prostřednictvím kódu programu

Když definujete zkušenostech prostřednictvím kódu programu, přidejte `cognitiveServices` části zkušenostech. V této části patří klíč prostředku služeb Cognitive Services, který chcete přidružit k zkušenostech. Mějte na paměti, že prostředek musí být ve stejné oblasti jako váš prostředek Azure Search. Také zahrnovat `@odata.type`a nastavte ho na `#Microsoft.Azure.Search.CognitiveServicesByKey`.

Následující příklad ukazuje tento model. Všimněte si, že `cognitiveServices` části na konci definice.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
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

Pokud chcete odhadnout náklady spojené s využitím kognitivního vyhledávání, indexování, začněte s představu o dokument průměrné vypadá tak můžete spouštět některé čísla. Například může Přibližná:

+ 1 000 souborů PDF.
+ Šest stránek každý.
+ Jednu image pro každou stránku (6 000 obrázků).
+ na stránce 3 000 znaků.

Předpokládejme kanál, který se skládá z dokumentu hádání každé PDF, obrázků a textu extrakci, optické rozpoznávání znaků (OCR) imagí a rozpoznávání entit organizací.

Ceny uvedené v tomto článku jsou hypotetické. Slouží ke znázornění procesu odhadu. Může být nižší náklady. Skutečné ceny za transakce, najdete v tématu [ceny služeb Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services).

1. Pro analýzy s obsahem textových a obrázkových dokumentu extrakce textu je aktuálně zdarma. 6 000 obrázků se předpokládá každých 1 000 obrázků extrahovat 1 USD. To je s náklady 6.00 pro tento krok.

2. Kognitivní dovednosti OCR pro OCR 6 000 obrázků v angličtině, používá nejlepší algoritmus (DescribeText). Za předpokladu, že náklady na 2,50 $ za 1 000 obrázků má být analyzován, zaplatíte 15,00 USD pro tento krok.

3. Extrakce entity má celkem tři textových záznamů na stránku. Každý záznam je 1 000 znaků. Tři textových záznamů na stránku vynásobené 6 000 stránky se rovná 18 000 textových záznamů. Za předpokladu, že 2.00 $ za 1 000 textových záznamů, tento krok by byly náklady 36.00 $.

Vložení všechno dohromady, zaplatíte přibližně $57,00 ingestovat 1 000 dokumentů PDF tohoto typu s popsané dovednosti.

## <a name="next-steps"></a>Další postup
+ [Stránce s cenami Azure Search](https://azure.microsoft.com/pricing/details/search/)
+ [Definování dovedností](cognitive-search-defining-skillset.md)
+ [Vytvoření dovedností (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Způsob mapování polí bohatších možností](cognitive-search-output-field-mapping.md)
