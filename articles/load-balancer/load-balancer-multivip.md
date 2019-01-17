---
title: Více virtuálních IP adres pro cloudové služby
titlesuffix: Azure Load Balancer
description: Přehled víc virtuálními IP adresami a jak nastavit více virtuálních IP adres v cloudové službě
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 3036bbc74b4ed8ee92e10f952252889c4c12f845
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54353936"
---
# <a name="configure-multiple-vips-for-a-cloud-service"></a>Konfigurace více virtuálních IP adres pro cloudové služby

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Cloudové služby Azure prostřednictvím veřejného Internetu přístupná pomocí IP adresy poskytovaný platformou Azure. Tato veřejná IP adresa se označuje jako virtuální IP adresy (virtuální IP adresu) vzhledem k tomu, že je propojený s Azure load balancer, a ne virtuální počítač (VM) instancí v rámci cloudové služby. Všechny instance virtuálních počítačů v rámci cloudové služby můžete přistupovat pomocí jedné virtuální IP adresu.

Existují ale scénáře, ve kterých budete potřebovat více než jednu virtuální IP adresy jako položku přejděte na stejné cloudové službě. Cloudové služby může například hostování několika webů, vyžadovat připojení SSL pomocí výchozí port 443, jako je každá lokalita je hostovaný pro různé zákazníky nebo tenanta. V tomto scénáři budete muset mít různých veřejná IP adresa pro každý web. Následující diagram znázorňuje typický víceklientské webhosting věcí a potřebovali pro více certifikátů SSL na stejný veřejný port.

![Scénář s více virtuálních IP adres protokolu SSL](./media/load-balancer-multivip/Figure1.png)

V předchozím příkladu všechny virtuální IP adresy používají stejný veřejný port (443) a provoz se přesměruje na jeden nebo více vyrovnaným virtuálních počítačů na jedinečný privátní port pro interní IP adresa cloudové služby hostování webů.

> [!NOTE]
> Další situace vyžadovat použití více virtuálních IP adres je hostitelem více SQL AlwaysOn naslouchacích procesů skupin dostupnosti ve stejné sadě virtuálních počítačů.

Virtuální IP adresy je dynamická ve výchozím nastavení, což znamená, že v průběhu času mohou měnit vlastní IP adresu přiřazenou ke cloudové službě. Chcete-li zabránit, který, si můžete rezervovat virtuální IP adresy pro vaši službu. Další informace o rezervovaných virtuálních IP adres najdete v tématu [vyhrazenou IP adresu veřejné](../virtual-network/virtual-networks-reserved-public-ip.md).

> [!NOTE]
> Podrobnosti najdete na [ceny IP adres](https://azure.microsoft.com/pricing/details/ip-addresses/) informace o cenách virtuálních IP adres a vyhrazené IP adresy.

Můžete ověřit virtuální IP adresy používá své cloudové služby pomocí prostředí PowerShell také přidat a odebrat virtuální IP adresy, přiřadit virtuální IP adresu pro koncový bod a konfigurace na konkrétní virtuální IP adresy služby Vyrovnávání zatížení.

## <a name="limitations"></a>Omezení

V současné době funkce s více virtuálních IP adres je omezena některý z následujících scénářů:

* **IaaS pouze**. Více virtuálních IP adres můžete povolit jenom pro cloudové služby, které obsahují virtuální počítače. Více virtuálních IP adres nelze použít ve scénářích PaaS s instancí rolí.
* **Prostředí PowerShell pouze**. Více virtuálních IP adres může spravovat jenom pomocí prostředí PowerShell.

Tato omezení jsou dočasné a může kdykoli změnit. Ujistěte se, že návštěvě této stránky můžete ověřit budoucí změny.

## <a name="how-to-add-a-vip-to-a-cloud-service"></a>Přidání virtuální IP adresu do cloudové služby
Pokud chcete přidat virtuální IP adresy k vaší službě, spusťte následující příkaz Powershellu:

```powershell
Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

Tento příkaz zobrazí výsledek podobný následujícím příkladu:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>Jak odebrat virtuální IP adresu z cloudové služby
Odebrat virtuální IP adresu přidat do vaší služby v předchozím příkladu, spusťte následující příkaz Powershellu:

```powershell
Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

> [!IMPORTANT]
> Virtuální IP adresy lze odebrat pouze, pokud nemá žádné koncové body přidružené k němu.


## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>Jak načíst informace o virtuálních IP adres z cloudové služby
Pokud chcete načíst virtuální IP adresy spojené s cloudovou službou, spusťte následující skript prostředí PowerShell:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

Tento skript zobrazí výsledek podobný následujícím příkladu:

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

V tomto příkladu je Cloudová služba 3 virtuálních IP adres:

* **Vip1** je výchozí virtuální IP adresy, víte, že vzhledem k tomu, že je hodnota pro IsDnsProgrammedName nastavena na hodnotu true.
* **Vip2** a **Vip3** nejsou použity jako nemají žádné IP adresy. Pouze používají-li přidružit koncový bod pro virtuální IP adresy.

> [!NOTE]
> Vaše předplatné naúčtujeme vám jenom pro další virtuální IP adresy poté, co jsou související s koncovým bodem. Další informace o cenách najdete v tématu [ceny IP adres](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>Přidružení virtuální IP adresu pro koncový bod

Pro přiřazení virtuální IP adresy v cloudové službě pro koncový bod, spusťte následující příkaz Powershellu:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 |
    Update-AzureVM
```

Příkaz vytvoří koncový bod spojený s virtuální IP adresy volá *Vip2* na portu *80*a odkazů na virtuální počítač s názvem *myVM1* v cloudové službě s názvem *Moje_služba* pomocí *TCP* na portu *8080*.

Pokud chcete ověřit konfiguraci, spusťte následující příkaz Powershellu:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

Výstup vypadá podobně jako v následujícím příkladu:

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

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>Jak povolit na konkrétní virtuální IP adresy služby Vyrovnávání zatížení

Jednu virtuální IP adresu můžete přidružit několik virtuálních počítačů pro účely služby Vyrovnávání zatížení. Například máte cloudové služby s názvem *Moje_služba*a dva virtuální počítače s názvem *myVM1* a *myVM2*. A cloudové služby má více virtuálních IP adres, jeden z nich s názvem *Vip2*. Pokud chcete mít jistotu, že všechny provoz na portu *81* na *Vip2* jsou rovnoměrně mezi *myVM1* a *myVM2* na portu *8181* , spusťte následující skript prostředí PowerShell:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2 -DefaultProbe |
    Update-AzureVM

Get-AzureVM -ServiceName myService -Name myVM2 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe |
    Update-AzureVM
```

Můžete také aktualizovat nástroj pro vyrovnávání zatížení různých virtuálních IP adres používat. Například pokud spustíte následující příkaz prostředí PowerShell, se změní sadu používat virtuální IP adresy s názvem Vip1 Vyrovnávání zatížení:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1
```

## <a name="next-steps"></a>Další kroky

[Log analytics pro nástroj pro vyrovnávání zatížení Azure](load-balancer-monitor-log.md)

[Internet protilehlé load balancer – přehled](load-balancer-internet-overview.md)

[Začínáme s internetového nástroje pro vyrovnávání zatížení](load-balancer-get-started-internet-arm-ps.md)

[Přehled služby Virtual Network](../virtual-network/virtual-networks-overview.md)

[Vyhrazenou IP adresu REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)
