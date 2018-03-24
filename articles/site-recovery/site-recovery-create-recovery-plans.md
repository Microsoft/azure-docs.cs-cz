---
title: Vytváření a přizpůsobení plánů obnovení pro převzetí služeb při selhání a obnovení v Azure Site Recovery | Microsoft Docs
description: Naučte se vytvářet a přizpůsobovat plány obnovení v Azure Site Recovery. Tento článek popisuje, jak k převzetí služeb při selhání a obnovení virtuálních počítačů a fyzických serverů.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/21/2018
ms.author: raynew
ms.openlocfilehash: e02672ea76eada2d660b20f91c4417019d4efc97
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="create-and-customize-recovery-plans"></a>Vytváření a přizpůsobení plánů obnovení

Tento článek popisuje, jak vytvářet a přizpůsobovat plán obnovení v [Azure Site Recovery](site-recovery-overview.md). Než začnete, [Další](recovery-plan-overview.md) o plány obnovení.

## <a name="create-a-recovery-plan"></a>Vytvoření plánu obnovení

1. V trezoru služeb zotavení vyberte **plány obnovení (služba Site Recovery)** > **+ plán obnovení**.
2. V **vytvořit plán obnovení**, zadejte název plánu.
3. Vyberte zdroj a cíl založené na počítačích v plánu a vyberte **Resource Manager** pro model nasazení. Zdrojové umístění musí mít počítače, které jsou povolené pro převzetí služeb při selhání a obnovení. 

   **Převzetí služeb při selhání** | **Zdroj** | **cíl** 
   --- | --- | ---
   Azure do Azure | Oblast Azure |Oblast Azure
   Z VMware do Azure | Konfigurace serveru | Azure
   Fyzické počítače do Azure | Konfigurace serveru | Azure   
   Technologie Hyper-V spravován nástrojem VMM do Azure  | Zobrazovaný název VMM | Azure
   Technologie Hyper-V bez VMM do Azure | Název lokality technologie Hyper-V | Azure
   Nástroj VMM do nástroje VMM |Popisný název VMM | Zobrazovaný název VMM 

   > [!NOTE]
   > Plán obnovení může obsahovat počítače se stejným zdrojem a cílem. Stejný plán, který nelze VMware a virtuálních počítačů Hyper-V spravován nástrojem VMM. Virtuální počítače VMware a fyzické servery může být ve stejném plánu, pokud je zdrojem konfigurační server.

2. V **vyberte položky virtuální počítače**, vyberte počítače (nebo replikační skupiny), který chcete přidat do plánu. Pak klikněte na **OK**.
    - Počítače se přidají výchozí skupinu (skupiny 1) v plánu. Po převzetí služeb při selhání všechny počítače v této skupině spustí ve stejnou dobu.
    - Můžete vybrat pouze počítače jsou ve zdrojové a cílové umístění, které jste zadali. 
1. Klikněte na tlačítko **OK** vytvořte plán.

## <a name="add-a-group-to-a-plan"></a>Přidání skupiny do plánu

Můžete vytvořit další skupiny a přidat počítače do různých skupin tak, aby na základě skupiny učet můžete zadat různé chování. Můžete například zadat, když počítače do skupiny by měla spustit po převzetí služeb při selhání, nebo zadejte vlastní akce na skupinu.

1. V **plány obnovení**, klikněte pravým tlačítkem na plán > **přizpůsobit**. Ve výchozím nastavení po vytvoření plánu všechny počítače, které jste přidali do ní jsou umístěny ve výchozích 1. skupina.
2. Klikněte na tlačítko **+ skupiny**. Ve výchozím nastavení je v pořadí, ve kterém je přidána číslované novou skupinu. Může mít až sedm skupiny.
3. Vyberte počítač, který chcete přesunout do nové skupiny, klikněte na tlačítko **změnit skupinu**a potom vyberte novou skupinu. Případně, klikněte pravým tlačítkem na název skupiny > **chráněné položce**a přidat počítače do skupiny. Počítače nebo replikační skupiny můžete přiřadit pouze jednu skupinu v plánu obnovení.


## <a name="add-a-script-or-manual-action"></a>Přidat akci skriptu nebo ruční

Plán obnovení můžete přizpůsobit přidáním skriptu nebo manuální akce. Poznámky:

- Pokud replikujete do Azure můžete integrovat Azure automation runbook do plánu obnovení. [Další informace](site-recovery-runbook-automation.md).
- Pokud replikujete virtuální počítače Hyper-V spravované nástrojem System Center VMM, můžete vytvořit skript na serveru VMM pro místní a její zahrnutí do plánu obnovení.
- Při přidání skript přidá novou sadu akcí pro skupinu. Například se vytvoří sadu předběžné kroky 1. skupina s názvem *1. skupina: předběžné kroky*. Všechny předběžné kroky jsou uvedeny v této sadě. Skript v primární lokalitě můžete přidat pouze v případě, že máte server VMM nasazený.
- Pokud přidáte ruční akce, při spuštění plánu obnovení, zastaví se v okamžiku, kdy jste vložili manuální akce. Dialogové okno zobrazí výzvu k určení, že je dokončená manuální akce.
- Chcete-li vytvořit skript na serveru VMM, postupujte podle pokynů v [v tomto článku](hyper-v-vmm-recovery-script.md).
- Skripty můžete použít během převzetí služeb při selhání pro sekundární lokalitu a navrácení služeb po obnovení sekundární lokality na primární. Podpora závisí na vašem scénáři replikace:
    
    **Scénář** | **Převzetí služeb při selhání** | **Navrácení služeb po obnovení**
    --- | --- | --- 
    Azure do Azure  | Runbook | Runbook
    Z VMware do Azure | Runbook | Není k dispozici 
    Technologie Hyper-V s nástrojem VMM do Azure | Runbook | Skript
    Z lokality Hyper-V do Azure | Runbook | Není k dispozici
    VMM sekundární VMM | Skript | Skript

1. V plánu obnovení, klikněte na krok, ke které má být přidána akce a zadat, když akce má být provedena:. Pokud chcete, aby akce udělat předtím, než počítače ve skupině jsou spuštěny po převzetí služeb při selhání, vyberte **přidat před akce**.
    b. Pokud chcete akci, která má následovat po počítačů ve skupině spuštění po převzetí služeb při selhání, vyberte **akce po přidání**. Chcete-li přesunout umístění akci, vyberte **nahoru** nebo **přesunout dolů** tlačítka.
2. V **vložit akce**, vyberte **skriptu** nebo **manuální akce**.
3. Pokud chcete přidat ruční akce, proveďte následující kroky". Zadejte název akce a zadejte v pokynech akce. Osoba, která spuštěna převzetí služeb při selhání se zobrazí tyto pokyny.
    b. Zadejte, zda chcete přidat ruční akce pro všechny typy převzetí služeb při selhání (Test převzetí služeb při selhání, plánovaná převzetí (Pokud je relevantní)). Pak klikněte na **OK**.
4. Pokud chcete přidat skript, proveďte následující kroky:. Pokud přidáváte skript VMM, vyberte **převzetí služeb při selhání do skriptu VMM**a v **cestu ke skriptu** zadejte relativní cestu ke sdílené složce. Například, pokud se sdílená složka nachází na \\ <VMMServerName>\MSSCVMMLibrary\RPScripts, zadejte cestu: \RPScripts\RPScript.PS1.
    b. Pokud přidáváte Azure automation spustit adresáře, zadejte **účet Azure Automation** ve kterém se nachází sada runbook a vyberte odpovídající **skript sady Runbook Azure**.
5. Spusťte testovací převzetí služeb při selhání obnovení plánu na zajištění, že skript funguje podle očekávání.

## <a name="watch-a-video"></a>Přehrát video

Přehrát video, které ukazuje, jak sestavit plán obnovení.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Další postup

Další informace o [systémem převzetí služeb při selhání](site-recovery-failover.md).  

    
