---
title: Životního cyklu znalostní báze – kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Životního cyklu znalostní bázi knowledge base
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: 9ecdd2c7823eed145621b214690eff7681065507
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "35343906"
---
# <a name="knowledge-base-lifecycle"></a>Životní cyklus znalostní báze Knowledge base
QnA Maker zjišťuje nejvhodnější v iterační cyklus změn modelu, příklady utterance, publikování a shromažďování dat z koncového bodu dotazů. 

![Vytváření cyklu](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Vytvoření QnA Maker znalostní báze
Koncový bod QnA Maker znalostní báze knowledge base (KB) poskytuje nejlepší shodu odpověď na dotaz uživatele na základě obsahu kB. Vytvoření znalostní báze je jednorázová akce pro vytvoření obsahu úložiště otázky, odpovědi a přidružených metadat. Procházení existující obsah, například stránky – nejčastější dotazy, příručky k produktům nebo strukturovaných páry Q-A lze vytvořit znalostní bázi knowledge base. Zjistěte, jak [vytvořit znalostní bázi](../How-To/create-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testování a aktualizace znalostní báze
Znalostní báze je připraven pro testování, jakmile je naplněna s obsahem, redakčně nebo prostřednictvím automatické extrakce. Testování lze provést prostřednictvím **Test** panely zadáním běžných uživatelských dotazů a ověření, že jsou odpovědi vrátil podle očekávání a s dostatečnou jistotou skóre. Můžete přidat alternativní otázky k nápravě nízkou spolehlivosti skóre. Můžete také přidat nové odpovědi, když dotaz vrátí "nebyl nalezen v databázi KB žádná shoda." výchozí odpovědi. Tato úzkou smyčky test aktualizace pokračuje, dokud budete spokojeni s výsledky. Zjistěte, jak [testování znalostní báze](../How-To/test-knowledge-base.md).

Pro velké články znalostní báze, testování je možné automatizovat pomocí generateAnswer rozhraní API. 

## <a name="publish-the-knowledge-base"></a>Publikování znalostní báze
Po dokončení testování znalostní bázi knowledge base, je možné ji publikovat. Publikování oznámení nejnovější verzi otestované znalostní báze do vyhrazené služby Azure Search indexu představující **publikovaná** znalostní báze knowledge base. Vytvoří také koncový bod, který je možné volat v aplikaci nebo chat robota.

Tímto způsobem, všechny změny prováděné na zkušební verzi znalostní báze nemají vliv na publikovanou verzi, která může být za provozu v případě produkční aplikace.

Každý z těchto znalostních bází můžete použít pro testování samostatně. Pomocí rozhraní API, můžete určit cílovou verzi testovací znalostní báze s `isTest=true` příznak ve volání generateAnswer.

Zjistěte, jak [publikování znalostní báze](../How-To/publish-knowledge-base.md).

## <a name="monitor-usage"></a>Monitorování využití
Abyste mohli protokol chat protokoly služby, museli byste povolit Application Insights při jste [vytvoření služby QnA Maker](../How-To/set-up-qnamaker-service-azure.md).

Můžete získat různé analytické informace o využití vaší služby. Další informace o tom, jak používat služby application insights k získání [analytics služby QnA Maker](../How-To/get-analytics-knowledge-base.md).

Podle toho, co poučení se z analytické údaje, zkontrolujte příslušné [aktualizace do znalostní báze](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Skóre spolehlivosti](./confidence-score.md)

## <a name="see-also"></a>Další informace najdete v tématech 

[Znalostní báze Knowledge base](./knowledge-base.md)
[QnA Maker – přehled](../Overview/overview.md)
