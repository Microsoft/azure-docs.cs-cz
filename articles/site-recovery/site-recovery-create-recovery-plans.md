---
title: Vytvoření nebo přizpůsobení plánů obnovení v Azure Site Recovery
description: Zjistěte, jak vytvořit a přizpůsobit plány obnovení pro zotavení po havárii pomocí služby Azure Site Recovery.
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: 6540317324a9f0d9bccc046ecf95824d4128bd09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705832"
---
# <a name="create-and-customize-recovery-plans"></a>Vytvoření a přizpůsobení plánů obnovení

Tento článek popisuje, jak vytvořit a přizpůsobit plán obnovení pro převzetí služeb při selhání v [azure site recovery](site-recovery-overview.md). Než začnete, [přečtěte si další informace](recovery-plan-overview.md) o plánech obnovení.

## <a name="create-a-recovery-plan"></a>Vytvoření plánu obnovení

1. V trezoru služby Recovery Services vyberte **plány obnovení (obnovení webu)** > **+ plán obnovení**.
2. V **části Vytvořit plán obnovení**zadejte název plánu.
3. Vyberte zdroj a cíl na základě počítačů v plánu a vyberte **Správce prostředků** pro model nasazení. Zdrojové umístění musí mít počítače, které jsou povoleny pro převzetí služeb při selhání a obnovení. 

    **Zabezpečení před selháním** | **Zdroj** | **Cíl** 
   --- | --- | ---
   Azure do Azure | Vyberte oblast Azure | Vyberte oblast Azure
   VMware do Azure | Výběr konfiguračního serveru | Vybrat Azure
   Fyzické počítače do Azure | Výběr konfiguračního serveru | Vybrat Azure   
   Z Hyper-V do Azure | Výběr názvu webu Technologie Hyper-V | Vybrat Azure
   Hyper-V (spravovaný VMM) do Azure  | Výběr serveru VMM | Vybrat Azure
  
    Je třeba počítat s následujícím:
    -  Plán obnovení lze použít jenom pro převzetí služeb při selhání ze zdrojového umístění do Azure. Pro navrácení služeb po obnovení z Azure nelze použít plán obnovení.
    - Zdrojové umístění musí mít počítače, které jsou povoleny pro převzetí služeb při selhání a obnovení. 
    - Plán obnovení může obsahovat počítače se stejným zdrojem a cílem. 
    - Virtuální mandy VMware a virtuální zařízení Hyper-V spravované vmm em it můžete zahrnout do stejného plánu.
    - Virtuální zařízení VMware a fyzické servery mohou být ve stejném plánu.

4. V **části Vybrat položky virtuálních počítačů**vyberte počítače (nebo replikační skupinu), které chcete přidat do plánu. Pak klikněte na **OK**.
    - Počítače jsou přidány výchozí skupiny (Skupina 1) v plánu. Po převzetí služeb při selhání všechny počítače v této skupině spustit ve stejnou dobu.
    - Můžete vybrat pouze stroje jsou ve zdrojových a cílových umístěních, které jste zadali. 
5. Chcete-li plán vytvořit, klepněte na tlačítko **OK.**

## <a name="add-a-group-to-a-plan"></a>Přidání skupiny do plánu

Můžete vytvořit další skupiny a přidat počítače do různých skupin, takže můžete zadat různé chování na základě skupiny po skupině. Můžete například určit, kdy mají počítače ve skupině začít po převzetí služeb při selhání, nebo zadat vlastní akce pro skupinu.

1. V **plánu obnovení**klepněte pravým tlačítkem myši na plán > **Přizpůsobit**. Ve výchozím nastavení jsou po vytvoření plánu všechny počítače, které jste do něj přidali, umístěny ve výchozí skupině 1.
2. Klepněte na tlačítko **+Skupina**. Ve výchozím nastavení je nová skupina očíslonována v pořadí, ve kterém je přidána. Můžete mít až sedm skupin.
3. Vyberte počítač, který chcete přesunout do nové skupiny, klikněte na **Změnit skupinu**a vyberte novou skupinu. Případně klepněte pravým tlačítkem myši na název skupiny > **Chráněná položka**a přidejte do skupiny počítače. Počítač nebo replikační skupina může patřit pouze do jedné skupiny v plánu obnovení.


## <a name="add-a-script-or-manual-action"></a>Přidání skriptu nebo ruční akce

Plán obnovení můžete přizpůsobit přidáním skriptu nebo ruční akce. Poznámky:

- Pokud se replikujete do Azure, můžete do plánu obnovení integrovat runbooky azure automation. [Další informace](site-recovery-runbook-automation.md).
- Pokud replikujete virtuální počítače Hyper-V spravované systémem VMM, můžete vytvořit skript na místním serveru VMM a zahrnout ho do plánu obnovení.
- Když přidáte skript, přidá novou sadu akcí pro skupinu. Například sada předkroků pro skupinu 1 je vytvořena s názvem *Skupina 1: předkroky*. Všechny předběžné kroky jsou uvedeny v této sadě. Skript můžete přidat do primární lokality pouze v případě, že máte nasazený server VMM.
- Pokud přidáte ruční akci, při spuštění plánu obnovení se zastaví v místě, ve kterém jste vložili ruční akci. Dialogové okno vás vyzve k zadání, že ruční akce byla dokončena.
- Chcete-li vytvořit skript na serveru VMM, postupujte podle pokynů v [tomto článku](hyper-v-vmm-recovery-script.md).
- Skripty lze použít během převzetí služeb při selhání do sekundární lokality a během navrácení služeb po selhání ze sekundární lokality na primární. Podpora závisí na scénáři replikace:
    
    **Scénář** | **Zabezpečení před selháním** | **Navrácení služeb po selhání**
    --- | --- | --- 
    Azure do Azure  | Runbook | Runbook
    Z VMware do Azure | Runbook | Není k dispozici 
    Technologie Hyper-V s VMM do Azure | Runbook | Skript
    Z lokality Hyper-V do Azure | Runbook | Není k dispozici
    VMM sekundární VMM | Skript | Skript

1. V plánu obnovení klepněte na krok, ke kterému má být akce přidána, a určete, kdy má akce nastat:
    1. Pokud chcete, aby k akci došlo před spuštěním počítačů ve skupině po převzetí služeb při selhání, vyberte **přidat předběžnou akci**.
    1. Pokud chcete, aby k akci došlo po spuštění počítačů ve skupině po převzetí služeb při selhání, vyberte **Přidat akci příspěvku**. Chcete-li přesunout pozici akce, vyberte tlačítka **Přesunout nahoru** nebo **Přesunout dolů.**
2. V **akci Vložit**vyberte **Skript** nebo Ruční **akce**.
3. Pokud chcete přidat ruční akci, postupujte takto:
    1. Zadejte název akce a zadejte pokyny pro akci. Osoba, která spouštěla převzetí služeb při selhání, uvidí tyto pokyny.
    1. Určete, zda chcete přidat ruční akci pro všechny typy převzetí služeb při selhání (Test, Převzetí služeb při selhání, Plánované převzetí služeb při selhání (pokud je to relevantní)). Pak klikněte na **OK**.
4. Chcete-li přidat skript, postupujte takto:
    1. Pokud přidáváte skript VMM, vyberte **převzetí služeb při selhání skriptu VMM**a v cestě **skriptu** zadejte relativní cestu ke sdílené položce. Pokud je například sdílená \\ \<složky umístěna na adrese VMMServerName>\MSSCVMMLibrary\RPScripts, zadejte cestu: \RPScripts\RPScript.PS1.
    1. Pokud přidáváte knihu spuštění automatizace Azure, zadejte **účet Azure Automation,** ve kterém se nachází runbook, a vyberte příslušný **skript Azure Runbook Script**.
5. Spusťte zkušební převzetí služeb při selhání plánu obnovení, abyste zajistili, že skript funguje podle očekávání.

## <a name="watch-a-video"></a>Podívejte se na video

Podívejte se na video, které ukazuje, jak vytvořit plán obnovení.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Další kroky

Další informace o [spuštění převzetí služeb při selhání](site-recovery-failover.md).  

    
