---
title: 'Víc tříd logistické regrese: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu Multiclass logistické Regrese ve službě Azure Machine Learning vytvořte Logistický regresní model, který můžete použít k předvídání více hodnot.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ac4310e851808d6e6d89d1a2b506975eea3b1d69
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029322"
---
# <a name="multiclass-logistic-regression-module"></a>Víc tříd modulu logistické regrese

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k vytvoření modelu logistické regrese, který můžete použít k předvídání více hodnot.

Klasifikace pomocí logistické regrese je metoda učení a proto vyžaduje datové sady s popiskem. Trénování modelu tím, že poskytuje model a s popiskem datovou sadu jako vstup do modulu jako [trénování modelu](./train-model.md). Trénovaného modelu lze pak použít k předpovědi hodnot pro nový vstupní příklady.

Také poskytuje služba Azure Machine Learning [Two-Class logistické regrese](./two-class-logistic-regression.md) modul, který je vhodný pro klasifikaci proměnných dichotomous nebo binary.

## <a name="about-multiclass-logistic-regression"></a>O víc tříd logistické regrese

Logistické regrese se známá metoda statistiky, se používá k předpovědi pravděpodobnosti jako výsledek, který se používá pro úlohy klasifikace. Algoritmus předpovídá pravděpodobnost výskytu události montáží data logistické funkci. 

V víc tříd logistické regrese třídění lze použít k předvídání více výsledků.

## <a name="configure-a-multiclass-logistic-regression"></a>Konfigurace víc tříd logistické regrese

1. Přidat **logistické regrese Multiclass** modulů na experiment.

2. Určení způsobu modelu zaškolení, tak, že nastavíte **režimu vytváření trainer** možnost.

    + **Jeden parametr**: Tuto možnost použijte, pokud víte, jak chcete konfigurovat modelu a poskytnout konkrétní sadu hodnot jako argumenty.

    + **Parametr rozsahu**: Tuto možnost použijte, pokud nejste jisti nejlepších parametrů a chcete použít Uklízení parametrů.

3. **Optimalizace tolerance**, zadejte požadovanou prahovou hodnotu pro konvergence optimalizace. Pokud zlepšení mezi iteracemi je menší než prahová hodnota, použije algoritmus se zastaví a vrátí aktuální model.

4. **Váha regularizace L1**, **L2 regularizace váha**: Zadejte hodnotu pro parametry regularizace L1 a L2. Nenulové hodnoty se doporučuje pro obojí.

    Regularizace je metoda kvůli overfitting ukládání sankcí modely s koeficient extrémní hodnoty. Regularizace funguje tak, že přidáte sankce, který je spojen s hodnotami koeficient chybu hypotézu. Přesný model s extrémně koeficient hodnoty by penalizován víc, ale méně přesný model s konzervativnější hodnoty by penalizován méně.

     L1 a míry regularizace L2 mají různé účinky a používá. L1 lze použít k modelům zhuštěný, což je užitečné při práci s vysokým počtem rozměrů data. Naproti tomu regularizace L2 je vhodnější pro data, která není zhuštěné.  Tento algoritmus podporuje lineární kombinace hodnot regularizace L1 a L2: to znamená, pokud `x = L1` a `y = L2`, `ax + by = c` definuje lineární rozsah podmínky regularizace.

     Různé kombinace lineární L1 a L2 podmínek se našli pro logistické regresní modely, jako například [elastické net regularizace](https://wikipedia.org/wiki/Elastic_net_regularization).

6. **Náhodné číslo**: Zadejte celočíselnou hodnotu má použít jako počáteční hodnotu pro tento algoritmus, pokud chcete výsledky bude opakovatelné přes spuštění. V opačném případě hodnota hodiny systému slouží jako počáteční hodnoty, které můžete vytvářet mírně odlišné výsledky v spuštění tom stejném experimentu.

8. Připojte s popiskem datovou sadu a jeden z modulů trénování:

    + Pokud nastavíte **režimu vytváření trainer** k **jediný parametr**, použijte [trénování modelu](./train-model.md) modulu.

9. Spusťte experiment.

## <a name="results"></a>Výsledky

Po dokončení školení můžete prohlédnout souhrnné informace o modelu parametry, společně s funkcí váhy získané při školení, klikněte pravým tlačítkem na výstupu [Train Model](./train-model.md) modul a vyberte **vizualizovat**.


## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 