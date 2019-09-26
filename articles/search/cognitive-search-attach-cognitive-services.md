---
title: Připojení Cognitive Services prostředku pomocí dovednosti-Azure Search
description: Pokyny pro připojení Cognitive Servicesho předplatného ke kanálu rozšíření pro rozpoznávání v Azure Search.
manager: nitinme
author: LuisCabrer
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: luisca
ms.openlocfilehash: 113286f829b628d4740fbba34e7279741a934aef
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265932"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Připojení prostředku Cognitive Services k dovednosti v Azure Search 

Algoritmy AI řídí [kanály indexování](cognitive-search-concept-intro.md) používané při obohacení dokumentů v Azure Search. Tyto algoritmy jsou založené na prostředcích Azure Cognitive Services, včetně [počítačové zpracování obrazu](https://azure.microsoft.com/services/cognitive-services/computer-vision/) pro analýzu obrázků a optické rozpoznávání znaků (OCR) a [Analýza textu](https://azure.microsoft.com/services/cognitive-services/text-analytics/) pro rozpoznávání entit, extrakci klíčových frází a jiné obohacení. . Jak je používá Azure Search pro účely rozšíření dokumentu jsou algoritmy zabaleny do *dovedností*, umístěné v *dovednosti*a odkazovány *indexerem* během indexování.

Omezený počet dokumentů můžete rozšířit zdarma. Případně můžete k *dovednosti* připojit fakturovatelný prostředek Cognitive Services pro větší a častější zatížení. V tomto článku se dozvíte, jak připojit fakturovatelný Cognitive Services prostředek k rozšíření dokumentů během Azure Search [indexování](search-what-is-an-index.md).

> [!NOTE]
> Fakturovatelné události zahrnují volání rozhraní API služeb Cognitive Services a extrakce obrázku v rámci fáze pro vystavování dokumentu v Azure Search. Za extrakci textu z dokumentů nebo pro dovednosti, které nevolají Cognitive Services, se neúčtují žádné poplatky.
>
> Při provádění fakturovatelných dovedností se používá [Cognitive Services cena](https://azure.microsoft.com/pricing/details/cognitive-services/)za průběžné platby. Ceny za extrakci imagí najdete na [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="same-region-requirement"></a>Požadavek stejné oblasti

Vyžadujeme, Azure Search a Azure Cognitive Services existují v rámci stejné oblasti. V opačném případě se tato zpráva zobrazí v době běhu:`"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

Neexistuje žádný způsob, jak přesunout službu mezi oblastmi. Pokud se zobrazí tato chyba, měli byste vytvořit nový prostředek Cognitive Services ve stejné oblasti jako Azure Search.

> [!NOTE]
> Některé integrované dovednosti jsou založené na neoblastní Cognitive Services (například [dovednost překladu textu](cognitive-search-skill-text-translation.md)). Uvědomte si, že pokud do svého dovednosti přidáte jakoukoli z těchto dovedností, že vaše data nejsou zaručená zůstat ve stejné oblasti jako Azure Search nebo Cognitive Services prostředků. Další podrobnosti najdete na [stránce Stav služby](https://aka.ms/allinoneregioninfo) .

## <a name="use-free-resources"></a>Použití volných prostředků

Můžete použít omezené a bezplatné možnosti zpracování k dokončení kurzu pro hledání vnímání a rychlé cvičení.

Prostředky Free (omezená rozšíření) jsou omezené na 20 dokumentů za den v rámci předplatného.

1. Otevřete Průvodce importem dat:

   ![Otevření Průvodce importem dat](media/search-get-started-portal/import-data-cmd.png "Otevření Průvodce importem dat")

1. Vyberte zdroj dat a pokračujte v **přidávání vyhledávání rozpoznávání (volitelné)** . Podrobný návod tohoto průvodce najdete v tématu [Import, index a dotazování pomocí nástrojů portálu](search-get-started-portal.md).

1. Rozbalte položku **připojit Cognitive Services** a pak vyberte možnost **Free (omezená rozšíření)** :

   ![Rozbalená Cognitive Services oddíl připojení](./media/cognitive-search-attach-cognitive-services/attach1.png "Rozbalená Cognitive Services oddíl připojení")

1. Pokračujte k dalšímu kroku a **přidejte obohacení**. Popis dovedností dostupných na portálu najdete v části [krok 2: Přidejte dovednosti](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) rozpoznávání v rychlém startu hledání rozpoznávání.

## <a name="use-billable-resources"></a>Použití fakturovatelných prostředků

Pro úlohy, které vytvářejí více než 20 obohacení za den, nezapomeňte připojit Fakturovatelné Cognitive Services prostředku. Doporučujeme vždy připojit Fakturovatelné Cognitive Services prostředku, i když nikdy nechcete volat rozhraní API služeb Cognitive Services. Připojení prostředku přepíše denní limit.

Účtují se vám jenom dovednosti, které volají rozhraní API služeb Cognitive Services. Neúčtují se vám žádné [vlastní dovednosti](cognitive-search-create-custom-skill-example.md)ani dovednosti, jako je například [fúze textu](cognitive-search-skill-textmerger.md), [rozdělování textu](cognitive-search-skill-textsplit.md)a [Shaper](cognitive-search-skill-shaper.md), což není založené na rozhraní API.

1. Otevřete Průvodce importem dat, vyberte zdroj dat a pokračujte v **přidávání vyhledávání rozpoznávání (volitelné)** .

1. Rozbalte položku **připojit Cognitive Services** a pak vyberte **vytvořit nový prostředek Cognitive Services**. Otevře se nová karta, abyste mohli vytvořit prostředek:

   ![Vytvoření prostředku Cognitive Services](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Vytvoření prostředku Cognitive Services")

1. V seznamu **umístění** vyberte oblast, ve které se nachází vaše služba Azure Search. Nezapomeňte tuto oblast používat z důvodů výkonu. Použití této oblasti také nevrací poplatky za odchozí šířku pásma napříč oblastmi.

1. V seznamu **cenová úroveň** vyberte **S0** , abyste získali celou kolekci funkcí Cognitive Services, včetně funkcí Vision a Language, které zpětně využívají předdefinované dovednosti používané v Azure Search.

   Pro úroveň S0 můžete pro konkrétní úlohy na [stránce s cenami Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)zjistit sazby.
  
   + V seznamu **Vybrat nabídky** se ujistěte, že je vybraná možnost **Cognitive Services** .
   + V části **jazykové** funkce se sazby pro **Analýza textu Standard** vztahují na indexování AI.
   + V rámci funkcí **Vision** se platí sazby **počítačové zpracování obrazu S1** .

1. Vyberte **vytvořit** pro zřízení nového prostředku Cognitive Services.

1. Vraťte se na předchozí kartu, která obsahuje Průvodce importem dat. Vyberte **aktualizovat** , aby se zobrazil prostředek Cognitive Services, a pak vyberte prostředek:

   ![Vyberte prostředek Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach2.png "Vyberte prostředek Cognitive Services")

1. Rozbalením části **Přidat obohacení** můžete vybrat konkrétní dovednosti v oblasti rozpoznávání, které chcete spustit na vašich datech. Dokončete zbývající část průvodce. Popis dovedností dostupných na portálu najdete v části [krok 2: Přidejte dovednosti](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) rozpoznávání v rychlém startu hledání rozpoznávání.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Připojit existující dovednosti k prostředku Cognitive Services

Pokud máte existující dovednosti, můžete ho připojit k novému nebo jinému prostředku Cognitive Services.

1. Na stránce **Přehled služby** vyberte **dovednosti**:

   ![Karta dovednosti](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Karta dovednosti")

1. Vyberte název dovednosti a pak vyberte existující prostředek, nebo vytvořte nový. Kliknutím na **OK** potvrďte provedené změny.

   ![Seznam prostředků dovednosti](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Seznam prostředků dovednosti")

   Mějte na paměti, že možnost **Free (omezená rozšíření)** vám omezí na každý den 20 dokumentů a můžete pomocí možnosti **vytvořit nový Cognitive Services prostředek** zřídit nový fakturovatelný prostředek. Pokud vytvoříte nový prostředek, vyberte **aktualizovat** , aby se aktualizoval seznam Cognitive Services prostředků, a pak vyberte prostředek.

## <a name="attach-cognitive-services-programmatically"></a>Připojit Cognitive Services programově

Při programovém definování dovednosti přidejte `cognitiveServices` do dovednosti oddíl. V této části zahrňte klíč Cognitive Services prostředku, který chcete přidružit k dovednosti. Pamatujte, že prostředek musí být ve stejné oblasti jako prostředek Azure Search. Také zahrňte `@odata.type`a nastavte na `#Microsoft.Azure.Search.CognitiveServicesByKey`.

Následující příklad ukazuje tento model. Všimněte si `cognitiveServices` oddílu na konci definice.

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

Chcete-li odhadnout náklady spojené s indexováním vyhledávání v rozpoznávání, začněte s nápadem, jak průměrný dokument vypadá, takže můžete spustit některá čísla. Můžete například přibližně:

+ 1 000 souborů PDF.
+ Každé šest stránek.
+ Jedna image na stránku (6 000 imagí).
+ 3 000 znaků na stránku

Představte si kanál, který obsahuje dokument odhalující jednotlivé soubory PDF, extrakci obrázků a textu, optické rozpoznávání znaků (OCR) obrázků a rozpoznávání entit organizací.

Ceny uvedené v tomto článku jsou hypotetické. Slouží k ilustraci procesu odhadu. Vaše náklady mohou být nižší. Skutečné ceny transakcí najdete v tématu o [cenách Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services).

1. V případě, že se má dokument vylomit pomocí obsahu textu a obrázku, je extrakce textu aktuálně zadarmo. Pro Image 6 000 Předpokládejme $1 pro 1 000 každý z extrahovaných imagí. To je cena $6,00 pro tento krok.

2. Pro optické rozpoznávání obrázků 6 000 imagí v angličtině používá rozpoznávání OCR nejlepší algoritmus (DescribeText). Za předpokladu, že se analyzují náklady na $2,50 za 1 000 imagí, platíte $15,00 pro tento krok.

3. Pro extrakci entit byste měli celkem tři textové záznamy na stránku. Každý záznam má 1 000 znaků. Tři textové záznamy na stránce vynásobené 6 000 stránky jsou rovny 18 000 textových záznamů. Za předpokladu, že $2,00 na 1 000 textové záznamy, bude tento krok nákladově $36,00.

To všechno dohromady zaplatíte přibližně $57,00, abyste ingestují 1 000 dokumentů PDF tohoto typu s popisem dovednosti.

## <a name="next-steps"></a>Další kroky
+ [Stránka s cenami Azure Search](https://azure.microsoft.com/pricing/details/search/)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Vytvořit dovednosti (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Jak mapovat obohacená pole](cognitive-search-output-field-mapping.md)
