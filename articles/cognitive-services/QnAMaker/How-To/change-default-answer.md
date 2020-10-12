---
title: Získat výchozí odpověď – QnA Maker
description: Pokud se k otázce neshoduje, vrátí se výchozí odpověď. Je možné, že budete chtít změnit výchozí odpověď z standardní výchozí odpovědi.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 07/13/2020
ms.openlocfilehash: 14954f89fcdcbbc1ef4b8654582a3274f4bb0923
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776812"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Změna výchozí odpovědi pro prostředek QnA Maker

Výchozí odpověď pro znalostní bázi je určena k vrácení, když odpověď nenalezne. Pokud používáte klientskou aplikaci, například [Azure bot Service](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs#calling-qna-maker-from-your-bot), může mít také samostatnou výchozí odpověď, která indikuje, že žádná odpověď nesplňovala prahovou hodnotu skóre.

## <a name="types-of-default-answer"></a>Typy výchozí odpovědi

Ve znalostní bázi existují dva typy výchozí odpovědi. Je důležité porozumět tomu, jak a kdy se jednotlivé výsledky vrátí z prediktivního dotazu:


|Typ otázky|Popis odpovědi|
|--|--|
|Odpověď KB, když se neurčí žádná odpověď|`No good match found in KB.` – Když [rozhraní API GenerateAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) nenajde žádnou odpovídající odpověď na otázku, vrátí se `DefaultAnswer` nastavení služby App Service. Všechna znalostní báze ve stejné QnA Maker prostředku sdílejí stejný výchozí text odpovědi.<br>Nastavení můžete spravovat v Azure Portal, prostřednictvím služby App Service nebo pomocí rozhraní REST API pro [získání](https://docs.microsoft.com/rest/api/appservice/webapps/listapplicationsettings) nebo [aktualizaci](https://docs.microsoft.com/rest/api/appservice/webapps/updateapplicationsettings) nastavení.|
|Text instrukcí pro následné výzvy|Když v toku konverzace použijete následné výzvy, možná nebudete potřebovat odpověď v páru QnA, protože chcete, aby uživatel mohl vybírat z následných výzev. V takovém případě nastavte konkrétní text nastavením výchozí text odpovědi, který se vrátí s každou předpověď pro následné výzvy. Text má být zobrazen jako instruktážní text pro výběr následných výzev. Příkladem tohoto výchozího textu odpovědi je `Please select from the following choices` . Tato konfigurace je vysvětlena v dalších částech tohoto dokumentu. Dá se nastavit také jako součást definice znalostní báze `defaultAnswerUsedForExtraction` pomocí [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create).|

### <a name="client-application-integration"></a>Integrace klientské aplikace

Pro klientskou aplikaci, jako je robot se **službou Azure bot Service**, si můžete vybrat ze běžných následujících scénářů:

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
