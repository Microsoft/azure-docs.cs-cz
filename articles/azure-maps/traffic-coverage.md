---
title: Provoz pokrytí v rámci služby Azure Maps | Microsoft Docs
description: Další informace o pokrytí provoz v rámci služby Azure Maps
services: azure-maps
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 11/28/2017
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: ''
ms.openlocfilehash: 08a4f3fc135aae2772bd60c67cbd282603cd4f8d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="azure-maps-traffic-coverage"></a>Azure pokrytí provoz mapy

Azure mapy poskytuje bohaté provoz informace ve formě provoz **toku** a **incidenty**. Tato data lze vizualizované ve mapy nebo použít ke generování chytřejší tras, které v reálném řídí podmínky. 

Mapy však nemá stejnou úroveň informací a přesnost pro všechny oblasti. Následující tabulka obsahuje informace o jaký druh informací provozu můžete požádat každou oblast: 

|Oblast  |Incidenty  |Tok  |
|---------|:---------:|:---------:|
|Argentina      |         |✓         |
|Austrálie     |✓         |✓        |
|Rakousko     |✓         |✓         |
|Bahrajn     |         |✓         |
|Belgie     |✓         |✓         |
|Brazílie     |✓         |✓         |
|Bulharsko     |✓         |✓         |
|Kanada     |✓         |✓         |
|Chile     |✓         |✓         |
|Kolumbie      |         |✓         |
|Chorvatsko     |         |✓         |
|Česká republika     |✓         |✓         |
|Dánsko     |✓         |✓         |
|Egypt     |         |✓         |
|Estonsko     |         | ✓        |
|Finsko     |✓         |✓         |
|+ Ålandy      |✓         |✓         |
|Francie     |✓         |✓         |
|+ Monaco     |✓         |✓         |
|Německo     |✓         |✓         |
|Řecko     |✓         |✓         |
|Hongkong – zvláštní administrativní oblast     |✓         |✓         |
|Maďarsko     |✓         |✓         |
|Island     |         |✓         |
|Indonésie     |✓         |✓         |
|Irsku (republika)     |✓         |✓         |
|Izrael     |         |✓         |
|Itálie     |✓         |✓        |
|+San Marino     |✓         |✓         |
|Kuvajt     |✓         |✓         |
|Lotyšsko     |         |✓         |
|Lesotho     |✓         |✓         |
|Litva     |         |✓         |
|Lucembursko     |✓         |✓         |
|Macao – zvláštní administrativní oblast     |         |✓         |
|Malajsie     |✓         |✓         |
|Malta     |✓         |✓         |
|Mexiko     |✓         |✓         |
|Maroko     |         |✓         |
|Nizozemsko     |✓         |✓         |
|Nový Zéland     |✓         |✓         |
|Norsko     |✓         |✓         |
|Omán     |         |✓         |
|Polsko     |✓         |✓         |
|Portugalsko     |✓         |✓         |
|+ Azory a Madeira     |         |✓         |
|Katar     |         |✓         |
|Rumunsko     |         |✓         |
|Ruská federace     |✓         |✓         |
|Saúdská Arábie     |✓         |✓         |
|Singapur     |✓         |✓         |
|Slovensko     |✓         |✓         |
|Slovinsko     |✓         |✓         |
|Jihoafrická republika     |✓         |✓         |
|Španělsko     |✓         |✓         |
|+Andorra     |✓         |✓         |
|+ Baleárské ostrovy     |✓         |✓         |
|+ Nevztahují     |✓         |✓         |
|+ Gibraltar     |✓         |✓         |
|Švédsko     |✓         |✓         |
|Švýcarsko     |✓         |✓        |
|+Liechtenstein      |✓         |✓         |
|Tchaj-wan     |✓         |✓        |
|Thajsko     |✓         |✓        |
|Turecko     |✓         |✓         |
|Ukrajina     |✓         |✓         |
|Spojené arabské emiráty     |✓         |✓         |
|Spojené království     |✓         |✓         |
|(Guernsey & Jersey)     |✓         |✓         |
|Ostrov Man     |✓         |✓         |
|Spojené státy     |✓         |✓        |
|+ Portoriko     |✓         |✓         |

Další informace o datech přenosy mapuje Azure, najdete v článku [provoz](https://docs.microsoft.com/rest/api/azure-maps/traffic) referenční stránky.