---
title: 'Rychlý Start: Language Understanding (LUIS) SDK klientské knihovny a REST API'
description: Vytvoření a dotazování aplikace LUIS pomocí klientských knihoven LUIS SDK a REST API.
ms.topic: quickstart
ms.date: 12/09/2020
ms.service: cognitive-services
ms.author: aahi
manager: nitinme
ms.subservice: language-understanding
author: aahill
keywords: Azure, umělal Intelligence, AI, zpracování přirozeného jazyka, NLP, LUIS, Azure Luis, přirozené jazykové porozumění, AI chatovací robot, chatovací robot maker, porozumění přirozenému jazyku
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-luis
ms.openlocfilehash: 7ff5844cf9f1bce45df438fc00e24da28c438b05
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939211"
---
# <a name="quickstart-language-understanding-luis-client-libraries-and-rest-api"></a>Rychlý Start: Language Understanding (LUIS) klientské knihovny a REST API

V tomto rychlém startu pomocí C#, Pythonu nebo JavaScriptu vytvořte a Dotazujte aplikaci Azure LUIS umělal Intelligence (AI) pomocí klientských knihoven LUIS SDK. K odeslání požadavků pomocí REST API lze také použít oblé.

Language Understanding (LUIS) umožňuje použít NLP (přirozené jazykové zpracování) na konverzaci uživatele, text přirozeného jazyka a předpovědět celkový význam a vyžádat si relevantní, podrobné informace.

* Klientská knihovna pro **vytváření** a REST API umožňuje vytváření, úpravy, výuku a publikování aplikace Luis.
* **Předpředpověď běhové** klientské knihovny a REST API umožňuje dotazování publikované aplikace.

::: zone pivot="programming-language-csharp"
[!INCLUDE [LUIS development with C# SDK](./includes/sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [LUIS development with Node.js SDK](./includes/sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [LUIS development with Python SDK](./includes/sdk-python.md)]
::: zone-end

::: zone pivot="rest-api"
[!INCLUDE [LUIS development with REST API](./includes/rest-api.md)]
::: zone-end

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tuto aplikaci můžete z [portálu Luis](https://www.luis.ai) odstranit a odstranit prostředky Azure z [Azure Portal](https://portal.azure.com/).

Pokud používáte REST API, odstraňte `ExampleUtterances.JSON` soubor ze systému souborů, když jste hotovi s rychlým startem.

## <a name="troubleshooting"></a>Řešení potíží

* Ověřování ke klientské knihovně – chyby ověřování obvykle signalizují, že byly použity nesprávné klíčové & koncové body. V tomto rychlém startu se jako pohodlí používá vytváření klíčového a koncového bodu pro předpověď za běhu, ale funguje jenom v případě, že jste ještě nepoužívali měsíční kvótu. Pokud nemůžete použít klíč a koncový bod pro vytváření obsahu, musíte při přístupu ke klientské knihovně sady SDK předpovědi prostředí použít předpověď klíč modulu runtime a koncový bod.
* Vytváření entit – Pokud se při vytváření vnořené entity strojového učení, která se používá v tomto kurzu, zobrazí chyba, ujistěte se, že jste kód zkopírovali a nezměnili kód, aby vytvořil jinou entitu.
* Vytvoření příkladu projevy – Pokud se zobrazí chyba při vytváření ukázkového utterance, který se používá v tomto kurzu, ujistěte se, že jste kód zkopírovali a nezměnili kód tak, aby vytvořil jiný označený příklad.
* Školení – Pokud obdržíte chybu školení, obvykle to indikuje prázdnou aplikaci (žádné záměry s příkladem projevy) nebo aplikaci s úmysly nebo nepoškozenými entitami.
* Různé chyby – protože kód volá do klientských knihoven s objekty text a JSON, ujistěte se, že jste kód nezměnili.

Další chyby – Pokud se zobrazí chyba, která není pokrytá v předchozím seznamu, dejte nám vědět o zpětné vazbě na této stránce níže. Zahrňte programovací jazyk a verzi nainstalovaných klientských knihoven.

## <a name="next-steps"></a>Další kroky

* [Co je rozhraní API pro Language Understanding (LUIS)?](what-is-luis.md)
* [Co je nového?](whats-new.md)
* [Záměry](luis-concept-intent.md), [entity](luis-concept-entity-types.md)a [Příklady projevy](luis-concept-utterance.md)a [předem připravených entit](luis-reference-prebuilt-entities.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code).
* Porozumění přirozenému jazyku: [porozumění přirozenému jazyku (NLU) a zpracování přirozeného jazyka (NLP)](artificial-intelligence.md)
* Roboty: [AI chatovacích robotů o](luis-csharp-tutorial-bf-v4.md "kurz k chatovací robot maker")
