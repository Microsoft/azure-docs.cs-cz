---
title: Nelze odstranit virtuální síť v Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak vyřešit problém, ve kterém nelze odstranit virtuální síť v Azure.
services: virtual-network
documentationcenter: na
author: chadmath
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 2d427a8b40fcb537801ce76aae6bc32fcda3a307
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71056929"
---
# <a name="troubleshooting-failed-to-delete-a-virtual-network-in-azure"></a>Řešení potíží: Nepodařilo se odstranit virtuální síť v Azure

Při pokusu o odstranění virtuální sítě v Microsoft Azure se mohou zobrazit chyby. Tento článek obsahuje postup řešení potíží, který vám pomůže tento problém vyřešit. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance"></a>Pokyny při řešení potíží 

1. [Zkontrolujte, jestli je ve virtuální síti spuštěná brána virtuální sítě](#check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network).
2. [Zkontrolujte, jestli je ve virtuální síti spuštěná aplikační brána](#check-whether-an-application-gateway-is-running-in-the-virtual-network).
3. [Zkontrolujte, jestli je ve virtuální síti povolená služba Azure Active Directory Domain Service](#check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network).
4. [Zkontrolujte, jestli je virtuální síť připojená k jinému prostředku](#check-whether-the-virtual-network-is-connected-to-other-resource).
5. [Zkontrolujte, jestli je virtuální počítač stále spuštěný ve virtuální síti](#check-whether-a-virtual-machine-is-still-running-in-the-virtual-network).
6. [Zkontrolujte, jestli se virtuální síť zasekla v migraci](#check-whether-the-virtual-network-is-stuck-in-migration).

## <a name="troubleshooting-steps"></a>Postup při řešení potíží

### <a name="check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network"></a>Kontrola, jestli je ve virtuální síti spuštěná brána virtuální sítě

Chcete-li virtuální síť odebrat, musíte nejprve odebrat bránu virtuální sítě.

Klasické virtuální sítě najdete na stránce **Přehled** klasické virtuální sítě na webu Azure Portal. V části **Připojení VPN,** pokud je brána spuštěna ve virtuální síti, uvidíte IP adresu brány. 

![Kontrola, zda je spuštěna brána](media/virtual-network-troubleshoot-cannot-delete-vnet/classic-gateway.png)

Pro virtuální sítě přejděte na stránku **Přehled** virtuální sítě. Zkontrolujte **Připojená zařízení** pro bránu virtuální sítě.

![Zkontrolujte připojené zařízení](media/virtual-network-troubleshoot-cannot-delete-vnet/vnet-gateway.png)

Před odebráním brány nejprve odeberte všechny objekty **připojení** v bráně. 

### <a name="check-whether-an-application-gateway-is-running-in-the-virtual-network"></a>Kontrola, jestli je ve virtuální síti spuštěná aplikační brána

Přejděte na stránku **Přehled** virtuální sítě. Zkontrolujte **připojená zařízení** pro aplikační bránu.

![Zkontrolujte připojené zařízení](media/virtual-network-troubleshoot-cannot-delete-vnet/app-gateway.png)

Pokud je brána aplikace, je nutné ji odebrat před odstraněním virtuální sítě.

### <a name="check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network"></a>Kontrola, jestli je ve virtuální síti povolená služba Azure Active Directory Domain Service

Pokud je služba Active Directory Domain Service povolena a připojena k virtuální síti, nelze tuto virtuální síť odstranit. 

![Zkontrolujte připojené zařízení](media/virtual-network-troubleshoot-cannot-delete-vnet/enable-domain-services.png)

Pokud chcete službu zakázat, [přečtěte si informace o zakázání služby Azure Active Directory Domain Services pomocí portálu Azure .](../active-directory-domain-services/delete-aadds.md)

### <a name="check-whether-the-virtual-network-is-connected-to-other-resource"></a>Kontrola, zda je virtuální síť připojena k jinému prostředku

Zkontrolujte propojení okruhů, připojení a partnerské vztahy virtuální sítě. Některý z nich může způsobit selhání odstranění virtuální sítě. 

Doporučené pořadí odstranění je následující:

1. Připojení brány
2. Brány
3. Ips
4. Partnerské servery virtuální sítě
5. Prostředí služby App Service (ASE)

### <a name="check-whether-a-virtual-machine-is-still-running-in-the-virtual-network"></a>Kontrola, jestli je virtuální počítač pořád spuštěný ve virtuální síti

Ujistěte se, že žádný virtuální počítač je ve virtuální síti.

### <a name="check-whether-the-virtual-network-is-stuck-in-migration"></a>Kontrola, zda se virtuální síť zasekla v migraci

Pokud se virtuální síť zasekla ve stavu migrace, nelze ji odstranit. Chcete-li migraci přerušit, spusťte následující příkaz a odstraňte virtuální síť.

    Move-AzureVirtualNetwork -VirtualNetworkName "Name" -Abort

## <a name="next-steps"></a>Další kroky

- [Azure Virtual Network](virtual-networks-overview.md)
- [Nejčastější dotazy ke službě Azure Virtual Network](virtual-networks-faq.md)
