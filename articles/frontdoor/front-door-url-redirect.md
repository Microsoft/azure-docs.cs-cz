---
title: Přední dvířka Azure – přesměrování adresy URL | Microsoft Docs
description: Tento článek vám pomůže porozumět tomu, jak přední dvířka Azure podporuje přesměrování adresy URL pro své trasy, pokud jsou nakonfigurované.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: duau
ms.openlocfilehash: 41cb2343cb86d2ec756bb0a2fb690b7df886024f
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399034"
---
# <a name="url-redirect"></a>Přesměrování adresy URL
K přesměrování provozu můžete použít přední dvířka Azure. Přenosy dat můžete přesměrovat na více úrovní (protokol, název hostitele, cesta, řetězec dotazu) a všechny funkce se dají nakonfigurovat pro jednotlivé mikroslužby, protože přesměrování je založené na cestě. Tato funkce zjednodušuje konfiguraci aplikace, optimalizuje využití prostředků a podporuje nové scénáře přesměrování včetně globálního přesměrování a přesměrování na základě cest.
</br>

![Přesměrování adresy URL front-dveří Azure][1]

## <a name="redirection-types"></a>Typy přesměrování
Typ přesměrování nastaví stavový kód odpovědi pro klienty, aby porozuměl účelu přesměrování. Podporovány jsou následující typy přesměrování:

- **301 (přesunuto trvale)**: označuje, že cílovým prostředkům byl přiřazen nový trvalý identifikátor URI a jakékoli budoucí odkazy na tento prostředek by měly používat jeden z uzavřených identifikátorů URI. Pro přesměrování HTTP na HTTPS použijte stavový kód 301. 
- **302 (nalezeno)**: označuje, že se cílový prostředek nachází dočasně pod jiným identifikátorem URI. Vzhledem k tomu, že přesměrování může být v některých případech změněno, klient by měl pokračovat v používání platného identifikátoru URI žádosti pro budoucí požadavky.
- **307 (dočasné přesměrování)**: označuje, že se cílový prostředek nachází dočasně pod jiným identifikátorem URI a uživatelský agent nesmí měnit metodu požadavku, pokud provede automatické přesměrování na tento identifikátor URI. Vzhledem k tomu, že přesměrování se může v průběhu času měnit, klient by měl pokračovat v používání původního platného identifikátoru URI žádosti pro budoucí požadavky.
- **308 (trvalé přesměrování)**: označuje, že cílovým prostředkům byl přiřazen nový trvalý identifikátor URI a jakékoli budoucí odkazy na tento prostředek by měly používat jeden z uzavřených identifikátorů URI. Klienti s možnostmi úprav odkazů by měli automaticky znovu propojit odkazy na platný identifikátor URI žádosti na jeden nebo více nových odkazů odesílaných serverem, pokud je to možné.

## <a name="redirection-protocol"></a>Protokol přesměrování
Můžete nastavit protokol, který se bude používat pro přesměrování. To umožňuje jeden z nejběžnějších případů použití funkce přesměrování, což je nastavení přesměrování HTTP na HTTPS.

- **Pouze https**: Nastavte protokol jenom na https, pokud chcete přesměrovat provoz z http na https. Přední dveře Azure doporučuje, abyste měli vždy nastavené přesměrování jenom na HTTPS.
- **Pouze http**: přesměruje příchozí požadavek na http. Tuto hodnotu použijte jenom v případě, že chcete zachovat provoz HTTP, který není šifrovaný.
- **Požadavek shody**: Tato možnost zachovává protokol používaný příchozím požadavkem. Požadavek HTTP tedy zůstane HTTP a požadavek HTTPS zůstane přesměrování post protokolu HTTPS.

## <a name="destination-host"></a>Cílový hostitel
V rámci konfigurace směrování přesměrování můžete také změnit název hostitele nebo doménu pro požadavek na přesměrování. Nastavením tohoto pole můžete změnit název hostitele v adrese URL pro přesměrování nebo jinak zachovat název hostitele z příchozího požadavku. Pomocí tohoto pole pak můžete přesměrovat všechny požadavky odeslané na `https://www.contoso.com/*` `https://www.fabrikam.com/*` .

## <a name="destination-path"></a>Cílová cesta
V případě, že chcete jako součást přesměrování nahradit segment cesty adresy URL, můžete toto pole nastavit s novou hodnotou cesty. V opačném případě můžete zvolit zachování hodnoty cesty jako součást přesměrování. Pomocí tohoto pole pak můžete přesměrovat všechny požadavky odeslané na `https://www.contoso.com/\*`  `https://www.contoso.com/redirected-site` .

## <a name="query-string-parameters"></a>Parametry řetězce dotazu
Můžete také nahradit parametry řetězce dotazu v přesměrované adrese URL. Chcete-li nahradit všechny existující řetězce dotazu z adresy URL příchozího požadavku, nastavte toto pole na hodnotu Replace a pak nastavte příslušnou hodnotu. V opačném případě můžete původní sadu řetězců dotazů zachovat nastavením pole na zachovat. Pomocí tohoto pole můžete například přesměrovat veškerý provoz odeslaný na `https://www.contoso.com/foo/bar` do `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` . 

## <a name="destination-fragment"></a>Fragment cíle
Cílový fragment je část adresy URL po #, která se obvykle používá v prohlížečích k pozemkům na určité části stránky. Nastavením tohoto pole můžete přidat fragment na adresu URL pro přesměrování.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png