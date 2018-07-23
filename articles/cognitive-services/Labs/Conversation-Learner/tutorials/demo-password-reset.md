---
title: Ukázka konverzace Learner modelu, pro resetování hesla – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Zjistěte, jak vytvořit model konverzace Learner ukázku.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f633dd375d690a1c3e66a2a6e02ae69665dbe960
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170629"
---
# <a name="demo-password-reset"></a>Ukázka: Resetování hesla
Tato ukázka znázorňuje robota jednoduché technické podpory, který vám pomůže s resetování hesla. 

Ukazuje, jak konverzace Learner další toky netriviální dialogového okna, sekvence více zapnout, včetně třídu mimo doménu. Tato ukázka nepoužívá žádné kódu nebo entity.

## <a name="video"></a>Video

[![Ukázka heslo ve verzi Preview](http://aka.ms/cl-demo-password-preview)](http://aka.ms/blis-demo-password)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna bot resetování hesla

    npm run demo-password

### <a name="open-the-demo"></a>Otevřete ukázku

V seznamu modelu ve webovém uživatelském rozhraní kliknutím na kurz ukázka pro resetování hesla. 

### <a name="actions"></a>Akce

Vytvořili jsme sadu akcí, kde se uživatel dívá pro pomoc s jejich hesla, včetně řešení.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Dialogová okna školení

Existuje několik dialogů, školení. Existují také ukázky out doménové třídy – například požadavky uživatelů jako "řízení pokynů" jsou mimo doménu. robot se předala příklady některé z požadavků domény a můžou reagovat s "Nelze zvýšit s ním."

![](../media/tutorial_pw_reset_entities.PNG)

Jako příklad si vyzkoušíme relaci výuky.

1. Klikněte na dialogová okna trénování, pak nové dialogové okno trénování.
1. Zadejte "Můžu ztráty hesla".
2. Klikněte na akci skóre.
3. Kliknutím vyberte "Je, že pro místní účet nebo účet Microsoft?"
4. Zadejte "Místní účet".
5. Klikněte na výsledek akce.
3. Klikněte na Vybrat "kterou verzi Windows máte?"
4. Zadejte "Windows 8'.
5. Klikněte na výsledek akce.
6. Vyberte "řešení: resetování hesla v systému Windows 8."
4. Klikněte na Hotovo výuky.

Pojďme si vyzkoušet, jak robota další třídu mimo doménu.

1. Klikněte na dialogová okna trénování, pak nové dialogové okno trénování.
1. Zadejte 'vyhledávání na webu'.
    - Toto je příklad mimo doménovou třídu. 
2. Klikněte na akci skóre.
3. Kliknutím vyberte: je nám líto, můžu vám nemůže pomoci s ním."
    - Všimněte si, že skóre pro tuto možnost je nyní nízká. Ale po pár dalších výuky, se zobrazí skóre vyšší.
4. Klikněte na Hotovo výuky.

Viděli jste nyní jak vytvořit na základní technickou podporu ukázku a jak můžete zjistěte, jak poskytnout řešení a také zpracování z ukázkové dotazy.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Ukázka – pizza pořadí](./demo-pizza-order.md)
