---
title: Nejčastější dotazy – Personality Chat
titlesuffix: Azure Cognitive Services
description: Nejčastější dotazy týkající se Personality Chat
services: cognitive-services
author: noellelacharite
manager: nitinme
ms.service: cognitive-services
ms.subservice: personality-chat
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: nolachar
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ROBOTS: NOINDEX
ms.openlocfilehash: e763e3c3731858e20226efbd354927f38c3d5d70
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704225"
---
# <a name="frequently-asked-questions"></a>Nejčastější dotazy

## <a name="why-doesnt-this-answer-every-question-i-ask-it-like-other-chat-bots"></a>Proč se tato odpověď nedotazuje na každou otázku, jako je třeba jiný chat roboty?

Diskuze o projektu: Vylepšete robota se společným malým rozhovorem, který prezentuje osobnost a vytvoří úplnější prostředí pro uživatele. Není navržený tak, aby převedl na dlouhé konverzace o tématech, která nejsou relevantní pro primární funkci bot. I když to může reagovat na všechny konverzace, je v beta verzi omezené na běžné domény v malých rozhovorech.

## <a name="how-can-i-customize-the-personality-to-suit-my-brand"></a>Jak můžu přizpůsobit osobnost, aby odpovídala mé značce?

Vyberte nejbližšího z dostupných výchozích osoby. Dnes můžete provést redakční knihovnu a upravit odpovědi, aby lépe vyhovovaly vaší značce. V budoucnu můžete nahrát sadu ukázek projevy ze zvolené preference a najít její nejbližší verzi ID. Existují také způsoby, jak přeškolit a přizpůsobit model.

## <a name="is-this-service-powering-existing-intelligent-agents-such-aszo"></a>Je tato služba napájena z existujících inteligentních agentů, jako je například Zo?

Služby, které procházejí v ZO, Cortana a Project Personality Chat sdílejí některé podobné techniky, ale jsou různými zásobníky. S využitím ZO a Cortana se zajímá učení z prostředí.

## <a name="can-this-service-lead-to-bad-customer-experiences"></a>Může tato služba vést k špatnému prostředí zákazníka?

Aby se zajistilo lepší prostředí, Personality Chat může generovat odpovědi nad rámec těch v redakční sadě a pokusí se interpretovat všechny vstupy uživatelů. Proto je možné, že odpověď nevypadá přímo v kontextu. Přidali jsme celou řadu kontrolních mechanismů, které mají zabránit nežádoucím odpovědím na základě znalostí z inteligentních agentů jako Zo. Ve výchozím nastavení je Project Personality Chat nastavený tak, aby odpovídal výhradně na rozpoznané záměry uživatele. Je vhodné si otestovat, jestli je Project Personality Chat vhodný pro vaše podmínky. Vaše zpětná vazba je vítaná, pokud si všimnete něčeho, co je potřeba se více naučit. Pokud tuto službu ve svých zákaznících používáte v budoucnu, doporučujeme zvážit, že byste měli zvážit, že byste měli zvážit, jak vám bude pomáhat při identifikaci problémů s interakcemi živého uživatele.
