---
title: Vytvoření vlastní služby pro rozpoznávání řeči ve slově Wake
titleSuffix: Azure Cognitive Services
description: Vaše zařízení je vždy naslouchání pro probuzení slova (nebo fráze). Když uživatel zvolí slovo probuzení, zařízení odesílá všechny následné zvuk do cloudu, dokud uživatel zastaví čtení. Přizpůsobení vaší aplikace word probuzení je účinný způsob, jak odlišit zařízení a posilovat brandingu.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 2bc1a6cbbf1e0d790326849a41b0788e332daa31
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553110"
---
# <a name="create-a-custom-wake-word-by-using-the-speech-service"></a>Vytvořit vlastní probuzení word s použitím Speech service

Vaše zařízení je vždy naslouchání pro probuzení slova (nebo fráze). Například "Hey Cortana" je slovo probuzení pro asistenta Cortany. Když uživatel zvolí slovo probuzení, zařízení odesílá všechny následné zvuk do cloudu, dokud uživatel zastaví čtení. Přizpůsobení vaší aplikace word probuzení je účinný způsob, jak odlišit zařízení a posilovat brandingu.

V tomto článku se dozvíte, jak vytvořit vlastní probuzení word pro vaše zařízení.

## <a name="choose-an-effective-wake-word"></a>Vyberte word efektivní probuzení

Při výběru slova probuzení vezměte v úvahu následující pokyny:

* Vaše aplikace word probuzení by měl být anglického slova nebo fráze. By měla trvat delší než 2 sekundy říct.

* Slova 4 až 7 slabik fungují nejlépe. Například "Hey, počítač" je dobré probuzení slovo. Právě "Hey" je nízký.

* Probuzení slova by měly dodržovat běžných pravidel anglické výslovnost.

* Jedinečný nebo dokonce zhotovené slovo, které následuje běžných pravidel anglické výslovnost může snížit počet falešně pozitivních výsledků. Například "computerama" může být vhodné probuzení word.

* Nevybírejte běžné slovo. Například "jíst" a "přejděte" jsou slova, které lidé často říkají v běžném konverzace. Mohou být false aktivační události pro vaše zařízení.

* Nepoužívejte slova probuzení, který může mít alternativní výslovnosti. Uživatelé museli vědět, výslovnost "vpravo" zařízení reagovat. Například "509" se mohou projevit "pět nula devíti," "pět ale devět," nebo "pět set a devět." "R.E.I." se mohou projevit "r-e – i" nebo "ray." "Live" se mohou projevit "/līv/" nebo "/liv/".

* Nepoužívejte speciální znaky, symboly nebo číslic. Například "Go #" a "20 + kočky" by dobré probuzení slova. Ale "Přejít sharp" nebo "dvacet plus kočky" mohou fungovat. Stále můžete použít symboly v brandingu a můžete posílit správné výslovnost uvádění na trh a dokumentace.

> [!NOTE]
> Pokud se rozhodnete značkou slova jako vaše slovo probuzení, ujistěte se, že vlastníte tuto ochranná známka nebo, že máte oprávnění vlastníka ochranná známka použít slova. Společnost Microsoft není odpovědná za jakékoli právních problémech, které mohou nastat ze zvoleného probuzení slova.

## <a name="create-your-wake-word"></a>Vytvoření vaší aplikace word probuzení

Než budete moct použít vlastní probuzení zařízení, budete muset vytvořit probudit Word se službou Microsoft Custom Wake Word Generation. Jakmile zadáte slovo Wake, služba vytvoří soubor, který nasadíte do vývojové sady, aby bylo možné na zařízení zapnout slovo Wake.

1. Přejít na [portál Custom Speech Service](https://aka.ms/sdsdk-speechportal) a **Přihlásit** se nebo pokud nemáte předplatné pro rozpoznávání řeči, vyberte [**Vytvořit předplatné**](https://go.microsoft.com/fwlink/?linkid=2086754) .

    ![Portál Custom Speech Service](media/speech-devices-sdk/wake-word-4.png)

1. Na vlastním typu stránky [Wordu Wake](https://aka.ms/sdsdk-wakewordportal) v slově Wake vyberte a klikněte na **Přidat probuzení**. Máme nějaké [pokyny](#choose-an-effective-wake-word) , které vám pomůžou zvolit efektivní klíčové slovo. V současné době podporujeme jenom jazyk EN-US.

    ![Zadejte vaše slova probuzení](media/speech-devices-sdk/wake-word-5.png)

1. Vytvoří se tři alternativní výslovnosti slova Wake. Můžete zvolit všechny výslovnosti, které chcete. Pak vyberte **Odeslat** , aby se vygenerovalo slovo Wake. Pokud chcete změnit slovo probudit, nejdřív prosím odeberte existující, a když najedete na řádek výslovnosti, zobrazí se ikona odstranit.

    ![Kontrola slova Wake](media/speech-devices-sdk/wake-word-6.png)

1. Generování modelu může trvat až jednu minutu. Zobrazí se výzva ke stažení souboru.

    ![Stažení slova Wake](media/speech-devices-sdk/wake-word-7.png)

1. Uložte soubor .zip na vašem počítači. Tento soubor budete potřebovat k nasazení vlastního probuzení Wordu do vývojové sady.

## <a name="next-steps"></a>Další kroky

Vyzkoušejte si vlastní funkci probuzení Wordu pro [rozpoznávání řeči zařízení pomocí sady Speech](https://aka.ms/sdsdk-quickstart).
