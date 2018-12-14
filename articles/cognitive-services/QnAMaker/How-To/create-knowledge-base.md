---
title: Vytvoření znalostní báze
titleSuffix: QnA Maker - Azure Cognitive Services
description: Použijte portál QnA Maker, chcete-li přidat vytvořte znalostní bázi s chit konverzace. Díky tomu aplikace zapojení. Přidat sadu nejčastější konverzace chit předvyplněný do znalostní BÁZÍ jako výchozí bod pro svého robota chit chat a vám ušetří čas a náklady na jejich vytváření od začátku.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 12/11/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 4a4f3d44454e7851f74bbeb072a9d4dcd600a6ef
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53338120"
---
# <a name="quickstart-create-a-knowledge-base-using-the-qna-maker-portal"></a>Rychlý start: Vytvoření znalostní báze na portálu pro nástroj QnA Maker

Nástroj QnA Maker usnadňuje přidat stávajících zdrojů dat při vytváření znalostní báze. Můžete vytvořit nové znalostní báze QnA Maker z následujících typů dokumentu:

<!-- added for scanability -->
* Nejčastější dotazy k stránky
* Příručky pro produkty
* Strukturovaných dokumentů

Zahrnout posouzení vašich osobnostních chit chatu, aby své znalosti v oblasti Další zapojení uživatelů.

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

1. Přidat **Chit chat** do znalostní BÁZÍ. Zvolte Přidat podporu chit chat pro svého robota výběrem jedné z 3 osobnosti. 

    ![Přidat chit chat KB ](../media/qnamaker-how-to-create-kb/create-kb-chit-chat.png)

1. Vyberte **vytvořit znalostní BÁZÍ**.

    ![Vytvoření znalostní BÁZE](../media/qnamaker-how-to-create-kb/create-kb.png)

1. Trvá několik minut, než se data extrahovat.

    ![Extrakce](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

1. Po úspěšném vytvoření znalostní báze, budete přesměrováni **znalostní báze** stránky.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete hotovi s znalostní báze, odeberte ho na portálu pro nástroj QnA Maker.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přidejte osobní chit chat](./chit-chat-knowledge-base.md)
