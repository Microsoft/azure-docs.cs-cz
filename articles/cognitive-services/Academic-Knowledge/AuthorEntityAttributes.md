---
title: Atributy entit autora – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Seznamte se s atributy, které můžete použít s entitou Author v Academic Knowledge API.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: d5fc770c380397f605f8810fa41d3a8907f2358e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146491"
---
# <a name="author-entity"></a>Vytvořit entitu

> [!NOTE]
> Následující atributy jsou specifické pro entitu Author. (Ty = ' 1 ')

Název | Popis | Typ | Operace
--- | --- | --- | ---
ID      | ID entity                             |Int64      |Rovná se
AuN     | Normalizovaný Název autora                    |Řetězec     |Rovná se
CC      | Celkový počet citací autora           |Datový typ Int32      |Žádný  
DAuN    | Zobrazované jméno autora                   |Řetězec     |Žádný
E | Rozšířená metadata</br></br>**Důležité**: Tento atribut je zastaralý a podporuje se jenom pro starší verze aplikací. Vyžadování tohoto atributu jednotlivě (tj. atributy = ID, ti, E) bude mít za následek vrácení všech rozšířených atributů metadat v *serializovaném řetězci JSON* .</br></br>Všechny atributy obsažené v rozšířených metadatech jsou nyní k dispozici jako atribut nejvyšší úrovně a mohou být požadovány jako takové (tj. atributy = ID, ti, DOI, IA). | [Cházející](#extended) | Žádný
ECC     | Celkový počet předpokládaných citací pro autora |Datový typ Int32      |Žádný
LKA.AfId | ID entity posledního známého umístění pro autora | Int64 | Žádný
LKA. AfN | Normalizovaný název posledního známého umístění pro autora | Řetězec | Žádný
PC | Celkový počet publikací autora | Datový typ Int32 | Žádný

## <a name="extended"></a>Cházející

> [!IMPORTANT]
> Tento atribut je zastaralý a podporuje se jenom pro starší verze aplikací. Vyžadování tohoto atributu jednotlivě (tj. atributy = ID, ti, E) bude mít za následek vrácení všech rozšířených atributů metadat v *serializovaném řetězci JSON* .</br></br>Všechny atributy obsažené v rozšířených metadatech jsou nyní k dispozici jako atribut nejvyšší úrovně a mohou být požadovány jako takové (tj. atributy = ID, ti, DOI, IA).

> [!IMPORTANT]
> Podpora pro vyžádání individuálních rozšířených atributů pomocí "E" obor, tj. "E. DN", je zastaralý. I když je tato možnost stále technicky podporovaná, požadují se jednotlivé rozšířené atributy pomocí "E". Výsledkem bude hodnota atributu vracená na dvou místech v odpovědi JSON jako součást objektu "E" a jako atribut nejvyšší úrovně.

Název | Popis | Typ | Operace
--- | --- | --- | ---
LKA.AfId | ID entity posledního známého umístění pro autora | Int64 | Žádný
LKA. AfN | Normalizovaný název posledního známého umístění pro autora | Řetězec | Žádný
PC | Celkový počet publikací autora | Datový typ Int32 | Žádný
