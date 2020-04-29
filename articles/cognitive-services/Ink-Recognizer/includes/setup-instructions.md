---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 942bcc6b150f990f9a9acab0d4ef68bfb6125c1b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "71996864"
---
>[!NOTE]
> Koncové body pro prostředky vytvořené po 1. červenci 2019 používají vlastní formát subdomény, který vidíte níže. Další informace a úplný seznam regionálních koncových bodů najdete v tématu [názvy vlastních subdomén pro Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro rozpoznávání rukopisu pomocí [Azure Portal](../../cognitive-services-apis-create-account.md). 

* Můžete také získat [zkušební klíč](https://azure.microsoft.com/try/cognitive-services/#decision) platný po dobu sedmi dnů zdarma. Po registraci bude na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/)k dispozici koncový bod.

Po vytvoření prostředku Získejte svůj koncový bod a klíč tak, že na [Azure Portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade)otevřete prostředek a kliknete na **Rychlé spuštění**.

Vytvořte dvě [proměnné prostředí](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY`– Koncový bod pro váš prostředek. Bude vypadat takto: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

* `INK_RECOGNITION_ENDPOINT`– Klíč předplatného pro ověření vašich požadavků.   
