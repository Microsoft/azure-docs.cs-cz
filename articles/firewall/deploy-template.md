---
title: Nasazení brány Azure Firewall pomocí šablony
description: Nasaďte Azure Firewall pomocí šablony. Vytvořená síť má jednu virtuální síť se třemi podsítěmi. Nasazují se dva dvoujádrové virtuální počítače se systémem Windows Server.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: b9e82998283bf44eede88148a12d88aae521b1ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74169201"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Nasazení brány Azure Firewall pomocí šablony

[Šablona nastavení izolovaného prostoru Vytvořit azurefirewall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox) vytvoří testovací síťové prostředí s bránou firewall. Síť má jednu virtuální síť (VNet) se třemi podsítěmi: *AzureFirewallSubnet*, *ServersSubnet*a *JumpboxSubnet*. Podsíť *ServersSubnet* a *JumpboxSubnet* mají jeden dvoujádrový virtuální počítač se systémem Windows Server.

Brána firewall je v podsíti *AzureFirewallSubnet* a má kolekci pravidel `www.microsoft.com`aplikace s jediným pravidlem, které umožňuje přístup k aplikaci .

Uživatelem definovaný síťový provoz bodů trasy z podsítě *ServersPodnet* prostřednictvím brány firewall, kde jsou použita pravidla brány firewall.

Další informace o Azure Firewall najdete [v tématu Nasazení a konfigurace Azure Firewall pomocí webu Azure Portal](tutorial-firewall-deploy-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>Nasazení Azure Firewall pomocí šablony

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

**Instalace a nasazení azure firewallu pomocí šablony:**

1. Přístup k [https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox)šabloně na adrese .
   
1. Přečtěte si úvod a až budete připraveni k nasazení, vyberte **Nasazení do Azure**.
   
1. Pokud je to potřeba, přihlaste se na Azure Portal. 

1. Na portálu na stránce **Vytvořit izolované ho dopískáče** na AzureFirewall zadejte nebo vyberte následující hodnoty:
   
   - **Skupina prostředků**: Vyberte **Vytvořit nový**, zadejte název skupiny prostředků a vyberte **OK**. 
   - **Název virtuální sítě**: Zadejte název nové virtuální sítě. 
   - **Uživatelské jméno správce**: Zadejte uživatelské jméno uživatelského účtu správce.
   - **Heslo správce**: Zadejte heslo správce. 
   
1. Přečtěte si podmínky a vyberte **Souhlasím s výše uvedenými podmínkami**.
   
1. Vyberte **Koupit**.
   
   Vytvoření prostředků bude trvat několik minut. 
   
1. Prozkoumejte prostředky, které byly vytvořeny pomocí brány firewall. 

Informace o syntaxi JSON a vlastnostech brány firewall v šabloně naleznete v tématu [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud je již nepotřebujete, můžete odebrat skupinu prostředků, bránu firewall a všechny související prostředky spuštěním příkazu [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) PowerShell. Chcete-li odebrat skupinu prostředků s názvem *MyResourceGroup*, spusťte: 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Pokud chcete pokračovat v kurzu sledování brány firewall, neodstraňujte ještě skupinu prostředků a bránu firewall. 

## <a name="next-steps"></a>Další kroky

Dále můžete sledovat protokoly azure firewall:

> [!div class="nextstepaction"]
> [Kurz: Monitorování protokolů brány Azure Firewall](./tutorial-diagnostics.md)
