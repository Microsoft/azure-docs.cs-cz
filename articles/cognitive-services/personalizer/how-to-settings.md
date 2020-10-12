---
title: Konfigurace služby Personalizace
description: Konfigurace služby zahrnuje způsob, jakým služba zpracovává ceny, jak často se služba zkoumá, jak často se model překládá a kolik dat se ukládá.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 9b7347cb98bcbf2e1d92f115d404197083acef4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91292604"
---
# <a name="configure-personalizer-learning-loop"></a>Konfigurovat smyčku učení pro přizpůsobení

Konfigurace služby zahrnuje způsob, jakým služba zpracovává ceny, jak často se služba zkoumá, jak často se model překládá a kolik dat se ukládá.

Nakonfigurujte smyčku učení na stránce **Konfigurace** v Azure Portal pro daný prostředek přizpůsobení.

<a name="configure-service-settings-in-the-azure-portal"></a>
<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

## <a name="planning-configuration-changes"></a>Plánování změn konfigurace

Vzhledem k tomu, že některé změny konfigurace [obnovily váš model](#settings-that-include-resetting-the-model), měli byste naplánovat změny konfigurace.

Pokud máte v úmyslu použít [režim učni](concept-apprentice-mode.md), nezapomeňte před přepnutím do režimu učni zkontrolovat konfiguraci vlastního nastavení.

<a name="clear-data-for-your-learning-loop"></a>

## <a name="settings-that-include-resetting-the-model"></a>Nastavení, která zahrnují Resetování modelu

Následující akce aktivují rekurzi modelu pomocí dostupných dat až poslední 2 dny.

* Oceňujte
* Průzkum

Pokud chcete [Vymazat](how-to-manage-model.md) všechna vaše data, použijte stránku **Nastavení modelu a učení** .

## <a name="configure-rewards-for-the-feedback-loop"></a>Konfigurace neprospěchu pro smyčku zpětné vazby

Nakonfigurujte službu pro využívání výukových smyček. Změny následujících hodnot obnoví aktuální model přizpůsobeného přizpůsobování a znovu ho provede za poslední 2 dny v datech.

> [!div class="mx-imgBorder"]
> ![Konfigurovat hodnoty odměňování pro smyčku zpětné vazby](media/settings/configure-model-reward-settings.png)

|Hodnota|Účel|
|--|--|
|Doba čekání na odměnu|Nastaví dobu, během které bude přizpůsobené aplikace shromažďovat hodnoty pro volání pořadí od okamžiku, kdy dojde k volání pořadí. Tato hodnota je nastavená dotazem: jak dlouho by měl přizpůsobený systém čekat na neprospěch volání? Jakékoli odměny, které přicházejí do tohoto okna, se budou protokolovat, ale nepoužijí se ke učení.|
|Výchozí odměna|Pokud přizpůsobené není žádné volání na základě odměny během časového intervalu čekání na odměnu přidruženého k volání pořadí, přizpůsobování přiřadí výchozí odměnu. Ve výchozím nastavení a ve většině scénářů je výchozí odměna nula (0).|
|Agregace odměňování|Pokud se pro stejné volání rozhraní API pořadí přijímá více než jeden případ, použije se tato agregační metoda: **Sum** nebo **nejstarší**. Nejstarší vybere nejstarší skóre, které zbytek přijal a zahodí. To je užitečné, pokud chcete jedinečnou měnu mezi potenciálně duplicitními voláními. |

Po změně těchto hodnot nezapomeňte vybrat **Save (Uložit**).

## <a name="configure-exploration-to-allow-the-learning-loop-to-adapt"></a>Nakonfigurovat průzkum, aby se umožnilo přizpůsobení výukového cyklu

Přizpůsobení je schopné zjistit nové vzory a přizpůsobit se změnám chování uživatelů v průběhu času tím, že prozkoumá alternativy namísto použití předpovědi vyškolených modelů. Hodnota **průzkumu** určuje, jaké procento volání klasifikace je zodpovězeno s průzkumem.

Změny této hodnoty obnoví aktuální model přizpůsobeného přizpůsobování a znovu ho přeškolí za poslední 2 dny dat.

![Hodnota průzkumu určuje, jaké procento volání klasifikace je zodpovězeno s průzkumem.](media/settings/configure-exploration-setting.png)

Po změně této hodnoty nezapomeňte vybrat **Save (Uložit**).

<a name="model-update-frequency"></a>

## <a name="configure-model-update-frequency-for-model-training"></a>Konfigurace frekvence aktualizace modelu pro školení modelů

**Frekvence aktualizace modelu** nastavuje, jak často se model vyškole.

|Nastavení četnosti|Účel|
|--|--|
|1 min.|Jednorázová frekvence aktualizace je užitečná při **ladění** kódu aplikace pomocí přizpůsobeného, provádění demo ukázek nebo interaktivního testování aspektů strojového učení.|
|15 minut|Vysoké frekvence aktualizací modelu jsou užitečné v situacích, kdy chcete **pozorně sledovat změny** chování uživatelů. Mezi příklady patří weby, které běží na aktivních novinkách, Virovém obsahu nebo živých nabídkách produktů. V těchto scénářích můžete použít frekvenci na 15 minut. |
|1 hodina|Pro většinu případů použití je nižší frekvence aktualizace platná.|

![Frekvence aktualizace modelu nastavuje, jak často se nový model přizpůsobování přeučení.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

Po změně této hodnoty nezapomeňte vybrat **Save (Uložit**).

## <a name="data-retention"></a>Uchovávání dat

**Doba uchovávání dat** určuje, kolik dnů přizpůsobování udržuje protokoly dat. Minulé datové protokoly se vyžadují k provedení [hodnocení offline](concepts-offline-evaluation.md), které slouží k měření efektivity přizpůsobování a optimalizace výukových zásad.

Po změně této hodnoty nezapomeňte vybrat **Save (Uložit**).



## <a name="next-steps"></a>Další kroky

[Naučte se spravovat model](how-to-manage-model.md)
