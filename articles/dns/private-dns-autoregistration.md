---
title: Funkce autoregistrování Azure DNS privátních zón
description: Přehled funkce autoregistrování Azure DNS privátních zón
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 2e5f41c0e149c99b5524c439c59e72afe554c776
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783893"
---
# <a name="what-is-the-autoregistration-feature-of-azure-dns-private-zones"></a>Funkce autoregistrování Azure DNS privátních zón

Funkce automatické registrace Azure DNS privátních zón přebírá z provozu správu záznamů DNS pro virtuální počítače nasazené ve virtuální síti. Když [propojíte virtuální síť](./private-dns-virtual-network-links.md) s privátní zónou DNS a povolíte automatickou registraci všech virtuálních počítačů, záznamy DNS pro virtuální počítače nasazené ve virtuální síti se automaticky vytvoří v privátní zóně DNS. Kromě záznamu záznamů o prohledání (záznamů) se taky pro virtuální počítače automaticky vytvoří záznamy zpětného vyhledávání (záznamy PTR).
Pokud do virtuální sítě přidáte další virtuální počítače, záznamy DNS pro tyto virtuální počítače se také automaticky vytvoří v propojené privátní zóně DNS.

Když odstraníte virtuální počítač, záznamy DNS pro virtuální počítač se automaticky odstraní z privátní zóny DNS.

Automatickou registraci můžete povolit výběrem možnosti Povolit automatickou registraci při vytváření propojení virtuální sítě.

![Povolit automatickou registraci](./media/privatedns-concepts/enable-autoregistration.png)

## <a name="restrictions"></a>Omezení

* Automatická registrace funguje jenom pro virtuální počítače. Pro všechny ostatní prostředky, jako jsou interní nástroje pro vyrovnávání zatížení atd., můžete ručně vytvořit záznamy DNS v privátní zóně DNS připojené k virtuální síti.
* Záznamy DNS se vytvoří automaticky jenom pro primární síťovou kartu virtuálního počítače. Pokud vaše virtuální počítače mají více než jednu síťovou kartu, můžete ručně vytvořit záznamy DNS pro další síťová rozhraní.
* Záznamy DNS se vytvoří automaticky jenom v případě, že primární síťová karta virtuálního počítače používá protokol DHCP. Pokud jsou nakonfigurovány statické IP adresy (například pro použití [více IP adres v Azure](../virtual-network/virtual-network-multiple-ip-addresses-portal.md#os-config)), nevytvoří Automatická registrace záznamy pro tento virtuální počítač.
* Automatická registrace pro protokol IPv6 (záznamy AAAA) není podporována.

## <a name="next-steps"></a>Další kroky

* Naučte se, jak vytvořit privátní zónu v Azure DNS pomocí [Azure PowerShell](./private-dns-getstarted-powershell.md) nebo [Azure CLI](./private-dns-getstarted-cli.md).

* Přečtěte si o některých běžných [scénářích privátní zóny](./private-dns-scenarios.md) , které je možné v Azure DNS realizovat s privátními zónami.

* Běžné otázky a odpovědi týkající se privátních zón v Azure DNS, včetně konkrétního chování, které můžete očekávat u určitých druhů operací, najdete v tématu [privátní DNS Nejčastější dotazy](./dns-faq-private.md).
