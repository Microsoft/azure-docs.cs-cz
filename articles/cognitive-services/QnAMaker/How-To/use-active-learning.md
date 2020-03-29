---
title: Zlepšete znalostní bázi - QnA Maker
description: Zlepšete kvalitu své znalostní báze s aktivním učením. Zkontrolujte, přijměte nebo zamítněte, přidejte bez odebrání nebo změny stávajících otázek.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 33b3c547b0aea9a1e235bf8a05d01aa16b468a71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80071130"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Využití aktivního učení k vylepšení vaší znalostní báze

[Aktivní učení](../Concepts/active-learning-suggestions.md) vám umožní zlepšit kvalitu vaší znalostní báze tím, že navrhne alternativní otázky. Odeslání uživatele se bere v úvahu a zobrazuje se jako návrhy v seznamu alternativních otázek. Máte možnost buď přidat tyto návrhy jako alternativní otázky, nebo je odmítnout.

Znalostní báze se automaticky nezmění. Aby se jakákoli změna projevila, musíte návrhy přijmout. Tyto návrhy přidávají otázky, ale nemění ani neodstraňují existující otázky.


## <a name="upgrade-runtime-version-to-use-active-learning"></a>Upgrade verze runtime pro použití aktivního učení

Aktivní učení je podporováno v modulu runtime verze 4.4.0 a vyšší. Pokud byla znalostní báze vytvořena ve starší verzi, [upgradujte běhový čas](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) tak, aby tuto funkci používal.

## <a name="turn-on-active-learning-for-alternate-questions"></a>Zapnutí aktivního učení pro alternativní otázky

Aktivní učení je ve výchozím nastavení vypnuto. Zapněte ji, abyste viděli navrhované otázky. Po zapnutí aktivního učení je potřeba odeslat informace z klientské aplikace do qnA makeru. Další informace naleznete [v tématu Architektonický tok pro použití GenerateAnswer a Train API z robota](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Chcete-li publikovat znalostní bázi, vyberte **publikovat.** Aktivní výukové dotazy jsou shromažďovány pouze z koncového bodu predikce rozhraní API generateanswer. Dotazy na podokno test na portálu QnA Maker nemají vliv na aktivní učení.

1. Pokud chcete aktivní učení zapnout na portálu QnA Maker, přejděte do pravého horního rohu, vyberte své **jméno**, přejděte na [**Nastavení služby**](https://www.qnamaker.ai/UserSettings).

    ![Na stránce Nastavení služby zapněte navrhované alternativy otázek pro aktivní učení. V nabídce vpravo nahoře vyberte své uživatelské jméno a pak vyberte Nastavení služby.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Vyhledejte službu QnA Maker a přepněte **aktivní výuku**.

    > [!div class="mx-imgBorder"]
    > [![Na stránce Nastavení služby přepněte na funkci Aktivní výuka. Pokud tuto funkci nemůžete přepínat, bude pravděpodobně nutné službu upgradovat.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > Přesná verze na předchozím obrázku je zobrazena pouze jako příklad. Vaše verze se může lišit.

    Po povolení **aktivního učení** navrhne znalostní báze v pravidelných intervalech nové otázky na základě otázek odeslaných uživatelem. **Aktivní učení** můžete zakázat znovu přepnutím nastavení.

## <a name="review-suggested-alternate-questions"></a>Kontrola navrhovaných alternativních otázek

[Zkontrolujte alternativní navrhované otázky](improve-knowledge-base.md) na stránce **Úpravy** každé znalostní báze.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření znalostní báze](./manage-knowledge-bases.md)