---
title: Vytvořit vlastní klíčová slova – služba Řeč
titleSuffix: Azure Cognitive Services
description: Vaše zařízení vždy naslouchá klíčovému slovu (nebo frázi). Když uživatel řekne klíčové slovo, zařízení odešle veškerý následný zvuk do cloudu, dokud uživatel nepřestane mluvit. Přizpůsobení klíčového slova je účinný způsob, jak odlišit vaše zařízení a posílit vaši značku.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: dapine
ms.openlocfilehash: 46e9f8e660c3fd62807d630481e6b3057d2351a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717023"
---
# <a name="create-a-custom-keyword-using-speech-studio"></a>Vytvoření vlastního klíčového slova pomocí aplikace Speech Studio

Vaše zařízení vždy naslouchá klíčovému slovu (nebo frázi). Například "Hey Cortana" je klíčové slovo pro asistenta Cortany. Když uživatel řekne klíčové slovo, zařízení odešle veškerý následný zvuk do cloudu, dokud uživatel nepřestane mluvit. Přizpůsobení klíčového slova je účinný způsob, jak odlišit vaše zařízení a posílit vaši značku.

V tomto článku se dozvíte, jak vytvořit vlastní klíčové slovo pro vaše zařízení.

## <a name="create-your-keyword"></a>Vytvoření klíčového slova

Před použitím vlastního klíčového slova budete muset vytvořit klíčové slovo pomocí stránky [Vlastní klíčové slovo](https://aka.ms/sdsdk-wakewordportal) v aplikaci Speech [Studio](https://aka.ms/sdsdk-speechportal). Po zadání klíčového slova vytvoří soubor, který nasadíte do zařízení.

1. Přejděte do [aplikace Speech Studio](https://aka.ms/sdsdk-speechportal) a **přihlaste se** nebo, pokud ještě nemáte předplatné řeči, zvolte Vytvořit [**předplatné**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Na stránce [Vlastní klíčové slovo](https://aka.ms/sdsdk-wakewordportal) vytvořte nový **projekt**. 

1. Zadejte **název**, volitelný **popis**a vyberte jazyk. Budete potřebovat jeden projekt na jazyk a podpora je v současné době omezena na jazyk en US.

    ![Popište projekt klíčových slov](media/custom-keyword/custom-kws-portal-new-project.png)

1. Vyberte projekt ze seznamu. 

    ![Výběr projektu klíčových slov](media/custom-keyword/custom-kws-portal-project-list.png)

1. Chcete-li spustit nový model klíčového slova, klepněte na tlačítko **Vlak model**.

1. Zadejte **název** modelu klíčového slova a volitelný **popis** a zadejte klíčové slovo podle svého **výběru** a klepněte na tlačítko **Další**. Máme několik [pokynů,](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) které vám pomohou vybrat efektivní klíčové slovo.

    ![Zadejte klíčové slovo.](media/custom-keyword/custom-kws-portal-new-model.png)

1. Portál nyní vytvoří výslovnosti kandidátů pro vaše klíčové slovo. Poslouchejte každého kandidáta kliknutím na tlačítka přehrávání a odstraňte šeky vedle všech nesprávných výslovností. Jakmile jsou zaškrtnuty pouze dobré výslovnosti, klepněte na **tlačítko Vlak** a začněte generovat klíčové slovo. 

    ![Kontrola klíčového slova](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. Může trvat až třicet minut pro model, který má být generován. Seznam klíčových slov se po dokončení modelu změní ze **zpracování** na **Úspěšné.** Poté můžete soubor stáhnout.

    ![Kontrola klíčového slova](media/custom-keyword/custom-kws-portal-download-model.png)

1. Uložte soubor ZIP do počítače. Tento soubor budete potřebovat k nasazení vlastního klíčového slova do zařízení.

## <a name="next-steps"></a>Další kroky

Otestujte vlastní klíčové slovo pomocí [nástroje Speech Devices SDK Quickstart](https://aka.ms/sdsdk-quickstart).
