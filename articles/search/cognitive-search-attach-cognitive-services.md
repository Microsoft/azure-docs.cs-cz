---
title: Připojení služeb Cognitive Services k sadě dovedností
titleSuffix: Azure Cognitive Search
description: Pokyny pro připojení předplatného služby Cognitive Services vše v jednom k kanálu obohacení ai v Azure Cognitive Search.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 254c912114e3f1c7a495f389bc6a6416cbde7e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472447"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Připojení prostředku služeb Cognitive Services k dovednostní sadě v Azure Cognitive Search 

Při konfiguraci kanálu obohacení v Azure Cognitive Search, můžete obohatit omezený počet dokumentů zdarma. Pro větší a častější úlohy byste měli připojit fakturovatelný prostředek služeb Cognitive Services.

V tomto článku se dozvíte, jak připojit prostředek přiřazením klíče k skillset, který definuje kanál obohacení.

## <a name="resources-used-during-enrichment"></a>Zdroje používané při obohacování

Azure Cognitive Search má závislost na kognitivních službách, včetně [počítačového vidění](https://azure.microsoft.com/services/cognitive-services/computer-vision/) pro analýzu obrázků a optické rozpoznávání znaků (OCR), [analýzy textu](https://azure.microsoft.com/services/cognitive-services/text-analytics/) pro zpracování přirozeného jazyka a dalších obohacení, jako je [překlad textu](https://azure.microsoft.com/services/cognitive-services/translator-text-api/). V kontextu obohacení v Azure Cognitive Search tyto algoritmy AI jsou zabaleny uvnitř *dovednosti*, umístěné v *skillset*a odkazuje *indexer* během indexování.

## <a name="how-billing-works"></a>Jak funguje fakturace

+ Azure Cognitive Search používá klíč prostředků cognitive services, který poskytujete na skillset účtovat za image a obohacení textu. Provádění fakturovatelných dovedností je v ceně [průběžných plateb cognitive services](https://azure.microsoft.com/pricing/details/cognitive-services/).

+ Extrakce bitové kopie je operace Azure Cognitive Search, ke které dochází, když jsou dokumenty popraskané před obohacením. Extrakce obrazu je fakturovatelná. Ceny za extrakci bitových obrázků najdete na [stránce s cenami azure kognitivního vyhledávání](https://go.microsoft.com/fwlink/?linkid=2042400).

+ Extrakce textu dochází také během dokumentu praskání fráze. Není fakturovatelný.

+ Dovednosti, které nevolají služby Cognitive Services, včetně podmíněné, shaper, sloučení textu a dovednosti rozdělení textu, nejsou fakturovatelné.

## <a name="same-region-requirement"></a>Požadavek na stejný region

Požadujeme, aby Azure Cognitive Search a Azure Cognitive Services existovaly ve stejné oblasti. V opačném případě se zobrazí tato zpráva za běhu:`"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

Neexistuje žádný způsob, jak přesunout službu mezi oblastmi. Pokud se zobrazí tato chyba, měli byste vytvořit nový prostředek služeb Cognitive Services ve stejné oblasti jako Azure Cognitive Search.

> [!NOTE]
> Některé vestavěné dovednosti jsou založeny na neregionálních kognitivních službách (například [Dovednosti překladu textu).](cognitive-search-skill-text-translation.md) Použití neregionální dovednosti znamená, že váš požadavek může být obsluhován v jiné oblasti než v oblasti Azure Cognitive Search. Další informace o neregionálních službách najdete na stránce [Cognitive Services product by region.](https://aka.ms/allinoneregioninfo)

## <a name="use-free-resources"></a>Použití volných zdrojů

Můžete použít omezenou možnost bezplatného zpracování k dokončení kurzu obohacení AI a cvičení rychlého startu.

Volné prostředky (omezené obohacení) jsou omezeny na 20 dokumentů za den na indexer. Můžete odstranit a znovu vytvořit indexer obnovit čítač.

1. Otevřete Průvodce importem dat:

   ![Otevření Průvodce importem dat](media/search-get-started-portal/import-data-cmd.png "Otevření Průvodce importem dat")

1. Zvolte zdroj dat a pokračujte v **přidání obohacení AI (volitelné).** Podrobný návod tohoto průvodce najdete v tématu [Vytvoření indexu na webu Azure Portal](search-get-started-portal.md).

1. Rozbalte **možnost Připojit služby Cognitive Services** a vyberte možnost Bezplatné **(omezené obohacení):**

   ![Rozšířená část Připojit kognitivní služby](./media/cognitive-search-attach-cognitive-services/attach1.png "Rozšířená část Připojit kognitivní služby")

1. Nyní můžete pokračovat v dalších krocích, včetně **přidání kognitivních dovedností**.

## <a name="use-billable-resources"></a>Použití fakturovatelných zdrojů

Pro úlohy, které vytvářejí více než 20 obohacení za den, nezapomeňte připojit fakturovatelné cognitive services prostředek. Doporučujeme vždy připojit fakturovatelné služby Cognitive Services prostředek, i v případě, že nikdy v úmyslu volat kognitivní služby API. Připojení prostředku přepíše denní limit.

Poplatky se vám účtují pouze za dovednosti, které volají na api služeb Cognitive Services. Neúčtují se vám [vlastní dovednosti](cognitive-search-create-custom-skill-example.md)nebo dovednosti, jako je sloučení [textu](cognitive-search-skill-textmerger.md), [rozdělovač textu](cognitive-search-skill-textsplit.md)a [tvarovač](cognitive-search-skill-shaper.md), které nejsou založeny na rozhraní API.

1. Otevřete Průvodce importem dat, zvolte zdroj dat a pokračujte **v přidávání obohacení AI (volitelné).**

1. Rozbalte **připojit služby Cognitive Services** a pak vyberte Vytvořit nový prostředek služeb Cognitive **Services**. Otevře se nová karta, abyste mohli zdroj vytvořit:

   ![Vytvoření prostředku služeb Cognitive Services](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Vytvoření prostředku služeb Cognitive Services")

1. V seznamu **Umístění** vyberte oblast, kde se nachází vaše služba Azure Cognitive Search. Ujistěte se, že používáte tuto oblast z důvodů výkonu. Použití této oblasti také ruší poplatky za odchozí šířku pásma napříč oblastmi.

1. V seznamu **Cenových úrovní** vyberte **S0,** chcete-li získat all-in-one kolekci funkcí služeb Cognitive Services, včetně funkcí Vision a Language, které zpět integrované dovednosti poskytované Azure Cognitive Search.

   Pro úroveň S0 můžete najít sazby pro konkrétní úlohy na [stránce oceňování cognitive services](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + V seznamu **Vybrat nabídku** zkontrolujte, zda je vybraná možnost **Služby Cognitive Services.**
   + V části **Jazykové** funkce se sazby pro **standard Text Analytics Standard** vztahují na indexování AI.
   + V části **Funkce Vision** platí sazby pro počítačové **vidění S1.**

1. Chcete-li zřídit nový prostředek služeb Cognitive Services, vyberte **možnost Vytvořit.**

1. Vraťte se na předchozí kartu, která obsahuje Průvodce importem dat. Vyberte **Aktualizovat,** chcete-li zobrazit prostředek služeb Cognitive Services, a pak vyberte zdroj:

   ![Výběr prostředku služeb Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach2.png "Výběr prostředku služeb Cognitive Services")

1. Rozbalte část **Přidat kognitivní dovednosti** a vyberte konkrétní kognitivní dovednosti, které chcete spustit na datech. Dokončete zbytek průvodce.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Připojení existující sady dovedností k prostředku služeb Cognitive Services

Pokud máte existující skillset, můžete ji připojit k nové nebo jiné prostředek služeb Cognitive Services.

1. Na stránce **Přehled služby** vyberte **Skillsets**:

   ![Karta Skillsets](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Karta Skillsets")

1. Vyberte název sady dovedností a pak vyberte existující zdroj nebo vytvořte nový. Chcete-li změny potvrdit, vyberte **OK.**

   ![Seznam zdrojů sady dovedností](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Seznam zdrojů sady dovedností")

   Nezapomeňte, že možnost **Bezplatné (omezené obohacení)** omezuje 20 dokumentů denně a že můžete použít **vytvořit nový prostředek služeb Cognitive Services** k zřízení nového fakturovatelného prostředku. Pokud vytvoříte nový prostředek, vyberte **Aktualizovat** aktualizovat seznam prostředků služeb Cognitive Services a vyberte prostředek.

## <a name="attach-cognitive-services-programmatically"></a>Programově připojit služby Cognitive Services

Když definujete sadu dovedností programově, přidejte `cognitiveServices` oddíl do sady dovedností. V této části zahrňte klíč prostředku služeb Cognitive Services, který chcete přidružit k skillset. Nezapomeňte, že prostředek musí být ve stejné oblasti jako prostředek Azure Cognitive Search. Také `@odata.type`zahrnout , a `#Microsoft.Azure.Search.CognitiveServicesByKey`nastavte ji na .

Následující příklad ukazuje tento vzor. Všimněte `cognitiveServices` si části na konci definice.

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

Chcete-li odhadnout náklady spojené s indexováním kognitivního vyhledávání, začněte s představou o tom, jak průměrný dokument vypadá, abyste mohli spustit některá čísla. Můžete například přibližné:

+ 1 000 PDF.
+ Každý po šesti stranách.
+ Jeden obrázek na stránku (6 000 obrázků).
+ 3 000 znaků na stránku.

Předpokládejme kanál, který se skládá z prolomení dokumentů každého PDF, extrakce obrazu a textu, optickérozpoznávání znaků (OCR) obrazů a rozpoznávání entit organizací.

Ceny uvedené v tomto článku jsou hypotetické. Používají se k ilustraci procesu odhadu. Vaše náklady by mohly být nižší. Skutečné ceny transakcí naleznete v tématu [Oceňování služeb Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services).

1. Pro vytváření obrázků s textovým a obrazovým obsahem je extrakce textu v současné době zdarma. U 6 000 obrázků předpokládejme 1 USD za každých 1 000 extrahovaných obrázků. To je cena 6,00 dolarů za tento krok.

2. Pro Rozpoznávání OCR 6 000 obrázků v angličtině používá kognitivní dovednost OCR nejlepší algoritmus (DescribeText). Za předpokladu, že náklady na 2,50 dolarů za 1000 obrázků, které mají být analyzovány, budete platit 15,00 dolarů za tento krok.

3. Pro extrakci entity byste měli celkem tři textové záznamy na stránce. Každý záznam má 1 000 znaků. Tři textové záznamy na stránku vynásobené 6 000 stránkami se rovnají 18 000 textovým záznamům. Za předpokladu, že 2,00 dolarů za 1000 textových záznamů, tento krok by stálo 36,00 dolarů.

Když to všechno dáte dohromady, zaplatíte asi 57,00 dolarů za požití 1000 dokumentů PDF tohoto typu s popsanou dovedností.

## <a name="next-steps"></a>Další kroky
+ [Stránka s cenami Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/)
+ [Jak definovat sadu dovedností](cognitive-search-defining-skillset.md)
+ [Vytvořit sadu dovedností (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Jak mapovat obohacená pole](cognitive-search-output-field-mapping.md)
