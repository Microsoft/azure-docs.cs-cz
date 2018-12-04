---
title: Operace opětovného zapnutí ochrany převzetí služeb při selhání virtuálních počítačů Azure zpět do primární oblasti Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Popisuje, jak znovunastavení ochrany virtuálních počítačů Azure do sekundární oblasti po převzetí služeb při selhání z primární oblasti, pomocí Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 0b5fe1b1df306bf0930715bc6cfb586e5445f85c
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52839955"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Operace opětovného zapnutí ochrany převzetí služeb při selhání virtuálních počítačů Azure do primární oblasti


Pokud jste [převzetí služeb při selhání](site-recovery-failover.md) virtuálních počítačů Azure z jedné oblasti do druhé pomocí [Azure Site Recovery](site-recovery-overview.md), spouštění virtuálních počítačů v sekundární oblasti, ocitne v nechráněném stavu. Pokud se navrácení služeb po obnovení virtuálních počítačů do primární oblasti, je třeba provést následující kroky:

- Znovunastavení ochrany virtuálních počítačů v sekundární oblasti, aby se začaly replikovat do primární oblasti.
- Po dokončení opětovného nastavování ochrany a replikaci virtuálních počítačů, můžete je převzít služby ze sekundární do primární oblasti.

> [!WARNING]
> Pokud jste [migrovat](migrate-overview.md#what-do-we-mean-by-migration) počítače z primární do sekundární oblasti, virtuální počítač přesunout do jiné skupiny prostředků nebo odstranění virtuálního počítače Azure, a nemůžete znovunastavení ochrany virtuálního počítače nebo po obnovení navrátit.


## <a name="prerequisites"></a>Požadavky
1. Převzetí služeb virtuálního počítače z primární do sekundární oblasti musí být potvrzeny.
2. Cílové primární lokalitě musí být k dispozici. proto byste měli mít přístup k nebo vytvářet prostředky v této oblasti.

## <a name="reprotect-a-vm"></a>Znovunastavení ochrany virtuálního počítače

1. V **trezor** > **replikované položky**, klikněte pravým tlačítkem na neúspěšný selhání pro virtuální počítač a vyberte **znovu nastavit ochranu**. Směr opětovného nastavování ochrany by měly vykazovat ze sekundární do primární.

  ![Operace opětovného zapnutí ochrany](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Projděte si sady skupiny, sítě, úložiště a dostupnosti prostředků. Pak klikněte na **OK**. Pokud jsou všechny prostředky označené jako nové, jsou vytvořeny jako součást procesu opětovného nastavování ochrany.
3. Úloha opětovného nastavování ochrany přidá do cílové lokality nejnovější data. Po dokončení, která se provádí rozdílová replikace. Potom můžete převzít služby zpět do primární lokality. Můžete vybrat účet úložiště nebo sítě, kterou chcete použít během znovu nastavit ochranu, pomocí možnosti přizpůsobit.

  ![Možnost přizpůsobení](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Přizpůsobení nastavení zpětné replikace

Můžete přizpůsobit následující vlastnosti cíle VMe během opětovného nastavování ochrany.

![Přizpůsobení](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Vlastnost |Poznámky  |
|---------|---------|
|Cílová skupina prostředků     | Upravte cílovou skupinu prostředků, ve kterém se vytvoří virtuální počítač. Jako součást opětovného nastavování ochrany, cílový virtuální počítač je odstraněný. Můžete zvolit novou skupinu prostředků, pod kterým chcete vytvořit virtuální počítač po převzetí služeb při selhání.        |
|Cílová virtuální síť     | Cílová síť nelze změnit během úlohy opětovného zapnutí ochrany. Chcete-li změnit síť znovu mapování sítě.         |
|Cílové úložiště (sekundární virtuální počítač nepoužívá spravované disky)     | Můžete změnit účet úložiště, který používá virtuální počítač po převzetí služeb při selhání.         |
|Repliky spravovaných disků (sekundární virtuální počítač používá spravované disky)    | Site Recovery vytvoří v primární oblasti zrcadlící spravované disky sekundárního virtuálního počítače repliky spravovaných disků.         |
|Úložiště mezipaměti     | Můžete zadat účet úložiště mezipaměti, které se použijí při replikaci. Ve výchozím nastavení je možné vytvořit nový účet úložiště mezipaměti, pokud neexistuje.         |
|Skupina dostupnosti     |Pokud virtuální počítač v sekundární oblasti je součástí skupiny dostupnosti, můžete sadu dostupnosti pro cílový virtuální počítač v primární oblasti. Ve výchozím nastavení Site Recovery se pokusí vyhledat existující dostupnosti v primární oblasti a použít ho. Během přizpůsobování můžete zadat novou skupinu dostupnosti.         |


### <a name="what-happens-during-reprotection"></a>Co se stane během opětovného nastavování ochrany?

Ve výchozím nastavení dojde k následujícímu:

1. Účet úložiště mezipaměti se vytvoří v oblasti, kde běží převzetí virtuálního počítače.
2. Pokud cílový účet úložiště (původního účtu úložiště v primární oblasti) neexistuje, vytvoří se nový. Název účtu úložiště přiřazené je název účtu úložiště používá sekundární virtuální počítač, příponu "Azure Site Recovery".
3. Pokud se váš virtuální počítač používá spravované disky, repliky spravovaných disků se vytvoří v primární oblast pro ukládání dat replikovaných z disků sekundárního virtuálního počítače.
4. Pokud cílová skupina dostupnosti neexistuje, je vytvořen nový jako součást úlohy opětovného zapnutí ochrany v případě potřeby. Pokud jste upravili nastavení opětovného nastavování ochrany, použije se vybrané sady.

Když spustíte úlohu znovunastavení ochrany a cílový virtuální počítač existuje, dojde k následující položky:

1. Straně cíle, které virtuální počítač je zapnutý vypnuto, pokud běží.
2. Pokud virtuální počítač používá spravované disky, kopii původní disky se vytvoří s "-ASRReplica" příponu. Původní disky se odstraní. "-ASRReplica" kopie se používají pro replikaci.
3. Pokud virtuální počítač používá nespravované disky, datové disky cílového virtuálního počítače jsou odpojená, používanou k replikaci. Kopii disku s operačním systémem se vytvoří a připojí na virtuálním počítači. Původní disk s operačním systémem je odpojená a použít pro replikaci.
4. Jsou synchronizovány pouze změny mezi zdrojový disk a cílový disk. Rozdíly jsou vypočítané porovnáním obou discích tak a pak přeneseny. Bude to trvat několik hodin.
5. Po dokončení synchronizace začíná rozdílové replikace a vytvoří bod obnovení podle zásady replikace.

Když aktivujte úlohu znovunastavení ochrany a cílový virtuální počítač a disky neexistuje, dojde k následující položky:
1. Pokud virtuální počítač používá spravované disky, repliky disků jsou vytvořeny pomocí "-ASRReplica" příponu. "-ASRReplica" kopie se používají pro replikaci.
2. Pokud virtuální počítač používá nespravované disky, repliky disků jsou vytvořeny v cílovém účtu úložiště.
3. Celý disky jsou zkopírovány z se v oblasti, aby se nové cílové oblasti.
4. Po dokončení synchronizace začíná rozdílové replikace a vytvoří bod obnovení podle zásady replikace.

## <a name="next-steps"></a>Další postup

Po aktivaci ochrany virtuálního počítače můžete spustit převzetí služeb při selhání. Převzetí služeb při vypnutí virtuálního počítače v sekundární oblasti a vytvoří a spustí virtuální počítač v primární oblasti s malý výpadek. Doporučujeme proto vyberte čas a spustíte test převzetí před zahájením úplné převzetí služeb při selhání do primární lokality. [Další informace](site-recovery-failover.md) o převzetí služeb při selhání.
