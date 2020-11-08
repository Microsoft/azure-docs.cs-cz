---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: c68e5b7ab24e2d7e7f30ddc356ae3c4382137507
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369069"
---
>[!NOTE]
> Koncové body pro prostředky vytvořené po 1. červenci 2019 používají vlastní formát subdomény, který vidíte níže. Další informace a úplný seznam regionálních koncových bodů najdete v tématu [názvy vlastních subdomén pro Cognitive Services](../../cognitive-services-custom-subdomains.md). 

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro rozpoznávání rukopisu pomocí [Azure Portal](../../cognitive-services-apis-create-account.md).

Po vytvoření prostředku Získejte svůj koncový bod a klíč tak, že na [Azure Portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade)otevřete prostředek a kliknete na **Rychlé spuštění**.

Vytvořte dvě [proměnné prostředí](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY` – Klíč předplatného pro ověření vašich požadavků. 

* `INK_RECOGNITION_ENDPOINT` – Koncový bod pro váš prostředek. Bude vypadat takto: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`