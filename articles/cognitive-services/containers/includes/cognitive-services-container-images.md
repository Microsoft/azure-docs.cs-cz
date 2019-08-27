---
ms.openlocfilehash: fe9879f9574fe1496ebdf20ac76fe5b759d4ea6a
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051230"
---
### <a name="container-repositories-and-images"></a>Úložiště kontejnerů a obrázky

Níže uvedené tabulky jsou vyčerpávajícím seznamem dostupných imagí kontejneru nabízených službou Azure Cognitive Services.

#### <a name="public-container-registry-mcrmicrosoftcom"></a>Veřejné (kontejnerový registr `mcr.microsoft.com`:)

Microsoft Container Registry hostuje všechny kontejnery všeobecné dostupnosti (GA) pro Cognitive Services.

| Služba | Kontejner | Název Container Registry/úložiště/image |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Analýza textu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Extrakce klíčových frází | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Analýza textu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Rozpoznávání jazyka | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Analýza textu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Analýza mínění | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-preview-container-registry-containerpreviewazurecrio"></a>Public Preview (registr kontejneru: `containerpreview.azurecr.io`)

Registry Preview kontejneru hostují všechny kontejnery "Public Preview" pro Cognitive Services, které ještě neprobíhaly při obecné dostupnosti (GA). Tyto kontejnery vyžadují formální žádost o přístup, aby je bylo možné spotřebovat.

| Služba | Kontejner | Název Container Registry/úložiště/image |
|--|--|--|
| [Detektor anomálií](../../anomaly-detector/anomaly-detector-container-howto.md) | Detektor anomálií | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Počítačové zpracování obrazu](../../Computer-vision/computer-vision-how-to-install-containers.md) | Rozpoznávání textu | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text` |
| [Rozpoznávání tváře](../../face/face-how-to-install-containers.md) | Tvář | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Nástroj pro rozpoznávání formulářů](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Rozpoznávání formulářů | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Rozhraní API služby Speech](../../speech-service/speech-container-howto.md) | Převod řeči na text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Rozhraní API služby Speech](../../speech-service/speech-container-howto.md) | Převod textu na řeč | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
