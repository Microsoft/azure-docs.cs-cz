---
title: Odebrání prostředků z kolekce přesunů v nástroji Azure Resource stěhovací
description: Naučte se, jak odebrat prostředky z kolekce přesunů v nástroji Azure Resource stěhovací.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 11/30/2020
ms.author: raynew
ms.openlocfilehash: 63548e2bf470c012e0dd8a5f879a51eeb631f453
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459279"
---
# <a name="manage-move-collections-and-resource-groups"></a>Správa přesunu kolekcí a skupin prostředků

Tento článek popisuje, jak odebrat prostředky z kolekce přesunutí nebo odebrat kolekci nebo skupinu prostředků v [Azure Resource stěhovací](overview.md). Přesun kolekcí se používá při přesunu prostředků Azure mezi oblastmi Azure.

## <a name="remove-a-resource-portal"></a>Odebrání prostředku (portál)

Prostředky můžete odebrat v kolekci přesunů, a to následujícím způsobem na portálu Resource stěhovací:

1. V **různých oblastech** vyberte všechny prostředky, které chcete z kolekce odebrat, a vyberte **Odebrat**. 

    ![Tlačítko pro výběr k odebrání](./media/remove-move-resources/portal-select-resources.png)

2. V nabídce **odebrat prostředky** klikněte na **Odebrat**.

    ![Tlačítko pro výběr odebrání prostředků z kolekce přesunutí](./media/remove-move-resources/remove-portal.png)

## <a name="remove-a-move-collectionresource-group-portal"></a>Odebrání kolekce přesunutí/skupiny prostředků (portál)

Na portálu můžete odebrat kolekci nebo skupinu prostředků přesunout.

1. Pokud chcete odebrat prostředky z kolekce, postupujte podle pokynů v výše uvedeném postupu. Pokud odebíráte skupinu prostředků, ujistěte se, že neobsahuje žádné prostředky.
2. Odstraňte kolekci přesunutí nebo skupinu prostředků.  

## <a name="remove-a-resource-powershell"></a>Odebrání prostředku (PowerShell)

Odeberte prostředek (v našem příkladu PSDemoVM počítače) z kolekce pomocí prostředí PowerShell následujícím způsobem:

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus -Name PSDemoVM
```
**Očekávaný výstup**

![Výstup textu po odebrání prostředku z kolekce Move](./media/remove-move-resources/remove-resource.png)

## <a name="remove-a-collection-powershell"></a>Odebrání kolekce (PowerShell)

Pomocí PowerShellu odeberte celou kolekci Move, a to následujícím způsobem:

1. Pokud chcete odebrat prostředky v kolekci pomocí PowerShellu, postupujte podle výše uvedených pokynů.
2. Spusťte tento příkaz:

    ```azurepowershell-interactive
    # Remove a resource using the resource ID
    Remove-AzResourceMoverMoveCollection -SubscriptionId <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus -MoveCollectionName MoveCollection-centralus-westcentralus
    ```
    **Očekávaný výstup**
    
    ![Výstupní text po odebrání kolekce Move](./media/remove-move-resources/remove-collection.png)

## <a name="vm-resource-state-after-removing"></a>Stav prostředku virtuálního počítače po odebrání

Co se stane, když odeberete prostředek virtuálního počítače z kolekce přesunutí, závisí na stavu prostředku, jak je shrnuto v tabulce.

###  <a name="remove-vm-state"></a>Odebrat stav virtuálního počítače
**Stav prostředku** | **Virtuální počítač** | **Sítě**
--- | --- | --- 
**Přidáno pro přesunutí kolekce** | Odstranit z kolekce Move | Odstranit z kolekce Move 
**Vyřešené závislosti/Příprava – čeká na vyřízení** | Odstranit z kolekce přesunů  | Odstranit z kolekce Move 
**Probíhá příprava.**<br/> (nebo jakýkoli jiný stav probíhá) | Operace odstranění se nezdařila s chybou.  | Operace odstranění se nezdařila s chybou.
**Příprava se nezdařila** | Odstraňte z kolekce Move.<br/>Odstraňte cokoli vytvořené v cílové oblasti, včetně disků repliky. <br/><br/> Prostředky infrastruktury vytvořené během přesunu je potřeba odstranit ručně. | Odstraňte z kolekce Move.  
**Zahájit probíhající přesun** | Odstranit z kolekce Move<br/><br/> Odstraňte cokoli vytvořené v cílové oblasti, včetně virtuálních počítačů, disků replik atd.  <br/><br/> Prostředky infrastruktury vytvořené během přesunu je potřeba odstranit ručně. | Odstranit z kolekce Move
**Nepovedlo se iniciovat přesun** | Odstranit z kolekce Move<br/><br/> Odstraňte cokoli vytvořené v cílové oblasti, včetně virtuálních počítačů, disků replik atd.  <br/><br/> Prostředky infrastruktury vytvořené během přesunu je potřeba odstranit ručně. | Odstranit z kolekce Move
**Potvrzení čeká na vyřízení** | Doporučujeme zrušit přesunutí, aby byly nejprve odstraněny cílové prostředky.<br/><br/> Prostředek se vrátí do stavu **zahájení přesunu čeká na vyřízení** a můžete pokračovat odsud. | Doporučujeme zrušit přesunutí, aby byly nejprve odstraněny cílové prostředky.<br/><br/> Prostředek se vrátí do stavu **zahájení přesunu čeká na vyřízení** a můžete pokračovat odsud. 
**Potvrzení změn nebylo úspěšné.** | Doporučujeme, abyste zahodíte, aby byly nejprve odstraněny cílové prostředky.<br/><br/> Prostředek se vrátí do stavu **zahájení přesunu čeká na vyřízení** a můžete pokračovat odsud. | Doporučujeme zrušit přesunutí, aby byly nejprve odstraněny cílové prostředky.<br/><br/> Prostředek se vrátí do stavu **zahájení přesunu čeká na vyřízení** a můžete pokračovat odsud.
**Zahození dokončeno** | Prostředek se vrátí do stavu **zahájení přesunu čeká na vyřízení** .<br/><br/> Odstraní se z kolekce přesunů spolu s cokoliv vytvořeným v cílovém virtuálním počítači, disketách replik, trezoru atd.  <br/><br/> Prostředky infrastruktury vytvořené během přesunu je potřeba odstranit ručně. <br/><br/> Prostředky infrastruktury vytvořené během přesunu je potřeba odstranit ručně. |  Prostředek se vrátí do stavu **zahájení přesunu čeká na vyřízení** .<br/><br/> Odstraní se z kolekce Move.
**Zrušení se nezdařilo** | Doporučujeme zrušit přesun, aby byly nejprve odstraněny cílové prostředky.<br/><br/> Potom se prostředek vrátí do stavu **čekání** na ukončení a můžete pokračovat tam. | Doporučujeme zrušit přesun, aby byly nejprve odstraněny cílové prostředky.<br/><br/> Potom se prostředek vrátí do stavu **čekání** na ukončení a můžete pokračovat tam.
**Probíhá odstranění zdroje.** | Odstraní se z kolekce Move.<br/><br/> Neodstraní cokoli vytvořené v cílové oblasti.  | Odstraní se z kolekce Move.<br/><br/> Neodstraní cokoli vytvořené v cílové oblasti.
**Nepovedlo se odstranit zdroj** | Odstraní se z kolekce Move.<br/><br/> Neodstraní cokoli vytvořené v cílové oblasti. | Odstraní se z kolekce Move.<br/><br/> Neodstraní cokoli vytvořené v cílové oblasti.

## <a name="sql-resource-state-after-removing"></a>Stav prostředku SQL po odebrání

Co se stane, když odeberete prostředek SQL Azure z kolekce přesunutí, závisí na stavu prostředku, jak je shrnuto v tabulce.

**Stav prostředku** | **SQL** 
--- | --- 
**Přidáno pro přesunutí kolekce** | Odstranit z kolekce Move 
**Vyřešené závislosti/Příprava – čeká na vyřízení** | Odstranit z kolekce přesunů 
**Probíhá příprava.**<br/> (nebo jakýkoli jiný stav probíhá)  | Operace odstranění se nezdařila s chybou. 
**Příprava se nezdařila** | Odstranit z kolekce přesunů<br/><br/>Odstraňte cokoli vytvořené v cílové oblasti. 
**Zahájit probíhající přesun** |  Odstranit z kolekce přesunů<br/><br/>Odstraňte cokoli vytvořené v cílové oblasti. V tuto chvíli databáze SQL existuje a bude odstraněna. 
**Nepovedlo se iniciovat přesun** | Odstranit z kolekce přesunů<br/><br/>Odstraňte cokoli vytvořené v cílové oblasti. V tuto chvíli existuje databáze SQL a je nutné ji odstranit. 
**Potvrzení čeká na vyřízení** | Doporučujeme zrušit přesunutí, aby byly nejprve odstraněny cílové prostředky.<br/><br/> Prostředek se vrátí do stavu **zahájení přesunu čeká na vyřízení** a můžete pokračovat odsud.
**Potvrzení změn nebylo úspěšné.** | Doporučujeme zrušit přesunutí, aby byly nejprve odstraněny cílové prostředky.<br/><br/> Prostředek se vrátí do stavu **zahájení přesunu čeká na vyřízení** a můžete pokračovat odsud. 
**Zahození dokončeno** |  Prostředek se vrátí do stavu **zahájení přesunu čeká na vyřízení** .<br/><br/> Odstraní se z kolekce přesunů spolu s cokoli vytvořeným v cíli, včetně databází SQL. 
**Zrušení se nezdařilo** | Doporučujeme zrušit přesun, aby byly nejprve odstraněny cílové prostředky.<br/><br/> Potom se prostředek vrátí do stavu **čekání** na ukončení a můžete pokračovat tam. 
**Probíhá odstranění zdroje.** | Odstraní se z kolekce Move.<br/><br/> Neodstraní cokoli vytvořené v cílové oblasti. 
**Nepovedlo se odstranit zdroj** | Odstraní se z kolekce Move.<br/><br/> Neodstraní cokoli vytvořené v cílové oblasti. 

## <a name="next-steps"></a>Další kroky

Zkuste [virtuální počítač přesunout](tutorial-move-region-virtual-machines.md) do jiné oblasti s nástrojem Resource stěhovací.
