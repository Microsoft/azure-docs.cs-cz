---
title: Optimalizace algoritmů
titleSuffix: ML Studio (classic) - Azure
description: Vysvětluje, jak zvolit optimální sadu parametrů pro algoritmus v Azure Machine Learning Studio (klasické).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 04148b482cb07665f43df5bd86a77175cbbaf08b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218272"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning-studio-classic"></a>Výběr parametrů pro optimalizaci algoritmů v Azure Machine Learning Studio (klasické)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Toto téma popisuje, jak zvolit správnou sadu hyperparametrů pro algoritmus v Azure Machine Learning Studio (klasické). Většina algoritmů strojového učení má nastavené parametry. Při trénování modelu je třeba zadat hodnoty pro tyto parametry. Účinnost trénovaného modelu závisí na parametrech modelu, které zvolíte. Proces hledání optimální sady parametrů je znám jako *výběr modelu*.



Existují různé způsoby, jak provést výběr modelu. Ve strojovém učení je křížové ověřování jednou z nejpoužívanějších metod výběru modelu a je to výchozí mechanismus výběru modelu v Azure Machine Learning Studio (klasika). Vzhledem k tomu, že Azure Machine Learning Studio (klasické) podporuje R i Python, můžete vždy implementovat své vlastní mechanismy výběru modelu pomocí R nebo Pythonu.

V procesu hledání nejlepší sady parametrů jsou čtyři kroky:

1. **Definujte prostor parametru**: Pro algoritmus nejprve rozhodněte přesné hodnoty parametrů, které chcete zvážit.
2. **Definujte nastavení křížového ověření**: Rozhodněte, jak zvolit přeložení křížového ověření pro datovou sadu.
3. **Definujte metriku**: Rozhodněte, jakou metriku použít pro určení nejlepší sady parametrů, jako je přesnost, střední hodnota kořenové chyby, přesnost, odvolání nebo f-skóre.
4. **Trénování, vyhodnocení a porovnání**: Pro každou jedinečnou kombinaci hodnot parametrů se křížové ověření provádí pomocí a na základě metriky chyby, kterou definujete. Po vyhodnocení a porovnání můžete zvolit nejvýkonnější model.

Následující obrázek ukazuje, jak toho lze dosáhnout v Azure Machine Learning Studio (klasické).

![Najít nejlepší sadu parametrů](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Definování prostoru parametru
Můžete definovat parametr nastavený v kroku inicializace modelu. Podokno parametrů všech algoritmů strojového učení má dva režimy trenažéru: *Jeden parametr* a *Rozsah parametrů*. Zvolte režim Rozsah parametrů. V režimu Rozsah parametrů můžete zadat více hodnot pro každý parametr. Do textového pole můžete zadat hodnoty oddělené čárkami.

![Dvoutřídní posílený rozhodovací strom, jeden parametr](./media/algorithm-parameters-optimize/fig2.png)

 Alternativně můžete definovat maximální a minimální počet bodů mřížky a celkový počet bodů, které mají být generovány pomocí **funkce Use Range Builder**. Ve výchozím nastavení jsou hodnoty parametrů generovány v lineárním měřítku. Ale pokud je zaškrtnuto **měřítko protokolu,** hodnoty jsou generovány v měřítku protokolu (to znamená, že poměr sousedních bodů je konstantní namísto jejich rozdílu). Pro parametry celého čísla můžete definovat oblast pomocí pomlčky. Například "1-10" znamená, že všechna celá čísla mezi 1 a 10 (oba včetně) tvoří sadu parametrů. Smíšený režim je také podporován. Například sada parametrů "1-10, 20, 50" by zahrnovala celá čísla 1-10, 20 a 50.

![Dvoutřídní posílený rozhodovací strom, rozsah parametrů](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Definování křížových validačních záhybů
[Oddíl a ukázkový][partition-and-sample] modul lze náhodně přiřadit záhyby k datům. V následující ukázkové konfiguraci modulu definujeme pětkrát a náhodně přiřadíme přeložené číslo ukázkovým instancím.

![Rozdělení a ukázka](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Definování metriky
Modul [Tune Model Hyperparameters][tune-model-hyperparameters] poskytuje podporu pro empiricky výběr nejlepší sadu parametrů pro daný algoritmus a datové sady. Kromě dalších informací týkajících se školení modelu obsahuje podokno **Vlastnosti** tohoto modulu metriku pro určení nejlepší sady parametrů. Má dva různé rozevírací seznamy pro klasifikaci a regresní algoritmy, v uvedeném pořadí. Pokud je zvažovaný algoritmus algoritmus klasifikace, regresní metrika je ignorována a naopak. V tomto konkrétním příkladu je **metrika Přesnost**.   

![Parametry tažení](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Trénování, vyhodnocování a porovnávání
Stejný modul [Tune Model Hyperparameters][tune-model-hyperparameters] trénuje všechny modely, které odpovídají sadě parametrů, vyhodnocuje různé metriky a pak vytvoří nejlépe trénovaný model na základě metriky, kterou zvolíte. Tento modul má dva povinné vstupy:

* Netrénovaný žák
* Soubor údajů

Modul má také volitelný vstup datové sady. Připojte datovou sadu s informacemi o přeložení k povinnému vstupu datové sady. Pokud datové sadě nejsou přiřazeny žádné informace o přeložení, bude ve výchozím nastavení automaticky provedeno 10násobné křížové ověření. Pokud přiřazení přeložení není provedeno a ověřovací datová sada je k dispozici na portu volitelné datové sady, je zvolen režim testu vlaku a první datová sada se použije k trénování modelu pro každou kombinaci parametrů.

![Posílený klasifikátor rozhodovacího stromu](./media/algorithm-parameters-optimize/fig6a.png)

Model je pak vyhodnocen v datové sadě ověření. Levý výstupní port modulu zobrazuje různé metriky jako funkce hodnot parametrů. Správný výstupní port poskytuje trénovaný model, který odpovídá nejvýkonnějšímu modelu podle zvolené**metriky** ( Přesnost v tomto případě).  

![Ověřovací datová sada](./media/algorithm-parameters-optimize/fig6b.png)

Můžete vidět přesné parametry zvolené vizualizací správného výstupního portu. Tento model lze použít při vyhodnocování testovací sady nebo v zprovozněné webové službě po uložení jako trénovaný model.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
