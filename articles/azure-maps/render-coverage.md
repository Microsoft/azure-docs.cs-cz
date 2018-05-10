---
title: Vykreslení pokrytí v rámci služby Azure Maps | Microsoft Docs
description: Další informace o vykreslení pokrytí v rámci služby Azure Maps
services: azure-maps
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 03/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: ''
ms.openlocfilehash: ab05277c4541ae859f79b1108c4cf8a7beb29271
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="azure-maps-render-coverage"></a>Vykreslení pokrytí Azure mapy

Azure mapy používá k vytvoření mapy rastrových dlaždice a vektoru dlaždice. V jeho nejnižší rozlišení celého světa se vejde na jednu dlaždici. Na nejvyšší rozlišení představuje jednu dlaždici 38 odmocnina měřidla. Jako Přiblížit na mapě, proto se zobrazí stále další podrobnosti o přes kontinenty až, oblastí, města a jednotlivých ulice. Další informace najdete v tématu [úrovně přiblížení a dlaždici mřížky](zoom-levels-and-tile-grid.md).

Mapy však nemá stejnou úroveň informací a přesnost pro všechny oblasti. Následující tabulka obsahuje informace o úroveň vykreslené podrobností, můžete očekávat od každou oblast.

## <a name="legend"></a>Legenda

| Symbol | Význam |
|--------|---------|
| ✓ | Oblast je reprezentována pomocí podrobná data.   |
| Ø | Oblast je reprezentována zjednodušené daty. |


## <a name="africa"></a>Afrika 


| Oblast | Rastrové dlaždice Unified | Dlaždice vektoru Unified |
| ------ | :------------------: | :------------------: |
| Alžírsko                          | ✓ | ✓ |
| Angola                           | ✓ | ✓ |
| Benin                            | ✓ | ✓ |
| Botswana                         | ✓ | ✓ |
| Burkina Faso                     | ✓ | ✓ |
| Burundi                          | ✓ | ✓ |
| Cabo Verde                       |   | ✓ |
| Kamerun                         | ✓ | ✓ |
| Středoafrická republika         |   | Ø |
| Čad                             |   | Ø |
| Komory                          |   | Ø |
| Kongo                            | ✓ | ✓ |
| Demokratická republika Kongo | ✓ | ✓ |
| Pobřeží slonoviny                    |   | Ø |
| Džibutsko                         |   | Ø |
| Egypt                            | ✓ | ✓ |
| Rovníková Guinea                |   | Ø |
| Eritrea                          |   | Ø |
| Etiopie                         |   | Ø |
| Gabon                            | ✓ | ✓ |
| Gambie                           |   | Ø |
| Ghana                            | ✓ | ✓ |
| Guinea                           |   | Ø |
| Guinea-Bissau                    |   | Ø |
| Keňa                            | ✓ | ✓ |
| Lesotho                          | ✓ | ✓ |
| Libérie                          |   | Ø |
| Libye                            |   | Ø |
| Madagaskar                       |   | Ø |
| Malawi                           | ✓ | ✓ |
| Mali                             | ✓ | ✓ |
| Mauritánie                       | ✓ | ✓ |
| Mauricius                        | ✓ | ✓ |
| Mayotte                          | ✓ | ✓ |
| Maroko                          | ✓ | ✓ |
| Mozambik                       | ✓ | ✓ |
| Namibie                          | ✓ | ✓ |
| Niger                            | ✓ | ✓ |
| Nigérie                          | ✓ | ✓ |
| Réunion                          | ✓ | ✓ |
| Rwanda                           | ✓ | ✓ |
| Svatá Helena, Ascension a Tristan da Cunha |   | Ø |
| Svatý Tomáš a Princův ostrov            |   | Ø |
| Senegal                          | ✓ | ✓ |
| Sierra Leone                     |   | Ø |
| Somálsko                          |   | Ø |
| Jihoafrická republika                     | ✓ | ✓ |
| Jižní Súdán                      |   | Ø |
| Súdán                            |   | Ø |
| Svazijsko                        | ✓ | ✓ |
| Tanzanská sjednocená republika      | ✓ | ✓ |
| Togo                             | ✓ | ✓ |
| Tunisko                          | ✓ | ✓ |
| Uganda                           | ✓ | ✓ |
| Zambie                           | ✓ | ✓ |
| Zimbabwe                         | ✓ | ✓ |

## <a name="americas"></a>Amerika

| Oblast | Rastrové dlaždice Unified | Dlaždice vektoru Unified |
| ------ | :------------------: | :------------------: |
| Anguilla                  | ✓ | ✓ |
| Antigua a Barbuda       | ✓ | ✓ |
| Argentina                 | ✓ | ✓ |
| Aruba                     | ✓ | ✓ |
| Bahamy                   | ✓ | ✓ |
| Barbados                  | ✓ | ✓ |
| Belize                    | ✓ | ✓ |
| Bermudy                   |   | ✓ |
| Mnohonárodní stát Bolívie |   | ✓ |
| Bonaire, Svatý Eustach a Saba |   | ✓ |
| Brazílie                    | ✓ | ✓ |
| Kanada                    | ✓ | ✓ |
| Kajmanské ostrovy            | ✓ | ✓ |
| Chile                     | ✓ | ✓ |
| Clippertonův ostrov         |   | ✓ |
| Kolumbie                  | ✓ | ✓ |
| Kostarika                |   | ✓ |
| Kuba                      | ✓ | ✓ |
| Curaçao                   | ✓ | ✓ |
| Dominika                  | ✓ | ✓ |
| Dominikánská republika        | ✓ | ✓ |
| Ekvádor                   |   | ✓ |
| Falklandy (Malvíny) |   | ✓ |
| Francouzská Guyana             | ✓ | ✓ |
| Grónsko                 |   | Ø |
| Grenada                   | ✓ | ✓ |
| Guadeloupe                | ✓ | ✓ |
| Guatemala                 |   | ✓ |
| Guyana                    | ✓ | ✓ |
| Haiti                     | ✓ | ✓ |
| Honduras                  | ✓ | ✓ |
| Jamajka                   | ✓ | ✓ |
| Martinik                | ✓ | ✓ |
| Mexiko                    | ✓ | ✓ |
| Montserrat                | ✓ | ✓ |
| Nikaragua                 | ✓ | ✓ |
| Severní Mariany  |   | ✓ |
| Panama                    | ✓ | ✓ | 
| Paraguay                  |   | ✓ |
| Peru                      | ✓ | ✓ |
| Portoriko               | ✓ | ✓ |
| Quebec (Kanada)           |   | ✓ |
| Svatý Bartoloměj          | ✓ | ✓ |
| Svatý Kryštof a Nevis     | ✓ | ✓ |
| Svatá Lucie               | ✓ | ✓ |
| Svatý Martin (francouzština)     | ✓ | ✓ |
| Svatý Pierre a Miquelon |   | ✓ |
| Svatý Vincenc a Grenadiny | ✓ | ✓ |
| Sint Maarten (holandština)      | ✓ | ✓ |
| Jižní Georgie a Jižní Sandwichovy ostrovy |   | ✓ |
| Surinam                  |   | ✓ |
| Trinidad a Tobago       | ✓ | ✓ |
| Turks a Caicos  | ✓ | ✓ |
| Spojené státy             | ✓ | ✓ |
| Uruguay                   | ✓ | ✓ |
| Venezuela                 | ✓ | ✓ |
| Britské Panenské ostrovy   | ✓ | ✓ |
| Americké Panenské ostrovy      | ✓ | ✓ |

## <a name="asia"></a>Asie 

| Oblast | Rastrové dlaždice Unified | Dlaždice vektoru Unified |
| ------ | :------------------: | :------------------: |
| Afghánistán               |   | Ø |
| Bahrajn                   | ✓ | ✓ |
| Bangladéš                |   | Ø |
| Bhútán                    |   | Ø |
| Britské území v Indickém oceánu |   | Ø |
| Brunej                    | ✓ | ✓ |
| Kambodža                  |   | Ø |
| Čína                     |   | Ø |
| Kokosové ostrovy (Keeling)   |   | Ø |
| Korejská lidově demokratická republika |   | Ø |
| Dokdo a Takeshima       |   | Ø |
| Hongkong                 | ✓ | ✓ |
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
| Macao                     | ✓ | ✓ |
| Malajsie                  | ✓ | ✓ |
| Maledivy                  |   | Ø |
| Mongolsko                  |   | Ø |
| Myanmar                   |   | Ø |
| Napal                     |   | Ø |
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

| Oblast | Rastrové dlaždice Unified | Dlaždice vektoru Unified |
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
| Norfolk (ostrov)            |   | Ø |
| Palau                     |   | Ø |
| Papua - Nová Guinea          |   | Ø |
| Ostrovy Pitcairn                  |   | Ø |
| Samoa                     |   | Ø |
| Šalamounovy ostrovy           |   | Ø|
| Tokelau                   |   | Ø |
| Tonga                     |   | Ø |
| Tuvalu                    |   | Ø |
| Vanuatu                   |   | Ø |
| Wallis a Futuna         |   | Ø |


## <a name="europe"></a>Evropa

| Oblast | Rastrové dlaždice Unified | Dlaždice vektoru Unified |
| ------ | :------------------: | :------------------: |
| Albánie                   | ✓ | ✓ |
| Andorra                   | ✓ | ✓ |
| Arménie                   |   | Ø |
| Rakousko                   | ✓ | ✓ |
| Ázerbájdžán                |   | Ø |
| Bělorusko                   | Ø | ✓ |
| Belgie                   | ✓ | ✓ |
| Bosny a Hercegoviny        | ✓ | ✓ |
| Bulharsko                  | ✓ | ✓ |
| Chorvatsko                   | ✓ | ✓ |
| Kypr                    | ✓ | ✓ |
| Česká republika            | ✓ | ✓ |
| Dánsko                   | ✓ | ✓ |
| Estonsko                   | ✓ | ✓ |
| Faerské ostrovy             |   | Ø |
| Finsko                   | ✓ | ✓ |
| Francie                    | ✓ | ✓ |
| Gruzie                   |   | Ø |
| Německo                   | ✓ |✓ |
| Gibraltar                 | ✓ |   |
| Řecko                    | ✓ | ✓ |
| Guernsey                  |   | ✓ |
| Maďarsko                   | ✓ | ✓ |
| Island                   | ✓ | ✓ |
| Irsku (republika)     | ✓ | ✓ |
| Ostrov Man               |   | ✓ |
| Itálie                     | ✓ | ✓ |
| Jan Mayen                 |   | ✓ |
| Jersey                    |   | ✓ |
| Lotyšsko                    | ✓ | ✓ |
| Lichtenštejnsko             | ✓ | ✓ |
| Litva                 | ✓ | ✓ |
| Lucembursko                | ✓ | ✓ |
| Makedonie – bývalá rep. Jugoslávie                 | ✓ | ✓ |
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
| Jižní Kurils           |   | ✓ |
| Španělsko                     | ✓ | ✓ |
| Svalbard                  |   | ✓ |
| Švédsko                    | ✓ |   |
| Švýcarsko               | ✓ | ✓ |
| Turecko                    | ✓ | ✓ |
| Ukrajina                   | ✓ | ✓ |
| Spojené království            | ✓ | ✓ |
| Vatikán              | ✓ | ✓ |

## <a name="next-steps"></a>Další postup

Další informace o vykreslování mapy Azure najdete v tématu [úrovně přiblížení a dlaždici mřížky](zoom-levels-and-tile-grid.md).

Další informace o [pokrytí oblasti pro Maps směrování služby](routing-coverage.md). 