---
title: Přidání chatu do znalostní báze QnA Maker
titleSuffix: Azure Cognitive Services
description: Přidání osobní chit-chat na bot dělá to více konverzační a poutavé při vytváření KB. QnA Maker umožňuje snadno přidat předem vyplněnou sadu horní chit-chat, do kb.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: a9a14056e6be62fc1c1b5e542c1a3acceb738eac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220707"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Přidání chatu chit do znalostní báze

Přidání chit-chat na bot dělá to více konverzační a poutavé. Funkce chit-chat v QnA maker umožňuje snadno přidat předem vyplněnou sadu horních chit-chat, do vaší znalostní báze (KB). To může být výchozím bodem pro osobnost vašeho robota, a to vám ušetří čas a náklady na psaní je od nuly.  

Tato datová sada má asi 100 scénářů chit-chat v hlase více personas, jako Professional, Friendly a Witty. Vyberte si osobnost, která se nejvíce podobá hlasu vašeho robota. Daný uživatelský dotaz, QnA Maker se pokusí porovnat s nejbližší známé chit-chat QnA.  

Některé příklady různých osobností jsou níže. Můžete vidět všechny osobnostní [datové sady](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) spolu s podrobnostmi o osobnostech.

Pro uživatelský dotaz `When is your birthday?`má každá osobnost stylovou odpověď:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Osobnosti|Příklad|
|--|--|
|Professional|Věk se mě netýká.|
|Přátelské|Já opravdu nejsem ve věku.|
|Vtipný|Jsem bez věků.|
|Péče|Nemám věk.|
|Nadšený|Jsem robot, takže nemám věk.|
||


## <a name="language-support"></a>Podpora jazyků

Datové sady chit-chat jsou podporovány v následujících jazycích:

|Jazyk|
|--|
|Chinese|
|Angličtina|
|Francouzština|
|Německo|
|Italština|
|Japonština|
|Korejština|
|Portugalština|
|Španělština|


## <a name="add-chit-chat-during-kb-creation"></a>Přidat chit-chat během vytváření KB
Během vytváření znalostní báze po přidání zdrojových adres URL a souborů existuje možnost přidání chatu. Vyberte si osobnost, kterou chcete jako svou chit-chat základnu. Pokud nechcete přidat chit-chat nebo pokud již máte podporu chit-chatu ve zdrojích dat, zvolte **Žádný**. 

## <a name="add-chit-chat-to-an-existing-kb"></a>Přidat Chit-chat do existující kb
Vyberte kb a přejděte na stránku **Nastavení.** Existuje odkaz na všechny datové sady chit-chat v příslušném formátu **TSV.** Stáhněte si osobnost, kterou chcete, a nahrajte ji jako zdroj souborů. Při stahování a nahrávání souboru nezapomeňte formát nebo metadata upravovat. 
  
![Přidat chit-chat do existující KB](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>Upravte své chit-chat otázky a odpovědi
Když upravíte kb, uvidíte nový zdroj pro chit-chat, na základě osobnosti, kterou jste vybrali. Nyní můžete přidávat změněné otázky nebo upravovat odpovědi, stejně jako u jiných zdrojů. 

![Upravit qnát chit-chat](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

Chcete-li metadata zobrazit, vyberte **možnosti zobrazení** na panelu nástrojů a pak vyberte **Zobrazit metadata**.

## <a name="add-additional-chit-chat-questions-and-answers"></a>Přidejte další chit-chat otázky a odpovědi
Můžete přidat nový chit-chat QnA, který není v předdefinované sadě. Ujistěte se, že nekopírujte dvojici QnA, která je již zahrnuta v sadě chit-chat. Když přidáte jakékoli nové chit-chat QnA, bude přidán do **redakčního** zdroje. Chcete-li zajistit, aby ranker pochopil, že se jedná o chit-chat, přidejte pár metadat "Editorial: chitchat", jak je vidět na následujícím obrázku:
   
![! [Přidat chit-chat QnAs] (.. /media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Odstranit chit-chat z existující kb
Vyberte kb a přejděte na stránku **Nastavení.** Váš konkrétní chit-chat zdroj je uveden jako soubor, s vybraným jménem osobnosti. Můžete jej odstranit jako zdrojový soubor.

![Odstranit chit-chat z KB](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Import znalostní báze](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Viz také 

[Přehled služby QnA Maker](../Overview/overview.md)
