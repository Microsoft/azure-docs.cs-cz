---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: a49df0c18ef9db9d0d41ca2e714474e6386ae7d1
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2018
ms.locfileid: "48904468"
---
<a name="definitions"></a>
## <a name="definitions"></a>Definice

<a name="point"></a>
### <a name="point"></a>Bod

|Název|Popis|Schéma|
|---|---|---|
|**Časové razítko**  <br>*Volitelné*|Časové razítko pro datový bod. Ujistěte se, že je v souladu s o půlnoci a použití UTC datum a čas, například řetězec 2017-08-01T00:00:00Z.|řetězec (datum a čas)|
|**Hodnota**  <br>*Volitelné*|Datovou hodnotu míry.|číslo (double)|


<a name="request"></a>
### <a name="request"></a>Žádost

|Název|Popis|Schéma|
|---|---|---|
|**Období**  <br>*Volitelné*|Období datových bodů. Pokud hodnota je null nebo nemá k dispozici, rozhraní API se automaticky určit období.|číslo (double)|
|**body**  <br>*Volitelné*|Data časových řad odkazuje. Data mají být řazeny podle časového razítka vzestupně tak, aby odpovídaly výsledek anomálií. Pokud data nejsou seřazená správně nebo neexistuje duplicitní časové razítko, rozhraní API pro detekci anomálií body správně, ale nelze dobře odpovídají body se vrátil s vstupu. V takovém případě se přidají upozornění v odpovědi.|< [bod](#point) > pole|


<a name="response"></a>
### <a name="response"></a>Odpověď

|Název|Popis|Schéma|
|---|---|---|
|**ExpectedValues**  <br>*Volitelné*|Model založený na předpovězené hodnoty učení. Pokud vstupní datové body jsou seřazeny podle časového razítka ascending, index pole je možné mapovat na očekávanou hodnotu a původní hodnotu.|pole < číslo (double) >|
|**IsAnomaly**  <br>*Volitelné*|Výsledek toho, zda datové body se anomálie nebo není v obou směrech (špičky nebo poklesy). Hodnota TRUE znamená, že se bod nachází anomálií, false znamená, že se bod nachází mimo anomálií. Pokud vstupní datové body jsou seřazeny podle časového razítka ascending, index pole je možné mapovat na očekávanou hodnotu a původní hodnotu.|pole < logická hodnota >|
|**IsAnomaly_Neg**  <br>*Volitelné*|Výsledek toho, zda datové body se anomálie ve směru záporná (DIP). Hodnota TRUE znamená, že směr anomálii je záporná. Pokud vstupní datové body jsou seřazeny podle časového razítka ascending, index pole je možné mapovat na očekávanou hodnotu a původní hodnotu.|pole < logická hodnota >|
|**IsAnomaly_Pos**  <br>*Volitelné*|Výsledek toho, zda datové body se anomálie v kladné směru (špičky). Hodnota TRUE znamená, že je směr anomálii kladné. Pokud vstupní datové body jsou seřazeny podle časového razítka ascending, index pole je možné mapovat očekávaným způsobem a je původní hodnota.|pole < logická hodnota >|
|**LowerMargin**  <br>*Volitelné*|(ExpectedValue - LowerMargin) Určuje dolní mez, která datového bodu je stále si mysleli, že jako obvykle. Pokud vstupní datové body jsou seřazeny podle časového razítka ascending, index pole je možné mapovat na očekávanou hodnotu a původní hodnotu.|pole < číslo (double) >|
|**Období**  <br>*Volitelné*|Doba, která používá rozhraní API pro detekci anomálií body.|číslo (float)|
|**UpperMargin**  <br>*Volitelné*|Součet ExpectedValue a UpperMargin Určuje horní mez, která datového bodu je stále si mysleli, že jako obvykle. Pokud vstupní datové body jsou seřazeny podle časového razítka ascending, index pole je možné mapovat na očekávanou hodnotu a původní hodnotu.|pole < číslo (double) >|
|**WarningText**  <br>*Volitelné*|Pokud vstupní datové body k dispozici nejsou pravidlo, které rozhraní API vyžaduje, a data lze zjistit stále rozhraním API, rozhraní API analyzuje data a připojit upozornění informace v tomto poli.|řetězec|



