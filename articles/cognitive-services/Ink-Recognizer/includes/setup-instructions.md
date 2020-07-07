---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: c202ba1d7363af9791daa801f0c447c49a80859b
ms.sourcegitcommit: bf8c447dada2b4c8af017ba7ca8bfd80f943d508
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2020
ms.locfileid: "85378443"
---
>[!NOTE]
> Koncové body pro prostředky vytvořené po 1. červenci 2019 používají vlastní formát subdomény, který vidíte níže. Další informace a úplný seznam regionálních koncových bodů najdete v tématu [názvy vlastních subdomén pro Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro rozpoznávání rukopisu pomocí [Azure Portal](../../cognitive-services-apis-create-account.md).

Po vytvoření prostředku Získejte svůj koncový bod a klíč tak, že na [Azure Portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade)otevřete prostředek a kliknete na **Rychlé spuštění**.

Vytvořte dvě [proměnné prostředí](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY`– Koncový bod pro váš prostředek. Bude vypadat takto: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

* `INK_RECOGNITION_ENDPOINT`– Klíč předplatného pro ověření vašich požadavků.   
