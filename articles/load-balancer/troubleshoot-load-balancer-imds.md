---
title: Běžné kódy chyb pro Azure Instance Metadata Service (IMDS)
titleSuffix: Azure Load Balancer
description: Přehled běžných chybových kódů a odpovídajících metod zmírňování pro Azure Instance Metadata Service (IMDS)
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: e932e211996a05b2740613381735a7de3492e5bf
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519180"
---
# <a name="error-codes-common-error-codes-when-using-imds-to-retrieve-load-balancer-information"></a>Kódy chyb: běžné chybové kódy při použití IMDS k získání informací o vyrovnávání zatížení

Tento článek popisuje běžné chyby nasazení a způsob řešení těchto chyb při použití Instance Metadata Service Azure (IMDS).

## <a name="error-codes"></a>Kódy chyb

| Kód chyby | Chybová zpráva | Podrobnosti a zmírnění |
| --- | ---------- | ----------------- |
| 400 | Chybí povinný parametr \<ParameterName> . Opravte prosím požadavek a zkuste to znovu. | Kód chyby indikuje chybějící parametr. </br> Další informace o přidání chybějícího parametru najdete v tématu [jak načíst metadata služby Vyrovnávání zatížení pomocí instance metadata Service Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).
| 400 | Hodnota parametru není povolená nebo není \<ParameterValue> povolená hodnota parametru pro parametr ParameterName. Opravte prosím požadavek a zkuste to znovu. | Kód chyby značí, že formát požadavku není správně nakonfigurován. </br> Další informace najdete v tématu [jak načíst metadata služby Vyrovnávání zatížení pomocí instance metadata Service Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response) , abyste opravili tělo žádosti a vydávali opakování. |
| 400 | Neočekávaná žádost Zkontrolujte prosím parametry dotazu a zkuste to znovu. | Kód chyby značí, že formát požadavku není správně nakonfigurován. </br> Další informace najdete v tématu [jak načíst metadata služby Vyrovnávání zatížení pomocí instance metadata Service Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response) , abyste opravili tělo žádosti a vydávali opakování. |
| 404 | Nenašla se žádná metadata služby Vyrovnávání zatížení. Zkontrolujte prosím, jestli váš virtuální počítač používá nestandardní SKU pro vyrovnávání zatížení, a zkuste to znovu později. | Kód chyby indikuje, že váš virtuální počítač není přidružený k nástroji pro vyrovnávání zatížení, nebo že nástroj pro vyrovnávání zatížení je základní SKU místo Standard. </br> Další informace najdete v tématu [rychlý Start: vytvoření veřejného nástroje pro vyrovnávání zatížení virtuálních počítačů pomocí Azure Portal](quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard) k nasazení standardního nástroje pro vyrovnávání zatížení.|
| 404 | Rozhraní API se nenašlo: cesta = " \<UrlPath> ", metoda = " \<Method> " | Kód chyby indikuje chybnou konfiguraci cesty. </br> Další informace najdete v tématu [jak načíst metadata služby Vyrovnávání zatížení pomocí instance metadata Service Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response) , abyste opravili tělo žádosti a vydávali opakování.|
| 405 | Metoda HTTP není povolena: cesta = " \<UrlPath> ", metoda = " \<Method> " | Kód chyby označuje nepodporovaný příkaz HTTP. </br> Další informace najdete v tématu [Azure instance metadata Service (IMDS)](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#http-verbs) pro podporované operace. |
| 429 | Příliš mnoho žádostí | Kód chyby označuje omezení přenosové rychlosti. </br> Další informace o omezení četnosti najdete v tématu [Azure instance metadata Service (IMDS)](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#rate-limiting).|
| 400 | Text žádosti je větší než MaxBodyLength:... | Kód chyby indikuje požadavek větší než MaxBodyLength. </br> Další informace o délce těla najdete v tématu [jak načíst metadata služby Vyrovnávání zatížení pomocí instance metadata Service Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).|
| 400 | Délka klíče parametru je větší než MaxParameterKeyLength:... | Kód chyby označuje délku klíče parametru větší než MaxParameterKeyLength. </br> Další informace o délce těla najdete v tématu [jak načíst metadata služby Vyrovnávání zatížení pomocí instance metadata Service Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response). |
| 400 | Délka hodnoty parametru je větší než MaxParameterValueLength:... | Kód chyby označuje délku klíče parametru větší než MaxParameterValueLength. </br> Další informace o délce hodnoty najdete v tématu [jak načíst metadata služby Vyrovnávání zatížení pomocí instance metadata Service Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).|
| 400 | Délka hodnoty hlavičky parametru je větší než MaxHeaderValueLength:... | Kód chyby označuje délku hodnoty hlavičky parametru větší než MaxHeaderValueLength. </br> Další informace o délce hodnoty najdete v tématu [jak načíst metadata služby Vyrovnávání zatížení pomocí instance metadata Service Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).|
| 404 | Rozhraní API pro Load Balancer metadat není nyní k dispozici. Zkuste to prosím znovu později. | Kód chyby indikuje, že se mohlo zřídit rozhraní API. Vyzkoušejte si požadavek později. |
| 404 | /metadata/Loadbalancer není aktuálně k dispozici. | Kód chyby značí, že je rozhraní API v průběhu povolení. Vyzkoušejte si požadavek později. |
| 503 | Interní služba není k dispozici. Zkuste to prosím znovu později.  | Kód chyby značí, že rozhraní API není dočasně k dispozici. Vyzkoušejte si požadavek později. |
|  |  |

## <a name="next-steps"></a>Další kroky

Další informace o [Azure instance metadata Service](../virtual-machines/windows/instance-metadata-service.md)

