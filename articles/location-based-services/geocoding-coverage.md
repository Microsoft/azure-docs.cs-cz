---
title: "Služby založené na geografické kódování pokrytí v umístění Azure | Microsoft Docs"
description: "Další informace o určování zeměpisných souřadnic pokrytí v umístění Azure na základě služby"
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: article
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: b2ae0f2cb8eba6ca42b3c82458d1fadf4ea93cdf
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="azure-location-based-services---geocoding-coverage"></a>Umístění Azure na základě služby - pokrytí geografické kódování
Azure umístění na základě služby (od kg) obsahuje geografické kódování podrobné informace o vyhledávání adresy, míst a dalšími subjekty geografické po celém světě. To je důležité pro zákazníky, kteří chtějí vědět, kde můžete a nelze najít různých úrovní věrnosti při vyhledávání pro adresy ve funkci hledání služby očekávat. Při hledání oblasti s nízkou věrnosti pokrytí, můžete očekávat, že výsledky hledání nižší spolehlivosti. Následující tabulka obsahuje informace o pokrytí pro Azure kg vyhledávání určování zeměpisných souřadnic.

| Oblast             | Adresa body<sup>1</sup>|Levné čísla<sup>2</sup>| Ulice úroveň | Úroveň Město | Vás zajímá |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| **Americas**                                            |                 |                |              |            |                    |
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
| Svatý Martin                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Svatý Pierre a Miquelon|                 |                |       ✓      |      ✓     |          ✓         |
| Svatý Vincenc a Grenadiny                    |                 |                |              |      ✓     |          ✓         |
| Sint Maarten                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Jižní Georgie a Jižní Sandwichovy ostrovy        |                 |                |              |      ✓     |          ✓         |
| Surinam                                            |                 |                |              |      ✓     |          ✓         |
| Trinidad a Tobago                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Menší odlehlé ostrovy Spojených států amerických                |                 |                |              |      ✓     |          ✓         |
| Spojené státy americké                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uruguay                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Venezuela                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Panenské ostrovy - britské                            |                 |                |              |      ✓     |          ✓         |
| Panenské ostrovy - Czech Republic                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| |                 |                |              |            |                    |
| **Asie a Tichomoří**                                        |                 |                |              |            |                    |
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
| Ostrovy Pitcairn                                            |                 |                |              |      ✓     |          ✓         |
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
| |                 |                |              |            |                    |
| **Evropa**                                              |                 |                |              |            |                    |
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
| Irsku (republika)                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
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
| |                 |                |              |            |                    |
| **Blízký východ Afrika**                                  |                 |                |              |            |                    |
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
| Džibutsko                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Egypt                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Rovníkové Guinejská, republika                      |                 |                |       ✓      |      ✓     |          ✓         |
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
| Mozambik                                          |                 |                |       ✓      |      ✓     |          ✓         |
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
| Svatý Tomáš a Princův ostrov, demokratická republika       |                 |                |       ✓      |      ✓     |          ✓         |
| Tanzanie                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Togo                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Tunisko                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Uganda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Spojené arabské emiráty                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Banka – západ                                           |                 |                |              |      ✓     |          ✓         |
| Jemen                                               |                 |                |              |      ✓     |          ✓         |
| Zambie                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Zimbabwe                                            |                 |                |       ✓      |      ✓     |          ✓         |


<br>
<br>




|Výrazy|Definice|
|--- |--- |
|**<sup>1</sup> -adresa bodu**|Odvozené od indexu adresa v Online hledání. Toto pole je podporována pouze při vyhledávání a určování zeměpisných souřadnic je podporována.|
|**<sup>2</sup> -obsahovat číslo**|Odvozené z adres interpolace indexu v Online hledání. Toto pole je podporována pouze při vyhledávání a určování zeměpisných souřadnic je podporována.|
|**<sup>3</sup> – přiřazené kódu**|Online hledání konkrétního kódu, není oficiální kód ISO 3166-1.|
