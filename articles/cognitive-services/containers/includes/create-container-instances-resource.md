---
title: Podpora kontejnerů
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak vytvořit prostředek instance kontejneru Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 09957461fd92114d1181a570550ee1a189edd8ea
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876403"
---
## <a name="create-an-azure-container-instance-resource"></a>Vytvoření prostředku instance kontejneru Azure

1. Přejděte na stránku [Vytvořit](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) pro instance kontejnerů.

2. Na kartě **Základy** zadejte následující podrobnosti:

    |Nastavení|Hodnota|
    |--|--|
    |Předplatné|Vyberte své předplatné.|
    |Skupina prostředků|Vyberte dostupnou skupinu prostředků nebo `cognitive-services`vytvořte novou, například .|
    |Název kontejneru|Zadejte název, `cognitive-container-instance`například . Název musí být v dolních písmenech.|
    |Umístění|Vyberte oblast pro nasazení.|
    |Typ image|Pokud je bitová kopie kontejneru uložená v registru `Public`kontejneru, který nevyžaduje pověření, zvolte . Pokud přístup k image kontejneru `Private`vyžaduje přihlašovací údaje, zvolte . Podrobnosti o tom, zda je obraz kontejneru `Public` nebo `Private` zda je ("Public Preview"), naleznete v [úložišti kontejnerů a na obrázcích.](../../cognitive-services-container-support.md#container-repositories-and-images) |
    |Název image|Zadejte umístění kontejneru služby Cognitive Services. Umístění je to, co se používá `docker pull` jako argument příkazu. Názvy dostupných obrázků a jejich odpovídající úložiště naleznete v [úložišti kontejnerů a na obrázcích.](../../cognitive-services-container-support.md#container-repositories-and-images)<br><br>Název obrázku musí být plně kvalifikovaný a specifikuje tři části. Nejprve registr kontejnerů, pak úložiště, nakonec `<container-registry>/<repository>/<image-name>`název obrázku: .<br><br>Tady je příklad, `mcr.microsoft.com/azure-cognitive-services/keyphrase` který by představoval bitovou kopii extrakce klíčových frází v registru kontejnerů Microsoft u úložiště Azure Cognitive Services. Dalším příkladem `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` je, který by představoval obraz Převod řeči na text v úložišti Microsoft registru kontejneru Náhled. |
    |Typ operačního příkazu|`Linux`|
    |Velikost|Změňte velikost na navrhovaná doporučení pro konkrétní kontejner služby Cognitive Service:<br>2 procesorová jádra<br>4 GB

3. Na kartě **Síť** zadejte následující podrobnosti:

    |Nastavení|Hodnota|
    |--|--|
    |Porty|Nastavte port TCP `5000`na . Zpřístupní kontejner na portu 5000.|

4. Na kartě **Upřesnit** zadejte požadované **proměnné prostředí** pro nastavení fakturace kontejneru prostředku instance kontejneru Azure:

    | Klíč | Hodnota |
    |--|--|
    |`apikey`|Zkopírováno ze stránky **Klíče** prostředku. Jedná se o řetězec 32 alfanumerických znaků `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`bez mezer nebo pomlček .|
    |`billing`|Zkopírováno ze stránky **Přehled** prostředku.|
    |`eula`|`accept`|

5. Klikněte na **Zkontrolovat a vytvořit.**
6. Po uplynutí platnosti ověření můžete dokončit proces vytváření kliknutím na **Vytvořit.**
7. Když je prostředek úspěšně nasazen, je připraven
