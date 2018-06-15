---
title: Postup vytvoření znalostní báze knowledge base - QnA Maker - Azure kognitivní Services | Microsoft Docs
description: Postup vytvoření znalostní bázi knowledge base
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 93b64948ecc52feeb0f862f2b76ea898dce2333a
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "35343882"
---
# <a name="create-a-knowledge-base"></a>Vytvoření znalostní báze

QnA Maker je velmi jednoduchý zařadit do svých existujících zdrojů dat k vytvoření znalostní bázi knowledge base. Můžete vytvořit nové báze knowledge base QnA Maker z nejčastější dotazy týkající se stránky, produkty příručky, strukturovaných dokumenty nebo redakčně přidat.

## <a name="steps"></a>Kroky

1. Abyste mohli začít, přihlaste k [QnA Maker portál](https://qnamaker.ai) pomocí přihlašovacích údajů služby azure a kliknutím na tlačítko **vytvořit novou službu**.

    ![Vytvoření KB ](../media/qnamaker-how-to-create-kb/create-new-service.png)

2. Pokud jste ještě nevytvořili služby QnA Maker, vyberte **vytvoření služby QnA**. Jinak vyberte službu QnA Maker z rozevírací nabídky v kroku 2. Vyberte službu QnA Maker, který bude hostitelem znalostní bázi Knowledge Base.

    ![Nastavení služby QnA](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

3. Zadejte následující informace pro vytvoření znalostní bázi knowledge base.

    ![Sada zdrojů dat](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Poskytnout služby **název.** Duplicitní názvy jsou podporované a jsou také podporovány speciální znaky.
    - Vložte adresy URL, které budou extrahována ze. Zobrazit další informace o typech zdrojů podporované [zde](../Concepts/data-sources-supported.md).
    - Můžete také nahrajte soubory, ze které je extrahována data. Najdete v článku [informace o cenách](https://aka.ms/qnamaker-pricing
) chcete zobrazit, kolik dokumenty, které můžete přidat.
    - Pokud chcete ručně přidat QnAs, můžete přeskočit propojení souborů.

4. Vyberte **Vytvořit**.

    ![Vytvoření KB](../media/qnamaker-how-to-create-kb/create-kb.png)

5. Trvá několik minut pro data extrahovat.

    ![Extrakce](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

6. Pokud vaše znalostní báze Knowledge Base byla úspěšně vytvořena, budete přesměrováni na **znalostní báze Knowledge base** stránky.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Import znalostní bázi knowledge base](../Tutorials/migrate-knowledge-base.md)
