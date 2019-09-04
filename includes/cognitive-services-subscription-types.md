---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 3b808f4eb853cf05eb08cd1acf08dedccabf71a6
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274682"
---
## <a name="azure-cognitive-service-resource-types"></a>Typy prostředků služby pro rozpoznávání Azure

<!-- > [!NOTE]
> Subscription owners can disable the creation of Cognitive Services resources for resource groups and subscriptions by applying [Azure policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), assigning a “Not allowed resource types” policy definition, and specifying **Microsoft.CognitiveServices/accounts** as the target resource type. -->
K Azure Cognitive Services můžete přistupovat prostřednictvím dvou různých prostředků: Prostředek s více službami nebo jeden pro jednu službu. Tato předplatná vám umožní připojit se k jedné službě rozpoznávání nebo k více službám v jednom.

### <a name="multi-service-resource"></a>Prostředek s více službami

Přihlášení k odběru prostředku Cognitive Services více službami:
* Umožňuje použít jeden prostředek Azure pro většinu Cognitive Services Azure.
* Získáte jeden klíč, který je možné použít s více Cognitive Services Azure.
* Konsoliduje fakturaci ze služeb, které používáte. Další informace najdete v tématu [Cognitive Services ceny](https://azure.microsoft.com/pricing/details/cognitive-services/) .

>[!WARNING]
> V tuto chvíli tyto služby nepodporují klíče s **více službami:** QnA Maker, služba Speech Services, Custom Vision a detektor anomálií.

### <a name="single-service-resource"></a>Prostředek s jednou službou

Přihlášení k odběru prostředku Cognitive Services s jednou službou:
* Umožňuje použít zadanou službu pro rozpoznávání (například Počítačové zpracování obrazu nebo Speech Services) pro vytvoření prostředku.
* Získáte klíč, který je specifický pro službu rozpoznávání, pro kterou vytvoříte prostředek.