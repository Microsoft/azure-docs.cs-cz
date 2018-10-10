---
title: Vytvořit vlastní probuzení word
description: Zjistěte, jak vytvořit vlastní probuzení word pro sadou SDK pro řeč zařízení.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: 52f1d9cb242f140d485ccf7b39a55a46ee575a3d
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2018
ms.locfileid: "48883614"
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

Slovo vlastní funkci mohli používat s vaším zařízením, musíte vytvořit slovo probuzení pomocí služby Microsoft vlastní funkci generování aplikace Word. Po zadání slovo probuzení, služba vytvoří soubor, který nasadíte vaše development Kit, aby vaše aplikace word probuzení na vašem zařízení.

1. Přejděte [portál Custom Speech service](https://cris.ai/).

1. Vytvořte nový účet s e-mailovou adresu, na který jste obdrželi pozvánku pro Azure Active Directory. 

    ![Vytvoření nového účtu](media/speech-devices-sdk/wake-word-1.png)
 
1.  Po přihlášení, vyplňte formulář a potom vyberte **Moje zahajte**.

    ![Úspěšné přihlášení](media/speech-devices-sdk/wake-word-3.png)
 
1. **Slovo probuzení vlastní** stránka není dostupná, proto neexistuje žádný přímý odkaz, která vás existuje. Custom Speech funkce vyžaduje předplatným Azure, ale nebude slovo probuzení vlastní funkce. Pokud jste se dostali **nenašla se žádná předplatná.** chybové stránky, stačí nahradit **"předplatných? chybová zpráva = žádná % 20Subscriptions % 20found"** s "**customkws**" v adrese URL a přístupů ENTER. Adresa URL by měla být jedna z následujících: https://westus.cris.ai/customkws, https://eastasia.cris.ai/customkws nebo https://northeurope.cris.ai/customkws, v závislosti na místě, kde vaši oblast.

    ![Word probuzení vlastní stránka skrytá](media/speech-devices-sdk/wake-word-4.png)
 
1. Zadejte hledané klíčové slovo probuzení podle vašeho výběru a pak vyberte **odeslat slovo**.

    ![Zadejte vaše slova probuzení](media/speech-devices-sdk/wake-word-5.png)
 
1. Může trvat několik minut, než soubory, které chcete vygenerovat. Měli byste vidět pokryjte kolečko v okně prohlížeče. Za okamžik informační panel se zobrazí, s výzvou ke stažení souboru .zip.

    ![Přijetí souboru ZIP](media/speech-devices-sdk/wake-word-6.png)

1. Uložte soubor .zip na vašem počítači. Je nutné tento soubor a nasadit vlastní probuzení slovo development Kit. Pokud chcete nasadit vlastní probuzení slovo, postupujte podle pokynů v [Začínáme se sadou SDK pro řeč zařízení](speech-devices-sdk-qsg.md).

1. Vyberte **Odhlásit se.**

## <a name="next-steps"></a>Další postup

Pokud chcete začít, získejte [bezplatný účet Azure](https://azure.microsoft.com/free/) a zaregistrujte sadou SDK pro řeč zařízení.

> [!div class="nextstepaction"]
> [Zaregistrujte zařízení sadou SDK pro řeč](get-speech-devices-sdk.md)

