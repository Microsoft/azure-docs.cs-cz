---
title: Vytvoření znalostní báze – QnA Maker
titleSuffix: Azure Cognitive Services
description: Pomocí portálu služby rozhraní API služby QnA Maker můžete přidat vytvoření znalostní báze pomocí funkce CHITEST-chat. Díky tomu bude vaše aplikace zajímavější. Přidejte předem vyplněnou sadu horních funkce CHITEST – chat do vaší znalostní báze jako výchozí bod pro funkce CHITEST – chat pro robota a Ušetřete čas a náklady na jejich zápis od začátku.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/10/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: dfd0663c09b78539a1d6b78aa7820385b9ddbdca
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563092"
---
# <a name="quickstart-create-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>Rychlý start: Vytvoření znalostní báze pomocí portálu služby rozhraní API služby QnA Maker

Portál služby rozhraní API služby QnA Maker usnadňuje přidávání stávajících zdrojů dat při vytváření znalostní báze. Můžete vytvořit nové znalostní báze QnA Maker z následujících typů dokumentu:

<!-- added for scanability -->
* Nejčastější dotazy k stránky
* Příručky pro produkty
* Strukturovaných dokumentů

Zahrňte do funkce CHITEST-chat preference, aby vaše znalosti uživatelů byly zajímavější.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="create-a-new-knowledge-base"></a>Vytvoření nové znalostní báze

1. Přihlaste se k [portál QnA Maker](https://qnamaker.ai) pomocí přihlašovacích údajů Azure a vyberte **vytvoření znalostní báze**.

1. Pokud jste ještě nevytvořili služba QnA Maker, vyberte **vytvořit službu QnA**. 

1. Vyberte tenanta Azure, název předplatného Azure a název prostředku Azure spojené s služba QnA Maker z seznamů v **kroku 2** portálu QnA Maker. Vyberte službu Azure QnA Maker, která bude hostovat ve znalostní bázi.

    ![Služba QnA instalace](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

1. Zadejte název znalostní báze a zdroje dat pro nový znalostní báze.

    ![Nastavení zdroje dat](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Zadejte vaši službu **název.** Duplicitní názvy a speciální znaky jsou podporovány.
    - Přidejte adresy URL pro data, která chcete, aby byl extrahován. Zobrazit další informace o typech podporované zdroje [tady](../Concepts/data-sources-supported.md).
    - Nahrání souborů pro data, která chcete, aby byl extrahován. Zobrazit [informace o cenách](https://aka.ms/qnamaker-pricing) chcete zobrazit, kolik dokumenty, které můžete přidat.
    - Pokud chcete ručně přidat maximálně, můžete přeskočit **kroku 4** je vidět na předchozím obrázku.

1. Přidat **Chit chat** do znalostní BÁZÍ. Zvolte možnost Přidat pro robota podporu funkce CHITEST-chat, a to tak, že vyberete jednu z osobních možností. 

    ![Přidání funkce CHITEST-chat do znalostní báze KB](../media/qnamaker-how-to-create-kb/create-kb-chit-chat.png)

1. Vyberte **vytvořit znalostní BÁZÍ**.

    ![Vytvoření znalostní BÁZE](../media/qnamaker-how-to-create-kb/create-kb.png)

1. Trvá několik minut, než se data extrahovat.

    ![Extrakce](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

1. Po úspěšném vytvoření znalostní báze, budete přesměrováni **znalostní báze** stránky.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až se znalostní báze dokončí, odeberte ji na portálu QnA Maker.

## <a name="next-steps"></a>Další postup

V případě míry úspory nákladů můžete [sdílet](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker) některé, ale ne všechny prostředky Azure vytvořené pro QnA maker.

> [!div class="nextstepaction"]
> [Přidejte osobní chit chat](./chit-chat-knowledge-base.md)
