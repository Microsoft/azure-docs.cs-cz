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
ms.openlocfilehash: 1a732e22d72c36afe11030e42bae529baa35df1a
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991188"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Brána Firewall služby Azure pomocí šablony nasazení

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

V příkladech v článcích Brána Firewall služby Azure se předpokládá, které jste povolili verze preview pro veřejnost Brána Firewall služby Azure. Další informace najdete v tématu [povolení verze preview pro veřejnost Brána Firewall služby Azure](public-preview.md).

Tato šablona vytvoří bránu firewall a testovací síťové prostředí. Síť má jednu virtuální síť se třemi podsítěmi: *AzureFirewallSubnet*, *ServersSubnet*a *JumpboxSubnet*. ServersSubnet a JumpboxSubnet mít jeden Server systému Windows 2 jádry v nich.

Brána firewall je ve AzureFirewallSubnet a je nakonfigurován s kolekcí pravidel aplikace s jedním pravidlem, které umožňuje přístup k www.microsoft.com.

Uživatelem definovaná trasa se vytvoří, který odkazuje síťový provoz z ServersSubnet přes bránu firewall, ve kterém jsou použita pravidla brány firewall.

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

- [Kurz: Brána Firewall služby Azure Monitor protokoly](./tutorial-diagnostics.md)

