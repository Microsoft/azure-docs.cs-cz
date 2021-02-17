---
title: Připojit Cognitive Services k dovednosti
titleSuffix: Azure Cognitive Search
description: Naučte se připojit Cognitive Services ke kanálu rozšíření AI v Azure Kognitivní hledání předplatné.
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 77735166fafe9d39dff483baa89a4b31db31275d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577934"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Připojení prostředku Cognitive Services k dovednosti v Azure Kognitivní hledání

Při konfiguraci [kanálu rozšíření AI](cognitive-search-concept-intro.md) v Azure kognitivní hledání můžete rozšířit omezený počet dokumentů zdarma. V případě větších a častých úloh byste měli připojit Fakturovatelné Cognitive Services prostředku "vše v jednom". Předplatné "All-in-One" odkazuje na "Cognitive Services" jako nabídku, a ne na jednotlivé služby, s přístupem uděleným přes jeden klíč rozhraní API.

Prostředek Cognitive Services "All-in-One" vydává [předdefinované dovednosti](cognitive-search-predefined-skills.md) , které můžete zahrnout do dovednosti:

+ [Počítačové zpracování obrazu](https://azure.microsoft.com/services/cognitive-services/computer-vision/) pro analýzu obrázků a optické rozpoznávání znaků (OCR)
+ [Analýza textu](https://azure.microsoft.com/services/cognitive-services/text-analytics/) pro detekci jazyka, rozpoznávání entit, analýzu mínění a extrakci klíčových frází
+ [Překlad textu](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)

Klíč Cognitive Services "All-in-One" je v definici dovednosti nepovinný. Když číslo denních transakcí je nižší než 20 za den, náklady jsou absorbovany. Pokud však transakce překročí tento počet, je vyžadován platný klíč prostředku, aby zpracování pokračovalo.

Klíč prostředku "All-in-One" je platný. Služba vyhledávání interně použije prostředek, který je umístěn ve stejné fyzické oblasti, i když je klíč "vše v jednom" pro prostředek v jiné oblasti. Stránka [dostupnost produktu](https://azure.microsoft.com/global-infrastructure/services/?products=search) zobrazuje oblastní dostupnost vedle sebe.

> [!NOTE]
> Pokud vynecháte předdefinované dovednosti v dovednosti, nebudete mít k dispozici Cognitive Services a nebude se vám účtovat ani v případě, že dovednosti určí klíč.

## <a name="how-billing-works"></a>Jak funguje fakturace

+ Azure Kognitivní hledání využívá klíč prostředků Cognitive Services, který zadáte na dovednosti k fakturaci na obrázek a obohacení textu. Při provádění fakturovatelných dovedností se používá [Cognitive Services cena](https://azure.microsoft.com/pricing/details/cognitive-services/)za průběžné platby.

+ Extrakce imagí je operace Azure Kognitivní hledání, ke které dochází, když se dokumenty před obohacením procházejí. Extrakce obrázků je fakturovatelná. Ceny za extrakci imagí najdete na [stránce s cenami pro Azure kognitivní hledání](https://azure.microsoft.com/pricing/details/search/).

+ K extrakci textu dojde také během fráze pro trhliny dokumentu. Není fakturovatelná.

+ Dovednosti, které nevolají Cognitive Services, včetně podmíněného, Shaper, sloučení textu a dovedností pro rozdělení textu, nejsou Fakturovatelné.

## <a name="same-region-requirement"></a>Požadavek stejné oblasti

Kognitivní hledání i Cognitive Services musí existovat ve stejné fyzické oblasti, jak je uvedeno na stránce [dostupnost produktu](https://azure.microsoft.com/global-infrastructure/services/?products=search) . Většina oblastí, které nabízí Kognitivní hledání také nabízí Cognitive Services.

Pokud se pokusíte o rozšíření AI v oblasti, která nemá obě služby, zobrazí se tato zpráva: "poskytnutý klíč není platným klíčem Cognitiveservices Account typu pro oblast vaší vyhledávací služby."

> [!NOTE]
> Některé integrované dovednosti jsou založené na neoblastní Cognitive Services (například [dovednost překladu textu](cognitive-search-skill-text-translation.md)). Používání neregionálních dovedností znamená, že váš požadavek se může obsluhovat v jiné oblasti, než je oblast Azure Kognitivní hledání. Další informace neregionální služby najdete na stránce [Cognitive Services produktu podle oblasti](https://aka.ms/allinoneregioninfo) .

## <a name="use-free-resources"></a>Použití volných prostředků

Můžete použít omezené a bezplatné možnosti zpracování pro dokončení kurzu obohacení AI a rychlé cvičení.

Prostředky Free (omezené obohacení) jsou na jeden indexer omezené na 20 dokumentů za den. Můžete [resetovat indexer](search-howto-run-reset-indexers.md) , aby se obnovila hodnota čítače.

Pokud používáte Průvodce **importem dat** pro obohacení AI, najdete možnosti "připojit Cognitive Services" na stránce **Přidat rozšíření AI (volitelné)** .

![Rozbalená Cognitive Services oddíl připojení](./media/cognitive-search-attach-cognitive-services/attach1.png "Rozbalená Cognitive Services oddíl připojení")

## <a name="use-billable-resources"></a>Použití fakturovatelných prostředků

Pro úlohy, které vytvářejí více než 20 obohacení za den, nezapomeňte připojit Fakturovatelné Cognitive Services prostředku. Doporučujeme vždy připojit Fakturovatelné Cognitive Services prostředku, i když nikdy nechcete volat rozhraní API služeb Cognitive Services. Připojení prostředku přepíše denní limit.

Účtují se vám jenom dovednosti, které volají rozhraní API služeb Cognitive Services. Neúčtují se vám žádné [vlastní dovednosti](cognitive-search-create-custom-skill-example.md)ani dovednosti, jako je například [fúze textu](cognitive-search-skill-textmerger.md), [rozdělování textu](cognitive-search-skill-textsplit.md)a [Shaper](cognitive-search-skill-shaper.md), což není založené na rozhraní API.

Pokud používáte Průvodce **importem dat** , můžete nakonfigurovat Fakturovatelné prostředky ze stránky **Přidat rozšíření AI (volitelné)** .

1. Rozbalte položku **připojit Cognitive Services** a pak vyberte **vytvořit nový prostředek Cognitive Services**. Otevře se nová karta, abyste mohli vytvořit prostředek:

   ![Vytvoření prostředku služeb Cognitive Services](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Vytvoření prostředku služeb Cognitive Services")

1. V seznamu **umístění** vyberte stejnou oblast, která má vyhledávací službu.

1. V seznamu **cenová úroveň** vyberte **S0** , abyste získali celou kolekci funkcí Cognitive Services, včetně funkcí Vision a Language, které jsou v Azure kognitivní hledání dostupné v rámci předdefinovaných dovedností.

   Pro úroveň S0 můžete pro konkrétní úlohy na [stránce s cenami Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)zjistit sazby.
  
   + V seznamu **Vybrat nabídky** se ujistěte, že je vybraná možnost **Cognitive Services** .
   + V části **jazykové** funkce se sazby pro **Analýza textu Standard** vztahují na indexování AI.
   + V rámci funkcí **Vision** se platí sazby **počítačové zpracování obrazu S1** .

1. Vyberte **vytvořit** pro zřízení nového prostředku Cognitive Services.

1. Vraťte se na předchozí kartu. Vyberte **aktualizovat** , aby se zobrazil prostředek Cognitive Services, a pak vyberte prostředek:

   ![Vyberte prostředek Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach2.png "Vyberte prostředek Cognitive Services")

1. Rozbalte část **Přidat dovednosti** v oblasti rozpoznávání, kde můžete vybrat konkrétní dovednosti v rozpoznávání, které chcete spustit na vašich datech. Dokončete zbývající část průvodce.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Připojit existující dovednosti k prostředku Cognitive Services

Pokud máte existující dovednosti, můžete ho připojit k novému nebo jinému prostředku Cognitive Services.

1. Na stránce Přehled služby Search vyberte **dovednosti**:

   ![Karta dovednosti](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Karta dovednosti")

1. Vyberte název dovednosti a pak vyberte existující prostředek, nebo vytvořte nový. Kliknutím na **OK** potvrďte provedené změny.

   ![Seznam prostředků dovednosti](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Seznam prostředků dovednosti")

   Mějte na paměti, že možnost **Free (omezená rozšíření)** vám omezí na každý den 20 dokumentů a můžete pomocí možnosti **vytvořit nový Cognitive Services prostředek** zřídit nový fakturovatelný prostředek. Pokud vytvoříte nový prostředek, vyberte **aktualizovat** , aby se aktualizoval seznam Cognitive Services prostředků, a pak vyberte prostředek.

## <a name="attach-cognitive-services-programmatically"></a>Připojit Cognitive Services programově

Při programovém definování dovednosti přidejte do `cognitiveServices` dovednosti oddíl. V této části zahrňte klíč Cognitive Services prostředku, který chcete přidružit k dovednosti. Pamatujte, že prostředek musí být ve stejné oblasti jako prostředek služby Azure Kognitivní hledání. Také zahrňte `@odata.type` a nastavte na `#Microsoft.Azure.Search.CognitiveServicesByKey` .

Následující příklad ukazuje tento model. Všimněte si `cognitiveServices` oddílu na konci definice.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
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

## <a name="example-estimate-costs"></a>Příklad: odhad nákladů

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

+ [Stránka s cenami za Azure Kognitivní hledání](https://azure.microsoft.com/pricing/details/search/)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Vytvořit dovednosti (REST)](/rest/api/searchservice/create-skillset)
+ [Jak mapovat obohacená pole](cognitive-search-output-field-mapping.md)