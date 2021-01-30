---
title: Pojmenované entity pro zdravotnictví
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 10/02/2020
ms.author: aahi
ms.openlocfilehash: 614d0fe69cee88791559758d5e08dda66672669b
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097255"
---
[Analýza textu pro stav](../../how-tos/text-analytics-for-health.md) detekuje lékařské koncepty v následujících kategoriích.  (Všimněte si, že tento kontejner ve verzi Preview podporuje jenom anglický text a v každé imagi kontejneru je k dispozici jenom jedna modelová verze.)


| Kategorie  | Popis  |
|---------|---------|
| [ROZBOR](#anatomy) | koncepty, které zaznamenávají informace o tělních a anatomii, lokalitách, umístěních nebo oblastech. |
 | [DEMOGRAFICKÝCH údajů](#demographics) | koncepty, které zaznamenávají informace o pohlaví a stáří. |
 | [PROVĚŘEN](#examinations) | koncepty, které zaznamenávají informace o diagnostických procedurách a testech. |
 | [GENOMICS](#genomics) | koncepty, které zaznamenávají informace o genůch a variantách. |
 | [ZDRAVOTNÍ](#healthcare) | koncepty, které zaznamenávají informace o událostech správy, prostředích péče a zdravotních profesích. |
 | [LÉKAŘSKÝ STAV](#medical-condition) | koncepty, které zaznamenávají informace o Diagnostics, symptomech nebo symbolech. |
 | [LÉKŮ](#medication) | koncepty, které zaznamenávají informace o léků, včetně léků názvů, tříd, dávkování a směrování správy. |
 | [RODN](#social) | koncepty, které zaznamenávají informace o lékařsky relevantních sociálních aspektech, jako je například rodinný vztah. |
 | [NÍ](#treatment) | koncepty, které zaznamenávají informace o léčebných postupech. |
  
Každá kategorie může zahrnovat dvě skupiny konceptů:

* **Entity** – pojem, který zachycuje lékařské koncepty, jako je například diagnostika, název léků nebo název zpracování.  Například *bronchitis* je diagnóza a *aspirin* je název léků.  Zmínky v této skupině mohou být propojeny s ID konceptu UMLS.
* **Atributy** – fráze, které poskytují další informace o zjištěné entitě, například *vážný* je kvalifikátor podmínky pro *bronchitis* nebo *81 mg* , je dávkou pro *aspirin*.  Zmínky v této kategorii nebudou propojeny s ID konceptu UMLS.

Kromě toho služba rozpoznává vztahy mezi různými koncepty, včetně vztahů mezi atributy a entitami, například *směr* pro *strukturu těla* nebo *dávkování* do *léků názvů* a vztahů mezi entitami, například při detekci zkratky.

## <a name="anatomy"></a>Rozbor

### <a name="entities"></a>Entity

**BODY_STRUCTURE** – základní systémy, umístění a oblasti anatomie a weby. Například ARM, kolenum, břicho, nos, játra, hlava, dýchací systém, lymphocytes.

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="Příklad entity struktury těla.":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="Rozbalený příklad entity struktury karoserie.":::

### <a name="attributes"></a>Atributy

**Směrové** směry, například: Left, příčný, horní, druhý, který charakterizuje strukturu těla.

:::image type="content" source="../../media/ta-for-health/anatomy-attributes.png" alt-text="Příklad obousměrného atributu.":::

### <a name="supported-relations"></a>Podporované relace

* **DIRECTION_OF_BODY_STRUCTURE**

## <a name="demographics"></a>Demografie

### <a name="entities"></a>Entity

**Stáří** – všechny věkové smlouvy a fráze, včetně členů pacienta, rodinných příslušníků a dalších. Příklad: 40-year-old, 51 Yo, 3 měsíce Old, dospělý, kojence, seniory, mladý, nezletilý, střední-stáří.

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="Příklad entity typu věk.":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="Další příklad entity typu věk.":::


**Pohlaví** – pojem, který zveřejňuje pohlaví subjektu. Například muž, Žena, Žena, gentleman, Lady.

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="Příklad entity žen":::

### <a name="attributes"></a>Atributy

**RELATIONAL_OPERATOR** – fráze, které vyjadřují vztah mezi demografickou entitou a dalšími informacemi.

:::image type="content" source="../../media/ta-for-health/relational-operator.png" alt-text="Příklad relačního operátoru.":::

## <a name="examinations"></a>Zkoušky

### <a name="entities"></a>Entity

**EXAMINATION_NAME** – diagnostické postupy a testy. Například MRI, ECG, HIV test, hemoglobin, Count na úrovni systému, jako je například *Bristol Stool Scale*.

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="Příklad entity zkoušky":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="Dalším příkladem entity názvu testu.":::

### <a name="attributes"></a>Atributy

**Směr** – směrové výrazy, které charakterizují zkoušku.

:::image type="content" source="../../media/ta-for-health/exam-direction-attribute.png" alt-text="Příkladem atributu Direction s entitou názvu testu":::

**MEASUREMENT_UNIT** – jednotka kontroly. Například v *hemoglobin > 9,5 g/dl* je termín *g/dl* jednotka pro test *hemoglobin* .

:::image type="content" source="../../media/ta-for-health/exam-unit-attribute.png" alt-text="Příklad atributu měrné jednotky s entitou názvu testu":::

**MEASUREMENT_VALUE** – hodnota kontroly. Například v *hemoglobin > 9,5 g/dl*, je výraz *9,5* hodnota pro test *hemoglobin* .

:::image type="content" source="../../media/ta-for-health/exam-value-attribute.png" alt-text="Příklad atributu hodnot měření s entitou názvu testu":::

**RELATIONAL_OPERATOR** – fráze, které vyjadřují vztah mezi kontrolou a dalšími informacemi. Například požadovaná hodnota měření pro cílovou zkoušku.

:::image type="content" source="../../media/ta-for-health/exam-relational-operator-attribute.png" alt-text="Příklad relačního operátoru s entitou názvu testu":::

**Čas** – dočasné podmínky vztahující se na začátek a/nebo délku (trvání) kontroly. Například při pokusu o test.

:::image type="content" source="../../media/ta-for-health/exam-time-attribute.png" alt-text="Příklad atributu Time s entitou názvu testu":::

### <a name="supported-relations"></a>Podporované relace

+ **DIRECTION_OF_EXAMINATION**
+   **RELATION_OF_EXAMINATION**
+   **TIME_OF_EXAMINATION**
+   **UNIT_OF_EXAMINATION**
+   **VALUE_OF_EXAMINATION**

## <a name="genomics"></a>Genomics

### <a name="entities"></a>Entity

**Genů** – všechny zmínky o genů. Například MTRR, F2.

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="Příklad entity genů":::

**Variant** – všechny zmínky o variacích genů Například c. 524C>T, (MTRR): r.1462_1557del96
  
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

### <a name="attributes"></a>Atributy

**CONDITION_QUALIFIER** podmínky kvality, které se používají k popisu lékařské podmínky. Všechny následující podkategorie se považují za kvalifikátory:

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

**Směrové** podmínky, které charakterizují lékařský stav těla.

:::image type="content" source="../../media/ta-for-health/medical-condition-direction-attribute.png" alt-text="Příkladem atributu Direction s entitou lékařské podmínky.":::

**Frekvence** – jak často došlo k lékařské situaci, nastane nebo by se měla objevit.

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute.png" alt-text="Příklad atributu frekvence s entitou lékařské podmínky.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute-2.png" alt-text="Další příklad atributu Direction s příznakem nebo entitou podpisu.":::

**MEASUREMENT_UNIT** -jednotka charakterizující lékařský stav. Například v *1,5 x2x1 cm tumor* je pojem *cm* jednotka měření pro *tumor*. 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-unit-attribute.png" alt-text="Příklad atributu měrné jednotky s entitou lékařské podmínky.":::

**MEASUREMENT_VALUE** – hodnota charakterizující lékařský stav. Například v hodnotě *1,5 x2x1 cm tumor* pojem *1,5 x2x1* je hodnota měření pro *tumor*. 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-value-attribute.png" alt-text="Snímek obrazovky ukazuje příklad atributu Direction s příznakem nebo entitou podpisu.":::

**RELATIONAL_OPERATOR** – fráze, které vyjadřují vztah mezi lékařskými podmínkami Další informace. Například hodnota času nebo měření. 

:::image type="content" source="../../media/ta-for-health/medical-condition-relational-operator.png" alt-text="Snímek obrazovky ukazuje jiný příklad atributu Direction s příznakem nebo entitou podpisu.":::

**Časově** dočasná období související s počátkem a/nebo délkou (trvání) lékařské podmínky. Například při spuštění příznaku nebo při výskytu choroby.

:::image type="content" source="../../media/ta-for-health/medical-condition-time-attribute.png" alt-text="Snímek obrazovky ukazuje další příklad atributu Direction s příznakem nebo symbolovou entitou.":::

### <a name="supported-relations"></a>Podporované relace

+ **DIRECTION_OF_CONDITION**
+   **QUALIFIER_OF_CONDITION**
+   **TIME_OF_CONDITION**
+   **UNIT_OF_CONDITION**
+   **VALUE_OF_CONDITION**

## <a name="medication"></a>Léků

### <a name="entities"></a>Entity

**MEDICATION_CLASS** – sada medications, která má podobný mechanismus působení, související režim akce, podobná chemická struktura a/nebo se používá ke zpracování stejné choroby. Například pro inhibičníi ACE, opioid, antibiotika a bolesti.

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="Příklad entity třídy léků":::

**MEDICATION_NAME** – zmínky o lékůech, včetně chráněných názvů značek a názvů, které nejsou značkou. Například Advil, ibuprofen.

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="Příklad entity názvu léků":::

### <a name="attributes"></a>Atributy

**Dávka** -množství léků seřazeno. Například řešení chloridu sodného v inzapékací *1000 ml*.

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="Příklad atributu dávkování léků.":::

**Frekvence** – jak často by měl být léků.

:::image type="content" source="../../media/ta-for-health/medication-frequency.png" alt-text="Příklad atributu frekvence léků":::

:::image type="content" source="../../media/ta-for-health/medication-frequency-2.png" alt-text="Další příklad atributu četnosti léků":::

**MEDICATION_FORM** – forma léků Například řešení, tablety, kapsle, tablet, patch, gelu, vložení, pěna, sprej, kapek, krém, sirup.

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="Příklad atributu formuláře léků":::

**MEDICATION_ROUTE** – metoda správy pro léků. Například orální, vaginal, IV, epidural, může být užitečná, vdechováno.

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="Příklad atributu Route léků":::

**RELATIONAL_OPERATOR** – fráze, které vyjadřují vztah mezi léků a dalšími informacemi. Například požadovaná hodnota měření.

:::image type="content" source="../../media/ta-for-health/medication-relational-operator.png" alt-text="Snímek obrazovky ukazuje příklad atributu relační operátor s entitou léků.":::

:::image type="content" source="../../media/ta-for-health/medication-time.png" alt-text="Snímek obrazovky ukazuje jiný příklad atributu relační operátor s entitou léků.":::

### <a name="supported-relations"></a>Podporované relace

+ **DOSAGE_OF_MEDICATION**
+   **FORM_OF_MEDICATION**
+   **FREQUENCY_OF_MEDICATION**
+   **ROUTE_OF_MEDICATION**
+   **TIME_OF_MEDICATION**

## <a name="social"></a>Sociální sítě

### <a name="entities"></a>Entity

**FAMILY_RELATION** – zmínky o rodinných příbuzních daného subjektu. Například otců, dceřiné, na stejné úrovni, rodiče.

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="Snímek obrazovky ukazuje jiný příklad atributu doby zpracování.":::

## <a name="treatment"></a>Ní

### <a name="entities"></a>Entity

**TREATMENT_NAME** – léčebné postupy. Například výměna za kolena, kostní dřeně transplant, TAVI, dietní.

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="Příklad entity názvu ošetření.":::

### <a name="attributes"></a>Atributy

**Směrové** podmínky, které charakterizují ošetření.

:::image type="content" source="../../media/ta-for-health/treatment-direction.png" alt-text="Snímek obrazovky ukazuje příklad atributu směr ošetření.":::

**Frekvence** – jak často dochází k ošetření, nebo by se mělo objevit.

:::image type="content" source="../../media/ta-for-health/treatment-frequency.png" alt-text="Snímek obrazovky ukazuje další příklad atributu směr ošetření.":::
 
**RELATIONAL_OPERATOR** – fráze, které vyjadřují vztah mezi zpracováním a dalšími informacemi.  Například kolik času bylo předáno z předchozího postupu.

:::image type="content" source="../../media/ta-for-health/treatment-relational-operator.png" alt-text="Příklad atributu relační operátor ošetření.":::

**Časově** dočasné podmínky vztahující se na začátek a/nebo délku (trvání) ošetření. Například datum, kdy bylo zpracování uděleno.

:::image type="content" source="../../media/ta-for-health/treatment-time.png" alt-text="Snímek obrazovky ukazuje příklad atributu doby zpracování.":::

### <a name="supported-relations"></a>Podporované relace

+ **DIRECTION_OF_TREATMENT**
+   **TIME_OF_TREATMENT**
+   **FREQUENCY_OF_TREATMENT**
