---
title: Privátní IP adresy v Azure
titlesuffix: Azure Virtual Network
description: Přečtěte si o privátních IP adresách v Azure.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/2020
ms.author: allensu
ms.openlocfilehash: 13688b73c0cd73e13e407f1b75beb8dda61a754f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84172372"
---
# <a name="private-ip-addresses"></a>Privátní IP adresy
Privátní IP adresy umožňují komunikaci mezi prostředky v Azure. 

Prostředky můžou být:
* Služby Azure, jako například:
    * síťová rozhraní virtuálních počítačů,
    * Interní nástroje pro vyrovnávání zatížení
    * brány Application Gateway.
* Ve [virtuální síti](virtual-networks-overview.md).
* Místní síť prostřednictvím brány VPN nebo okruhu ExpressRoute.

Privátní IP adresy umožňují komunikaci s těmito prostředky bez použití veřejné IP adresy.

## <a name="allocation-method"></a>Metoda přidělování

Azure přiřadí k prostředkům privátní IP adresy z rozsahu adres podsítě virtuální sítě, ve které je prostředek.

Azure si vyhrazuje první čtyři adresy v každém rozsahu adres podsítě. Adresy nelze přiřadit k prostředkům. Pokud je například rozsah adres podsítě 10.0.0.0/16, adresy 10.0.0.0-10.0.0.3 a 10.0.255.255 nejsou k dispozici. IP adresy v rámci rozsahu adres podsítě je možné v každém okamžiku přiřadit jenom jednomu prostředku. 

Existují dvě metody, ve kterých je zadána privátní IP adresa:

- **Dynamická:** Azure přiřadí další dostupnou nepřiřazenou nebo nevyhrazenou IP adresu v rozsahu adres podsítě. Azure novému prostředku například přiřadí 10.0.0.10, pokud adresy 10.0.0.4-10.0.0.9 jsou už přiřazené jiným prostředkům. 

    Dynamická metoda přidělování je výchozí metoda. Po přiřazení se uvolní dynamické IP adresy, pokud je síťové rozhraní:
    
    * Odstraněné
    * Znovu přiřazená k jiné podsíti ve stejné virtuální síti.
    * Metoda přidělení se změnila na statickou a zadá se jiná IP adresa. 
    
    Když změníte metodu přidělování z dynamické na statickou, Azure ve výchozím nastavení jako statickou IP adresu přiřadí dříve dynamicky přiřazenou adresu.

- **Statická:** Vyberete a přiřadíte libovolnou nepřiřazenou nebo nevyhrazenou IP adresu v rozsahu adres podsítě. 

    Například rozsah adres podsítě je 10.0.0.0/16 a adresy 10.0.0.4-10.0.0.9 jsou přiřazeny jiným prostředkům. Můžete přiřadit libovolnou adresu mezi 10.0.0.10-10.0.255.254. Statické adresy se uvolní pouze v případě odstranění síťového rozhraní. 
    
    Azure při změně metody přidělování přiřadí statickou IP adresu jako dynamickou IP adresu. Změna přiřazení nastane i v případě, že adresa není další dostupná v podsíti. Adresa se změní, když je síťové rozhraní přiřazeno k jiné podsíti.
    
    Chcete-li přiřadit síťové rozhraní k jiné podsíti, změňte metodu přidělení z static na Dynamic. Přiřaďte síťové rozhraní k jiné podsíti a pak změňte metodu přidělení zpět na statickou. Přiřaďte IP adresu z rozsahu adres nové podsítě.
    
## <a name="virtual-machines"></a>Virtuální počítače

Jedna nebo více privátních IP adres je přiřazeno k jednomu nebo více **síťovým rozhraním**. Síťová rozhraní jsou přiřazena k virtuálnímu počítači se [systémem Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Pro každou privátní IP adresu můžete určit dynamickou nebo statickou metodu přidělování.

### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>Interní překlad názvů hostitelů DNS (pro virtuální počítače)

Virtuální počítače Azure jsou ve výchozím nastavení nakonfigurované se [servery DNS spravovanými Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) . Můžete explicitně nakonfigurovat vlastní servery DNS. Tyto servery DNS poskytují interní překlad adres IP pro virtuální počítače, které jsou ve stejné virtuální síti.

Mapování názvu hostitele na privátní IP adresu virtuálního počítače se přidá do serverů DNS spravovaných Azure. 

Název hostitele je namapovaný na primární IP adresu hlavního síťového rozhraní, když má virtuální počítač následující:

* Několik síťových rozhraní
* Několik IP adres
* Obojí

Virtuální počítače nakonfigurované pomocí DNS spravované službou Azure vyřeší názvy hostitelů v rámci stejné virtuální sítě. K překladu názvů hostitelů virtuálních počítačů v připojených virtuálních sítích použijte vlastní server DNS.

## <a name="internal-load-balancers-ilb--application-gateways"></a>Interní nástroje pro vyrovnávání a Application Gateway

Privátní IP adresu můžete přiřadit konfiguraci **front-endu** pro:

* [Interní nástroj pro vyrovnávání zatížení Azure](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (interního nástroje)
* [Azure Application Gateway](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 

Tato privátní IP adresa slouží jako interní koncový bod. Interní koncový bod je přístupný pouze pro prostředky v rámci své virtuální sítě a vzdálené sítě, které jsou k němu připojeny. Je možné přiřadit dynamickou nebo statickou IP adresu.

## <a name="at-a-glance"></a>Přehledně
V následující tabulce je uvedena vlastnost, jejímž prostřednictvím je možné privátní IP adresu přiřadit k prostředku. 

Lze také zobrazit možné metody přidělení, které lze použít:

* Dynamická
* Static

| Prostředek nejvyšší úrovně | Přidružení IP adresy | Dynamická | Static |
| --- | --- | --- | --- |
| Virtuální počítač |Síťové rozhraní |Yes |Yes |
| Nástroj pro vyrovnávání zatížení |Konfigurace front-endu |Yes |Yes |
| Application Gateway |Konfigurace front-endu |Yes |Yes |

## <a name="limits"></a>Omezení
Omezení IP adres najdete v úplné sadě [omezení pro sítě](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) v Azure. Omezení platí pro jednotlivé oblasti a jednotlivá předplatná. Pokud chcete zvýšit výchozí omezení na základě vašich obchodních potřeb, obraťte se na [podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

## <a name="next-steps"></a>Další kroky
Další informace o [veřejné IP adresy v Azure](public-ip-addresses.md)
* [Nasazení virtuálního počítače se statickou privátní IP adresou pomocí webu Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)
