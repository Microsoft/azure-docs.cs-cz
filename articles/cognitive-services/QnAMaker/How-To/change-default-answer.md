---
title: Získat výchozí odpověď – QnA Maker
description: Pokud se k otázce neshoduje, vrátí se výchozí odpověď. Je možné, že budete chtít změnit výchozí odpověď z standardní výchozí odpovědi.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 11/09/2020
ms.openlocfilehash: ceff3127eba724ba9aa9bc8f9398d0f27ba687eb
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376603"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Změna výchozí odpovědi pro prostředek QnA Maker

Výchozí odpověď pro znalostní bázi je určena k vrácení, když odpověď nenalezne. Pokud používáte klientskou aplikaci, například [Azure bot Service](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna), může mít také samostatnou výchozí odpověď, která indikuje, že žádná odpověď nesplňovala prahovou hodnotu skóre.

## <a name="types-of-default-answer"></a>Typy výchozí odpovědi

Ve znalostní bázi existují dva typy výchozí odpovědi. Je důležité porozumět tomu, jak a kdy se jednotlivé výsledky vrátí z prediktivního dotazu:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

|Typy výchozích odpovědí|Popis odpovědi|
|--|--|
|Odpověď KB, když se neurčí žádná odpověď|`No good match found in KB.` – Když [rozhraní API GenerateAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) nenajde žádnou odpovídající odpověď na otázku, vrátí se `DefaultAnswer` nastavení služby App Service. Všechna znalostní báze ve stejné QnA Maker prostředku sdílejí stejný výchozí text odpovědi.<br>Nastavení můžete spravovat v Azure Portal, prostřednictvím služby App Service nebo pomocí rozhraní REST API pro [získání](https://docs.microsoft.com/rest/api/appservice/webapps/listapplicationsettings) nebo [aktualizaci](https://docs.microsoft.com/rest/api/appservice/webapps/updateapplicationsettings) nastavení.|
|Text instrukcí pro následné výzvy|Když v toku konverzace použijete následné výzvy, možná nebudete potřebovat odpověď v páru QnA, protože chcete, aby uživatel mohl vybírat z následných výzev. V takovém případě nastavte konkrétní text nastavením výchozí text odpovědi, který se vrátí s každou předpověď pro následné výzvy. Text má být zobrazen jako instruktážní text pro výběr následných výzev. Příkladem tohoto výchozího textu odpovědi je `Please select from the following choices` . Tato konfigurace je vysvětlena v dalších částech tohoto dokumentu. Dá se nastavit také jako součást definice znalostní báze `defaultAnswerUsedForExtraction` pomocí [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create).|

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

|Typy výchozích odpovědí|Popis odpovědi|
|--|--|
|Odpověď KB, když se neurčí žádná odpověď|`No good match found in KB.` – Když [rozhraní API GenerateAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) nenajde žádnou odpovídající odpověď na otázku, zobrazí se výchozí textová odpověď. V QnA Maker spravovaném (ve verzi Preview) můžete tento text nastavit v **Nastavení** znalostní báze. <br><br> ![QnA Maker Managed (Preview) nastavit výchozí odpověď](../media/qnamaker-how-change-default-answer/qnamaker-v2-change-default-answer.png)|
|Text instrukcí pro následné výzvy|Když v toku konverzace použijete následné výzvy, možná nebudete potřebovat odpověď v páru QnA, protože chcete, aby uživatel mohl vybírat z následných výzev. V takovém případě nastavte konkrétní text nastavením výchozí text odpovědi, který se vrátí s každou předpověď pro následné výzvy. Text má být zobrazen jako instruktážní text pro výběr následných výzev. Příkladem tohoto výchozího textu odpovědi je `Please select from the following choices` . Tato konfigurace je vysvětlena v dalších částech tohoto dokumentu. Můžete ji také nastavit jako součást definice znalostní báze s `defaultAnswerUsedForExtraction` použitím [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create).|

---

### <a name="client-application-integration"></a>Integrace klientské aplikace

Pro klientskou aplikaci, jako je robot se **službou Azure bot Service** , si můžete vybrat ze běžných následujících scénářů:

* Použít nastavení znalostní báze
* V klientské aplikaci použijte jiný text, který bude rozlišovat při vrácení odpovědi, ale nesplňuje prahovou hodnotu skóre. Tento text může být buď statický text uložený v kódu, nebo může být uložený v seznamu nastavení klientské aplikace.

## <a name="set-follow-up-prompts-default-answer-when-you-create-knowledge-base"></a>Při vytváření znalostní báze nastavit výchozí odpověď pro následnou výzvu

Při vytváření nové znalostní báze je výchozím textem odpovědí jedno z nastavení. Pokud se rozhodnete, že ho nechcete během procesu vytváření nastavit, můžete ho později změnit pomocí následujícího postupu.

## <a name="change-follow-up-prompts-default-answer-in-qna-maker-portal"></a>Změnit výchozí odpověď na příkazovém řádku na portálu QnA Maker

Výchozí odpověď znalostní báze se vrátí, když služba QnA Maker nevrátí žádnou odpověď.

1. Přihlaste se k [portálu QnA maker](https://www.qnamaker.ai/) a ze seznamu vyberte znalostní bázi.
1. V navigačním panelu vyberte **Nastavení** .
1. Změňte hodnotu **výchozího textu odpovědi** v části **Správa znalostní báze** .

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="Snímek obrazovky QnA Makerového portálu, stránka nastavení s zvýrazněným výchozím textovým polem odpovědi":::

1. Kliknutím na **Uložit a výuka** změny uložte.

## <a name="next-steps"></a>Další kroky

* [Vytvoření znalostní báze](../How-to/manage-knowledge-bases.md)