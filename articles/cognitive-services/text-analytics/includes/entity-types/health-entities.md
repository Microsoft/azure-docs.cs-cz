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
ms.openlocfilehash: 6880391fb54791fe5f597de2305d24f8c0e47ec6
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2020
ms.locfileid: "88122498"
---
## <a name="health-entity-categories"></a>Kategorie entit stavu:

[Analýza textu pro stav](../../how-tos/text-analytics-for-health.md)vrátí následující kategorie entit.  Upozorňujeme, že v tomto kontejneru Preview je podporovaný jenom anglický text a v každé imagi kontejneru je k dispozici jenom jedna modelová verze.

### <a name="named-entity-recognition"></a>Rozpoznávání pojmenovaných entit

|Kategorie  |Popis   |
|----------|--------------|
| Stáří | Ve věku. Například *staré 30 let*. |
| AdministrativeEvent | Událost správy. |
| BodyStructure | Části lidského těla, včetně orgánů a dalších struktur. Například *ARM*nebo *srdce*. | 
| CareEnvironment | Prostředí, kde je spravována péče nebo zpracování. Například *záchranná místnost* | 
| ConditionQualifier | Úrovně podmínek. Například *mírné*, *Rozšířené*nebo *rozptýlené*. | 
| Stanovení | Lékařské podmínky. Například " *mikronapětí*. | 
| Směr | Značení. Například *Left* nebo *anterior*. | 
| Dávkovač | Velikost nebo množství léků. Například *25mg*.  | 
| Prověřování | Metoda nebo procedura kontroly. Například *X-ray*. | 
| RelationalOperator | Operátor, který definuje vztah mezi dvěma entitami. Například *menší než*nebo `>=` .  | 
| MeasurementUnit | Měrná jednotka (jako procento). | 
| MeasurementValue | Číselná hodnota měrné jednotky. | 
| FamilyRelation | Familial vztah. Například: *sesterské*.  | 
| Frequency | Opakování.   | 
| Pohlaví | Pohlaví. | 
| Genů | Entita genu, jako je *TP53*.   | 
| HealthcareProfession | Způsob správy léků Například *orální Správa*. | 
| MedicationClass | Třídy léků Například *antibiotika*.  | 
| MedicationForm | Forma léků Například *kapsle*. | 
| MedicationName  | Obecná a značka s názvem medications. Například *ibuprofen*. | 
| MedicationRoute | Způsob správy léků Například *orální Správa*. | 
| SymptomOrSign  | Příznaky nemoci. Například *sore Throat*. | 
| Čas | Časový. Například *8 let* nebo *2:10:30 tuto ráno* |
| Prokládání  | Názvy ošetření. Například pro *léčbu*. | 
| Variantní | Genetická varianta entity genu | 

### <a name="relation-extraction"></a>Extrakce vztahu

Extrakce relace identifikuje smysluplná připojení mezi koncepty uvedenými v textu. Například relace "čas podmínky" je nalezena přidružením názvu podmínky k času. Analýza textu pro stav mohou identifikovat následující vztahy:

|Kategorie  |Popis   |
|----------|--------------|
| DirectionOfBodyStructure | Směr struktury těla. |
| DirectionOfCondition | Směr podmínky. |
| DirectionOfExamination | Směr kontroly. |
| DirectionOfTreatment | Směr ošetření. |
| TimeOfCondition | Čas přidružený k začátku podmínky. |
| QualifierOfCondition | Přidružený kvalifikátor pro podmínku. |
| DosageOfMedication | Dávkou léků. |
| FormOfMedication | Forma léků |
| RouteOfMedication | Trasa nebo režim využívání lékařství. Například *orální*. |
| FrequencyOfMedication | Frekvence, s jakou se léků spotřebované. | 
| ValueOfCondition | Číselná hodnota přidružená k podmínce. |
| UnitOfCondition | Jednotka (například čas) spojená s podmínkou. |
| TimeOfMedication | Čas, kdy se léků spotřeboval |
| TimeOfTreatment | Čas, kdy byla úprava spravována. | 
| FrequencyOfTreatment | Frekvence, s jakou je spravováno ošetření. |
| ValueOfExamination | Číselná hodnota přidružená k prozkoumání. | 
| UnitOfExamination | Jednotka (například procento) přidružená k prozkoumání. |
| RelationOfExamination | Vztah mezi entitou a prohlídkou. | 
| TimeOfExamination | Čas přidružený k vyšetření. |
| Zkratka | Zkratka.  | 
