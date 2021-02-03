---
title: Použití aktivního učení s využitím znalostní báze Knowledge Base – QnA Maker
description: Naučte se zlepšovat kvalitu znalostní báze s aktivním učením. Zkontrolujte, přijměte nebo odmítněte, přidejte bez odebrání nebo změny existujících otázek.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 396cba079baf92da1f5d14a4ecf3dfdb7de0aa2f
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509218"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Využití aktivního učení k vylepšení vaší znalostní báze

[Aktivní učení](../Concepts/active-learning-suggestions.md) vám umožní zdokonalit kvalitu znalostní báze tím, že navrhuje alternativní otázky. V seznamu alternativních otázek se vezme v úvahu, že se odeberou uživatelé, a zobrazí se jako návrhy. Máte flexibilní možnost přidat tyto návrhy jako alternativní otázky nebo je odmítnout.

Vaše znalostní báze se nemění automaticky. Aby se změny projevily, musíte přijmout návrhy. Tyto návrhy přidávají otázky, ale nemění ani neodstraňují stávající otázky.

## <a name="upgrade-runtime-version-to-use-active-learning"></a>Upgradovat verzi modulu runtime pro použití aktivního učení

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

Aktivní učení je podporované v modulu runtime verze 4.4.0 a vyšší. Pokud byla znalostní báze vytvořena v dřívější verzi, [upgradujte modul runtime](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) , aby tuto funkci používal.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

V QnA Maker spravovaném (ve verzi Preview), protože modul runtime je hostovaný pomocí samotné služby QnA Maker, není potřeba ručně upgradovat modul runtime.

---

## <a name="turn-on-active-learning-for-alternate-questions"></a>Zapnout aktivní učení pro alternativní otázky

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

Aktivní učení je ve výchozím nastavení vypnuté. Zapněte ho, aby se zobrazily navržené otázky. Po zapnutí aktivního učení je potřeba odeslat informace z klientské aplikace do QnA Maker. Další informace najdete v tématu [architektonický tok pro použití GenerateAnswer a rozhraní API pro učení z robota](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Vyberte **publikovat** a publikujte znalostní bázi. Aktivní výukové dotazy se shromažďují jenom z koncového bodu předpovědi rozhraní GenerateAnswer API. Dotazy do podokna test na portálu QnA Maker neovlivňují aktivní učení.

1. Pokud chcete zapnout aktivní učení na portálu QnA Maker, přečtěte si v pravém horním rohu, vyberte své **jméno** a pak přejít na [**nastavení služby**](https://www.qnamaker.ai/UserSettings).

    ![Na stránce nastavení služby zapněte možnosti pro navrhované otázky pro aktivní učení. V pravé horní nabídce vyberte své uživatelské jméno a pak vyberte nastavení služby.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Vyhledejte službu QnA Maker a potom přepněte **aktivní učení**.

    > [!div class="mx-imgBorder"]
    > [![Na stránce nastavení služby přepněte na funkci aktivní učení. Pokud nemůžete funkci zapnout, možná budete muset upgradovat službu.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > Přesná verze na předchozím obrázku se zobrazí jenom jako příklad. Vaše verze se může lišit.

    Po povolení **aktivního učení** znalostní báze v pravidelných intervalech navrhuje nové otázky na základě dotazů odeslaných uživatelem. Můžete zakázat **aktivní učení** tím, že znovu přepnete nastavení.
    
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

Ve výchozím **nastavení je aktivní učení v QnA maker** spravovaném (ve verzi Preview). Chcete-li zobrazit navrhované alternativní otázky, [použijte možnosti zobrazení](../How-To/improve-knowledge-base.md#view-suggested-questions) na stránce Upravit.

---

## <a name="review-suggested-alternate-questions"></a>Kontrola navrhovaných alternativních otázek

[Přečtěte si alternativní navrhované otázky](improve-knowledge-base.md) na stránce pro **Úpravy** jednotlivých znalostí znalostní báze.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření znalostní báze](./manage-knowledge-bases.md)
