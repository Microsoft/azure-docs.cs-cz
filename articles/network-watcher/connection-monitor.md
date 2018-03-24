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
ms.openlocfilehash: c7d98350dc8f66ebd4097f22b44dcbbe2653b25d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="monitor-network-connections-with-azure-network-watcher-using-the-azure-portal"></a>Monitorování připojení k síti s sledovací proces sítě Azure pomocí portálu Azure

Naučte se používat monitorování připojení k monitorování síťové připojení mezi virtuální počítač Azure a IP adresu. Monitorování připojení poskytuje sledování na úrovni připojení. Připojení je definován jako kombinace zdrojové a cílové IP adresy a portu. Monitorování připojení umožňuje scénáře, jako je monitorování připojení z virtuálního počítače ve virtuální síti do virtuálního počítače se systémem SQL server ve stejné nebo jiné virtuální síti, přes port 1433. Monitorování připojení poskytuje latence připojení Azure monitorování metriky zaznamenávají každých 60 sekund. Také poskytuje topologii směrování směrování a identifikuje problémy s konfigurací vašeho připojení, které mají vliv.


## <a name="prerequisites"></a>Požadavky

Před dokončením kroků v tomto článku, musí splňovat následující požadavky:

* Instance sledovací proces sítě v oblasti, kterou chcete sledovat pro připojení. Pokud jste již nemáte, můžete vytvořit jeden pomocí kroků v [vytvoření instance sledovací proces sítě Azure](network-watcher-create.md).
* Virtuální počítač z monitorování.
* Máte `AzureNetworkWatcherExtension` nainstalovat na virtuální počítače, kterou chcete sledovat připojení z. Chcete-li nainstalovat rozšíření virtuálního počítače s Windows, přečtěte si téma [rozšíření virtuálního počítače Azure sítě sledovacích procesů agenta pro Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) a nainstalovat rozšíření najdete v tématu virtuálního počítače Linux [Azure sítě sledovacích procesů agenta rozšíření virtuálního počítače pro Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k [portálu Azure](http://portal.azure.com).

## <a name="create-a-connection-monitor"></a>Vytvoření připojení monitorování

Následující kroky povolí připojení monitorování cílového virtuálního počítače přes porty 80 a 1433:

1. Na levé straně na portálu, vyberte **další služby**.
2. Začněte psát *sledovací proces sítě*. Když **sledovací proces sítě** se zobrazí ve výsledcích hledání, vyberte ho.
3. V části **monitorování**, vyberte **monitorování připojení (Preview)**. Funkce ve verzi preview nemají stejnou úroveň spolehlivosti nebo dostupnost v oblastech jako funkce v hlavní verze.
4. Vyberte **+ přidat**.
5. Zadejte nebo vyberte informace pro připojení, které chcete monitorovat a potom vyberte **přidat**. V příkladu znázorněno na následujícím obrázku je sledovat připojení mezi *MultiTierApp0* a *Database0* virtuální počítače:

    ![Přidat monitorování připojení](./media/connection-monitor/add-connection-monitor.png)

    Monitorování začne. Monitorování připojení sondy každých 60 sekund.

## <a name="view-connection-monitoring"></a>Zobrazení monitorování připojení

1. Dokončete kroky 1 – 3 [vytvořit monitorování připojení](#create-a-connection-monitor) zobrazení monitorování připojení.
2. Následující obrázek znázorňuje podrobnosti AppToDB(80) připojení. **Stav** je dostupný. **Graf zobrazení** ukazuje **Průměrná doba odezvy** a **sondy se nezdařilo %**. Graf poskytuje informace o směrování směrování a ukazuje, že žádné problémy, které mají vliv cílové dostupnosti.

    ![Zobrazení monitorování připojení](./media/connection-monitor/view-connection-monitor.png)

3. Zobrazení *AppToDB(1433)* monitorování, vidět na následujícím obrázku, uvidíte, že pro stejný zdroj a cílovým virtuálním počítačům, stav nedosažitelný přes port 1433. **Zobrazení mřížky** v tomto scénáři poskytuje informace o směrování směrování a problému, dostupnosti, které mají vliv. V takovém případě pravidlo NSG blokuje veškerý provoz na portu 1433 v druhé směrování.

    ![Zobrazení monitorování připojení](./media/connection-monitor/view-connection-monitor-2.png)

## <a name="next-steps"></a>Další postup

- Informace o automatizaci paketu zachytává se virtuální počítač výstrahy podle [vytváření zachycení aktivuje výstraha paketu](network-watcher-alert-triggered-packet-capture.md).
- Určení, zda některé provoz povolen v nebo z virtuálního počítače pomocí [IP tok ověření](network-watcher-check-ip-flow-verify-portal.md).