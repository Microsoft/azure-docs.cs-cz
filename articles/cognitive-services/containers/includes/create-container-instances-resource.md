---
title: Podpora kontejnerů
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak vytvořit prostředek Azure container instance.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: 05284d434e6bd22fd50957f7cc5ec966f88a4fd4
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229308"
---
## <a name="create-an-azure-container-instance-resource"></a>Vytvoří prostředek služby Azure Container Instance

1. Přejděte [vytvořit](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) stránky pro Container Instances.

2. Na **Základy** kartu, zadejte následující údaje:

    |Nastavení|Value|
    |--|--|
    |Subscription|Vyberte své předplatné.|
    |Resource group|Vyberte skupinu prostředků nebo vytvořte novou, jako `cognitive-services`.|
    |Název kontejneru|Zadejte název, například `cognitive-container-instance`. Název musí být velkými písmeny nižší.|
    |Location|Vyberte oblast pro nasazení.|
    |Typ obrázku|`Public`|
    |Název image|Zadejte umístění kontejneru služeb Cognitive Services. Umístění může být používán stejný `docker pull` příkazu, přečtěte si [kontejneru úložišť a imagí](../../cognitive-services-container-support.md#container-repositories-and-images) pro názvy dostupných imagí a jejich odpovídající úložiště.|
    |Typ operačního systému|`Linux`|
    |Size|Změnit velikost tak, aby navrhované doporučení pro vaše konkrétní kontejner služby Cognitive Services:<br>2 jádra procesoru<br>4 GB

3. Na **sítě** kartu, zadejte následující údaje:

    |Nastavení|Value|
    |--|--|
    |Porty|Nastavte TCP port na `5000`. Poskytuje kontejner na portu 5000.|

4. Na **Upřesnit** kartu, zadejte požadované **proměnné prostředí** kontejneru fakturace nastavení ACI prostředku:

    | Klíč | Hodnota |
    |--|--|
    |`apikey`|Zkopírovány z **klíče** stránky prostředku. Jedná se o 32 alfanumerický znak řetězec bez mezery, pomlčky, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Zkopírovány z **přehled** stránky prostředku.|
    |`eula`|`accept`|

1. Klikněte na tlačítko **zkontrolovat a vytvořit**
1. Jakmile úspěšně proběhne ověření, klikněte na tlačítko **vytvořit** na dokončení procesu vytváření
1. Po úspěšném nasazení prostředku je připravený