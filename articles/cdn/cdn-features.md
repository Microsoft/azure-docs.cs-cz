---
title: Porovnání funkcí produktu Azure Content Delivery Network (CDN) | Microsoft Docs
description: Seznamte se s podporovanými funkcemi v jednotlivých produktech Azure Content Delivery Network (CDN).
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: mdgattuso
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 08/28/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 6826c1340de16891933b290eb29bd4fb8d120974
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127922"
---
# <a name="compare-azure-cdn-product-features"></a>Porovnání funkcí produktu Azure CDN

Azure Content Delivery Network (CDN) zahrnuje čtyři produkty: **Azure CDN Standard od Microsoftu**, **Azure CDN Standard od Akamai**, **Azure CDN Standard z Verizon**a **Azure CDN Premium od Verizon**. Informace o migraci profilu **Azure CDN Standard od společnosti Verizon** na profil **Azure CDN Premium od společnosti Verizon** najdete v článku [Migrace profilu Azure CDN z úrovně Standard Verizon na úroveň Premium Verizon](cdn-migrate.md). Všimněte si, že když existuje cesta pro upgrade ze Standard Verizon na Premium Verizon, neexistuje žádný mechanismus převodu mezi ostatními produkty v tuto chvíli.

Následující tabulka obsahuje porovnání funkcí, které jsou dostupné v jednotlivých produktech.

| **Funkce a optimalizace výkonu** | **Microsoft Standard** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** |
| --- | --- | --- | --- | --- |
| [Akcelerace dynamického webu](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  | Nabízeno přes [službu Azure front-dveří](https://docs.microsoft.com/azure/frontdoor/front-door-overview) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Akcelerace dynamického webu – adaptivní komprese obrázků](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Akcelerace dynamického webu – předběžné načtení objektů](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Obecná optimalizace webového doručování](https://docs.microsoft.com/azure/cdn/cdn-optimization-overview#general-web-delivery)  | **&#x2713;** | **&#x2713;** Vyberte tento typ optimalizace, pokud je průměrná velikost souboru menší než 10 MB.  | **&#x2713;** |  **&#x2713;** |
| [Optimalizace streamování videa](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | prostřednictvím obecného webového doručování | **&#x2713;**  | prostřednictvím obecného webového doručování |  prostřednictvím obecného webového doručování |
| [Optimalizace velkých souborů](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | prostřednictvím obecného webového doručování | **&#x2713;** Vyberte tento typ optimalizace, pokud je průměrná velikost souboru větší než 10 MB.   | prostřednictvím obecného webového doručování |  prostřednictvím obecného webového doručování |
| Změnit typ optimalizace | |**&#x2713;** | | |
| Zdrojový port |Všechny porty TCP |[Povolené počáteční porty](https://docs.microsoft.com/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Všechny porty TCP |Všechny porty TCP |
| [Vyrovnávání zatížení globálního serveru (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Rychlé vyprázdnění](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;** , Vyprázdnit všechny a zástupné znaky nejsou aktuálně podporované Azure CDN z Akamai. |**&#x2713;** |**&#x2713;** |
| [Předběžné načítání prostředku](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Nastavení mezipaměti nebo hlaviček (pomocí [pravidel ukládání do mezipaměti](cdn-caching-rules.md))  |  |**&#x2713;** |**&#x2713;** | |
| Přizpůsobitelný modul pro doručování obsahu založený na pravidlech (pomocí [stroje pravidel](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Nastavení mezipaměti nebo hlaviček (pomocí [stroje pravidel](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Přesměrování nebo přepsání adresy URL (pomocí [stroje pravidel](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Pravidla mobilních zařízení (pomocí [stroje pravidel](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| [Ukládání řetězce dotazu do mezipaměti](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Duální sada protokolů IPv4/IPv6 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Podpora HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Zabezpečení** | **Microsoft Standard** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** | 
| Podpora HTTPS s koncovými body CDN | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTPS pro vlastní doménu](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;** , Vyžaduje přímý záznam CNAME pro povolení |**&#x2713;** |**&#x2713;** |
| [Podpora vlastních názvů domén](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Geografická filtrování](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Ověření tokenu](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [Ochrana proti útoku DDoS](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Používání vlastního certifikátu](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
||||
| **Analýzy a generování sestav** | **Microsoft Standard** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** | 
| [Diagnostické protokoly Azure](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Základní sestavy od Verizonu](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Vlastní sestavy od Verizonu](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Rozšířené sestavy HTTP](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Statistiky v reálném čase](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Výkon hraničních uzlů](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Výstrahy v reálném čase](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Snadné použití** | **Microsoft Standard** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** | 
| Snadná integrace se službami Azure, jako jsou [Storage](cdn-create-a-storage-account-with-cdn.md), [Web Apps](cdn-add-to-web-app.md) a [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Správa prostřednictvím rozhraní [REST API](/rest/api/cdn/), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) nebo [PowerShellu](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Komprese typů MIME](https://docs.microsoft.com/azure/cdn/cdn-improve-performance)  |Pouze výchozí |Konfigurovatelné |Konfigurovatelné  |Konfigurovatelné  |
| Kódování komprese  |gzip, brotli |gzip |gzip, deflate, bzip2, brotili  |gzip, deflate, bzip2, brotili  |






