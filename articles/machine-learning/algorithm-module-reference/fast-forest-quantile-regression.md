---
title: 'Rychlá doménová struktura Quantile regrese: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul Quantile regrese Fast doménové struktury k vytvoření regresního modelu, který může předpovědět hodnoty pro zadaný počet quantiles.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/13/2020
ms.openlocfilehash: 6d59d2fabb0b74ef12e33f55467b0ba68e0b7386
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907950"
---
# <a name="fast-forest-quantile-regression"></a>Rychlá kvantilová regrese rozhodovacích stromů

Tento článek popisuje modul v Návrháři Azure Machine Learning.

Pomocí tohoto modulu můžete v kanálu vytvořit Quantile regresní model s rychlými doménovými strukturami. Quantile regrese rychlé doménové struktury je užitečná, pokud chcete lépe pochopit distribuci předpovězené hodnoty, spíše než získat jednu střední hodnotu předpovědi. Tato metoda má mnoho aplikací, včetně:  
  
- Předpověď cen  
  
- Odhad výkonnosti studenta nebo použití růstových grafů k vyhodnocení podřízeného vývoje  
  
- Zjišťování prediktivních vztahů v případech, kdy je mezi proměnnými pouze slabý vztah  
  
Tento regresní algoritmus je metoda učení **pod dohledem** , což znamená, že vyžaduje objekt s příznakem, který obsahuje sloupec popisku. Vzhledem k tomu, že se jedná o regresní algoritmus, sloupec popisku musí obsahovat pouze číselné hodnoty.

## <a name="more-about-quantile-regression"></a>Další informace o regresi Quantile

Existuje mnoho různých typů regrese. Jednoduše řečeno, regrese znamená přizpůsobení modelu pro cíl vyjádřený jako číselný vektor. Nicméně statistiku vyvíjejí stále větší pokročilé metody pro regresi.

Nejjednodušší definice *Quantile* je hodnota, která rozdělí sadu dat do skupin se stejnou velikostí; Proto hodnoty Quantile označují hranice mezi skupinami. Statisticky řečeno, quantiles jsou hodnoty, které se provádí v pravidelných intervalech, od inverzní funkce k funkci kumulativní distribuce (CDF) náhodné proměnné.

Vzhledem k tomu, že modely lineární regrese se pokusí odhadnout hodnotu číselné proměnné pomocí jednoho odhadu *, je třeba v některých*případech potřebovat odhadnout rozsah nebo celou distribuci cílové proměnné. Pro tento účel se vyvinuly techniky, jako je bayesovského rozhodování regrese a Quantile regrese.

Quantile regrese vám pomůže pochopit distribuci předpovězené hodnoty. Quantile regresní modely založené na stromové struktuře, jako je například ta, která se používá v tomto modulu, mají další výhody, které lze použít k předpovídání distribucí bez ukazatelů.

  
## <a name="how-to-configure-fast-forest-quantile-regression"></a>Jak nakonfigurovat Quantile regresi rychlé doménové struktury

1. Do kanálu v Návrháři přidejte modul **Quantile regrese Fast doménové struktury** . Tento modul můžete najít v části **Machine Learning algoritmy**v kategorii **regrese** .

2. V pravém podokně **Quantile regrese rychlé doménové struktury** určete způsob, jakým chcete model vyškolený, nastavením možnosti **vytvořit režim Trainer** .  
  
    - **Jediný parametr**: Pokud víte, jak chcete model konfigurovat, poskytněte konkrétní sadu hodnot jako argumenty. Při výukovém modelu použijte [model výuky](train-model.md).
  
    - **Rozsah parametrů**: Pokud si nejste jistí, co nejlépe vyhovuje parametrům, udělejte s použitím modulu [ladění modelu](tune-model-hyperparameters.md) modulů parametrů. Trainer projde více hodnot, které zadáte, abyste našli optimální konfiguraci.

3. **Počet stromů**, zadejte maximální počet stromů, které lze v kompletu vytvořit. Pokud vytvoříte více stromů, obvykle to vede k větší přesnosti, ale za cenu delší dobu školení.  

4. **Počet pochodů**, zadejte maximální počet listů nebo uzly terminálu, které se dají vytvořit v jakémkoli stromu.  

5. **Minimální počet instancí školení vyžadovaných pro vytvoření listu**a určení minimálního počtu příkladů, které jsou nutné k vytvoření libovolného uzlu terminálu (list) ve stromu.  
  
     Zvýšením této hodnoty zvýšíte prahovou hodnotu pro vytváření nových pravidel. Například výchozí hodnota 1, dokonce i jeden případ, může způsobit vytvoření nového pravidla. Pokud zvýšíte hodnotu na 5, musí školicí data obsahovat alespoň 5 případů, které splňují stejné podmínky.

6. **Zlomek**, zadejte číslo mezi 0 a 1, které představuje zlomek vzorků, který se má použít při sestavování jednotlivých skupin quantiles. Vzorky se volí náhodně, s náhradou.

7. **Rozdělit zlomky**, zadejte číslo od 0 do 1, které představuje zlomek funkcí, které se mají použít v každém rozdělení stromu. Používané funkce se vždycky vybírají náhodně.

8. **Quantiles, který**se má odhadnout, zadejte seznam quantiles oddělený středníkem, pro který chcete, aby model vytvořil a vytvořil předpovědi.
  
     Například pokud chcete sestavit model s odhadem pro Kvartily, měli byste zadat `0.25; 0.5; 0.75` .  

9. V případě potřeby zadejte hodnotu pro **počáteční číslo počátečního čísla** pro počáteční generátor náhodných čísel používaný modelem.  Výchozí hodnota je 0, což znamená, že je zvolen náhodný základ.
  
     Pokud potřebujete reprodukování výsledků napříč následnými běhy na stejných datech, je třeba zadat hodnotu.  

10. Připojte datovou sadu školení a nevýukový model k jednomu z školicích modulů: 

    - Pokud nastavíte **režim vytvořit Trainer** na **jeden parametr**, použijte modul [vlakového modelu](train-model.md) .

    - Pokud nastavíte **režim vytvořit Trainer** na **rozsah parametrů**, použijte modul [ladit model s parametry](tune-model-hyperparameters.md) .

    > [!WARNING]
    > 
    > - Pokud předáte rozsah parametru pro [vlakový model](train-model.md), použije se pouze první hodnota v seznamu rozsah parametrů.
    > 
    > - Pokud předáte jednu sadu hodnot parametrů do modulu [předparametrů modelu ladění](tune-model-hyperparameters.md) , když očekává rozsah nastavení pro každý parametr, ignoruje hodnoty a použije výchozí hodnoty pro učit se.
    > 
    > - Pokud vyberete možnost **rozsahu parametrů** a zadáte jednu hodnotu pro libovolný parametr, tato jediná hodnota, kterou jste zadali, se použije v celém oblouku, a to i v případě, že se jiné parametry mění v rozsahu hodnot.

11. Odešlete kanál.

## <a name="results"></a>Výsledky

Po dokončení školení:

+ Pokud chcete uložit snímek školicího modelu, vyberte modul školení a pak přepněte na kartu **výstupy + protokoly** v pravém panelu. Klikněte na ikonu pro **datovou sadu registru**.  Uložený model můžete najít jako modul ve stromu modulu.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning.
