---
title: Více aplikačními virtuální IP adresy pro cloudové služby
description: Přehled o víc virtuálními IP adresami a jak nastavit několika virtuálními IP adresami v cloudové službě
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: 85f6d26a-3df5-4b8e-96a1-92b2793b5284
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: b6b7b0b2d7a7f33facaf72bbd2d7937364770673
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23855198"
---
# <a name="configure-multiple-vips-for-a-cloud-service"></a>Konfigurace více virtuální IP adresy pro cloudové služby

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Cloudové služby Azure získat přístup prostřednictvím veřejného Internetu pomocí IP adresy poskytované platformou Azure. Tato veřejná IP adresa se označuje jako virtuální IP adresy (virtuální IP adresy) vzhledem k tomu, že je propojená se službou Vyrovnávání zatížení Azure a není virtuální počítač (VM) instancí v rámci cloudové služby. Všechny instance virtuálního počítače v rámci cloudové služby můžete přistupovat pomocí jedné virtuální IP adresy.

Existují však scénáře, ve kterých může potřebovat více než jeden bod VIP jako položku do stejné cloudové služby. Cloudové služby může například hostování více webů, který vyžaduje připojení SSL s použitím výchozího portu 443, jako je každá lokalita hostované pro různých zákazníků, nebo klienta. V tomto scénáři musíte mít různé veřejných přístupných IP adresu pro každý web. Následující obrázek znázorňuje typické víceklientské webhosting s potřeba víc certifikátů SSL na stejném veřejného portu.

![Scénář více virtuálních IP adres SSL](./media/load-balancer-multivip/Figure1.png)

V příkladu nahoře všechny virtuální adresy VIP používají stejný veřejný port (443) a provoz se přesměruje na jeden nebo virtuální počítače na jedinečný privátní port pro interní IP adresu cloudové služby hostování všechny weby s vyrovnáváním zatížení Další.

> [!NOTE]
> Jiné situaci vyžadující použití několika virtuálními IP adresami je hostitelem více SQL AlwaysOn dostupnosti naslouchací procesy skupiny na stejnou sadu virtuálních počítačů.

Virtuální IP adresy jsou dynamické ve výchozím nastavení, což znamená, že v průběhu času mění skutečné přiřazené ke cloudové službě IP adresy. Aby nedocházelo, můžete vyhradit VIP pro vaši službu. Další informace o vyhrazenou virtuální IP adresy, najdete v části [vyhrazené veřejné IP adresy](../virtual-network/virtual-networks-reserved-public-ip.md).

> [!NOTE]
> Najdete v tématu [ceny IP adresu](https://azure.microsoft.com/pricing/details/ip-addresses/) informace o cenách na virtuální IP adresy a vyhrazené IP adresy.

Můžete pomocí prostředí PowerShell k ověření virtuální IP adresy používané vaší cloudové služby, a také přidat a odebrat virtuální IP adresy, přidružit VIP na koncový bod a konfigurovat konkrétní VIP pro vyrovnávání zatížení.

## <a name="limitations"></a>Omezení

V tomto okamžiku funkce více virtuálních IP adres je omezený na následující scénáře:

* **Pouze IaaS**. Více virtuálních IP adres můžete povolit jenom pro cloudové služby, které obsahují virtuální počítače. Více virtuálních IP adres nelze použít ve scénářích PaaS s instancí rolí.
* **Prostředí PowerShell pouze**. Více virtuálních IP adres může spravovat jenom pomocí prostředí PowerShell.

Tato omezení jsou dočasné a může kdykoli změnit. Zajistěte, aby k pokroku tuto stránku k ověření budoucí změny.

## <a name="how-to-add-a-vip-to-a-cloud-service"></a>Postup přidání virtuální IP adresu do cloudové služby
Chcete-li přidat virtuální IP adresu k službě, spusťte následující příkaz prostředí PowerShell:

```powershell
Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

Tento příkaz zobrazí výsledek podobně jako v následujícím příkladu:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>Postup odebrání virtuální IP adresu z cloudové služby
Odebrat virtuální IP adresu přidat do služby v příkladu nahoře, spusťte následující příkaz prostředí PowerShell:

```powershell
Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

> [!IMPORTANT]
> Pokud ji nemá žádné koncové body přidružené k němu můžete pouze odebrat virtuální IP adresu.


## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>Jak načíst informace o virtuálních IP adres z cloudové služby
Načtení virtuální IP adresy přidružené ke cloudové službě, spusťte následující skript prostředí PowerShell:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

Skript zobrazí výsledek podobně jako v následujícím příkladu:

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

V tomto příkladu cloudové služby má 3 virtuální IP adresy:

* **Vip1** je výchozí VIP, víte, že vzhledem k tomu, že je hodnota pro IsDnsProgrammedName nastavena na hodnotu true.
* **Vip2** a **Vip3** nejsou použity jako nemají žádné IP adresy. Pouze používají Pokud přidružíte koncového bodu virtuální IP adresu.

> [!NOTE]
> Vaše předplatné bude pouze vám účtována navíc VIP po jsou spojeny s koncový bod. Další informace o cenách najdete v tématu [ceny IP adresu](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>Postup přidružení VIP na koncový bod

Chcete-li přidružit VIP na cloudové služby na koncový bod, spusťte následující příkaz prostředí PowerShell:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 |
    Update-AzureVM
```

Příkaz vytvoří koncový bod propojené s názvem VIP *Vip2* na portu *80*a odkazů na virtuální počítač s názvem *myVM1* v rámci cloudové služby s názvem *Moje_služba* pomocí *TCP* na portu *8080*.

Pokud chcete ověřit konfiguraci, spusťte následující příkaz prostředí PowerShell:

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

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>Postup povolení konkrétní VIP pro vyrovnávání zatížení

Více virtuálních počítačů pro účely Vyrovnávání zatížení je možné přidružit jednu VIP. Například máte cloudové služby s názvem *Moje_služba*a dva virtuální počítače s názvem *myVM1* a *Můjvp2*. A cloudové služby má několika virtuálními IP adresami, jeden z nich s názvem *Vip2*. Pokud chcete, aby všechny přenosy na portu *81* na *Vip2* je rovnoměrně rozdělen mezi *myVM1* a *Můjvp2* na portu *8181* , spusťte následující skript prostředí PowerShell:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2 -DefaultProbe |
    Update-AzureVM

Get-AzureVM -ServiceName myService -Name myVM2 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe |
    Update-AzureVM
```

Můžete také aktualizovat nástroj pro vyrovnávání zatížení použít jiný virtuální IP adresy. Například pokud spustíte následující příkaz prostředí PowerShell, se změní sadu používat s názvem Vip1 VIP pro vyrovnávání zatížení:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1
```

## <a name="next-steps"></a>Další kroky

[Analýzy protokolů pro vyrovnávání zatížení Azure](load-balancer-monitor-log.md)

[Přehled nástroje pro vyrovnávání zatížení přístupných Internetu](load-balancer-internet-overview.md)

[Začněte na internetové nástroj pro vyrovnávání zatížení](load-balancer-get-started-internet-arm-ps.md)

[Přehled virtuálních sítí](../virtual-network/virtual-networks-overview.md)

[Vyhrazená IP adresa REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)
