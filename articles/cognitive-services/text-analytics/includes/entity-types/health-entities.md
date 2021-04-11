---
title: Pojmenované entity pro zdravotnictví
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: 805c726d33f2050f6f2797c0689069aa5ec4ee71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599295"
---
[Analýza textu pro procesy stavu](../../how-tos/text-analytics-for-health.md) a vyextrahují přehledy z nestrukturovaných lékařských dat. Služba detekuje lékařské koncepty, přiřadí kontrolní výrazy k konceptům, odvodí sémantické vztahy mezi koncepty a propojuje je se společnými lékaři ontologie.

Analýza textu pro stav detekuje lékařské koncepty v následujících kategoriích. V této verzi Preview je podporován pouze anglický text a k dispozici je pouze jedna verze modelu.

| Kategorie  | Popis  |
|---------|---------|
| [ROZBOR](#anatomy) | koncepty, které zachycují informace o karoserii a systémech anatomie, lokalitách, umístěních nebo oblastech. |
 | [DEMOGRAFICKÝCH údajů](#demographics) | koncepty, které zaznamenávají informace o pohlaví a stáří. |
 | [PROVĚŘEN](#examinations) | koncepty, které zaznamenávají informace o diagnostických procedurách a testech. |
 | [OBECNÉ ATRIBUTY](#general-attributes) | koncepty, které poskytují další informace o dalších konceptech z výše uvedených kategorií. |
 | [GENOMICS](#genomics) | koncepty, které zaznamenávají informace o genůch a variantách. |
 | [ZDRAVOTNÍ](#healthcare) | koncepty, které zaznamenávají informace o událostech správy, prostředí péče o péči a zdravotním povolání. |
 | [LÉKAŘSKÝ STAV](#medical-condition) | koncepty, které zaznamenávají informace o Diagnostics, symptomech nebo symbolech. |
 | [LÉKŮ](#medication) | koncepty, které zaznamenávají informace o léků, včetně léků názvů, tříd, dávkování a postupu správy. |
 | [RODN](#social) | koncepty, které zaznamenávají informace o lékařsky relevantních sociálních aspektech, jako je například rodinný vztah. |
 | [NÍ](#treatment) | koncepty, které zaznamenávají informace o léčebných postupech. |

Najdete tady další informace a příklady.

## <a name="anatomy"></a>Rozbor

### <a name="entities"></a>Entity

**BODY_STRUCTURE** – základní systémy, umístění a oblasti anatomie a weby. Například ARM, kolenum, břicho, nos, játra, hlava, dýchací systém, lymphocytes.

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="Příklad entity struktury těla.":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="Rozbalený příklad entity struktury karoserie.":::

## <a name="demographics"></a>Demografie

### <a name="entities"></a>Entity

**Stáří** – všechny věkové smlouvy a fráze, včetně těch, které jsou pro pacienty, rodinné příslušníky a další. Příklad: 40-year-old, 51 Yo, 3 měsíce Old, dospělý, kojence, seniory, mladý, nezletilý, střední-stáří.

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="Příklad entity typu věk.":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="Další příklad entity typu věk.":::


**Pohlaví** – pojem, který zveřejňuje pohlaví subjektu. Například muž, Žena, Žena, gentleman, Lady.

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="Příklad entity žen":::

## <a name="examinations"></a>Zkoušky

### <a name="entities"></a>Entity

**EXAMINATION_NAME** – diagnostické postupy a testy, včetně důležitých příznaků a měření textu. Například MRI, ECG, HIV test, hemoglobin, Count na úrovni systému, jako je například *Bristol Stool Scale*.

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="Příklad entity zkoušky":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="Dalším příkladem entity názvu testu.":::

## <a name="general-attributes"></a>Obecné atributy

### <a name="entities"></a>Entity

**Datum** a úplné datum týkající se lékařské podmínky, kontroly, léčby, léků nebo administrativní události.

**Směr** – směrové podmínky, které se mohou vztahovat na strukturu těla, lékařský stav, průzkum nebo léčbu, jako například: Left, příčný, horní a druhý.

**Frekvence** – popisuje, jak často došlo k lékařskému stavu, zkoušení, zpracování nebo léků, dochází k tomu, nebo by se mělo objevit.

**MEASUREMENT_VALUE** – hodnota související s zkouškou nebo měřením lékařské podmínky.

**MEASUREMENT_UNIT** – jednotka měření související s zkouškou nebo měřením lékařské podmínky.

**RELATIONAL_OPERATOR** – fráze, které vyjadřují kvantitativní vztah mezi entitou a dalšími informacemi.

**Časově** dočasné podmínky vztahující se na začátek a/nebo délku (trvání) lékařské podmínky, šetření, zpracování, léků nebo administrativní události. 

## <a name="genomics"></a>Genomics

### <a name="entities"></a>Entity

**GENE_OR_PROTEIN** – všechny zmínky o názvech a symbolech lidského genů a také chromozomy a části chromozomů a bílkovin. Například MTRR, F2.

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="Příklad entity genů":::

**Variant** – všechny zmínky o variacích a mutacích genů Například `c.524C>T` ,, `(MTRR):r.1462_1557del96`
  
## <a name="healthcare"></a>Zdravotnictví

### <a name="entities"></a>Entity
  
**ADMINISTRATIVE_EVENT** – události, které se týkají systému zdravotní péče, ale administrativní/částečně administrativní povahy. Například registrace, použití, zkušební verze, vstup studie, přenos, vypouštění, hospitalizaci a nemocnice. 

:::image type="content" source="../../media/ta-for-health/healthcare-event-entity.png" alt-text="Příklad osoby zdravotní péče.":::

**CARE_ENVIRONMENT** – prostředí nebo umístění, kde se pacientům dostanou starosti. Například záchranná místnost, kancelář, cardio jednotka, hospice a nemocnice.

:::image type="content" source="../../media/ta-for-health/healthcare-environment-entity.png" alt-text="Tento snímek obrazovky ukazuje příklad entity prostředí zdravotnictví.":::

**HEALTHCARE_PROFESSION** – licencování specialisty na zdravotní péči nebo nelicencované. Například Dentist, pathologist, neurologist, radiologie, pharmacist, výživa, fyzický therapist, chiropractor.

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity.png" alt-text="Tento snímek obrazovky ukazuje jiný příklad entity prostředí zdravotnictví.":::

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity-2.png" alt-text="Další příklad entity péče o zdravotní prostředí.":::

## <a name="medical-condition"></a>Lékařský stav

### <a name="entities"></a>Entity

**Diagnóza** – nemoc, Syndrome a otrava. Například prsní rakovinu, Alzheimerova, HTN, CHF, zranění kabelů.

:::image type="content" source="../../media/ta-for-health/medical-condition-entity.png" alt-text="Příklad entity lékařské podmínky.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-entity-2.png" alt-text="Další příklad entity lékařské podmínky.":::

**SYMPTOM_OR_SIGN** – subjektivní nebo objektivní důkaz choroby nebo jiné diagnostiky. Například: bedna, starostí, dizziness, rash, SOB, břich byl měkký, dobrý bowel zvuků, dobře.

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity.png" alt-text="Příklad entity pro podepsání nebo příznak lékařské podmínky":::

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity-2.png" alt-text="Dalším příkladem pro podepsání nebo příznak příznaku zdravotní podmínky.":::

**CONDITION_QUALIFIER** – kvalitativní podmínky, které se používají k popisu lékařské podmínky. Všechny následující podkategorie se považují za kvalifikátory:

1.  Výrazy související s časem: Jedná se o termín, který popisuje kvantitativní časovou dimenzi, jako je například náhlé, akutní, chronická, dlouhodobě zavazuje chránit. 
2.  Výrazy kvality: Jedná se o podmínky, které popisují "charakter" lékařské podmínky, jako je například vypalování, Sharp.
3.  Výrazy závažnosti: závažná, mírná, bitová, neřízená.
4.  Výrazy Extensivity: místní, kontaktní, rozptýlené.
5.  Radiační výrazy: vyradiování, záření.
6.  Měřítko podmínky: v některých případech je podmínka charakterizována škálováním, což je konečný seřazený seznam hodnot. Například pacienty s fáze III pancreatic rakovinu.
7.  Podmínka kurzu: termín, který souvisí s kurzem nebo průběhem podmínky, jako je například zlepšování, zhoršení, vyřešení, opakování. 

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis.png" alt-text="Příklad atributu kvalifikátoru podmínky a entity diagnostiky.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-2.png" alt-text="Další příklad atributu kvalifikátoru podmínky a entity diagnostiky.":::

:::image type="content" source="../../media/ta-for-health/conditional-qualifier-symptom-medication.png" alt-text="Příklad atributu kvalifikátoru podmínky s entitami příznakem a léků.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" alt-text="Tento snímek obrazovky ukazuje další příklad atributu kvalifikátoru podmínky s entitou diagnózy.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-symptom.png" alt-text="Tento snímek obrazovky ukazuje další příklad atributu kvalifikátoru podmínky s entitou diagnózy.":::

## <a name="medication"></a>Léků

### <a name="entities"></a>Entity

**MEDICATION_CLASS** – sada medications, která má podobný mechanismus působení, související režim akce, podobná chemická struktura a/nebo se používá ke zpracování stejné choroby. Například pro inhibičníi ACE, opioid, antibiotika a bolesti.

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="Příklad entity třídy léků":::

**MEDICATION_NAME** – zmínky o lékůech, včetně chráněných názvů značek a názvů, které nejsou značkou. Například ibuprofen.

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="Příklad entity názvu léků":::

**Dávka** -množství léků seřazeno. Například řešení chloridu sodného v inzapékací *1000 ml*.

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="Příklad atributu dávkování léků.":::

**MEDICATION_FORM** – forma léků Například řešení, tablety, kapsle, tablet, patch, gelu, vložení, pěna, sprej, kapek, krém, sirup.

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="Příklad atributu formuláře léků":::

**MEDICATION_ROUTE** – metoda správy pro léků. Například orální, vaginal, IV, epidural, může být užitečná, vdechováno.

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="Příklad atributu Route léků":::

## <a name="social"></a>Sociální sítě

### <a name="entities"></a>Entity

**FAMILY_RELATION** – zmínky o rodinných příbuzních daného subjektu. Například otců, dceřiné, na stejné úrovni, rodiče.

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="Snímek obrazovky ukazuje jiný příklad atributu doby zpracování.":::

## <a name="treatment"></a>Ní

### <a name="entities"></a>Entity

**TREATMENT_NAME** – léčebné postupy. Například výměna za kolena, kostní dřeně transplant, TAVI, dietní.

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="Příklad entity názvu ošetření.":::
