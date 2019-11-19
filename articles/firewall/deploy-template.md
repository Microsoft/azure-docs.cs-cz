---
title: Nasazení Azure Firewall pomocí šablony
description: Nasaďte Azure Firewall pomocí šablony. Vytvořená síť má jednu virtuální síť se třemi podsítěmi. Nasadí se dva základní virtuální počítače s Windows serverem.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: b9e82998283bf44eede88148a12d88aae521b1ca
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74169201"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Nasazení Azure Firewall pomocí šablony

[Šablona nastavení izolovaného prostoru pro vytváření AzureFirewall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox) vytvoří testovací síťové prostředí s bránou firewall. Síť má jednu virtuální síť se třemi podsítěmi: *AzureFirewallSubnet*, *ServersSubnet*a *JumpboxSubnet*. Každá podsíť *ServersSubnet* a *JumpboxSubnet* má jeden virtuální počítač se dvěma jádry Windows serveru.

Brána firewall je v podsíti *AzureFirewallSubnet* a má kolekci pravidel aplikace s jedním pravidlem, které umožňuje přístup k `www.microsoft.com`.

Uživatelem definovaná trasa směrování v síti z podsítě *ServersSubnet* přes bránu firewall, kde se používají pravidla brány firewall.

Další informace o Azure Firewall najdete v tématu [nasazení a konfigurace Azure firewall použití Azure Portal](tutorial-firewall-deploy-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>Použití šablony k nasazení Azure Firewall

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

**Instalace a nasazení Azure Firewall pomocí šablony:**

1. Přístup k šabloně na [https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox).
   
1. Přečtěte si Úvod a po jeho přípravě na nasazení vyberte **nasadit do Azure**.
   
1. V případě potřeby se přihlaste k Azure Portal. 

1. Na portálu na stránce **Vytvoření nastavení izolovaného prostoru (AzureFirewall)** zadejte nebo vyberte následující hodnoty:
   
   - **Skupina prostředků**: vyberte **vytvořit novou**, zadejte název skupiny prostředků a vyberte **OK**. 
   - **Virtual Network název**: zadejte název nové virtuální sítě. 
   - **Uživatelské jméno správce**: zadejte uživatelské jméno pro uživatelský účet správce.
   - **Heslo správce**: zadejte heslo správce. 
   
1. Přečtěte si podmínky a ujednání a potom vyberte Souhlasím **s výše uvedenými podmínkami a ujednáními**.
   
1. Vyberte **Koupit**.
   
   Vytvoření prostředků bude trvat několik minut. 
   
1. Prozkoumejte prostředky, které byly vytvořeny pomocí brány firewall. 

Další informace o syntaxi a vlastnostech JSON pro bránu firewall v šabloně najdete v článku [Microsoft. Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud je už nepotřebujete, můžete skupinu prostředků, bránu firewall a všechny související prostředky odebrat spuštěním příkazu [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) prostředí PowerShell. Pokud chcete odebrat skupinu prostředků s názvem *MyResourceGroup*, spusťte: 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Tuto skupinu prostředků a bránu firewall zatím neodstraňujte, pokud máte v plánu pokračovat na kurz monitorování brány firewall. 

## <a name="next-steps"></a>Další kroky

Dále můžete monitorovat protokoly Azure Firewall:

> [!div class="nextstepaction"]
> [Kurz: Monitorování protokolů brány Azure Firewall](./tutorial-diagnostics.md)
