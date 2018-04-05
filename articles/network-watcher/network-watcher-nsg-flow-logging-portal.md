---
title: Správa sítě zabezpečení skupiny toku protokoly s sledovací proces sítě Azure | Microsoft Docs
description: Tato stránka vysvětluje, jak spravovat protokoly toku skupinu zabezpečení sítě v sledovací proces sítě Azure
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: cb41781c5ac8fb759cecea01402c08dd716bf7d7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="manage-network-security-group-flow-logs-in-the-azure-portal"></a>Správa sítě zabezpečení skupiny toku protokoly na portálu Azure

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
> - [CLI 2.0](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Protokoly toku skupiny zabezpečení sítě jsou funkce sledovací proces sítě, která umožňuje zobrazit informace o příchozí a odchozí provoz IP prostřednictvím skupinu zabezpečení sítě. Tyto protokoly toku jsou zapsané ve formátu JSON a obsahují důležité informace, včetně: 

- Odchozí a příchozí tok na základě pravidla.
- Síťové adaptéry, které toku platí pro.
- 5 řazené kolekce členů informace o toku (zdrojové nebo cílové adresy IP, zdrojový nebo cílový port, protokol).
- Informace o tom, jestli je povolené nebo zakázané přenosy.

## <a name="before-you-begin"></a>Než začnete

Pokud chcete provést kroky v tomto článku, je nutné v následujících zdrojích informací:

- Existující sledovací proces sítě. Vytvoření sledovací proces sítě naleznete v tématu [vytvořit instanci sledovací proces sítě](network-watcher-create.md).
- Existující skupinu prostředků s platným virtuálním počítačem. Pokud nemáte virtuálního počítače, najdete v části vytvořit [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) nebo [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) virtuálního počítače.

## <a name="register-insights-provider"></a>Registrace zprostředkovatele statistiky

Pro tok protokolování nemusí fungovat správně **Microsoft.Insights** zprostředkovatele musí být zaregistrován. K registraci poskytovatele, proveďte následující kroky: 

1. Přejděte na **odběry**a potom vyberte předplatné, pro který chcete povolit protokoly toku. 
2. Na **předplatné** vyberte **zprostředkovatelé prostředků**. 
3. Podívejte se na seznam zprostředkovatelů a ověřte, zda **microsoft.insights** zprostředkovatel registroval. Pokud ne, pak vyberte **zaregistrovat**.

![Zprostředkovatelé zobrazení][providers]

## <a name="enable-flow-logs"></a>Povolení toku protokolů

Tyto kroky vás provede procesem povolování toku protokoly na skupinu zabezpečení sítě.

### <a name="step-1"></a>Krok 1

Přejděte do instance sledovací proces sítě a potom vyberte **NSG toku protokoly**.

![Přehled protokoly toku][1]

### <a name="step-2"></a>Krok 2

Ze seznamu vyberte skupinu zabezpečení sítě.

![Přehled protokoly toku][2]

### <a name="step-3"></a>Krok 3 

Na **tok se nastavení protokolů** okno, nastaví stav na **na**a pak nakonfigurujte účet úložiště. Vyberte existující účet úložiště, který má **všechny sítě** (výchozí) vybraný v seznamu **brány firewall a virtuální sítě**v části **nastavení** pro účet úložiště. Po dokončení výběru účtu úložiště, vyberte **OK**a potom vyberte **Uložit**.

![Přehled protokoly toku][3]

## <a name="download-flow-logs"></a>Stažení protokolů o toku

Tok protokoly se ukládají do účtu úložiště. Stažení protokolů toku k jejich zobrazení.

### <a name="step-1"></a>Krok 1

Ke stažení protokolů toku, vyberte **toku protokoly si můžete stáhnout z účtů úložiště**. Tento krok přejdete na zobrazení účtu úložiště kde si můžete vybrat, které protokoly ke stažení.

![Nastavení protokolů toku][4]

### <a name="step-2"></a>Krok 2

Přejděte k účtu úložiště správné. Potom vyberte **kontejnery** > **insights-log-networksecuritygroupflowevent**.

![Nastavení protokolů toku][5]

### <a name="step-3"></a>Krok 3

Přejděte do umístění protokolu toku, vyberte ji a potom vyberte **Stáhnout**.

![Nastavení protokolů toku][6]

Informace o struktuře protokolu najdete v článku [přehled protokolu toku skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md).

## <a name="next-steps"></a>Další postup

Zjistěte, jak [vizualizovat protokolů NSG toku pomocí PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md).

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-portal/figure1.png
[2]: ./media/network-watcher-nsg-flow-logging-portal/figure2.png
[3]: ./media/network-watcher-nsg-flow-logging-portal/figure3.png
[4]: ./media/network-watcher-nsg-flow-logging-portal/figure4.png
[5]: ./media/network-watcher-nsg-flow-logging-portal/figure5.png
[6]: ./media/network-watcher-nsg-flow-logging-portal/figure6.png
[providers]: ./media/network-watcher-nsg-flow-logging-portal/providers.png
