---
title: Konfigurace resetování TCP pro vyrovnávání zatížení a časový limit nečinnosti
titleSuffix: Azure Load Balancer
description: V tomto článku se dozvíte, jak nakonfigurovat Azure Load Balancer časový limit nečinnosti protokolu TCP a resetování.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2020
ms.author: allensu
ms.openlocfilehash: 8a6be588544883b77c3ff115c9dba5e6ecd5fbd7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92747180"
---
# <a name="configure-tcp-reset-and-idle-timeout-for-azure-load-balancer"></a>Konfigurace resetování TCP a vypršení časového limitu nečinnosti pro Azure Load Balancer

Azure Load Balancer má následující rozsah časového limitu nečinnosti:

* 4 minuty až 100 minut pro odchozí pravidla
* 4 minuty až 30 minut pro pravidla Load Balancer a příchozí pravidla NAT

Ve výchozím nastavení je nastaveno na 4 minuty. Pokud je doba nečinnosti delší než hodnota časového limitu, není zaručena údržba relace TCP nebo HTTP mezi klientem a službou. 

Následující části popisují, jak změnit nastavení časového limitu nečinnosti a resetování TCP pro prostředky nástroje pro vyrovnávání zatížení.

## <a name="set-tcp-reset-and-idle-timeout"></a>Nastavit resetování TCP a časový limit nečinnosti
---
# <a name="portal"></a>[**Portál**](#tab/tcp-reset-idle-portal)

Pokud chcete nastavit časový limit nečinnosti a resetování TCP pro nástroj pro vyrovnávání zatížení, upravte pravidlo s vyrovnáváním zatížení. 

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

2. V nabídce na levé straně vyberte **skupiny prostředků**.

3. Vyberte skupinu prostředků pro nástroj pro vyrovnávání zatížení. V tomto příkladu skupina prostředků má název **myResourceGroup**.

4. Vyberte svůj Load Balancer. V tomto příkladu se nástroj pro vyrovnávání zatížení nazývá **myLoadBalancer**.

5. V **Nastavení** vyberte **pravidla vyrovnávání zatížení**.

     :::image type="content" source="./media/load-balancer-tcp-idle-timeout/portal-lb-rules.png" alt-text="Upravit pravidla nástroje pro vyrovnávání zatížení." border="true":::

6. Vyberte pravidlo vyrovnávání zatížení. V tomto příkladu se pravidlo vyrovnávání zatížení nazývá **myLBrule**.

7. V pravidle vyrovnávání zatížení přesuňte posuvník v poli **časový limit nečinnosti (minuty)** na hodnotu časového limitu.  

8. V části **resetovat TCP** vyberte **povoleno**.

   :::image type="content" source="./media/load-balancer-tcp-idle-timeout/portal-lb-rules-tcp-reset.png" alt-text="Nastavte časový limit nečinnosti a resetování TCP." border="true":::

9. Vyberte **Uložit**.

# <a name="powershell"></a>[**PowerShell**](#tab/tcp-reset-idle-powershell)

Nastavení časového limitu nečinnosti a resetování TCP nastavíte tak, že nastavíte hodnoty v následujících parametrech pravidla vyrovnávání zatížení pomocí [set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

* **IdleTimeoutInMinutes**
* **EnableTcpReset**

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte prostředí PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

Nahraďte následující příklady hodnotami z vašich prostředků:

* **myResourceGroup**
* **myLoadBalancer**

```azurepowershell
$lb = Get-AzLoadBalancer -Name "myLoadBalancer" -ResourceGroup "myResourceGroup"
$lb.LoadBalancingRules[0].IdleTimeoutInMinutes = '15'
$lb.LoadBalancingRules[0].EnableTcpReset = 'true'
Set-AzLoadBalancer -LoadBalancer $lb
```

# <a name="azure-cli"></a>[**Azure CLI**](#tab/tcp-reset-idle-cli)

Pokud chcete nastavit časový limit nečinnosti a resetování TCP, použijte následující parametry pro [AZ Network Rule Update](/cli/azure/network/lb/rule?az_network_lb_rule_update):

* **--nečinný – časový limit**
* **--Enable-TCP-reset**

Než začnete, ověřte si prostředí:

* Přihlaste se k Azure Portal a ověřte, že je vaše předplatné aktivní spuštěním `az login` .
* Podívejte se na verzi rozhraní příkazového řádku Azure CLI v terminálu nebo příkazovém okně spuštěním příkazu `az --version` . Nejnovější verzi najdete v [poznámkách k nejnovější verzi](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Pokud nemáte nejnovější verzi, aktualizujte instalaci pomocí [instalační příručky pro váš operační systém nebo platformu](/cli/azure/install-azure-cli).

Nahraďte následující příklady hodnotami z vašich prostředků:

* **myResourceGroup**
* **myLoadBalancer**
* **myLBrule**


```azurecli
az network lb rule update \
    --resource-group myResourceGroup \
    --name myLBrule \
    --lb-name myLoadBalancer \
    --idle-timeout 15 \
    --enable-tcp-reset true
```
---
## <a name="next-steps"></a>Další kroky

Další informace o vypršení časového limitu nečinnosti protokolu TCP a resetování najdete v tématu [Load Balancer obnovení TCP a časový limit](load-balancer-tcp-reset.md)

Další informace o konfiguraci distribučního režimu nástroje pro vyrovnávání zatížení najdete v tématu [Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení](load-balancer-distribution-mode.md).
