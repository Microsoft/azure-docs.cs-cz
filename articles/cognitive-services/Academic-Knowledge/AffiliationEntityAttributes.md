---
title: Atributy entity přidružení v Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Seznamte se s atributy, které můžete použít spolu s entitou afilace v Academic Knowledge API.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: 52dcccebe1a1cbab7a6afadeb6b543b34b8b1eb7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143935"
---
# <a name="affiliation-entity"></a>Entita přidružení

> [!NOTE]
> Následující atributy jsou specifické pro entitu přidružení. (Ty = ' 5 ')

Název | Popis | Typ | Operace
--- | --- | --- | ---
AfN | Normalizovaný název přidružení |Řetězec |Rovná se
CC | Celkový počet citací v přidružení |Datový typ Int32        |Žádná  
DAfN | Zobrazovaný název afilace |Řetězec |Žádná
E | Rozšířená metadata</br></br>**Důležité**: Tento atribut je zastaralý a podporuje se jenom pro starší verze aplikací. Vyžadování tohoto atributu jednotlivě (tj. atributy = ID, ti, E) bude mít za následek vrácení všech rozšířených atributů metadat v *serializovaném řetězci JSON* .</br></br>Všechny atributy obsažené v rozšířených metadatech jsou nyní k dispozici jako atribut nejvyšší úrovně a mohou být požadovány jako takové (tj. atributy = ID, ti, DOI, IA). | [Cházející](#extended) | Žádný
ECC | Celkový počet odhadovaných citací v přidružení |Datový typ Int32 |Žádná
ID | ID entity |Int64 |Rovná se
PC | Celkový počet publikací napsaných v afilaci s touto entitou | Datový typ Int32 | Žádný

## <a name="extended"></a>Cházející

> [!IMPORTANT]
> Tento atribut je zastaralý a podporuje se jenom pro starší verze aplikací. Vyžadování tohoto atributu jednotlivě (tj. atributy = ID, ti, E) bude mít za následek vrácení všech rozšířených atributů metadat v *serializovaném řetězci JSON* .</br></br>Všechny atributy obsažené v rozšířených metadatech jsou nyní k dispozici jako atribut nejvyšší úrovně a mohou být požadovány jako takové (tj. atributy = ID, ti, DOI, IA).

> [!IMPORTANT]
> Podpora pro vyžádání individuálních rozšířených atributů pomocí "E" obor, tj. "E. DN", je zastaralý. I když je tato možnost stále technicky podporovaná, požadují se jednotlivé rozšířené atributy pomocí "E". Výsledkem bude hodnota atributu vracená na dvou místech v odpovědi JSON jako součást objektu "E" a jako atribut nejvyšší úrovně.

Název | Popis | Typ | Operace
--- | --- | --- | ---
PC | Celkový počet publikací napsaných v afilaci s touto entitou | Datový typ Int32 | Žádný
