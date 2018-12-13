---
title: Geografické filtrování na doméně pro Azure Front Door Service | Microsoft Docs
description: V tomto článku se seznámíte se zásadami geografického filtrování pro službu Azure Front Door Service.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/09/2018
ms.author: sharadag
ms.openlocfilehash: a2ba0fb34dd34129a134aa2639d06816f3523408
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52865501"
---
# <a name="geo-filtering-geographic-based-access-control-to-azure-front-door-service-frontends"></a>Geografické filtrování: Řízení přístupu k front-endům služby Azure Front Door Service na základě geografického umístění

Azure Front Door Service ve výchozím nastavení reaguje na požadavky uživatelů bez ohledu na umístění uživatele, který požadavek provádí. V některých případech však můžete chtít omezit přístup k vašim webovým aplikacím podle země. Zabezpečení aplikační vrstvy ve službě Azure Front Door umožňuje pro konkrétní cestu na koncovém bodu definovat zásadu s použitím vlastních pravidel ochrany, která povolí nebo zablokuje přístup ze zadaných zemí. 

Zásada zabezpečení aplikace obvykle zahrnuje sadu vlastních pravidel. Pravidlo se skládá z podmínek shody, akce a priority. V podmínce shody definujete proměnnou shody, operátor a hodnotu shody.  V případě pravidla geografického filtrování je proměnná shody REMOTE_ADDR, operátor je GeoMatch a hodnota je dvoupísmenný kód požadované země. Kombinací podmínky GeoMatch a podmínky shody řetězce REQUEST_URI můžete vytvořit pravidlo geografického filtrování na základě cesty.

Zásadu geografického filtrování pro službu Front Door můžete nakonfigurovat pomocí [Azure PowerShellu](front-door-tutorial-geo-filtering.md) nebo naší [šablony pro rychlý start](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering).

## <a name="country-code-reference"></a>Referenční informace ke kódům zemí

|Kód země | Jméno země |
| ----- | ----- |
| AD | Andorra |
| AE | Spojené arabské emiráty|
| AF | Afghánistán|
| AG | Antigua a Barbuda|
| AL | Albánie|
| AM | Arménie|
| AO | Angola|
| AR | Argentina|
| AS | Americká Samoa|
| AT | Rakousko|
| AU | Austrálie|
| AZ | Ázerbájdžán|
| BA | Bosna a Hercegovina|
| BB | Barbados|
| BD | Bangladéš|
| BE | Belgie|
| BF | Burkina Faso|
| BG | Bulharsko|
| BH | Bahrajn|
| BI | Burundi|
| BJ | Benin|
| BL | Svatý Bartoloměj|
| BN | Sultanát Brunej|
| BO | Bolívie|
| BR | Brazílie|
| BS | Bahamy|
| BT | Bhútán|
| BW | Botswana|
| BY | Bělorusko|
| BZ | Belize|
| CA | Kanada|
| CD | Demokratická republika Kongo|
| CF | Středoafrická republika|
| CH | Švýcarsko|
| CI | Pobřeží slonoviny|
| CL | Chile|
| CM | Kamerun|
| CN | Čína|
| CO | Kolumbie|
| CR | Kostarika|
| CU | Kuba|
| CV | Kapverdy|
| CY | Kypr|
| CZ | Česká republika|
| DE | Německo|
| DK | Dánsko|
| DO | Dominikánská republika|
| DZ | Alžírsko|
| EC | Ekvádor|
| EE | Estonsko|
| EG | Egypt|
| ES | Španělsko|
| ET | Etiopie|
| FI | Finsko|
| FJ | Fidži|
| FM | Federativní státy Mikronésie|
| FR | Francie|
| GB | Spojené království|
| GE | Gruzie|
| GF | Francouzská Guyana|
| GH | Ghana|
| GN | Guinea|
| GP | Guadeloupe|
| GR | Řecko|
| GT | Guatemala|
| GY | Guyana|
| HK | Hongkong|
| HN | Honduras|
| HR | Chorvatsko|
| HT | Haiti|
| HU | Maďarsko|
| ID | Indonésie|
| IE | Irsko|
| IL | Izrael|
| IN | Indie|
| IQ | Irák|
| IR | Íránská islámská republika|
| IS | Island|
| IT | Itálie|
| JM | Jamajka|
| JO | Jordánsko|
| JP | Japonsko|
| KE | Keňa|
| KG | Kyrgyzstán|
| KH | Kambodža|
| KI | Kiribati|
| KN | Svatý Kryštof a Nevis|
| KP | Korejská lidově demokratická republika|
| KR | Korejská republika|
| KW | Kuvajt|
| KY | Kajmanské ostrovy|
| KZ | Kazachstán|
| LA | Laoská lidově demokratická republika|
| LB | Libanon|
| LI | Lichtenštejnsko|
| LK | Srí Lanka|
| LR | Libérie|
| LS | Lesotho|
| LT | Litva|
| LU | Lucembursko|
| LV | Lotyšsko|
| LY | Libyjská arabská džamahíríja|
| MA | Maroko|
| MD | Moldavská republika|
| MG | Madagaskar|
| MK | Makedonie|
| ML | Mali|
| MM | Myanmar|
| MN | Mongolsko|
| MO | Macao|
| MQ | Martinik|
| MR | Mauritánie|
| MT | Malta|
| MV | Maledivy|
| MW | Malawi|
| MX | Mexiko|
| MY | Malajsie|
| MZ | Mosambik|
| Není k dispozici | Namibie|
| NE | Niger|
| NG | Nigérie|
| NI | Nikaragua|
| NL | Nizozemsko|
| NO | Norsko|
| NP | Nepál|
| NR | Nauru|
| NZ | Nový Zéland|
| OM | Omán|
| PA | Panama|
| PE | Peru|
| PH | Filipíny|
| PK | Pákistán|
| PL | Polsko|
| PR | Portoriko|
| PT | Portugalsko|
| PW | Palau|
| PY | Paraguay|
| QA | Katar|
| RE | Réunion|
| RO | Rumunsko|
| RS | Srbsko|
| RU | Ruská federace|
| RW | Rwanda|
| SA | Saúdská Arábie|
| SD | Súdán|
| SE | Švédsko|
| SG | Singapur|
| SI | Slovinsko|
| SK | Slovensko|
| SN | Senegal|
| SO | Somálsko|
| SR | Surinam|
| SS | Jižní Súdán|
| SV | Salvador|
| SY | Syrská arabská republika|
| SZ | Svazijsko|
| TC | Turks a Caicos|
| TG | Togo|
| TH | Thajsko|
| TN | Tunisko|
| TR | Turecko|
| TT | Trinidad a Tobago|
| TW | Tchaj-wan|
| TZ | Sjednocená tanzanská republika|
| UA | Ukrajina|
| UG | Uganda|
| USA | Spojené státy|
| UY | Uruguay|
| UZ | Uzbekistán|
| VC | Svatý Vincenc a Grenadiny|
| VE | Venezuela|
| VG | Britské Panenské ostrovy|
| VI | Americké Panenské ostrovy|
| VN | Vietnam|
| ZA | Jižní Afrika|
| ZM | Zambie|
| ZW | Zimbabwe|

## <a name="next-steps"></a>Další postup

- Přečtěte si o [zabezpečení aplikační vrstvy pomocí služby Front Door](front-door-application-security.md).
- Přečtěte si, jak [vytvořit službu Front Door](quickstart-create-front-door.md).
