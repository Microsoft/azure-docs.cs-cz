---
title: Kontrola uživatele projevy-LUIS
titleSuffix: Azure Cognitive Services
description: Podívejte se na projevy zachycené aktivním učením a vyberte entity a označte je pro čtení-World projevy; přijměte změny, školení a publikování.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/08/2020
ms.openlocfilehash: ea2b44d05d25756a16b6b84f0734966b1f579848
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "97007598"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>Postup zlepšení aplikace LUIS kontrolou Endpoint projevy

Proces kontroly projevy koncového bodu pro správný předpovědi se nazývá [Active Learning](luis-concept-review-endpoint-utterances.md). Active Learning zachycuje dotazy koncových bodů a vybere koncové body uživatele projevy, že si nejste jistí. Projděte si tyto projevy a vyberte záměr a označte entity pro tyto projevy reálného světa. Přijměte tyto změny v ukázkových promluvách, natrénujte je a publikujte je. LUIS pak identifikuje projevy přesněji.

## <a name="log-user-queries-to-enable-active-learning"></a>Protokolovat uživatelské dotazy a povolit aktivní učení

Chcete-li povolit aktivní učení, je nutné protokolovat uživatelské dotazy. Toho je možné dosáhnout voláním [dotazu koncového bodu](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) s `log=true` parametrem a hodnotou QueryString.

Pomocí portálu LUIS vytvořte správný dotaz na koncový bod.

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Otevřete svou aplikaci tak, že na stránce **Moje aplikace** vyberete její název.
1. V části **Správa** vyberte **prostředky Azure**.
1. Pro přiřazený prostředek předpovědi vyberte **změnit parametry dotazu**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky se zobrazí odkaz změnit parametry dotazu.](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. Kliknutím na tlačítko **Dokončit** zapněte možnost **Uložit protokoly** a pak ji uložte.

    > [!div class="mx-imgBorder"]
    > ![Pomocí portálu LUIS můžete ukládat protokoly, které se vyžadují pro aktivní učení.](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     Tato akce změní ukázkovou adresu URL přidáním `log=true` parametru QueryString. Kopírování a použití změněné ukázkové adresy URL dotazu při provádění prediktivních dotazů na koncový bod modulu runtime.

## <a name="correct-intent-predictions-to-align-utterances"></a>Správný předpovědi záměru pro zarovnání projevy

Každý utterance má navrhovaný záměr zobrazený ve sloupci **zarovnaný záměr** .

> [!div class="mx-imgBorder"]
> [![Zkontrolujte koncový bod projevy, na který LUIS nejste.](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

Pokud s tímto záměrem souhlasíte, zaškrtněte políčko. Pokud nesouhlasím s návrhem, vyberte v rozevíracím seznamu zarovnaný záměr správný záměr a potom zaškrtněte políčko vpravo od zarovnanáho záměru. Po zaškrtnutí políčka na zaškrtnutí bude utterance přesunut do záměru a odebrán ze seznamu **projevyového koncového bodu** .

> [!TIP]
> Je důležité přejít na stránku s podrobnostmi záměru a zkontrolovat a opravit entitu předpovědi ze všech ukázkových projevy ze seznamu **revizí koncového bodu projevy** .

## <a name="delete-utterance"></a>Odstranit utterance

Jednotlivé utterancey je možné odstranit ze seznamu revizí. Po odstranění se v seznamu znovu nezobrazí. To platí i v případě, že uživatel zadá stejný utterance z koncového bodu.

Pokud si nejste jistí, jestli byste měli utterance odstranit, přesuňte ho buď na záměr None, nebo vytvořte nový záměr, jako třeba `miscellaneous` a přesuňte utterance k tomuto záměru.

## <a name="disable-active-learning"></a>Zakázat aktivní učení

Pokud chcete zakázat aktivní učení, Neprotokolujte uživatelské dotazy. Toho je možné dosáhnout nastavením [dotazu koncového bodu](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) s `log=false` parametrem QueryString a hodnotou nebo nepoužitím hodnoty QueryString, protože výchozí hodnota je false.

## <a name="next-steps"></a>Další kroky

Chcete-li otestovat, jak se zvyšuje výkon po označení navrhovaných projevy, můžete získat přístup ke konzole test výběrem možnosti **test** na horním panelu. Pokyny k otestování aplikace pomocí testovací konzoly najdete v tématu [výuka a testování vaší aplikace](luis-interactive-test.md).
