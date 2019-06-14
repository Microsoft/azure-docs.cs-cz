---
title: Zajištění pokrytí ve službě Azure Maps | Dokumentace Microsoftu
description: Další informace o pokrytí vykreslovací ve službě Azure Maps
author: jingjing-z
ms.author: jinzh
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 7feea92d48a826000fd8aa878b95658b79d6d908
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64869995"
---
# <a name="azure-maps-render-coverage"></a>Zajištění vykreslování Azure Maps

K vytvoření mapy používá Azure Maps rastrové dlaždice a dlaždic vektorových. Nejnižší rozlišením celý svět vejde na jednu dlaždici. Nejvyšší rozlišením představuje jednu dlaždici 38 Čtvereček měřiče. Jak Přiblížit na mapě proto vidíte stále další podrobnosti o kontinentech, oblasti, města a jednotlivé ulice. Další informace najdete v tématu [úrovně přiblížení a mřížka dlaždic](zoom-levels-and-tile-grid.md).

Mapy, ale nemá stejnou úroveň informací a přesnost pro všechny oblasti. Následující tabulka obsahuje informace o jaké úroveň vykreslené podrobností můžete očekávat od každé oblasti.

## <a name="legend"></a>Legenda

| Symbol | Význam |
|--------|---------|
| ✓ | Oblast je reprezentována pomocí podrobné údaje.   |
| Ø | Oblast je reprezentována pomocí jednodušší. |


## <a name="africa"></a>Afrika 


| Země/oblast | Dlaždice rastrové Unified | Sjednocené dlaždic vektorových |
| ------ | :------------------: | :------------------: |
| Alžírsko                          | ✓ | ✓ |
| Angola                           | ✓ | ✓ |
| Benin                            | ✓ | ✓ |
| Botswana                         | ✓ | ✓ |
| Burkina Faso                     | ✓ | ✓ |
| Burundi                          | ✓ | ✓ |
| Cabo Verde                       | ✓ | ✓ |
| Kamerun                         | ✓ | ✓ |
| Středoafrická republika         | ✓ | Ø |
| Čad                             | ✓ | Ø |
| Komory                          | ✓ | Ø |
| Demokratická republika Kongo | ✓ | ✓ |
| Pobřeží slonoviny                    | ✓ | Ø |
| Džibuti                         | ✓ | Ø |
| Egypt                            | ✓ | ✓ |
| Rovníková Guinea                | ✓ | Ø |
| Eritrea                          | ✓ | Ø |
| Etiopie                         | ✓ | Ø |
| Gabon                            | ✓ | ✓ |
| Gambie                           | ✓ | Ø |
| Ghana                            | ✓ | ✓ |
| Guinea                           | ✓ | Ø |
| Guinea-Bissau                    | ✓ | Ø |
| Keňa                            | ✓ | ✓ |
| Lesotho                          | ✓ | ✓ |
| Libérie                          | ✓ | Ø |
| Libye                            | ✓ | Ø |
| Madagaskar                       | ✓ | Ø |
| Malawi                           | ✓ | ✓ |
| Mali                             | ✓ | ✓ |
| Mauritánie                       | ✓ | ✓ |
| Mauricius                        | ✓ | ✓ |
| Mayotte                          | ✓ | ✓ |
| Maroko                          | ✓ | ✓ |
| Mosambik                       | ✓ | ✓ |
| Namibie                          | ✓ | ✓ |
| Niger                            | ✓ | ✓ |
| Nigérie                          | ✓ | ✓ |
| Réunion                          | ✓ | ✓ |
| Rwanda                           | ✓ | ✓ |
| Svatá Helena, Ascensión a Tristan da Cunha | ✓ | Ø |
| Svatý Tomáš a Princův ostrov            | ✓ | Ø |
| Senegal                          | ✓ | ✓ |
| Sierra Leone                     | ✓ | ✓ |
| Somálsko                          | ✓ | ✓ |
| Jižní Afrika                     | ✓ | ✓ |
| Jižní Súdán                      | ✓ | ✓ |
| Súdán                            | ✓ | ✓ |
| Svazijsko                        | ✓ | ✓ |
| Sjednocená Tanzanská republika      | ✓ | ✓ |
| Togo                             | ✓ | ✓ |
| Tunisko                          | ✓ | ✓ |
| Uganda                           | ✓ | ✓ |
| Zambie                           | ✓ | ✓ |
| Zimbabwe                         | ✓ | ✓ |

## <a name="americas"></a>Amerika

| Země/oblast | Dlaždice rastrové Unified | Sjednocené dlaždic vektorových |
| ------ | :------------------: | :------------------: |
| Anguilla                  | ✓ | ✓ |
| Antigua a Barbuda       | ✓ | ✓ |
| Argentina                 | ✓ | ✓ |
| Aruba                     | ✓ | ✓ |
| Bahamy                   | ✓ | ✓ |
| Barbados                  | ✓ | ✓ |
| Belize                    | ✓ | ✓ |
| Bermudy                   | ✓ | ✓ |
| Mnohonárodní stát Bolívie | ✓ | ✓ |
| Bonaire, Svatý Eustach a Saba | ✓ | ✓ |
| Brazílie                    | ✓ | ✓ |
| Kanada                    | ✓ | ✓ |
| Kajmanské ostrovy            | ✓ | ✓ |
| Chile                     | ✓ | ✓ |
| Kolumbie                  | ✓ | ✓ |
| Kostarika                | ✓ | ✓ |
| Kuba                      | ✓ | ✓ |
| Curaçao                   | ✓ | ✓ |
| Dominika                  | ✓ | ✓ |
| Dominikánská republika        | ✓ | ✓ |
| Ekvádor                   | ✓ | ✓ |
| Falklandské ostrovy (Malvíny) | ✓ | ✓ |
| Francouzská Guyana             | ✓ | ✓ |
| Grónsko                 | ✓ | Ø |
| Grenada                   | ✓ | ✓ |
| Guadeloupe                | ✓ | ✓ |
| Guatemala                 | ✓ | ✓ |
| Guyana                    | ✓ | ✓ |
| Haiti                     | ✓ | ✓ |
| Honduras                  | ✓ | ✓ |
| Jamajka                   | ✓ | ✓ |
| Martinik                | ✓ | ✓ |
| Mexiko                    | ✓ | ✓ |
| Montserrat                | ✓ | ✓ |
| Nikaragua                 | ✓ | ✓ |
| Severní Mariany  | ✓ | ✓ |
| Panama                    | ✓ | ✓ | 
| Paraguay                  | ✓ | ✓ |
| Peru                      | ✓ | ✓ |
| Portoriko               | ✓ | ✓ |
| Quebec (Kanada)           | ✓ | ✓ |
| Svatý Bartoloměj          | ✓ | ✓ |
| Svatý Kryštof a Nevis     | ✓ | ✓ |
| Svatá Lucie               | ✓ | ✓ |
| Svatý Martin (francouzština)     | ✓ | ✓ |
| Saint Pierre a Miquelon | ✓ | ✓ |
| Svatý Vincenc a Grenadiny | ✓ | ✓ |
| Sint Maarten (holandština)      | ✓ | ✓ |
| Jižní Georgie a Jižní Sandwichovy ostrovy | ✓ | ✓ |
| Surinam                  | ✓ | ✓ |
| Trinidad a Tobago       | ✓ | ✓ |
| Turks a Caicos  | ✓ | ✓ |
| Spojené státy             | ✓ | ✓ |
| Uruguay                   | ✓ | ✓ |
| Venezuela                 | ✓ | ✓ |
| Britské Panenské ostrovy   | ✓ | ✓ |
| Americké Panenské ostrovy      | ✓ | ✓ |

## <a name="asia"></a>Asie 

| Země/oblast | Dlaždice rastrové Unified | Sjednocené dlaždic vektorových |
| ------ | :------------------: | :------------------: |
| Afghánistán               |   | Ø |
| Bahrajn                   | ✓ | ✓ |
| Bangladéš                |   | Ø |
| Bhútán                    |   | Ø |
| Britské území v Indickém oceánu |   | Ø |
| Brunei                    | ✓ | ✓ |
| Kambodža                  |   | Ø |
| Čína                     |   | Ø |
| Kokosové (Keelingovy) ostrovy   |   | Ø |
| Korejská lidově demokratická republika |   | Ø |
| Dokdo a Takeshima       |   | Ø |
| Hongkong – zvláštní administrativní oblast             | ✓ | ✓ |
| Indie                     | Ø | ✓ | 
| Indonésie                 | ✓ | ✓ |
| Írán                      |   | Ø |
| Irák                      | ✓ | ✓ |
| Izrael                    |   | ✓ |
| Japonsko                     |   | Ø |
| Jordánsko                    | ✓ | ✓ |
| Kazachstán                |   | ✓ |
| Kuvajt                    | ✓ | ✓ |
| Kyrgyzstán                |   | Ø |
| Laoská lidově demokratická republika |   | Ø |
| Libanon                   | ✓ | ✓ |
| Macao – zvláštní administrativní oblast                 | ✓ | ✓ |
| Malajsie                  | ✓ | ✓ |
| Maledivy                  |   | Ø |
| Mongolsko                  |   | Ø |
| Myanmar                   |   | Ø |
| Nepál                     |   | Ø |
| Omán                      | ✓ | ✓ |
| Pákistán                  |   | Ø |
| Filipíny               | ✓ | ✓ |
| Katar                     | ✓ | ✓ |
| Korejská republika         | ✓ | Ø |
| Saúdská Arábie              | ✓ | ✓ |
| Senkaku ostrovy           |   | ✓ |
| Singapur                 | ✓ | ✓|
| Srí Lanka                 |   | Ø |
| Syrská arabská republika      |   | Ø |
| Tchaj-wan                    | ✓ | ✓ |
| Tádžikistán                |   | Ø |
| Thajsko                  | ✓ | ✓ |
| Timor-Leste               |   | Ø |
| Turkmenistán              |   | Ø |
| Spojené arabské emiráty      | ✓ | ✓ |
| Menší odlehlé ostrovy Spojených států amerických |   | Ø |
| Uzbekistán                |   | Ø |
| Vietnam                   | ✓ | ✓ |
| Jemen                     | ✓ | ✓ |

## <a name="oceania"></a>Oceánie

| Země/oblast | Dlaždice rastrové Unified | Sjednocené dlaždic vektorových |
| ------ | :------------------: | :------------------: |
| Americká Samoa            |   | ✓ |
| Austrálie                 | ✓ | ✓ |
| Cookovy ostrovy              |   | Ø |
| Fidži                      |   | Ø |
| Francouzská Polynésie          |   | Ø |
| Guam                      | ✓ | ✓ |
| Kiribati                  |   | Ø |
| Marshallovy ostrovy          |   | Ø |
| Mikronésie                |   | Ø |
| Nauru                     |   | Ø |
| Nová Kaledonie             |   | Ø |
| Nový Zéland               | ✓ | ✓ |
| Niue                      |   | Ø |
| Ostrova Norfolk            |   | Ø |
| Palau                     |   | Ø |
| Papua – Nová Guinea          |   | Ø |
| Pitcairnovy                  |   | Ø |
| Samoa                     |   | Ø |
| Šalamounovy ostrovy           |   | Ø|
| Tokelau                   |   | Ø |
| Tonga                     |   | Ø |
| Tuvalu                    |   | Ø |
| Vanuatu                   |   | Ø |
| Wallis a Futuna         |   | Ø |


## <a name="europe"></a>Evropa

| Země/oblast | Dlaždice rastrové Unified | Sjednocené dlaždic vektorových |
| ------ | :------------------: | :------------------: |
| Albánie                   | ✓ | ✓ |
| Andorra                   | ✓ | ✓ |
| Arménie                   | ✓ | Ø |
| Rakousko                   | ✓ | ✓ |
| Ázerbájdžán                | ✓ | Ø |
| Bělorusko                   | Ø | ✓ |
| Belgie                   | ✓ | ✓ |
| Bosna a Hercegovina        | ✓ | ✓ |
| Bulharsko                  | ✓ | ✓ |
| Chorvatsko                   | ✓ | ✓ |
| Kypr                    | ✓ | ✓ |
| Česká republika            | ✓ | ✓ |
| Dánsko                   | ✓ | ✓ |
| Estonsko                   | ✓ | ✓ |
| Faerské ostrovy             | ✓ | Ø |
| Finsko                   | ✓ | ✓ |
| Francie                    | ✓ | ✓ |
| Gruzie                   | ✓ | Ø |
| Německo                   | ✓ | ✓ |
| Gibraltar                 | ✓ | ✓ |
| Řecko                    | ✓ | ✓ |
| Guernsey                  | ✓ | ✓ |
| Maďarsko                   | ✓ | ✓ |
| Island                   | ✓ | ✓ |
| Irsko                   | ✓ | ✓ |
| Ostrov Man               | ✓ | ✓ |
| Itálie                     | ✓ | ✓ |
| Jan Mayen                 | ✓ | ✓ |
| Jersey                    | ✓ | ✓ |
| Lotyšsko                    | ✓ | ✓ |
| Lichtenštejnsko             | ✓ | ✓ |
| Litva                 | ✓ | ✓ |
| Lucembursko                | ✓ | ✓ |
| Makedonie – sever           | ✓ | ✓ |
| Malta                     | ✓ | ✓ |
| Moldavsko                   | ✓ | ✓ |
| Monako                    | ✓ | ✓ |
| Černá Hora                | ✓ | ✓ |
| Nizozemsko               | ✓ | ✓ |
| Norsko                    | ✓ | ✓ |
| Polsko                    | ✓ | ✓ |
| Portugalsko                  | ✓ | ✓ |
| Rumunsko                   | ✓ | ✓ |
| Ruská federace        | ✓ | ✓ |
| San Marino                | ✓ | ✓ |
| Srbsko                    | ✓ | ✓ |
| Slovensko                  | ✓ | ✓ |
| Slovinsko                  | ✓ | ✓ |
| Jižní Kurils           | ✓ | ✓ |
| Španělsko                     | ✓ | ✓ |
| Svalbard                  | ✓ | ✓ |
| Švédsko                    | ✓ | ✓ |
| Švýcarsko               | ✓ | ✓ |
| Turecko                    | ✓ | ✓ |
| Ukrajina                   | ✓ | ✓ |
| Spojené království            | ✓ | ✓ |
| Vatikán              | ✓ | ✓ |

## <a name="next-steps"></a>Další postup

Další informace o Azure Maps vykreslování najdete v tématu [úrovně přiblížení a mřížka dlaždic](zoom-levels-and-tile-grid.md).

Další informace o [pokrytí oblastí pro službu Maps, směrování služby](routing-coverage.md). 