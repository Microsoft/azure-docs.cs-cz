---
title: Sledování síťových připojení k Azure sledovací proces sítě - portálu Azure | Microsoft Docs
description: Naučte se monitorovat připojení k síti s sledovací proces sítě Azure pomocí portálu Azure.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2018
ms.author: jdial
ms.openlocfilehash: 0d550d3bda119cfcb9ecc6f852006d5e325fdfa3
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="monitor-network-connections-with-azure-network-watcher-using-the-azure-portal"></a>Monitorování připojení k síti s sledovací proces sítě Azure pomocí portálu Azure

Naučte se používat monitorování připojení k monitorování síťové připojení mezi Azure virtuálního počítače (VM) a IP adresu. Monitorování připojení poskytuje sledování mezi zdrojovou a cílovou IP adresu a port. Monitorování připojení umožňuje scénáře, jako je monitorování připojení z virtuálního počítače ve virtuální síti na virtuální počítač spuštěný SQL Server ve stejné nebo jiné virtuální síti, přes port 1433. Monitorování připojení poskytuje latence připojení Azure monitorování metriky zaznamenávají každých 60 sekund. Také poskytuje topologii směrování směrování a identifikuje problémy s konfigurací vašeho připojení, které mají vliv.

## <a name="prerequisites"></a>Požadavky

Před dokončením kroků v tomto článku, musí splňovat následující požadavky:

* Instance sledovací proces sítě v oblasti, kterou chcete sledovat pro připojení. Pokud jste již nemáte, můžete vytvořit jeden pomocí kroků v [vytvoření instance sledovací proces sítě Azure](network-watcher-create.md).
* Virtuální počítač z monitorování. Postup vytvoření virtuálního počítače, najdete v části vytvořit [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) nebo [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) virtuálních počítačů.
* Máte `AzureNetworkWatcherExtension` nainstalovaná ve virtuálním počítači, který chcete sledovat připojení z. Instalaci rozšíření ve virtuálním počítači Windows naleznete v tématu [rozšíření virtuálního počítače Azure sítě sledovacích procesů agenta pro Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) a nainstalovat rozšíření najdete v tématu virtuálního počítače s Linuxem [rozšíření virtuálního počítače Azure sítě sledovacích procesů agenta pro Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k [portálu Azure](http://portal.azure.com).

## <a name="create-a-connection-monitor"></a>Vytvoření připojení monitorování

Následující kroky povolí monitorování připojení do cílového umístění virtuálních počítačů přes porty 80 a 1433:

1. Na levé straně na portálu, vyberte **všechny služby**.
2. Začněte psát *sledovací proces sítě* v **filtru** pole. Když **sledovací proces sítě** se zobrazí ve výsledcích hledání, vyberte ho.
3. V části **monitorování**, vyberte **monitorování připojení**.
4. Vyberte **+ přidat**.
5. Zadejte nebo vyberte informace pro připojení, které chcete monitorovat a potom vyberte **přidat**. V příkladu znázorněno na následujícím obrázku je sledovat připojení z *MultiTierApp0* virtuálního počítače *Database0* virtuálních počítačů přes port 80:

    ![Přidat monitorování připojení](./media/connection-monitor/add-connection-monitor.png)

    Monitorování začne. Monitorování připojení sondy každých 60 sekund.
6. Dokončete krok 5 opakujte, zadání stejné zdrojové a cílové virtuální počítače a následující hodnoty:
    
    |Nastavení  |Hodnota          |
    |---------|---------      |
    |Název     | AppToDB(1433) |
    |Port     | 1433          |

## <a name="view-connection-monitoring"></a>Zobrazení monitorování připojení

1. Dokončete kroky 1 – 3 [vytvořit monitorování připojení](#create-a-connection-monitor) zobrazení monitorování připojení.
2. Následující obrázek znázorňuje podrobnosti *AppToDB(80)* připojení. **Stav** je dostupný. **Graf zobrazení** ukazuje **Průměrná doba odezvy** a **sondy se nezdařilo %**. Graf poskytuje informace o směrování směrování a ukazuje, že žádné problémy, které mají vliv cílové dostupnosti.

    ![Zobrazení grafu](./media/connection-monitor/view-graph.png)

3. Zobrazení *AppToDB(1433)* připojení vidět na následujícím obrázku vidíte, že stejné zdrojové a cílové virtuální počítače, stav nedosažitelný přes port 1433. **Zobrazení mřížky** v tomto scénáři poskytuje informace o směrování směrování a problému, dostupnosti, které mají vliv. V takovém případě pravidlo NSG blokuje veškerý provoz na portu 1433 v druhé směrování.

    ![Zobrazení mřížky](./media/connection-monitor/view-grid.png)

## <a name="next-steps"></a>Další postup

- Informace o automatizaci paketu zachytává se virtuální počítač výstrahy podle [vytváření zachycení aktivuje výstraha paketu](network-watcher-alert-triggered-packet-capture.md).
- Určení, zda některé provoz povolen v nebo z virtuálního počítače pomocí [IP tok ověření](network-watcher-check-ip-flow-verify-portal.md).