---
title: Pokrytí vykreslování v Azure Maps | Microsoft Docs
description: Další informace o pokrytí vykreslování v Azure Maps
author: jingjing-z
ms.author: jinzh
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5affc9952cfe13ebfeede126051f2236499d1343
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934290"
---
# <a name="azure-maps-render-coverage"></a>Pokrytí vykreslování Azure Maps

Pro vytváření map používá Azure Maps rastrové dlaždice i vektorové dlaždice. V nejnižším rozlišení se celý svět vejde na jednu dlaždici. V jeho nejvyšším rozlišení jedna dlaždice představuje 38 čtvereční měřiče. Při přiblížení na mapě vám proto můžete zobrazit stále více podrobností o kontinentech, oblastech, městech a jednotlivých ulic. Další informace najdete v tématu [úrovně přiblížení a mřížka dlaždic](zoom-levels-and-tile-grid.md).

Mapy ale nemají stejnou úroveň informací a přesnost pro všechny oblasti. Následující tabulky obsahují informace o tom, jakou úroveň podrobností z jednotlivých oblastí se má vykreslovat.

## <a name="legend"></a>Popisek

| Písmeno | Význam |
|--------|---------|
| ✓ | Oblast je zastoupena s podrobnými daty.   |
| Ř | Oblast je reprezentována s zjednodušenými daty. |


## <a name="africa"></a>Afrika 


| Země/oblast | Rastrové dlaždice sjednocené | Sjednocení vektorových dlaždic |
| ------ | :------------------: | :------------------: |
| Alžírsko                          | ✓ | ✓ |
| Angola                           | ✓ | ✓ |
| Benin                            | ✓ | ✓ |
| Botswana                         | ✓ | ✓ |
| Burkina Faso                     | ✓ | ✓ |
| Burundi                          | ✓ | ✓ |
| Cabo Verde                       | ✓ | ✓ |
| Kamerun                         | ✓ | ✓ |
| Středoafrická republika         | ✓ | Ř |
| Čad                             | ✓ | Ř |
| Komory                          | ✓ | Ř |
| Demokratická republika Kongo | ✓ | ✓ |
| Pobřeží slonoviny                    | ✓ | Ř |
| Džibutsko                         | ✓ | Ř |
| Egypt                            | ✓ | ✓ |
| Rovníková Guinea                | ✓ | Ř |
| Eritrea                          | ✓ | Ř |
| Etiopie                         | ✓ | Ř |
| Gabon                            | ✓ | ✓ |
| Gambie                           | ✓ | Ř |
| Ghana                            | ✓ | ✓ |
| Guinea                           | ✓ | Ř |
| Guinea-Bissau                    | ✓ | Ř |
| Keňa                            | ✓ | ✓ |
| Lesotho                          | ✓ | ✓ |
| Libérie                          | ✓ | Ř |
| Libye                            | ✓ | Ř |
| Madagaskar                       | ✓ | Ř |
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
| Svatá Helena, Ascension a Tristan da Cunha | ✓ | Ř |
| Svatý Tomáš a Princův ostrov            | ✓ | Ř |
| Senegal                          | ✓ | ✓ |
| Sierra Leone                     | ✓ | ✓ |
| Somálsko                          | ✓ | ✓ |
| Jihoafrická republika                     | ✓ | ✓ |
| Jižní Súdán                      | ✓ | ✓ |
| Súdán                            | ✓ | ✓ |
| Svazijsko                        | ✓ | ✓ |
| Česká republika Tanzanie      | ✓ | ✓ |
| Togo                             | ✓ | ✓ |
| Tunisko                          | ✓ | ✓ |
| Uganda                           | ✓ | ✓ |
| Zambie                           | ✓ | ✓ |
| Zimbabwe                         | ✓ | ✓ |

## <a name="americas"></a>Amerika

| Země/oblast | Rastrové dlaždice sjednocené | Sjednocení vektorových dlaždic |
| ------ | :------------------: | :------------------: |
| Anguilla                  | ✓ | ✓ |
| Antigua a Barbuda       | ✓ | ✓ |
| Argentina                 | ✓ | ✓ |
| Aruba                     | ✓ | ✓ |
| Bahamy                   | ✓ | ✓ |
| Barbados                  | ✓ | ✓ |
| Belize                    | ✓ | ✓ |
| Bermudy                   | ✓ | ✓ |
| Mnohonárodní okres Bolívie | ✓ | ✓ |
| Bonaire, Sint Eustach a Saba | ✓ | ✓ |
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
| Grónsko                 | ✓ | Ř |
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
| Saint-Pierre a Miquelon | ✓ | ✓ |
| Svatý Vincenc a Grenadiny | ✓ | ✓ |
| Svatý Martin (Nizozemsko)      | ✓ | ✓ |
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

| Země/oblast | Rastrové dlaždice sjednocené | Sjednocení vektorových dlaždic |
| ------ | :------------------: | :------------------: |
| Afghánistán               |   | Ř |
| Bahrajn                   | ✓ | ✓ |
| Bangladéš                |   | Ř |
| Bhútán                    |   | Ř |
| Britské území v Indickém oceánu |   | Ř |
| Sultanát                    | ✓ | ✓ |
| Kambodža                  |   | Ř |
| Čína                     |   | Ř |
| Kokosové (Keelingovy) ostrovy   |   | Ř |
| Korejská lidově demokratická republika |   | Ř |
| Hongkong – zvláštní správní oblast             | ✓ | ✓ |
| Indie                     | Ř | ✓ | 
| Indonésie                 | ✓ | ✓ |
| Írán                      |   | Ř |
| Irák                      | ✓ | ✓ |
| Izrael                    |   | ✓ |
| Japonsko                     |   | Ř |
| Jordánsko                    | ✓ | ✓ |
| Kazachstán                |   | ✓ |
| Kuvajt                    | ✓ | ✓ |
| Kyrgyzstán                |   | Ř |
| Laoská lidově demokratická republika |   | Ř |
| Libanon                   | ✓ | ✓ |
| Macao – zvláštní správní oblast                 | ✓ | ✓ |
| Malajsie                  | ✓ | ✓ |
| Maledivy                  |   | Ř |
| Mongolsko                  |   | Ř |
| Myanmar                   |   | Ř |
| Nepál                     |   | Ř |
| Omán                      | ✓ | ✓ |
| Pákistán                  |   | Ř |
| Filipíny               | ✓ | ✓ |
| Katar                     | ✓ | ✓ |
| Korejská republika         | ✓ | Ř |
| Saúdská Arábie              | ✓ | ✓ |
| Senkaku ostrovy           |   | ✓ |
| Singapur                 | ✓ | ✓|
| Srí Lanka                 |   | Ř |
| Syrská arabská republika      |   | Ř |
| Tchaj-wan                    | ✓ | ✓ |
| Tádžikistán                |   | Ř |
| Thajsko                  | ✓ | ✓ |
| Timor Leste               |   | Ř |
| Turkmenistán              |   | Ř |
| Spojené arabské emiráty      | ✓ | ✓ |
| Menší odlehlé ostrovy Spojených států amerických |   | Ř |
| Uzbekistán                |   | Ř |
| Vietnam                   | ✓ | ✓ |
| Jemen                     | ✓ | ✓ |

## <a name="oceania"></a>Oceánie a

| Země/oblast | Rastrové dlaždice sjednocené | Sjednocení vektorových dlaždic |
| ------ | :------------------: | :------------------: |
| Americká Samoa            |   | ✓ |
| Austrálie                 | ✓ | ✓ |
| Cookovy ostrovy              |   | Ř |
| Fidži                      |   | Ř |
| Francouzská Polynésie          |   | Ř |
| Guam                      | ✓ | ✓ |
| Kiribati                  |   | Ř |
| Marshallovy ostrovy          |   | Ř |
| Mikronésie                |   | Ř |
| Nauru                     |   | Ř |
| Nová Kaledonie             |   | Ř |
| Nový Zéland               | ✓ | ✓ |
| Niue                      |   | Ř |
| Ostrov Norfolk            |   | Ř |
| Palau                     |   | Ř |
| Papua – Nová Guinea          |   | Ř |
| Pitcairnovy                  |   | Ř |
| Samoa                     |   | Ř |
| Šalamounovy ostrovy           |   | Ř|
| Tokelau                   |   | Ř |
| Tonga                     |   | Ř |
| Tuvalu                    |   | Ř |
| Vanuatu                   |   | Ř |
| Wallis a Futuna         |   | Ř |


## <a name="europe"></a>Evropa

| Země/oblast | Rastrové dlaždice sjednocené | Sjednocení vektorových dlaždic |
| ------ | :------------------: | :------------------: |
| Albánie                   | ✓ | ✓ |
| Andorra                   | ✓ | ✓ |
| Arménie                   | ✓ | Ř |
| Rakousko                   | ✓ | ✓ |
| Ázerbájdžán                | ✓ | Ř |
| Bělorusko                   | Ř | ✓ |
| Belgie                   | ✓ | ✓ |
| Bosna a Hercegovina        | ✓ | ✓ |
| Bulharsko                  | ✓ | ✓ |
| Chorvatsko                   | ✓ | ✓ |
| Kypr                    | ✓ | ✓ |
| Česká republika            | ✓ | ✓ |
| Dánsko                   | ✓ | ✓ |
| Estonsko                   | ✓ | ✓ |
| Faerské ostrovy             | ✓ | Ř |
| Finsko                   | ✓ | ✓ |
| Francie                    | ✓ | ✓ |
| Gruzie                   | ✓ | Ř |
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
| Severní Makedonie           | ✓ | ✓ |
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
| Síť San Marino                | ✓ | ✓ |
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

## <a name="next-steps"></a>Další kroky

Další informace o vykreslování Azure Maps najdete v tématu [úrovně přiblížení a mřížka dlaždic](zoom-levels-and-tile-grid.md).

Přečtěte si o [oblastech pokrytí pro směrovací službu MAPS](routing-coverage.md). 