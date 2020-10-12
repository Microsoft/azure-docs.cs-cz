---
title: Přehled brány pro samoobslužné hostování | Microsoft Docs
description: Přečtěte si, jak funkce samoobslužná brána v Azure API Management pomáhá organizacím spravovat rozhraní API v hybridních i cloudových prostředích.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: b560b02544eeb96167e68ed305d4d9942d2b1e0f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82232968"
---
# <a name="self-hosted-gateway-overview"></a>Přehled brány v místním prostředí

Tento článek vysvětluje, jak funkce samoobslužná brána v Azure API Management umožňuje hybridní a multi-cloudovou správu rozhraní API, prezentuje svoji architekturu na nejvyšší úrovni a zvýrazňuje její možnosti.

## <a name="hybrid-and-multi-cloud-api-management"></a>Hybridní a multi-cloudová Správa API

Funkce brány pro místní hostování rozšiřuje API Management podporu hybridních prostředí a prostředí pro více cloudů a umožňuje organizacím efektivně a bezpečně spravovat rozhraní API hostovaná místně a v cloudech z jediné služby API Management v Azure.

Díky samoobslužné bráně mají zákazníci flexibilitu při nasazování kontejnerové verze API Management brány do stejného prostředí, kde hostují svá rozhraní API. Všechny brány v místním prostředí jsou spravované z API Management služby, se kterou jsou federované, takže zákazníci poskytují možnosti viditelnosti a sjednocení správy napříč všemi interními a externími rozhraními API. Umístění bran do rozhraní API umožňuje zákazníkům optimalizovat toky provozu rozhraní API a řešit požadavky na zabezpečení a dodržování předpisů.

Každá služba API Management se skládá z následujících klíčových součástí:

-   Rovina správy vystavená jako rozhraní API, které slouží ke konfiguraci služby prostřednictvím Azure Portal, PowerShellu a dalších podporovaných mechanismů.
-   Brána (nebo rovina dat) zodpovídá za požadavky rozhraní API pro proxy, používání zásad a shromažďování telemetrie.
-   Portál pro vývojáře, který vývojáři používají k zjišťování, seznámení a zprovoznění používání rozhraní API

Ve výchozím nastavení jsou všechny tyto komponenty nasazené v Azure, což způsobí, že všechny přenosy rozhraní API (zobrazené jako plné černé šipky na obrázku níže) přecházejí z Azure bez ohledu na to, kde jsou hostované back-endy implementující rozhraní API. Provozní jednoduchost tohoto modelu přináší náklady na zvýšení latence, problémy s dodržováním předpisů a v některých případech i další poplatky za přenos dat.

![Tok provozu API bez bran pro samoobslužné hostování](media/self-hosted-gateway-overview/without-gateways.png)

Nasazení bran v místním prostředí do stejných prostředí, kde jsou hostované implementace rozhraní API back-endu, umožňují tok dat rozhraní API přímo do back-endové rozhraní API, což zlepšuje latenci, optimalizuje náklady na přenos dat a umožňuje dodržování předpisů a současně zachovává výhody, které mají jediný bod správy, pozorovatel a zjišťování všech rozhraní API v rámci organizace bez ohledu na to, kde jsou jejich implementace hostované.

![Tok provozu rozhraní API s branami pro samoobslužné hostování](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Balení a funkce

Brána v místním prostředí je v rámci každé služby API Management v rámci každé služby nainstalovaná do Azure v podobě kontejnerem funkčně ekvivalentní verze spravované brány. Samoobslužná brána je k dispozici jako [kontejner](https://aka.ms/apim/sputnik/dhub) Docker založený na systému Linux z Microsoft Container Registry. Dá se nasadit do Docker, Kubernetes nebo jakékoli jiné řešení orchestrace kontejnerů, které běží na serverovém clusteru místně, v cloudové infrastruktuře nebo pro účely vyhodnocení a vývoje na osobním počítači.

Ve spravovaných branách **nejsou k dispozici** následující funkce bran pro místní hostování:

- Protokoly služby Azure Monitor
- Nadřazený (back-end) TLS verze a Správa šifr
- Ověření certifikátů serveru a klienta pomocí [kořenových certifikátů certifikační autority](api-management-howto-ca-certificates.md) odeslaných do služby API Management Pokud chcete přidat podporu pro vlastní certifikační autoritu, přidejte vrstvu do image kontejneru samoobslužná brána, která nainstaluje kořenový certifikát certifikační autority.
- Integrace s [Service Fabric](../service-fabric/service-fabric-api-management-overview.md)
- Obnovení relace TLS
- Opětovné vyjednávání klientského certifikátu. To znamená, že pro klienty rozhraní API pro [ověřování klienta](api-management-howto-mutual-certificates-for-clients.md) musí být certifikáty přítomné jako součást počáteční metody handshake protokolu TLS. Pokud chcete zajistit, aby se při konfiguraci vlastního názvu hostitele pro samoobslužnou bránu povolilo nastavení certifikátu klienta vyjednat.
- Integrovaná mezipaměť. V tomto [dokumentu](api-management-howto-cache-external.md) se dozvíte, jak používat externí mezipaměť v samoobslužných branách.

## <a name="connectivity-to-azure"></a>Možnosti připojení k Azure

Samoobslužné brány vyžadují odchozí připojení TCP/IP k Azure na portu 443. Každá brána v místním prostředí musí být přidružená k jedné API Management službě a nakonfigurovaná přes rovinu správy. Samoobslužná brána používá připojení k Azure pro:

-   Hlášení stavu odesláním prezenčních zpráv každou minutu
-   Pravidelně kontroluje (každých 10 sekund) a používá aktualizace konfigurace, kdykoliv jsou k dispozici.
-   Odesílání protokolů a metrik požadavků do Azure Monitor, pokud je nakonfigurujete.
-   Odesílání událostí do Application Insights, pokud je nastaveno tak, aby

Když dojde ke ztrátě připojení k Azure, místní hostitelská brána nebude moci přijímat aktualizace konfigurace, nahlásit jejich stav nebo nahrát telemetrii.

Samoobslužná brána je navržená tak, aby byla neúspěšná a mohla docházet k dočasné ztrátě připojení k Azure. Dá se nasadit s místní zálohou konfigurace nebo bez ní. V bývalém případě budou brány v místním prostředí pravidelně ukládat záložní kopii poslední stažené konfigurace na trvalém svazku připojeném ke kontejneru nebo pod.

Když je zálohování konfigurace vypnuté a dojde k přerušení připojení k Azure:

-   Spouštění bran v místním prostředí bude i nadále fungovat s použitím kopie konfigurace v paměti.
-   Zastavení samoobslužných bran se nebude moct spustit.

Když je zapnuté Zálohování konfigurace a dojde k přerušení připojení k Azure:

-   Spouštění bran v místním prostředí bude i nadále fungovat s použitím kopie konfigurace v paměti.
-   Zastavené samoobslužné brány budou moci začít používat záložní kopii konfigurace.

Po obnovení připojení se v každé samoobslužné bráně ovlivněné výpadkem automaticky znovu připojí ke své přidružené API Management službě a stáhnou všechny aktualizace konfigurace, ke kterým došlo, když brána byla "offline".

## <a name="next-steps"></a>Další kroky

-   [Další základní informace o tomto tématu najdete na dokumentu White Paper.](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Nasazení samoobslužné brány do Docker](how-to-deploy-self-hosted-gateway-docker.md)
-   [Nasazení samoobslužné brány do Kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md)
