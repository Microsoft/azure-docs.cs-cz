---
title: Pokyny pro efektivní ukotvení prostředí v Azure prostorových kotvy | Dokumentace Microsoftu
description: Pokyny a důležité informace pro vytváření prostředků a umístění ukotvení efektivně s Azure prostorových ukotvení.
author: mattwojo
manager: jken
services: azure-spatial-anchors
ms.author: mattwoj
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 196958e4818251bd7f2ee78ca472e6f28292d908
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752987"
---
# <a name="guidelines-for-an-effective-anchor-experience-with-azure-spatial-anchors"></a>Pokyny pro efektivní ukotvení zkušeností s Azure prostorových kotvy

Tento článek obsahuje pokyny a důležité informace pro vytváření prostředků a umístění ukotvení efektivně s Azure prostorových ukotvení.

## <a name="creating-anchors"></a>Vytváření kotvy

Vytvoření dobré kotvy je jedním z aspektů práce s Azure prostorových kotvy jejich činnost nejvíc ohrožují. Je potřeba investovat čas ve vzdělávání nebo vedení jak dobré ukotvení vytvářet uživatele vaše uživatelské prostředí. Investujete do ještě před zahájením vytváření ukotvení, povolit koncovým uživatelům spolehlivě vyhledejte kotev vztahů:

- V různých zařízeních
- V různých časech.
- Na různé podmínky
- Z požadovaného perspektiv v prostoru
- Atd.

Níže jsou uvedeny důležité informace a pokyny můžete navrhnout vaše prostředí pro vytváření a umístění ukotvení.

## <a name="static-and-dynamic-locations"></a>Statické a dynamické umístění

Součástí návrhu ukotvení prostředí bude vyberete umístění, které se účastní. Bude umístění statická a definovaná správcem prostoru? Nebo se být dynamická a uživatelem definovanými?

Prodejní manažer úložiště může být vhodné statické prostředí ve storu k přesvědčit uživatelé navštívit. Zatímco vývojář desková hra pro hybridní realitu pravděpodobně chce umožňuje uživatelům zvolit umístění, kam chcete přehrát.

Pro statické umístění můžete říct správci a ztrácet čas Správa prostor s dobrým ukotvení.

Pro dynamické umístění byste uvažovat o tom, jak říct nebo vedou uživatele vaše uživatelské prostředí na dobrý ukotvení vytvářet.

## <a name="stable-visual-features"></a>Stabilní vizuální funkce

Vizuální sledování systémy používané ve smíšené reality a rozšířené realitě závisí na vizuální funkce prostředí. Pokud chcete získat nejspolehlivější prostředí:  

- Ukotvení vytvořte v umístěních s stabilní vizuální funkce (to znamená, funkce, které se nemění často).

- Nevytvářejte kotvy na velkých prázdné plochy s vlastnostmi není jedinečný.

- Nevytvářejte kotvy na vysoce odrazivý materiály.

- Nevytvářejte kotvy na površích, kde zopakujete pouze vzor, například carpet nebo tapetu.

![Příklad obrázků představující dobré a špatné prostředí](./media/stable-visual.png)

## <a name="consider-various-viewing-perspectives"></a>Vezměte v úvahu různá zobrazení perspektivy

Při vytváření anchor, vezměte osoby, které budou navštivte později k vyhledání ukotvení.

Zvažte například ukotvení uprostřed místnosti se dvěma. Pravděpodobně chcete povolit uživatele, kteří vstoupit do místnosti z obou dveře k vyhledání ukotvení. Při vytváření ukotvení, je potřeba zkontrolovat jeho pozice z obou dveří. To zaznamená data o prostředí kolem ukotvení z obou perspektivy, tak, aby uživatelé mohli najít ukotvení z obou dvířka knihovny.

Obecně platí při vytváření anchor, to by se měl zkontrolovat z různých míst nebo perspektivy, že očekáváte, že uživatelům se, abyste se při pokusu o ho najít.  

Při umísťování virtuální obsah na venkovní socha, má smysl procesem kolem sochy při prohlížení, jako je vytváření ukotvení.  

Na druhé straně Pokud vaše ukotvení v horním rohu místnosti, je pouze jeden směr, abyste ho. Při vytváření ukotvení, jednoduše potřebuje ke kontrole z tohoto hlediska.

## <a name="multiple-anchors"></a>Více kotvy

Osvětlení mohou přinést důležité ve vizuální funkce, které jsou zjištěny. Kotvy vytvořené v silné přirozené světlo může být obtížné najít po tmavé pod umělý osvětlení a naopak.  

Pokud se vyskytne tento problém, může pomoct vytvořit dvě kotvy vztahů – jednu v denního světla a další v rámci umělé osvětlení – na stejném místě. Aplikace pak vyhledávat obou ukotvení. Pokud se buď nachází, aplikace bude mít pozice pro ukotvení. 

Podobně v prostředích, kde vizuální funkce změnit, protože většina objektů přesunout, může pomoct více ukotvení. Jakmile ukotvení příliš obtížné vyhledat z důvodu významné změny v prostředí, můžete nahradit ho novým. Případech to může být například v maloobchodě, kde rozložení se aktualizují každých několik měsíců.

## <a name="targets-and-rooms"></a>Cíle a místnosti

V mnoha případech umístění ukotvení představuje vstupní bod jedinečné prostředí vaší aplikace. Budete chtít získat prostřednictvím tohoto kroku rychle a spolehlivě tak uživatelům můžete zadat prostředí. Výdaje čas na způsobu, jakým se uživatelé hledají vaše kotev vztahů je důležité návrh krok. Je vhodné uvažovat o to z hlediska dva různé scénáře: **Cíle** a **místnosti**.

### <a name="targets"></a>Cíle

![Začněte tady úpravy](./media/start-here-edit.png)

Ve scénáři cílové umístění ukotvení je dobře známá. Například ve fiktivní aplikaci Malování MR umístí jeden uživatel virtuální plátna na zdi. Marcela nastaví ostatní uživatelé v místnosti tak, aby odkazoval na jednom místě na zeď vyhledejte ukotvení a začněte prostředí svoje zařízení.  

Dalším příkladem scénáře cíl mohou být přihlášení s oznámením "vyhledat obchody" při čekání na řádku v kavárně. Dříve ukotvení kavárny nebo umístil tady. Jak uživatelé kontrolovat znaménko, vyhledejte ukotvení a zadejte AR prostředí pro nabídky na kávu.

Scénář, cíl může pomoct fotografie. Pokud uživatelé můžete zobrazit na svých zařízeních na určené fotografii, se můžete rychle identifikovat co kontrolovat v reálném světě. Například může pomoct vaši uživatelé přicházejí v rámci obecné okolí určené pomocí GPS. Jakmile uživatel doručení, vaše aplikace zobrazuje fotografii cíle. Uživatel vypadá kolem prostor, vyhledá cíl a pokračuje vyhledání ukotvení.

### <a name="rooms"></a>místnosti

![Kontrola místa](./media/scan-room.png)

Uživatelé v místnosti scénář, zadejte mezeru jednoduše vědomím, že tady ukotvení je někde. Uživatelé zkontrolovat prostor pomocí jejich zařízení a rychle vyhledat ukotvení.

Dosažení tohoto prostředí obvykle vyžaduje vytvoření dobře kurátorované ukotvení, jak je popsáno v [vezměte v úvahu různá zobrazení perspektivy](#consider-various-viewing-perspectives) dříve. Pokud místnosti byla provedena z mnoho pohledů na problematiku při vytváření ukotvení, pak uživatelé můžete naskenovat skoro kdekoli při pokusu o nalezení ho.

V podstatě, kdo vytváří ukotvení stráví více času kontrolu místa, osoby, které se později mohli kontrolovat a vyhledejte ukotvení rychle. To je důležité kompromis, bude nutné vzít v úvahu pro vaše prostředí.

Malování aplikace MR jsme probírali výše je příklad, který není vhodné scénář a místa. Tady chce uživatel umístění ukotvení ostatním uživatelům rychle připojit k prostředí. Uživatelé nechtějí čekat místnosti je dobře naskenované, aby bylo možné spustit prostředí. Vzhledem k tomu, že všichni uživatelé víte přesně, kde tak, aby odkazoval na jejich zařízení najít ukotvení, tento scénář je lépe považováno za cílové scénáře.

## <a name="effectively-locating-anchors"></a>Efektivní umístění ukotvení

Vizuální sledování systémy využívají vizuální funkce prostředí pro funkce. Víc vizuální funkce jsou součástí kontrolu, tím pravděpodobněji budou hledání ukotvení.

Existují některé obecné pokyny, které můžete použít k sestavení a činnost koncových uživatelů, která vývojářům umožňuje kontrolu užitečné prostředí.

Nejprve Pokud uživatel není během pár sekund vyhledat anchor, aplikace by měly podporovat uživatelům přesunovat zařízení tak, aby další perspektivy jsou zachyceny.  Aplikace může také podporovat samotných uživatelů přesouvat prostředí při hledání ukotvení z další perspektivy. Další perspektivy, ze kterých se zařízení zobrazí stejné body tím lépe.

Pro cílové scénáře požádejte uživatele, aby pohyb cíl, všechny při zobrazení z různých perspektiv. Jinými slovy požádejte uživatele, který chcete přesunout svoje stopy zachycení cíle z nové perspektivy, dokud ukotvení se nachází.

Pro scénáře místnosti požádejte uživatele, aby pomalu kontrolovat místnosti. Třeba požádejte uživatele, aby otočit zachycení 180 nebo dokonce 360 stupňů místnosti. Nebo můžete požádat uživatele, přesunout do nové perspektivy v místnosti. Smysluplných způsob vyhledávání je kontrola v místnosti. To zachycuje další vizuální funkce prostředí než Řekněme, vyhledávání blízkých zdi. Vyhledávání blízkých wall nezachytává jako velmi užitečné funkce prostředí sady visual.

Není vhodné přejít na straně na straně zařízení pořád dokola, když hledáte ukotvení. To by jednoduše zachytit stejné body z hlediska stejné.

## <a name="testing-the-experience"></a>Testování prostředí

Výše jsou obecné pokyny. S Azure prostorových ukotvení vytváření aplikací, které pracují s reálného světa. V důsledku toho je potřeba věnovat čas testování vaší aplikace ukotvení scénáře ve skutečných prostředích. To platí zejména pro prostředí, které jsou reprezentativní kde očekáváte, že uživatelům k používání aplikace.