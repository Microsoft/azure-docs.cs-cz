---
title: Kontrola uživatele projevy-LUIS
titleSuffix: Azure Cognitive Services
description: Podívejte se na projevy zachycené aktivním učením a vyberte entity a označte je pro čtení-World projevy; přijměte změny, školení a publikování.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: b04a17c893f748670eb1c79f0ada879c0d5c401f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75381711"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>Postup zlepšení aplikace LUIS kontrolou Endpoint projevy

Proces kontroly projevy koncového bodu pro správný předpovědi se nazývá [Active Learning](luis-concept-review-endpoint-utterances.md). Active Learning zachycuje dotazy koncových bodů a vybere koncové body uživatele projevy, že si nejste jistí. Projděte si tyto projevy a vyberte záměr a označte entity pro tyto projevyy na úrovni Read-World. Přijměte tyto změny do svého ukázkového projevyu a potom proveďte výuku a publikování. LUIS pak identifikuje projevy přesněji.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>Povolit aktivní učení

Chcete-li povolit aktivní učení, je nutné protokolovat uživatelské dotazy. Toho je možné dosáhnout voláním [dotazu koncového bodu](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) s parametrem `log=true` QueryString a hodnotou.

## <a name="correct-intent-predictions-to-align-utterances"></a>Správný předpovědi záměru pro zarovnání projevy

Každý utterance má navrhované záměr zobrazí v **zarovnané záměr** sloupce.

> [!div class="mx-imgBorder"]
> [![projevy Endpoint na kontrolu, že LUIS si nejste jisti](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

Pokud s tímto záměrem souhlasíte, zaškrtněte políčko. Pokud jste Nesouhlasím s návrhem, vyberte správné záměr zarovnané záměru rozevíracího seznamu a potom vyberte na značku zaškrtnutí vpravo zarovnaný záměr. Po zaškrtnutí políčka na zaškrtnutí bude utterance přesunut do záměru a odebrán ze seznamu **projevyového koncového bodu** .

> [!TIP]
> Je důležité přejít na stránku s podrobnostmi záměru a zkontrolovat a opravit entitu předpovědi ze všech ukázkových projevy ze seznamu **revizí koncového bodu projevy** .

## <a name="delete-utterance"></a>Odstranit utterance

Každý utterance lze odstranit ze seznamu revize. Po odstranění, nebude se zobrazovat v seznamu znovu. To platí i v případě, že uživatel zadá stejný utterance z koncového bodu.

Pokud si nejste jistí, jestli byste měli utterance odstranit, přesuňte ho buď na záměr None, nebo vytvořte nový záměr, jako je například `miscellaneous` a přesuňte utterance k tomuto záměru.

## <a name="disable-active-learning"></a>Zakázat aktivní učení

Pokud chcete zakázat aktivní učení, Neprotokolujte uživatelské dotazy. Toho je možné dosáhnout nastavením [dotazu koncového bodu](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) s parametrem `log=false` QueryString a hodnotou nebo nepoužitím hodnoty řetězce dotazu, protože výchozí hodnota je false.

## <a name="next-steps"></a>Další kroky

Pokud chcete otestovat, jak vylepšuje výkon po popisek navrhované projevy, dostanete testovací konzole tak, že vyberete **testování** v horním panelu. Pokyny o tom, jak testovat svou aplikaci pomocí testovací konzole najdete v tématu [trénování a testování vaší aplikace](luis-interactive-test.md).
