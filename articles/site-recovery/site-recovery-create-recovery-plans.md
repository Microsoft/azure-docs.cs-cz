---
title: Vytvoření a přizpůsobení plánů obnovení pro převzetí služeb při selhání a obnovení ve službě Azure Site Recovery | Dokumentace Microsoftu
description: Zjistěte, jak vytvářet a přizpůsobovat plány obnovení ve službě Azure Site Recovery. Tento článek popisuje, jak převzetí služeb při selhání a obnovení virtuálních počítačů a fyzických serverů.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 145b23aa7b8e48a0301676595c8d59a64bebdd0b
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226664"
---
# <a name="create-and-customize-recovery-plans"></a>Vytvoření a přizpůsobení plánů obnovení

Tento článek popisuje, jak vytvořit a přizpůsobit plán obnovení v [Azure Site Recovery](site-recovery-overview.md). Než začnete, [Další](recovery-plan-overview.md) plány obnovení.

## <a name="create-a-recovery-plan"></a>Vytvoření plánu obnovení

1. V trezoru služby Recovery Services vyberte **plány obnovení (služba Site Recovery)** > **+ plánu obnovení**.
2. V **vytvořit plán obnovení**, zadejte název plánu.
3. Klikněte zdroj a cíl založené na počítačích v plánu a vyberte **Resource Manageru** pro model nasazení. Zdrojové umístění musí mít pro převzetí služeb při selhání a obnovení počítačů, které jsou povoleny. 

   **Převzetí služeb při selhání** | **Zdroj** | **Cíl** 
   --- | --- | ---
   Azure do Azure | Oblast Azure |Oblast Azure
   Z VMware do Azure | Konfigurační server | Azure
   Fyzické počítače do Azure | Konfigurační server | Azure   
   Technologie Hyper-V spravované nástrojem VMM do Azure  | Zobrazovaný název VMM | Azure
   Technologie Hyper-V bez VMM do Azure | Název lokality Hyper-V | Azure
   Nástroj VMM do nástroje VMM |Popisný název VMM | Zobrazovaný název VMM 

   > [!NOTE]
   > Plán obnovení může obsahovat počítače se stejným zdrojem a cílem. VMware a virtuálních počítačů Hyper-V spravované nástrojem VMM nemůže být v rámci stejného plánu. Virtuální počítače VMware a fyzických serverů může být v rámci stejného plánu, pokud je zdrojem konfiguračního serveru.

2. V **vyberte položky virtuálních počítačů**, vyberte počítače (replikační skupiny), který chcete přidat do plánu. Pak klikněte na **OK**.
    - Počítače se přidají výchozí skupinu (skupiny 1) v rámci plánu. Po převzetí služeb při selhání všechny počítače v této skupině spuštění ve stejnou dobu.
    - Můžete vybrat pouze počítače ve zdrojové a cílové umístění, které jste zadali. 
1. Klikněte na tlačítko **OK** vytvořte plán.

## <a name="add-a-group-to-a-plan"></a>Přidání skupiny do plánu

Vytvořit další skupiny a přidat počítače do různých skupin tak, aby na základě skupiny PinY lze určit různá chování. Můžete například zadat, pokud počítače ve skupině by měly spustit po převzetí služeb při selhání, nebo zadat vlastní akce na skupinu.

1. V **plány obnovení**, klikněte pravým tlačítkem myši plán > **vlastní**. Ve výchozím nastavení po vytvoření plánu všechno, co jsou počítače, které jste přidali do něj nachází ve výchozím 1 skupině.
2. Klikněte na tlačítko **+ skupina**. Ve výchozím nastavení je v pořadí, ve kterém se přidá číslované novou skupinu. Můžete mít až sedm skupiny.
3. Vyberte počítač, který chcete přesunout do nové skupiny, klikněte na tlačítko **změnit skupinu**a pak vyberte novou skupinu. Alternativně klikněte pravým tlačítkem na název skupiny > **chráněné položce**a přidat počítače do skupiny. Skupiny počítačů nebo replikace může patřit pouze do jedné skupiny v plánu obnovení.


## <a name="add-a-script-or-manual-action"></a>Přidání skriptu nebo ruční akce

Plán obnovení můžete přizpůsobit tak, že přidáte skriptu nebo ruční akce. Poznámky:

- Pokud replikujete do Azure můžete integrovat runbooky Azure automation do plánu obnovení. [Další informace](site-recovery-runbook-automation.md).
- Pokud provádíte replikaci virtuálních počítačů Hyper-V spravované nástrojem System Center VMM, můžete vytvořit skript na místní server VMM a zahrnout do plánu obnovení.
- Při přidání skript přidá novou sadu akcí pro skupinu. Například vytvořit sadu kroky prováděné před zpracováním pro 1 skupinu s názvem *1. skupina: kroky prováděné před zpracováním*. Všechny kroky prováděné před zpracováním jsou uvedené v této sadě. Skript v primární lokalitě můžete přidat pouze v případě, že máte server VMM nasazený.
- Pokud chcete přidat ručně prováděné akce, při spuštění plánu obnovení, ukončí v okamžiku, kdy jste vložili manuální akce. Dialogové okno se zobrazí výzva k zadání, manuální akce byla dokončena.
- Chcete-li vytvořit skript na serveru VMM, postupujte podle pokynů v [v tomto článku](hyper-v-vmm-recovery-script.md).
- Skripty můžete použít během převzetí služeb při selhání do sekundární lokality a během navrácení služeb po obnovení ze sekundární lokality na primární. Podpora závisí na vašem scénáři replikaci:
    
    **Scénář** | **Převzetí služeb při selhání** | **Navrácení služeb po obnovení**
    --- | --- | --- 
    Azure do Azure  | Runbook | Runbook
    Z VMware do Azure | Runbook | Není k dispozici 
    Technologie Hyper-V pomocí nástroje VMM do Azure | Runbook | Skript
    Z lokality Hyper-V do Azure | Runbook | Není k dispozici
    Sekundární VMM pomocí nástroje VMM | Skript | Skript

1. V plánu obnovení, klikněte na krok, ke kterému by se měl přidat akci a zadejte, když akce má být provedena:. Pokud chcete, aby akce, která má udělat předtím, než počítačů ve skupině se spustí po převzetí služeb při selhání, vyberte **přidat akci před**.
    b. Pokud chcete akce má být provedena po počítačů v nabídce start skupiny po převzetí služeb při selhání, vyberte **přidat akci po**. Chcete-li přesunout umístění akce, vyberte **nahoru** nebo **přesunout dolů** tlačítka.
2. V **vložení akce**vyberte **skript** nebo **manuální akce**.
3. Pokud chcete přidat ručně prováděné akce, proveďte následující". Zadejte název pro akci a zadejte instrukce pro akci. Osoba, spouštění převzetí služeb při selhání se zobrazí tyto pokyny.
    b. Určete, zda chcete přidat ručně prováděné akce u všech typů převzetí služeb při selhání (testovací převzetí služeb při selhání, převzetí služeb při plánované selhání (Pokud je to potřeba)). Pak klikněte na **OK**.
4. Pokud chcete přidat skript, postupujte takto:. Pokud přidáváte skriptu VMM, vyberte **převzetí služeb při selhání skriptu VMM**a v **cesta ke skriptu** zadejte relativní cestu ke sdílené složce. Například, pokud sdílená složka nachází na \\ <VMMServerName>\MSSCVMMLibrary\RPScripts, zadejte cestu: \RPScripts\RPScript.PS1.
    b. Pokud přidáváte služby Azure automation spustit knihy, zadejte **účet Azure Automation** ve kterém sada runbook nachází a vyberte příslušné **skript Runbooku Azure**.
5. Spuštění testovací převzetí služeb při selhání plánu obnovení k zajištění, že skript funguje podle očekávání.

## <a name="watch-a-video"></a>Podívejte se na video

Podívejte se na video ukazuje, jak vytvořit plán obnovení.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Další postup

Další informace o [spuštění převzetí služeb při selhání](site-recovery-failover.md).  

    
