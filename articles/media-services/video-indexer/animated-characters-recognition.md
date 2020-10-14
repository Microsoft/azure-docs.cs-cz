---
title: Animovaná detekce znaků pomocí Video Indexer
titleSuffix: Azure Media Services
description: Toto téma ukazuje, jak použít animovanou detekci znaků pomocí Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: 9d304767cf28c0e9230c93d5b9e7504b03ed25d4
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018878"
---
# <a name="animated-character-detection-preview"></a>Detekce animovaných postav (Preview)

Azure Media Services Video Indexer podporuje detekci, seskupení a rozpoznávání znaků v animovaném obsahu prostřednictvím integrace s [Cognitive Services vlastní vize](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Tato funkce je k dispozici prostřednictvím portálu i přes rozhraní API.

Po nahrání animovaného videa s konkrétním modelem animace Video Indexer extrahuje klíčové snímky, detekuje animované znaky v těchto rámečcích, seskupí podobný znak a zvolí nejlepší vzorek. Pak pošle seskupené znaky Custom Vision, které identifikují znaky na základě modelů, na kterých byla vyškolená. 

Než začnete s školením modelu, zjistí se namelessly znaky. Při přidávání názvů a výukovém modelu Video Indexer rozpozná tyto znaky a odpovídajícím způsobem je pojmenujte.

## <a name="flow-diagram"></a>Diagram toku

Následující diagram znázorňuje tok animovaného procesu detekce znaků.

![Diagram toku](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Účty

V závislosti na typu Video Indexer účtu jsou k dispozici různé sady funkcí. Informace o tom, jak připojit účet k Azure, najdete v tématu [Vytvoření účtu video indexer připojeného k Azure](connect-to-azure.md).

* Zkušební účet: Video Indexer používá k vytvoření modelu interní účet Custom Vision a připojuje ho ke svému účtu Video Indexer. 
* Placený účet: připojíte účet Custom Vision k účtu Video Indexer (pokud ho ještě nemáte, musíte nejdřív vytvořit účet).

### <a name="trial-vs-paid"></a>Zkušební verze vs. placená

|Funkce|Zkušební verze|Hrazen|
|---|---|---|
|Účet Custom Vision|Spravováno na pozadí Video Indexer. |Váš účet Custom Vision je připojen k Video Indexer.|
|Počet modelů animace|Jednu|Až 100 modelů na účet (omezení Custom Vision).|
|Trénování modelu|Video Indexer navlacích model pro nové znaky další příklady stávajících znaků.|Vlastník účtu naplní model, jakmile budou připraveni provést změny.|
|Rozšířené možnosti v Custom Vision|K portálu Custom Vision nemáte přístup.|Modely můžete upravit sami na portálu Custom Vision.|

## <a name="use-the-animated-character-detection-with-portal"></a>Použití animovaného rozpoznávání znaků s portálem 

Tato část popisuje kroky, které musíte provést, abyste mohli začít používat animovaný model detekce znaků. 

Vzhledem k tomu, že ve zkušebních účtech je Custom Vision integrace spravována pomocí Video Indexer, můžete začít vytvářet a používat animovaný znakový model a přeskočit následující část ("připojit účet Custom Vision").

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Připojit účet Custom Vision (jenom placené účty)

Pokud vlastníte Video Indexer placený účet, musíte nejdřív připojit účet Custom Vision. Pokud účet Custom Vision ještě nemáte, vytvořte ho prosím. Další informace najdete v tématu [Custom Vision](../../cognitive-services/custom-vision-service/overview.md).

> [!NOTE]
> Oba účty musí být ve stejné oblasti. Integrace Custom Vision v současnosti není v oblasti Japonsko podporována.

#### <a name="connect-a-custom-vision-account-with-api"></a>Připojení účtu Custom Vision k rozhraní API 

Pomocí těchto kroků se připojíte Custom Vision účtu k Video Indexer nebo změníte účet Custom Vision, který je aktuálně připojený k Video Indexer:

1. Přejděte na [www.customvision.AI](https://www.customvision.ai) a přihlaste se.
1. Zkopírujte následující klíče: 

    * Školicí klíč (pro školicí prostředek)
    * Klíč předpovědi (pro prostředek předpovědi)
    * Koncový bod 
    * ID prostředku předpovědi
    
    > [!NOTE]
    > K poskytnutí všech klíčů, které potřebujete k získání dvou samostatných prostředků v Custom Vision, jeden pro školení a jeden pro předpověď.
1. Přejděte na [video indexer](https://vi.microsoft.com/)a přihlaste se.
1. Klikněte na otazník v pravém horním rohu stránky a vyberte **Reference k rozhraní API**.
1. Ujistěte se, že jste se přihlásili k odběru API Management kliknutím na kartu **produkty** . Pokud máte připojené rozhraní API, můžete pokračovat k dalšímu kroku, jinak se přihlaste k odběru. 
1. Na portálu pro vývojáře klikněte na **úplný odkaz rozhraní API** a přejděte na **operace**.  
1. Vyberte **připojit Custom Vision účet (Preview)** a klikněte na **vyzkoušet**.
1. Vyplňte požadovaná pole i přístupového tokenu a klikněte na **Odeslat**. 

    Další informace o tom, jak získat přístupový token Video Indexer přejděte na portál pro [vývojáře](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token?)a podívejte se na [příslušnou dokumentaci](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api).  
1. Jakmile volání vrátí odpověď 200 OK, váš účet je připojený.
1. Připojení můžete ověřit tak, že přejdete na portál [video indexer](https://vi.microsoft.com/)):
1. Klikněte na tlačítko **Přizpůsobení modelu obsahu** v pravém horním rohu.
1. Přejít na kartu **animované znaky** .
1. Po kliknutí na spravovat modely v Custom Vision "* * se přenesete na účet Custom Vision, který jste právě připojili.

> [!NOTE]
> V současné době jsou podporovány pouze modely vytvořené prostřednictvím Video Indexer. Modely vytvořené prostřednictvím Custom Vision nebudou k dispozici. Kromě toho osvědčeným postupem je upravit modely vytvořené prostřednictvím Video Indexer jenom prostřednictvím Video Indexer platformy, protože změny provedené prostřednictvím Custom Vision můžou způsobit neočekávané výsledky.

### <a name="create-an-animated-characters-model"></a>Vytvořit animovaný znakový model

1. Přejděte na web [Video Indexer](https://vi.microsoft.com/) a přihlaste se.
1. Klikněte na tlačítko Přizpůsobení modelu obsahu v pravém horním rohu stránky.

    ![Přizpůsobení modelu obsahu](./media/animated-characters-recognition/content-model-customization.png)
1. V části Přizpůsobení modelu přejdete na kartu **animované znaky** .
1. Klikněte na **přidat model**.
1. Název, který zadáte, a kliknutím na ENTER uložte název.

> [!NOTE]
> Osvědčeným postupem je mít pro každou animovanou řadu jeden model vlastní vize. 

### <a name="index-a-video-with-an-animated-model"></a>Indexování videa pomocí animovaného modelu

1. V horní nabídce klikněte na tlačítko **nahrát** .
1. Vyberte video, které chcete nahrát (ze souboru nebo adresy URL).
1. Klikněte na **Upřesnit možnosti**.
1. V části **lidé/animované znaky** vyberte **modely animace**.
1. Pokud máte jeden model, vybere se automaticky a pokud máte více modelů, můžete si z rozevírací nabídky vybrat příslušnou z nich.
1. Klikněte na nahrát.
1. Po indexování videa se v podokně **přehledy** zobrazí zjištěné znaky v části **animované znaky** .

> [!NOTE] 
> Před označením a školením modelu budou všechny animované znaky pojmenovány "unknown #X". Po proškolování modelu budou také rozpoznány.

### <a name="customize-the-animated-characters-models"></a>Přizpůsobení animovaných modelů znaků

1. Označte a prohlaste model.

    1. Označte zjištěný znak úpravou jeho názvu. Jakmile je určitý znak vyškolen do modelu, bude rozpoznán jako další video indexované pomocí tohoto modelu. 
    1. Chcete-li označit animovaný znak ve videu, přejděte na kartu **přehledy** a klikněte na tlačítko **Upravit** v pravém horním rohu okna.
    1. V podokně **přehledy** klikněte na kterýkoli z nalezených animovaných znaků a změňte jejich názvy z "Neznámý #X" (nebo název, který byl dříve přiřazen ke znaku).
    1. Po zadání nového názvu klikněte na ikonu zaškrtnutí vedle nového názvu. Tím se nový název uloží do modelu ve Video Indexer.
    1. Po dokončení úprav všech požadovaných názvů budete muset vyškolit model.

        Otevřete stránku přizpůsobení a klikněte na kartu **animované znaky** a potom klikněte na tlačítko **výuka** a prohlaste svůj model.
         
        Pokud máte placený účet, můžete kliknout na odkaz **Správa modelů v zákaznických vizi** (viz níže). Pak budete přesměrováni na stránku modelu v **Custom Vision**.
 
        ![Přizpůsobení modelu obsahu](./media/animated-characters-recognition/content-model-customization-tab.png)

     1. Po vyškolení budou všechny videozáznamy, které budou indexovány nebo přeindexovány pomocí tohoto modelu, rozpoznány jako vyškolené znaky. 
    Placené účty, které mají přístup ke svému účtu Custom Vision, můžou v něm vidět modely a označené obrázky. Přečtěte si další informace o [vylepšení klasifikátoru v Custom Vision](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md).

1. Odstraní animovaný znak.

    1. Pokud chcete odstranit animovaný znak v video Insights, přejděte na kartu **přehledy** a klikněte na tlačítko **Upravit** v pravém horním rohu okna.
    1. Zvolte animovaný znak a pak klikněte na tlačítko **Odstranit** pod jeho názvem.

    > [!NOTE]
    > Tato akce odstraní vhled z tohoto videa, ale nebude mít vliv na model.

1. Odstraní model.

    1. V horní nabídce klikněte na tlačítko **Přizpůsobení modelu obsahu** a přejděte na kartu **animované znaky** .
    1. Klikněte na ikonu se třemi tečkami napravo od modelu, který chcete odstranit, a pak na tlačítku odstranit.
    
    * Placený účet: model se odpojí od Video Indexer a nebudete ho moct znovu připojit.
    * Zkušební účet: model se odstraní i z celního výhledu. 
    
        > [!NOTE]
        > Ve zkušebním účtu máte jenom jeden model, který můžete použít. Po odstranění nemůžete proškolit jiné modely.

## <a name="use-the-animated-character-detection-with-api"></a>Použití animovaného rozpoznávání znaků s rozhraním API 

1. Připojte účet Custom Vision.

    Pokud vlastníte Video Indexer placený účet, musíte nejdřív připojit účet Custom Vision. <br/>
    Pokud účet Custom Vision ještě nemáte, vytvořte ho prosím. Další informace najdete v tématu [Custom Vision](../../cognitive-services/custom-vision-service/overview.md).

    [Připojte účet Custom Vision pomocí rozhraní API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag).
1. Vytvořte animovaný znakový model.

    Použijte rozhraní API pro [Vytvoření modelu animace](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag) .
1. Indexuje nebo znovu indexuje video.

    Použijte rozhraní API pro [opakované indexování](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) . 
1. Přizpůsobení animovaných modelů znaků.

    Použijte rozhraní API pro [model animace vlaku](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag) .

### <a name="view-the-output"></a>Zobrazit výstup

Podívejte se na animované znaky ve vygenerovaném souboru JSON.

```json
"animatedCharacters": [
    {
    "videoId": "e867214582",
    "confidence": 0,
    "thumbnailId": "00000000-0000-0000-0000-000000000000",
    "seenDuration": 201.5,
    "seenDurationRatio": 0.3175,
    "isKnownCharacter": true,
    "id": 4,
    "name": "Bunny",
    "appearances": [
        {
            "startTime": "0:00:52.333",
            "endTime": "0:02:02.6",
            "startSeconds": 52.3,
            "endSeconds": 122.6
        },
        {
            "startTime": "0:02:40.633",
            "endTime": "0:03:16.6",
            "startSeconds": 160.6,
            "endSeconds": 196.6
        },
    ]
    },
]
```

## <a name="limitations"></a>Omezení

* V současné době není schopnost "Identifikace animace" v oblasti East-Asia podporována.
* Znaky, které se zdají být malé nebo daleko ve videu, nemusí být správně identifikovány, pokud kvalita videa není nízká.
* Doporučení je použití modelu na sadu animovaných znaků (například na animovanou řadu).

## <a name="next-steps"></a>Další kroky

[Přehled Video Indexeru](video-indexer-overview.md)