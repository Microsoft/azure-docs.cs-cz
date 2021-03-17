---
title: 'Regrese sítě neuronové: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se, jak pomocí modulu neuronové Network regrese v Azure Machine Learning vytvořit regresní model pomocí přizpůsobitelného síťového algoritmu neuronové.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 403576454615effeb53651b51679681422b08e9e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90890362"
---
# <a name="neural-network-regression-module"></a>Neuronové Network regrese – modul

*Vytvoří regresní model pomocí neuronové síťového algoritmu.*  
  
 Kategorie: Machine Learning/inicializovat model/regrese
  
## <a name="module-overview"></a>Přehled modulu  

Tento článek popisuje modul v Návrháři Azure Machine Learning.

Tento modul použijte k vytvoření regresního modelu pomocí přizpůsobitelného neuronové síťového algoritmu.
  
 I když se neuronové sítě často označují pro použití v obsáhlém učení a modelování složitých problémů, jako je rozpoznávání obrázků, jsou snadno přizpůsobené regresním problémům. Každá třída statistických modelů může být označována jako neuronovéá síť, pokud používá adaptivní hmotnosti a může přibližné nelineární funkce jejich vstupů. Proto je regrese sítě neuronové vhodná pro problémy, které nemůžou přizpůsobit model s větším tradičním regresí.
  
 Regrese sítě neuronové je metoda učení pod dohledem a proto vyžaduje *tagované datové sady*, která obsahuje sloupec popisku. Vzhledem k tomu, že regresní model předpovídá číselnou hodnotu, sloupec popisku musí být číselný datový typ.  
  
 Model můžete proškolit poskytnutím modelu a tagované datové sady jako vstupu pro [vlakový model](./train-model.md). K předpovědi hodnot pro nové příklady vstupu je pak možné použít trained model.  
  
## <a name="configure-neural-network-regression"></a>Konfigurace regrese sítě neuronové 

Neuronové sítě je možné výrazně přizpůsobit. Tato část popisuje, jak vytvořit model pomocí dvou metod:
  
+ [Vytvoření modelu sítě neuronové pomocí výchozí architektury](#bkmk_DefaultArchitecture)  
  
    Pokud přijmete výchozí neuronové architekturu sítě, použijte podokno **vlastnosti** k nastavení parametrů, které řídí chování sítě neuronové, jako je třeba počet uzlů ve skryté vrstvě, rychlost učení a normalizace.

    Začněte zde, pokud s neuronovéými sítěmi začínáte. Tento modul podporuje mnoho úprav a také optimalizaci modelu bez důkladné znalosti neuronové sítí. 

+ Definování vlastní architektury pro neuronové síť 

    Tuto možnost použijte, pokud chcete přidat další skryté vrstvy nebo plně přizpůsobit síťové architektury, její připojení a funkce aktivace.
    
    Tato možnost je nejlepší, pokud už se neuronové sítě už trochu obeznámené. K definování síťové architektury použijte jazyk NET #.  

##  <a name="create-a-neural-network-model-using-the-default-architecture"></a><a name="bkmk_DefaultArchitecture"></a> Vytvoření modelu sítě neuronové pomocí výchozí architektury

1.  Přidejte do kanálu modul **regrese sítě neuronové** v návrháři. Tento modul najdete v části **Machine Learning**, **Initialize**, v kategorii **regrese** . 
  
2. Určete, jak chcete, aby byl model vyškolený, nastavením možnosti **vytvořit Trainer režim** .  
  
    -   **Jeden parametr**: tuto možnost vyberte, pokud už víte, jak chcete model nakonfigurovat.

    -   **Rozsah parametrů**: tuto možnost vyberte, pokud si nejste jisti nejlepšími parametry a chcete spustit mazání parametrů. Vyberte rozsah hodnot, na které se mají iterovat, a s [parametry modelu ladění](tune-model-hyperparameters.md) prochází všechny možné kombinace nastavení, které jste zadali, abyste určili, jaké parametry jsou výsledkem optimálních výsledků.   

3.  V případě **specifikace skryté vrstvy**vyberte možnost **plně připojené**. Tato možnost vytvoří model pomocí výchozí síťové architektury neuronové, která je pro model regrese sítě neuronové, má tyto atributy:  
  
    + Síť obsahuje přesně jednu skrytou vrstvu.
    + Výstupní vrstva je plně připojená ke skryté vrstvě a skrytá vrstva je plně připojená ke vstupní vrstvě.
    + Počet uzlů ve skryté vrstvě může nastavit uživatel (výchozí hodnota je 100).  
  
    Vzhledem k tomu, že počet uzlů ve vstupní vrstvě je určen počtem funkcí v školicích datech, v regresním modelu může být ve výstupní vrstvě pouze jeden uzel.  
  
4. Pro **Počet skrytých uzlů**zadejte počet skrytých uzlů. Výchozím nastavením je jedna skrytá vrstva s 100 uzly. (Tato možnost není k dispozici, pokud definujete vlastní architekturu pomocí .NET #.)
  
5.  Do pole **rychlost učení**zadejte hodnotu, která definuje krok prováděný v každé iteraci před opravou. Větší hodnota pro studijní kurzy může způsobit, že se model konverguje rychleji, ale může vyhodnotit místní minima.

6.  Pro **počet iterací učení**určete maximální počet pokusů, kolikrát algoritmus zpracovává školicí případy.


8.  Pro **potenciál**zadejte hodnotu, která se má použít během učení, jako váha na uzlech z předchozích iterací.

10. Pokud chcete změnit pořadí případů mezi iteracemi, vyberte možnost, která bude náhodně vymezit **Příklady**. Pokud zrušíte výběr této možnosti, jsou případy zpracovávány přesně stejným způsobem pokaždé, když spustíte kanál.
  
11. V případě použití **náhodného čísla**můžete volitelně zadat hodnotu, která se má použít jako počáteční hodnota. Zadání počáteční hodnoty je užitečné, pokud chcete zajistit opakování v rámci spuštění stejného kanálu.
  
13. Připojte školicí datovou sadu a výuku modelu:

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

- Pokud chcete uložit snímek výukového modelu, vyberte kartu **výstupy** na pravém panelu modulu **výuka modelu** . Výběrem ikony **Registrovat datovou sadu** uložte model jako opakovaně použitelný modul.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 