---
title: Konfigurace distribučního režimu Azure Load Balancer
titleSuffix: Azure Load Balancer
description: V tomto článku začnete s konfigurací distribučního režimu pro Azure Load Balancer podporovat spřažení zdrojové IP adresy.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2021
ms.author: allensu
ms.openlocfilehash: 2d8d5d84d32cdb8cc813d033f3f3fbb453b538fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739912"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Konfigurace režimu distribuce pro Azure Load Balancer

Azure Load Balancer podporuje dva režimy distribuce pro distribuci provozu do aplikací:

* Založený na hodnotě hash
* Spřažení se zdrojovou IP adresou

V tomto článku se dozvíte, jak nakonfigurovat distribuční režim pro Azure Load Balancer.


## <a name="configure-distribution-mode"></a>Konfigurace distribučního režimu

---

# <a name="azure-portal"></a>[**Azure Portal**](#tab/azure-portal)

Konfiguraci režimu distribuce můžete změnit úpravou pravidla vyrovnávání zatížení na portálu.

1. Přihlaste se k Azure Portal a vyhledejte skupinu prostředků obsahující nástroj pro vyrovnávání zatížení, který chcete změnit kliknutím na **skupiny prostředků**.
2. Na obrazovce Přehled nástroje pro vyrovnávání zatížení vyberte v části **Nastavení** možnost **pravidla vyrovnávání zatížení** .
3. Na obrazovce pravidla vyrovnávání zatížení vyberte pravidlo vyrovnávání zatížení, u kterého chcete změnit režim distribuce.
4. V rámci pravidla se režim distribuce změní změnou rozevíracího seznamu **trvalá relace** . 

Dostupné jsou tyto možnosti: 

* **Žádný (založený na hodnotě hash)** – určuje, že úspěšné požadavky ze stejného klienta můžou být zpracovávány jakýmkoli virtuálním počítačem.
* **IP adresa klienta (vztah zdrojové IP adresy – kořazená kolekce členů)** – určuje, že úspěšné požadavky ze stejné IP adresy klienta budou zpracovávány stejným virtuálním počítačem.
* **IP adresa klienta a protokol (přidružení zdrojové IP adresy – tři – řazená kolekce členů)** – určuje, že na stejném virtuálním počítači bude zpracována následná žádost ze stejné kombinace IP adresy klienta a protokolu.

5. Zvolte režim distribuce a potom vyberte **Uložit**.

:::image type="content" source="./media/load-balancer-distribution-mode/session-persistence.png" alt-text="Změna trvalosti relace pro pravidlo nástroje pro vyrovnávání zatížení." border="true":::


# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Použijte PowerShell ke změně nastavení distribuce nástroje pro vyrovnávání zatížení pro existující pravidlo vyrovnávání zatížení. Následující příkaz aktualizuje distribuční režim: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLoadBalancer -ResourceGroupName MyResourceGroupLB
$lb.LoadBalancingRules[0].LoadDistribution = 'default'
Set-AzLoadBalancer -LoadBalancer $lb
```

Nastavte hodnotu `LoadDistribution` elementu pro typ vyrovnávání zatížení, který je povinný. 

* Zadejte **SourceIP** pro vyrovnávání zatížení se dvěma řazenými kolekcemi členů (zdrojová IP adresa a cílová IP adresa). 

* Pro vyrovnávání zatížení zadejte **SourceIPProtocol** pro tři řazené kolekce členů (zdrojová adresa IP, cílová IP adresa a typ protokolu). 

* Zadejte **výchozí** hodnotu pro výchozí chování pro vyrovnávání zatížení s pěti řazenými kolekcemi členů.

# <a name="cli"></a>[**Rozhraní příkazového řádku**](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

Pomocí Azure CLI změňte nastavení distribuce nástroje pro vyrovnávání zatížení pro existující pravidlo vyrovnávání zatížení.  Následující příkaz aktualizuje distribuční režim:

```azurecli-interactive
az network lb rule update \
    --lb-name myLoadBalancer \
    --load-distribution Default \
    --name myHTTPRule \
    --resource-group myResourceGroupLB 
```
Nastavte hodnotu `--load-distribution` pro typ vyrovnávání zatížení, který je povinný.

* Zadejte **SourceIP** pro vyrovnávání zatížení se dvěma řazenými kolekcemi členů (zdrojová IP adresa a cílová IP adresa). 

* Pro vyrovnávání zatížení zadejte **SourceIPProtocol** pro tři řazené kolekce členů (zdrojová adresa IP, cílová IP adresa a typ protokolu). 

* Zadejte **výchozí** hodnotu pro výchozí chování pro vyrovnávání zatížení s pěti řazenými kolekcemi členů.

Další informace o příkazu, který se používá v tomto článku, najdete v tématu [AZ Network Rule Update](/cli/azure/network/lb/rule#az_network_lb_rule_update) .

---

## <a name="next-steps"></a>Další kroky

* [Azure Load Balancer – přehled](load-balancer-overview.md)
* [Začínáme s konfigurací internetového nástroje pro vyrovnávání zatížení](quickstart-load-balancer-standard-public-powershell.md)
* [Konfigurace nastavení časového limitu nečinnosti protokolu TCP pro nástroj pro vyrovnávání zatížení](load-balancer-tcp-idle-timeout.md)