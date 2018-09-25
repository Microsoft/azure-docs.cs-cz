---
title: Brána Firewall služby Azure pomocí šablony nasazení
description: Brána Firewall služby Azure pomocí šablony nasazení
services: firewall
author: vhorne
manager: jpconnock
ms.service: firewall
ms.topic: article
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: d32e6e29c287d140c28206743e36dc025b26158b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991330"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Brána Firewall služby Azure pomocí šablony nasazení

Tato šablona vytvoří bránu firewall a testovací síťové prostředí. Síť má jednu virtuální síť se třemi podsítěmi: *AzureFirewallSubnet*, *ServersSubnet*a *JumpboxSubnet*. Podsítě ServersSubnet a JumpboxSubnet mají každá jeden dvoujádrový Windows Server.

Brána firewall je v podsíti AzureFirewallSubnet a je nakonfigurovaná kolekcí pravidel aplikace s jediným pravidlem, které umožňuje přístup k www.microsoft.com.

Vytvoří se trasa definovaná uživatelem, která odkazuje síťový provoz z podsítě ServersSubnet přes bránu firewall, kde se používají její pravidla.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="template-location"></a>Umístění šablony.

Šablona se nachází na:

[https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox)

Čtení zavedení a až budete připraveni k nasazení, klikněte na tlačítko **nasadit do Azure**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejdřív seznámit se s informačními, které byly vytvořeny pomocí brány firewall a když už je nepotřebujete, můžete použít [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) příkazu odeberte skupinu prostředků, brány firewall a všech souvisejících prostředků.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```
## <a name="next-steps"></a>Další postup

Dále můžete monitorovat protokoly brány Firewall Azure:

- [Kurz: Monitorování protokolů brány Azure Firewall](./tutorial-diagnostics.md)

