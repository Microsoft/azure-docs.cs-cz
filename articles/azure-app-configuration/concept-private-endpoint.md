---
title: Používání privátních koncových bodů pro konfiguraci aplikací Azure
description: Zabezpečení úložiště konfigurace aplikací pomocí privátních koncových bodů
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: f18672b9e3a368a833fc8cba279d748dfe3c2a9e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79366764"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>Používání privátních koncových bodů pro konfiguraci aplikací Azure

Pomocí [privátních koncových bodů](../private-link/private-endpoint-overview.md) pro konfiguraci aplikací Azure můžete klientům ve virtuální síti (VNET) umožňovat zabezpečený přístup k datům prostřednictvím [privátního propojení](../private-link/private-link-overview.md). Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě pro úložiště konfigurace aplikace. Síťový provoz mezi klienty ve virtuální síti a úložištěm konfigurace aplikace prochází přes virtuální síť pomocí privátního odkazu v páteřní síti Microsoftu a eliminuje tak expozici veřejnému Internetu.

Použití privátních koncových bodů pro vaše úložiště konfigurace aplikace vám umožní:
- Zabezpečte podrobnosti konfigurace aplikace konfigurací brány firewall tak, aby blokovala všechna připojení ke konfiguraci aplikace ve veřejném koncovém bodu.
- Zvyšte zabezpečení virtuální sítě (VNet), která z virtuální sítě zajišťuje, že se data neřídí.
- Bezpečně se připojte k úložišti konfigurace aplikace z místních sítí, které se připojují k virtuální síti pomocí [sítě VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [ExpressRoutes](../expressroute/expressroute-locations.md) s privátním partnerským vztahem.

> [!NOTE]
> Konfigurace aplikace Azure nabízí použití privátních koncových bodů jako veřejné verze Preview. Nabídky veřejné verze Preview umožňují zákazníkům experimentovat s novými funkcemi před jejich oficiální verzí.  Funkce a služby verze Public Preview nejsou určeny pro produkční použití.

## <a name="conceptual-overview"></a>Koncepční přehled

Privátní koncový bod je speciální síťové rozhraní pro službu Azure ve vaší [Virtual Network](../virtual-network/virtual-networks-overview.md) (virtuální síť). Když vytvoříte privátní koncový bod pro úložiště konfigurace aplikace, zajistíte zabezpečené připojení mezi klienty ve vaší virtuální síti a vaším úložištěm konfigurace. Privátnímu koncovému bodu je přiřazena IP adresa z rozsahu IP adres vaší virtuální sítě. Připojení mezi soukromým koncovým bodem a úložištěm konfigurace používá zabezpečený privátní odkaz.

Aplikace ve virtuální síti se můžou ke konfiguračnímu úložišti připojit prostřednictvím privátního koncového bodu **pomocí stejných připojovacích řetězců a mechanismů autorizace, které by jinak používaly**. Soukromé koncové body lze použít se všemi protokoly podporovanými úložištěm konfigurace aplikace.

I když konfigurace aplikace nepodporuje koncové body služby, v podsítích, které používají [koncové body služby](../virtual-network/virtual-network-service-endpoints-overview.md), je možné vytvořit privátní koncové body. Klienti v podsíti se můžou bezpečně připojit k úložišti konfigurace aplikace pomocí privátního koncového bodu při použití koncových bodů služby k přístupu k ostatním.  

Při vytváření privátního koncového bodu pro službu ve vaší virtuální síti se ke schválení vlastníka účtu služby pošle žádost o souhlas. Pokud uživatel žádající o vytvoření privátního koncového bodu je zároveň vlastníkem účtu, je tato žádost o souhlas automaticky schválena.

Vlastníci účtu služby můžou spravovat žádosti o souhlas a soukromé koncové `Private Endpoints` body prostřednictvím karty úložiště konfigurace v [Azure Portal](https://portal.azure.com).

### <a name="private-endpoints-for-app-configuration"></a>Privátní koncové body pro konfiguraci aplikace 

Při vytváření privátního koncového bodu je nutné zadat úložiště konfigurace aplikace, ke kterému se připojí. Pokud máte v rámci jednoho účtu více instancí konfigurace aplikace, budete pro každého úložiště potřebovat samostatný soukromý koncový bod.

### <a name="connecting-to-private-endpoints"></a>Připojování k soukromým koncovým bodům

Azure při směrování připojení z virtuální sítě do úložiště konfigurace prostřednictvím privátního propojení spoléhá na překlad DNS. Řetězce připojení můžete rychle najít v Azure Portal tak, že vyberete úložiště konfigurace aplikace a pak vyberete **Nastavení** > **přístupové klíče**.  

> [!IMPORTANT]
> Použijte stejný připojovací řetězec pro připojení k úložišti konfigurace aplikace pomocí privátních koncových bodů, protože byste použili pro veřejný koncový bod. Nepřipojujte se k účtu úložiště pomocí `privatelink` adresy URL své subdomény.

## <a name="dns-changes-for-private-endpoints"></a>Změny DNS u privátních koncových bodů

Při vytváření privátního koncového bodu se záznam prostředku CNAME DNS pro úložiště konfigurace aktualizuje na alias v subdoméně s předponou `privatelink`. Azure také vytvoří [privátní ZÓNU DNS](../dns/private-dns-overview.md) , která `privatelink` odpovídá subdoméně, a záznamy prostředků DNS pro privátní koncové body.

Při překladu adresy URL koncového bodu mimo virtuální síť se přeloží na veřejný koncový bod úložiště. Při řešení v rámci virtuální sítě, která je hostitelem privátního koncového bodu, se adresa URL koncového bodu přeloží na soukromý koncový bod.

Přístup pro klienty mimo virtuální síť můžete řídit prostřednictvím veřejného koncového bodu pomocí služby Azure Firewall.

Tento přístup umožňuje přístup k úložišti **pomocí stejného připojovacího řetězce** pro klienty ve virtuální síti, která je hostitelem privátních koncových bodů, i klientů mimo virtuální síť.

Pokud ve vaší síti používáte vlastní server DNS, klienti musí být schopni přeložit plně kvalifikovaný název domény (FQDN) pro koncový bod služby na IP adresu privátního koncového bodu. Nakonfigurujte server DNS tak, aby delegoval subdoménu privátního propojení s privátní zónou DNS pro virtuální síť, nebo nakonfigurujte záznamy A pro `AppConfigInstanceA.privatelink.azconfig.io` IP adresu privátního koncového bodu.

> [!TIP]
> Pokud používáte vlastní nebo místní server DNS, měli byste server DNS nakonfigurovat tak, aby přeložil název úložiště v `privatelink` subdoméně na IP adresu privátního koncového bodu. Můžete to udělat tak, že subdoménu delegujete `privatelink` do privátní zóny DNS virtuální sítě nebo nakonfigurujete zónu DNS na serveru DNS a PŘIDÁTE záznamy DNS.

## <a name="pricing"></a>Ceny

Povolení privátních koncových bodů vyžaduje úložiště konfigurace aplikace [úrovně Standard](https://azure.microsoft.com/pricing/details/app-configuration/) .  Další informace o cenách privátních propojení najdete v tématu [ceny za privátní propojení Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Další kroky

Další informace o vytvoření privátního koncového bodu pro úložiště konfigurace aplikace najdete v následujících článcích:

- [Vytvoření privátního koncového bodu pomocí centra privátního spojení v Azure Portal](../private-link/create-private-endpoint-portal.md)
- [Vytvoření privátního koncového bodu pomocí Azure CLI](../private-link/create-private-endpoint-cli.md)
- [Vytvoření privátního koncového bodu pomocí Azure PowerShell](../private-link/create-private-endpoint-powershell.md)

Naučte se konfigurovat server DNS pomocí privátních koncových bodů:

- [Překlad názvů pro prostředky ve virtuálních sítích Azure](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [Konfigurace DNS pro privátní koncové body](/azure/private-link/private-endpoint-overview#dns-configuration)
