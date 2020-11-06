---
title: Konfigurace pravidel směrování žádostí Azure Application Gateway
description: Tento článek popisuje, jak nakonfigurovat pravidla směrování žádostí Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 047ce9b33836e2c23a37b1383942323d7c382485
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397531"
---
# <a name="application-gateway-request-routing-rules"></a>Pravidla směrování žádostí Application Gateway

Když vytvoříte Aplikační bránu pomocí Azure Portal, vytvoříte výchozí pravidlo ( *rule1* ). Toto pravidlo váže výchozí naslouchací proces ( *appGatewayHttpListener* ) s výchozím fondem back-end ( *appGatewayBackendPool* ) a výchozím nastavením back-endu http ( *appGatewayBackendHttpSettings* ). Po vytvoření brány můžete upravit nastavení výchozího pravidla nebo vytvořit nová pravidla.

## <a name="rule-type"></a>Typ pravidla

Když vytvoříte pravidlo, zvolíte mezi [ *základními* a *založenými na cestách*](./application-gateway-components.md#request-routing-rules).

- Pokud chcete před všemi požadavky na přiřazený naslouchací proces (například *blog <i></i> . contoso.com/ \* )* na jeden fond back-end, vyberte základní.
- Pokud chcete směrovat požadavky od konkrétních cest URL ke konkrétním fondům back-endu, vyberte na základě cesty. Vzor cesty je použit pouze pro cestu k adrese URL, nikoli k parametrům dotazu.

### <a name="order-of-processing-rules"></a>Pořadí pravidel zpracování

V případě SKU V1 a V2 se porovnávání vzorů příchozích požadavků zpracovává v pořadí, v jakém jsou cesty uvedeny v mapě cesty URL pravidla založeného na cestě. Pokud požadavek odpovídá vzoru ve dvou nebo více cestách v mapě cesty, cesta uvedená jako první je shodná. A požadavek se přepošle na back-end, který je přidružený k této cestě.

## <a name="associated-listener"></a>Přidružený naslouchací proces

Přidružte naslouchací proces k pravidlu, aby se vyhodnotilo *pravidlo směrování požadavků* , které je přidružené ke naslouchacího procesu, aby se zjistilo, že fond back-end má směrovat požadavek.

## <a name="associated-back-end-pool"></a>Přidružený fond back-end

Přidružte k pravidlu fond back-end, který obsahuje cíle back-endu, které obsluhují požadavky, které naslouchací proces obdrží.

 - Pro základní pravidlo je povolen pouze jeden fond back-end. Všechny požadavky na přidruženém naslouchacího procesu se předají do tohoto fondu back-end.

 - V případě pravidla založeného na cestách přidejte více fondů back-end, které odpovídají každé cestě URL. Požadavky, které odpovídají zadané cestě URL, se předají do odpovídajícího fondu back-end. Přidejte také výchozí fond back-end. Žádosti, které neodpovídají žádné cestě URL v pravidle, se předají do tohoto fondu.

## <a name="associated-back-end-http-setting"></a>Přidružené nastavení HTTP back-endu

Přidejte nastavení back-endu protokolu HTTP pro každé pravidlo. Požadavky jsou směrovány z aplikační brány na cíle back-endu pomocí čísla portu, protokolu a dalších informací, které jsou zadány v tomto nastavení.

Pro základní pravidlo je povolené jenom jedno nastavení back-endu HTTP. Všechny požadavky na přidruženém naslouchacího procesu jsou předávány odpovídajícím cílům back-endu pomocí tohoto nastavení protokolu HTTP.

Pro pravidlo založené na cestách přidejte více nastavení HTTP back-endu, která odpovídají každé cestě URL. Požadavky, které odpovídají cestě URL v tomto nastavení, se předají do odpovídajících cílů back-endu pomocí nastavení protokolu HTTP, které odpovídá každé cestě URL. Přidejte také výchozí nastavení HTTP. Požadavky, které neodpovídají žádné cestě URL v tomto pravidle, se předají do výchozího fondu back-end s použitím výchozího nastavení HTTP.

## <a name="redirection-setting"></a>Nastavení přesměrování

Pokud je přesměrování nakonfigurováno pro základní pravidlo, všechny požadavky na přidruženém naslouchací službě budou přesměrovány do cíle. Toto je *globální* přesměrování. Pokud je přesměrování nakonfigurováno pro pravidlo na základě cesty, budou přesměrovány pouze požadavky v určité oblasti lokality. Příkladem je oblast nákupního košíku, která se označuje *jako \* /Cart/*. Toto je přesměrování *na základě cest* .

Další informace o přesměrování najdete v tématu [Přehled přesměrování Application Gateway](redirect-overview.md).

### <a name="redirection-type"></a>Typ přesměrování

Vyberte typ požadovaného přesměrování: *trvalá (301)* , *dočasná (307)* , *Nalezeno (302* ) nebo *jiný (303)*.

### <a name="redirection-target"></a>Cíl přesměrování

Jako cíl přesměrování vyberte jiný naslouchací proces nebo externí Web.

#### <a name="listener"></a>Naslouchací proces

Jako cíl přesměrování vyberte naslouchací proces pro přesměrování provozu z jednoho naslouchacího procesu do jiného v bráně. Toto nastavení se vyžaduje, když chcete povolit přesměrování od protokolu HTTP do HTTPS. Přesměruje provoz od naslouchacího procesu zdroje, který kontroluje příchozí požadavky HTTP na cílový naslouchací proces, který kontroluje příchozí požadavky HTTPS. Můžete také zvolit, že se má v požadavku, který předává cíli přesměrování, zahrnout řetězec dotazu a cestu z původního požadavku.

![Dialogové okno Application Gateway součásti](./media/configuration-overview/configure-redirection.png)

Další informace o přesměrování mezi HTTP a HTTPS najdete v těchto tématech:
- [Přesměrování HTTP na HTTPS pomocí Azure Portal](redirect-http-to-https-portal.md)
- [Přesměrování HTTP na HTTPS pomocí PowerShellu](redirect-http-to-https-powershell.md)
- [Přesměrování HTTP na HTTPS pomocí rozhraní příkazového řádku Azure](redirect-http-to-https-cli.md)

#### <a name="external-site"></a>Externí web

Vyberte externí web, když chcete přesměrovat provoz na naslouchací proces, který je přidružený k tomuto pravidlu, na externí Web. Můžete zvolit zahrnutí řetězce dotazu z původní žádosti do žádosti, která je předána cíli přesměrování. Cestu k externímu webu, který byl v původní žádosti, nelze přeslat.

Další informace o přesměrování najdete v tématu:
- [Přesměrování provozu na externí web pomocí prostředí PowerShell](redirect-external-site-powershell.md)
- [Přesměrování provozu na externí web pomocí rozhraní příkazového řádku](redirect-external-site-cli.md)

## <a name="rewrite-http-headers-and-url"></a>Přepsání hlaviček HTTP a adres URL

Pomocí pravidel pro přepsání můžete přidat, odebrat nebo aktualizovat žádosti a hlavičky odpovědí HTTP (S) a také cestu URL a parametry řetězce dotazu, protože pakety požadavků a odpovědí se pohybují mezi klienty klienta a back-endu přes Aplikační bránu.

Parametry hlaviček a adres URL lze nastavit na statické hodnoty nebo na jiné hlavičky a proměnné serveru. To pomáhá s důležitými případy použití, jako je například extrakce IP adres klientů, odebrání citlivých informací o back-endu, přidání dalších zabezpečení atd.
Další informace naleznete v tématech:

 - [Přehled přepsaných hlaviček a adres URL protokolu HTTP](rewrite-http-headers-url.md)
 - [Konfigurace přepsání hlaviček HTTP](rewrite-http-headers-portal.md)
 - [Konfigurace přepsání adresy URL](rewrite-url-portal.md)

## <a name="next-steps"></a>Další kroky

- [Informace o nastavení HTTP](configuration-http-settings.md)