---
title: Zkontrolovat projevy uživatelů – LUIS
titleSuffix: Azure Cognitive Services
description: Zkontrolujte projevy zachycené aktivním učením, abyste vybrali záměr a označili entity pro projevy čtení. přijímat změny, trénovat a publikovat.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: diberry
ms.openlocfilehash: 95b7c7446a47fafd26d00b0da4d880786340fcd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219849"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>Jak vylepšit aplikaci LUIS kontrolou projevy koncového bodu

Proces kontroly projevy koncového bodu pro správné předpovědi se nazývá [Aktivní učení](luis-concept-review-endpoint-utterances.md). Aktivní učení zachycuje dotazy koncového bodu a vybírá projevy koncového bodu uživatele, které si nejsou jisti. Zkontrolujte tyto projevy vybrat záměr a označit entity pro tyto projevy čtení světa. Přijměte tyto změny do ukázkových projevů a pak trénujte a publikujte. Služba LUIS pak identifikuje projevy přesněji.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>Povolení aktivního učení

Chcete-li povolit aktivní učení, je nutné protokolovat dotazy uživatelů. Toho lze dosáhnout voláním [dotazu koncového bodu](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) s parametrem a hodnotou řetězce dotazu. `log=true`

Pomocí portálu LUIS vytvořte správný dotaz koncového bodu.

1. Na [portálu LUIS ve verzi Preview](https://preview.luis.ai/)vyberte aplikaci ze seznamu aplikací.
1. Přejděte do části **Spravovat** a vyberte **prostředky Azure**.
1. Pro přiřazený prostředek předpovědi vyberte **Změnit parametry dotazu**.

    > [!div class="mx-imgBorder"]
    > ![Pomocí portálu LUIS uložte protokoly, které jsou vyžadovány pro aktivní učení.](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. Přepnout **uložit protokoly** pak uložit výběrem **Hotovo**.

    > [!div class="mx-imgBorder"]
    > ![Pomocí portálu LUIS uložte protokoly, které jsou vyžadovány pro aktivní učení.](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     Tato akce změní ukázkovou `log=true` adresu URL přidáním parametru querystring. Zkopírujte a použijte adresu URL změněného příkladu dotazu při vytváření dotazů předpověď do koncového bodu runtime.

## <a name="correct-intent-predictions-to-align-utterances"></a>Opravit záměr předpovědi zarovnat projevy

Každý utterance má navrhovaný záměr zobrazenve **sloupci Zarovnaný záměr.**

> [!div class="mx-imgBorder"]
> [![Zkontrolujte projevy koncového bodu, kterými si služba LUIS není jistá](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

Pokud s tímto záměrem souhlasíte, zaškrtněte políčko. Pokud s návrhem nesouhlasíte, vyberte správný záměr ze zarovnaného rozevíracího seznamu záměru a pak vyberte zaškrtnutí napravo od zarovnaného záměru. Po zaškrtnutí na zaškrtnutí utterance je přesunuta do záměru a odebrána ze seznamu **promluvy koncového bodu revize.**

> [!TIP]
> Je důležité přejít na stránku podrobnosti záměru ke kontrole a opravě předpovědi entit ze všech příklad projevy ze seznamu **promluvy koncový bod revize.**

## <a name="delete-utterance"></a>Odstranit utterance

Každý utterance lze odstranit ze seznamu kontrol. Po odstranění se v seznamu znovu nezobrazí. To platí i v případě, že uživatel zadá stejný utterance z koncového bodu.

Pokud si nejste jisti, zda byste měli odstranit utterance, přesuňte jej do `miscellaneous` none záměru nebo vytvořit nový záměr, jako je například a přesunout utterance k tomuto záměru.

## <a name="disable-active-learning"></a>Zakázat aktivní učení

Chcete-li zakázat aktivní učení, neprotokolujte dotazy uživatelů. Toho lze dosáhnout nastavením [dotazu koncového bodu](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) s parametrem a hodnotou řetězce dotazu `log=false` nebo nepoužitím hodnoty řetězce dotazu, protože výchozí hodnota je false.

## <a name="next-steps"></a>Další kroky

Chcete-li otestovat, jak se zlepší výkon po označení navrhované projevy, můžete získat přístup k testovací konzoli výběrem **Test** v horním panelu. Pokyny k testování aplikace pomocí testovací konzole najdete v tématu [Trénování a testování aplikace](luis-interactive-test.md).
