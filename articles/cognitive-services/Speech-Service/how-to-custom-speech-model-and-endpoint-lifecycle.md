---
title: Životní cyklus modelu a koncového bodu služby Custom Speech-Speech
titleSuffix: Azure Cognitive Services
description: Custom Speech poskytuje základní modely pro přizpůsobení a umožňuje vytvářet vlastní modely z vašich dat. Tento článek popisuje časové osy modelů a koncových bodů, které používají tyto modely.
services: cognitive-services
author: heikora
manager: dongli
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/2/2021
ms.author: heikora
ms.openlocfilehash: b82a732533c3d069b519b07c3209d4b96c472900
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385021"
---
# <a name="model-and-endpoint-lifecycle"></a>Životní cyklus modelu a koncového bodu

Naše standardní (nepřizpůsobené) řeč je postavená na základě modelů AI, které volají základní modely. Ve většině případů je pro každý mluvený jazyk, který podporujeme, základem jiný základní model.  Službu rozpoznávání řeči aktualizujeme novými základními modely každé několik měsíců, aby se zlepšila přesnost a kvalita.  
Pomocí Custom Speech se vlastní modely vytvářejí pomocí přizpůsobení zvoleného základního modelu s daty z vašeho konkrétního scénáře zákazníka. Když vytvoříte vlastní model, tento model se neaktualizuje ani nezmění, i když se odpovídající základní model, ze kterého byl přizpůsobený, aktualizuje ve standardní službě pro rozpoznávání řeči.  
Tato zásada vám umožní průběžně používat konkrétní vlastní model po dlouhou dobu, když máte vlastní model, který vyhovuje vašim potřebám.  Doporučujeme ale pravidelně znovu vytvořit vlastní model, abyste se mohli přizpůsobit od nejnovějšího základního modelu a využít tak lepší přesnost a kvalitu.

K dalším klíčovým podmínkám souvisejícím s životním cyklem modelu patří:

* **Přizpůsobení**: Vytvoření základního modelu a jeho přizpůsobení do vaší domény/scénáře pomocí textových dat nebo zvukových dat.
* **Dekódování**: použití modelu a provádění rozpoznávání řeči (dekódování zvuků do textu).
* **Koncový bod**: uživatelsky specifické nasazení buď základního modelu, nebo vlastního modelu, který je přístupný *jenom* pro daného uživatele.

### <a name="expiration-timeline"></a>Časová osa vypršení platnosti

Nové modely a nové funkce budou k dispozici a starší, ale méně přesné modely jsou vyřazeny, a to v následujících časových osách pro model a vypršení platnosti koncového bodu:

**Základní modely** 

* Přizpůsobení: k dispozici po dobu jednoho roku. Po importu modelu je možné vytvořit vlastní modely v jednom roce. Po jednom roce se musí vytvořit nové vlastní modely z novější verze základního modelu.  
* Dekódování: k dispozici po dobu dvou let po importu. Takže můžete vytvořit koncový bod a pomocí tohoto modelu Batch přepisovat dva roky. 
* Koncové body: k dispozici na stejné časové ose jako dekódování.

**Vlastní modely**

* Dekódování: je k dispozici po dobu dvou let po vytvoření modelu. Takže můžete použít vlastní model po dobu dvou let (Batch/reálný/testování) po jeho vytvoření. Po dvou letech *byste si měli svůj model přeškolit* , protože základní model se obvykle už pro přizpůsobení nepoužívá.  
* Koncové body: k dispozici na stejné časové ose jako dekódování.

Když vyprší platnost základního modelu nebo vlastního modelu, vždycky se vrátí k *nejnovější verzi základního modelu*. Vaše implementace tak nebude nikdy přerušena, ale může být méně přesné pro *vaše konkrétní data* , pokud je dosaženo vypršení vlastních modelů. Vypršení platnosti modelu můžete zobrazit na následujících místech Custom Speech oblasti aplikace Speech Studio:

* Souhrn školení modelů
* Podrobnosti školení modelů
* Souhrn nasazení
* Podrobnosti o nasazení

Tady je příklad formuláře souhrn školení modelů:

![Souhrn školení modelů ](media/custom-speech/custom-speech-model-training-with-expiry.png) a také na stránce s podrobnostmi o školení modelů:

![Podrobnosti školení modelů](media/custom-speech/custom-speech-model-details-with-expiry.png)

Můžete také kontrolovat data vypršení platnosti přes [`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) rozhraní API vlastního rozpoznávání řeči pod `deprecationDates` vlastností v odpovědi JSON.

Tady je příklad údajů o vypršení platnosti z volání rozhraní API GetMode. **DEPRECATIONDATES** se zobrazí po vypršení platnosti modelu: 
```json
{
    "SELF": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}",
    "BASEMODEL": {
    "SELF": HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/BASE/{id}
    },
    "DATASETS": [
    {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/datasets/{id}
    }
    ],
    "LINKS": {
    "MANIFEST": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}/MANIFEST",
    "COPYTO": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}/copyto
    },
    "PROJECT": {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/projects/{id}
    },
    "PROPERTIES": {
    "DEPRECATIONDATES": {
        "ADAPTATIONDATETIME": "2022-01-15T00:00:00Z",     // last date the base model can be used for adaptation
        "TRANSCRIPTIONDATETIME": "2023-03-01T21:27:29Z"   // last date this model can be used for decoding
    }
    },
    "LASTACTIONDATETIME": "2021-03-01T21:27:40Z",
    "STATUS": "SUCCEEDED",
    "CREATEDDATETIME": "2021-03-01T21:27:29Z",
    "LOCALE": "EN-US",
    "DISPLAYNAME": "EXAMPLE MODEL",
    "DESCRIPTION": "",
    "CUSTOMPROPERTIES": {
    "PORTALAPIVERSION": "3"
    }
}
```
Počítejte s tím, že model můžete upgradovat na vlastní koncový bod řeči bez výpadků změnou modelu používaného koncovým bodem v části nasazení v nástroji Speech Studio nebo pomocí vlastního rozhraní API pro rozpoznávání řeči.

## <a name="what-happens-when-models-expire-and-how-to-update-them"></a>Co se stane, když vyprší platnost modelů a jak je aktualizovat
Co se stane, když vyprší platnost modelu a jak model aktualizovat, závisí na tom, jak se používá.
### <a name="batch-transcription"></a>Dávkový přepis
Pokud vyprší platnost modelu, který se používá u požadavků [Batch přepisu](batch-transcription.md) přepisu, dojde k chybě 4xx. K tomu, aby se tato aktualizace nedalo aktualizovat `model` parametr ve formátu JSON odeslaném v těle žádosti o **Vytvoření přepisu** , který odkazuje na novější nebo novější základní model. Můžete také odebrat `model` položku z formátu JSON a vždy použít nejnovější základní model.
### <a name="custom-speech-endpoint"></a>Vlastní koncový bod řeči
Pokud vyprší platnost modelu používaného [vlastním koncovým bodem řeči](how-to-custom-speech-train-model.md), služba se automaticky vrátí k používání nejnovějšího základního modelu pro jazyk, který používáte. používáte, můžete vybrat **nasazení** v nabídce **Custom Speech** v horní části stránky a potom kliknutím na název koncového bodu zobrazit jeho podrobnosti. V horní části stránky podrobností se zobrazí tlačítko **aktualizovat model** , které vám umožní plynule aktualizovat model používaný tímto koncovým bodem bez výpadku. Tuto změnu můžete provést také programově pomocí rozhraní REST API [**modelu aktualizace**](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/UpdateModel) .

## <a name="next-steps"></a>Další kroky

* [Trénování a nasazení modelu](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Další zdroje informací

* [Příprava a testování dat](./how-to-custom-speech-test-and-train.md)
* [Kontrola dat](how-to-custom-speech-inspect-data.md)