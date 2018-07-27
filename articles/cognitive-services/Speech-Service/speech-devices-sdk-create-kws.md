---
title: Vytváří se vlastní probuzení word
description: Vytvoření vlastní probuzení word pro sadou SDK pro řeč zařízení.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: 615a901c70fff92141442699ea6e4b8fce1c9ace
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282569"
---
# <a name="create-a-custom-wake-word-using-speech-service"></a>Vytvořit vlastní probuzení slovo pomocí Speech service

Vaše zařízení je vždy naslouchání pro probuzení slova (nebo fráze). Například "Hey Cortana" je slovo probuzení pro asistenta Cortany. Když uživatel zvolí slovo probuzení, spuštění zařízení, všechny následné zvuk posílají do cloudu, dokud uživatel zastaví čtení. Přizpůsobení vaší aplikace word probuzení je účinný způsob, jak odlišit zařízení a posilovat brandingu.

V tomto článku se dozvíte, jak vytvořit vlastní probuzení word pro vaše zařízení.

## <a name="choosing-an-effective-wake-word"></a>Výběr word efektivní probuzení

Při výběru slova probuzení vezměte v úvahu následující pokyny.

* Vaše aplikace word probuzení by měl být anglického slova nebo fráze. By měla trvat delší než 2 sekundy říct.

* Slova 4 – 7 slabik fungují nejlépe. "Hey, počítač" je dobré probuzení slova, zatímco právě "Hey" je nízký.

* Probuzení slova by měly dodržovat běžných pravidel anglické výslovnost.

* Jedinečný nebo dokonce zhotovené slova, která následuje běžných pravidel anglické Výslovnost se může snížit počet falešně pozitivních výsledků. Například "computerama" může být vhodné probuzení word.

* Nevybírejte běžné slovo. Například "jíst" a "přejděte" jsou slova, které lidé často říkají v běžném konverzace. Může být falešně aktivační události pro vaše zařízení.

* Vyhněte se použití probuzení slovo, které by mohly mít alternativní výslovnosti. Uživatelé museli vědět, výslovnost "vpravo" zařízení reagovat. Například "509" může projevit jako "pět nula devíti", "pět ale devět", nebo "pět set a devět." "R.E.I." může být vyslovuje se jako "R E můžu" nebo "Ray." "Live" může být vyslovuje se jako [līv] nebo [liv].

* Nepoužívejte speciální znaky, symboly nebo číslic. Například "Go #" a "20 + kočky" by dobré probuzení slova. Ale "Přejít sharp" nebo "dvacet plus kočky" může fungovat. Stále můžete použít symboly v brandingu a můžete posílit správné výslovnost uvádění na trh a dokumentace.

> [!NOTE]
> Pokud se rozhodnete značkou slova jako vaše slovo probuzení, ujistěte se, že jste vlastníkem tohoto ochranná známka, jinak mají oprávnění od vlastníka ochranná známka jeho použití. Společnost Microsoft není odpovědná za jakékoli právních problémech, které mohou vzniknout podle vašeho výběru probuzení slova.

## <a name="creating-your-wake-word"></a>Vytváří se vaše slova probuzení

Před použitím vlastní probuzení word s vaším zařízením, musíte vytvořit pomocí služby Microsoft vlastní funkci generování aplikace Word. Po zadání slovo probuzení, služba vytvoří soubor, který pak nasadíte do si vývojářský balíček, aby vaše aplikace word probuzení na vašem zařízení.

1. Přejděte [portál Custom Speech Service](https://cris.ai/).

2. Vytvořte nový účet s e-mailovou adresu, na který jste obdrželi pozvánku pro Azure Active Directory. 

    ![Vytvořit nový účet](media/speech-devices-sdk/wake-word-1.png)
 
3.  Po přihlášení, vyplňte formulář a potom klikněte na **pusťte se.**

    ![úspěšně přihlásil](media/speech-devices-sdk/wake-word-3.png)
 
4. **Slovo probuzení vlastní** stránka není dostupná, proto neexistuje žádný odkaz, která vás došlo. Klikněte na tlačítko nebo místo toho zkopírujte tento odkaz: https://cris.ai/customkws.

    ![skrytou stránku](media/speech-devices-sdk/wake-word-4.png)
 
6. Typ hledané klíčové slovo probuzení podle vašeho výběru, pak **odeslat** ho.

    ![Zadejte vaše slova probuzení](media/speech-devices-sdk/wake-word-5.png)
 
7. Může trvat několik minut, než soubory, které chcete vygenerovat. Pokryjte kruh byste měli vidět na kartě prohlížeče. Za okamžik, informační panel se zobrazí s výzvou ke stažení `.zip` souboru.

    ![přijetí souboru ZIP](media/speech-devices-sdk/wake-word-6.png)

8. Uložit `.zip` soubor do počítače. Je nutné tento soubor a nasadit vlastní probuzení slovo development Kit, postupujte podle pokynů v [Začínáme se sadou SDK pro řeč zařízení](speech-devices-sdk-qsg.md).

9. Nyní můžete **Odhlásit se.**

## <a name="next-steps"></a>Další postup

Pokud chcete začít, získejte [bezplatný účet Azure](https://azure.microsoft.com/free/) a zaregistrujte sadou SDK pro řeč zařízení.

> [!div class="nextstepaction"]
> [Zaregistrujte zařízení sadou SDK pro řeč](get-speech-devices-sdk.md)

