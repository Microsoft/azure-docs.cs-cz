---
title: Pokrytí geografického kódování | Mapy Microsoft Azure
description: Proces převodu adresy umístění na zeměpisnou šířku a délky souřadnic se označuje jako geografické kódování. V tomto článku se dozvíte o oblastech s pokrytím geografického kódování v Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/31/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 52c6cda800b535109d906fd6e6ae1aa8c709f23f
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132802"
---
# <a name="azure-maps-geocoding-coverage"></a>Azure Maps pokrytí geografické kódování

Když vyhledáte umístění pomocí Azure Maps, vyhledávací služba, například [získat adresu hledání](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress), převezme hledané výrazy a vrátí souřadnice zeměpisné šířky a délky. Tento proces se nazývá geografické kódování. Azure Maps ale nemá stejnou úroveň informací a přesnost pro všechny oblasti a země. V tomto článku zjistíte, jaký druh umístění můžete spolehlivě Hledat v každé oblasti. 

Schopnost geografického kódu v zemi nebo oblasti je závislá na pokrytí dat a přesnosti geografického kódování ve službě geografické kódování. Následující kategorizace se používají k určení úrovně podpory geografického kódování v každé zemi nebo oblasti.
* **Adresované body** – data adres se dají přeložit na souřadnici zeměpisné šířky a délky v rámci adresního pozemku (hranice vlastnosti). Někdy označované jako "Rooftop". Toto je nejvyšší úroveň přesnosti, která je k dispozici pro adresy. 
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

Další informace o Azure Maps geografické kódování naleznete na referenčních stránkách [hledání](https://docs.microsoft.com/rest/api/maps/search) .

Přečtěte si o [oblastech pokrytí pro službu Maps (Maps) Service](traffic-coverage.md). 

