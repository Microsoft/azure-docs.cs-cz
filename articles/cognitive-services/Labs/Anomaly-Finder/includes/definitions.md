---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: ff36202b67f6262b7ba67fe48ef37f2b656b91fa
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343021"
---
<a name="definitions"></a>
## <a name="definitions"></a>Definice

<a name="point"></a>
### <a name="point"></a>Bod

|Název|Popis|Schéma|
|---|---|---|
|**Časové razítko**  <br>*Volitelné*|Časové razítko pro datový bod. Zajistěte, aby zarovnaná s půlnoc a použití UTC datum a čas, například řetězec 2017-08-01T00:00:00Z.|řetězec (datum a čas)|
|**Hodnota**  <br>*Volitelné*|Datové hodnoty míry.|číslo (Dvojitá přesnost)|


<a name="request"></a>
### <a name="request"></a>Žádost

|Název|Popis|Schéma|
|---|---|---|
|**Období**  <br>*Volitelné*|Období datových bodů. Pokud hodnota je null nebo nemá k dispozici, rozhraní API automaticky určuje období.|číslo (Dvojitá přesnost)|
|**Body**  <br>*Volitelné*|Data řady čas body. Data by měla seřazená podle časového razítka vzestupné tak, aby odpovídala výsledku anomálií. Pokud data není seřazen správně nebo je duplicitní časové razítko, rozhraní API zjištění anomálií body správně, ale nelze dobře odpovídají body vrátí s vstupu. V takovém případě bude přidán upozornění v odpovědi.|< [bod](#point) > pole|


<a name="response"></a>
### <a name="response"></a>Odpověď

|Název|Popis|Schéma|
|---|---|---|
|**ExpectedValues**  <br>*Volitelné*|Předpovězené hodnoty metodou učení základě modelu. Pokud vstupní datové body jsou seřazené podle časového razítka vzestupně, index pole můžete použít k mapování očekávaná hodnota a původní hodnotu.|pole < číslo (Dvojitá přesnost) >|
|**IsAnomaly**  <br>*Volitelné*|Výsledek na tom, zda datové body jsou anomálií nebo není v obou směrech (špičky nebo vyhrazené IP adresy). nastavena hodnota TRUE znamená, že se bod nachází anomálií, false znamená, že se bod nachází bez anomálií. Pokud vstupní datové body jsou seřazené podle časového razítka vzestupně, index pole můžete použít k mapování očekávaná hodnota a původní hodnotu.|pole < logická hodnota >|
|**IsAnomaly_Neg**  <br>*Volitelné*|Výsledek na tom, zda datové body jsou anomálie v záporné směru (vyhrazené). Hodnota TRUE znamená, že směr anomálií je záporná. Pokud vstupní datové body jsou seřazené podle časového razítka vzestupně, index pole můžete použít k mapování očekávaná hodnota a původní hodnotu.|pole < logická hodnota >|
|**IsAnomaly_Pos**  <br>*Volitelné*|Výsledek na tom, zda datové body jsou anomálie v kladné směru (špičky). Hodnota TRUE znamená, že směr anomálií je kladné. Pokud vstupní datové body jsou seřazené podle časového razítka vzestupně, index pole můžete použít k mapování očekávané a původní hodnotu.|pole < logická hodnota >|
|**LowerMargin**  <br>*Volitelné*|(ExpectedValue - LowerMargin) určuje, že je stále představit jako normální dolní hranice, která datového bodu. Pokud vstupní datové body jsou seřazené podle časového razítka vzestupně, index pole můžete použít k mapování očekávaná hodnota a původní hodnotu.|pole < číslo (Dvojitá přesnost) >|
|**Období**  <br>*Volitelné*|Období, které používají rozhraní API pro zjištění anomálií bodů.|číslo (float)|
|**UpperMargin**  <br>*Volitelné*|Součet ExpectedValue a UpperMargin Určuje, že je stále představit jako normální horní hranice, která datového bodu. Pokud vstupní datové body jsou seřazené podle časového razítka vzestupně, index pole můžete použít k mapování očekávaná hodnota a původní hodnotu.|pole < číslo (Dvojitá přesnost) >|
|**WarningText**  <br>*Volitelné*|Pokud vstupní datové body, které poskytuje nejsou podle pravidla, které vyžaduje rozhraní API a data lze zjistit stále rozhraním API, rozhraní API se analyzovat data a připojit upozornění informace v tomto poli.|řetězec|



