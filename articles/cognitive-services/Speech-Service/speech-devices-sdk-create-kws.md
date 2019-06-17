---
title: Vytvořit vlastní probuzení word – hlasové služby
titleSuffix: Azure Cognitive Services
description: Vaše zařízení je vždy naslouchání pro probuzení slova (nebo fráze). Když uživatel zvolí slovo probuzení, zařízení odesílá všechny následné zvuk do cloudu, dokud uživatel zastaví čtení. Přizpůsobení vaší aplikace word probuzení je účinný způsob, jak odlišit zařízení a posilovat brandingu.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 2280af4bf37fdb3cd12482da855f979a9180f0ec
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65020505"
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

Před použitím vlastní probuzení word s vaším zařízením, budete muset vytvořit probuzení slova ve službě Microsoft vlastní funkci generování aplikace Word. Po zadání slovo probuzení, služba vytvoří soubor, který nasadíte do vaše sada povolit probuzení slovo na vašem zařízení.

1. Přejděte na [vlastní Speech Service Portal](https://aka.ms/sdsdk-speechportal) a **přihlášení** nebo pokud máte předplatné řeči zvolte [ **vytvořit odběr**](https://go.microsoft.com/fwlink/?linkid=2086754)

    ![Portál Custom Speech Service](media/speech-devices-sdk/wake-word-4.png)

1. Na [slovo probuzení vlastní](https://aka.ms/sdsdk-wakewordportal) napište v aplikaci word probuzení podle vašeho výběru a klikněte na tlačítko **přidat slovo probuzení**. Jsme našli [pokyny](#choose-an-effective-wake-word) pomoc při výběru efektivní klíčové slovo. Momentálně podporujeme pouze jazyk en US.

    ![Zadejte vaše slova probuzení](media/speech-devices-sdk/wake-word-5.png)

1. Vytvoří se tři alternativní výslovnosti vaše slova probuzení. Můžete použít všechny výslovnosti, které vám vyhovuje. Potom vyberte **odeslat** ke generování slovo probuzení. Pokud chcete změnit slovo probuzení prosím odeberte existující nejprve při najetí myší na řádku Výslovnost se zobrazí na ikonu Odstranit.

    ![Projděte si vaše aplikace word probuzení](media/speech-devices-sdk/wake-word-6.png)

1. Může trvat až jednu minutu modelu, který má být vygenerována. Zobrazí výzva ke stažení souboru.

    ![Stáhněte si vaše aplikace word probuzení](media/speech-devices-sdk/wake-word-7.png)

1. Uložte soubor .zip na vašem počítači. Budete potřebovat tento soubor k nasazení vaší vlastní probuzení slovo development Kit.

## <a name="next-steps"></a>Další postup

Testovat váš vlastní probuzení word s [řeči zařízení SDK Quickstart](https://aka.ms/sdsdk-quickstart).
