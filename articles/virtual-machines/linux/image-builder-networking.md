---
title: Možnosti sítě služby Azure image Builder
description: Pochopení možností sítě při nasazení služby Azure VM Image Builder
author: danielsollondon
ms.author: danis
ms.date: 08/10/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: cd5027ca6e0ce3dc02da14b7dd6afd6e00e3f92d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101669445"
---
# <a name="azure-image-builder-service-networking-options"></a>Možnosti sítě služby Azure image Builder

Musíte se rozhodnout nasadit Azure image Builder s existující virtuální sítí nebo bez ní.

## <a name="deploy-without-specifying-an-existing-vnet"></a>Nasadit bez zadání existující virtuální sítě

Pokud nezadáte existující virtuální síť, Azure image Builder vytvoří virtuální síť a podsíť v pracovní skupině prostředků. Prostředek veřejné IP adresy se používá se skupinou zabezpečení sítě k omezení příchozího provozu do služby Azure image Builder. Veřejná IP adresa usnadňuje příkazy kanálu pro Azure image Builder během sestavení image. Po dokončení sestavení se odstraní virtuální počítač, veřejná IP adresa, disky a virtuální síť. Pokud chcete použít tuto možnost, nezadávejte žádné vlastnosti virtuální sítě.

## <a name="deploy-using-an-existing-vnet"></a>Nasazení pomocí existující virtuální sítě

Pokud zadáte virtuální síť a podsíť, Azure image Builder nasadí virtuální počítač sestavení do zvolené virtuální sítě. Máte přístup k prostředkům, které jsou přístupné ve vaší virtuální síti. Můžete také vytvořit silou virtuální síť, která není připojená k žádné jiné virtuální síti. Pokud zadáte virtuální síť, Azure image Builder nepoužívá veřejnou IP adresu. Komunikace ze služby Azure image Builder do virtuálního počítače sestavení používá technologii privátního propojení Azure.

Další informace najdete v jednom z následujících příkladů:

* [Použití Azure image Builder pro virtuální počítače s Windows, který umožňuje přístup k existující virtuální síti Azure](../windows/image-builder-vnet.md)
* [Použití Azure image Builder pro virtuální počítače se systémem Linux umožňující přístup k existující virtuální síti Azure](image-builder-vnet.md)

### <a name="what-is-azure-private-link"></a>Co je privátní propojení Azure?

Privátní propojení Azure poskytuje privátní připojení z virtuální sítě k platformě Azure jako služby (PaaS), které patří zákazníkům nebo partnerům Microsoftu. Zjednodušuje architekturu sítě a zabezpečuje připojení mezi koncovými body v Azure tím, že eliminuje vystavení dat veřejnému Internetu. Další informace najdete v [dokumentaci k privátním odkazům](../../private-link/index.yml).

### <a name="required-permissions-for-an-existing-vnet"></a>Požadovaná oprávnění pro existující virtuální síť

Azure image Builder vyžaduje specifická oprávnění pro použití existující virtuální sítě. Další informace najdete v tématu [Konfigurace oprávnění služby Azure image Builder pomocí Azure CLI](image-builder-permissions-cli.md) nebo [Konfigurace oprávnění služby Azure image Builder pomocí PowerShellu](image-builder-permissions-powershell.md).

### <a name="what-is-deployed-during-an-image-build"></a>Co je nasazeno během sestavení obrázku?

Použití existující virtuální sítě znamená, že Azure image Builder nasadí další virtuální počítač (proxy) a Azure Load Balancer (ALB), která je připojená k privátnímu odkazu. Provoz ze služby AIB přechází přes privátní odkaz na ALB. ALB komunikuje s virtuálním počítačem proxy pomocí portu 60001 pro Linux OS nebo 60000 pro operační systém Windows. Proxy přeposílá příkazy k virtuálnímu počítači sestavení pomocí portu 22 pro Linux OS nebo 5986 pro operační systém Windows.

> [!NOTE]
> Virtuální síť musí být ve stejné oblasti jako oblast služby Azure image Builder.
> 

### <a name="why-deploy-a-proxy-vm"></a>Proč nasadit proxy virtuální počítač?

Když je virtuální počítač bez veřejné IP adresy za interním Load Balancer, nemá přístup k Internetu. Azure Load Balancer používaná pro virtuální síť je interní. Proxy virtuální počítač umožňuje přístup k Internetu pro virtuální počítač sestavení během sestavení. Přidružené skupiny zabezpečení sítě se dají použít k omezení přístupu k virtuálnímu počítači sestavení.

Velikost nasazeného virtuálního počítače proxy je standardní A1_v2 navíc k virtuálnímu počítači sestavení. K posílání příkazů mezi službou Azure image Builder a virtuálním počítačem sestavení se používá proxy server. Vlastnosti virtuálního počítače proxy nelze měnit včetně velikosti nebo operačního systému. Vlastnosti virtuálního počítače proxy nemůžete změnit pro sestavení imagí Windows i Linux.

### <a name="image-template-parameters-to-support-vnet"></a>Parametry šablony obrázku pro podporu virtuální sítě
```json
"VirtualNetworkConfig": {
        "name": "",
        "subnetName": "",
        "resourceGroupName": ""
        },
```

| Nastavení | Popis |
|---------|---------|
| name | Volitelné Název již existující virtuální sítě. |
| subnetName | Název podsítě v zadané virtuální síti. Musí být zadán pouze v případě, že je zadán *název* . |
| resourceGroupName | Název skupiny prostředků, která obsahuje zadanou virtuální síť. Musí být zadán pouze v případě, že je zadán *název* . |

Služba privátního propojení vyžaduje IP adresu z dané virtuální sítě a podsítě. V současné době Azure pro tyto IP adresy nepodporuje zásady sítě. Proto je třeba v podsíti zakázat zásady sítě. Další informace najdete v [dokumentaci k privátním odkazům](../../private-link/index.yml).

### <a name="checklist-for-using-your-vnet"></a>Kontrolní seznam pro použití vaší virtuální sítě

1. Povolení komunikace Azure Load Balancer (ALB) s virtuálním počítačem proxy v NSG
    * [AZ CLI – ukázka](image-builder-vnet.md#add-network-security-group-rule)
    * [Příklad PowerShellu](../windows/image-builder-vnet.md#add-network-security-group-rule)
2. Zakázat zásady pro privátní služby v podsíti
    * [AZ CLI – ukázka](image-builder-vnet.md#disable-private-service-policy-on-subnet)
    * [Příklad PowerShellu](../windows/image-builder-vnet.md#disable-private-service-policy-on-subnet)
3. Povolení Azure image Builder k vytvoření ALB a přidání virtuálních počítačů do virtuální sítě
    * [AZ CLI – ukázka](image-builder-permissions-cli.md#existing-vnet-azure-role-example)
    * [Příklad PowerShellu](image-builder-permissions-powershell.md#permission-to-customize-images-on-your-vnets)
4. Povolení Azure image Builder pro čtení a zápis zdrojových imagí a vytváření imagí
    * [AZ CLI – ukázka](image-builder-permissions-cli.md#custom-image-azure-role-example)
    * [Příklad PowerShellu](image-builder-permissions-powershell.md#custom-image-azure-role-example)
5. Ujistěte se, že používáte virtuální síť ve stejné oblasti jako oblast služby Azure image Builder.


## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [Přehled nástroje Azure image Builder](../image-builder-overview.md).