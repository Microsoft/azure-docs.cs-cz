---
title: Vytváření vlastní probuzení word | Microsoft Docs
description: Vytváření vlastní probuzení slova pro sadu SDK řeči zařízení.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: 2575ed24bb931ca4da05dd6663b976406af590e6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343793"
---
# <a name="create-a-custom-wake-word-using-speech-service"></a>Vytvořit vlastní probuzení slovo pomocí služby řeči

Zařízení je vždy naslouchání pro probuzení slovo (nebo frázi). "Blogu Hey Cortana" je například word probuzení pro asistenta Cortany. Když uživatel zvolí slovo probuzení, zařízení spustí odesílání všechny následné zvuk do cloudu, dokud uživatel přestane hovořícího. Přizpůsobení vaší aplikace word probuzení je efektivní způsob, jak odlišit od zařízení a posilovat brandingu.

V tomto článku zjistěte, jak vytvořit vlastní probuzení slova pro vaše zařízení.

## <a name="choosing-an-effective-wake-word"></a>Výběr efektivní probuzení aplikace word

Při výběru slovo probuzení vezměte v úvahu následující pokyny.

* Vaše aplikace word probuzení by měl být anglické slovo nebo frázi. Má trvat déle, než dvě sekundy. Tím vyjádříte.

* Slova 4 – 7 slabik nejlépe fungovat. Například "Blogu Hey, počítač" je dobré probuzení slova, při právě blogu Hey"" je nízký.

* Probuzení slova by mělo vycházet běžné pravidla výslovnosti angličtinu.

* Slova jedinečný nebo i zhotovené následujícího běžné pravidla výslovnosti anglické může snížil počet falešných poplachů. Například "computerama" může být dobré probuzení word.

* Nevybírejte běžné aplikace word. Například "eat" a "jít" jsou slova, která obecně se často říká v obyčejnou konverzace. False aktivačních událostí pro zařízení, může být.

* Nepoužívejte probuzení slovo, které by mohly mít alternativní výslovnosti. Uživatelé museli vědět, "vpravo" výslovnosti zařízení reagovat. Například "509" může projevit jako "pět nulové 9", "pět jejda devět", nebo "pěti set devět." "R.E.I." může projevit jako "R E I" nebo "Ray." "Live", může projevit jako [līv] nebo [liv].

* Nepoužívejte speciální znaky, symboly a čísla. Například "Přejděte #" a "20 + kočky" nebudou funkční probuzení slova. Ale "přejděte sharp" nebo "dvacet plus kočky" může fungovat. Stále můžete použít symboly v branding a použít marketing a dokumentace k posílení správné výslovnosti.

> [!NOTE]
> Pokud si zvolíte značkou slovo jako vaše aplikace word probuzení, ujistěte se, jste vlastníkem tohoto ochranná známka, jinak mají oprávnění od vlastníka ochranná známka ho používat. Společnost Microsoft není zodpovědná za všechny právní problémy, které může způsobit zvoleného probuzení slov.

## <a name="creating-your-wake-word"></a>Vytvoření vaší aplikace word probuzení

Než použijete vlastní probuzení word s vaším zařízením, musíte ji pomocí služby Microsoft vlastní probuzení generování Word vytvořit. Po zadání slova probuzení, vytváří služby a soubor potom nasadíte do vaší dev sady k povolení vaší aplikace word probuzení ve vašem zařízení.

1. Přejděte na [portálu vlastní řeči](https://cris.ai/).

2. Vytvořte nový účet s e-mailovou adresu, na který jste obdrželi tuto pozvánku pro Azure Active Directory. 

    ![Vytvořit nový účet](media/speech-devices-sdk/wake-word-1.png)
 
3.  Po přihlášení, vyplňte formulář, a pak klikněte na **spustit cesty.**

    ![úspěšně přihlášen.](media/speech-devices-sdk/wake-word-3.png)
 
4. **Word probuzení vlastní** stránky není k dispozici na veřejnost, takže neexistuje žádný odkaz, který přejdete existuje. Klikněte na tlačítko nebo místo toho vložte tento odkaz: https://cris.ai/customkws.

    ![skrytou stránku](media/speech-devices-sdk/wake-word-4.png)
 
6. Typ v aplikaci word probuzení podle vaší volby, pak **odeslání** ho.

    ![Zadejte vaše aplikace word probuzení](media/speech-devices-sdk/wake-word-5.png)
 
7. To může trvat několik minut, než soubory, které má být vygenerován. Měli byste vidět kruh roztočený na kartě prohlížeče. Po chvíli, zobrazí informační panel zobrazí se s žádostí o stažení `.zip` souboru.

    ![přijetí souboru ZIP](media/speech-devices-sdk/wake-word-6.png)

8. Uložit `.zip` soubor do počítače. Je nutné tento soubor k nasazení aplikace word vlastní probuzení development Kit podle pokynů v [Začínáme se sadou SDK zařízení řeči](speech-devices-sdk-qsg.md).

9. Nyní mohou **odhlásit.**

## <a name="next-steps"></a>Další postup

Abyste mohli začít, získání [bezplatný účet Azure](https://azure.microsoft.com/free/) a zaregistrujte se pro sadu SDK řeči zařízení.

> [!div class="nextstepaction"]
> [Zaregistrujte si sady SDK zařízení řeči](get-speech-devices-sdk.md)

