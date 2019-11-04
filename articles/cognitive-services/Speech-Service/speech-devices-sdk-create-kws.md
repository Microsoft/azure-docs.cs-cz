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
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: bf9afb66163532b4095e0d30b1167010320abbf8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490945"
---
# <a name="create-a-custom-keyword-by-using-the-speech-service"></a>Vytvoření vlastního klíčového slova pomocí služby Speech

Zařízení vždycky naslouchá klíčovému slovu (nebo frázi). Například "Hey Cortana" je klíčové slovo pro pomocníka Cortana. Když uživatel uvede klíčové slovo, zařízení pošle veškeré následné zvukové nahrávky do cloudu, dokud uživatel nepřestane mluvit. Přizpůsobení klíčového slova je efektivní způsob, jak odlišit vaše zařízení a posílit vaše branding.

V tomto článku se dozvíte, jak vytvořit vlastní klíčové slovo pro vaše zařízení.

## <a name="choose-an-effective-keyword"></a>Zvolit efektivní klíčové slovo

Při volbě klíčového slova Vezměte v úvahu následující pokyny:

* Klíčové slovo by mělo být anglické slovo nebo fráze. Řekněme, že by neměl trvat déle než dvě sekundy.

* Slova 4 až 7 slabiky fungují nejlépe. Například "Hey, Computer" je klíčové slovo dobrý. Pouze "Hey" je nekvalitní.

* Klíčová slova by měla dodržovat běžná pravidla pro výslovnost angličtiny.

* Jedinečné nebo dokonce zavedené slovo, které následuje po běžných pravidlech výslovnosti angličtiny, může snížit falešně pozitivní výsledky. Například "computerama" může být dobrým klíčovým slovem.

* Nevybírejte běžné slovo. Například "EAT" a "jít" jsou slova, která lidé v běžné konverzaci často říkají. Pro vaše zařízení můžou být triggery nepravdivé.

* Vyhněte se použití klíčového slova, které může mít alternativní výslovnost. Uživatelé by museli znát správné výslovnost, aby zařízení mohla reagovat. Například "509" lze vyslovit "5 0 9", "5 0 9" nebo "509". "R.E.I." lze vyslovit "r-e-i" nebo "Ray". "Živý" může být vyslovný jako "/līv/" nebo "/Liv/".

* Nepoužívejte speciální znaky, symboly a číslice. Například "jít #" a "20 + kočky" by nepředstavovala správná klíčová slova. Může se ale stát, že "jít prudce" nebo "dvacet plus kočky". Můžete pořád používat symboly v brandingu a používat marketing a dokumentaci k posílení správné výslovnosti.

> [!NOTE]
> Pokud zvolíte klíčové slovo jako klíčové slovo, ujistěte se, že vlastníte tuto ochrannou známku nebo že máte oprávnění od vlastníka ochranné známky, abyste mohli toto slovo použít. Společnost Microsoft není odpovědná za žádné právní problémy, které mohou nastat podle vašeho výběru klíčového slova.

## <a name="create-your-keyword"></a>Vytvoření klíčového slova

Než budete moct použít vlastní klíčové slovo, budete muset vytvořit klíčové slovo se službou Microsoft Custom Klíčs Generation Service. Po zadání klíčového slova služba vytvoří soubor, který nasadíte do vývojové sady, a povolte tak klíčové slovo na svém zařízení.

1. Pokud ještě nemáte předplatné pro rozpoznávání řeči, vyberte [**Vytvořit předplatné**](https://go.microsoft.com/fwlink/?linkid=2086754)a přihlaste se ke službě [Speech Studio](https://aka.ms/sdsdk-speechportal) a **přihlaste** se.

1. Na stránce [vlastní klíčové slovo](https://aka.ms/sdsdk-wakewordportal) zadejte klíčové slovo dle vašeho výběru a klikněte na **Přidat klíčové slovo**. Máme nějaké [pokyny](#choose-an-effective-keyword) , které vám pomůžou zvolit efektivní klíčové slovo. Podpora je aktuálně omezená na jazyk EN-US.

    ![Zadejte své klíčové slovo](media/speech-devices-sdk/custom-kws-portal-enter-keyword.png)

1. Portál teď pro klíčové slovo vytvoří kandidáty na kandidáty. Naslouchat každému kandidátovi kliknutím na tlačítko Přehrát a odebrat kontroly u všech nesprávných výslovností. Jakmile budou kontrolovány pouze dobré výslovnosti, vyberte **Odeslat** pro začátek generování klíčového slova. Chcete-li změnit klíčové slovo, odstraňte nejprve existující kliknutím na tlačítko Odstranit, které se zobrazí na pravé straně řádku, a najeďte na něj.

    ![Kontrola klíčového slova](media/speech-devices-sdk/custom-kws-portal-review-keyword.png)

1. Generování modelu může trvat až jednu minutu. Pak se zobrazí výzva k stažení souboru.

    ![Stáhnout klíčové slovo](media/speech-devices-sdk/custom-kws-portal-download-keyword.png)

1. Uložte soubor. zip do počítače. Tento soubor budete potřebovat k nasazení vlastního klíčového slova do vývojové sady.

## <a name="next-steps"></a>Další kroky

Otestujte vlastní klíčové slovo pomocí [sady Speech Devices SDK rychlý Start](https://aka.ms/sdsdk-quickstart).
