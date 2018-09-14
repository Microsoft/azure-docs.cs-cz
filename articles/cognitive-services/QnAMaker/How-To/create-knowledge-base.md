---
title: Vytvoření znalostní báze – QnA Maker
titleSuffix: Azure Cognitive Services
description: Přidání chit chat pro svého robota díky konverzační a poutavé. Nástroj QnA Maker umožňuje snadno přidat předvyplněný sadu nejvyšší chit konverzace, do znalostní BÁZÍ. To můžete jako výchozí bod pro svého robota chit chat a vám ušetří čas a náklady na jejich vytváření od začátku.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: 66705a0afdcdb04fe49bea0bfc03286df3611071
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2018
ms.locfileid: "45543354"
---
# <a name="create-a-knowledge-base"></a>Vytvoření znalostní báze

Nástroj QnA Maker umožňuje velmi jednoduchý připojení stávajících zdrojů dat k vytvoření znalostní báze. Můžete vytvořit nové znalostní báze QnA Maker z nejčastější dotazy k stránky, produkty příručky, strukturovaných dokumentů nebo je přidat pomocí redakčních úprav.

## <a name="steps"></a>Kroky

1. Abyste mohli začít, přihlaste se k [portál QnA Maker](https://qnamaker.ai) s azure přihlašovací údaje a klikněte na kartu **vytvořit novou službu**.

    ![Vytvoření znalostní BÁZE ](../media/qnamaker-how-to-create-kb/create-new-service.png)

2. Pokud jste ještě nevytvořili služba QnA Maker, vyberte **vytvořit službu QnA**. Jinak klikněte na tlačítko služba QnA Maker z rozevírací nabídky v kroku 2. Vyberte službu QnA Maker, která bude hostovat ve znalostní bázi.

    ![Služba QnA instalace](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

3. Chcete-li vytvořit ve znalostní bázi, zadejte následující informace.

    ![Nastavení zdroje dat](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Zadejte vaši službu **název.** Duplicitní názvy jsou podporovány a jsou také podporovány speciální znaky.
    - Vložte adresy URL, které se extrahují z. Zobrazit další informace o typech podporované zdroje [tady](../Concepts/data-sources-supported.md).
    - Můžete také nahrajte soubory, ze které je extrahována data. Zobrazit [informace o cenách](https://aka.ms/qnamaker-pricing
) chcete zobrazit, kolik dokumenty, které můžete přidat.
    - Pokud chcete ručně přidat maximálně, můžete přeskočit propojování souborů.

4. Vyberte **Vytvořit**.

    ![Vytvoření znalostní BÁZE](../media/qnamaker-how-to-create-kb/create-kb.png)

5. Trvá několik minut, než se data extrahovat.

    ![Extrakce](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

6. Pokud znalostní báze byl úspěšně vytvořen, budete přesměrováni na **znalostní báze** stránky.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Importovat znalostní báze](../Tutorials/migrate-knowledge-base.md)
