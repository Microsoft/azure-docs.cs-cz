---
title: Atributy entity instance konference – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Seznamte se s atributy, které můžete použít s entitou instance konference v Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 37a353fbb86ca199b2316dcfba5904f4b46b0276
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705062"
---
# <a name="conference-instance-entity"></a>Entita instance konference

<sub>* Následující atributy jsou specifické pro entitu instance konference. (Ty = ' 4 ')</sub>

Name    |Popis                            |type       | Operace
------- | ------------------------------------- | --------- | ----------------------------
Id      |ID entity                              |Int64      |Je rovno
CIN     |Normalizovaný název instance konference ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |Řetězec     |Je rovno
DCN     |Zobrazovaný název instance konference ({ConferenceSeriesName}: {ConferenceInstanceYear})       |Řetězec     |žádný
CIL     |Umístění instance konference    |Řetězec     |Rovná<br/>StartsWith
CISD    |Počáteční datum instance konference  |Date       |Rovná<br/>Mezi
CIED    |Koncové datum instance konference    |Date       |Rovná<br/>Mezi
CIARD   |Datum splatnosti abstraktní registrace instance konference  |Date       |Rovná<br/>Mezi
CISDD   |Datum splatnosti instance konference     |Date       |Rovná<br/>Mezi
CIFVD   |Konečné datum splatnosti instance konferenční verze  |Date       |Rovná<br/>Mezi
CINDD   |Datum oznámení instance konference   |Date       |Rovná<br/>Mezi
CD.T    |Název události instance konference   |Date       |Rovná<br/>Mezi
CD.D    |Datum události instance konference    |Date       |Rovná<br/>Mezi
PCS.CN  |Název řady konferenčních řad instance |Řetězec     |Je rovno
PCS.CId |ID řady konference instance |Int64    |Je rovno
CC      |Celkový počet citací instance konference           |Int32      |žádný  
ECC     |Celkový počet odhadovaných citací instance konference |Int32      |žádný


## <a name="extended-metadata-attributes"></a>Rozšířené atributy metadat ##

Name    | Popis               
--------|---------------------------    
FN      | Úplný název instance konference
