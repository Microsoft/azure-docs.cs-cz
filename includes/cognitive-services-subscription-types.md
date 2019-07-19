---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 674dd30ff3e493ec4c4036f032f82624a6ca5749
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334259"
---
## <a name="azure-cognitive-service-resource-types"></a>Typy prostředků služby pro rozpoznávání Azure

> [!NOTE]
> Vlastníci předplatného můžou zakázat vytváření prostředků Cognitive Services pro skupiny prostředků a předplatná pomocí [zásad Azure](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), přiřazení nepovolených typů prostředků a zadáním **. Microsoft. Cognitiveservices Account/Accounts** jako cílový typ prostředku.

K Azure Cognitive Services můžete přistupovat prostřednictvím dvou různých prostředků: Prostředek s více službami nebo jeden pro jednu službu. Tato předplatná vám umožní připojit se k jedné službě nebo k několika službám najednou.

### <a name="multi-service-resource"></a>Prostředek s více službami

>[!WARNING]
> V tuto chvíli **tyto služby** nepodporují klíče s více službami: QnA Maker, služba Speech Services, Custom Vision a detektor anomálií.

Přihlášení k odběru prostředku Cognitive Services více službami:
* Umožňuje použít jeden prostředek Azure pro většinu Cognitive Services Azure.
* Konsoliduje fakturaci ze služeb, které používáte. Další informace najdete v tématu [Cognitive Services ceny](https://azure.microsoft.com/pricing/details/cognitive-services/) .

### <a name="single-service-resource"></a>Prostředek s jednou službou

Prostředky s jednou službou (například Počítačové zpracování obrazu nebo hlasové služby) jsou omezené na jejich konkrétní služby.