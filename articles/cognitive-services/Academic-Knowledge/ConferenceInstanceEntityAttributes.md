---
title: Atributy entity instance konference – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Seznamte se s atributy, které můžete použít s entitou instance konference v Academic Knowledge API.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: c6761206a58724dae96d9dc6f6234658892d4d18
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146563"
---
# <a name="conference-instance-entity"></a>Entita instance konference

> [!NOTE]
> Následující atributy jsou specifické pro entitu instance konference. (Ty = ' 4 ')

Název | Popis | Typ | Operace
--- | --- | --- | ---
CC      |Celkový počet citací instance konference           |Datový typ Int32      |Žádný  
CD.D    |Datum události instance konference    |Datum       |Rovná se, mezi
CD.T    |Název události instance konference   |Datum       |Rovná se, mezi
CIARD   |Datum splatnosti abstraktní registrace instance konference  |Datum       |Rovná se, mezi
CIED    |Koncové datum instance konference    |Datum       |Rovná se, mezi
CIFVD   |Konečné datum splatnosti instance konferenční verze  |Datum       |Rovná se, mezi
CIL     |Umístění instance konference    |Řetězec     |Equals, StartsWith
CIN     |Normalizovaný název instance konference |Řetězec        |Rovná se
CINDD   |Datum oznámení instance konference   |Datum       |Rovná se, mezi
CISD    |Počáteční datum instance konference  |Datum       |Rovná se, mezi
CISDD   |Datum splatnosti instance konference     |Datum       |Rovná se, mezi
DCN     |Zobrazovaný název instance konference  |Řetězec      |Žádný
E | Rozšířená metadata</br></br>**Důležité**: Tento atribut je zastaralý a podporuje se jenom pro starší verze aplikací. Vyžadování tohoto atributu jednotlivě (tj. atributy = ID, ti, E) bude mít za následek vrácení všech rozšířených atributů metadat v *serializovaném řetězci JSON* .</br></br>Všechny atributy obsažené v rozšířených metadatech jsou nyní k dispozici jako atribut nejvyšší úrovně a mohou být požadovány jako takové (tj. atributy = ID, ti, DOI, IA). | [Cházející](#extended) | Žádný
ECC     |Celkový počet odhadovaných citací instance konference |Datový typ Int32      |Žádný
FN | Úplný název instance konference | Řetězec | Žádný
ID      |ID entity                              |Int64      |Rovná se
PC | Celkový počet publikací instancí konference | Datový typ Int32 | Žádný
PCS.CN  |Název nadřazené řady konferenčních konferencí instance |Řetězec  |Rovná se
PCS.CId |ID nadřazené série konference instance |Int64     |Rovná se

## <a name="extended"></a>Cházející

> [!IMPORTANT]
> Tento atribut je zastaralý a podporuje se jenom pro starší verze aplikací. Vyžadování tohoto atributu jednotlivě (tj. atributy = ID, ti, E) bude mít za následek vrácení všech rozšířených atributů metadat v *serializovaném řetězci JSON* .</br></br>Všechny atributy obsažené v rozšířených metadatech jsou nyní k dispozici jako atribut nejvyšší úrovně a mohou být požadovány jako takové (tj. atributy = ID, ti, DOI, IA).

> [!IMPORTANT]
> Podpora pro vyžádání individuálních rozšířených atributů pomocí "E" obor, tj. "E. DN", je zastaralý. I když je tato možnost stále technicky podporovaná, požadují se jednotlivé rozšířené atributy pomocí "E". Výsledkem bude hodnota atributu vracená na dvou místech v odpovědi JSON jako součást objektu "E" a jako atribut nejvyšší úrovně.

Název | Popis | Typ | Operace
--- | --- | --- | ---
FN | Úplný název instance konference | Řetězec | Žádný
PC | Celkový počet publikací instancí konference | Datový typ Int32 | Žádný
