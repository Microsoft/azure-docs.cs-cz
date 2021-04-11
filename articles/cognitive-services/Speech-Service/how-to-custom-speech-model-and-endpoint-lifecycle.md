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
ms.date: 03/10/2021
ms.author: heikora
ms.openlocfilehash: b8e02071eca139cde02a8bad1b0e0e443db6ab86
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103555251"
---
# <a name="model-and-endpoint-lifecycle"></a>Životní cyklus modelu a koncového bodu

Custom Speech používá *základní modely* i *vlastní modely*. Každý jazyk má jeden nebo více základních modelů. Obecně platí, že když se do služby normálního rozpoznávání řeči uvolní nový model řeči, naimportuje se také do služby Custom Speech jako nový základní model. Aktualizují se každých 6 až 12 měsíců. Starší modely obvykle jsou méně užitečné v průběhu času, protože nejnovější model obvykle má vyšší přesnost.

Naproti tomu vlastní modely se vytvářejí pomocí přizpůsobení zvoleného základního modelu s daty z vašeho konkrétního scénáře zákazníka. Můžete používat konkrétní vlastní model po dlouhou dobu, jakmile budete mít ten, který vyhovuje vašim potřebám. Doporučujeme, abyste pravidelně aktualizovali na nejnovější základní model a v průběhu času znovu provedli další data. 

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

Tady je příklad údajů o vypršení platnosti z volání rozhraní API GetMode. "DEPRECATIONDATES" zobrazuje: 
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
        "ADAPTATIONDATETIME": "2022-01-15T00:00:00Z",     // last date this model can be used for adaptation
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

## <a name="next-steps"></a>Další kroky

* [Trénování a nasazení modelu](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Další zdroje informací

* [Příprava a testování dat](./how-to-custom-speech-test-and-train.md)
* [Kontrola dat](how-to-custom-speech-inspect-data.md)