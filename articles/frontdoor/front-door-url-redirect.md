---
title: Přední dvířka Azure – přesměrování adresy URL | Microsoft Docs
description: Tento článek vám pomůže pochopit, jak přední dvířka Azure podporuje přesměrování adresy URL pro pravidla směrování.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 61077c7900530fd4c5be64054bedd9c5d087fe77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91442048"
---
# <a name="url-redirect"></a>Přesměrování adresy URL
Přední dvířka Azure můžou přesměrovat provoz na každou z těchto úrovní: protokol, název hostitele, cesta, řetězec dotazu. Tyto funkce se dají nakonfigurovat pro jednotlivé mikroslužby, protože přesměrování je založené na cestě. To může zjednodušit konfiguraci aplikace optimalizací využití prostředků a podporovat nové scénáře přesměrování, včetně globálního přesměrování a přesměrování na základě cest.
</br>

:::image type="content" source="./media/front-door-url-redirect/front-door-url-redirect.png" alt-text="Přesměrování adresy URL front-dveří Azure":::

## <a name="redirection-types"></a>Typy přesměrování
Typ přesměrování nastaví stavový kód odpovědi pro klienty, aby porozuměl účelu přesměrování. Podporovány jsou následující typy přesměrování:

- **301 (přesunuto trvale)**: označuje, že cílovým prostředkům byl přiřazen nový trvalý identifikátor URI. Jakékoli budoucí odkazy na tento prostředek budou používat jeden z uzavřených identifikátorů URI. Pro přesměrování HTTP na HTTPS použijte stavový kód 301. 
- **302 (nalezeno)**: označuje, že cílový prostředek je dočasně pod jiným identifikátorem URI. Vzhledem k tomu, že se přesměrování může v některých případech změnit, klient by měl nadále používat platný identifikátor URI žádosti pro budoucí požadavky.
- **307 (dočasné přesměrování)**: označuje, že cílový prostředek je dočasně pod jiným identifikátorem URI. Pokud má uživatelský agent automatické přesměrování na tento identifikátor URI, nesmí změnit metodu žádosti. Vzhledem k tomu, že přesměrování se může v průběhu času měnit, klient by měl pokračovat v používání původního platného identifikátoru URI žádosti pro budoucí požadavky.
- **308 (trvalé přesměrování)**: označuje, že cílový prostředek byl přiřazen k novému TRVALÉmu identifikátoru URI. Jakékoli budoucí odkazy na tento prostředek by měly používat jeden z uzavřených identifikátorů URI.

## <a name="redirection-protocol"></a>Protokol přesměrování
Můžete nastavit protokol, který se bude používat pro přesměrování. Nejběžnějšími případy použití funkce přesměrování je nastavení přesměrování HTTP na HTTPS.

- **Pouze https**: Nastavte protokol jenom na https, pokud chcete přesměrovat provoz z http na https. Přední dveře Azure doporučuje, abyste měli vždy nastavené přesměrování jenom na HTTPS.
- **Pouze http**: přesměruje příchozí požadavek na http. Tuto hodnotu použijte jenom v případě, že chcete zachovat provoz HTTP, který není šifrovaný.
- **Požadavek shody**: Tato možnost zachovává protokol používaný příchozím požadavkem. Požadavek HTTP tedy zůstane HTTP a požadavek HTTPS zůstane přesměrování post protokolu HTTPS.

## <a name="destination-host"></a>Cílový hostitel
V rámci konfigurace směrování přesměrování můžete také změnit název hostitele nebo doménu pro požadavek na přesměrování. Nastavením tohoto pole můžete změnit název hostitele v adrese URL pro přesměrování nebo jinak zachovat název hostitele z příchozího požadavku. Pomocí tohoto pole pak můžete přesměrovat všechny požadavky odeslané na `https://www.contoso.com/*` `https://www.fabrikam.com/*` .

## <a name="destination-path"></a>Cílová cesta
V případě, že chcete jako součást přesměrování nahradit segment cesty adresy URL, můžete toto pole nastavit s novou hodnotou cesty. V opačném případě můžete zvolit zachování hodnoty cesty jako součást přesměrování. Pomocí tohoto pole pak můžete přesměrovat všechny požadavky odeslané na `https://www.contoso.com/\*`  `https://www.contoso.com/redirected-site` .

## <a name="query-string-parameters"></a>Parametry řetězce dotazu
Můžete také nahradit parametry řetězce dotazu v přesměrované adrese URL. Chcete-li nahradit všechny existující řetězce dotazu z adresy URL příchozího požadavku, nastavte toto pole na hodnotu Replace a pak nastavte příslušnou hodnotu. V opačném případě můžete zachovat původní sadu dotazovacích řetězců nastavením pole na možnost zachovat. Pomocí tohoto pole můžete například přesměrovat veškerý provoz odeslaný na `https://www.contoso.com/foo/bar` do `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` . 

## <a name="destination-fragment"></a>Fragment cíle
Cílový fragment je část adresy URL po #, kterou prohlížeč používá k pozemku v konkrétní části webové stránky. Nastavením tohoto pole můžete přidat fragment na adresu URL pro přesměrování.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
