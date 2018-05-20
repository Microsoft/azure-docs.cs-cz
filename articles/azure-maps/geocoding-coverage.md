---
title: Geografické kódování pokrytí ve službě Azure Maps | Microsoft Docs
description: Další informace o určování zeměpisných souřadnic pokrytí v Azure mapy
services: azure-maps
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: a7dc7a1dbf024b6d334ec374f78d5e912db3835c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="azure-maps-geocoding-coverage"></a>Azure pokrytí geografické kódování mapy

Při hledání umístění s Azure mapy, službu vyhledávání trvá termíny a vrátí souřadnice zeměpisné šířky a délky procesu označovaného jako geografické kódování. Mapy však nemá stejnou úroveň informací a přesnost pro všechny oblasti a zemích. Pomocí tohoto článku můžete určit, jaký druh umístění můžete spolehlivě vyhledat v každé oblasti. 

## <a name="americas"></a>Amerika

| Oblast             | Adresa body | Úklidové čísla | Ulice úroveň | Úroveň Město | Vás zajímá |
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
| Svatý Martin                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Svatý Pierre a Miquelon|                 |                |       ✓      |      ✓     |          ✓         |
| Svatý Vincenc a Grenadiny                    |                 |                |              |      ✓     |          ✓         |
| Svatý Martin                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Jižní Georgie a Jižní Sandwichovy ostrovy        |                 |                |              |      ✓     |          ✓         |
| Surinam                                            |                 |                |              |      ✓     |          ✓         |
| Trinidad a Tobago                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Menší odlehlé ostrovy Spojených států amerických                |                 |                |              |      ✓     |          ✓         |
| Spojené státy americké                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uruguay                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Venezuela                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Panenské ostrovy - britské                            |                 |                |              |      ✓     |          ✓         |
| Panenské ostrovy - Czech Republic                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |

## <a name="asia-pacific"></a>Asie a Tichomoří

| Oblast             | Adresa body |Úklidové čísla | Ulice úroveň | Úroveň Město | Vás zajímá |
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
| Papua-Nová Guinea                                    |                 |                |              |      ✓     |          ✓         |
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

| Oblast             | Adresa body |Úklidové čísla | Ulice úroveň | Úroveň Město | Vás zajímá |
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


## <a name="middle-east-and-africa"></a>Střední východ a Afrika

| Oblast             | Adresa body |Úklidové čísla | Ulice úroveň | Úroveň Město | Vás zajímá |
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



## <a name="next-steps"></a>Další postup

Další informace o určování zeměpisných souřadnic mapy Azure najdete v tématu [vyhledávání](https://docs.microsoft.com/rest/api/maps/search) referenční stránky.

Další informace o [pokrytí oblasti pro mapy provozu služby](traffic-coverage.md). 

