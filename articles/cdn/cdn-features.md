---
title: Porovnání funkcí produktu Azure Content Delivery Network (CDN)
description: Seznamte se s podporovanými funkcemi v jednotlivých produktech Azure Content Delivery Network (CDN).
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: overview
ms.date: 11/15/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 3a794d8915c820abecd530911f05ac649e5e538c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100379909"
---
# <a name="what-are-the-comparisons-between-azure-cdn-product-features"></a>Jaké jsou srovnání mezi Azure CDNmi funkcemi produktu?

Azure Content Delivery Network (CDN) zahrnuje čtyři produkty: 

* **Azure CDN Standard od Microsoftu**
* **Azure CDN Standard z Akamai**
* **Azure CDN Standard z Verizon**
* **Azure CDN Premium z Verizon**. 

Následující tabulka obsahuje porovnání funkcí, které jsou dostupné v jednotlivých produktech.

| **Funkce a optimalizace výkonu** | **Microsoft – standardní** | **Akamai úrovně Standard** | **Verizon úrovně Standard** | **Verizon úrovně Premium** |
| --- | --- | --- | --- | --- |
| [Akcelerace dynamických webů](./cdn-dynamic-site-acceleration.md)  | Nabízeno přes [službu Azure front-dveří](../frontdoor/front-door-overview.md) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Akcelerace dynamického webu – adaptivní komprese obrázků](./cdn-dynamic-site-acceleration.md#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Akcelerace dynamického webu – předběžné načtení objektů](./cdn-dynamic-site-acceleration.md#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Obecná optimalizace webového doručování](./cdn-optimization-overview.md#general-web-delivery)  | **&#x2713;** | **&#x2713;** vyberte tento typ optimalizace, pokud je průměrná velikost souboru menší než 10 MB.  | **&#x2713;** |  **&#x2713;** |
| [Optimalizace streamování videa](./cdn-media-streaming-optimization.md)  | prostřednictvím obecného webového doručování | **&#x2713;**  | prostřednictvím obecného webového doručování |  prostřednictvím obecného webového doručování |
| [Optimalizace velkých souborů](./cdn-large-file-optimization.md)  | prostřednictvím obecného webového doručování | **&#x2713;** vyberte tento typ optimalizace, pokud je průměrná velikost souboru větší než 10 MB.   | prostřednictvím obecného webového doručování |  prostřednictvím obecného webového doručování |
| Změnit typ optimalizace | |**&#x2713;** | | |
| Zdrojový port |Všechny porty TCP |[Povolené počáteční porty](/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Všechny porty TCP |Všechny porty TCP |
| [Globální vyrovnávání zatížení serveru (GSLB)](../traffic-manager/traffic-manager-load-balancing-azure.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Rychlé vyprázdnění](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;**, vyprázdnit všechny a zástupné znaky nejsou aktuálně Azure CDN z Akamai. |**&#x2713;** |**&#x2713;** |
| [Předběžné načítání prostředku](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Nastavení mezipaměti nebo hlaviček (pomocí [pravidel ukládání do mezipaměti](cdn-caching-rules.md))  |**&#x2713;** pomocí [modulu Standard Rules](cdn-standard-rules-engine.md)  |**&#x2713;** |**&#x2713;** | |
| Přizpůsobitelný modul pro doručování obsahu založený na pravidlech |**&#x2713;** pomocí [modulu Standard Rules](cdn-standard-rules-engine.md)  | | |**&#x2713;** pomocí [modulu pravidel](./cdn-verizon-premium-rules-engine.md) |
| Nastavení mezipaměti nebo hlaviček  |**&#x2713;** pomocí [modulu Standard Rules](cdn-standard-rules-engine.md) | | |**&#x2713;** používání [modulu pravidel Premium](./cdn-verizon-premium-rules-engine.md) |
| Přesměrování nebo přepsání adresy URL |**&#x2713;** pomocí [modulu Standard Rules](cdn-standard-rules-engine.md)  | | |**&#x2713;** používání [modulu pravidel Premium](./cdn-verizon-premium-rules-engine.md) |
| Pravidla mobilních zařízení  |**&#x2713;** pomocí [modulu Standard Rules](cdn-standard-rules-engine.md) | | |**&#x2713;** používání [modulu pravidel Premium](./cdn-verizon-premium-rules-engine.md) |
| [Ukládání řetězců dotazu do mezipaměti](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Duální sada protokolů IPv4/IPv6 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Podpora HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Zabezpečení** | **Microsoft – standardní** | **Akamai úrovně Standard** | **Verizon úrovně Standard** | **Verizon úrovně Premium** | 
| Podpora HTTPS s koncovými body CDN | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTPS pro vlastní doménu](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;** vyžaduje přímý záznam CNAME, aby bylo možné povolit |**&#x2713;** |**&#x2713;** |
| [Podpora vlastních názvů domén](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Geografické filtrování](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Ověření tokenu](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [Ochrana DDOS](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Používání vlastního certifikátu](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
| Podporované verze TLS | TLS 1,2, TLS 1.0/1.1 – [konfigurovatelný](/rest/api/cdn/cdn/customdomains/enablecustomhttps#usermanagedhttpsparameters) | TLS 1.2 | TLS 1.2 | TLS 1.2 |
||||
| **Analýzy a generování sestav** | **Microsoft – standardní** | **Akamai úrovně Standard** | **Verizon úrovně Standard** | **Verizon úrovně Premium** | 
| [Diagnostické protokoly Azure](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Základní sestavy od Verizonu](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Vlastní sestavy od Verizonu](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Rozšířené sestavy HTTP](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Statistiky v reálném čase](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Výkon hraničních uzlů](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Výstrahy v reálném čase](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Jednoduché používání** | **Microsoft – standardní** | **Akamai úrovně Standard** | **Verizon úrovně Standard** | **Verizon úrovně Premium** | 
| Snadná integrace se službami Azure, jako jsou [Storage](cdn-create-a-storage-account-with-cdn.md), [Web Apps](cdn-add-to-web-app.md) a [Media Services](../media-services/previous/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Správa prostřednictvím [REST API](/rest/api/cdn/), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md)nebo [PowerShellu](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Komprese typů MIME](./cdn-improve-performance.md)  |Pouze výchozí |Konfigurovatelné |Konfigurovatelné  |Konfigurovatelné  |
| Kódování komprese  |GZIP, brotli |GZIP |GZIP, Deflate, bzip2, brotli  |GZIP, Deflate, bzip2, brotli  |

## <a name="migration"></a>Migrace

Informace o migraci profilu **Azure CDN Standard od společnosti Verizon** na profil **Azure CDN Premium od společnosti Verizon** najdete v článku [Migrace profilu Azure CDN z úrovně Standard Verizon na úroveň Premium Verizon](cdn-migrate.md). 

> [!NOTE]
> Existuje cesta pro upgrade ze Standard Verizon na Premium Verizon, ale v současné době neexistuje žádný mechanismus převodu mezi ostatními produkty.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [Azure CDN](cdn-overview.md).
