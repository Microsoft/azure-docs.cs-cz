---
title: Brána Firewall služby Azure pomocí šablony nasazení
description: Brána Firewall služby Azure pomocí šablony nasazení
services: firewall
author: vhorne
manager: jpconnock
ms.service: firewall
ms.topic: article
ms.date: 12/01/2018
ms.author: victorh
ms.openlocfilehash: 86fdbbacf3e8064afe0aaaaebea1d6ef6c25f9d4
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52865820"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Brána Firewall služby Azure pomocí šablony nasazení

[AzureFirewall vytvoření izolovaného prostoru nastavení šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox) vytvoří testovací síťové prostředí s bránou firewall. Síť má jednu virtuální síť (VNet) se třemi podsítěmi: *AzureFirewallSubnet*, *ServersSubnet*, a *JumpboxSubnet*. *ServersSubnet* a *JumpboxSubnet* podsítě jednotlivých mít jednu, dvě jádra serveru systému Windows virtuální počítač.

Brána firewall je ve *AzureFirewallSubnet* podsítě, a mají kolekci pravidel aplikace s jedním pravidlem, které umožňuje přístup k *www.microsoft.com*.

Trasy definované uživatelem odkazuje síťového provozu z *ServersSubnet* podsítě přes bránu firewall, ve kterém jsou použita pravidla brány firewall.

Další informace o bráně Firewall Azure najdete v tématu [nasazení a konfiguraci brány Firewall Azure pomocí webu Azure portal](tutorial-firewall-deploy-portal.md).

## <a name="use-the-template-to-deploy-azure-firewall"></a>Šablona pro nasazení brány Firewall na Azure

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

**Při instalaci a nasazení Brána Firewall služby Azure pomocí šablony:**

1. Přístup k šabloně na [ https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox).
   
1. Přečtěte si zavedení a až budete připraveni k nasazení, vyberte **nasadit do Azure**.
   
1. V případě potřeby, přihlaste se k webu Azure portal. 

1. Na portálu na **vytvoření izolovaného prostoru nastavení AzureFirewall** stránky zadejte nebo vyberte následující hodnoty:
   
   - **Skupina prostředků**: vyberte **vytvořit nový**, zadejte název pro skupinu prostředků a vyberte **OK**. 
   - **Název virtuální sítě**: Zadejte název nové sítě VNet. 
   - **Uživatelské jméno správce**: Zadejte uživatelské jméno pro účet správce systému.
   - **Heslo správce**: Zadejte heslo správce. 
   
1. Přečtěte si podmínky a ujednání a potom vyberte **vyjadřuji souhlas s podmínkami a ujednáními uvedenými nahoře**.
   
1. Vyberte **Koupit**.
   
   Bude trvat několik minut, než vytvoříte prostředky. 
   
1. Materiály, které byly vytvořeny pomocí brány firewall. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už je budete potřebovat, můžete odebrat skupinu prostředků, brány firewall a všech souvisejících prostředků spuštěním [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) příkaz prostředí PowerShell. Chcete-li odebrat skupinu prostředků s názvem *MyResourceGroup*, spusťte: 

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name MyResourceGroup
```
Neodebírat skupinu prostředků a brány firewall, pokud budete chtít pokračovat ke kurzu monitorování brány firewall. 

## <a name="next-steps"></a>Další postup

Dále můžete monitorovat protokoly brány Firewall Azure:

> [!div class="nextstepaction"]
> [Kurz: Monitorování protokolů brány Azure Firewall](./tutorial-diagnostics.md)
