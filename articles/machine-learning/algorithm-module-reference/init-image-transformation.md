---
title: Transformace obrázku Transformationply init image
titleSuffix: Azure Machine Learning
description: Naučte se používat modul pro transformaci init image v Návrháři Azure Machine Learning k inicializaci transformace obrázku.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: fc0eb196ed24e413c35d64f0571ff29dc3725032
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421273"
---
# <a name="init-image-transformation"></a>Počáteční transformace obrázku

Tento článek popisuje, jak použít modul pro **transformaci init image** v Návrháři Azure Machine Learning, inicializovat transformaci obrázku a určit, jakým způsobem má být obrázek transformován.

## <a name="how-to-configure-init-image-transformation"></a>Jak nakonfigurovat transformaci inicializačních imagí

1.  Přidejte modul pro **transformaci init image** do kanálu v návrháři. 

2.  Pokud chcete **změnit** velikost, určete, jestli se má změnit velikost vstupního pil obrázku na danou velikost. Pokud zvolíte true, můžete zadat velikost požadované velikosti výstupního **obrazu ve výchozím** nastavení 256. 

3.  V části **Vystředit oříznout** určete, jestli se má oříznout daná pil image uprostřed. Pokud zvolíte true (pravda), můžete zadat požadovanou velikost výstupního obrazu oříznutí ve **formátu oříznutí**, ve výchozím nastavení 224.  

4.  V poli pro **panel** určete, zda má být daný pil obrázek zaohraničen na všech stranách s hodnotou panelu 0. Pokud zvolíte true, můžete pro každé ohraničení v **odsazení** zadat odsazení (kolik pixelů se má přidat).

5.  V případě **kolísání barvy** určete, jestli se má náhodně změnit jas, kontrast a sytost obrázku.

6.  V případě **stupňů šedi** určete, zda se má převést obrázek na stupně šedi.

7.  Pro **oříznutí s náhodným zvětšením velikosti** určete, jestli se má oříznout daný obraz pil k náhodné velikosti a poměru stran. Oříznutí náhodné velikosti (rozsah od 0,08 do 1,0) původní velikosti a náhodného poměru stran (rozsah od 3/4 do 4/3) původního poměru stran. Velikost této oříznutí je nakonec změněna na danou velikost.
    Tato možnost se běžně používá při výuce sítě v rámci zahájení. Pokud zvolíte true (pravda), můžete ve 256 výchozím nastavení určovat očekávanou velikost výstupu každého okraje v **náhodné velikosti**.

8.  Pro možnost **náhodný oříznutí** určete, zda má být daná pil image oříznuta v náhodném umístění. Pokud zvolíte true, můžete zadat požadovanou výstupní velikost oříznutí v **náhodné velikosti oříznutí** ve výchozím nastavení 224.

9.  V případě **náhodného vodorovného překlápění** určete, jestli se má náhodně překlopit zadaný obrázek pil s pravděpodobností 0,5.

10.  V případě **náhodného vertikálního překlápění** určete, jestli se má náhodně překlopit zadaný obrázek pil s pravděpodobností 0,5.

11.  Pro možnost **náhodné otočení** určete, zda se má obrázek otočit podle úhlu. Pokud zvolíte true (pravda), můžete zadat v rozsahu stupňů nastavením **stupně náhodného otočení**, což znamená (-Degrees), ve výchozím nastavení 0.

12.  V případě **náhodného spřažení** určete, zda má být při transformaci obrazu z neutrálního středu zachovává transformace. Pokud zvolíte true (pravda), můžete zadat v rozsahu stupňů, ze kterého se mají vybírat v **náhodných stupních spřažení**, což znamená (-Degrees, + stupňů), ve výchozím nastavení 0.

13.  V případě **náhodných stupňů šedi** určete, jestli se má náhodným převodem obrázku na stupně šedi použít pravděpodobnost 0,1.

14.  V případě **náhodné perspektivy** určete, zda má být provedena transformace perspektivy daného obrázku pil náhodně s pravděpodobností 0,5.


16.  Připojte se k použití modulu [transformace obrázků](apply-image-transformation.md) a použijte transformaci určenou výše na vstupní datovou sadu obrázku.

17. Odešlete kanál.

## <a name="results"></a>Výsledky

Po dokončení transformace můžete ve výstupu modulu [transformace obrázku použít transformované](apply-image-transformation.md) obrázky.


## <a name="technical-notes"></a>Technické poznámky  

[https://pytorch.org/docs/stable/torchvision/transforms.html](https://pytorch.org/docs/stable/torchvision/transforms.html)Další informace o transformaci obrázku najdete v tématu.

###  <a name="module-parameters"></a>Parametry modulu  

| Name                    | Rozsah   | Typ    | Výchozí | Description                              |
| ----------------------- | ------- | ------- | ------- | ---------------------------------------- |
| Změna velikosti                  | Všechny     | Logická hodnota | Ano    | Změnit velikost vstupního PIL obrázku na danou velikost |
| Velikost                    | >= 1     | Integer | 256     | Zadejte požadovanou velikost výstupu.          |
| Vycentrovat oříznutí             | Všechny     | Logická hodnota | Ano    | Ořízne daný PIL obrázek ve středu.  |
| Oříznout velikost               | >= 1     | Integer | 224     | Zadejte požadovanou výstupní velikost oříznutí. |
| Pad                     | Všechny     | Logická hodnota | Ne   | Rozsadí daný PIL obrázek na všech stranách s danou hodnotou "pad". |
| Odsazení                 | >= 0     | Integer | 0       | Odsazení u každého ohraničení                   |
| Kolísání barvy            | Všechny     | Logická hodnota | Ne   | Náhodně změnit jas, kontrast a sytost obrázku |
| Stín               | Všechny     | Logická hodnota | Ne   | Převést obrázek na stupně šedi               |
| Oříznutí náhodné velikosti     | Všechny     | Logická hodnota | Ne   | Ořízne daný PIL obrázek k náhodné velikosti a poměru stran. |
| Náhodná velikost             | >= 1     | Integer | 256     | Očekávaná velikost výstupu každého okraje        |
| Náhodný oříznutí             | Všechny     | Logická hodnota | Ne   | Ořízne danou image PIL v náhodném umístění. |
| Velikost náhodné oříznutí        | >= 1     | Integer | 224     | Požadovaná výstupní velikost oříznutí          |
| Náhodné Vodorovné překlopení  | Všechny     | Logická hodnota | Ano    | Překlopit daný PIL obrázek náhodně s určitou pravděpodobností |
| Náhodné vertikální překlopení    | Všechny     | Logická hodnota | Ne   | Překlopí zadaný obrázek PIL náhodně s danou pravděpodobností. |
| Náhodné otočení         | Všechny     | Logická hodnota | Ne   | Otočit obrázek o úhel                |
| Stupně náhodného otočení | [0180] | Integer | 0       | Rozsah stupňů, ze kterého se mají vybírat          |
| Náhodný spřažení           | Všechny     | Logická hodnota | Ne   | Náhodná transformace spřažení fotovariantního středu pro uchování obrazu |
| Náhodné spřažení stupňů   | [0180] | Integer | 0       | Rozsah stupňů, ze kterého se mají vybírat          |
| Náhodné stupně šedé        | Všechny     | Logická hodnota | Ne   | Náhodně převést obrázek na stupně šedi s pravděpodobností 0,1 |
| Náhodný pohled      | Všechny     | Logická hodnota | Ne   | Provede transformaci daného PIL obrázku náhodně s pravděpodobností 0,5 |
| Náhodné mazání          | Všechny     | Logická hodnota | Ne   | Náhodně vybere oblast obdélníku v obrázku a smaže její pixely s pravděpodobností 0,5 |

###  <a name="output"></a>Výstup  

| Název                        | Typ                    | Description                              |
| --------------------------- | ----------------------- | ---------------------------------------- |
| Transformace výstupního obrázku | TransformationDirectory | Transformace výstupního obrázku, kterou lze připojit k **použití modulu transformace obrázku** . |

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 