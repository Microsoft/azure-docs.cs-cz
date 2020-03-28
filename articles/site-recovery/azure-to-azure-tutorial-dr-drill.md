---
title: Spuštění cvičení zotavení po havárii virtuálního počítače Azure s Azure Site Recovery
description: Zjistěte, jak spustit cvičení pro zotavení po havárii do sekundární oblasti pro virtuální počítače Azure pomocí služby Azure Site Recovery.
services: site-recovery
ms.topic: tutorial
ms.date: 01/16/2020
ms.custom: mvc
ms.openlocfilehash: b2ce157f0f192135ab0507e4aae4c0a282bda1ea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76166183"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>Spuštění cvičení pro zotavení po havárii do sekundární oblasti pro virtuální počítače Azure

Služba [Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii provozní kontinuity a zotavení po havárii (BCDR) tím, že zajišťuje provoz a dostupnost obchodních aplikací během plánovaných i neplánovaných výpadků. Site Recovery spravuje a orchestruje zotavení po havárii místních počítačů a virtuálních počítačů Azure, včetně replikace, převzetí služeb při selhání a zotavení.

V tomto kurzu se dozvíte, jak pro virtuální počítač Azure spustit postup zotavení po havárii z jedné oblasti Azure do jiné s testovacím převzetím služeb při selhání. Postup ověří vaši strategii replikace bez ztráty dat, bez výpadku a bez dopadu na vaše produkční prostředí. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Kontrola požadavků
> * Spuštění testovacího převzetí služeb při selhání pro jeden virtuální počítač

> [!NOTE]
> Tento kurz vám pomůže provést cvičení pro zotavení po havárii s minimálními kroky. Další informace o různých funkcích souvisejících s prováděním cvičení pro zotavení po havárii najdete v dokumentaci k [replikaci](azure-to-azure-how-to-enable-replication.md)virtuálních počítačích Azure , [k síti](azure-to-azure-about-networking.md), [automatizaci](azure-to-azure-powershell.md)nebo [řešení potíží](azure-to-azure-troubleshoot-errors.md).

## <a name="prerequisites"></a>Požadavky

Před tímto kurzem zkontrolujte následující položky:

- Před spuštěním testu převzetí služeb při selhání, doporučujeme zkontrolovat vlastnosti virtuálního počítače a ujistěte se, že je nakonfigurovaný pro zotavení po havárii. Přejděte na **vlastnosti zotavení** > **Disaster Recovery** > po**havárii** operací virtuálního počítače a zobrazte vlastnosti replikace a převzetí služeb při selhání.
- **Pro testovací převzetí služeb při selhání doporučujeme použít samostatnou síť virtuálních počítačů Azure**, a ne výchozí síť, kterou jste nastavili při povolování replikace.
- V závislosti na konfiguracích zdrojové sítě pro každou síťovou síť můžete před provedením cvičení pro zotavení po havárii zadat **podsíť**, **privátní IP adresu**, **veřejnou IP**, **skupinu zabezpečení sítě**nebo nástroj pro **vyrovnávání zatížení,** který chcete připojit ke každé síťové krychle v rámci testovacího nastavení převzetí služeb při selhání v **aplikaci Compute and Network.**

## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

Tento příklad ukazuje, jak pomocí trezoru služby Recovery Services provést převzetí služeb při selhání testu virtuálního provozu.

1. Vyberte úschovnu a přejděte na **Položky** > **replikované položky** chráněné a vyberte virtuální počítač.
1. V **části Test Failover**vyberte bod obnovení, který chcete použít pro převzetí služeb při selhání:
   - **Nejnovější**: Zpracuje všechna data v site recovery a poskytuje nejnižší RTO (Cíl doby zotavení).
   - **Nejnovější zpracovaný**: Vrátí virtuální počítač k nejnovějšímu bodu obnovení zpracovanému službou Site Recovery. Zobrazí se časové razítko. S touto volbou není čas strávený zpracování dat, takže poskytuje nízké RTO.
   - **Nejnovější konzistentní vzhledem k aplikacím**: Tato možnost vrátí všechny virtuální počítače k nejnovějšímu konzistentnímu bodu obnovení vzhledem k aplikacím. Zobrazí se časové razítko.
   - **Vlastní:** Převzetí služeb při selhání do konkrétního bodu obnovení. Vlastní je k dispozici pouze při převzetí služeb při selhání jednoho virtuálního pracovního systému a ne pro převzetí služeb při selhání s plánem obnovení.
1. Vyberte cílovou virtuální síť Azure, ke které se virtuální počítače Azure v sekundární oblasti připojí po převzetí služeb při selhání.

   > [!NOTE]
   > Pokud jsou nastavení převzetí služeb při selhání testu předem nakonfigurovaná pro replikovanou položku, rozbalovací nabídka pro výběr virtuální sítě Azure není viditelná.

1. Chcete-li spustit převzetí služeb při selhání, vyberte **možnost OK**. Chcete-li sledovat průběh z úložiště, přejděte na **úlohy monitorování** > **obnovení webu** a vyberte **úlohu test převzetí služeb při selhání.**
1. Po dokončení převzetí služeb při selhání se replika virtuálního počítače Azure zobrazí ve **virtuálních počítačích**na webu Azure Portal . Ujistěte se, že je virtuální počítač spuštěný, má odpovídající velikost a je připojený k odpovídající síti.
1. Chcete-li odstranit virtuální počítač, které byly vytvořeny během převzetí služeb při selhání testu testu testu **testu,** vyberte převzetí služeb při selhání testu vyčištění u replikované položky nebo plánu obnovení. V **poznámkách**zaznamenejte a uložte všechna pozorování spojená s převzetím služeb při selhání testu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Spuštění produkčního převzetí služeb při selhání](azure-to-azure-tutorial-failover-failback.md)
