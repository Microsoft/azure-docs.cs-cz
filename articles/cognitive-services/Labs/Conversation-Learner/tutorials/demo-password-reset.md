---
title: Ukázka konverzace Learner modelu, pro resetování hesla – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Zjistěte, jak vytvořit model konverzace Learner ukázku.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 6436193dadb5933074fdce861a31672a0f4849de
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55227331"
---
# <a name="demo-password-reset"></a>Ukázka: Resetování hesla
Tento kurz představuje robota jednoduché technické podpory, který vám pomůže s resetování hesla s využitím learner konverzace. Model bodu robotů také další toky netriviální dialogové okno a více zapnout včetně třídy mimo doménu pořadí. Úkol můžete udělat bez kódu nebo entity.

## <a name="video"></a>Video

[![Demo Password Preview](https://aka.ms/cl_Tutorial_v3_DemoPassword_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPassword)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna bot resetování hesla

    npm run demo-password

### <a name="open-the-demo"></a>Otevřete ukázku

V seznamu modelu ve webovém uživatelském rozhraní kliknutím na kurz ukázka pro resetování hesla. 

### <a name="actions"></a>Akce

Model obsahuje sadu akcí, které má pomoct uživatelům s řešením běžných problémů heslo.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Dialogová okna školení

Model obsahuje také několik trénování dialogová okna, včetně některých, které ukazují mimo doménu třídy školení. Například, jako jsou uživatelé, kteří můžou požádat o řízení pokynů. Ukázka bot vyškolila na několik pro demonstrační účely, a jednoduše odpoví uvádí ho "nelze pomáhá, který." Seznam existujících dialogů trénování nachází v části "Dialogů Train" na levém panelu.

![](../media/tutorial_pw_reset_entities.PNG)

1. Na levém panelu klikněte na tlačítko "Dialogů Train" a potom na tlačítko "Dialogové okno Nový Train".
2. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "i ztráty hesla."
3. Klikněte na tlačítko "Skóre akce".
4. Vyberte odpověď na "Je, že pro místní účet nebo účet Microsoft?"
5. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "prosím místní účet"
6. Klikněte na tlačítko "Skóre akce".
7. Vyberte odpověď na "kterou verzi Windows máte?"
8. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "windows xp"
9. Klikněte na tlačítko "Skóre akce".
10. Klikněte "+ akce" tlačítko.
11. "Bodu robotů také odpovědi na..." pole, zadejte "řešení: Postup resetování hesla na Windows XP."
12. Klikněte na tlačítko "Vytvořit".

### <a name="training-dialogs-for-out-of-domain-scenarios"></a>Dialogová okna školení pro scénáře mimo doménu

1. Na levém panelu klikněte na tlačítko "Dialogů Train" a pak existující "hračkami úložišť" trénování dialogového okna.
2. Na panelu chatu klikněte na tlačítko utterance "slonovi úložiště".
3. V poli "Přidat alternativní vstup..." typ "vyhledávání na webu" a stiskněte enter.
4. V poli "Přidat alternativní vstup..." typ "letu rezervace" a stiskněte enter.
5. Klikněte na tlačítko "Uložit změny".
6. Klikněte na tlačítko "Upravit uložit".
7. Na levém panelu klikněte na tlačítko "Protokolu dialogů" a potom na tlačítko "Dialogové okno Nový protokol".
8. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "nelze najít heslo"
9. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Účet Microsoft"
10. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Děkujeme"
11. Klikněte na tlačítko "Testování Hotovo".
12. Kliknutím na dialogové okno protokol "nelze najít heslo" ze zobrazení mřížky.
13. Na panelu chatu, klikněte na tlačítko nesprávně vykreslené "řešení: Postup resetování hesla účtu Microsoft"odpověď.
14. Klikněte "+ akce" tlačítko.
15. "Bodu robotů také odpovědi na..." pole, zadejte "Určitě"
16. Klikněte na tlačítko "Vytvořit".
17. Klikněte na tlačítko "Uložit jako trénování dialogové okno".

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Ukázka – pizza pořadí](./demo-pizza-order.md)
