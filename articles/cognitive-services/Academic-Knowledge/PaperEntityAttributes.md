---
title: Atributy entit papíru – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Další atributy, které můžete použít s papíru entit v rozhraní Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: 92844b5faf691b67617c9f3424a1322aa05429bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64875743"
---
# <a name="paper-entity"></a>Entity dokumentu

<sub> * Následující atributy jsou specifická pro entity papíru. (Ty = "0") </sub>


Název    |Popis                                        |Type       | Operace
------- | ------------------------------------------------- | --------- | ----------------------------
ID      |Entity ID                                          |Int64      |Je rovno
Ti      |Název dokumentu                                        |String     |Je rovno<br/>StartsWith
L       |Kód jazyka papíru oddělené "\@\@\@"          |String     |Je rovno
Ano       |Rok papíru                                         |Int32      |Je rovno<br/>IsBetween
D       |Datum papíru                                         |Datum       |Je rovno<br/>IsBetween
CC      |Počet citace                                     |Int32      |None  
ECC     |Citaci odhadovaný počet                           |Int32      |None
AA.AuN  |Jméno autora                                        |String     |Je rovno<br/>StartsWith
AA.AuId |ID autora                                          |Int64      |Je rovno
AA.AfN  |Jméno autora přidružení                            |String     |Je rovno<br/>StartsWith
AA.AfId |ID umístění autora                              |Int64      |Je rovno
AA.S    |Autor pořadí papíru                         |Int32      |Je rovno
F.FN    |Pole Název studie                                |String     |Je rovno<br/>StartsWith
F.FId   |Pole ID studie                                  |Int64      |Je rovno
J.JN    |Název deníku                                       |String     |Je rovno<br/>StartsWith
J.JId   |ID deníku                                         |Int64      |Je rovno
C.CN    |Název řady konference                             |String     |Je rovno<br/>StartsWith
C.CId   |ID číselné řady konference                               |Int64      |Je rovno
Identifikátorů rId     |Odkazované Paper ID                              |Int64[]    |Je rovno
W       |Slova z názvu dokumentu a Abstract současně                |Řetězec]   |Je rovno
E       |Rozšířená metadata (viz tabulka níže)                |String     |None  
        


## <a name="extended-metadata-attributes"></a>Rozšířené atributy metadat ##

Name    | Popis               
--------|---------------------------    
ROZLIŠUJÍCÍ NÁZEV      | Zobrazovaný název papíru 
S       | Zdroje – seznam webové zdroje papíru, seřazené podle hodnocení statické
S.Ty    | Source Type (1:HTML, 2:Text, 3:PDF, 4:DOC, 5:PPT, 6:XLS, 7:PS)
S.U     | Adresa URL zdroje
VFN     | Jméno a příjmení příslušností – celý název deníku nebo místa konání konference
VSN     | Krátký název příslušností - krátký název deníku nebo místa konání konference
V       | Svazek – svazek deníku
BV      | Název deníku
BT      | 
PB      | Zkratky deníku
I       | Chyba – problém deníku
FP      | Události FirstPage – první stránka papíru
LP      | LastPage – poslední stránky z dokumentu
DOI     | Identifikátor digitální objektu
CC      | Kontexty citace – seznam odkazovaných papíru ID a odpovídající kontext v dokumentu (například [{123: ["Hnědý lišek jsou známé pro přechod, který jste použili v dokumentu 123", "opožděné psi jsou historických misnomer, jak je uvedeno v dokumentu 123"]})
IA      | Obráceným abstraktní
IA.IndexLength| Počet položek v indexu (počet slov na abstraktní)
IA.InvertedIndex| Seznam abstraktní slova a jejich odpovídající pozici v původní abstraktní (například [{"the": [0, 15, 30]}, {"Hnědý": [1]}, {"fox":[2]}])
