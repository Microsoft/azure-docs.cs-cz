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
ms.date: 01/27/2020
ms.author: diberry
ms.openlocfilehash: 95b7c7446a47fafd26d00b0da4d880786340fcd0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219849"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>Postup zlepšení aplikace LUIS kontrolou Endpoint projevy

Proces kontroly projevy koncového bodu pro správný předpovědi se nazývá [Active Learning](luis-concept-review-endpoint-utterances.md). Active Learning zachycuje dotazy koncových bodů a vybere koncové body uživatele projevy, že si nejste jistí. Projděte si tyto projevy a vyberte záměr a označte entity pro tyto projevyy na úrovni Read-World. Přijměte tyto změny do svého ukázkového projevyu a potom proveďte výuku a publikování. LUIS pak identifikuje projevy přesněji.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>Povolit aktivní učení

Chcete-li povolit aktivní učení, je nutné protokolovat uživatelské dotazy. Toho je možné dosáhnout voláním [dotazu koncového bodu](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) s parametrem `log=true` QueryString a hodnotou.

Pomocí portálu LUIS vytvořte správný dotaz na koncový bod.

1. Na [portálu Preview Luis](https://preview.luis.ai/)v seznamu aplikací vyberte svou aplikaci.
1. V části **Správa** vyberte **prostředky Azure**.
1. Pro přiřazený prostředek předpovědi vyberte **změnit parametry dotazu**.

    > [!div class="mx-imgBorder"]
    > ![pomocí portálu LUIS ukládat protokoly, které se vyžadují pro aktivní učení.](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. Kliknutím na tlačítko **Dokončit**zapněte možnost **Uložit protokoly** a pak ji uložte.

    > [!div class="mx-imgBorder"]
    > ![pomocí portálu LUIS ukládat protokoly, které se vyžadují pro aktivní učení.](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     Tato akce změní ukázkovou adresu URL přidáním parametru `log=true` QueryString. Kopírování a použití změněné ukázkové adresy URL dotazu při provádění prediktivních dotazů na koncový bod modulu runtime.

## <a name="correct-intent-predictions-to-align-utterances"></a>Správný předpovědi záměru pro zarovnání projevy

Každý utterance má navrhovaný záměr zobrazený ve sloupci **zarovnaný záměr** .

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

Chcete-li otestovat, jak se zvyšuje výkon po označení navrhovaných projevy, můžete získat přístup ke konzole test výběrem možnosti **test** na horním panelu. Pokyny k otestování aplikace pomocí testovací konzoly najdete v tématu [výuka a testování vaší aplikace](luis-interactive-test.md).
