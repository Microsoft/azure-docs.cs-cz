---
title: Papír atributy entit v rozhraní Academic Knowledge API | Dokumentace Microsoftu
description: Další atributy, které můžete použít s papíru entit v rozhraní Academic Knowledge API služeb Cognitive Services.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: 75efba2c2ce8842f233f766876ca00844338fb25
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47094707"
---
# <a name="paper-entity"></a>Entity dokumentu

<sub> * Následující atributy jsou specifická pro entity papíru. (Ty = "0") </sub>


Název    |Popis                                        |Typ       | Operace
------- | ------------------------------------------------- | --------- | ----------------------------
ID      |ID entity                                          |Int64      |Rovná se
Čas      |Název dokumentu                                        |Řetězec     |Je rovno<br/>StartsWith
L       |Dokument jazyka kódu odděleny pomocí "\@@@\"            |Řetězec     |Rovná se
Ano       |Rok papíru                                         |Datový typ Int32      |Je rovno<br/>IsBetween
D       |Datum papíru                                         |Datum       |Je rovno<br/>IsBetween
Kopie      |Počet citace                                     |Datový typ Int32      |žádný  
ECC     |Citaci odhadovaný počet                           |Datový typ Int32      |žádný
AA. AuN  |Jméno autora                                        |Řetězec     |Je rovno<br/>StartsWith
AA. AuId |ID autora                                          |Int64      |Rovná se
AA. AfN  |Jméno autora přidružení                            |Řetězec     |Je rovno<br/>StartsWith
AA. AfId |ID umístění autora                              |Int64      |Rovná se
AA. S    |Autor pořadí papíru                         |Datový typ Int32      |Rovná se
F.FN    |Pole Název studie                                |Řetězec     |Je rovno<br/>StartsWith
F.FId   |Pole ID studie                                  |Int64      |Rovná se
J.JN    |Název deníku                                       |Řetězec     |Je rovno<br/>StartsWith
J.JId   |ID deníku                                         |Int64      |Rovná se
C.CN    |Název řady konference                             |Řetězec     |Je rovno<br/>StartsWith
C.CId   |ID číselné řady konference                               |Int64      |Rovná se
Identifikátorů rId     |Odkazované Paper ID                              |[] Int64    |Rovná se
W       |Slova z názvu dokumentu a Abstract současně                |Řetězec]   |Rovná se
E       |Rozšířená metadata (viz tabulka níže)                |Řetězec     |žádný  
        


## <a name="extended-metadata-attributes"></a>Rozšířené atributy metadat ##

Název    | Popis               
--------|---------------------------    
DN      | Zobrazovaný název papíru 
S       | Zdroje – seznam webové zdroje papíru, seřazené podle hodnocení statické
S.Ty    | Zdrojový typ (1:HTML, 2:Text, 3:PDF, 4:DOC, 5:PPT, 6:XLS, 7:PS)
S.U     | Zdrojová adresa URL
VFN     | Jméno a příjmení příslušností – celý název deníku nebo místa konání konference
VSN     | Krátký název příslušností - krátký název deníku nebo místa konání konference
V       | Svazek – svazek deníku
BV      | Název deníku
BT      | 
PB      | Abreviations deníku
I       | Chyba – problém deníku
FP      | Události FirstPage – první stránka papíru
LP      | LastPage – poslední stránky z dokumentu
DOI     | Identifikátor digitální objektu
Kopie      | Kontexty citace – seznam odkazovaných papíru ID a odpovídající kontext v dokumentu (například [{123: ["Hnědý lišek jsou známé pro přechod, který jste použili v dokumentu 123", "opožděné psi jsou historických misnomer, jak je uvedeno v dokumentu 123"]})
IA      | Obráceným abstraktní
I A. IndexLength| Počet položek v indexu (počet slov na abstraktní)
I A. InvertedIndex| Seznam abstraktní slova a jejich odpovídající pozici v původní abstraktní (například [{"the": [0, 15, 30]}, {"Hnědý": [1]}, {"fox":[2]}])
