---
title: Porovnání funkcí produktu Azure Content Delivery Network (CDN) | Microsoft Docs
description: Seznamte se s podporovanými funkcemi v jednotlivých produktech Azure Content Delivery Network (CDN).
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/01/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 55bcfb3a7afdf8db0cf9aaea4674685277ce1b24
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55696213"
---
# <a name="compare-azure-cdn-product-features"></a>Porovnání funkcí produktu Azure CDN

Azure Content Delivery Network (CDN) zahrnuje čtyři produkty: **Azure CDN Standard od společnosti Microsoft**, **Azure CDN Standard od Akamai**, **Azure CDN Standard od Verizonu**, a **Azure CDN Premium od Verizonu**. 

Informace o migraci profilu **Azure CDN Standard od společnosti Verizon** na profil **Azure CDN Premium od společnosti Verizon** najdete v článku [Migrace profilu Azure CDN z úrovně Standard Verizon na úroveň Premium Verizon](cdn-migrate.md).

Následující tabulka obsahuje porovnání funkcí, které jsou dostupné v jednotlivých produktech.

| **Funkce a optimalizace výkonu** | **Microsoft Standard** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** |
| --- | --- | --- | --- | --- |
| [Akcelerace dynamického webu](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  |  | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Akcelerace dynamického webu – adaptivní komprese obrázků](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Akcelerace dynamického webu – předběžné načtení objektů](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  |  | **&#x2713;**  |  |  |
| [Optimalizace streamování videa](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | \* | **&#x2713;**  | \* |  \* |
| [Optimalizace velkých souborů](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | \* | **&#x2713;**  | \* |  \* |
| [Vyrovnávání zatížení globálního serveru (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Rychlé vyprázdnění](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Předběžné načítání prostředku](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Nastavení mezipaměti nebo hlaviček (pomocí [pravidel ukládání do mezipaměti](cdn-caching-rules.md))  |  |**&#x2713;** |**&#x2713;** | |
| Nastavení mezipaměti nebo hlaviček (pomocí [stroje pravidel](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| [Ukládání řetězce dotazu do mezipaměti](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Duální sada protokolů IPv4/IPv6 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Podpora HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Zabezpečení** | **Microsoft Standard** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** | 
| Podpora HTTPS s koncovými body CDN | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTPS pro vlastní doménu](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Podpora vlastních názvů domén](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Geografická filtrování](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Ověření tokenu](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [Ochrana proti útoku DDoS](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Používání vlastního certifikátu](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) |**&#x2713;** |  |  |  |
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
| Správa prostřednictvím rozhraní [REST API](https://msdn.microsoft.com/library/mt634456.aspx), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) nebo [PowerShellu](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Přizpůsobitelný modul doručování obsahu založený na pravidlech](cdn-rules-engine.md)  |  | | |**&#x2713;** |
| Přesměrování nebo přepsání adresy URL (pomocí [stroje pravidel](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Pravidla mobilních zařízení (pomocí [stroje pravidel](cdn-rules-engine.md))  |  | | |**&#x2713;** |

\* Microsoft a Verizon podporují přímé doručování velkých souborů a médií prostřednictvím optimalizace obecného doručování webu.



