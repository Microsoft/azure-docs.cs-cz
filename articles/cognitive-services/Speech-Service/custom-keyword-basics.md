---
title: Vytvoření klíčového slova pro rychlý Start – služba Speech
titleSuffix: Azure Cognitive Services
description: Zařízení vždycky naslouchá klíčovému slovu (nebo frázi). Když uživatel uvede klíčové slovo, zařízení pošle veškeré následné zvukové nahrávky do cloudu, dokud uživatel nepřestane mluvit. Přizpůsobení klíčového slova je efektivní způsob, jak odlišit vaše zařízení a posílit vaše branding.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: trbye
ms.custom: devx-track-csharp
zone_pivot_groups: keyword-quickstart
ms.openlocfilehash: 49ac70b6881085f48c8bc3a12e31e4a1aa220c6a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021945"
---
# <a name="get-started-with-custom-keyword"></a>Začínáme se službou Vlastní klíčové slovo

V tomto rychlém startu se naučíte základy práce s vlastními klíčovými slovy pomocí rozpoznávání řeči Studio a sady Speech SDK. Klíčové slovo je slovo nebo krátká fráze, která umožňuje, aby byl váš produkt hlasově aktivovaný. Modely klíčových slov vytvoříte v nástroji Speech Studio a potom exportujete soubor modelu, který používáte se sadou Speech SDK ve vašich aplikacích.

## <a name="prerequisites"></a>Požadavky

Postup v tomto článku vyžaduje předplatné řeči a sadu Speech SDK. Pokud ještě nemáte předplatné, [Vyzkoušejte službu Speech Service zdarma](overview.md#try-the-speech-service-for-free). Chcete-li získat sadu SDK, přečtěte si [příručku Instalace](quickstarts/setup-platform.md) pro vaši platformu.

## <a name="create-a-keyword-in-speech-studio"></a>Vytvoření klíčového slova v studiu řeči

Než budete moct použít vlastní klíčové slovo, musíte vytvořit klíčové slovo pomocí stránky [vlastní klíčová slova](https://aka.ms/sdsdk-wakewordportal) v [studiu řeči](https://aka.ms/sdsdk-speechportal). Po zadání klíčového slova se vytvoří `.table` soubor, který můžete použít se sadou Speech SDK.

> [!IMPORTANT]
> Vlastní modely klíčových slov a výsledné `.table` soubory lze vytvořit **pouze** v studiu řeči.
> Nemůžete vytvořit vlastní klíčová slova ze sady SDK nebo pomocí volání REST.

1. Pokud ještě nemáte předplatné pro rozpoznávání řeči, vyberte [**Vytvořit předplatné**](https://go.microsoft.com/fwlink/?linkid=2086754)a přihlaste se ke službě [Speech Studio](https://aka.ms/sdsdk-speechportal) a **přihlaste** se.

1. Na stránce [vlastní klíčové slovo](https://aka.ms/sdsdk-wakewordportal) vytvořte **Nový projekt**. 

1. Zadejte **název**, volitelný **Popis** a vyberte jazyk. Potřebujete jeden projekt na jazyk a podpora je aktuálně omezená na `en-US` jazyk.

    ![Popis projektu s klíčovým slovem](media/custom-keyword/custom-kws-portal-new-project.png)

1. Vyberte svůj projekt ze seznamu. 

    ![Vyberte projekt vašeho klíčového slova.](media/custom-keyword/custom-kws-portal-project-list.png)

1. Pokud chcete vytvořit nový model klíčových slov, klikněte na **výuka model**.

1. Zadejte **název** modelu, volitelný **Popis** a **klíčové slovo** podle vlastního výběru a pak klikněte na **Další**. Přečtěte si [pokyny](./custom-keyword-overview.md#choose-an-effective-keyword) pro výběr efektivního klíčového slova.

    ![Zadejte své klíčové slovo](media/custom-keyword/custom-kws-portal-new-model.png)

1. Portál vytvoří kandidáty na kandidáty pro klíčové slovo. Naslouchat každému kandidátovi kliknutím na tlačítko Přehrát a odebrat kontroly u všech nesprávných výslovností. Až budou kontrolovány jenom dobré výslovnosti, klikněte na **výuka** a začněte vygenerovat model klíčových slov. 

    ![Snímek obrazovky, který ukazuje, kde si zvolíte správnou pronounciations.](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. Generování modelu může trvat až 30 minut. Po dokončení modelu se seznam klíčových slov změní ze **zpracování** na **dokončeno** . Pak můžete soubor stáhnout.

    ![Kontrola klíčového slova](media/custom-keyword/custom-kws-portal-download-model.png)

1. Stažený soubor je `.zip` archiv. Extrahujte archiv a zobrazíte soubor s `.table` příponou. Jedná se o soubor, který v sadě SDK použijete v další části, a nezapomeňte si uvědomit jeho cestu. název souboru odráží název vašeho klíčového slova, například klíčové slovo **aktivovat zařízení** má název souboru `Activate_device.table` .

## <a name="use-a-keyword-model-with-the-sdk"></a>Použití modelu klíčového slova se sadou SDK

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/keyword-recognition/keyword-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/keyword-recognition/keyword-basics-python.md)]
::: zone-end

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [ObjectiveC/Swift Basics include](includes/how-to/keyword-recognition/keyword-basics-objc.md)]
::: zone-end

## <a name="next-steps"></a>Další kroky

Otestujte svoje vlastní klíčové slovo pomocí [sady Speech Devices SDK pro rychlý Start](./speech-devices-sdk-quickstart.md?pivots=platform-android).