---
title: Použití privátní koncové body pro konfiguraci aplikace Azure
description: Zabezpečení úložiště konfigurace aplikací pomocí privátních koncových bodů
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: f18672b9e3a368a833fc8cba279d748dfe3c2a9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366764"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>Použití privátní koncové body pro konfiguraci aplikace Azure

[Privátní koncové body](../private-link/private-endpoint-overview.md) pro Azure App Configuration můžete povolit klientům ve virtuální síti (VNet) k bezpečnému přístupu k datům přes [privátní propojení](../private-link/private-link-overview.md). Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě pro váš obchod Konfigurace aplikací. Síťový provoz mezi klienty ve virtuální síti a v úložišti Konfigurace aplikací prochází přes virtuální síť pomocí privátního propojení v páteřní síti Microsoftu, což eliminuje vystavení veřejnému internetu.

Použití soukromých koncových bodů pro úložiště konfigurace aplikací umožňuje:
- Zabezpečte podrobnosti konfigurace aplikace konfigurací brány firewall tak, aby blokovala všechna připojení ke konfiguraci aplikací ve veřejném koncovém bodě.
- Zvyšte zabezpečení virtuální sítě (VNet), která zajišťuje, že data neuniknou z virtuální sítě.
- Bezpečně se připojujte k úložišti Konfigurace aplikací z místních sítí, které se připojují k virtuální síti pomocí [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [ExpressRoutes](../expressroute/expressroute-locations.md) s privátním partnerem.

> [!NOTE]
> Azure App Configuration nabízí použití privátních koncových bodů jako veřejné verze Preview. Nabídky ve verzi Public Preview umožňují zákazníkům experimentovat s novými funkcemi před jejich oficiálním vydáním.  Funkce a služby veřejné verze Preview nejsou určeny pro produkční použití.

## <a name="conceptual-overview"></a>Koncepční přehled

Privátní koncový bod je speciální síťové rozhraní pro službu Azure ve [vaší virtuální síti](../virtual-network/virtual-networks-overview.md) (Virtuální síť). Když vytvoříte soukromý koncový bod pro úložiště konfigurace aplikací, poskytuje zabezpečené připojení mezi klienty ve vaší virtuální síti a úložiště konfigurace. Privátnímu koncovému bodu je přiřazena IP adresa z rozsahu IP adres vaší virtuální sítě. Připojení mezi privátním koncovým bodem a úložištěm konfigurace používá zabezpečené soukromé propojení.

Aplikace ve virtuální síti se můžou připojit k úložišti konfigurace přes privátní koncový bod **pomocí stejných připojovacích řetězců a autorizačních mechanismů, které by jinak používaly**. Soukromé koncové body lze použít se všemi protokoly podporovanými úložištěm Konfigurace aplikací.

Zatímco konfigurace aplikace nepodporuje koncové body služby, soukromé koncové body lze vytvořit v podsítích, které používají [koncové body služby](../virtual-network/virtual-network-service-endpoints-overview.md). Klienti v podsíti se můžou bezpečně připojit k úložišti konfigurace aplikací pomocí privátního koncového bodu, zatímco používají koncové body služby pro přístup k ostatním.  

Když vytvoříte soukromý koncový bod pro službu ve virtuální síti, žádost o souhlas se odešle ke schválení vlastníkovi účtu služby. Pokud je uživatel požadující vytvoření privátního koncového bodu také vlastníkem účtu, bude tato žádost o souhlas automaticky schválena.

Vlastníci účtu služby můžou spravovat žádosti `Private Endpoints` o souhlas a soukromé koncové body prostřednictvím karty config store na [webu Azure Portal](https://portal.azure.com).

### <a name="private-endpoints-for-app-configuration"></a>Privátní koncové body pro konfiguraci aplikace 

Při vytváření privátní koncový bod, musíte zadat úložiště konfigurace aplikace, ke kterému se připojuje. Pokud máte více instancí konfigurace aplikace v rámci účtu, budete potřebovat samostatný soukromý koncový bod pro každý obchod.

### <a name="connecting-to-private-endpoints"></a>Připojení k privátním koncovým bodům

Azure spoléhá na překlad DNS pro směrování připojení z virtuální sítě do úložiště konfigurace přes privátní propojení. Řetězce připojení můžete rychle najít na webu Azure Portal tak, že vyberete úložiště konfigurace aplikací a pak vyberete **nastavení** > **přístupových klíčů**.  

> [!IMPORTANT]
> Použijte stejný připojovací řetězec pro připojení k úložišti konfigurace aplikace pomocí privátní koncové body, jako byste použít pro veřejný koncový bod. Nepřipojujte se k `privatelink` účtu úložiště pomocí jeho adresy URL subdomény.

## <a name="dns-changes-for-private-endpoints"></a>Změny DNS pro soukromé koncové body

Při vytváření privátního koncového bodu se záznam o prostředku Dns CNAME pro `privatelink`úložiště konfigurace aktualizuje na alias v subdoméně s předponou . Azure také vytvoří [privátní zónu DNS](../dns/private-dns-overview.md) odpovídající `privatelink` subdoméně se záznamy o prostředcích DNS A pro privátní koncové body.

Když vyřešíte adresu URL koncového bodu z mimo virtuální síť, vyřeší se na veřejný koncový bod úložiště. Při vyřešení z virtuální sítě hostující privátní koncový bod, adresa URL koncového bodu překládá na soukromý koncový bod.

Přístup klientů mimo virtuální síť můžete řídit prostřednictvím veřejného koncového bodu pomocí služby Azure Firewall.

Tento přístup umožňuje přístup k úložišti **pomocí stejného připojovacího řetězce** pro klienty na virtuální síti hostování privátní koncové body, jakož i klienty mimo virtuální síť.

Pokud v síti používáte vlastní server DNS, musí být klienti schopni přeložit plně kvalifikovaný název domény (FQDN) pro koncový bod služby na privátní ip adresu koncového bodu. Nakonfigurujte server DNS tak, aby delegoval subdoménu privátního propojení do privátní zóny DNS pro virtuální síť, nebo nakonfigurujte záznamy A s `AppConfigInstanceA.privatelink.azconfig.io` privátní IP adresou koncového bodu.

> [!TIP]
> Při použití vlastního nebo místního serveru DNS byste měli nakonfigurovat server `privatelink` DNS tak, aby překládal název úložiště v subdoméně na privátní IP adresu koncového bodu. Můžete to provést delegováním `privatelink` subdomény do privátní zóny DNS virtuální sítě nebo konfigurací zóny DNS na serveru DNS a přidáním záznamů DNS A.

## <a name="pricing"></a>Ceny

Povolení soukromých koncových bodů vyžaduje úložiště konfigurace aplikací [úrovně Standard.](https://azure.microsoft.com/pricing/details/app-configuration/)  Další informace o podrobnostech o cenách privátních odkazů najdete v článku [Ceny privátních odkazů Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Další kroky

Další informace o vytvoření privátního koncového bodu pro váš obchod Konfigurace aplikací naleznete v následujících článcích:

- [Vytvoření privátního koncového bodu pomocí Centra privátního propojení na webu Azure Portal](../private-link/create-private-endpoint-portal.md)
- [Vytvoření privátního koncového bodu pomocí azure cli](../private-link/create-private-endpoint-cli.md)
- [Vytvoření privátního koncového bodu pomocí Azure PowerShellu](../private-link/create-private-endpoint-powershell.md)

Naučte se konfigurovat dns server s privátními koncovými body:

- [Překlad názvů pro prostředky ve virtuálních sítích Azure](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [Konfigurace DNS pro soukromé koncové body](/azure/private-link/private-endpoint-overview#dns-configuration)
