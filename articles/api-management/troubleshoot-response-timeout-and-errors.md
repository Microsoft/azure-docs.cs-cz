---
title: Řešení potíží s časovými limity odezvy klienta a chybami API Management
description: Řešení chyb přerušovaného připojení a souvisejících potíží s latencí v API Management
author: vladvino
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: apimpm
ms.service: api-management
ms.openlocfilehash: 6cace4a02c8d45cacbbc34e9778b5c4a78ada27f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576522"
---
# <a name="troubleshooting-client-response-timeouts-and-errors-with-api-management"></a>Řešení potíží s časovými limity odezvy klienta a chybami API Management

Tento článek vám pomůže vyřešit chyby přerušovaného připojení a souvisejících potíží s latencí v [Azure API Management](./api-management-key-concepts.md). Konkrétně tento článek poskytuje informace a řešení potíží pro vyčerpání zdrojových adres portů (SNAT). Pokud potřebujete další pomoc, obraťte se na odborníky na Azure v [komunitní podpoře komunity Azure](https://azure.microsoft.com/support/community/) nebo na žádost o podporu v rámci [podpory Azure](https://azure.microsoft.com/support/options/).

## <a name="symptoms"></a>Příznaky

Klientské aplikace volající rozhraní API prostřednictvím služby API Management (APIM) mohou vykazovat jeden nebo několik následujících příznaků:

* Občasné chyby HTTP 500
* Chybové zprávy časového limitu

Tyto příznaky se manifestují jako instance `BackendConnectionFailure` v [protokolech prostředků Azure monitor](../azure-monitor/essentials/resource-logs.md).

## <a name="cause"></a>Příčina

K tomuto vzoru příznaků často dochází v důsledku omezení portů překladu adres (SNAT) u vaší služby APIM.

Když klient zavolá jedno z vašich rozhraní APIM API, služba Azure API Management otevře port SNAT pro přístup k back-endu API. Jak je popsáno v části [odchozí připojení v Azure](../load-balancer/load-balancer-outbound-connections.md), Azure používá překlad zdrojového síťového adres (SNAT) a Load Balancer (nezveřejněné pro zákazníky) ke komunikaci s koncovými body mimo Azure ve veřejném adresním prostoru IP adres a koncovým bodům interním pro Azure, které nepoužívají [koncové body služby Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md). Tato situace se vztahuje jenom na rozhraní API back-endu zveřejněná na veřejných IP adresách.

Každé instanci API Management služby se zpočátku dostal předem přidělený počet portů SNAT. Toto omezení má vliv na otevírání připojení ke stejné kombinaci hostitelů a portů. Porty SNAT se používají, když máte opakovaná volání stejné kombinace adres a portů. Po uvolnění portu SNAT se port dá v případě potřeby použít k opětovnému použití. Nástroj pro vyrovnávání zatížení sítě Azure uvolní porty SNAT z uzavřených připojení jenom po uplynutí čtyř minut.

Rychlé pokusy požadavků klientů na vaše rozhraní API můžou vyčerpat předem přidělenou kvótu portů SNAT, pokud tyto porty nejsou zavřené a dostatečně rychle recyklované, aby služba APIM nemohla včas zpracovávat požadavky klienta.

## <a name="mitigations-and-solutions"></a>Zmírnění rizik a řešení

Vyřešení problému vyčerpání portů SNAT nejprve vyžaduje diagnostiku a optimalizaci výkonu back-endové služby.

Obecné strategie pro zmírnění vyčerpání portů SNAT jsou popsány v tématu [Poradce při potížích s chybami odchozích připojení](../load-balancer/troubleshoot-outbound-connection.md) z *Azure Load Balancer* dokumentaci. Z těchto strategií platí následující pravidla pro API Management.

### <a name="scale-your-apim-instance"></a>Škálování instance APIM

Každá instance API Management má přiděleno množství portů SNAT na základě jednotek APIM. Dalším jednotkám můžete přidělit další porty SNAT změnou velikosti instance API Management. Další informace najdete v tématu [škálování služby API Management](upgrade-and-scale.md#scale-your-api-management-service) .

> [!NOTE]
> Využití portu SNAT není momentálně k dispozici jako metrika pro automatické škálování API Managementch jednotek.

### <a name="use-multiple-ips-for-your-backend-urls"></a>Použití více IP adres pro back-endové adresy URL

Každé připojení z vaší instance APIM do stejné cílové IP adresy a cílového portu back-end služby bude používat port SNAT, aby bylo možné zachovat odlišný tok přenosů. Bez různých portů SNAT pro návratový provoz ze služby na pozadí by APIM neměl žádný způsob, jak oddělit jednu odpověď od druhé.

Vzhledem k tomu, že se porty SNAT dají znovu použít, pokud se cílová IP adresa nebo cílový port liší, další způsob, jak se vyhnout vyčerpání portů SNAT, je použití více IP adres pro adresy URL back-end služby.

Další informace najdete v tématu [odchozí proxy Azure Load Balancer](../load-balancer/load-balancer-outbound-connections.md).

### <a name="place-your-apim-and-backend-service-in-the-same-vnet"></a>Umístěte APIM a back-end službu do stejné virtuální sítě

Pokud je vaše back-end rozhraní API hostované ve službě Azure, která podporuje *koncové body služby* , například App Service, můžete se vyhnout problémům s vyčerpáním portů SNAT tím, že umístíte instanci APIM a back-end službu do stejné virtuální sítě a zpřístupníte ji prostřednictvím [koncových bodů služby](../virtual-network/virtual-network-service-endpoints-overview.md) nebo [privátních koncových bodů](../private-link/private-endpoint-overview.md). Když použijete společné virtuální sítě a koncové body služby v podsíti integrace, odchozí přenosy z vaší instance APIM do těchto služeb obcházejí Internet, čímž se vyhnete omezením portů SNAT. Podobně platí, že pokud používáte virtuální síť a soukromé koncové body, nebudete mít k tomuto cíli žádné odchozí problémy portů SNAT.

Podrobnosti najdete v tématu [použití API Management Azure s virtuálními sítěmi](api-management-using-with-vnet.md) a [integrace App Service s Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md).

### <a name="place-your-apim-in-a-virtual-network-and-route-outbound-calls-to-azure-firewall"></a>Umístění APIM do virtuální sítě a směrování odchozích volání do Azure Firewall

Podobně jako při umístění služeb APIM a back-endu ve virtuální síti můžete využívat Azure Firewall ve virtuální síti s vaší službou APIM a pak směrovat odchozí volání APIM do Azure Firewall. Mezi APIM a Azure Firewall (v rámci stejné virtuální sítě) nejsou vyžadovány žádné porty SNAT. Pro připojení SNAT k back-end službám Azure Firewall má 64 000 dostupných portů, mnohem větší množství, než je přiděleno instancím APIM.

Další informace najdete v dokumentaci k [Azure firewall](../firewall/overview.md) .

### <a name="consider-response-caching-and-other-backend-performance-tuning"></a>Zvažte ukládání odpovědí do mezipaměti a další ladění výkonu back-endu.

Další možné zmírnění, které by bylo potřeba zvážit, je zlepšení doby zpracování back-end rozhraní API. Jedním ze způsobů, jak to provést, je nakonfigurovat určitá rozhraní API s ukládáním do mezipaměti odpovědí a snížit tak latenci mezi klientskými aplikacemi, které volají vaše rozhraní API a APIM back-endu.

Další informace najdete v tématu [Přidání ukládání do mezipaměti za účelem zvýšení výkonu v Azure API Management](api-management-howto-cache.md).

### <a name="consider-implementing-access-restriction-policies"></a>Zvažte implementaci zásad omezení přístupu.

Pokud to vyhovuje vašemu firemnímu scénáři, můžete pro svůj API Management produkt implementovat zásady omezení přístupu. `rate-limit-by-key`Můžete například použít zásadu, která zabrání špičkám využití rozhraní API na základě klíčů, a to omezením rychlosti volání za zadané časové období.

Další informace najdete v tématu [zásady omezení přístupu API Management](api-management-access-restriction-policies.md) .

## <a name="see-also"></a>Viz také

* [Azure Load Balancer: odstraňování potíží s chybami odchozích připojení](../load-balancer/troubleshoot-outbound-connection.md)
* [Azure App Service: řešení chyb občasného odchozího připojení](../app-service/troubleshoot-intermittent-outbound-connection-errors.md)
