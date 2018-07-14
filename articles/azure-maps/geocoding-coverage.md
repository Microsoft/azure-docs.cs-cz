---
title: Zajištění geokódování ve službě Azure Maps | Dokumentace Microsoftu
description: Další informace o zajištění Geokódování ve službě Azure Maps
author: dsk-2015
ms.author: dkshir
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: d7c1eaa26edef830ca156eef943fcb256d737d77
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989873"
---
# <a name="azure-maps-geocoding-coverage"></a>Zajištění geokódování Azure Maps

Při hledání umístění s Azure Maps, vyhledávací služba přebírá hledané termíny a vrací procesu nazývaného geokódování souřadnice zeměpisné šířky a délky. Mapy, ale nemá stejnou úroveň informací a přesnost pro všechny oblasti a země. V tomto článku použijte k určení, jaký druh umístění můžete spolehlivě vyhledat v jednotlivých oblastech. 

## <a name="americas"></a>Amerika

| Oblast             | Adresa body | Čísla House | Ulice úroveň | Na úrovni města | Body zájmu |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Anguilla                                            |                 |                |              |      ✓     |          ✓         |
| Antarktida                                          |                 |                |              |      ✓     |          ✓         |
| Antigua a Barbuda                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Argentina                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Aruba                                               |                 |                |              |      ✓     |          ✓         |
| Bahamy                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Barbados                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Belize                                              |                 |                |              |      ✓     |          ✓         |
| Bermudy                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bolívie                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bonaire, Svatý Eustach a Saba|                 |                |              |      ✓     |          ✓         |
| Brazílie                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kanada                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kajmanské ostrovy                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Chile                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Clippertonův ostrov                                   |                 |                |              |      ✓     |                    |
| Kolumbie                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Kostarika                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Kuba                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Dominika                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Dominicana                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Ekvádor                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Salvador                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Falklandské ostrovy                                    |                 |                |              |      ✓     |          ✓         |
| Francouzská Guyana                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Grenada                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Guadeloupe|                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Guam                                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Guatemala                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Haiti                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Honduras                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Jamajka                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Martinik                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Mexiko                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Montserrat                                          |                 |                |              |      ✓     |          ✓         |
| Nikaragua                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Panama                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Paraguay                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Peru                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Portoriko                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Svatý Bartoloměj                                    |                 |                |       ✓      |      ✓     |          ✓         |
| Svatý Kryštof a Nevis                               |                 |                |       ✓      |      ✓     |          ✓         |
| Svatá Lucie                                         |                 |                |              |      ✓     |          ✓         |
| Svatý Martin (Francie)                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Svatý Pierre a Miquelon|                 |                |       ✓      |      ✓     |          ✓         |
| Svatý Vincenc a Grenadiny                    |                 |                |              |      ✓     |          ✓         |
| Svatý Martin (Nizozemsko)                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Jižní Georgie a Jižní Sandwichovy ostrovy        |                 |                |              |      ✓     |          ✓         |
| Surinam                                            |                 |                |              |      ✓     |          ✓         |
| Trinidad a Tobago                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Menší odlehlé ostrovy Spojených států amerických                |                 |                |              |      ✓     |          ✓         |
| Spojené státy americké                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uruguay                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Venezuela                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Panenské ostrovy – britské                            |                 |                |              |      ✓     |          ✓         |
| Panenské ostrovy – Spojené státy                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |

## <a name="asia-pacific"></a>Asie a Tichomoří

| Oblast             | Adresa body |Čísla House | Ulice úroveň | Na úrovni města | Body zájmu |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Americká Samoa                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Austrálie                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bangladéš                                          |                 |                |              |      ✓     |          ✓         |
| Bhútán                                              |                 |                |              |      ✓     |          ✓         |
| Britské území v Indickém oceánu                      |                 |                |              |      ✓     |          ✓         |
| Brunej                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kambodža                                            |                 |                |              |      ✓     |          ✓         |
| Čína |                 |                |              |      ✓     |          ✓         |
| Vánoční ostrov                                    |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kokosové ostrovy (Keeling)|                 |                |              |      ✓     |          ✓         |
| Komory|                 |                |              |      ✓     |          ✓         |
| Cookovy ostrovy                                        |                 |                |              |      ✓     |          ✓         |
| Fidži |                 |                |              |      ✓     |          ✓         |
| Francouzská Polynésie                                    |                 |                |              |      ✓     |          ✓         |
| Heardův ostrov a McDonaldovy ostrovy                   |                 |                |              |      ✓     |          ✓         |
| Hongkong                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Indonésie                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Japonsko                                               |                 |                |              |      ✓     |          ✓         |
| Kiribati                                            |                 |                |              |      ✓     |          ✓         |
| Laos                                                |                 |                |              |      ✓     |          ✓         |
| Macao                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malajsie                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Mikronésie |                 |                |              |      ✓     |          ✓         |
| Mongolsko                                            |                 |                |              |      ✓     |          ✓         |
| Nauru                                               |                 |                |              |      ✓     |          ✓         |
| Napal|                 |                |              |      ✓     |          ✓         |
| Nová Kaledonie                                       |                 |                |              |      ✓     |          ✓         |
| Nový Zéland                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Niue                                                |                 |                |              |      ✓     |          ✓         |
| Norfolk (ostrov)                                      |                 |                |              |      ✓     |          ✓         |
| Severní Korea                                         |                 |                |              |      ✓     |          ✓         |
| Severní Mariany                            |                 |                |       ✓      |      ✓     |          ✓         |
| Pákistán                                            |                 |                |              |      ✓     |          ✓         |
| Palau |                 |                |              |      ✓     |          ✓         |
| Papua - Nová Guinea                                    |                 |                |              |      ✓     |          ✓         |
| Paracel ostrovy                                     |                 |                |              |      ✓     |                    |
| Filipíny                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Pitcairn                                            |                 |                |              |      ✓     |          ✓         |
| Samoa                                               |                 |                |              |      ✓     |          ✓         |
| Senkaku ostrovy                                     |        ✓        |                |              |      ✓     |          ✓         |
| Singapur                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Šalamounovy ostrovy                                     |                 |                |              |      ✓     |          ✓         |
| Jižní Korea                                         |                 |                |              |      ✓     |          ✓         |
| Jižní Kurils                                     |        ✓        |                |              |      ✓     |          ✓         |
| Spratly ostrovy                                     |                 |                |              |      ✓     |                    |
| Srí Lanka                                           |                 |                |              |      ✓     |          ✓         |
| Tchaj-wan                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Thajsko                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Tokelau                                             |                 |                |              |      ✓     |          ✓         |
| Tonga                                               |                 |                |              |      ✓     |          ✓         |
| Turks a Caicos                            |                 |                |              |      ✓     |          ✓         |
| Tuvalu                                              |                 |                |              |      ✓     |          ✓         |
| Vanuatu                                             |                 |                |              |      ✓     |          ✓         |
| Vietnam                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wallis a Futuna|                 |                |              |      ✓     |          ✓         |

## <a name="europe"></a>Evropa

| Oblast             | Adresa body |Čísla House | Ulice úroveň | Na úrovni města | Body zájmu |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Albánie                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Andorra                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Arménie                                             |                 |                |              |      ✓     |          ✓         |
| Rakousko                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ázerbájdžán                                          |                 |                |              |      ✓     |          ✓         |
| Belgie                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bosna a Hercegovina                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bulharsko                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Belerus|                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Chorvatsko                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kypr                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Česká republika                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Dánsko                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Estonsko                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Faerské ostrovy                                       |                 |                |              |      ✓     |          ✓         |
| Finsko                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Francie                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gruzie                                             |                 |                |              |      ✓     |          ✓         |
| Německo                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gibralter                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Řecko                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Grónsko                                           |                 |                |              |      ✓     |          ✓         |
| Guernsey                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Maďarsko                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Island                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Irsko (republika)                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ostrov Man                                         |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Itálie                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jan Mayen                                           |        ✓        |                |              |      ✓     |          ✓         |
| Jersey                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Kazachstán                                          |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kosovo                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Kyrgyzstán                                          |                 |                |              |      ✓     |          ✓         |
| Lotyšsko                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Lichtenštejnsko                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Litva                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Lucembursko                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Makedonie – bývalá rep. Jugoslávie                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Malta                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Moldavsko                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Monako                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Černá Hora                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Nizozemsko                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Norsko                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Polsko                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Portugalsko                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Rumunsko                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Ruská federace                                  |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| San Marino                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Srbsko                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Slovensko                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Slovinsko                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Španělsko                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Svalbard                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Švédsko                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Švýcarsko                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tádžikistán                                          |                 |                |              |      ✓     |          ✓         |
| Turecko                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Turkmenistán                                        |                 |                |              |      ✓     |          ✓         |
| Ukrajina                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Spojené království                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uzbekistán                                          |                 |                |              |      ✓     |          ✓         |
| Vatikán                                        |                 |                |       ✓      |      ✓     |          ✓         |


## <a name="middle-east-and-africa"></a>Střední východ a Afrika

| Oblast             | Adresa body |Čísla House | Ulice úroveň | Na úrovni města | Body zájmu |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Afghánistán                                         |                 |                |              |      ✓     |          ✓         |
| Alžírsko                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Angola                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bahrajn                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Benin                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Botswana                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Bouvet                                       |                 |                |              |      ✓     |          ✓         |
| Burkina Faso                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Burundi                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Kamerun                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Cabo Verde|                 |                |       ✓      |      ✓     |          ✓         |
| Středoafrická republika                            |                 |                |       ✓      |      ✓     |          ✓         |
| Čad                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Kongo                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Pobřeží slonoviny                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Demokratická republika Kongo                        |                 |                |       ✓      |      ✓     |          ✓         |
| Džibuti                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Egypt                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Rovníková Guinea, republika                      |                 |                |       ✓      |      ✓     |          ✓         |
| Eritrea                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Etiopie                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Francouzská jižní území|                 |                |              |      ✓     |          ✓         |
| Gabon                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Gambie                                              |                 |                |              |      ✓     |          ✓         |
| Ghana                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Guinea                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Guinea-Bissau                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Írán                                                |                 |                |              |      ✓     |          ✓         |
| Irák                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Izrael                                              |                 |                |              |      ✓     |          ✓         |
| Jordánsko                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Keňa                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Kuvajt                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Libanon                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Lesotho                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Libérie                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Libye|                 |                |       ✓      |      ✓     |          ✓         |
| Madagaskar                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Malawi                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Maledivy |                 |                |              |      ✓     |          ✓         |
| Mali                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Marshallovy ostrovy |                 |                |              |      ✓     |          ✓         |
| Mauritánie                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Mauricius                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Mayotte                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Maroko                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Mosambik                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Myanmar                                             |                 |                |              |      ✓     |          ✓         |
| Namibie                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Niger                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Nigérie                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Omán                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Katar                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Réunion|                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Rwanda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Svatá Helena                                        |                 |                |              |      ✓     |          ✓         |
| Saúdská Arábie                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Senegal                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Seychely                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Sierra Leone                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Somálsko                                             |                 |                |              |      ✓     |          ✓         |
| Jihoafrická republika                                        |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jižní Súdán                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Súdán                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Svazijsko                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Sýrie                                               |                 |                |              |      ✓     |          ✓         |
| Svatý Tomáš a Princův ostrov, Konžská demokratická republika       |                 |                |       ✓      |      ✓     |          ✓         |
| Tanzanie                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Togo                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Tunisko                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Uganda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Spojené arabské emiráty                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Západní břeh                                           |                 |                |              |      ✓     |          ✓         |
| Jemen                                               |                 |                |              |      ✓     |          ✓         |
| Zambie                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Zimbabwe                                            |                 |                |       ✓      |      ✓     |          ✓         |



## <a name="next-steps"></a>Další postup

Další informace o Azure Maps geokódování, najdete v článku [hledání](https://docs.microsoft.com/rest/api/maps/search) odkazují na stránky.

Další informace o [pokrytí oblastí pro mapy provozu služby](traffic-coverage.md). 

