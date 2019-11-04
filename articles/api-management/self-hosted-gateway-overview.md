---
title: Přehled služby Azure API Management Gateway pro místní hostování | Microsoft Docs
description: Seznamte se s tím, jak brána Azure API Management Gateway umožňuje organizacím spravovat rozhraní API ve hybridních a cloudových prostředích.
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
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513715"
---
# <a name="self-hosted-api-management-gateway-overview"></a>Přehled API Management brány pro samoobslužné hostování

Tento článek vysvětluje, jak funkce samoobslužná brána umožňuje hybridní a multi-cloudovou správu rozhraní API, prezentuje svoji architekturu vysoké úrovně a zvýrazňuje základní funkce.

> [!NOTE]
> Funkce brány pro samoobslužné hostování je ve verzi Preview. V rámci verze Preview je brána v místním prostředí dostupná jenom pro vývojáře a úrovně Premium bez dalších poplatků. Úroveň pro vývojáře je omezená na jediné nasazení samoobslužné brány.

## <a name="hybrid-and-multi-cloud-api-management"></a>Hybridní a multi-cloudová Správa API

Funkce brány pro místní hostování rozšiřuje API Management podporu hybridních prostředí a prostředí pro více cloudů a umožňuje organizacím efektivně a bezpečně spravovat rozhraní API hostovaná místně a v cloudech z jediné služby API Management v Azure.

Díky samoobslužné bráně mají zákazníci flexibilitu při nasazování kontejnerové verze API Management brány do stejného prostředí, kde hostují svá rozhraní API. Všechny brány v místním prostředí jsou spravované z API Management služby, se kterou jsou federované, takže zákazníci poskytují možnosti viditelnosti a sjednocení správy napříč všemi interními a externími rozhraními API. Umístění bran do rozhraní API umožňuje zákazníkům optimalizovat toky provozu rozhraní API a řešit požadavky na zabezpečení a dodržování předpisů.

Každá služba API Management se skládá z následujících klíčových součástí:

-   Rovina správy vystavená jako rozhraní API, které slouží ke konfiguraci služby prostřednictvím Azure Portal, PowerShellu a dalších podporovaných mechanismů.
-   Brána (nebo rovina dat) zodpovídá za požadavky rozhraní API pro proxy, používání zásad a shromažďování telemetrie.
-   Portál pro vývojáře, který vývojáři používají k zjišťování, seznámení a zprovoznění používání rozhraní API

Ve výchozím nastavení jsou všechny tyto komponenty nasazené v Azure, což způsobí, že všechny přenosy rozhraní API (zobrazené jako plné černé šipky na obrázku níže) přecházejí z Azure bez ohledu na to, kde jsou hostované back-endy implementující rozhraní API. Provozní jednoduchost tohoto modelu přináší náklady na zvýšení latence, problémy s dodržováním předpisů a v některých případech i další poplatky za přenos dat.

![Tok provozu API bez bran pro samoobslužné hostování](media/self-hosted-gateway-overview/without-gateways.png)

Nasazení bran v místním prostředí do stejných prostředí jako implementace rozhraní API back-endu a jejich přidání do služby API Management umožňuje tok provozu rozhraní API přímo do back-endové rozhraní API, což zlepšuje latenci, optimalizuje náklady na přenos dat a povoluje dodržování předpisů při zachování výhod s jediným bodem správy a zjišťování všech rozhraní API v rámci organizace bez ohledu na to, kde jsou jejich implementace hostované.

![Tok provozu rozhraní API s branami pro samoobslužné hostování](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Balení a funkce

Brána v místním prostředí je v rámci každé služby API Management v rámci každé služby nainstalovaná do Azure v podobě kontejnerem funkčně ekvivalentní verze spravované brány. Samoobslužná brána je k dispozici jako kontejner Docker založený na systému Linux z Microsoft Container Registry. Dá se nasadit do Docker, Kubernetes nebo do jiného řešení orchestrace kontejnerů, které běží na stolních počítačích, na serverovém clusteru nebo v cloudové infrastruktuře.

> [!IMPORTANT]
> Některé funkce, které jsou k dispozici ve spravované bráně, ještě nejsou k dispozici ve verzi Preview. Hlavně: protokolování do zásad centra událostí, Service Fabric Integration, HTTP/2 pro příjem dat. V samoobslužné bráně není k dispozici žádný plán k zpřístupnění integrované mezipaměti.

## <a name="connectivity-to-azure"></a>Připojení k Azure

Samoobslužná brána vyžaduje odchozí připojení TCP/IP k Azure na portu 443. Každá brána, která je v místním prostředí, musí být přidružená k jedné API Management službě a nakonfigurovaná přes svou rovinu správy. Samoobslužná brána používá připojení k Azure pro:

-   Hlášení stavu odesláním prezenčních zpráv každou minutu
-   Pravidelně kontroluje (každých 10 sekund) a používá aktualizace konfigurace, kdykoliv jsou k dispozici.
-   Odesílání protokolů a metrik požadavků do Azure Monitor, pokud je nakonfigurujete.
-   Odesílání událostí do Application Insights, pokud je nastaveno tak, aby

Když dojde ke ztrátě připojení k Azure, místní hostitelská brána nebude moci přijímat aktualizace konfigurace, nahlásit jejich stav nebo nahrát telemetrii.

Samoobslužná brána je navržená tak, aby se nezdařila statická, a mohla by zacházet s dočasnou ztrátou připojení k Azure. Dá se nasadit s povolenou místní zálohou konfigurace nebo bez ní. V bývalém případě budou brány v místním prostředí pravidelně ukládat záložní kopii konfigurace na trvalém svazku připojeném ke kontejneru nebo pod ním.

Když je zálohování konfigurace vypnuté a dojde k přerušení připojení k Azure:

-   Používané samoobslužné brány budou dál fungovat s použitím kopie konfigurace v paměti.
-   Zastavení samoobslužných bran se nebude moct spustit.

Když je zapnuté Zálohování konfigurace a dojde k přerušení připojení k Azure:

-   Používané samoobslužné brány budou dál fungovat s použitím kopie konfigurace v paměti.
-   Zastavení místních bran se spustí pomocí záložní kopie konfigurace.

Po obnovení připojení se v každé samoobslužné bráně ovlivněné výpadkem automaticky znovu připojí ke své přidružené API Management službě a stáhnou všechny aktualizace konfigurace, ke kterým došlo, když brána byla "offline".

## <a name="next-steps"></a>Další kroky

-   [Další základní informace o tomto tématu najdete na dokumentu White Paper.](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Nasazení samoobslužné brány do Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
-   [Nasazení samoobslužné brány do Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
