---
title: Geografické filtrování v doméně pro službu Azure front-dveří
description: V tomto článku se seznámíte se zásadami geografického filtrování pro službu Azure Front Door Service.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: fcd7a0fe60639bbb17661a906d15136996b325e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87005442"
---
# <a name="what-is-geo-filtering-on-a-domain-for-azure-front-door-service"></a>Co je geografické filtrování v doméně pro službu Azure front-dveří?

Azure Front Door Service ve výchozím nastavení reaguje na požadavky uživatelů bez ohledu na umístění uživatele, který požadavek provádí. V některých případech však můžete chtít omezit přístup k vašim webovým aplikacím podle země nebo oblasti. Služba WAF (Web Application firewall) ve front-dveřích umožňuje definovat zásadu s použitím pravidel vlastního přístupu pro konkrétní cestu ke koncovému bodu a povolit nebo blokovat přístup ze zadaných zemí nebo oblastí. 

Zásada WAF obvykle zahrnuje sadu vlastních pravidel. Pravidlo se skládá z podmínek shody, akce a priority. V podmínce shody definujete proměnnou shody, operátor a hodnotu shody.  Pro pravidlo geografického filtrování, které odpovídá proměnné, je REMOTE_ADDR, je operátor geograficky shodný, hodnota je dvě písmena v kódu země/oblasti, kterou je důležité. Kombinací podmínky GeoMatch a podmínky shody řetězce REQUEST_URI můžete vytvořit pravidlo geografického filtrování na základě cesty.

Zásady geografického filtrování pro vaše přední dveře můžete nakonfigurovat buď pomocí [Azure PowerShell](waf-front-door-tutorial-geo-filtering.md) , nebo pomocí naší [šablony pro rychlý Start](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering).

## <a name="countryregion-code-reference"></a>Odkaz na kód země/oblasti

|Kód země/oblasti | Krajin? Název oblasti |
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
| CD | Konžská demokratická republika|
| CF | Středoafrická republika|
| CH | Švýcarsko|
| CI | Pobřeží slonoviny|
| CL | Chile|
| CM | Kamerun|
| CN | Čína|
| CO | Kolumbie|
| CR | Kostarika|
| CU | Kuba|
| CV | Cabo Verde|
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
| HK | Hongkong – zvláštní administrativní oblast|
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
| LY | Libye |
| MA | Maroko|
| MD | Moldavská republika|
| MG | Madagaskar|
| MK | Severní Makedonie|
| ML | Mali|
| MM | Myanmar|
| MN | Mongolsko|
| MO | Macao – zvláštní administrativní oblast|
| MQ | Martinik|
| MR | Mauritánie|
| MT | Malta|
| MV | Maledivy|
| MW | Malawi|
| MX | Mexiko|
| MY | Malajsie|
| MZ | Mosambik|
| NA | Namibie|
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
| Směrodatná odchylka | Súdán|
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
| TC | Ostrovy Turks a Caicos|
| TG | Togo|
| TH | Thajsko|
| TN | Tunisko|
| TR | Turecko|
| TT | Trinidad a Tobago|
| TW | Tchaj-wan|
| TZ | Sjednocená tanzanská republika|
| UA | Ukrajina|
| UG | Uganda|
| USA | USA|
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

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [zabezpečení aplikační vrstvy pomocí služby Front Door](../../frontdoor/front-door-application-security.md).
- Přečtěte si, jak [vytvořit Front Door](../../frontdoor/quickstart-create-front-door.md).
