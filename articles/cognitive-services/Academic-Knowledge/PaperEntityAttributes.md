---
title: Atributy entity papíru – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Seznamte se s atributy, které můžete použít spolu s entitou Paper v Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 4b9431469a7925d26003ad9c34f6b401e5767f6d
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704937"
---
# <a name="paper-entity"></a>Entita papíru

<sub>* Níže jsou atributy specifické pro papírovou entitu. (Ty = 0)</sub>


Name    |Popis                                        |type       | Operace
------- | ------------------------------------------------- | --------- | ----------------------------
Id      |ID entity                                          |Int64      |Je rovno
Čas      |Název papíru                                        |Řetězec     |Rovná<br/>StartsWith
O       |Kód jazyka papíru oddělený znakem "\@"\@\@          |Řetězec     |Je rovno
Ano       |Rok papíru                                         |Int32      |Rovná<br/>Mezi
D       |Datum papíru                                         |Date       |Rovná<br/>Mezi
CC      |Počet citací                                     |Int32      |žádný  
ECC     |Odhadovaný počet citací                           |Int32      |žádný
AA.AuN  |Jméno autora                                        |Řetězec     |Rovná<br/>StartsWith
AA.AuId |ID autora                                          |Int64      |Je rovno
Vpravo. AfN  |Název afilace autora                            |Řetězec     |Rovná<br/>StartsWith
AA.AfId |ID přidružení autora                              |Int64      |Je rovno
VPRAVO. PRACUJÍ    |Pořadí autora dokumentu                         |Int32      |Je rovno
F.FN    |Pole názvu studie                                |Řetězec     |Rovná<br/>StartsWith
F.FId   |Pole ID studie                                  |Int64      |Je rovno
J. JN    |Název deníku                                       |Řetězec     |Rovná<br/>StartsWith
J. JId   |ID deníku                                         |Int64      |Je rovno
C.CN    |Název řady konferencí                             |Řetězec     |Rovná<br/>StartsWith
C.CId   |ID řady konferenčních kurzů                               |Int64      |Je rovno
Mezinárodní     |ID odkazovaných dokumentů                              |Int64[]    |Je rovno
st       |Slova z titulu a abstrakce papíru                |Řetězec []   |Je rovno
E       |Rozšířená metadata (viz tabulka níže)                |Řetězec     |žádný  
        


## <a name="extended-metadata-attributes"></a>Rozšířené atributy metadat ##

Name    | Popis               
--------|---------------------------    
ROZLIŠUJÍCÍ NÁZEV      | Zobrazovaný název dokumentu 
ne       | Zdroje – seznam webových zdrojů dokumentu seřazený podle statického rozměru
S. ty    | Typ zdroje (1: HTML, 2: text, 3: PDF, 4: DOC, 5: PPT, 6: XLS, 7: PS)
S. U     | Zdrojová adresa URL
VFN     | Celé jméno – celé jméno, deník nebo konference
VSN     | Krátké jméno – krátký název deníku nebo konference
V       | Svazek deníku svazků
BV      | Název deníku
BT      | 
PB      | Zkratky deníku
I       | Problém – problém deníku
FP      | FirstPage – první stránka dokumentu
ÚLOH      | LastPage – poslední stránka dokumentu
DOI     | Identifikátor digitálního objektu
CC      | Kontexty citací – seznam odkazovaných ID papíru a odpovídající kontext v dokumentu (např. [{123: ["Brown Foxes jsou známy pro přechod tak, jak je odkazováno v dokumentu 123" "," opožděné psi jsou historické misnomer, jak je znázorněno na papíře 123 "]}).
HMOTNÉ      | Obrácený abstrakt
IA.IndexLength| Počet položek v indexu (počet slov abstrakce)
IA.InvertedIndex| Seznam abstraktních slov a jejich odpovídající pozice v původní abstraktu (např. [{": [0, 15, 30]}, {" Brown ": [1]}, {" Fox ": [2]}])
