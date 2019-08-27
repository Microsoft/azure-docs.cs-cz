---
title: Podpora kontejnerů
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak vytvořit prostředek Azure Container instance.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 8/21/2019
ms.author: dapine
ms.openlocfilehash: 07e2067571c7bc7403ee8a1d1a0600c451e1581f
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051161"
---
## <a name="create-an-azure-container-instance-resource"></a>Vytvoření prostředku Azure Container instance

1. Pro Container Instances přejít na stránku [vytvořit](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) .

2. Na kartě **základy** zadejte následující podrobnosti:

    |Nastavení|Value|
    |--|--|
    |Subscription|Vyberte své předplatné.|
    |Resource group|Vyberte dostupnou skupinu prostředků nebo vytvořte novou, například `cognitive-services`.|
    |Název kontejneru|Zadejte název, například `cognitive-container-instance`. Název musí být malými písmeny.|
    |Location|Vyberte oblast pro nasazení.|
    |Typ image|Pokud je vaše image kontejneru uložená v registru kontejneru, který nevyžaduje přihlašovací údaje, vyberte `Public`. Pokud přístup k imagi kontejneru vyžaduje přihlašovací údaje, `Private`vyberte. Podrobnosti o tom, zda je `Public` nebo není bitová kopie kontejneru nebo `Private` ("Public Preview"), naleznete v úložištích kontejnerů [a obrázcích](../../cognitive-services-container-support.md#container-repositories-and-images) . |
    |Název image|Zadejte umístění kontejneru Cognitive Services. Umístění se používá jako argument `docker pull` příkazu. Podívejte se na [úložiště kontejnerů a obrázky](../../cognitive-services-container-support.md#container-repositories-and-images) pro dostupné názvy imagí a jejich odpovídající úložiště.<br><br>Název Image musí být plně kvalifikovaný, aby se zadaly tři části. Nejprve registr kontejneru, potom úložiště, nakonec název bitové kopie: `<container-registry>/<repository>/<image-name>`.<br><br>Tady je příklad, `mcr.microsoft.com/azure-cognitive-services/keyphrase` který představuje extrakce klíčových frází image v Microsoft Container Registry v úložišti Azure Cognitive Services. Dalším příkladem je `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` , který představuje řeč k textovému obrázku v úložišti Microsoft úložiště kontejneru kontejnerů ve verzi Preview kontejneru. |
    |Typ operačního systému|`Linux`|
    |Size|Změňte velikost na navrhovaná doporučení pro konkrétní kontejner služby pro rozpoznávání:<br>2 jádra procesoru<br>4 GB

3. Na kartě **sítě** zadejte následující podrobnosti:

    |Nastavení|Value|
    |--|--|
    |Porty|Nastavte port TCP na `5000`. Zpřístupňuje kontejner na portu 5000.|

4. Na kartě **Upřesnit** zadejte požadované **proměnné prostředí** pro nastavení fakturace kontejneru prostředku Azure Container instance:

    | Klíč | Value |
    |--|--|
    |`apikey`|Zkopírováno ze stránky **klíče** v prostředku. Je to 32 alfanumerický znak řetězce bez mezer nebo pomlček, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Zkopírováno ze stránky **Přehled** prostředku.|
    |`eula`|`accept`|

1. Klikněte na tlačítko **zkontrolovat a vytvořit**
1. Po úspěšném ověření kliknutím na **vytvořit** dokončete proces vytváření.
1. Po úspěšném nasazení prostředku je jeho připravenost