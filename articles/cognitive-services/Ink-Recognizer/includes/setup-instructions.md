---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: ede1fb4bd2a9a6e6536959053e3ca4d8e4a82f87
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91327343"
---
>[!NOTE]
> Koncové body pro prostředky vytvořené po 1. červenci 2019 používají vlastní formát subdomény, který vidíte níže. Další informace a úplný seznam regionálních koncových bodů najdete v tématu [názvy vlastních subdomén pro Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro rozpoznávání rukopisu pomocí [Azure Portal](../../cognitive-services-apis-create-account.md).

Po vytvoření prostředku Získejte svůj koncový bod a klíč tak, že na [Azure Portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade)otevřete prostředek a kliknete na **Rychlé spuštění**.

Vytvořte dvě [proměnné prostředí](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY` – Klíč předplatného pro ověření vašich požadavků. 

* `INK_RECOGNITION_ENDPOINT` – Koncový bod pro váš prostředek. Bude vypadat takto: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`   
