---
title: Používání privátních koncových bodů pro konfiguraci aplikací Azure
description: Zabezpečení úložiště konfigurace aplikací pomocí privátních koncových bodů
services: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: 6cadadfb3623d05dd3ae3851acd5eaca13860023
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96929839"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>Používání privátních koncových bodů pro konfiguraci aplikací Azure

Pomocí [privátních koncových bodů](../private-link/private-endpoint-overview.md) pro konfiguraci aplikací Azure můžete klientům ve virtuální síti (VNET) umožňovat zabezpečený přístup k datům prostřednictvím [privátního propojení](../private-link/private-link-overview.md). Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě pro úložiště konfigurace aplikace. Síťový provoz mezi klienty ve virtuální síti a úložištěm konfigurace aplikace prochází přes virtuální síť pomocí privátního odkazu v páteřní síti Microsoftu a eliminuje tak expozici veřejnému Internetu.

Použití privátních koncových bodů pro vaše úložiště konfigurace aplikace vám umožní:
- Zabezpečte podrobnosti konfigurace aplikace konfigurací brány firewall tak, aby blokovala všechna připojení ke konfiguraci aplikace ve veřejném koncovém bodu.
- Zvyšte zabezpečení virtuální sítě (VNet), která z virtuální sítě zajišťuje, že se data neřídí.
- Bezpečně se připojte k úložišti konfigurace aplikace z místních sítí, které se připojují k virtuální síti pomocí [sítě VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [ExpressRoutes](../expressroute/expressroute-locations.md) s privátním partnerským vztahem.

## <a name="conceptual-overview"></a>Základní přehled

Privátní koncový bod je speciální síťové rozhraní pro službu Azure ve vaší [Virtual Network](../virtual-network/virtual-networks-overview.md) (virtuální síť). Když vytvoříte privátní koncový bod pro úložiště konfigurace aplikace, zajistíte zabezpečené připojení mezi klienty ve vaší virtuální síti a vaším úložištěm konfigurace. Privátnímu koncovému bodu je přiřazena IP adresa z rozsahu IP adres vaší virtuální sítě. Připojení mezi soukromým koncovým bodem a úložištěm konfigurace používá zabezpečený privátní odkaz.

Aplikace ve virtuální síti se můžou ke konfiguračnímu úložišti připojit prostřednictvím privátního koncového bodu **pomocí stejných připojovacích řetězců a mechanismů autorizace, které by jinak používaly**. Soukromé koncové body lze použít se všemi protokoly podporovanými úložištěm konfigurace aplikace.

I když konfigurace aplikace nepodporuje koncové body služby, v podsítích, které používají [koncové body služby](../virtual-network/virtual-network-service-endpoints-overview.md), je možné vytvořit privátní koncové body. Klienti v podsíti se můžou bezpečně připojit k úložišti konfigurace aplikace pomocí privátního koncového bodu při použití koncových bodů služby k přístupu k ostatním.  

Při vytváření privátního koncového bodu pro službu ve vaší virtuální síti se ke schválení vlastníka účtu služby pošle žádost o souhlas. Pokud uživatel žádající o vytvoření privátního koncového bodu je zároveň vlastníkem účtu, je tato žádost o souhlas automaticky schválena.

Vlastníci účtu služby můžou spravovat žádosti o souhlas a soukromé koncové body prostřednictvím `Private Endpoints` karty úložiště konfigurace v [Azure Portal](https://portal.azure.com).

### <a name="private-endpoints-for-app-configuration"></a>Privátní koncové body pro konfiguraci aplikace 

Při vytváření privátního koncového bodu je nutné zadat úložiště konfigurace aplikace, ke kterému se připojí. Pokud máte v rámci jednoho účtu více instancí konfigurace aplikace, budete pro každého úložiště potřebovat samostatný soukromý koncový bod.

### <a name="connecting-to-private-endpoints"></a>Připojování k soukromým koncovým bodům

Azure při směrování připojení z virtuální sítě do úložiště konfigurace prostřednictvím privátního propojení spoléhá na překlad DNS. Řetězce připojení můžete rychle najít v Azure Portal tak, že vyberete úložiště konfigurace aplikace a pak vyberete **Nastavení**  >  **přístupové klíče**.  

> [!IMPORTANT]
> Použijte stejný připojovací řetězec pro připojení k úložišti konfigurace aplikace pomocí privátních koncových bodů, protože byste použili pro veřejný koncový bod. Nepřipojujte se k úložišti pomocí `privatelink` adresy URL své subdomény.

## <a name="dns-changes-for-private-endpoints"></a>Změny DNS u privátních koncových bodů

Při vytváření privátního koncového bodu se záznam prostředku CNAME DNS pro úložiště konfigurace aktualizuje na alias v subdoméně s předponou `privatelink` . Azure také vytvoří [privátní ZÓNU DNS](../dns/private-dns-overview.md) , která odpovídá `privatelink` subdoméně, a záznamy prostředků DNS pro privátní koncové body.

Při překladu adresy URL koncového bodu z virtuální sítě, která je hostitelem privátního koncového bodu, se přeloží na soukromý koncový bod úložiště. Při vyřešení z vnějšku virtuální sítě se adresa URL koncového bodu převede na veřejný koncový bod. Při vytváření privátního koncového bodu je veřejný koncový bod zakázán.

Pokud ve vaší síti používáte vlastní server DNS, klienti musí být schopni přeložit plně kvalifikovaný název domény (FQDN) pro koncový bod služby na IP adresu privátního koncového bodu. Nakonfigurujte server DNS tak, aby delegoval subdoménu privátního propojení s privátní zónou DNS pro virtuální síť, nebo nakonfigurujte záznamy A pro `AppConfigInstanceA.privatelink.azconfig.io` IP adresu privátního koncového bodu.

> [!TIP]
> Pokud používáte vlastní nebo místní server DNS, měli byste server DNS nakonfigurovat tak, aby přeložil název úložiště v `privatelink` subdoméně na IP adresu privátního koncového bodu. Můžete to udělat tak `privatelink` , že subdoménu delegujete do privátní zóny DNS virtuální sítě nebo nakonfigurujete ZÓNU DNS na serveru DNS a přidáte záznamy DNS.

## <a name="pricing"></a>Ceny

Povolení privátních koncových bodů vyžaduje úložiště konfigurace aplikace [úrovně Standard](https://azure.microsoft.com/pricing/details/app-configuration/) .  Další informace o cenách privátních propojení najdete v tématu [ceny za privátní propojení Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Další kroky

Další informace o vytvoření privátního koncového bodu pro úložiště konfigurace aplikace najdete v následujících článcích:

- [Vytvoření privátního koncového bodu pomocí centra privátního spojení v Azure Portal](../private-link/create-private-endpoint-portal.md)
- [Vytvoření privátního koncového bodu pomocí Azure CLI](../private-link/create-private-endpoint-cli.md)
- [Vytvoření privátního koncového bodu pomocí Azure PowerShell](../private-link/create-private-endpoint-powershell.md)

Naučte se konfigurovat server DNS pomocí privátních koncových bodů:

- [Překlad názvů pro prostředky ve virtuálních sítích Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [Konfigurace DNS pro privátní koncové body](../private-link/private-endpoint-overview.md#dns-configuration)