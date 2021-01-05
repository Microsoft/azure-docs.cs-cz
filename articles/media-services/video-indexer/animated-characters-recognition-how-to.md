---
title: Animovaná detekce znaků pomocí Video Indexer
titleSuffix: Azure Media Services
description: Postup ukazuje, jak používat animovanou detekci znaků pomocí Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.custom: references_regions
ms.topic: how-to
ms.date: 12/07/2020
ms.author: juliako
ms.openlocfilehash: 1ee179efbe936c742f1eb51b998c10f9349c14fb
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763383"
---
# <a name="use-the-animated-character-detection-preview-with-portal-and-api"></a>Použití animovaného rozpoznávání znaků (Preview) s portálem a rozhraním API 

Azure Media Services Video Indexer podporuje detekci, seskupení a rozpoznávání znaků v animovaném obsahu, tato funkce je k dispozici prostřednictvím Azure Portal a přes rozhraní API. Přečtěte si [Toto téma přehledu](animated-characters-recognition.md) .

Tento článek ukazuje, jak používat animované rozpoznávání znaků pomocí Azure Portal a rozhraní Video Indexer API.

## <a name="use-the-animated-character-detection-with-portal"></a>Použití animovaného rozpoznávání znaků s portálem 

V rámci zkušebních účtů je Custom Vision integrace spravována Video Indexer a můžete začít vytvářet a používat animovaný znakový model. Pokud používáte zkušební účet, můžete přeskočit následující oddíl ("připojit svůj Custom Vision účet").

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Připojit účet Custom Vision (jenom placené účty)

Pokud vlastníte Video Indexer placený účet, musíte nejdřív připojit účet Custom Vision. Pokud účet Custom Vision ještě nemáte, vytvořte ho prosím. Další informace najdete v tématu [Custom Vision](../../cognitive-services/custom-vision-service/overview.md).

> [!NOTE]
> Oba účty musí být ve stejné oblasti. Integrace Custom Vision v současnosti není v oblasti Japonsko podporována.

Placené účty, které mají přístup ke svému účtu Custom Vision, můžou v něm vidět modely a označené obrázky. Přečtěte si další informace o [vylepšení klasifikátoru v Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier). 

Všimněte si, že školení modelu by se mělo provádět jenom prostřednictvím Video Indexer, a ne prostřednictvím Custom Vision webu. 

#### <a name="connect-a-custom-vision-account-with-api"></a>Připojení účtu Custom Vision k rozhraní API 

Pomocí těchto kroků se připojíte Custom Vision účtu k Video Indexer nebo změníte účet Custom Vision, který je aktuálně připojený k Video Indexer:

1. Přejděte na [www.customvision.AI](https://www.customvision.ai) a přihlaste se.
1. Zkopírujte klíče pro školicí a předpovědi prostředků:

    > [!NOTE]
    > K poskytnutí všech klíčů, které potřebujete k získání dvou samostatných prostředků v Custom Vision, jeden pro školení a jeden pro předpověď.
1. Zadejte další informace:

    * Koncový bod 
    * ID prostředku předpovědi
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
1. Jakmile kliknete na spravovat modely v Custom Vision, přenesou se na účet Custom Vision, který jste právě připojili.

> [!NOTE]
> V současné době jsou podporovány pouze modely vytvořené prostřednictvím Video Indexer. Modely vytvořené prostřednictvím Custom Vision nebudou k dispozici. Kromě toho osvědčeným postupem je upravit modely vytvořené prostřednictvím Video Indexer jenom prostřednictvím Video Indexer platformy, protože změny provedené prostřednictvím Custom Vision můžou způsobit neočekávané výsledky.

### <a name="create-an-animated-characters-model"></a>Vytvořit animovaný znakový model

1. Přejděte na web [Video Indexer](https://vi.microsoft.com/) a přihlaste se.
1. Pokud chcete ve svém účtu přizpůsobit model, vyberte tlačítko **Přizpůsobení modelu obsahu** na levé straně stránky.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="Přizpůsobení modelu obsahu v Video Indexer":::
1. V části Přizpůsobení modelu přejdete na kartu **animované znaky** .
1. Klikněte na **přidat model**.
1. Název, který zadáte, a kliknutím na ENTER uložte název.

> [!NOTE]
> Osvědčeným postupem je mít pro každou animovanou řadu jeden model vlastní vize. 

### <a name="index-a-video-with-an-animated-model"></a>Indexování videa pomocí animovaného modelu

Pro počáteční školení nahrajte aspoň dvě videa. Každý by měl být nejlépe delší než 15 minut, než bude očekáván model správného rozpoznávání. Pokud máte kratší díly, doporučujeme před školením nahrát aspoň 30 minut obsahu videa. To vám umožní sloučit skupiny, které patří ke stejnému znaku z různých scén a pozadí, a tím zvýšit pravděpodobnost, že se tento znak detekuje v následujících epizodách, které Indexujte. Chcete-li vytvořit model na více videích (epizodách), musíte je indexovat všechny pomocí stejného modelu animace. 

1. Klikněte na tlačítko **nahrát** .
1. Vyberte video, které chcete nahrát (ze souboru nebo adresy URL).
1. Klikněte na **Upřesnit možnosti**.
1. V části **lidé/animované znaky** vyberte **modely animace**.
1. Pokud máte jeden model, vybere se automaticky a pokud máte více modelů, můžete si z rozevírací nabídky vybrat příslušnou z nich.
1. Klikněte na nahrát.
1. Po indexování videa se v podokně **přehledy** zobrazí zjištěné znaky v části **animované znaky** .

Před označením a školením modelu budou všechny animované znaky pojmenovány "unknown #X". Po proškolování modelu budou také rozpoznány.

### <a name="customize-the-animated-characters-models"></a>Přizpůsobení animovaných modelů znaků

1. Pojmenujte znaky v Video Indexer.

    1. Po vytvoření modelu skupiny znaků se doporučuje tyto skupiny zkontrolovat v Custom Vision. 
    1. Chcete-li označit animovaný znak ve videu, přejděte na kartu **přehledy**   a klikněte na ****   tlačítko Upravit v pravém horním rohu okna. 
    1. V podokně **přehledy**   klikněte na kterýkoli ze zjištěných animovaných znaků a změňte jejich názvy z "Neznámý #X" na dočasný název (nebo název, který byl dříve přiřazen ke znaku). 
    1. Po zadání nového názvu klikněte na ikonu zaškrtnutí vedle nového názvu. Tím se nový název uloží do modelu ve Video Indexer. 
1. Jenom placené účty: Projděte si skupiny v Custom Vision 

    > [!NOTE]
    > Placené účty, které mají přístup ke svému účtu Custom Vision, můžou v něm vidět modely a označené obrázky. Přečtěte si další informace o [vylepšení klasifikátoru v Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier). Je důležité si uvědomit, že školení modelu by se mělo provádět jenom prostřednictvím Video Indexer (jak je popsáno v tomto topid), nikoli prostřednictvím Custom Vision webu. 

    1. Na stránce **vlastní modely** v video indexer klikněte na kartu **animované znaky** . 
    1. Klikněte na tlačítko Upravit pro model, na kterém pracujete, abyste ho mohli spravovat v Custom Vision. 
    1. Zkontrolujte každou skupinu znaků: 

        * Pokud skupina obsahuje nesouvisející image, doporučuje se je odstranit na webu Custom Vision. 
        * Pokud existují obrázky, které patří jinému znaku, změňte značku na těchto konkrétních obrázcích kliknutím na obrázek, přidáním pravé značky a odstraněním nesprávné značky. 
        * Pokud skupina není správná, což znamená, že obsahuje hlavně neznakové obrázky nebo obrázky z více znaků, můžete je odstranit v Custom Vision webu nebo v Video Indexer Insights. 
        * Algoritmus seskupení někdy rozdělí vaše znaky do různých skupin. Proto se doporučuje poskytnout všem skupinám, které patří do stejného znaku, stejný název (v Video Indexer Insights), což způsobí, že se všechny tyto skupiny budou zobrazovat jako v Custom Vision webu. 
    1. Jakmile je skupina zapnutá, ujistěte se, že počáteční název, který jste označili, odráží znak ve skupině. 
1. Trénování modelu 

    1. Po dokončení úprav všech požadovaných názvů budete muset vyškolit model. 
    1. Jakmile je určitý znak vyškolen do modelu, bude rozpoznán jako další video indexované pomocí tohoto modelu. 
    1. Otevřete stránku přizpůsobení a klikněte na kartu **animované znaky**   a potom klikněte na tlačítko **výuka** a prohlaste svůj model. Aby se zachovalo propojení mezi videem 
    
Indexer a model, nevytvářejte model na webu Custom Vision (placené účty mají přístup k Custom Vision webu), jenom v Video Indexer. Po vyškolení budou všechny videozáznamy, které budou indexovány nebo přeindexovány pomocí tohoto modelu, rozpoznány jako vyškolené znaky. 

## <a name="delete-an-animated-character-and-the-model"></a>Odstranění animovaného znaku a modelu

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
