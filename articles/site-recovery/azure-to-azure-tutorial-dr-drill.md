---
title: Spuštění postupu zotavení po havárii virtuálního počítače Azure pomocí Azure Site Recovery
description: Přečtěte si, jak spustit postup zotavení po havárii do sekundární oblasti pro virtuální počítače Azure pomocí služby Azure Site Recovery.
services: site-recovery
ms.topic: tutorial
ms.date: 01/16/2020
ms.custom: mvc
ms.openlocfilehash: b2ce157f0f192135ab0507e4aae4c0a282bda1ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76166183"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>Spuštění postupu zotavení po havárii do sekundární oblasti pro virtuální počítače Azure

Služba [Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii provozní kontinuity a zotavení po havárii (BCDR) tím, že zajišťuje provoz a dostupnost obchodních aplikací během plánovaných i neplánovaných výpadků. Site Recovery spravuje a orchestruje zotavení po havárii místních počítačů a virtuálních počítačů Azure, včetně replikace, převzetí služeb při selhání a zotavení.

V tomto kurzu se dozvíte, jak pro virtuální počítač Azure spustit postup zotavení po havárii z jedné oblasti Azure do jiné s testovacím převzetím služeb při selhání. Postup ověří vaši strategii replikace bez ztráty dat, bez výpadku a bez dopadu na vaše produkční prostředí. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Kontrola požadavků
> * Spuštění testovacího převzetí služeb při selhání pro jeden virtuální počítač

> [!NOTE]
> Tento kurz vám pomůže udělat si podrobné pokyny pro zotavení po havárii s minimálními kroky. Další informace o různých funkcích týkajících se postupu při zotavení po havárii najdete v dokumentaci k [replikaci](azure-to-azure-how-to-enable-replication.md)virtuálních počítačů Azure, [síťové služby](azure-to-azure-about-networking.md), [automatizaci](azure-to-azure-powershell.md)nebo [řešení potíží](azure-to-azure-troubleshoot-errors.md).

## <a name="prerequisites"></a>Požadavky

Než se pustíte do tohoto kurzu, podívejte se na následující položky:

- Před spuštěním testovacího převzetí služeb při selhání doporučujeme zkontrolovat vlastnosti virtuálního počítače, abyste se ujistili, že je nakonfigurovaný pro zotavení po havárii. Pokud chcete **Operations**  >  Zobrazit vlastnosti replikace a převzetí služeb při selhání, přečtěte si vlastnosti**zotavení po havárii**virtuálního počítače  >  **Properties** .
- **Pro testovací převzetí služeb při selhání doporučujeme použít samostatnou síť virtuálních počítačů Azure**, a ne výchozí síť, kterou jste nastavili při povolování replikace.
- V závislosti na vašich zdrojových síťových konfiguracích pro každou síťovou kartu můžete zadat **podsíť**, **soukromou IP adresu**, **veřejnou IP**adresu, **skupinu zabezpečení sítě**nebo **Nástroj pro vyrovnávání zatížení** , aby se k jednotlivým síťovým kartám připojily v rámci nastavení testovacího převzetí služeb při selhání v části **výpočty a síť** .

## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

Tento příklad ukazuje, jak použít Recovery Services Trezor k provedení testovacího převzetí služeb při selhání virtuálního počítače.

1. Vyberte trezor, klikněte na **chráněné položky**  >  **replikované** položky a vyberte virtuální počítač.
1. V části **testovací převzetí služeb při selhání**vyberte bod obnovení, který chcete použít pro převzetí služeb při selhání:
   - **Nejnovější**: zpracovává všechna data v Site Recovery a poskytuje nejnižší RTO (cíl doby obnovení).
   - **Nejnovější zpracovaný**: Vrátí virtuální počítač k nejnovějšímu bodu obnovení zpracovanému službou Site Recovery. Zobrazí se časové razítko. S touto možností se nestráví zpracováním dat žádný čas, takže poskytuje nízkou RTO.
   - **Nejnovější konzistentní vzhledem k aplikacím**: Tato možnost vrátí všechny virtuální počítače k nejnovějšímu konzistentnímu bodu obnovení vzhledem k aplikacím. Zobrazí se časové razítko.
   - **Vlastní**: převzetí služeb při selhání konkrétním bodem obnovení. Vlastní je dostupná jenom v případě, že převezmete služby při selhání jednoho virtuálního počítače, a ne pro převzetí služeb při selhání s plánem obnovení
1. Vyberte cílovou virtuální síť Azure, ke které se virtuální počítače Azure v sekundární oblasti připojí po převzetí služeb při selhání.

   > [!NOTE]
   > Pokud jsou nastavení testovacího převzetí služeb při selhání předem nakonfigurovaná pro replikovanou položku, rozevírací nabídka pro výběr virtuální sítě Azure není viditelná.

1. Pokud chcete spustit převzetí služeb při selhání, vyberte **OK**. Pokud chcete sledovat průběh z trezoru, pokračujte na **sledování**  >  **Site Recovery úlohy** a vyberte úlohu **testovací převzetí služeb při selhání** .
1. Po dokončení převzetí služeb při selhání se virtuální počítač Azure repliky zobrazí v **Virtual Machines**Azure Portal. Ujistěte se, že je virtuální počítač spuštěný, má odpovídající velikost a je připojený k odpovídající síti.
1. Pokud chcete odstranit virtuální počítače vytvořené během testovacího převzetí služeb při selhání, vyberte **vyčistit testovací převzetí služeb při selhání** u replikované položky nebo plánu obnovení. V části **poznámky**si zaznamenejte a uložte všechny poznámky spojené s testovacím převzetím služeb při selhání.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Spuštění produkčního převzetí služeb při selhání](azure-to-azure-tutorial-failover-failback.md)
