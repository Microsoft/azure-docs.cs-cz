---
title: Získání klíčů předplatného - pro počítačové zpracování obrazu
titlesuffix: Azure Cognitive Services
description: Další informace o získání klíčů předplatného pro volání rozhraní API pro počítačové zpracování obrazu ve službě Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 05/19/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: c4c7ee1ab756f458c6e9fed4ff5ee54dfaba919e
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55222231"
---
# <a name="how-to-obtain-subscription-keys"></a>Získání klíčů předplatného

Počítačové zpracování obrazu služby vyžadují speciální předplatné klíče. Ke každému volání rozhraní API pro počítačové zpracování obrazu potřebujete klíč předplatného. Tento klíč je potřeba předat buď jako parametr řetězce dotazu, nebo ho zadat v hlavičce požadavku.

K registraci pro klíče předplatného najdete v článku [předplatná](https://azure.microsoft.com/try/cognitive-services/). Je to zdarma zaregistrovat. Ceny za tyto služby se může změnit.

>[!NOTE]
Klíče předplatného jsou platné pouze pro jeden z těchto [oblasti Microsoft Azure](https://azure.microsoft.com/regions/). 

| Oblast | Adresa |
|---|---|
| Západní USA | westus.API.cognitive.microsoft.com |
| Východní USA 2 | eastus2.api.cognitive.microsoft.com |
| Západní střed USA | westcentralus.api.cognitive.microsoft.com |
| Západní Evropa | westeurope.api.cognitive.microsoft.com |
| Jihovýchodní Asie | southeastasia.api.cognitive.microsoft.com |

Pokud se zaregistrujete pomocí bezplatné zkušební verze pro počítačové zpracování obrazu, klíče předplatného jsou platné pro **westcentral** oblasti (`https://westcentralus.api.cognitive.microsoft.com/`). Je to nejběžnější případ. Ale Pokud zaregistrujete pro počítačové zpracování obrazu pomocí Microsoft Azure přes účet [ https://azure.microsoft.com/ ](https://azure.microsoft.com/) webu, určit oblast pro zkušební verze v předchozím seznamu oblastí.

Pokud zaregistrujete například pro počítačové zpracování obrazu pomocí účtu Microsoft Azure a můžete určit `westus` pro vaši oblast, je nutné použít `westus` oblast pro volání rozhraní REST API (`https://westus.api.cognitive.microsoft.com/`).

Pokud zapomenete oblast pro klíč předplatného. po obdržení vašeho klíče pro zkušební verzi, můžete najít ve vaší oblasti [ https://azure.microsoft.com/try/cognitive-services/my-apis/ ](https://azure.microsoft.com/try/cognitive-services/my-apis/).

### <a name="related-links"></a>Související odkazy:

* [Cenové možnosti pro rozhraní API služeb Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)
