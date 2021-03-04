---
title: Přesný zodpovězení pomocí detekce rozsahu odpovědí – QnA Maker
description: Seznamte se s přesnými odpověďmi dostupnými ve spravovaném QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 6c597e8fd48eb157b69c918ae5fd5f699611ad75
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703401"
---
# <a name="precise-answering"></a>Přesné zodpovězení dotazů

Přesná funkce, která byla představena v QnA Maker spravovaná (Preview), umožňuje získat přesnou stručnou odpověď z nejlepšího průchodu odpovědi, který je k dispozici v článku znalostní báze pro libovolný dotaz uživatele. Tato funkce využívá model hloubkového učení, který je v běhu, který rozumí záměr dotazování uživatele a detekuje přesnou krátké odpověď v rámci odpovědi, pokud existuje krátká odpověď přítomná jako fakt v pasáži odpovědí. 

Tato funkce je ve výchozím nastavení zapnuta v podokně test, takže můžete testovat funkce specifické pro váš scénář. Tato funkce je mimořádně výhodná pro vývojáře obsahu i koncové uživatele. Vývojáři obsahu teď nepotřebují ručně zjistit konkrétní páry QnA pro všechny fakty, které jsou ve znalostní bázi Knowledge Base, a koncový uživatel nemusí procházet celý průchod odpovědí vrácený ze služby, aby vyhledal skutečný fakt, který odpovídá na dotaz uživatele. 

## <a name="precise-answering-on-qna-maker-portal"></a>Přesný zodpovězení na portálu QnA Maker

Když v QnA Makerovém portálu otevřete podokno test, zobrazí se v horní části možnost **Zobrazit krátkou odpověď** . Tato možnost bude vybrána jako výchozí. Při zadávání dotazu do podokna test se zobrazí krátká odpověď spolu s pasáží odpovědi, pokud je v průchodu odpovědí přítomna krátká odpověď. 
 
![Spravované podokno povoleného testu](../QnAMaker/media/conversational-context/test-pane-with-managed.png)

Můžete zrušit výběr možnosti **Zobrazit krátkou odpověď** , pokud chcete v podokně test zobrazit pouze pasáž odpovědi. 

Služba také vrátí skóre spolehlivosti přesnou odpověď jako **skóre** , které můžete zkontrolovat výběrem možnosti **prozkoumat** přítomné hned pod dotazem v podokně test.

![Skóre rozsahu spravované odpovědi](../QnAMaker/media/conversational-context/managed-answer-span-score.png)

## <a name="publishing-a-qna-maker-bot"></a>Publikování robota QnA Maker

Když publikujete robota, ve výchozím nastavení se ve vaší aplikaci zobrazí přesné možnosti Answer, kde uvidíte krátkou odpověď spolu s pasáží odpovědí. Informace o tom, jak v odpovědi použít přesnou odpověď (s názvem AnswerSpan), najdete v referenčních informacích k rozhraní API pro [vygenerování odpovědi](/rest/api/cognitiveservices/qnamakerv5.0-preview.1/knowledgebase/generateanswer#answerspan) . Uživatel má flexibilitu zvolit další prostředí tím, že aktualizuje šablonu prostřednictvím služby robot App Service. 

## <a name="language-support"></a>Podpora jazyků

V současné době se přesná funkce odpovědi podporuje jenom v angličtině.