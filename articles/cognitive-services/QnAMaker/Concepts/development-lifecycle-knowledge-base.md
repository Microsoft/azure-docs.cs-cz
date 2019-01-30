---
title: Životní cyklus znalostní báze – QnA Maker
titleSuffix: Azure Cognitive Services
description: Nástroj QnA Maker se učí nejlepší v iterativní cyklus změny modelu, příklady utterance, publikování a shromažďování dat z koncového bodu dotazů.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: c728946582ec2d0423c1e984b5ff0463f59da466
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207339"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Životní cyklus znalostní báze Knowledge base v nástroje QnA Maker
Nástroj QnA Maker se učí nejlepší v iterativní cyklus změny modelu, příklady utterance, publikování a shromažďování dat z koncového bodu dotazů. 

![Cyklus vytváření](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Vytvoření znalostní báze QnA Maker
Koncový bod znalostní báze knowledge base (KB) nástroje QnA Maker poskytuje nejlepší shody odpověď na dotaz uživatele na základě obsahu KB. Vytvoření znalostní báze se o jednorázovou akci pro nastavení úložiště obsahu otázky, odpovědi a přidružená metadata. Procházení existující obsah, například stránky – nejčastější dotazy, produktových příruček nebo strukturovaných Q A páry je možné vytvořit znalostní báze. Zjistěte, jak [vytvoření znalostní báze](../How-To/create-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testování a aktualizují znalostní báze
Znalostní báze připravený k testování, jakmile se vyplní s obsahem, buď pomocí redakčních úprav nebo Automatická extrakce. Testování lze provést prostřednictvím **Test** panelu zadáním běžné dotazy na uživatele a ověření, že odpovědi vracené odpovídají očekávání a s dostatečnou skóre spolehlivosti. Můžete přidat alternativní otázky k nápravě skóre, které se s nízkou spolehlivostí. Můžete také přidat nové odpovědi, když dotaz vrátí "nebyla nalezena odpovídající položka v KB" výchozí odpověď. Tato těsné smyčce test aktualizace pokračuje, dokud budete spokojeni s výsledky. Zjistěte, jak [testování znalostní báze](../How-To/test-knowledge-base.md).

Pro velké znalostní báze testování lze automatizovat pomocí generateAnswer rozhraní API. 

## <a name="publish-the-knowledge-base"></a>Publikování znalostní báze
Po dokončení testování ve znalostní bázi, můžete ji publikovat. Publikovat na nejnovější verzi otestované znalostní báze k vyhrazené Azure Search indexovat představující nabízených oznámení **publikované** znalostní báze knowledge base. Také se přitom vytvoří koncový bod, který je možné volat v aplikaci nebo chatovacím robotu.

Tímto způsobem veškerých změnách prováděných na testovací verzi znalostní báze nemají vliv publikovanou verzi, které můžou být naživo v produkčním prostředí aplikace.

Každá z těchto znalostních bází může služba je určená pro testování samostatně. Pomocí rozhraní API, můžete se zaměřit na testovací verzi ve znalostní bázi s `isTest=true` příznak generateAnswer volání.

Zjistěte, jak [publikovat znalostní báze](../How-To/publish-knowledge-base.md).

## <a name="monitor-usage"></a>Monitorování využití
Aby bylo možné protokol protokoly chatu vaší služby, je třeba, povolte Application Insights při vám [vytvoření služby QnA Maker](../How-To/set-up-qnamaker-service-azure.md).

Můžete získat různé analýzy využití služeb. Další informace o tom, jak pomocí služby application insights získáte [analytics pro vaši službu QnA Maker](../How-To/get-analytics-knowledge-base.md).

Podle další analýzy, provést příslušné [aktualizace do znalostní báze](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Skóre spolehlivosti](./confidence-score.md)

## <a name="see-also"></a>Další informace najdete v tématech 

[Znalostní báze](./knowledge-base.md)
[přehled nástroje QnA Maker](../Overview/overview.md)
