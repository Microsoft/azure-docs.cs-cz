---
title: Skóre odměny - Personalizátor
description: Skóre odměny označuje, jak dobře volba personalizace, RewardActionID, výsledkem pro uživatele. Hodnota skóre odměny je určena vaší obchodní logikou na základě pozorování chování uživatelů. Personalizátor trénuje své modely strojového učení vyhodnocením odměn.
ms.date: 02/20/2020
ms.topic: conceptual
ms.openlocfilehash: 734e4d0fdcec25884f8535ec61ccd10569fa8890
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219365"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>Skóre odměn y znamená úspěch personalizace

Skóre odměny označuje, jak dobře volba [personalizace, RewardActionID](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/rank/rank#response), výsledkem pro uživatele. Hodnota skóre odměny je určena vaší obchodní logikou na základě pozorování chování uživatelů.

Personalizátor trénuje své modely strojového učení vyhodnocením odměn.

Přečtěte [si, jak nawebu](how-to-settings.md#configure-rewards-for-the-feedback-loop) na webu Azure Portal nakonfigurovat výchozí skóre odměnpro váš prostředek personalikátoru.

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>Pomocí rozhraní REWARD API můžete odeslat skóre odměn personalistovi

Odměny jsou zasílány personalistovi [pomocí rozhraní Reward API](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/events/reward). Odměnou je obvykle číslo od 0 do 1. Negativní odměna s hodnotou -1 je možná v určitých scénářích a měla by být použita pouze v případě, že máte zkušenosti s učením výztuže (RL). Personalizátor trénuje model, aby dosáhl nejvyšší možné sumy odměn v průběhu času.

Odměny jsou odesílány po chování uživatele, což může být o několik dní později. Maximální doba personalizace bude čekat, až se událost bude považovat za žádnou odměnu nebo výchozí odměna je nakonfigurována s [dobou čekání odměny](#reward-wait-time) na webu Azure Portal.

Pokud nebylo skóre odměny za událost v rámci **čekací doby odměny**obdrženo , bude použita **výchozí odměna.** **[Výchozí odměna](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** je obvykle nakonfigurována jako nulová.


## <a name="behaviors-and-data-to-consider-for-rewards"></a>Chování a data, která je třeba zvážit pro odměny

Zvažte tyto signály a chování pro kontext skóre odměny:

* Přímý vstup uživatele pro návrhy, pokud jde o možnosti ("Myslíte X?").
* Délka relace.
* Čas mezi sezeními.
* Analýza mínění interakcí uživatele.
* Přímé otázky a mini průzkumy, kde bot požádá uživatele o zpětnou vazbu o užitečnosti, přesnosti.
* Odpověď na výstrahy nebo zpoždění na odpověď na výstrahy.

## <a name="composing-reward-scores"></a>Skládání skóre odměny

Skóre odměny musí být vypočítáno ve vaší obchodní logice. Skóre může být reprezentováno jako:

* Jedno číslo odeslané jednou
* Skóre odeslané okamžitě (například 0,8) a další skóre odeslané později (obvykle 0,2).

## <a name="default-rewards"></a>Výchozí odměny

Pokud v rámci čekací doby odměny , doby trvání od volání [hodnosti,](#reward-wait-time)personalista implicitně použije **výchozí odměnu** na tuto událost hodnocení.

## <a name="building-up-rewards-with-multiple-factors"></a>Budování odměn s mnoha faktory

Pro efektivní personalizaci můžete získat skóre odměny na základě několika faktorů.

Tato pravidla můžete například použít pro přizpůsobení seznamu videoobsahu:

|Chování uživatele|Částečná hodnota skóre|
|--|--|
|Uživatel klikl na horní položku.|+0.5 odměna|
|Uživatel otevřel skutečný obsah této položky.|+0.3 odměna|
|Uživatel sledoval 5 minut obsahu nebo 30 %, podle toho, co je delší.|+0.2 odměna|
|||

Celkovou odměnu pak můžete odeslat do rozhraní API.

## <a name="calling-the-reward-api-multiple-times"></a>Volání rozhraní REWARD API vícekrát

Můžete také volat bonus API pomocí stejného ID události, odesílání různých skóre odměny. Když personalizátor získá tyto odměny, určí konečnou odměnu za tuto událost tak, že je agreguje tak, jak je uvedeno v konfiguraci Personalista.

Hodnoty agregace:

*  **Za prvé**: Vezme první skóre odměny získané pro událost, a zahodí zbytek.
* **Součet**: Vezme všechny skóre odměny shromážděné pro eventId a sečte je dohromady.

Všechny odměny za událost, které jsou obdrženy po **čekací době odměny**, jsou vyřazeny a nemají vliv na trénink modelů.

Sečtením skóre odměny může být vaše konečná odměna mimo očekávaný rozsah skóre. Tím se služba nezklame.

## <a name="best-practices-for-calculating-reward-score"></a>Doporučené postupy pro výpočet skóre odměny

* **Vezměme si skutečné ukazatele úspěšnépersonalizace**: Je snadné myslet, pokud jde o kliknutí, ale dobrá odměna je založena na tom, co chcete, aby vaši uživatelé *dosáhnout* místo toho, co chcete, aby lidé *dělali*.  Například odměňování za kliknutí může vést k výběru obsahu, který je náchylný k clickbaitu.

* **Použijte skóre odměny za to, jak dobré personalizace fungovala**: Přizpůsobení návrhu filmu by snad vedlo k tomu, že by uživatel sledoval film a dal mu vysoké hodnocení. Vzhledem k tomu, že hodnocení filmu pravděpodobně závisí na mnoha věcech (kvalita herectví, nálada uživatele), není to dobrý signál odměny za to, jak dobře *personalizace* fungovala. Uživatel sledující prvních pár minut filmu však může být lepším signálem efektivity personalizace a odeslání odměny 1 po 5 minutách bude lepším signálem.

* **Odměny se vztahují pouze na RewardActionID**: Personalizátor použije odměny, aby pochopil účinnost akce uvedené v RewardActionID. Pokud se rozhodnete zobrazit další akce a uživatel na ně klikne, odměna by měla být nulová.

* **Zvažte nezamýšlené důsledky**: Vytvořte funkce odměňování , které vedou k odpovědným výsledkům s [etikou a odpovědným používáním](ethics-responsible-use.md).

* **Použít přírůstkové odměny**: Přidání částečných odměn za chování menších uživatelů pomáhá personalistovi dosáhnout lepších odměn. Tato přírůstková odměna umožňuje algoritmu vědět, že se blíží k zapojení uživatele do konečného požadovaného chování.
    * Pokud zobrazujete seznam filmů, pokud uživatel na chvíli zobrazí další informace, zobrazí se nad prvním, můžete určit, že došlo k určité interakci uživatelů. Chování se může počítat se skóre odměny 0,1.
    * Pokud uživatel otevřel stránku a poté ji opustil, může být skóre odměny 0,2.

## <a name="reward-wait-time"></a>Doba čekání na odměnu

Personalizák bude korelovat informace o volání hodnosti s odměnami zaslanými v rámci volání za odměnu za vyškolení modelu. Ty mohou přijít v různých časech. Personalizár čeká na omezenou dobu, počínaje voláním hodnosti, i když bylo volání hodnosti provedeno jako neaktivní událost a aktivováno později.

Pokud vyprší **čekací doba odměny** a nebyly k dispozici žádné informace o odměně, použije se na tuto událost pro trénink výchozí odměna. Maximální čekací doba je 6 dní.

## <a name="best-practices-for-reward-wait-time"></a>Doporučené postupy pro čekací dobu odměny

Postupujte podle těchto doporučení pro lepší výsledky.

* Zaměřte čekací dobu odměny co nejkratší, abyste měli dostatek času na získání zpětné vazby od uživatelů.

* Nevybírejte dobu, která je kratší než doba potřebná k získání zpětné vazby. Pokud například některé z vašich odměn přijdou poté, co uživatel zhlédne 1 minutu videa, délka experimentu by měla být alespoň dvojnásobná.

## <a name="next-steps"></a>Další kroky

* [Zpětnovazební učení](concepts-reinforcement-learning.md)
* [Vyzkoušejte rozhraní API hodnosti](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [Vyzkoušejte rozhraní API pro odměňování](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)
