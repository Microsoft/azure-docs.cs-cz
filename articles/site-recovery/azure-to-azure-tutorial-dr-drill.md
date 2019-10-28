---
title: Spuštění postupu zotavení po havárii pro virtuální počítače Azure do sekundární oblasti Azure pomocí služby Azure Site Recovery
description: Zjistěte, jak spustit postup zotavení po havárii pro virtuální počítače Azure IaaS do sekundární oblasti Azure pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: f85dd3abae8f6e4b3ccc10654e6da8363e80b3d3
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968277"
---
# <a name="run-a-disaster-recovery-drill-for-azure-vms-to-a-secondary-azure-region"></a>Spuštění postupu zotavení po havárii pro virtuální počítače Azure do sekundární oblasti Azure

Služba [Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii provozní kontinuity a zotavení po havárii (BCDR) tím, že zajišťuje provoz a dostupnost obchodních aplikací během plánovaných i neplánovaných výpadků. Site Recovery spravuje a orchestruje zotavení po havárii místních počítačů a virtuálních počítačů Azure, včetně replikace, převzetí služeb při selhání a zotavení.

V tomto kurzu se dozvíte, jak pro virtuální počítač Azure spustit postup zotavení po havárii z jedné oblasti Azure do jiné s testovacím převzetím služeb při selhání. Postup ověří vaši strategii replikace bez ztráty dat, bez výpadku a bez dopadu na vaše produkční prostředí. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Kontrola požadavků
> * Spuštění testovacího převzetí služeb při selhání pro jeden virtuální počítač

> [!NOTE]
> Cílem tohoto kurzu je provést uživatele postupem provedení postupu zotavení po havárii s minimálním počtem kroků. Pokud se chcete dozvědět více o různých aspektech souvisejících s provedením postupu zotavení po havárii, včetně důležitých aspektů sítí, automatizace nebo řešení potíží, prostudujte si dokumenty v části Postupy pro virtuální počítače Azure.

## <a name="prerequisites"></a>Předpoklady

- Před spuštěním testovacího převzetí služeb při selhání doporučujeme ověřit vlastnosti virtuálního počítače, abyste se ujistili, že je vše tak, jak má být.  Přejděte k vlastnostem virtuálního počítače v části **Replikované položky**. V okně **Základy** se zobrazí informace o nastavení a stavu počítačů.
- **Pro testovací převzetí služeb při selhání doporučujeme použít samostatnou síť virtuálních počítačů Azure**, a ne výchozí síť, kterou jste nastavili při povolování replikace.
- V závislosti na vašich zdrojových síťových konfiguracích pro každou síťovou kartu můžete volitelně zadat **podsíť, IP adresu, veřejnou IP adresu, skupinu zabezpečení sítě nebo interní Load Balancer** , které se mají připojit k jednotlivým síťovým adaptérům v části Nastavení testovacího převzetí služeb při selhání ve službě COMPUTE & před k provedení postupu zotavení po havárii.


## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

1. V části **Nastavení** > **Replikované položky** klikněte na ikonu **+ Testovací převzetí služeb při selhání** virtuálního počítače.

2. V části **Testovací převzetí služeb při selhání** vyberte bod obnovení, který se má pro převzetí služeb při selhání použít:

   - **Nejnovější zpracovaný:** Vrátí virtuální počítač k nejnovějšímu bodu obnovení zpracovanému službou Site Recovery. Zobrazí se časové razítko. S touto možností se neztrácí žádný čas zpracováním dat, takže poskytuje nízkou plánovanou dobu obnovení (RTO).
   - **Nejnovější konzistentní vzhledem k aplikacím:** Tato možnost vrátí všechny virtuální počítače k nejnovějšímu bodu obnovení konzistentnímu vzhledem k aplikacím. Zobrazí se časové razítko.
   - **Vlastní:** Vyberete si libovolný bod obnovení.

3. Vyberte cílovou virtuální síť Azure, ke které se připojí virtuální počítače Azure v sekundární oblasti po převzetí služeb při selhání.

    > [!NOTE]
    > Rozevírací seznam pro výběr virtuální sítě Azure nebude viditelný, pokud jsou nastavení testovacího převzetí služeb při selhání předem nakonfigurovaná pro replikovanou položku.

4. Převzetí služeb při selhání spustíte kliknutím na **OK**. Pokud chcete sledovat průběh, kliknutím na virtuální počítač otevřete jeho vlastnosti. Případně můžete kliknout na úlohu **Testovací převzetí služeb při selhání** v části název_trezoru > **Nastavení** > **Úlohy** > **Úlohy Site Recovery**.
5. Po dokončení převzetí služeb při selhání se replika virtuálního počítače Azure zobrazí na webu Azure Portal v části **Virtuální počítače**. Ujistěte se, že je virtuální počítač spuštěný, má odpovídající velikost a je připojený k odpovídající síti.
6. Virtuální počítače vytvořené během testovacího převzetí služeb při selhání odstraníte kliknutím na **Vyčistit testovací převzetí služeb při selhání** v replikované položce nebo v plánu obnovení. V části **Poznámky** si zaznamenejte a uložte jakékoli připomínky související s testovacím převzetím služeb při selhání.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Spuštění produkčního převzetí služeb při selhání](azure-to-azure-tutorial-failover-failback.md)
