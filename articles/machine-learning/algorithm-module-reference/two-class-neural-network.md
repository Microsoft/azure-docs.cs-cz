---
title: 'Two-Class neuronové Network: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se, jak pomocí síťového modulu Two-Class neuronové v Azure Machine Learning vytvořit model sítě neuronové, který se dá použít k předpovědi cíle, který má jenom dvě hodnoty.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 2ad4fc575a6e9d07e4e24c2d570f68edbbea46c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907686"
---
# <a name="two-class-neural-network-module"></a>Two-Class síťový modul neuronové

Tento článek popisuje modul v Návrháři Azure Machine Learning.

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

    -   **Rozsah parametrů**: Pokud si nejste jisti nejlepšími parametry, můžete najít optimální parametry pomocí modulu [ladit model parametrů](tune-model-hyperparameters.md) . Poskytnete určitou škálu hodnot a Trainer iterovat více kombinací nastavení a určí kombinaci hodnot, které vytvářejí nejlepší výsledek.  

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
  
13. Přidejte do kanálu datovou sadu s popiskem a vytvořte si model:

    + Pokud nastavíte **režim vytvořit Trainer** na **jeden parametr**, připojíte tagované datové sady a modul [vlakového modelu](train-model.md) .  
  
    + Pokud nastavíte **režim vytvořit Trainer** na **rozsah parametrů**, připojíte s příznakovou datovou sadu a provedete model pomocí [předparametrů ladit model](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Pokud předáte rozsah parametru pro [vlakový model](train-model.md), použije se jenom výchozí hodnota v seznamu jednoho parametru.  
    > 
    > Pokud předáte jednu sadu hodnot parametrů do modulu [předparametrů modelu ladění](tune-model-hyperparameters.md) , když očekává rozsah nastavení pro každý parametr, ignoruje hodnoty a použije výchozí hodnoty pro učit se.  
    > 
    > Pokud vyberete možnost **rozsahu parametrů** a zadáte jednu hodnotu pro libovolný parametr, tato jediná hodnota, kterou jste zadali, se použije v celém oblouku, a to i v případě, že se jiné parametry mění v rozsahu hodnot.  
  
14. Odešlete kanál.

## <a name="results"></a>Výsledky

Po dokončení školení:

+ Pokud chcete uložit snímek výukového modelu, vyberte kartu **výstupy** na pravém panelu modulu **výuka modelu** . Výběrem ikony **Registrovat datovou sadu** uložte model jako opakovaně použitelný modul.

+ Chcete-li použít model pro bodování, přidejte modul určení **skóre modelu** do kanálu.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
