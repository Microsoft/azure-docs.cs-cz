---
title: Vytvoření nebo přizpůsobení plánů obnovení v Azure Site Recovery
description: Naučte se vytvářet a přizpůsobovat plány obnovení pro zotavení po havárii pomocí služby Azure Site Recovery.
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: 0dcde98e8dcaef12896c18c25429f0ba7b1b27d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009717"
---
# <a name="create-and-customize-recovery-plans"></a>Vytvoření a přizpůsobení plánů obnovení

Tento článek popisuje, jak vytvořit a přizpůsobit plán obnovení pro převzetí služeb při selhání v [Azure Site Recovery](site-recovery-overview.md). Než začnete, [Přečtěte si další informace](recovery-plan-overview.md) o plánech obnovení.

## <a name="create-a-recovery-plan"></a>Vytvoření plánu obnovení

1. V trezoru Recovery Services vyberte **plány obnovení (Site Recovery)**  >  **+ plán obnovení**.
2. V části **vytvořit plán obnovení** zadejte název plánu.
3. Zvolte zdroj a cíl na základě počítačů v plánu a vyberte **Správce prostředků** pro model nasazení. Zdrojové umístění musí mít počítače, u kterých je povolené převzetí služeb při selhání a obnovení. 

    **Převzetí služeb při selhání** | **Zdroj** | **Cíl** 
   --- | --- | ---
   Azure do Azure | Výběr oblasti Azure | Výběr oblasti Azure
   Z VMware do Azure | Vyberte konfigurační server. | Vybrat Azure
   Fyzické počítače do Azure | Vyberte konfigurační server. | Vybrat Azure   
   Z Hyper-V do Azure | Vyberte název lokality Hyper-V. | Vybrat Azure
   Technologie Hyper-V (spravovaná nástrojem VMM) do Azure  | Vybrat server VMM | Vybrat Azure
  
    Všimněte si, že:
    - Můžete použít plán obnovení pro převzetí služeb při selhání do Azure i navrácení služeb po obnovení z Azure.
    - Zdrojové umístění musí mít počítače, u kterých je povolené převzetí služeb při selhání a obnovení.
    - Plán obnovení může obsahovat počítače se stejným zdrojem a cílem.
    - Ve stejném plánu můžete zahrnout virtuální počítače VMware a virtuální počítače Hyper-V spravované nástrojem VMM.
    - Virtuální počítače VMware a fyzické servery můžou být ve stejném plánu.

4. V části **Vybrat položky virtuálních počítačů** vyberte počítače (nebo replikační skupinu), které chcete přidat do plánu. Pak klikněte na **OK**.
    - Počítače se přidají do plánu do výchozí skupiny (skupina 1). Po převzetí služeb při selhání se všechny počítače v této skupině spustí ve stejnou dobu.
    - V zadaných zdrojových a cílových umístěních můžete vybrat jenom počítače. 
5. Kliknutím na tlačítko **OK** vytvořte plán.

## <a name="add-a-group-to-a-plan"></a>Přidání skupiny do plánu

Vytvoříte další skupiny a přidáte počítače do různých skupin, abyste mohli určit různé chování na základě skupin. Můžete například určit, kdy se mají počítače ve skupině spouštět po převzetí služeb při selhání, nebo zadat přizpůsobené akce na skupinu.

1. V části **plány obnovení** klikněte pravým tlačítkem na plán > **přizpůsobit**. Ve výchozím nastavení platí, že po vytvoření plánu jsou všechny počítače, které jste do něho přidali, umístěny ve výchozí skupině 1.
2. Klikněte na **+ Skupina**. Ve výchozím nastavení je nová skupina očíslována v pořadí, ve kterém je přidána. Můžete mít až sedm skupin.
3. Vyberte počítač, který chcete přesunout do nové skupiny, klikněte na **změnit skupinu** a potom vyberte novou skupinu. Případně můžete kliknout pravým tlačítkem myši na název skupiny > **chráněná položka** a přidat počítače do skupiny. Počítač nebo replikační skupina můžou patřit do jedné skupiny v rámci plánu obnovení.


## <a name="add-a-script-or-manual-action"></a>Přidání skriptu nebo ruční akce

Plán obnovení můžete přizpůsobit přidáním skriptu nebo ruční akce. Poznámky:

- Pokud provádíte replikaci do Azure, můžete do plánu obnovení integrovat Runbooky Azure Automation. [Další informace](site-recovery-runbook-automation.md).
- Pokud provádíte replikaci virtuálních počítačů Hyper-V, které spravuje System Center VMM, můžete vytvořit skript na místním serveru VMM a zahrnout ho do plánu obnovení.
- Když přidáte skript, přidá novou sadu akcí pro skupinu. Například sada předběžných kroků pro skupinu 1 se vytvoří se *skupinou název 1: předběžné kroky*. Všechny předběžné kroky jsou uvedeny v této sadě. Skript můžete do primární lokality přidat jenom v případě, že máte nasazený server VMM.
- Pokud přidáte ruční akci, při spuštění plánu obnovení se zastaví v okamžiku, kdy jste vložili ruční akci. Zobrazí se dialogové okno s výzvou, abyste určili, že ruční akce byla dokončena.
- Chcete-li vytvořit skript na serveru VMM, postupujte podle pokynů v [tomto článku](hyper-v-vmm-recovery-script.md).
- Skripty lze použít během převzetí služeb při selhání do sekundární lokality a během navrácení služeb po obnovení ze sekundární lokality na primární. Podpora závisí na vašem scénáři replikace:
    
    **Scénář** | **Převzetí služeb při selhání** | **Vráceny**
    --- | --- | --- 
    Azure do Azure  | Runbook | Runbook
    Z VMware do Azure | Runbook | NA 
    Hyper-V s VMM do Azure | Runbook | Skript
    Z lokality Hyper-V do Azure | Runbook | NA
    VMM do sekundárního VMM | Skript | Skript

1. V plánu obnovení klikněte na krok, ke kterému má být akce přidána, a určete, kdy má být provedena akce:
    1. Pokud chcete, aby tato akce proběhla před spuštěním počítačů ve skupině po převzetí služeb při selhání, vyberte **přidat akci před**.
    1. Pokud chcete, aby se akce po převzetí služeb při selhání spouštěla po počítačích ve skupině, vyberte **přidat akci post**. Chcete-li přesunout pozici akce, vyberte tlačítko **nahoru** **nebo dolů** .
2. V možnosti **Vložit akci** vyberte **skript** nebo **Ruční akce**.
3. Pokud chcete přidat ruční akci, udělejte toto:
    1. Zadejte název akce a zadejte pokyny pro akci. Uživatel, který spustí převzetí služeb při selhání, uvidí tyto pokyny.
    1. Určete, jestli chcete přidat ruční akci pro všechny typy převzetí služeb při selhání (test, převzetí služeb při selhání, plánované převzetí služeb při selhání (Pokud je relevantní)). Pak klikněte na **OK**.
4. Pokud chcete přidat skript, udělejte toto:
    1. Pokud přidáváte skript VMM, vyberte **převzetí služeb při selhání do skriptu VMM** a v **cestě ke skriptu** zadejte relativní cestu ke sdílené složce. Pokud se například sdílená složka nachází na adrese \\ \<VMMServerName> \MSSCVMMLibrary\RPScripts, zadejte cestu: \RPScripts\RPScript.PS1.
    1. Pokud přidáváte knihu Azure Automation Run, určete **účet Azure Automation** , ve kterém se sada Runbook nachází, a vyberte příslušný **skript Runbooku Azure**.
5. Spusťte testovací převzetí služeb při selhání plánu obnovení, aby se zajistilo, že skript funguje podle očekávání.

## <a name="watch-a-video"></a>Přehrát video

Podívejte se na video, které ukazuje, jak vytvořit plán obnovení.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [spuštění převzetí služeb při selhání](site-recovery-failover.md).  

    
