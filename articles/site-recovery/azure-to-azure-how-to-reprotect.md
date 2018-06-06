---
title: Opětovné ochrany převzetí služeb při selhání virtuálních počítačích Azure zpět na primární oblast Azure s Azure Site Recovery | Microsoft Docs
description: Popisuje, jak znovu nastavte ochranu virtuálních počítačů Azure v sekundární oblasti, po převzetí služeb při selhání z primární oblasti, pomocí Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 05/31/2018
ms.author: rajanaki
ms.openlocfilehash: 6a57226f81e626e620033750c58d5998d8e7e894
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716580"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Opětovné ochrany převzal virtuálních počítačích Azure primární oblasti


Pokud jste [převzetí služeb při selhání](site-recovery-failover.md) virtuálních počítačích Azure z jedné oblasti na jiný pomocí [Azure Site Recovery](site-recovery-overview.md), spouštění virtuálních počítačů se v oblasti sekundární v nechráněném stavu. Pokud selhání zpět na primární oblasti virtuálních počítačů, je třeba provést následující akce:

- Znovu nastavte ochranu virtuálních počítačů v sekundární oblasti, tak, aby spuštění při replikaci na primární oblasti. 
- Po dokončení vytvoření a jsou replikaci virtuálních počítačů, které můžete převzetí služeb při selhání je ze sekundární primární oblasti.

> [!WARNING]
> Pokud jste [migrovat](migrate-overview.md#what-do-we-mean-by-migration) počítačů z primárního na sekundární oblasti přesunout virtuální počítač do jiné skupiny prostředků, nebo odstranit virtuální počítač Azure, nelze znovu nastavte ochranu virtuálního počítače, nebo ji návratu.


## <a name="prerequisites"></a>Požadavky
1. Virtuálních počítačů převzetí služeb při selhání z primárního na sekundární oblasti musí být potvrzeny.
2. Primární cílovou lokalitu by měly být dostupné a nyní byste měli mít přístup k nebo vytvářet prostředky v této oblasti.

## <a name="reprotect-a-vm"></a>Znovu nastavte ochranu virtuálního počítače

1. V **trezoru** > **replikované položky**, klikněte pravým tlačítkem na neúspěšný přes virtuální počítač a vyberte **znovu nastavit ochranu**. Směr došlo by měl zobrazit z sekundární pro primární. 

  ![Znovu nastavte ochranu](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Zkontrolujte sady skupiny, sítě, úložiště a dostupnosti prostředků. Pak klikněte na **OK**. Pokud jsou všechny prostředky označené jako nové, jsou vytvořeny jako součást procesu vytvoření.
3. Vytvoření úlohy doplňuje pro cílovou lokalitu s nejnovější data. Po dokončení, rozdílové replikace probíhá. Potom můžete převzít zpět do primární lokality. Můžete vybrat účet úložiště nebo síť, kterou chcete použít během znovu nastavte ochranu, pomocí možnosti přizpůsobit.

  ![Přizpůsobení možnost](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Upravit nastavení opětovné ochrany

Následující vlastnosti cíle VMe můžete přizpůsobit během vytvoření.

![Přizpůsobení](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Vlastnost |Poznámky  |
|---------|---------|
|Cílová skupina prostředků     | Upravte cílová skupina prostředků, ve kterém je virtuální počítač vytvořený. Jako součást nové provedení ochrany je odstraněn cílovém virtuálním počítači. Můžete novou skupinu prostředků, pod kterým chcete vytvořit virtuální počítač po převzetí služeb při selhání.        |
|Cílová virtuální síť     | Cílová síť nelze změnit během úloh opětovné ochrany. Chcete-li změnit síť, znovu proveďte mapování sítě.         |
|Cílové úložiště (sekundární virtuální počítač nepoužívá spravovaných disků)     | Můžete změnit účet úložiště, který používá virtuální počítač po převzetí služeb při selhání.         |
|Repliky spravované disky (sekundární virtuální počítač používá spravovaných disků)    | Site Recovery vytvoří repliky spravované disky v primární oblasti pro zrcadlení sekundární virtuální počítač spravovaný disky.         | 
|Úložiště mezipaměti     | Můžete zadat účet úložiště mezipaměti, který se používá během replikace. Ve výchozím nastavení je možné vytvořit nový účet úložiště mezipaměti, pokud neexistuje.         |
|Skupina dostupnosti     |Pokud virtuální počítač v sekundární oblasti je součástí skupiny dostupnosti, můžete vybrat sadu dostupnosti pro cílový počítač v primární oblasti. Ve výchozím nastavení Site Recovery se pokusí vyhledat existující dostupnosti nastavena v primární oblasti a použít. Během přizpůsobení můžete určit nové sady dostupnosti.         |


### <a name="what-happens-during-reprotection"></a>Co se stane při vytvoření?

Ve výchozím nastavení dojde k následujícímu:

1. Účet úložiště mezipaměti se vytvoří v primární oblasti
2. Pokud cílový účet úložiště (původní účet úložiště v primární oblasti) neexistuje, vytvoří se nový. Název účtu úložiště přiřazené je název účtu úložiště používat sekundární virtuální počítač, na konci "Automatické obnovení systému".
3. Pokud virtuální počítač používá spravované disky, spravovat repliky disků jsou vytvořené v primární oblasti k ukládání dat replikovaných z disků sekundární virtuální počítač. 
4. Pokud cílovou sadu dostupnosti neexistuje, vytvoří se nový jako součást úlohy opětovné ochrany v případě potřeby. Pokud jste upravili nastavení nové provedení ochrany, je použít vybrané sady.

Když spustíte opětovné ochrany úlohy a cílových virtuálních počítačů existuje, dojde k následující položky:

1. Požadované součásti jsou vytvořené jako součást opětovné ochrany. Pokud již existují, jsou opakovaně využívány.
2. Straně cíle, které se virtuální počítač vypnutý, pokud je spuštěná.
3. Cílový disk virtuálního počítače straně se službou Site Recovery zkopíruje do kontejneru, jako objekt blob počáteční hodnoty.
4. Straně cíle virtuálních počítačů se odstraní.
5. Objekt blob počáteční hodnoty je používán aktuální zdroj straně (sekundární) virtuálních počítačů k replikaci. Tím se zajistí, že jsou replikovány pouze rozdíly.
6. Hlavní změny mezi zdrojového disku a objektů blob počáteční hodnoty jsou synchronizovány. To může trvat delší dobu.
7. Po dokončení úloh opětovné ochrany rozdílová replikace začne a vytvoří bod obnovení souladu zásady replikace.
8. Po úspěšné opětovné ochrany úlohy virtuálního počítače zadá chráněném stavu.

## <a name="next-steps"></a>Další postup

Jakmile je chráněný virtuální počítač, můžete spustit převzetí služeb při selhání. Převzetí služeb při selhání vypne virtuální počítač v sekundární oblasti a vytvoří a spustí virtuální počítač v primární oblasti s výpadky malé. Doporučujeme, abyste odpovídajícím způsobem nastavit čas a spustit testovací převzetí služeb, ale inicializaci úplné převzetí služeb při selhání do primární lokality. [Další informace](site-recovery-failover.md) o převzetí služeb při selhání.

