---
title: Vytvořit vlastní klíčová slova – služba Speech
titleSuffix: Azure Cognitive Services
description: Zařízení vždycky naslouchá klíčovému slovu (nebo frázi). Když uživatel uvede klíčové slovo, zařízení pošle veškeré následné zvukové nahrávky do cloudu, dokud uživatel nepřestane mluvit. Přizpůsobení klíčového slova je efektivní způsob, jak odlišit vaše zařízení a posílit vaše branding.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: dapine
ms.openlocfilehash: 46e9f8e660c3fd62807d630481e6b3057d2351a5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717023"
---
# <a name="create-a-custom-keyword-using-speech-studio"></a>Vytvoření vlastního klíčového slova pomocí řeči Studio

Zařízení vždycky naslouchá klíčovému slovu (nebo frázi). Například "Hey Cortana" je klíčové slovo pro pomocníka Cortana. Když uživatel uvede klíčové slovo, zařízení pošle veškeré následné zvukové nahrávky do cloudu, dokud uživatel nepřestane mluvit. Přizpůsobení klíčového slova je efektivní způsob, jak odlišit vaše zařízení a posílit vaše branding.

V tomto článku se dozvíte, jak vytvořit vlastní klíčové slovo pro vaše zařízení.

## <a name="create-your-keyword"></a>Vytvoření klíčového slova

Než budete moct použít vlastní klíčové slovo, budete muset vytvořit klíčové slovo pomocí stránky [vlastní klíčová slova](https://aka.ms/sdsdk-wakewordportal) v [studiu řeči](https://aka.ms/sdsdk-speechportal). Po zadání klíčového slova se vytvoří soubor, který nasadíte do svého zařízení.

1. Pokud ještě nemáte předplatné pro rozpoznávání řeči, vyberte [**Vytvořit předplatné**](https://go.microsoft.com/fwlink/?linkid=2086754)a přihlaste se ke službě [Speech Studio](https://aka.ms/sdsdk-speechportal) a **přihlaste** se.

1. Na stránce [vlastní klíčové slovo](https://aka.ms/sdsdk-wakewordportal) vytvořte **Nový projekt**. 

1. Zadejte **název**, volitelný **Popis**a vyberte jazyk. Budete potřebovat jeden projekt na jazyk a podpora je aktuálně omezená na jazyk EN-US.

    ![Popis projektu s klíčovým slovem](media/custom-keyword/custom-kws-portal-new-project.png)

1. Vyberte svůj projekt ze seznamu. 

    ![Vyberte projekt vašeho klíčového slova.](media/custom-keyword/custom-kws-portal-project-list.png)

1. Chcete-li spustit nový model klíčového slova, klikněte na možnost **model výuky**.

1. Zadejte **název** modelu klíčového slova a volitelný **Popis** , zadejte **klíčové slovo** podle vlastního výběru a klikněte na **Další**. Máme nějaké [pokyny](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) , které vám pomůžou zvolit efektivní klíčové slovo.

    ![Zadejte své klíčové slovo](media/custom-keyword/custom-kws-portal-new-model.png)

1. Portál teď pro klíčové slovo vytvoří kandidáty na kandidáty. Naslouchat každému kandidátovi kliknutím na tlačítko Přehrát a odebrat kontroly u všech nesprávných výslovností. Jakmile budou kontrolovány pouze dobré výslovnosti, klikněte na možnost **výuka** a začněte vygenerovat klíčové slovo. 

    ![Kontrola klíčového slova](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. Generování modelu může trvat až 30 minut. Po dokončení modelu se seznam klíčových slov změní ze **zpracování** na **dokončeno** . Pak můžete soubor stáhnout.

    ![Kontrola klíčového slova](media/custom-keyword/custom-kws-portal-download-model.png)

1. Uložte soubor .zip na vašem počítači. Tento soubor budete potřebovat k nasazení vašeho vlastního klíčového slova do zařízení.

## <a name="next-steps"></a>Další kroky

Otestujte vlastní klíčové slovo pomocí [sady Speech Devices SDK rychlý Start](https://aka.ms/sdsdk-quickstart).
