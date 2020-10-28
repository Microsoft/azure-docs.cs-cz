---
title: Pokrytí geografické kódování ve službě Microsoft Azure Maps Search Service
description: Podívejte se, které oblasti Azure Maps hledání pokrývá. Kategorie geografického kódování zahrnují adresní body, čísla domů, úroveň ulice, úroveň města a body zájmu.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/28/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3cf27319fa84ff5d693e74e90664b7a86f4d934a
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897207"
---
# <a name="azure-maps-geocoding-coverage"></a>Azure Maps pokrytí geografické kódování

Služba Azure Maps [Search](/rest/api/maps/search) podporuje geografické kódování, což znamená, že vaše žádost o rozhraní API může mít hledané výrazy, jako je adresa nebo název místa, a výsledek vrátí jako souřadnice zeměpisné šířky a délky. Například [rozhraní API pro hledání adres](/rest/api/maps/search/getsearchaddress) v Azure Maps obdrží dotazy, které obsahují informace o poloze, a vrátí výsledky jako souřadnice zeměpisné šířky a délky.

[Služba Azure Maps Search](/rest/api/maps/search) však nemá stejnou úroveň informací a přesnost pro všechny oblasti a země. V tomto článku zjistíte, jaký druh umístění můžete spolehlivě Hledat v každé oblasti.

Schopnost geografického kódu v zemi nebo oblasti závisí na rozsahu dat a přesnosti geografického kódování ve službě geografické kódování. Následující kategorizace se používají k určení úrovně podpory geografického kódování v každé zemi nebo oblasti.

* **Adresované body** – data adresy lze přeložit na souřadnice zeměpisné šířky a délky v rámci třídy Address parcela (hranice vlastnosti). Adresní body se často označují jako "Rooftop", což je nejvyšší úroveň přesnosti dostupné pro adresy.
* **Čísla domů** – adresy se interpolují na ulici a zeměpisnou délku.
* Adresa na **úrovni ulice** se přeloží na souřadnici zeměpisné šířky a délky ulice, která obsahuje adresu. Číslo domu nesmí být zpracováno.
* **Úroveň města** – názvy míst měst jsou podporovány.

## <a name="americas"></a>Amerika

| Země/oblast                                       | Adresní body | Čísla domu | Úroveň ulice | Úroveň města | Body zájmu |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Anguilla                                            |                 |                |              |      ✓     |          ✓         |
| Antarktida                                          |                 |                |              |      ✓     |          ✓         |
| Antigua a Barbuda                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Argentina                                           |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Aruba                                               |                 |                |              |      ✓     |          ✓         |
| Bahamy                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Barbados                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Belize                                              |                 |                |              |      ✓     |          ✓         |
| Bermudy                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bolívie                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bonaire, Sint Eustach a Saba                   |                 |                |              |      ✓     |          ✓         |
| Brazílie                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kanada                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kajmanské ostrovy                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Chile                                               |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Kolumbie                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kostarika                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Kuba                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Dominika                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Dominikánská                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Ekvádor                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Salvador                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Falklandské ostrovy                                    |                 |                |              |      ✓     |          ✓         |
| Francouzská Guyana                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Grenada                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Guadeloupe                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Guam                                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Guatemala                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Guyana                                              |                |             |           |      ✓     |                 |
| Haiti                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Honduras                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Jamajka                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Martinik                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Mexiko                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Montserrat                                          |                 |                |              |      ✓     |          ✓         |
| Nikaragua                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Panama                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Paraguay                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Peru                                                |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Portoriko                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Svatý Bartoloměj                                    |                 |                |       ✓      |      ✓     |          ✓         |
| Svatý Kryštof a Nevis                               |                 |                |       ✓      |      ✓     |          ✓         |
| Svatá Lucie                                         |                 |                |              |      ✓     |          ✓         |
| Svatý Martin (Francie)                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Saint-Pierre a Miquelon                           |                 |                |       ✓      |      ✓     |          ✓         |
| Svatý Vincenc a Grenadiny                    |                 |                |              |      ✓     |          ✓         |
| Svatý Martin (Nizozemsko)                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Jižní Georgie a Jižní Sandwichovy ostrovy        |                 |                |              |      ✓     |          ✓         |
| Surinam                                            |                 |                |              |      ✓     |          ✓         |
| Trinidad a Tobago                                 |                 |                |       ✓      |      ✓     |          ✓         |
| USA menší odlehlé ostrovy                |                 |                |              |      ✓     |          ✓         |
| USA Amerika                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uruguay                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Venezuela                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Britské Panenské ostrovy                              |                 |                |              |      ✓     |          ✓         |
| Americké Panenské ostrovy                                 |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |

## <a name="asia-pacific"></a>Asie a Tichomoří

| Země/oblast                                      | Adresní body |Čísla domu | Úroveň ulice | Úroveň města | Body zájmu |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Americká Samoa                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Austrálie                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bangladéš                                          |                 |                |              |      ✓     |          ✓         |
| Bhútán                                              |                 |                |              |      ✓     |          ✓         |
| Britské indickooceánské území                      |                 |                |              |      ✓     |          ✓         |
| Brunej                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kambodža                                            |                 |                |              |      ✓     |          ✓         |
| Čína                                               |                 |                |              |      ✓     |          ✓         |
| Vánoční ostrov                                    |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kokosové ostrovy                             |                 |                |              |      ✓     |          ✓         |
| Komory                                             |                 |                |              |      ✓     |          ✓         |
| Cookovy ostrovy                                        |                 |                |              |      ✓     |          ✓         |
| Fidži                                                |                  |                |              |      ✓     |          ✓        |
| Francouzská Polynésie                                    |                 |                |              |      ✓     |          ✓         |
| Heardův ostrov a MacDonaldovy ostrovy                   |                 |                |              |      ✓     |          ✓         |
| Hongkong – zvláštní administrativní oblast                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Indonésie                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Indie                                               |        ✓        |        ✓       |       ✓      |      ✓     |                   |
| Japonsko                                               |                 |                |              |      ✓     |          ✓         |
| Kiribati                                            |                 |                |              |      ✓     |          ✓         |
| Jižní Korea                                         |                 |                |              |      ✓     |          ✓         |
| Laos                                                |                 |                |              |      ✓     |          ✓         |
| Macao – zvláštní administrativní oblast                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malajsie                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Mikronésie                                          |                 |                |              |      ✓     |          ✓         |
| Mongolsko                                            |                 |                |              |      ✓     |          ✓         |
| Nauru                                               |                 |                |              |      ✓     |          ✓         |
| Nepál                                               |                 |                |              |      ✓     |          ✓         |
| Nová Kaledonie                                       |                 |                |              |      ✓     |          ✓         |
| Nový Zéland                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Niue                                                |                 |                |              |      ✓     |          ✓         |
| Norfolk                                      |                 |                |              |      ✓     |          ✓         |
| Severní Korea                                         |                 |                |              |      ✓     |          ✓         |
| Severní Mariany                            |                 |                |       ✓      |      ✓     |          ✓         |
| Pákistán                                            |                 |                |              |      ✓     |          ✓         |
| Palau                                               |                 |                |              |      ✓     |          ✓         |
| Papua-Nová Guinea                                    |                 |                |              |      ✓     |          ✓         |
| Filipíny                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Pitcairn                                            |                 |                |              |      ✓     |          ✓         |
| Samoa                                               |                 |                |              |      ✓     |          ✓         |
| Senkaku ostrovy                                     |        ✓        |                |              |      ✓     |          ✓         |
| Singapur                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Šalamounovy ostrovy                                     |                 |                |              |      ✓     |          ✓         |
| Jižní Kurils                                     |        ✓        |                |              |      ✓     |          ✓         |
| Srí Lanka                                           |                 |                |              |      ✓     |          ✓         |
| Tchaj-wan                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Thajsko                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Tokelau                                             |                 |                |              |      ✓     |          ✓         |
| Tonga                                               |                 |                |              |      ✓     |          ✓         |
| Ostrovy Turks a Caicos                            |                 |                |              |      ✓     |          ✓         |
| Tuvalu                                              |                 |                |              |      ✓     |          ✓         |
| Vanuatu                                             |                 |                |              |      ✓     |          ✓         |
| Vietnam                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wallis a Futuna                                   |                 |                |              |      ✓     |          ✓         |

## <a name="europe"></a>Evropa

| Země/oblast                                      | Adresní body |Čísla domu | Úroveň ulice | Úroveň města | Body zájmu |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Albánie                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Andorra                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Arménie                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Rakousko                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ázerbájdžán                                          |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Belgie                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bosna a Hercegovina                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bulharsko                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bělorusko                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Chorvatsko                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kypr                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Česká republika                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Dánsko                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Estonsko                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Faerské ostrovy                                       |                 |                |              |      ✓     |          ✓         |
| Finsko                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Francie                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gruzie                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Německo                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gibraltar                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Řecko                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Grónsko                                           |                 |                |              |      ✓     |          ✓         |
| Guernsey                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Maďarsko                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Island                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Irsko                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ostrov Man                                         |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Itálie                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jan Mayen                                           |        ✓        |                |              |      ✓     |          ✓         |
| Jersey                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Kazachstán                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kosovo                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Kyrgyzstán                                          |                 |                |              |      ✓     |          ✓         |
| Lotyšsko                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Lichtenštejnsko                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Litva                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Lucembursko                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Severní Makedonie                                     |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malta                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Moldavsko                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Monako                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Černá Hora                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Nizozemsko                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Norsko                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Polsko                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Portugalsko                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| + Azory a Madeira                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Rumunsko                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Ruská federace                                  |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| San Marino                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Srbsko                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Slovensko                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Slovinsko                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Španělsko                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Svalbard                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Švédsko                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Švýcarsko                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tádžikistán                                          |                 |                |              |      ✓     |          ✓         |
| Turecko                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Turkmenistán                                        |                 |                |              |      ✓     |          ✓         |
| Ukrajina                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Spojené království                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uzbekistán                                          |                 |                |              |      ✓     |          ✓         |
| Vatikán                                        |                 |                |       ✓      |      ✓     |          ✓         |

## <a name="middle-east-and-africa"></a>Střední východ a Afrika

| Země/oblast                                      | Adresní body |Čísla domu | Úroveň ulice | Úroveň města | Body zájmu |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Afghánistán                                         |                 |                |              |      ✓     |          ✓         |
| Alžírsko                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Angola                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bahrajn                                             |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Benin                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Botswana                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Bouvetův ostrov                                       |                 |                |              |      ✓     |          ✓         |
| Burkina Faso                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Burundi                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Kamerun                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Cabo Verde                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Středoafrická republika                            |                 |                |       ✓      |      ✓     |          ✓         |
| Čad                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Kongo                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Côte d’Ivoire (Pobřeží slonoviny)                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Konžská demokratická republika                    |                 |                |       ✓      |      ✓     |          ✓         |
| Džibutsko                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Egypt                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Rovníková Guinea, republika                      |                 |                |       ✓      |      ✓     |          ✓         |
| Eritrea                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Etiopie                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Francouzská jižní území|                        |                |              |      ✓     |          ✓         |
| Gabon                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Gambie                                              |                 |                |              |      ✓     |          ✓         |
| Ghana                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Guinea                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Guinea-Bissau                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Iran                                                |                 |                |              |      ✓     |          ✓         |
| Irák                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Izrael                                              |        ✓        |       ✓        |              |      ✓     |          ✓         |
| Jordánsko                                              |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Keňa                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Kuvajt                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Libanon                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Lesotho                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Libérie                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Libye                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Madagaskar                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Malawi                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Maledivy                                            |                 |                |              |      ✓     |          ✓         |
| Mali                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Marshallovy ostrovy                                    |                 |                |              |      ✓     |          ✓         |
| Mauritánie                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Mauricius                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Mayotte                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Maroko                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Mosambik                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Myanmar                                             |                 |                |              |      ✓     |          ✓         |
| Namibie                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Niger                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Nigérie                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Omán                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Katar                                               |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Réunion                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Rwanda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Svatá Helena                                        |                 |                |              |      ✓     |          ✓         |
| Saúdská Arábie                                        |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Senegal                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Seychely                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Sierra Leone                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Somálsko                                             |                 |                |              |      ✓     |          ✓         |
| Jižní Afrika                                        |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jižní Súdán                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Súdán                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Svazijsko                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Sýrie                                               |                 |                |              |      ✓     |          ✓         |
| Svatý Tomáš a Princův ostrov                               |                 |                |       ✓      |      ✓     |          ✓         |
| Tanzanie                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Togo                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Tunisko                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Uganda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Spojené arabské emiráty                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jemen                                               |                 |                |              |      ✓     |          ✓         |
| Zambie                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Zimbabwe                                            |                 |                |       ✓      |      ✓     |          ✓         |

## <a name="next-steps"></a>Další kroky

Další informace o Azure Maps geografické kódování:
> [!div class="nextstepaction"]
> [Služba Azure Maps Search](/rest/api/maps/search)