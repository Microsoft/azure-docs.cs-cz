---
title: Azure Front Door – přesměrování adres URL | Dokumenty společnosti Microsoft
description: Tento článek vám pomůže pochopit, jak Azure Front Door podporuje přesměrování adres URL pro jejich trasy, pokud jsou nakonfigurované.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: sharadag
ms.openlocfilehash: 5e3e44c4aee84fe9e2e21174a1d65fdf26b765a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295479"
---
# <a name="url-redirect"></a>Přesměrování adresy URL
Pomocí Azure Front Door můžete přesměrovat provoz. Můžete přesměrovat provoz na více úrovních (protokol, název hostitele, cesta, řetězec dotazu) a všechny funkce lze nakonfigurovat pro jednotlivé mikroslužby jako přesměrování je cesta založená. To zjednodušuje konfiguraci aplikace, optimalizuje využití prostředků a podporuje nové scénáře přesměrování, včetně globálního přesměrování a přesměrování založeného na cestě.
</br>

![Přesměrování adresy URL předních dveří Azure][1]

## <a name="redirection-types"></a>Typy přesměrování
Typ přesměrování nastaví kód stavu odpovědi pro klienty pochopit účel přesměrování. Podporovány jsou následující typy přesměrování:

- **301 (Trvale přesunuto)**: Označuje, že cílovému prostředku byl přiřazen nový trvalý identifikátor URI a všechny budoucí odkazy na tento prostředek by měly používat jednu z přiložených identifikátorů URI. Použijte stavový kód 301 pro přesměrování HTTP na HTTPS. 
- **302 (Nalezeno)**: Označuje, že cílový prostředek je dočasně umístěn pod jiným identifikátorem URI. Vzhledem k tomu, že přesměrování může být příležitostně změněno, měl by klient nadále používat identifikátor URI efektivní požadavek pro budoucí požadavky.
- **307 (Dočasné přesměrování)**: Označuje, že cílový prostředek je dočasně umístěn pod jiným identifikátorem URI a uživatelský agent nesmí změnit metodu požadavku, pokud provádí automatické přesměrování na tento identifikátor URI. Vzhledem k tomu, že přesměrování může v průběhu času měnit, měl by klient pokračovat v používání původního identifikátoru URI efektivní ho požadavku pro budoucí požadavky.
- **308 (Trvalé přesměrování)**: Označuje, že cílovému prostředku byl přiřazen nový trvalý identifikátor URI a všechny budoucí odkazy na tento prostředek by měly používat jednu z přiložených identifikátorů URI. Klienti s možnostmi úprav odkazů by měli automaticky znovu propojit odkazy na identifikátor URI efektivního požadavku na jeden nebo více nových odkazů odeslaných serverem, pokud je to možné.

## <a name="redirection-protocol"></a>Protokol přesměrování
Můžete nastavit protokol, který bude použit pro přesměrování. To umožňuje jeden z nejběžnějších případů použití funkce přesměrování, to znamená nastavit http na přesměrování HTTPS.

- **Pouze HTTPS**: Nastavte protokol pouze na HTTPS, pokud chcete přesměrovat provoz z PROTOKOLU HTTP na HTTPS. Azure Front Door doporučuje, abyste vždy nastavili přesměrování pouze na https.
- **Pouze http:** Tím přesměruje příchozí požadavek na HTTP. Tuto hodnotu použijte pouze v případě, že chcete zachovat provoz HTTP, který je nešifrovaný.
- **Požadavek na shodu**: Tato možnost zachová protokol používaný příchozím požadavkem. Takže požadavek HTTP zůstává HTTP a požadavek HTTPS zůstává https post přesměrování.

## <a name="destination-host"></a>Cílový hostitel
V rámci konfigurace směrování přesměrování můžete také změnit název hostitele nebo doménu pro požadavek na přesměrování. Toto pole můžete nastavit tak, aby změnilo název hostitele v adrese URL pro přesměrování nebo jinak zachovalo název hostitele z příchozí žádosti. Pomocí tohoto pole můžete tedy přesměrovat všechny `https://www.contoso.com/*` `https://www.fabrikam.com/*`odeslané požadavky na program .

## <a name="destination-path"></a>Cílová cesta
V případech, kdy chcete nahradit segment cesty adresy URL jako součást přesměrování, můžete toto pole nastavit novou hodnotou cesty. V opačném případě můžete zvolit zachování hodnoty cesty jako součást přesměrování. Pomocí tohoto pole můžete tedy přesměrovat všechny `https://www.contoso.com/\*` požadavky `https://www.contoso.com/redirected-site`odeslané společnosti .

## <a name="query-string-parameters"></a>Parametry řetězce dotazu
Můžete také nahradit parametry řetězce dotazu v přesměrované adrese URL. Chcete-li nahradit existující řetězec dotazu z adresy URL příchozího požadavku, nastavte toto pole na hodnotu Nahradit a nastavte příslušnou hodnotu. V opačném případě můžete zachovat původní sadu řetězců dotazu nastavením pole na Zachovat. Například pomocí tohoto pole můžete přesměrovat veškerý `https://www.contoso.com/foo/bar` provoz `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F`odeslaný do aplikace . 

## <a name="destination-fragment"></a>Cílový fragment
Cílový fragment je část adresy URL po '#', obvykle používá prohlížeče přistát na konkrétní sekci na stránce. Toto pole můžete nastavit tak, aby bylo fragment upřeno na adresu URL přesměrování.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png