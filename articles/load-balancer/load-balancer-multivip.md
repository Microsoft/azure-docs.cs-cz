---
title: Více VIP pro cloudovou službu
titlesuffix: Azure Load Balancer
description: Přehled nevyužívá a postup nastavení více virtuálních IP adres v cloudové službě
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 3e97bea85d4d97b159168b21b4a6e932e655ccfb
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274695"
---
# <a name="configure-multiple-vips-for-a-cloud-service"></a>Konfigurace více virtuálních IP adres pro cloudovou službu

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

K cloudovým službám Azure můžete přistupovat přes veřejný Internet pomocí IP adresy poskytované Azure. Tato veřejná IP adresa se označuje jako virtuální IP adresa (VIP), protože je propojená s nástrojem pro vyrovnávání zatížení Azure, a ne s instancemi virtuálních počítačů v rámci cloudové služby. K libovolné instanci virtuálního počítače můžete přistupovat v rámci cloudové služby pomocí jediné virtuální IP adresy.

Existují však scénáře, ve kterých budete možná potřebovat více než jednu virtuální IP adresu jako vstupní bod ke stejné cloudové službě. Cloudová služba může například hostovat více webů, které vyžadují připojení SSL, s použitím výchozího portu 443, protože každá lokalita je hostovaná pro jiného zákazníka nebo tenanta. V tomto scénáři musíte mít pro každý web jinou veřejnou IP adresu. Následující diagram znázorňuje typické víceklientské hostování webů s několika certifikáty SSL na jednom veřejném portu.

![Scénář SSL s více VIP](./media/load-balancer-multivip/Figure1.png)

V příkladu výše všechny VIP používají stejný veřejný port (443) a přenos se přesměruje na jeden nebo více virtuálních počítačů vyrovnaných s vyrovnáváním zatížení na jedinečném privátním portu pro interní IP adresu cloudové služby, která je hostitelem všech webů.

> [!NOTE]
> Další situací, která vyžaduje použití více virtuálních IP adres, je hostování více posluchačů skupiny dostupnosti SQL AlwaysOn na stejné sadě Virtual Machines.

Virtuální IP adresy jsou ve výchozím nastavení dynamické, což znamená, že se skutečná IP adresa přiřazená ke cloudové službě může v průběhu času měnit. Aby k tomu nedocházelo, můžete si vyhradit virtuální IP adresu pro vaši službu. Další informace o rezervovaných virtuálních IP adresách najdete v tématu [vyhrazená veřejná IP adresa](../virtual-network/virtual-networks-reserved-public-ip.md).

> [!NOTE]
> Informace o cenách pro VIP a vyhrazené IP adresy najdete v článku o [cenách IP adres](https://azure.microsoft.com/pricing/details/ip-addresses/) .

Pomocí prostředí PowerShell můžete ověřit virtuální IP adresy používané v cloudových službách a také přidávat a odebírat VIP, přidružit k koncovému bodu virtuální IP adresu a nakonfigurovat vyrovnávání zatížení pro konkrétní VIP.

## <a name="limitations"></a>Omezení

V současné době jsou funkce více VIP omezené na následující scénáře:

* **Pouze IaaS**. Pro cloudové služby, které obsahují virtuální počítače, můžete povolit jenom víc VIP. Ve scénářích PaaS s instancemi role nemůžete použít Multi-VIP.
* **Pouze PowerShell**. Víc virtuálních IP adres můžete spravovat jenom pomocí PowerShellu.

Tato omezení jsou dočasná a můžou se kdykoli změnit. Nezapomeňte znovu navštívit tuto stránku a ověřit budoucí změny.

## <a name="how-to-add-a-vip-to-a-cloud-service"></a>Postup přidání virtuální IP adresy do cloudové služby
Pokud chcete do služby přidat virtuální IP adresu, spusťte následující příkaz PowerShellu:

```powershell
Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

Tento příkaz zobrazí výsledek podobný následujícímu příkladu:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>Jak odebrat VIP z cloudové služby
Pokud chcete odebrat VIP přidané do vaší služby v předchozím příkladu, spusťte následující příkaz PowerShellu:

```powershell
Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

> [!IMPORTANT]
> Virtuální IP adresu můžete odebrat jenom v případě, že k ní nejsou přidružené žádné koncové body.


## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>Jak načíst informace VIP z cloudové služby
Pokud chcete načíst virtuální IP adresy přidružené ke cloudové službě, spusťte následující skript prostředí PowerShell:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

Skript zobrazí výsledek podobný následující ukázce:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

V tomto příkladu má cloudová služba 3 VIP:

* **Vip1** je výchozí VIP, ale víte, že hodnota pro IsDnsProgrammedName je nastavená na true.
* **Vip2** a **Vip3** se nepoužívají, protože nemají žádné IP adresy. Budou se používat jenom v případě, že k virtuální IP adrese přiřadíte koncový bod.

> [!NOTE]
> Po přiřazení k koncovému bodu se vašemu předplatnému účtují jenom dodatečné virtuální IP adresy. Další informace o cenách najdete v tématu [ceny IP adres](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>Postup přidružení virtuální IP adresy ke koncovému bodu

K přidružení virtuální IP adresy pro cloudovou službu ke koncovému bodu spusťte následující příkaz prostředí PowerShell:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 |
    Update-AzureVM
```

Příkaz vytvoří koncový bod propojený s virtuální IP adresou s názvem *Vip2* na portu *80*a připojí ho k virtuálnímu počítači s názvem *myVM1* v cloudové službě s názvem *mojesluzba* pomocí *TCP* na portu *8080*.

Chcete-li ověřit konfiguraci, spusťte následující příkaz prostředí PowerShell:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

Výstup bude vypadat podobně jako v následujícím příkladu:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         : 191.238.74.13
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>Jak povolit vyrovnávání zatížení pro konkrétní VIP

K vyrovnávání zatížení můžete přidružit jednu virtuální IP adresu s několika virtuálními počítači. Máte třeba cloudovou službu s názvem *mojesluzba*a dva virtuální počítače s názvem *myVM1* a *myVM2*. A vaše cloudová služba má více virtuálních IP adres, jednu z nich s názvem *Vip2*. Pokud chcete zajistit, aby veškerý provoz na portu *81* na *Vip2* byl vyvážen mezi *myVM1* a *myVM2* na portu *8181*, spusťte následující skript prostředí PowerShell:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2 -DefaultProbe |
    Update-AzureVM

Get-AzureVM -ServiceName myService -Name myVM2 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe |
    Update-AzureVM
```

Nástroj pro vyrovnávání zatížení můžete také aktualizovat tak, aby používal jinou virtuální IP adresu. Pokud třeba spustíte příkaz PowerShellu níže, změníte sadu vyrovnávání zatížení tak, aby používala VIP s názvem Vip1:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1
```

## <a name="next-steps"></a>Další kroky

[Protokoly Azure Monitor pro vyrovnávání zatížení Azure](load-balancer-monitor-log.md)

[Přehled internetového nástroje pro vyrovnávání zatížení](load-balancer-internet-overview.md)

[Začínáme s internetovým nástrojem pro vyrovnávání zatížení](load-balancer-get-started-internet-arm-ps.md)

[Přehled služby Virtual Network](../virtual-network/virtual-networks-overview.md)

[Rozhraní REST API pro Vyhrazená IP adresa](https://msdn.microsoft.com/library/azure/dn722420.aspx)
