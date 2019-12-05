---
title: Vytvoření vlastního klíčového slova – služba Speech
titleSuffix: Azure Cognitive Services
description: Zařízení vždycky naslouchá klíčovému slovu (nebo frázi). Když uživatel uvede klíčové slovo, zařízení pošle veškeré následné zvukové nahrávky do cloudu, dokud uživatel nepřestane mluvit. Přizpůsobení klíčového slova je efektivní způsob, jak odlišit vaše zařízení a posílit vaše branding.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: erhopf
ms.openlocfilehash: 15a0e27f3f96eda27182e8437dc95d047f56e260
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815288"
---
# <a name="create-a-custom-keyword-by-using-the-speech-service"></a>Vytvoření vlastního klíčového slova pomocí služby Speech

Zařízení vždycky naslouchá klíčovému slovu (nebo frázi). Například "Hey Cortana" je klíčové slovo pro pomocníka Cortana. Když uživatel uvede klíčové slovo, zařízení pošle veškeré následné zvukové nahrávky do cloudu, dokud uživatel nepřestane mluvit. Přizpůsobení klíčového slova je efektivní způsob, jak odlišit vaše zařízení a posílit vaše branding.

V tomto článku se dozvíte, jak vytvořit vlastní klíčové slovo pro vaše zařízení.

## <a name="choose-an-effective-keyword"></a>Zvolit efektivní klíčové slovo

Při volbě klíčového slova Vezměte v úvahu následující pokyny:

* Klíčové slovo by mělo být anglické slovo nebo fráze. By měla trvat delší než 2 sekundy říct.

* Slova 4 až 7 slabik fungují nejlépe. Například "Hey, Computer" je klíčové slovo dobrý. Právě "Hey" je nízký.

* Klíčová slova by měla dodržovat běžná pravidla pro výslovnost angličtiny.

* Jedinečný nebo dokonce zhotovené slovo, které následuje běžných pravidel anglické výslovnost může snížit počet falešně pozitivních výsledků. Například "computerama" může být dobrým klíčovým slovem.

* Nevybírejte běžné slovo. Například "jíst" a "přejděte" jsou slova, které lidé často říkají v běžném konverzace. Mohou být false aktivační události pro vaše zařízení.

* Vyhněte se použití klíčového slova, které může mít alternativní výslovnost. Uživatelé museli vědět, výslovnost "vpravo" zařízení reagovat. Například "509" se mohou projevit "pět nula devíti," "pět ale devět," nebo "pět set a devět." "R.E.I." se mohou projevit "r-e – i" nebo "ray." "Live" se mohou projevit "/līv/" nebo "/liv/".

* Nepoužívejte speciální znaky, symboly nebo číslic. Například "jít #" a "20 + kočky" by nepředstavovala správná klíčová slova. Ale "Přejít sharp" nebo "dvacet plus kočky" mohou fungovat. Stále můžete použít symboly v brandingu a můžete posílit správné výslovnost uvádění na trh a dokumentace.

> [!NOTE]
> Pokud zvolíte klíčové slovo jako klíčové slovo, ujistěte se, že vlastníte tuto ochrannou známku nebo že máte oprávnění od vlastníka ochranné známky, abyste mohli toto slovo použít. Společnost Microsoft není odpovědná za žádné právní problémy, které mohou nastat podle vašeho výběru klíčového slova.

## <a name="create-your-keyword"></a>Vytvoření klíčového slova

Než budete moct použít vlastní klíčové slovo, budete muset vytvořit klíčové slovo pomocí stránky [vlastní klíčová slova](https://aka.ms/sdsdk-wakewordportal) v [studiu řeči](https://aka.ms/sdsdk-speechportal). Po zadání klíčového slova se vytvoří soubor, který nasadíte do svého zařízení.

1. Pokud ještě nemáte předplatné pro rozpoznávání řeči, vyberte [**Vytvořit předplatné**](https://go.microsoft.com/fwlink/?linkid=2086754)a přihlaste se ke službě [Speech Studio](https://aka.ms/sdsdk-speechportal) a **přihlaste** se.

1. Na stránce [vlastní klíčové slovo](https://aka.ms/sdsdk-wakewordportal) zadejte klíčové slovo dle vašeho výběru a klikněte na **Přidat klíčové slovo**. Máme nějaké [pokyny](#choose-an-effective-keyword) , které vám pomůžou zvolit efektivní klíčové slovo. Podpora je aktuálně omezená na jazyk EN-US.

    ![Zadejte své klíčové slovo](media/speech-devices-sdk/custom-kws-portal-enter-keyword.png)

1. Portál teď pro klíčové slovo vytvoří kandidáty na kandidáty. Naslouchat každému kandidátovi kliknutím na tlačítko Přehrát a odebrat kontroly u všech nesprávných výslovností. Jakmile budou kontrolovány pouze dobré výslovnosti, vyberte **Odeslat** pro začátek generování klíčového slova. Chcete-li změnit klíčové slovo, odstraňte nejprve existující kliknutím na tlačítko Odstranit, které se zobrazí na pravé straně řádku, a najeďte na něj.

    ![Kontrola klíčového slova](media/speech-devices-sdk/custom-kws-portal-review-keyword.png)

1. Generování modelu může trvat až jednu minutu. Pak se zobrazí výzva k stažení souboru.

    ![Stáhnout klíčové slovo](media/speech-devices-sdk/custom-kws-portal-download-keyword.png)

1. Uložte soubor .zip na vašem počítači. Tento soubor budete potřebovat k nasazení vašeho vlastního klíčového slova do zařízení.

## <a name="next-steps"></a>Další kroky

Otestujte vlastní klíčové slovo pomocí [sady Speech Devices SDK rychlý Start](https://aka.ms/sdsdk-quickstart).
