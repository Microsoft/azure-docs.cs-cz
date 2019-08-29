---
title: 'Neuronové síť pro více tříd: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se používat síťový modul neuronové ve službě Azure Machine Learning k vytvoření modelu sítě neuronové, který se dá použít k předpovědi cíle, který má více hodnot.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 44d1e7606efd5bc6d2286254dc4863728e3edbfd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128598"
---
# <a name="multiclass-neural-network-module"></a>Neuronové síťový modul pro více tříd

Tento článek popisuje modul vizuálního rozhraní (Preview) pro službu Azure Machine Learning.

Tento modul použijte k vytvoření neuronové síťového modelu, který se dá použít k předpovědi cíle, který má více hodnot. 

Například neuronové sítě tohoto druhu se můžou používat ve složitých úlohách počítačové vize, jako je například rozpoznávání číslic nebo písmen, klasifikace dokumentů a rozpoznávání vzorků.

Klasifikace pomocí sítě neuronové je metoda učení pod dohledem a proto vyžaduje *tagované datové sady* , která obsahuje sloupec popisku.

Model můžete proškolit poskytnutím modelu a tagované datové sady jako vstupu pro vlakový [model](./train-model.md). K předpovědi hodnot pro nové příklady vstupu je pak možné použít trained model.  

## <a name="about-neural-networks"></a>O sítích neuronové

Neuronové síť je sada vzájemně propojených vrstev. Vstupy jsou první vrstva a jsou připojeny ke výstupní vrstvě pomocí grafu acyklického, který se skládá z váženého okraje a uzlů.

Mezi vstupní a výstupní vrstvou můžete vložit více skrytých vrstev. Většinu prediktivních úloh je možné snadno dosáhnout pouze pomocí jedné nebo několika skrytých vrstev. Poslední výzkum ale ukázal, že rozsáhlé sítě neuronové (DNN) s mnoha vrstvami můžou být efektivní v složitých úlohách, jako je například rozpoznávání obrázků nebo řeči. Po sobě jdoucí vrstvy se používají k modelování rostoucí úrovně sémantické hloubky.

Vztah mezi vstupy a výstupy se od školení sítě neuronové ke vstupním datům dozvěděl. Směr grafu pokračuje ze vstupů přes skrytou vrstvu a do výstupní vrstvy. Všechny uzly ve vrstvě jsou propojeny váženou hranou k uzlům v další vrstvě.

Chcete-li vypočítat výstup sítě pro konkrétní vstup, hodnota je vypočítána na každém uzlu ve skrytých vrstvách a ve výstupní vrstvě. Hodnota je nastavena výpočtem váženého součtu hodnot uzlů z předchozí vrstvy. Na tento vážená suma se pak aplikuje aktivační funkce.

## <a name="configure-multiclass-neural-network"></a>Nakonfigurovat neuronové síť s více třídami

1. Přidejte do experimentu **neuronové síťový** modul pro více tříd, a to v rozhraní. Tento modul můžete najít v části **Machine Learning**, **Initialize**, v kategorii **klasifikace** .

2. **Vytvořit Trainer režim**: Tuto možnost použijte k určení způsobu, jakým chcete model vyškolet:

    - **Jeden parametr**: Tuto možnost vyberte, pokud už víte, jak chcete model nakonfigurovat.

    

3. **Skrytá specifikace vrstvy**: Vyberte typ síťové architektury, která se má vytvořit.

    - **Plně připojený případ**: Tuto možnost vyberte, pokud chcete vytvořit model pomocí výchozí neuronové síťové architektury. Pro modely neuronové sítě s více třídami jsou výchozí hodnoty následující:

        - Jedna skrytá vrstva
        - Výstupní vrstva je plně připojená ke skryté vrstvě.
        - Skrytá vrstva je plně připojená ke vstupní vrstvě.
        - Počet uzlů ve vstupní vrstvě je určený počtem funkcí v školicích datech.
        - Počet uzlů ve skryté vrstvě může nastavit uživatel. Výchozí hodnota je 100.
        - Počet uzlů ve výstupní vrstvě závisí na počtu tříd.
  
   

5. **Počet skrytých uzlů**: Tato možnost umožňuje přizpůsobit počet skrytých uzlů ve výchozí architektuře. Zadejte počet skrytých uzlů. Výchozím nastavením je jedna skrytá vrstva s 100 uzly.

6. **Rychlost učení**: Definuje velikost kroku provedeného v každé iteraci před opravou. Větší hodnota pro studijní kurzy může způsobit, že se model konverguje rychleji, ale může vyhodnotit místní minima.

7. **Počet iterací učení**: Zadejte maximální počet pokusů, kolikrát by měl algoritmus zpracovat školicí případy.

8. **Průměr počátečních vah pro studium**: Určete tloušťky uzlů na začátku procesu učení.

9. **Potenciál**: Zadejte váhu, která se má použít při učení pro uzly z předchozích iterací.
  
11. **Příklady náhodného**: Tuto možnost vyberte, pokud chcete náhodné případy mezi iteracemi.

    Pokud zrušíte výběr této možnosti, jsou případy zpracovávány přesně stejným způsobem pokaždé, když spustíte experiment.

12. **Počáteční číslo prvního čísla**: Zadejte hodnotu, která se má použít jako počáteční hodnota, pokud chcete zajistit opakování v rámci jednoho spuštění stejného experimentu.

14. Připojte datovou sadu školení a jeden z [školicích modulů](module-reference.md): 

    - Pokud nastavíte **režim vytvořit Trainer** na **jeden parametr**, použijete [model výuky](train-model.md).  
  

## <a name="results"></a>Výsledky

Po dokončení školení:

- Pokud chcete zobrazit souhrn parametrů modelu spolu s váhy funkcí zjištěnými ze školení a dalšími parametry sítě neuronové, klikněte pravým tlačítkem na výstup [výukového modelu](./train-model.md) a vyberte **vizualizovat**.  

- Pokud chcete uložit snímek výukového modelu, klikněte pravým tlačítkem na výstup **trained model** a vyberte **Uložit jako trained model**. Tento model není aktualizován při následných spuštěních stejného experimentu.


## <a name="next-steps"></a>Další postup

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 