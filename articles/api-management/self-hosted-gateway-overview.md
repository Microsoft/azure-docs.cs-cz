---
title: Přehled brány Azure API se správou vlastního hostitele | Dokumenty společnosti Microsoft
description: Zjistěte, jak brána Azure API Management s vlastním hostitelem pomáhá organizacím spravovat rozhraní API v hybridních prostředích a prostředích s více cloudy.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 415f0e209e607a863d715b1a66a2435603a662f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513715"
---
# <a name="self-hosted-api-management-gateway-overview"></a>Přehled brány pro správu rozhraní API s vlastním hostitelem

Tento článek vysvětluje, jak funkce brány s vlastním hostitelem umožňuje správu hybridního a vícecloudového rozhraní API, představuje architekturu vysoké úrovně a zdůrazňuje jeho základní možnosti.

> [!NOTE]
> Funkce brány s vlastním hostitelem je ve verzi Preview. Během předběžné verze je brána s vlastním hostitelem dostupná pouze na úrovních Vývojář a Premium bez dalších poplatků. Úroveň pro vývojáře je omezena na jedno nasazení brány hostované sami sebou.

## <a name="hybrid-and-multi-cloud-api-management"></a>Hybridní správa rozhraní API a multicloudu

Funkce brány s vlastním hostitelem rozšiřuje podporu správy rozhraní API pro hybridní a vícecloudová prostředí a umožňuje organizacím efektivně a bezpečně spravovat rozhraní API hostovaná místně a napříč cloudy z jediné služby správy rozhraní API v Azure.

Díky vlastní hostované bráně mají zákazníci možnost nasadit kontejnerizovanou verzi komponenty brány api management u stejného prostředí, kde hostují svá rozhraní API. Všechny brány hostované samostatně jsou spravovány ze služby API Management, se kterou jsou federovány, a poskytují tak zákazníkům viditelnost a jednotné prostředí pro správu ve všech interních i externích rozhraních API. Umístění bran v blízkosti rozhraní API umožňuje zákazníkům optimalizovat toky provozu rozhraní API a řešit požadavky na zabezpečení a dodržování předpisů.

Každá služba správy rozhraní API se skládá z následujících klíčových součástí:

-   Rovina správy, vystavená jako rozhraní API, používaná ke konfiguraci služby prostřednictvím portálu Azure, PowerShellu a dalších podporovaných mechanismů.
-   Brána (nebo rovina dat) je zodpovědná za proxysonování požadavků rozhraní API, použití zásad a shromažďování telemetrie
-   Vývojářský portál používaný vývojáři k objevování, učení a na palubě k používání rozhraní API

Ve výchozím nastavení jsou všechny tyto součásti nasazeny v Azure, což způsobuje, že veškerý provoz rozhraní API (zobrazený jako plné černé šipky na obrázku níže) toku přes Azure bez ohledu na to, kde back-endy implementující rozhraní API jsou hostované. Provozní jednoduchost tohoto modelu přichází za cenu zvýšené latence, problémů s dodržováním předpisů a v některých případech dalších poplatků za přenos dat.

![Tok provozu rozhraní API bez vlastních hostovaných bran](media/self-hosted-gateway-overview/without-gateways.png)

Nasazení samoobslužných bran do stejných prostředí jako implementace back-endového rozhraní API a jejich přidání do služby API Management umožňuje přenos rozhraní API tok přímo do rozhraní API back-endu, což zlepšuje latenci, optimalizuje náklady na přenos dat a umožňuje dodržování předpisů při zachování výhod, které mají jeden bod správy a zjišťování všech api v rámci organizace bez ohledu na to, kde jsou hostovány jejich implementace.

![Tok provozu rozhraní API s vlastními hostovanými bránami](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Balení a vlastnosti

Brána s vlastním hostitelem je kontejnerizovaná, funkčně ekvivalentní verze spravované brány nasazené do Azure jako součást každé služby správy rozhraní API. Brána s vlastním hostitelem je k dispozici jako kontejner Dockeru založeného na Linuxu z registru kontejnerů Microsoft. Dá se nasadit do Dockeru, Kubernetes nebo jiného řešení orchestrace kontejnerů spuštěného na desktopové, serverovém clusteru nebo cloudové infrastruktuře.

> [!IMPORTANT]
> Některé funkce dostupné ve spravované bráně ještě nejsou ve verzi Preview k dispozici. Nejpozoruhodnější: Protokolovat do centra událostí zásady, Service Fabric integrace, příjem HTTP/2. Neexistuje žádný plán, který by zpřístupnioval vestavěnou mezipaměť v samoobslužné bráně.

## <a name="connectivity-to-azure"></a>Připojení k Azure

Samoobslužná brána vyžaduje odchozí připojení TCP/IP k Azure na portu 443. Každá brána s vlastním hostitelem musí být přidružena k jedné službě správy rozhraní API a je nakonfigurována prostřednictvím své roviny správy. Samoobslužná brána využívá připojení k Azure pro:

-   Hlášení stavu odesíláním zpráv o prezenčním signálu každou minutu
-   Pravidelná kontrola (každých 10 sekund) a použití aktualizací konfigurace, kdykoli jsou k dispozici
-   Odesílání protokolů požadavků a metrik do Azure Monitoru, pokud je k tomu nakonfigurované
-   Odesílání událostí do Application Insights, pokud je k tomu nastaveno

Při ztrátě připojení k Azure, samoobslužná brána nebude moci přijímat aktualizace konfigurace, hlásit jeho stav nebo nahrát telemetrii.

Samoobslužná brána je navržena tak, aby "selhala staticky" a může přežít dočasnou ztrátu připojení k Azure. Lze jej nasadit se zapnutou zálohou místní konfigurace nebo bez ní. V prvním případě budou brány hostované samostatně pravidelně ukládat záložní kopii konfigurace na trvalém svazku připojeném ke kontejneru nebo podu.

Když je záloha konfigurace vypnutá a připojení k Azure se přeruší:

-   Spuštěné brány s vlastním hostitelem budou nadále fungovat pomocí kopie konfigurace v paměti.
-   Zastavené brány hostované samostatně se nebudou moci spustit.

Když je zapnutá konfigurace zálohování a připojení k Azure je přerušeno:

-   Spuštěné brány s vlastním hostitelem budou nadále fungovat pomocí kopie konfigurace v paměti.
-   Zastavené brány s vlastním hostitelem začnou používat záložní kopii konfigurace.

Po obnovení připojení se každá samoobslužná brána ovlivněná výpadkem automaticky znovu připojí ke přidružené službě správy rozhraní API a stáhne všechny aktualizace konfigurace, ke kterým došlo v době, kdy byla brána "offline".

## <a name="next-steps"></a>Další kroky

-   [Přečtěte si bílou knihu pro další informace o tomto tématu](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Nasazení brány hostované sami nebo sami do Dockeru](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
-   [Nasazení brány s vlastním hostitelem do Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
