---
title: Oblasti – služba řeči
titleSuffix: Azure Cognitive Services
description: Seznam dostupných oblastí a koncových bodů pro službu rozpoznávání řeči, včetně převodu řeči na text, převod textu na řeč a překladu řeči.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 409ce8b904997f2ab75f70b2138ec5b1e70a0e69
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816663"
---
# <a name="speech-service-supported-regions"></a>Oblasti podporované službou Speech

Služba Speech umožňuje vaší aplikaci převod zvuku na text, překlad řeči a převod textu na řeč. Služba je k dispozici v několika oblastech pomocí jedinečných koncových bodů pro rozhraní API REST a sadou SDK pro řeč.

Ujistěte se, že používáte koncového bodu, který se shoduje s oblastí pro vaše předplatné.

## <a name="speech-sdk"></a>Speech SDK

V [sadě Speech SDK](speech-sdk.md)jsou oblasti určeny jako řetězec (například jako parametr `SpeechConfig.FromSubscription` v sadě Speech SDK pro C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Převod řeči na text, převod textu na řeč a překlad

Sada Speech SDK je k dispozici v těchto oblastech pro **rozpoznávání řeči**, převod **textu na řeč**a **překlady**:

| Oblast           | Parametr SDK řeči | Přizpůsobení portálu řeči    |
| ---------------- | -------------------- | ------------------------------ |
| Západní USA          | `westus`             | https://westus.cris.ai         |
| Západní USA 2        | `westus2`            | https://westus2.cris.ai        |
| USA – východ          | `eastus`             | https://eastus.cris.ai         |
| Východ USA 2        | `eastus2`            | https://eastus2.cris.ai        |
| Střední USA       | `centralus`          | https://centralus.cris.ai      |
| Středoseverní USA | `northcentralus`     | https://northcentralus.cris.ai |
| Středojižní USA | `southcentralus`     | https://southcentralus.cris.ai |
| Střední Indie    | `centralindia`       | https://centralindia.cris.ai   |
| Východní Asie        | `eastasia`           | https://eastasia.cris.ai       |
| Jihovýchodní Asie   | `southeastasia`      | https://southeastasia.cris.ai  |
| Japonsko – východ       | `japaneast`          | https://japaneast.cris.ai      |
| Korea – střed    | `koreacentral`       | https://koreacentral.cris.ai   |
| Austrálie – východ   | `australiaeast`      | https://australiaeast.cris.ai  |
| Kanada – střed   | `canadacentral`      | https://canadacentral.cris.ai  |
| Severní Evropa     | `northeurope`        | https://northeurope.cris.ai    |
| Západní Evropa      | `westeurope`         | https://westeurope.cris.ai     |
| Velká Británie – jih         | `uksouth`            | https://uksouth.cris.ai        |
| Francie – střed   | `francecentral`      | https://francecentral.cris.ai  |

### <a name="intent-recognition"></a>Rozpoznávání záměru

Dostupné oblasti pro **rozpoznávání záměrů** prostřednictvím sady Speech SDK jsou následující:

| Globální oblast | Oblast           | Parametr SDK řeči |
| ------------- | ---------------- | -------------------- |
| Asie          | Východní Asie        | `eastasia`           |
| Asie          | Jihovýchodní Asie   | `southeastasia`      |
| Austrálie     | Austrálie – východ   | `australiaeast`      |
| Evropa        | Severní Evropa     | `northeurope`        |
| Evropa        | Západní Evropa      | `westeurope`         |
| Severní Amerika | USA – východ          | `eastus`             |
| Severní Amerika | Východ USA 2        | `eastus2`            |
| Severní Amerika | Středojižní USA | `southcentralus`     |
| Severní Amerika | Středozápadní USA  | `westcentralus`      |
| Severní Amerika | Západní USA          | `westus`             |
| Severní Amerika | Západní USA 2        | `westus2`            |
| Jižní Amerika | Brazílie – jih     | `brazilsouth`        |

Toto je podmnožina oblastí publikování, které podporuje [služba Language Understanding (Luis)](/azure/cognitive-services/luis/luis-reference-regions).

### <a name="voice-assistants"></a>Hlasoví asistenti

[Sada Speech SDK](speech-sdk.md) podporuje možnosti **hlasového asistenta** v těchto oblastech:

| Oblast         | Parametr SDK řeči |
| -------------- | -------------------- |
| Západní USA        | `westus`             |
| Západní USA 2      | `westus2`            |
| USA – východ        | `eastus`             |
| Východ USA 2      | `eastus2`            |
| Západní Evropa    | `westeurope`         |
| Severní Evropa   | `northeurope`        |
| Jihovýchodní Asie | `southeastasia`      |

## <a name="rest-apis"></a>Rozhraní REST API

Speech service také poskytuje koncové body REST pro požadavky na převod řeči na text a převod textu na řeč.

### <a name="speech-to-text"></a>Převod řeči na text

Referenční dokumentaci k textu pro převod řeči na text najdete v tématu [REST API řeči](rest-speech-to-text.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Převod textu na řeč

Referenční dokumentaci pro převod textu na řeč najdete v tématu [REST API převodu textu na řeč](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
