---
title: Atributy entity papíru – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Seznamte se s atributy, které můžete použít spolu s entitou Paper v Academic Knowledge API.
services: cognitive-services
author: DarrinEide
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: c300a6477daa5759a68d5d11d40b1a71b46bd808
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793825"
---
# <a name="paper-entity"></a>Entita papíru

<sub>* Níže jsou atributy specifické pro papírovou entitu. (Ty = 0)</sub>

Name (Název) | Popis | Typ | Operations
--- | --- | --- | ---
Vpravo. AfId | ID přidružení autora | Int64 | Rovná se
Vpravo. AfN | Název afilace autora | Řetězec | Equals, StartsWith
Vpravo. AuId | ID autora | Int64 | Rovná se
Vpravo. AuN | Normalizované jméno autora | Řetězec | Equals, StartsWith
Vpravo. DAuN | Původní jméno autora | Řetězec | Žádné
Vpravo. DAfN | Název původní afilace | Řetězec | Žádné
Vpravo. Pracují | Číselná pozice v seznamu autorů | Uvedena | Rovná se
UZEL | Počet citací | Uvedena | Žádné  
C. CId | ID řady konferenčních kurzů | Int64 | Rovná se
C.CN | Název řady konferencí | Řetězec | Equals, StartsWith
D | Datum publikování ve formátu RRRR-MM-DD | Datum | Rovná se, mezi
Cerebrální | Rozšířená metadata (viz tabulka níže) | Řetězec | Nevztahuje se  
ECC | Odhadovaný počet citací | Uvedena | Žádné
F. DFN | Původní pole názvu studie | Řetězec | Žádné
F. FId | Pole ID studie | Int64 | Rovná se
F. FN | Normalizované pole s názvem studie | Řetězec | Equals, StartsWith
ID | ID papíru | Int64 | Rovná se
J. JId | ID deníku | Int64 | Rovná se
J. JN | Název deníku | Řetězec | Equals, StartsWith
Bodů | Typ publikace (0: neznámý, 1: článek o deníku, 2: patent, 3: konferenční papír, 4: Kapitola knihy, 5: kniha, 6: položka odkaz na knihu, 7: datová sada, 8: úložiště | Řetězec | Rovná se
Mezinárodní | Seznam odkazovaných ID papíru | Int64 [] | Rovná se
Čas | Normalizovaný název | Řetězec | Equals, StartsWith
W | Jedinečná slova v názvu | Řetězec [] | Rovná se
Ano | Rok Publikováno | Uvedena | Rovná se, mezi

## <a name="extended-metadata-attributes"></a>Rozšířené atributy metadat ##

Name (Název) | Popis               
--- | ---
BT | Typ dokumentu BibTex (' a ': článek deníku, ' b ': kniha, ' c ': Kapitola Kapitola, ' p ': konferenční papír)
BV | Název místa pro BibTex
UZEL | Kontexty citací – seznam odkazovaných ID papíru a odpovídající kontext v dokumentu (např. [{123: ["Brown Foxes jsou známy pro přechod tak, jak je odkazováno v dokumentu 123" "," opožděné psi jsou historické misnomer, jak je znázorněno na papíře 123 "]}).
JMÉNA | Původní nadpis papíru
DOI | Identifikátor digitálního objektu
DP | První stránka dokumentu v publikaci
I | Problém publikace
HMOTNÉ | Obrácený abstrakt
Hmotné. IndexLength | Počet položek v indexu (počet slov abstrakce)
Hmotné. InvertedIndex | Seznam abstraktních slov a jejich odpovídající pozice v původní abstraktu (např. [{": [0, 15, 30]}, {" Brown ": [1]}, {" Fox ": [2]}])
ÚLOH | Poslední stránka dokumentu v publikaci
PB | Vydavatel
S | Zdroje – seznam webových zdrojů dokumentu seřazený podle statického rozměru
S. ty | Typ zdroje (1: HTML, 2: text, 3: PDF, 4: DOC, 5: PPT, 6: XLS, 7: PS)
S. U | Adresa URL zdroje
ICES | Svazek publikace
VFN | Úplný název deníku nebo místa konání konference
VSN | Krátký název deníku nebo místa konání konference
