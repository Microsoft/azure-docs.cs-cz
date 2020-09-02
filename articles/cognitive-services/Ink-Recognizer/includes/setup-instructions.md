---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 9d46b304d598b4830cf325909f77eea6b68af757
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89304126"
---
>[!NOTE]
> Koncové body pro prostředky vytvořené po 1. červenci 2019 používají vlastní formát subdomény, který vidíte níže. Další informace a úplný seznam regionálních koncových bodů najdete v tématu [názvy vlastních subdomén pro Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro rozpoznávání rukopisu pomocí [Azure Portal](../../cognitive-services-apis-create-account.md).

Po vytvoření prostředku Získejte svůj koncový bod a klíč tak, že na [Azure Portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade)otevřete prostředek a kliknete na **Rychlé spuštění**.

Vytvořte dvě [proměnné prostředí](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY` – Klíč předplatného pro ověření vašich požadavků. 

* `INK_RECOGNITION_ENDPOINT` – Koncový bod pro váš prostředek. Bude vypadat takto: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`   
