---
title: Vytvoření znalostní báze – QnA Maker
titleSuffix: Azure Cognitive Services
description: Přidání chit chat pro svého robota díky konverzační a poutavé. Nástroj QnA Maker umožňuje snadno přidat předvyplněný sadu nejvyšší chit konverzace, do znalostní BÁZÍ. To můžete jako výchozí bod pro svého robota chit chat a vám ušetří čas a náklady na jejich vytváření od začátku.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 059e959aec34503f31cbf87266d0633865cbff46
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53098742"
---
# <a name="create-a-knowledge-base-in-qna-maker"></a>Vytvoření znalostní báze v nástroje QnA Maker

Nástroj QnA Maker usnadňuje přidat stávajících zdrojů dat při vytváření znalostní báze. Můžete vytvořit nové znalostní báze QnA Maker z následujících typů dokumentu:

<!-- added for scanability -->
* Nejčastější dotazy k stránky
* Příručky pro produkty
* Strukturovaných dokumentů

## <a name="steps"></a>Kroky

1. Přihlaste se k [portál QnA Maker](https://qnamaker.ai) pomocí přihlašovacích údajů Azure a vyberte **vytvoření znalostní báze**.

2. Pokud jste ještě nevytvořili služba QnA Maker, vyberte **vytvořit službu QnA**. 

3. Vyberte tenanta Azure, název předplatného Azure a název prostředku Azure spojené s služba QnA Maker z seznamů v **kroku 2** portálu QnA Maker. Vyberte službu Azure QnA Maker, která bude hostovat ve znalostní bázi.

    ![Služba QnA instalace](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

4. Zadejte název znalostní báze a zdroje dat pro nový znalostní báze.

    ![Nastavení zdroje dat](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Zadejte vaši službu **název.** Duplicitní názvy a speciální znaky jsou podporovány.
    - Přidejte adresy URL pro data, která chcete, aby byl extrahován. Zobrazit další informace o typech podporované zdroje [tady](../Concepts/data-sources-supported.md).
    - Nahrání souborů pro data, která chcete, aby byl extrahován. Zobrazit [informace o cenách](https://aka.ms/qnamaker-pricing) chcete zobrazit, kolik dokumenty, které můžete přidat.
    - Pokud chcete ručně přidat maximálně, můžete přeskočit **kroku 4** je vidět na předchozím obrázku.

5. Přidat **Chit chat** do znalostní BÁZÍ. Zvolte chit chat podporu pro svého robota výběrem jedné z 3 osobnosti předem definované. 

    <!-- TBD: add back in when chit chat how-to is merged
    ![Add chit-chat to KB ](../media/qnamaker-how-to-chitchat/create-kb-chit-chat.png)
    -->

6. Vyberte **vytvořit znalostní BÁZÍ**.

    ![Vytvoření znalostní BÁZE](../media/qnamaker-how-to-create-kb/create-kb.png)

7. Trvá několik minut, než se data extrahovat.

    ![Extrakce](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

8. Po úspěšném vytvoření znalostní báze, budete přesměrováni **znalostní báze** stránky.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přidejte osobní chit chat](./chit-chat-knowledge-base.md)
