---
title: Pokyny pro efektivní ukotvení prostředí pro, které používají Azure prostorových kotvy | Dokumentace Microsoftu
description: Pokyny a důležité informace o vytvoření a vyhledejte kotvy efektivně pomocí prostorových kotvy Azure.
author: mattwojo
manager: jken
services: azure-spatial-anchors
ms.author: mattwoj
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 4714ea7aa4bf55f7cbd4500977b09505788233dd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "57895800"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Vytvoření prostředí efektivní ukotvení pomocí prostorových kotev vztahů Azure

Tento článek obsahuje pokyny a aspekty, které vám pomůžou účinně vytvářet a vyhledejte ukotvení pomocí prostorových ukotvení.

## <a name="good-anchors"></a>Dobré kotvy

Prostorové kotvy vám pomůže vytvořit dobré ukotvení. Je potřeba investovat čas ve vzdělávání nebo která bude obsahovat uživatele ve vaší uživatelské rozhraní (UX) k vytvoření dobré kotvy vztahů. Investujete do vytváření dobré kotvy ještě před zahájením, pomoct koncovým uživatelům se spolehlivě najít kotev vztahů:

- V různých zařízeních.
- V různých časech.
- V různé podmínky.
- Z požadovaného perspektiv v prostoru.

## <a name="static-and-dynamic-locations"></a>Statické a dynamické umístění

Část návrhu prostředí ukotvení je volba umístění. Bude umístění statická a definovaná správcem prostoru? Nebo se být dynamická a uživatelem definovaných?

Prodejní manažer úložiště může být vhodné statické prostředí ve storu k přesvědčit uživatelé navštívit. Ale pravděpodobně chtít umožnit uživatelům zvolit umístění, kam chcete přehrát developer desková hra realitu.

Pro statické umístění naučíte správci a ztrácet čas Správa prostor s dobrým ukotvení.

Pro dynamické umístění byste uvažovat o jak, které učíte nebo vedou uživatele vaše uživatelské prostředí na dobrý ukotvení vytvářet.

## <a name="stable-visual-features"></a>Stabilní vizuální funkce

Používat vizuální sledování systémy v realitu a rozšířené reality zařízení využívají visual funkce prostředí. Pokud chcete získat nejspolehlivější prostředí:  

- *Proveďte* ukotvení vytvářet v lokalitách, které nemají stabilní vizuální funkce (to znamená, funkce, které se nemění často).

- *Není* ukotvení vytvářet na velkých prázdné plochy, které mají žádné rozlišovací charakteristiky.

- *Není* ukotvení vytvářet vysoce odrazivý materiálů.

- *Není* ukotvení vytvářet na površích, kde vzor opakovat, jako je například carpet nebo tapetu.

![Příklady dobré prostředí pro ukotvení a špatné prostředí ukotvení](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Různá zobrazení perspektivy

Při vytváření anchor, představte si, kdo se později pokusí vyhledat ukotvení.

Zvažte například ukotvení uprostřed prostor, který má dvě možnosti, aby. Pravděpodobně chcete povolit uživatelům z obou dveře vstoupit do místnosti. Při vytváření ukotvení, budete muset zkontrolovat jeho pozice z obou dveří. Můžete změnit perspektivy k zaznamenání dat prostředí kolem ukotvení tak, aby uživatelé mohli najít ukotvení z obou dvířka knihovny.

Obecně platí při vytváření anchor, ho naskenujte z perspektivy uživatelů, kteří se pokusí najít. Proto při umísťování virtuální obsah na venkovní socha, má smysl procesem kolem socha, při hledání, jako je vytváření ukotvení. Pokud vaše ukotvení v horním rohu místnosti, je pouze jeden směr, abyste ho. Při vytváření tohoto ukotvení, můžete ho naskenovat právě z tohoto hlediska.

## <a name="multiple-anchors"></a>Více kotvy

Osvětlení mohou přinést visual funkcí, které aplikace rozpozná. Kotvy vytvořené v silné přirozené světlo může být obtížné najít umělé světla a naopak.  

Pokud máte potíže, může pomoct vytvořit dvě kotvy vztahů. Na stejném místě vytvořte jeden ukotvení v denního světla a druhý v umělé světla. Aplikace pak vyhledávat obou ukotvení. Pokud se nachází buď ukotvení, aplikace bude mít pozice pro ukotvení. 

Podobně v prostředích, kde vizuální funkce změnit, protože většina objektů přesunout, může pomoct více ukotvení. Jakmile ukotvení příliš obtížné vyhledat z důvodu významné změny v prostředí, můžete nahradit novou ukotvení. Může to provedete, například v maloobchodě, kde rozložení se aktualizují každých několik měsíců.

## <a name="targets-and-rooms"></a>Cíle a místnosti

V mnoha případech je ukotvení vstupní bod do prostředí vaší aplikace. Budete chtít získat prostřednictvím tohoto kroku rychle a spolehlivě tak uživatelům můžete zadat prostředí. Výdaje čas na způsobu, jakým se uživatelé hledají vaše kotev vztahů je důležité návrh krok. Je vhodné uvažovat o vyhledání kotvy z hlediska dva různé scénáře: *cíle* a *místnosti*.

### <a name="targets"></a>Cíle

Ve scénáři cílové umístění ukotvení je dobře známá. Například v aplikaci pro fiktivní Malování smíšené realitě, umístí jeden uživatel virtuální plátna na zdi. Marcela nastaví ostatní uživatelé v místnosti tak, aby odkazoval na jednom místě na zeď vyhledejte ukotvení a začněte prostředí svoje zařízení.  

Dalším příkladem scénáře cíl mohou být přihlášení kavárny, který čte, "Vyhledat obchody." Kavárny nebo byl umístěn zde ukotvení. Jak uživatelé kontrolovat znaménko, vyhledejte ukotvení a zadejte možnosti rozšířené reality Najít obchody na kávu.

Scénář, cíl může pomoct fotografie. Je-li zobrazit uživatele fotku na určené na svém zařízení, můžete rychle identifikovat co kontrolovat v reálném světě. Například může pomoct vaši uživatelé přicházejí v rámci obecné oblasti zamýšleným cílem pomocí GPS. Po přijetí uživateli, vaše aplikace zobrazuje fotografii cíle. Uživatel vypadá kolem prostor, vyhledá cíl a hledá ukotvení.

![Obrázek anchor, zobrazení fotografií cíle mobilních zařízení uživatele](./media/start-here-edit.png)

### <a name="rooms"></a>místnosti

Uživatelé v místnosti scénář, zadejte mezeru jednoduše vědomím, že tady ukotvení je někde. Uživatelé zkontrolovat prostor pomocí jejich zařízení a rychle vyhledat ukotvení.

Toto prostředí obvykle vyžaduje, abyste dobře kurátorované ukotvení vytvářet jak je popsáno v [různých perspektiv zobrazení](#consider-various-viewing-perspectives). Pokud jste místo z mnoho pohledů na problematiku při vytváření ukotvení, uživatelé můžete naskenovat skoro kdekoli, při pokusu o ho najít.

![Obrázek jak uživatele můžete naskenovat místo najít ukotvení](./media/scan-room.png)

V podstatě můžete věnovat víc času skenování prostor při vytváření ukotvení tak, aby novější uživatelů můžete prohledávat a rychle vyhledejte ukotvení. Při vytváření prostředí, musíte vzít v úvahu tato důležité kompromis.

Příklad realitu vykreslovací aplikace, kterou jsme probírali dříve nefunguje dobře jako scénář místnosti. Tady chce uživatel zadávající ukotvení ostatním uživatelům rychle připojit k prostředí. Uživatelé nechtějí čekat na spuštění prostředí, dokud dobře kontrole místnosti. Vzhledem k tomu, že všichni uživatelé víte přesně, kde tak, aby odkazoval na jejich zařízení najít ukotvení, tento příklad funguje lépe jako cílové scénáře.

## <a name="anchor-location"></a>Umístění ukotvení

Vizuální sledování systémy využívají vizuální funkce v prostředí. Víc vizuální funkce, které zahrnuje kontrolu, tím pravděpodobněji budou hledání ukotvení.

Postupujte podle pokynů v této části k vytvoření uživatelského prostředí, která vývojářům umožňuje kontrolu užitečné prostředí na obecné.

Nejprve Pokud uživatel není během pár sekund vyhledat anchor, aplikace by měly podporovat uživatelům přesunovat zařízení zachytit další perspektivy. Aplikace může také Vyzvěte uživatele, aby sami pohyb prostředí tak, aby kontrolovala ukotvení z další perspektivy. Další funkce perspektivy, které se zobrazí zařízení, tím lépe.

Cílové scénáře požádejte uživatele přesouvat cíl, chcete-li zobrazit z různých perspektiv. Jinými slovy požádejte uživatele o zachycení cíle z nové perspektivy, dokud nebude nalezen ukotvení.

Pro scénáře místnosti požádejte uživatele, aby pomalu kontrolovat místnosti. Třeba požádejte uživatele, chcete-li k zaznamenání 180stupňový rozsah s orientací nebo dokonce 360 stupňů místnosti. Nebo můžete požádat uživatele, chcete-li zobrazit místnosti z nové perspektivy. 

Smysluplných metodou je kontrola v místnosti. Kontrola v místnosti zaznamená další vizuální funkce prostředí než prohledávání blízké wall, například. Kontrola blízké stěny nebude zachytávat jako mnoho užitečných funkcí visual prostředí.

Není vhodné opakovaně přesunout zařízení ze strany na stranu při hledání ukotvení. To zachycuje jednoduše stejné body z hlediska stejné.

## <a name="experience-tests"></a>Prostředí pro testy

V tomto článku jsme probrali obecné pokyny. S prostorových kotvy psaní aplikací, které umožňují interakci s reálným světem. Proto by měl čas věnovat v testování vaší aplikace ukotvení scénáře ve skutečných prostředích. To platí zejména pro prostředí, které představují kde očekáváte, že vaši uživatelé k používání aplikace.