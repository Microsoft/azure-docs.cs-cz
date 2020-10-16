---
title: Správa aplikace QnA Maker App-QnA Maker
description: QnA Maker umožňuje více lidem spolupracovat na znalostní bázi. QnA Maker nabízí možnost zlepšit kvalitu znalostní báze s aktivním učením. Jednu z nich můžete zkontrolovat, přijmout nebo odmítnout a přidat, aniž byste museli odebírat nebo měnit stávající otázky.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/11/2020
ms.openlocfilehash: 77290d271709db36f9c62e165b0b4070783b3ec6
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92128453"
---
# <a name="manage-qna-maker-app"></a>Správa aplikace QnA Maker

QnA Maker umožňuje spolupracovat s různými autory a editory obsahu tím, že nabízí možnost omezit přístup spolupracovníka na základě role spolupracovníka.
Přečtěte si další informace o [QnA maker konceptech ověřování spolupracovníka](../Concepts/role-based-access-control.md).

Kvalitu znalostní báze můžete také vylepšit tím, že navrhujete alternativní otázky prostřednictvím [aktivního učení](../Concepts/active-learning-suggestions.md). V seznamu alternativních otázek se vezme v úvahu, že se odeberou uživatelé a zobrazují se jako návrhy. Máte flexibilní možnost přidat tyto návrhy jako alternativní otázky nebo je odmítnout.

Vaše znalostní báze se nemění automaticky. Aby se změny projevily, musíte přijmout návrhy. Tyto návrhy přidávají otázky, ale nemění ani neodstraňují stávající otázky.

## <a name="add-azure-role-based-access-control-azure-rbac"></a>Přidání řízení přístupu na základě role Azure (Azure RBAC)

QnA Maker umožňuje více lidem spolupracovat na všech základech znalostní báze ve stejném prostředku QnA Maker. Tato funkce je k dispozici s [řízením přístupu na základě role Azure (Azure RBAC)](../../../active-directory/role-based-access-control-configure.md).

## <a name="access-at-the-qna-maker-resource-level"></a>Přístup na úrovni prostředků QnA Maker

V QnA Maker službě nemůžete sdílet konkrétní znalostní bázi. Pokud chcete podrobnější řízení přístupu, zvažte distribuci znalostní báze mezi různými QnA Maker prostředky a pak přidejte role do každého prostředku.

## <a name="add-a-role-to-a-resource"></a>Přidání role k prostředku

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Přidat uživatelský účet do prostředku QnA Maker

V následujících krocích se používá role spolupracovníka, ale kterákoli z [rolí](../reference-role-based-access-control.md) se dá přidat pomocí těchto kroků.

1. Přihlaste se k webu [Azure](https://portal.azure.com/) Portal a Projděte si prostředek QnA maker.

    ![Seznam prostředků QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. Přejít na kartu **Access Control (IAM)** .

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. Vyberte **Přidat**.

    ![QnA Maker IAM – přidat](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. Vyberte roli z následujícího seznamu:

    |Role|
    |--|
    |Vlastník|
    |Přispěvatel|
    |Čtecí modul Cognitive Services QnA Maker|
    |Editor QnA Maker Cognitive Services|
    |Cognitive Services uživatel|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="QnA Maker přidat roli IAM.":::

1. Zadejte e-mailovou adresu uživatele a stiskněte **Uložit**.

    ![QnA Maker IAM – Přidání e-mailu](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>Zobrazit QnA Maker základy znalostní báze

Když někdo nasdílel službu QnA Maker pomocí protokolů na [portál QnA maker](https://qnamaker.ai), uvidí všechny databáze znalostí v této službě na základě jejich role.

Když vyberou znalostní bázi, jejich aktuální role na tomto QnA Maker prostředku se zobrazí vedle názvu znalostní báze.

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="QnA Maker přidat roli IAM.":::

## <a name="upgrade-runtime-version-to-use-active-learning"></a>Upgradovat verzi modulu runtime pro použití aktivního učení

Aktivní učení je podporované v modulu runtime verze 4.4.0 a vyšší. Pokud byla znalostní báze vytvořena v dřívější verzi, [upgradujte modul runtime](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) , aby tuto funkci používal.

## <a name="turn-on-active-learning-for-alternate-questions"></a>Zapnout aktivní učení pro alternativní otázky

Aktivní učení je ve výchozím nastavení vypnuté. Zapněte ho, aby se zobrazily navržené otázky. Po zapnutí aktivního učení je potřeba odeslat informace z klientské aplikace do QnA Maker. Další informace najdete v tématu [architektonický tok pro použití GenerateAnswer a rozhraní API pro učení z robota](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Vyberte **publikovat** a publikujte znalostní bázi. Aktivní výukové dotazy se shromažďují jenom z koncového bodu předpovědi rozhraní GenerateAnswer API. Dotazy do podokna test na portálu QnA Maker neovlivňují aktivní učení.

1. Pokud chcete zapnout aktivní učení na portálu QnA Maker, přečtěte si v pravém horním rohu, vyberte své **jméno**a pak přejít na [**nastavení služby**](https://www.qnamaker.ai/UserSettings).

    ![Na stránce nastavení služby zapněte možnosti pro navrhované otázky pro aktivní učení. V pravé horní nabídce vyberte své uživatelské jméno a pak vyberte nastavení služby.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Vyhledejte službu QnA Maker a potom přepněte **aktivní učení**.

    > [!div class="mx-imgBorder"]
    > [![Na stránce nastavení služby přepněte na funkci aktivní učení. Pokud nemůžete funkci zapnout, možná budete muset upgradovat službu.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)
    > [!Note]
    > Přesná verze na předchozím obrázku se zobrazí jenom jako příklad. Vaše verze se může lišit.
    Po povolení **aktivního učení** znalostní báze v pravidelných intervalech navrhuje nové otázky na základě dotazů odeslaných uživatelem. Můžete zakázat **aktivní učení** tím, že znovu přepnete nastavení.

## <a name="review-suggested-alternate-questions"></a>Kontrola navrhovaných alternativních otázek

[Přečtěte si alternativní navrhované otázky](improve-knowledge-base.md) na stránce pro **Úpravy** jednotlivých znalostí znalostní báze.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření znalostní báze](./manage-knowledge-bases.md)