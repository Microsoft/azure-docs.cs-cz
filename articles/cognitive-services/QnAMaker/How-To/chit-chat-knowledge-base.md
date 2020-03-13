---
title: Přidání chit chat znalostní báze QnA Maker
titleSuffix: Azure Cognitive Services
description: Když přidáte osobní chat chit pro svého robota budete konverzační a poutavé při vytváření znalostní bázi. Nástroj QnA Maker umožňuje snadno přidat předvyplněný sadu nejvyšší chit konverzace, do znalostní BÁZÍ.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220707"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Přidat Chit chat znalostní báze

Přidání chit chat pro svého robota díky konverzační a poutavé. Funkci chit konverzace v nástroje QnA maker umožňuje snadno přidat předvyplněný sadu nejvyšší chit konverzace, do znalostní báze (KB). To může být výchozím bodem pro posouzení vašich osobnostních svého robota, a to vám ušetří čas a náklady na jejich vytváření od začátku.  

Tato datová sada obsahuje přibližně 100 scénářů funkce CHITEST – chat v hlasu více osoby, jako je Professional, Friendly and Witty. Vyberte osoby, která se nejvíce podobá hlasové svého robota. Zadaný uživatelský dotaz, nástroje QnA Maker se pokusí shodovat s nejbližší QnA známé chit chat.  

Níže jsou uvedeny některé příklady různých osobních druhů. Můžete zobrazit všechny [datové sady](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) osobnosti společně s podrobnostmi o osobních členech.

U uživatelských dotazů na `When is your birthday?`má každá osobnost ve stylu odpověď:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Osobní|Příklad|
|--|--|
|Professional|Stáří neplatí pro mě.|
|Vhodná|Nejedná se opravdu o stáří.|
|Witty|Jsem zdarma.|
|Caring|Nemáte stáří.|
|Dostáváme|Jsem robot, takže nemám věk.|
||


## <a name="language-support"></a>Podpora jazyků

CHITEST – datové sady chatu jsou podporovány v následujících jazycích:

|Jazyk|
|--|
|Čínština|
|angličtina|
|Francouzština|
|Německo|
|italština|
|Japonština|
|Korejština|
|Portugalština|
|Španělština|


## <a name="add-chit-chat-during-kb-creation"></a>Přidání chit chat během vytváření KB
Během vytváření znalostních bází, po přidání zdrojovými adresami a soubory je možnost pro přidání chit konverzace. Zvolte charakteru, který chcete použít jako základ vašich chit konverzace. Pokud nechcete přidat CHITEST – chat nebo pokud již máte ve svých zdrojích dat podporu funkce CHITEST-chat, vyberte možnost **žádný**. 

## <a name="add-chit-chat-to-an-existing-kb"></a>Přidat do existující KB Chit chatu
Vyberte své znalostní báze a přejděte na stránku **Nastavení** . V příslušném formátu **. TSV** je odkaz na všechny datové sady CHITEST-chat. Posouzení vašich osobnostních, které chcete stáhnout, potom nahrajte ho jako souboru použitému jako zdroj. Zajistěte, aby při stažení a nahrání souboru upravit formát nebo metadata. 
  
![Přidat do existující KB chit chatu](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>Upravit chit chat otázky a odpovědi
Při úpravě znalostní BÁZÍ, zobrazí se nový zdroj pro chit chat, založené na posouzení vašich osobnostních, kterou jste vybrali. Teď můžete přidat, změnit dotazy nebo upravit odpovědi, stejně jako s jakýmkoli zdrojem. 

![Upravit maximálně chit chatu](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

Chcete-li zobrazit metadata, vyberte možnost **zobrazení možností** na panelu nástrojů a pak vyberte možnost **zobrazit metadata**.

## <a name="add-additional-chit-chat-questions-and-answers"></a>Přidat další chit chat otázek a odpovědí
Můžete přidat nové QnA chit chatu, který ne v předdefinovaném nastaven. Ujistěte se, že nereplikujete QnA pár, který je již zahrnut v sadě chit konverzace. Když přidáte všechny nové funkce CHITEST-chat QnA, přidají se do vašeho **redakčního** zdroje. Chcete-li zajistit, aby toto pořadí porozumělo tomu, že se jedná o funkci CHITEST-chat, přidejte dvojici klíč/hodnota metadat "redakční: chitchat", jak je vidět na následujícím obrázku:
   
![! [Přidání funkce CHITEST-chat QnAs] (.. /media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Odstranit chit chat z existující KB
Vyberte své znalostní báze a přejděte na stránku **Nastavení** . Váš konkrétní chit chat zdroj je uveden jako soubor s názvem vybrané posouzení vašich osobnostních. Odstranit tento jako zdrojový soubor.

![Odstranit chit chat ze znalostní BÁZE](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Import znalostní báze](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Viz také: 

[Přehled služby QnA Maker](../Overview/overview.md)
