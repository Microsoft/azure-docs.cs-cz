---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 942bcc6b150f990f9a9acab0d4ef68bfb6125c1b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71996864"
---
>[!NOTE]
> Koncové body pro prostředky vytvořené po 1. Další informace a úplný seznam místních koncových bodů naleznete [v tématu Vlastní názvy subdomén pro služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Azure Cognitive Services jsou reprezentované prostředky Azure, které si předplatíte. Vytvořte prostředek pro rozpoznávání rukopisu pomocí [portálu Azure](../../cognitive-services-apis-create-account.md). 

* Můžete také získat [zkušební klíč](https://azure.microsoft.com/try/cognitive-services/#decision) platný po dobu sedmi dnů zdarma. Po registraci bude ona a koncový bod k dispozici na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).

Po vytvoření prostředku získáte koncový bod a klíč otevřením prostředku na [webu Azure Portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade)a kliknutím na tlačítko Rychlý **start**.

Vytvořte dvě [proměnné prostředí](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY`- Koncový bod pro váš prostředek. Bude to vypadat takto: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

* `INK_RECOGNITION_ENDPOINT`- Klíč předplatného pro ověření vašich požadavků.   
