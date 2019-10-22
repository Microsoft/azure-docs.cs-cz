---
title: 'Neuronové síť se dvěma třídami: odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se používat neuronové síťový modul se dvěma třídami ve službě Azure Machine Learning k vytvoření modelu sítě neuronové, který se dá použít k předpovědi cíle, který má jenom dvě hodnoty.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 8f38a7b7086e5023eb63e94363301ac5277f7e7c
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693597"
---
# <a name="two-class-neural-network-module"></a>Síťový modul neuronové se dvěma třídami

Tento článek popisuje modul vizuálního rozhraní (Preview) pro službu Azure Machine Learning.

Pomocí tohoto modulu můžete vytvořit model sítě neuronové, který se dá použít k předpovědi cíle, který má jenom dvě hodnoty.

Klasifikace pomocí sítě neuronové je metoda učení pod dohledem a proto vyžaduje *tagované datové sady*, která obsahuje sloupec popisku. Tento model sítě neuronové můžete použít například k předpovědi binárních výsledků, jako je například to, zda pacient má určitou chorobu nebo zda je počítač v zadaném časovém období pravděpodobně neúspěšný.  

Po definování modelu můžete ho vytvořit pomocí tagované datové sady a modelu jako vstupu pro [vlakový model](./train-model.md). K předpovědi hodnot pro nové vstupy pak můžete použít trained model.

### <a name="more-about-neural-networks"></a>Další informace o neuronovéch sítích

Neuronové síť je sada vzájemně propojených vrstev. Vstupy jsou první vrstva a jsou připojeny ke výstupní vrstvě pomocí grafu acyklického, který se skládá z váženého okraje a uzlů.

Mezi vstupní a výstupní vrstvou můžete vložit více skrytých vrstev. Většinu prediktivních úloh je možné snadno dosáhnout pouze pomocí jedné nebo několika skrytých vrstev. Poslední výzkum ale ukázal, že rozsáhlé sítě neuronové (DNN) s mnoha vrstvami můžou být efektivní v složitých úlohách, jako je například rozpoznávání obrázků nebo řeči. Po sobě jdoucí vrstvy se používají k modelování rostoucí úrovně sémantické hloubky.

Vztah mezi vstupy a výstupy se od školení sítě neuronové ke vstupním datům dozvěděl. Směr grafu pokračuje ze vstupů přes skrytou vrstvu a do výstupní vrstvy. Všechny uzly ve vrstvě jsou propojeny váženou hranou k uzlům v další vrstvě.

Chcete-li vypočítat výstup sítě pro konkrétní vstup, hodnota je vypočítána na každém uzlu ve skrytých vrstvách a ve výstupní vrstvě. Hodnota je nastavena výpočtem váženého součtu hodnot uzlů z předchozí vrstvy. Na tento vážená suma se pak aplikuje aktivační funkce.
  
## <a name="how-to-configure"></a>Jak nakonfigurovat

1.  Přidejte do svého kanálu **síťový modul neuronové pro dva třídy** . Tento modul můžete najít v části **Machine Learning**, **Initialize**, v kategorii **klasifikace** .  
  
2.  Určete, jak chcete model vyškolet nastavením možnosti **vytvořit Trainer režim** .  
  
    -   **Jeden parametr**: tuto možnost vyberte, pokud už víte, jak chcete model nakonfigurovat.  

3.  V případě **specifikace skryté vrstvy**vyberte typ síťové architektury, která se má vytvořit.  
  
    -   **Plně připojený případ**: používá výchozí neuronové síťovou architekturu definovanou pro neuronové sítě se dvěma třídami, jak je znázorněno níže:
  
        -   Má jednu skrytou vrstvu.
  
        -   Výstupní vrstva je plně připojená ke skryté vrstvě a skrytá vrstva je plně připojená ke vstupní vrstvě.
  
        -   Počet uzlů ve vstupní vrstvě se rovná počtu funkcí v školicích datech.
  
        -   Počet uzlů ve skryté vrstvě je nastaven uživatelem. Výchozí hodnota je 100.
  
        -   Počet uzlů se rovná počtu tříd. Pro neuronové síť se dvěma třídami to znamená, že všechny vstupy musí být namapovány na jeden ze dvou uzlů ve výstupní vrstvě.

5.  V případě **studijních kurzů**definujte velikost kroku provedeného v každé iteraci před opravou. Větší hodnota pro studijní kurzy může způsobit, že se model konverguje rychleji, ale může vyhodnotit místní minima.

6.  Pro **počet iterací učení**určete maximální počet pokusů, kolikrát by měl algoritmus zpracovat školicí případy.

7.  Pro **průměr počátečních vah pro studium**určete tloušťku uzlů na začátku procesu učení.

8.  Pro **potenciál**zadejte váhu, která se má použít při učení na uzly z předchozích iterací.  

10. Vyberte možnost **náhodné příklady** pro náhodné případy mezi iteracemi. Pokud zrušíte výběr této možnosti, jsou případy zpracovávány přesně stejným způsobem pokaždé, když spustíte kanál.
  
11. Pro **počáteční číslo semen**zadejte hodnotu, která se má použít jako počáteční hodnota.
  
     Zadání počáteční hodnoty je užitečné, pokud chcete zajistit opakování v rámci spuštění stejného kanálu.  Jinak se jako počáteční hodnota použije systémová časová hodnota, která může při každém spuštění kanálu způsobit mírně odlišné výsledky.
  
13. Přidejte do kanálu příznakovou datovou sadu a propojte jeden ze [školicích modulů](module-reference.md).  
  
    -   Pokud nastavíte **režim vytvořit Trainer** na **jeden parametr**, použijte modul [vlakového modelu](train-model.md) .  
  
14. Spuštění kanálu

## <a name="results"></a>Výsledky

Po dokončení školení:

+ Chcete-li zobrazit souhrn parametrů modelu spolu s váhy funkcí zjištěnými ze školení a dalšími parametry sítě neuronové, klikněte pravým tlačítkem na výstup [výukového modelu](./train-model.md)a vyberte **vizualizovat**.  

+ Pokud chcete uložit snímek výukového modelu, klikněte pravým tlačítkem na výstup **trained model** a vyberte **Uložit jako trained model**. Tento model se neaktualizuje při následných spuštěních stejného kanálu.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 
