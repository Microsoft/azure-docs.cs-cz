---
title: Detekce animovaných postav pomocí videoindexeru
titleSuffix: Azure Media Services
description: Toto téma ukazuje, jak používat detekci animovaných znaků s Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: af608dcfbb5d98cf3116de4e14dc12bf6facb97b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989905"
---
# <a name="animated-character-detection-preview"></a>Detekce animovaných postav (náhled)

Azure Media Services Video Indexer podporuje detekci, seskupování a rozpoznávání postav v animovaném obsahu prostřednictvím integrace s [vlastní vizí služeb Cognitive Services](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Tato funkce je k dispozici prostřednictvím portálu i prostřednictvím rozhraní API.

Po nahrání animovaného videa s konkrétním modelem animace video indexer extrahuje klíčové snímky, detekuje animované postavy v těchto snímcích, seskupuje podobný znak a vybírá nejlepší vzorek. Potom odešle seskupené znaky do vlastní vize, která identifikuje znaky na základě modelů, na kterých byl trénován. 

Než začnete trénovat model, znaky jsou detekovány bezejmenně. Při přidávání jmen a trénování modelu video indexer rozpozná znaky a odpovídajícím způsobem je pojmenuje.

## <a name="flow-diagram"></a>Vývojový diagram

Následující diagram znázorňuje tok procesu detekce animovaných znaků.

![Vývojový diagram](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Účty

V závislosti na typu účtu video indexeru jsou k dispozici různé sady funkcí. Informace o tom, jak propojit svůj účet s Azure, najdete [v tématu Vytvoření účtu Video Indexer připojeného k Azure](connect-to-azure.md).

* Zkušební účet: Video Indexer používá interní účet Custom Vision k vytvoření modelu a jeho připojení k účtu Video Indexer. 
* Placený účet: svůj účet Custom Vision připojíte ke svému účtu Video Indexer (pokud ho ještě nemáte, musíte si účet nejprve vytvořit).

### <a name="trial-vs-paid"></a>Zkušební vs. placené

|Funkce|Zkušební verze|Zaplaceno|
|---|---|---|
|Vlastní účet Vision|Spravuje v zákulisí Video Indexer. |Váš vlastní účet Vision je připojen k video indexeru.|
|Počet animačních modelů|Jeden|Až 100 modelů na účet (omezení vlastní vize).|
|Trénování modelu|Video Indexer trénuje model pro nové znaky další příklady existujících znaků.|Vlastník účtu trénuje model, když jsou připraveni provést změny.|
|Pokročilé možnosti ve vlastním vidění|Žádný přístup k portálu Vlastní vize.|Modely můžete upravit sami na portálu Custom Vision.|

## <a name="use-the-animated-character-detection-with-portal"></a>Použití detekce animovaných znaků s portálem 

Tato část popisuje kroky, které je třeba provést, abyste začali používat model detekce animovaných znaků. 

Vzhledem k tomu, že ve zkušebních účtech je integrace Custom Vision spravována video indexerem, můžete začít vytvářet a používat model animovaných postav a přeskočit následující část ("Připojte svůj vlastní účet Vision").

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Připojení vlastního účtu Vision (pouze placené účty)

Pokud vlastníte placený účet Video Indexer, musíte nejprve připojit účet Custom Vision. Pokud ještě nemáte účet Vlastní vize, vytvořte si ho. Další informace naleznete [v tématu Custom Vision](../../cognitive-services/custom-vision-service/home.md).

> [!NOTE]
> Oba účty musí být ve stejné oblasti. Integrace custom vision není aktuálně podporována v oblasti Japonska.

#### <a name="connect-a-custom-vision-account-with-api"></a>Připojení vlastního účtu Vize s rozhraním API 

Chcete-li propojit vlastní účet Vision s indexerem videa, nebo změnit účet Custom Vision, který je aktuálně připojený k videoindexeru, postupujte takto:

1. Přejděte na [www.customvision.ai](https://www.customvision.ai) a přihlaste se.
1. Zkopírujte následující klávesy: 

    * Tréninkový klíč (pro školicí zdroj)
    * Predikční klíč (pro prostředek předpověď)
    * Koncový bod 
    * ID prostředku předpovědi
    
    > [!NOTE]
    > Chcete-li poskytnout všechny klíče, musíte mít dva samostatné prostředky v custom vision, jeden pro školení a jeden pro předpověď.
1. Procházejte a přihlaste se k [video indexeru](https://vi.microsoft.com/).
1. Klikněte na otazník v pravém horním rohu stránky a zvolte **ODKAZ NA ROZHRANÍ API**.
1. Kliknutím na kartu **Produkty** se ujistěte, že jste přihlášeni ke správě rozhraní API. Pokud máte připojené rozhraní API, můžete pokračovat k dalšímu kroku, jinak se přihlásit. 
1. Na portálu pro vývojáře klikněte na **odkaz na úplné rozhraní API** a přejděte na **Operations**.  
1. Vyberte **Připojit vlastní účet Vision (PREVIEW)** a klepněte na **Vyzkoušet**.
1. Vyplňte požadovaná pole a přístupový token a klepněte na tlačítko **Odeslat**. 

    Další informace o tom, jak získat token přístupu video indexeru, přejděte na [portál pro vývojáře](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token?)a naleznete [v příslušné dokumentaci](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api).  
1. Jakmile hovor vrátí odpověď 200 OK, váš účet je připojen.
1. Chcete-li ověřit připojení pomocí portálu [Video Indexer](https://vi.microsoft.com/)) :
1. Klikněte na tlačítko **Přizpůsobení modelu obsahu** v pravém horním rohu.
1. Přejděte na kartu **Animované postavy.**
1. Jakmile kliknete na Spravovat modely ve vlastní vizi"**, budete převedeni na účet Custom Vision, který jste právě připojili.

> [!NOTE]
> V současné době jsou podporovány pouze modely, které byly vytvořeny prostřednictvím Video Indexer. Modely, které jsou vytvořeny prostřednictvím vlastní vize nebude k dispozici. Kromě toho je osvědčeným postupem upravit modely, které byly vytvořeny prostřednictvím video indexeru pouze prostřednictvím platformy Video Indexer, protože změny provedené prostřednictvím vlastní vize může způsobit nezamýšlené výsledky.

### <a name="create-an-animated-characters-model"></a>Vytvoření modelu animovaných postav

1. Přejděte na web [Video Indexer](https://vi.microsoft.com/) a přihlaste se.
1. Klikněte na tlačítko přizpůsobení modelu obsahu v pravém horním rohu stránky.

    ![Přizpůsobení modelu obsahu](./media/animated-characters-recognition/content-model-customization.png)
1. Přejděte na kartu **Animované postavy** v části přizpůsobení modelu.
1. Klikněte na **Přidat model**.
1. Název modelu a klepnutím na zadat název uložte.

> [!NOTE]
> Osvědčeným postupem je mít jeden vlastní model vize pro každý animovaný seriál. 

### <a name="index-a-video-with-an-animated-model"></a>Indexování videa animovaným modelem

1. Klikněte na tlačítko **Nahrát** v horní nabídce.
1. Vyberte video, které chcete nahrát (ze souboru nebo adresy URL).
1. Klikněte na **Upřesnit možnosti**.
1. V části **Lidé / Animované postavy** zvolte **Animační modely**.
1. Pokud máte jeden model, bude vybrán automaticky, a pokud máte více modelů, můžete si vybrat příslušný z rozbalovací nabídky.
1. Klikněte na upload.
1. Po indexování videa se zjištěné znaky zobrazí v části **Animované postavy** v podokně **Přehledy.**

> [!NOTE] 
> Před označením a trénováním modelu budou všechny animované postavy pojmenovány "Neznámý #X". Po tréninku modelu budou také rozpoznány.

### <a name="customize-the-animated-characters-models"></a>Přizpůsobení modelů animovaných postav

1. Tag a vlak modelu.

    1. Označte zjištěný znak úpravou jeho názvu. Jakmile znak je trénovaný do modelu, bude rozpoznán, že další video indexovány s tímto modelem. 
    1. Pokud chcete ve videu označit animovanou postavu, přejděte na kartu **Přehledy** a klikněte na tlačítko **Upravit** v pravém horním rohu okna.
    1. V podokně **Přehledy** klikněte na některou z detekovaných animovaných postav a změňte jejich názvy z "Neznámý #X" (nebo název, který byl dříve přiřazen znaku).
    1. Po zadání nového názvu klikněte na ikonu šeku vedle nového názvu. Tím uložíte nový název v modelu v video indexeru.
    1. Po dokončení úprav všech jmen, které chcete, je třeba trénovat model.

        Otevřete stránku přizpůsobení a klikněte na kartu **Animované postavy** a poté klikněte na tlačítko **Vlak** pro trénování modelu.
         
        Pokud máte placený účet, můžete kliknout na odkaz Spravovat modely v části **Vize zákazníka** (jak je znázorněno níže). Poté budete přeposíláni na stránku modelu v **části Vlastní vize**.
 
        ![Přizpůsobení modelu obsahu](./media/animated-characters-recognition/content-model-customization-tab.png)

     1. Po trénování, jakékoli video, které bude indexováno nebo reindexovány s tímto modelem rozpozná trénované znaky. 
    Placené účty, které mají přístup ke svému účtu Custom Vision, tam mohou vidět modely a označené obrázky. Další informace o [vylepšení klasifikátoru v aplikaci Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier).

1. Odstraňte animovaný znak.

    1. Pokud chcete v přehledech videa odstranit animovanou postavu, přejděte na kartu **Přehledy** a klikněte na tlačítko **Upravit** v pravém horním rohu okna.
    1. Vyberte animovaný znak a klikněte na tlačítko **Odstranit** pod jejich jménem.

    > [!NOTE]
    > Tím odstraníte přehled z tohoto videa, ale nebude mít vliv na model.

1. Odstraňte model.

    1. Klikněte na tlačítko **Přizpůsobení modelu obsahu** v horní nabídce a přejděte na kartu Animované **postavy.**
    1. Klikněte na ikonu tři tečky napravo od modelu, který chcete odstranit, a pak na tlačítko odstranit.
    
    * Placený účet: model bude odpojen od Video Indexer a nebudete jej moci znovu připojit.
    * Zkušební účet: model bude také odstraněn z vize celního úřadu. 
    
        > [!NOTE]
        > Ve zkušebním účtu máte pouze jeden model, který můžete použít. Po odstranění nelze trénovat jiné modely.

## <a name="use-the-animated-character-detection-with-api"></a>Použití detekce animovaných znaků s rozhraním API 

1. Připojte si vlastní účet Vision.

    Pokud vlastníte placený účet Video Indexer, musíte nejprve připojit účet Custom Vision. <br/>
    Pokud ještě nemáte účet Vlastní vize, vytvořte si ho. Další informace naleznete [v tématu Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home).

    [Připojte svůj vlastní účet Vision pomocí rozhraní API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag).
1. Vytvořte model animovaných postav.

    Použijte rozhraní API [modelu animace.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag)
1. Indexujte nebo přeindexujte video.

    Použijte [rozhraní API pro přeindexování.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) 
1. Přizpůsobte si modely animovaných postav.

    Použijte rozhraní API [modelu animace vlaku.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag)

### <a name="view-the-output"></a>Zobrazit výstup

Podívejte se na animované postavy v generovaném souboru JSON.

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

* V současné době není v oblasti východní Asie podporována funkce identifikace animace.
* Znaky, které se ve videu zdají být malé nebo vzdálené, nemusí být správně identifikovány, pokud je kvalita videa špatná.
* Doporučujeme použít model pro sadu animovaných postav (například na jeden animovaný seriál).

## <a name="next-steps"></a>Další kroky

[Přehled Video Indexeru](video-indexer-overview.md)
