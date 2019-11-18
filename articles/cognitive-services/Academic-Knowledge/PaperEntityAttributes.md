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
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: fc3bb5987c31fe718951a3cae02ed7c4ddc29957
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123077"
---
# <a name="paper-entity"></a>Entita papíru

> [!NOTE]
> Níže jsou uvedeny atributy, které jsou specifické pro papírovou entitu. (Ty = 0)

Název | Popis | Typ | Operace
--- | --- | --- | ---
AA.AfId | ID přidružení autora | Int64 | Rovná se
Vpravo. AfN | Název afilace autora | Řetězec | Equals, StartsWith
AA.AuId | ID autora | Int64 | Rovná se
AA.AuN | Normalizované jméno autora | Řetězec | Equals, StartsWith
Vpravo. DAuN | Původní jméno autora | Řetězec | Žádný
Vpravo. DAfN | Název původní afilace | Řetězec | Žádný
Vpravo. Pracují | Číselná pozice v seznamu autorů | Datový typ Int32 | Rovná se
BT | Typ dokumentu BibTex (' a ': článek deníku, ' b ': kniha, ' c ': Kapitola Kapitola, ' p ': konferenční papír) | Řetězec | Žádný
BV | Název místa pro BibTex | Řetězec | Žádný
C.CId | ID řady konferenčních kurzů | Int64 | Rovná se
C.CN | Název řady konferencí | Řetězec | Equals, StartsWith
CC | Počet citací | Datový typ Int32 | Žádný  
CitCon | Kontexty citací</br></br>Seznam odkazovaného ID papíru a odpovídající kontext v dokumentu (např. [{123: ["Brown Foxes jsou známy pro přechod tak, jak je odkazováno v dokumentu 123" "," opožděné psi jsou historické misnomer, jak je znázorněno na papíře 123 "]}). | Vlastní | Žádný
D | Datum publikování ve formátu RRRR-MM-DD | Datum | Rovná se, mezi
ROZLIŠUJÍCÍ NÁZEV | Původní nadpis papíru | Řetězec | Žádný
DOI | Identifikátor digitálního objektu | Řetězec | Equals, StartsWith
E | Rozšířená metadata</br></br>**Důležité**: Tento atribut je zastaralý a podporuje se jenom pro starší verze aplikací. Vyžadování tohoto atributu jednotlivě (tj. atributy = ID, ti, E) bude mít za následek vrácení všech rozšířených atributů metadat v *serializovaném řetězci JSON* .</br></br>Všechny atributy obsažené v rozšířených metadatech jsou nyní k dispozici jako atribut nejvyšší úrovně a mohou být požadovány jako takové (tj. atributy = ID, ti, DOI, IA). | [Cházející](#extended) | Žádný
ECC | Odhadovaný počet citací | Datový typ Int32 | Žádný
F. DFN | Původní pole názvu studie | Řetězec | Žádný
F.FId | Pole ID studie | Int64 | Rovná se
F.FN | Normalizované pole s názvem studie | Řetězec | Equals, StartsWith
FP | První stránka dokumentu v publikaci | Řetězec | Rovná se
I | Problém publikace | Řetězec | Rovná se
HMOTNÉ | Obrácený abstrakt | [InvertedAbstract](#invertedabstract) | Žádný
ID | ID papíru | Int64 | Rovná se
J. JId | ID deníku | Int64 | Rovná se
J. JN | Název deníku | Řetězec | Equals, StartsWith
ÚLOH | Poslední stránka dokumentu v publikaci | Řetězec | Rovná se
PB | Vydavatel | Řetězec | Žádný
Bodů | Typ publikace (0: neznámý, 1: článek o deníku, 2: patent, 3: konferenční papír, 4: Kapitola knihy, 5: kniha, 6: položka odkaz na knihu, 7: datová sada, 8: úložiště | Řetězec | Rovná se
Mezinárodní | Seznam odkazovaných ID papíru | Int64[] | Rovná se
S | Seznam zdrojových adres URL dokumentu seřazený podle relevance | [Zdroj](#source)[] | Žádný
Čas | Normalizovaný název | Řetězec | Equals, StartsWith
V | Svazek publikace | Řetězec | Rovná se
VFN | Úplný název deníku nebo místa konání konference | Řetězec | Žádný
VSN | Krátký název deníku nebo místa konání konference | Řetězec | Žádný
W | Jedinečná slova v názvu | Řetězec [] | Rovná se
Ano | Rok Publikováno | Datový typ Int32 | Rovná se, mezi

## <a name="extended"></a>Cházející
> [!IMPORTANT]
> Tento atribut je zastaralý a podporuje se jenom pro starší verze aplikací. Vyžadování tohoto atributu jednotlivě (tj. atributy = ID, ti, E) bude mít za následek vrácení všech rozšířených atributů metadat v *serializovaném řetězci JSON* .</br></br>Všechny atributy obsažené v rozšířených metadatech jsou nyní k dispozici jako atribut nejvyšší úrovně a mohou být požadovány jako takové (tj. atributy = ID, ti, DOI, IA).

> [!IMPORTANT]
> Podpora pro vyžádání individuálních rozšířených atributů pomocí "E" obor, tj. "E. DN", je zastaralý. I když je tato možnost stále technicky podporovaná, požadují se jednotlivé rozšířené atributy pomocí "E". Výsledkem bude hodnota atributu vracená na dvou místech v odpovědi JSON jako součást objektu "E" a jako atribut nejvyšší úrovně.

Název | Popis | Typ | Operace
--- | --- | --- | ---
BT | Typ dokumentu BibTex (' a ': článek deníku, ' b ': kniha, ' c ': Kapitola Kapitola, ' p ': konferenční papír) | Řetězec | Žádný
BV | Název místa pro BibTex | Řetězec | Žádný
CC | Kontexty citací</br></br>Seznam odkazovaného ID papíru a odpovídající kontext v dokumentu (např. [{123: ["Brown Foxes jsou známy pro přechod tak, jak je odkazováno v dokumentu 123" "," opožděné psi jsou historické misnomer, jak je znázorněno na papíře 123 "]}). | Vlastní | Žádný
ROZLIŠUJÍCÍ NÁZEV | Původní nadpis papíru | Řetězec | Žádný
DOI | Identifikátor digitálního objektu | Řetězec | Žádný
FP | První stránka dokumentu v publikaci | Řetězec | Žádný
I | Problém publikace | Řetězec | Žádný
HMOTNÉ | Obrácený abstrakt | [InvertedAbstract](#invertedabstract) | Žádný
ÚLOH | Poslední stránka dokumentu v publikaci | Řetězec | Žádný
PB | Vydavatel | Řetězec | Žádný
S | Seznam zdrojových adres URL dokumentu seřazený podle relevance | [Zdroj](#source)[] | Žádný
V | Svazek publikace | Řetězec | Žádný
VFN | Úplný název deníku nebo místa konání konference | Řetězec | Žádný
VSN | Krátký název deníku nebo místa konání konference | Řetězec | Žádný

## <a name="invertedabstract"></a>InvertedAbstract

> [!IMPORTANT]
> Atributy InvertedAbstract se nedají přímo vyžádat jako návratový atribut. Pokud potřebujete individuální atribut, musíte požádat o atribut nejvyšší úrovně "IA", tj. k získání jazyka IA. Atributy žádosti IndexLength = IA

Název | Popis | Typ | Operace
--- | --- | --- | ---
IndexLength | Počet položek v indexu (počet slov abstrakce) | Datový typ Int32 | Žádný
InvertedIndex | Seznam abstraktních slov a jejich odpovídající pozice v původní abstraktu (např. [{": [0, 15, 30]}, {" Brown ": [1]}, {" Fox ": [2]}]) | Vlastní | Žádný

## <a name="source"></a>Zdroj

> [!IMPORTANT]
> Zdrojové atributy nelze přímo vyžádat jako návratový atribut. Pokud potřebujete jednotlivý atribut, musíte požádat o atribut nejvyšší úrovně, tj. k získání atributů žádosti S. U = S.

Název | Popis | Typ | Operace
--- | --- | --- | ---
Ty | Typ zdrojové adresy URL (1: HTML, 2: text, 3: PDF, 4: DOC, 5: PPT, 6: XLS, 7: PS) | Řetězec | Žádný
U | Adresa URL zdroje | Řetězec | Žádný
