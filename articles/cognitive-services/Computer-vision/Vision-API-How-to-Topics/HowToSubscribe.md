---
title: Získání klíčů předplatného pro rozhraní API pro počítačové zpracování obrazu
titlesuffix: Azure Cognitive Services
description: Další informace o získání klíčů předplatného pro volání rozhraní API pro počítačové zpracování obrazu ve službě Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 05/19/2017
ms.author: kefre
ms.openlocfilehash: db4d589bb0c7611e632a90f2174ad8e9c415bf6a
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985418"
---
# <a name="how-to-obtain-subscription-keys"></a>Získání klíčů předplatného

Počítačové zpracování obrazu služby vyžadují speciální předplatné klíče. Všechna volání rozhraní API pro počítačové zpracování obrazu vyžadují klíč předplatného. Tento klíč se musí buď předat prostřednictvím parametru řetězce dotazu, nebo zadat v hlavičce požadavku.

K registraci pro klíče předplatného najdete v článku [předplatná](https://azure.microsoft.com/try/cognitive-services/). Je to zdarma zaregistrovat. Ceny za tyto služby se může změnit.

>[!NOTE]
Klíče předplatného jsou platné pouze pro jeden z těchto [oblasti Microsoft Azure](https://azure.microsoft.com/regions/). 

| Oblast | Adresa |
|---|---|
| USA – západ | westus.API.cognitive.microsoft.com |
| Východní USA 2 | eastus2.API.cognitive.microsoft.com |
| Západní střed USA | westcentralus.API.cognitive.microsoft.com |
| Západní Evropa | westeurope.API.cognitive.microsoft.com |
| Jihovýchodní Asie | southeastasia.API.cognitive.microsoft.com |

Pokud se zaregistrujete pomocí bezplatné zkušební verze pro počítačové zpracování obrazu, klíče předplatného jsou platné pro **westcentral** oblasti (`https://westcentralus.api.cognitive.microsoft.com/`). Je to nejběžnější případ. Ale Pokud zaregistrujete pro počítačové zpracování obrazu pomocí Microsoft Azure přes účet [ https://azure.microsoft.com/ ](https://azure.microsoft.com/) webu, určit oblast pro zkušební verze v předchozím seznamu oblastí.

Pokud zaregistrujete například pro počítačové zpracování obrazu pomocí účtu Microsoft Azure a můžete určit `westus` pro vaši oblast, je nutné použít `westus` oblast pro volání rozhraní REST API (`https://westus.api.cognitive.microsoft.com/`).

Pokud zapomenete oblast pro klíč předplatného. po obdržení vašeho klíče pro zkušební verzi, můžete najít ve vaší oblasti [ https://azure.microsoft.com/try/cognitive-services/my-apis/ ](https://azure.microsoft.com/try/cognitive-services/my-apis/).

### <a name="related-links"></a>Související odkazy:

* [Cenové možnosti pro rozhraní API služeb Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)
