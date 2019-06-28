---
title: Podpora kontejnerů
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak vytvořit prostředek azure container instance (ACI).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: b24a78873c3220b969fc548d2553e465e80773cd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455073"
---
## <a name="create-an-azure-container-instance-aci-resource"></a>Vytvoří prostředek služby Azure Container Instance (ACI)

1. Přejděte [vytvořit](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) stránky pro Container Instances.

2. Na **Základy** kartu, zadejte následující údaje:

    |Nastavení|Hodnota|
    |--|--|
    |Předplatné|Vyberte své předplatné.|
    |Skupina prostředků|Vyberte skupinu prostředků nebo vytvořte novou, jako `cognitive-services`.|
    |Název kontejneru|Zadejte název, například `cognitive-container-instance`. Tento název musí být velkými písmeny nižší.|
    |Location|Vyberte oblast pro nasazení.|
    |Typ obrázku|`Public`|
    |Název Image|Zadejte umístění kontejneru Cognitive Services. To může být na stejném místě jste použili v `docker pull` příkazu _například_: <br>`mcr.microsoft.com/azure-cognitive-services/sentiment`|
    |Typ operačního systému|`Linux`|
    |Size|Změnit velikost na navrhované doporučení pro vaše konkrétní kontejner služby Cognitive Services.:<br>2 jádra<br>4 GB

3. Na **sítě** kartu, zadejte následující údaje:

    |Nastavení|Hodnota|
    |--|--|
    |Porty|Nastavte TCP port na `5000`. Poskytuje kontejner na portu 5000.|

4. Na **Upřesnit** kartu, zadejte následující podrobnosti o předávání kontejneru [vyžaduje nastavení fakturace](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers#billing-arguments) ACI prostředku:

    |Stránka Upřesnit klíč|Hodnota pokročilé stránky|
    |--|--|
    |`apikey`|Zkopírovány z **klíče** stránce prostředků pro analýzu textu. Jedná se o 32 alfanumerický znak řetězec bez mezery, pomlčky, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Zkopírovány z **přehled** stránce prostředků pro analýzu textu. Příklad: `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |`eula`|`accept`|

1. Klikněte na tlačítko **zkontrolovat a vytvořit**
1. Jakmile úspěšně proběhne ověření, klikněte na tlačítko **vytvořit** na dokončení procesu vytváření
1. Po úspěšném nasazení prostředku je připravená k použití