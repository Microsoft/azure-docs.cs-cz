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
ms.openlocfilehash: 7f2525b89f03e8bc1a2c3166b46c40b4dbb6ff17
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99562007"
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
* **IP adresa klienta (spřažení zdrojové IP adresy 2 – řazená kolekce členů)** – určuje, že úspěšné požadavky ze stejné IP adresy klienta budou zpracovávány stejným virtuálním počítačem.
* **IP adresa klienta a protokol (přidružení zdrojové IP adresy 3 – řazená kolekce členů)** – určuje, že po jednom virtuálním počítači bude zpracována úspěšná žádost ze stejné kombinace IP adresy klienta a protokolu.

5. Zvolte režim distribuce a potom vyberte **Uložit**.

:::image type="content" source="./media/load-balancer-distribution-mode/session-persistence.png" alt-text="Změna trvalosti relace pro pravidlo nástroje pro vyrovnávání zatížení." border="true":::


# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Použijte PowerShell ke změně nastavení distribuce nástroje pro vyrovnávání zatížení pro existující pravidlo vyrovnávání zatížení. Následující příkaz aktualizuje distribuční režim: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLoadBalancer -ResourceGroupName MyResourceGroupLB
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

Nastavte hodnotu `LoadDistribution` prvku pro požadovanou velikost vyrovnávání zatížení. 

Zadejte **sourceIP** pro vyrovnávání zatížení se dvěma řazenými kolekcemi členů (zdrojová IP adresa a cílová IP adresa). 

Pro vyrovnávání zatížení zadejte **sourceIPProtocol** pro tři řazené kolekce členů (zdrojová adresa IP, cílová IP adresa a typ protokolu). 

Zadejte **výchozí** hodnotu pro výchozí chování pro vyrovnávání zatížení s pěti řazenými kolekcemi členů.

---

## <a name="next-steps"></a>Další kroky

* [Azure Load Balancer – přehled](load-balancer-overview.md)
* [Začínáme s konfigurací internetového nástroje pro vyrovnávání zatížení](quickstart-load-balancer-standard-public-powershell.md)
* [Konfigurace nastavení časového limitu nečinnosti protokolu TCP pro nástroj pro vyrovnávání zatížení](load-balancer-tcp-idle-timeout.md)