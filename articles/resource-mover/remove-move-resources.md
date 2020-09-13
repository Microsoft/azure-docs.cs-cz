---
title: Odebrání prostředků z kolekce přesunů v nástroji Azure Resource stěhovací
description: Naučte se, jak odebrat prostředky z kolekce přesunů v nástroji Azure Resource stěhovací.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 241ccbda67f7a2518d0c44a0d362673922ad4284
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89652947"
---
# <a name="remove-resources-from-a-move-collection"></a>Odebrání prostředků z kolekce pro přesun

Tento článek popisuje, jak odebrat prostředky z kolekce přesunů v nástroji [Azure Resource stěhovací](overview.md). Přesun kolekcí se používá při přesunu prostředků Azure mezi oblastmi Azure.

## <a name="remove-a-resource-portal"></a>Odebrání prostředku (portál)

Odeberte na portálu pro poinstalaci prostředku následujícím způsobem:

1. V **různých oblastech**vyberte prostředky, které chcete odebrat z kolekce > **Odebrat**.

    ![Tlačítko pro výběr k odebrání](./media/remove-move-resources/portal-select-resources.png)

1. V nabídce **odebrat prostředky**klikněte na **Odebrat**.

    ![Tlačítko pro výběr odebrání prostředků z kolekce přesunutí](./media/remove-move-resources/remove-portal.png)

## <a name="remove-a-resource-powershell"></a>Odebrání prostředku (PowerShell)

Odeberte prostředek (v našem příkladu PSDemoVM počítače) z kolekce pomocí prostředí PowerShell následujícím způsobem:

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus - Name PSDemoVM
```
**Očekávaný výstup** 
 ![ Výstup textu po odebrání prostředku z kolekce Move](./media/remove-move-resources/remove-resource.png)

## <a name="remove-a-collection-powershell"></a>Odebrání kolekce (PowerShell)

Pomocí PowerShellu odeberte celou kolekci Move, a to následujícím způsobem:

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus 
```
**Očekávaný výstup** 
 ![ Výstupní text po odebrání kolekce Move](./media/remove-move-resources/remove-collection.png)

## <a name="vm-resource-state-after-removing"></a>Stav prostředku virtuálního počítače po odebrání

Co se stane, když odeberete prostředek virtuálního počítače z kolekce přesunutí, závisí na stavu prostředku, jak je shrnuto v tabulce.

###  <a name="remove-vm-state"></a>Odebrat stav virtuálního počítače
**Stav prostředku** | **SÍŤ** | **Sítě**
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