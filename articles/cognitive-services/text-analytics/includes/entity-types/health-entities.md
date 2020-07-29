---
title: Pojmenované entity pro zdravotnictví
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: 4ac65e85e05f408b8d2f37a1d6845dc9e28e2bab
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373062"
---
## <a name="health-entity-categories"></a>Kategorie entit stavu:

[Analýza textu pro stav](../../how-tos/text-analytics-for-health.md)vrátí následující kategorie entit.  Upozorňujeme, že v tomto kontejneru Preview je podporovaný jenom anglický text a v každé imagi kontejneru je k dispozici jenom jedna modelová verze.

### <a name="named-entity-recognition"></a>Rozpoznávání pojmenovaných entit

|Kategorie  |Popis   |
|----------|--------------|
| VĚKEM | Ve věku. |
| BODY_STRUCTURE | Části lidského těla, včetně orgánů a dalších struktur. | 
| CONDITION_QUALIFIER | Úrovně podmínek, jako je *mírná*, *Rozšířená*nebo *difúze*. | 
| STANOVENÍ | Lékařské podmínky. Například " *mikronapětí* . | 
| SMĚR | Pokyny, jako je *Left* nebo *anterior*. | 
| DÁVKOVAČ | Velikost nebo množství léků.  | 
| EXAMINATION_NAME | Metoda nebo procedura kontroly. | 
| EXAMINATION_RELATION | přidružení mezi měrnou jednotkou a prohlídkou.  | 
| EXAMINATION_UNIT | Měrná jednotka pro zkoušku. | 
| EXAMINATION_VALUE | Hodnota jednotky měření prověřování. | 
| FAMILY_RELATION | Familial vztah, jako je například " *sesterské*".  | 
| OPAKOVÁNÍ | Opakování.   | 
| POHLAVÍ | Pohlaví. | 
| GENŮ | Entita genu, jako je *TP53*.   | 
| MEDICATION_CLASS | Třídy léků Například *antibiotika*.  | 
| MEDICATION_NAME  | Obecná a značka s názvem medications.| 
| ROUTE_OR_MODE  | Způsob správy léků | 
| SYMPTOM_OR_SIGN  | Příznaky nemoci. | 
| ČAS  | Časový. Například "8 let" nebo "2:10:30 this ráno" |
| TREATMENT_NAME  | Názvy ošetření. | 
| VARIANT  | Genetická varianta entity genů | 

### <a name="relation-extraction"></a>Extrakce vztahu

Extrakce relace identifikuje smysluplná připojení mezi koncepty uvedenými v textu. Například relace "čas podmínky" je nalezena přidružením názvu podmínky k času. Analýza textu pro stav mohou identifikovat následující vztahy:

* DIRECTION_OF_BODY_STRUCTURE  
* TIME_OF_CONDITION
* QUALIFIER_OF_CONDITION  
* DOSAGE_OF_MEDICATION 
* FORM_OF_MEDICATION  
* ROUTE_OR_MODE_OF_MEDICATION   
* STRENGTH_OF_MEDICATION 
* ADMINISTRATION_RATE_OF_MEDICATION   
* FREQUENCY_OF_MEDICATION 
* TIME_OF_MEDICATION 
* TIME_OF_TREATMENT 
* FREQUENCY_OF_TREATMENT  
* VALUE_OF_EXAMINATION
* UNIT_OF_EXAMINATION 
* RELATION_OF_EXAMINATION 
* TIME_OF_EXAMINATION  
* ZKRATKA 
