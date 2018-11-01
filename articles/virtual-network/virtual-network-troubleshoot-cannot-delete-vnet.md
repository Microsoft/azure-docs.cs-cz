---
title: Nelze odstranit virtuální síť v Azure | Dokumentace Microsoftu
description: Zjistěte, jak vyřešit problém, ve kterém nelze odstranit virtuální síť v Azure.
services: virtual-network
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 4bd143b37e4403d039108b4349b27604b6503e0e
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50415215"
---
# <a name="troubleshooting-failed-to-delete-a-virtual-network-in-azure"></a>Řešení potíží: Nepovedlo se odstranit virtuální síť v Azure

Při pokusu o odstranění virtuální sítě v Microsoft Azure, může dojít k chybám. Tento článek obsahuje postup řešení potíží, které vám pomohou vyřešit tento problém. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance"></a>Pokyny při řešení potíží 

1. [Zkontrolujte, zda bránu virtuální sítě běží ve virtuální síti](#check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network).
2. [Zkontrolujte, zda služby application gateway je spuštěná ve virtuální síti](#check-whether-an-application-gateway-is-running-in-the-virtual-network).
3. [Zkontrolujte, zda je povoleno služby Azure Active Directory Domain Services ve virtuální síti](#check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network).
4. [Zkontrolujte, zda virtuální síť připojená k jinému prostředku](#check-whether-the-virtual-network-is-connected-to-other-resource).
5. [Zkontrolujte, zda je stále spuštěný virtuální počítač ve virtuální síti](#check-whether-a-virtual-machine-is-still-running-in-the-virtual-network).
6. [Zkontrolujte, zda virtuální sítě se zasekla v automatickém migrace](#check-whether-the-virtual-network-is-stuck-in-migration).

## <a name="troubleshooting-steps"></a>Postup při řešení potíží

### <a name="check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network"></a>Zkontrolujte, zda bránu virtuální sítě běží ve virtuální síti

Virtuální síť bude odebrána, musíte nejdřív odebrat bránu virtuální sítě.

U klasických virtuálních sítí, přejděte **přehled** stránky klasického virtuálního počítače na webu Azure Portal. V **připojení k síti VPN** části, pokud je brána spuštěná ve virtuální síti, zobrazí se IP adresa brány. 

![Zkontrolujte, jestli je brána spuštěná](media/virtual-network-troubleshoot-cannot-delete-vnet/classic-gateway.png)

Pro virtuální sítě, přejděte **přehled** stránky ve virtuální síti. Zkontrolujte **připojená zařízení** pro bránu virtuální sítě.

![Zkontrolujte připojené zařízení](media/virtual-network-troubleshoot-cannot-delete-vnet/vnet-gateway.png)

Před odebráním brány, nejdřív všechny odebrat **připojení** objekty v bráně. 

### <a name="check-whether-an-application-gateway-is-running-in-the-virtual-network"></a>Zkontrolujte, zda služby application gateway je spuštěná ve virtuální síti

Přejděte **přehled** stránky ve virtuální síti. Zkontrolujte, **připojená zařízení** pro službu application gateway.

![Zkontrolujte připojené zařízení](media/virtual-network-troubleshoot-cannot-delete-vnet/app-gateway.png)

Pokud je službu application gateway, musíte odebrat před odstraněním virtuální sítě.

### <a name="check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network"></a>Zkontrolujte, zda je povoleno služby Azure Active Directory Domain Services ve virtuální síti

Pokud Active Directory Domain Services je povolen a připojen k virtuální síti, tuto virtuální síť nejde odstranit. 

![Zkontrolujte připojené zařízení](media/virtual-network-troubleshoot-cannot-delete-vnet/enable-domain-services.png)

Zakázat službu, najdete v článku [zakažte Azure Active Directory Domain Services pomocí webu Azure portal](../active-directory-domain-services/active-directory-ds-disable-aadds.md).

### <a name="check-whether-the-virtual-network-is-connected-to-other-resource"></a>Zkontrolujte, zda virtuální síť připojená k jinému prostředku

Zkontrolujte propojení okruhu, připojení a partnerské vztahy virtuálních sítí. Některé z těchto může způsobit selhání odstranění virtuální sítě. 

Pořadí odstranění doporučené vypadá takto:

1. připojení brány
2. Brány
3. IP adresy
4. Partnerské vztahy virtuálních sítí
5. App Service Environment (ASE)

### <a name="check-whether-a-virtual-machine-is-still-running-in-the-virtual-network"></a>Zkontrolujte, zda je stále spuštěný virtuální počítač ve virtuální síti

Ujistěte se, že žádný virtuální počítač je ve virtuální síti.

### <a name="check-whether-the-virtual-network-is-stuck-in-migration"></a>Zkontrolujte, zda virtuální sítě se zasekla v automatickém migrace

Pokud virtuální síť se zasekla v automatickém migrace stavu, nelze odstranit. Spusťte následující příkaz k migraci přerušit a pak odstranit virtuální síť.

    Move-AzureVirtualNetwork -VirtualNetworkName "Name" -Abort

## <a name="next-steps"></a>Další postup

- [Azure Virtual Network](virtual-networks-overview.md)
- [Nejčastější dotazy ke službě Azure Virtual Network](virtual-networks-faq.md)
