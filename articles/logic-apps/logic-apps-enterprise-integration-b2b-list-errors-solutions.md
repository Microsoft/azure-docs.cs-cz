---
title: Řešení běžných chyb a problémů ve scénářích B2B
description: Hledání řešení běžných chyb a problémů při řešení potíží se scénáři B2B v Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/02/2017
ms.openlocfilehash: 38e281ce3d8117bff719b1bb572f09acbbb89669
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666682"
---
# <a name="b2b-errors-and-solutions-for-azure-logic-apps"></a>Chyby a řešení B2B pro aplikace Azure Logic Apps

Tento článek vám pomůže řešit chyby, ke kterým může dojít ve scénářích Logic Apps B2B a navrhne vhodné akce pro opravu těchto chyb.

## <a name="agreement-resolution"></a>Řešení dohody

### <a name="no-agreement-found"></a>Nebyla nalezena žádná dohoda. 

|   |   |  
|---|---|
| Popis chyby | Nebyla nalezena žádná dohoda s parametry řešení smlouvy. | 
| Akce uživatele | Dohoda by měla být přidána do integračního účtu s dohodnutými obchodními identitami. </br>Obchodní identity by se měly shodovat s ID vstupní zprávy. |  
|   |   |

### <a name="no-agreement-found-with-identities"></a>Nebyla nalezena žádná dohoda s identitami.

|   |   | 
|---|---|
| Popis chyby | Nebyla nalezena žádná dohoda s identitami: 'AS2Identity'::'Partner1' a'AS2Identity'::'Partner3' | 
| Akce uživatele | Neplatný AS2-From nebo AS2-To nakonfigurovaný pro smlouvu. </br>Opravte hlavičky zprávy AS2 nebo "AS2-To" zprávy AS2 nebo dohodu tak, aby odpovídala ID AS2 v záhlaví zpráv AS2 s konfiguracemi smlouvy. |
|   |   |     

## <a name="as2"></a>AS2

### <a name="missing-as2-message-headers"></a>Chybí záhlaví zpráv AS2  

|   |   |  
|---|---|
| Popis chyby | Neplatné hlavičky AS2. Jedno z hlaviček "AS2-To" nebo "AS2-Od" je prázdné. | 
| Akce uživatele | Byla přijata zpráva AS2, která neobsahovala as2-from nebo AS2-To nebo obě záhlaví. </br> Zkontrolujte hlavičky zprávy AS2-From a AS2-To a opravte je na základě konfigurace smlouvy. |
|  |  | 

### <a name="missing-as2-message-body-and-headers"></a>Chybějící tělo zprávy AS2 a záhlaví    

|   |   |  
|---|---|
| Popis chyby | Obsah požadavku je null nebo prázdný. | 
| Akce uživatele | Byla přijata zpráva AS2, která neobsahovala text zprávy. |
|  |  | 

### <a name="as2-message-decryption-failure"></a>Selhání dešifrování zprávy AS2

|   |   | 
|---|---|
| Popis chyby |  [zpracováno/Chyba: dešifrování se nezdařilo] | 
| Akce uživatele | Přidat @base64ToBinary do AS2Message před odesláním partnerovi. |
|||

Například:

```json
"HTTP": {
   "inputs": {
   "body": "@base64ToBinary(body('Encode_to_AS2_message')?['AS2Message']?['Content'])",
   "headers": "@body('Encode_to_AS2_message')?['AS2Message']?['OutboundHeaders']",
   "method": "POST",
   "uri": "xxxxx.xxx"
},
``` 

### <a name="mdn-decryption-failure"></a>Selhání dešifrování MDN

|   |   | 
|---|---|
| Popis chyby |  [zpracováno/Chyba: dešifrování se nezdařilo] | 
| Akce uživatele | Přidat @base64ToBinary do MDN před odesláním partnerovi. | 
|||

Například:

```json
"Response": {
   "inputs": {
   "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
   "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
   "statusCode": 200
},               
``` 

### <a name="missing-signing-certificate"></a>Chybí podpisový certifikát

|   |   |  
|---|---|
| Popis chyby| Podpisový certifikát nebyl nakonfigurován pro stranu AS2. </br>AS2-Od: partner1 AS2-To: partner2 | 
| Akce uživatele | Nakonfigurujte nastavení smlouvy AS2 se správným certifikátem pro podpis. |
|  |  | 

## <a name="x12-and-edifact"></a>X12 a EDIFACT

### <a name="leading-or-trailing-space-found"></a>Nalezena úvodní nebo koncová mezera    
    
|   |   | 
|---|---|
| Popis chyby | Při analýzě došlo k chybě. Sada transakcí EDIFACT s ID "123456" obsažená ve výměně (bez skupiny) s ID "987654", s ID odesílatele "Partner1", ID příjemce "Partner2" je pozastavena s následujícími chybami: <p>"Byl nalezen přední trailový oddělovač" |
| Akce uživatele | Nastavení smlouvy, které má být nakonfigurováno tak, aby umožňovalo úvodní a koncové místo. </br>Upravte nastavení smlouvy, abyste povolili úvodní a koncový prostor. |
|   |   |

![povolit prostor](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="duplicate-check-has-enabled-in-the-agreement"></a>Duplicitní kontrola byla povolena ve smlouvě.

|   |   | 
|---|---| 
| Popis chyby | Duplicitní řídicí číslo |
| Akce uživatele | Tato chyba označuje, že přijatá zpráva má duplicitní řídicí čísla. </br>Opravte číslo ovládacího prvku a zprávu odešlete znovu. |
|   |   |

### <a name="missing-schema-in-the-agreement"></a>Chybějící schéma v dohodě

|   |   | 
|---|---| 
| Popis chyby | Při analýzě došlo k chybě. Sada transakcí X12 s ID "564220001" obsažených ve funkční skupině s ID "56422", ve výměně s ID '000056422', s ID odesílatele '12345678', ID přijímače "87654321" je pozastaveno s následujícími chybami: <p>"Zpráva má neznámý typ dokumentu a nevyřešila žádné existující schémata nakonfigurované ve smlouvě" |
| Akce uživatele | V nastavení smlouvy nakonfigurujte schéma.  |
|   |   |

### <a name="incorrect-schema-in-the-agreement"></a>Nesprávné schéma ve smlouvě

|   |   | 
|---|---| 
| Popis chyby | Zpráva má neznámý typ dokumentu a nevyřešila žádné existující schémata nakonfigurované ve smlouvě. |
| Akce uživatele | Nakonfigurujte správné schéma v nastavení smlouvy. |
|   |   |

## <a name="flat-file"></a>Plochý soubor

### <a name="input-message-with-no-body"></a>Vstupní zpráva bez textu

|   |   | 
|---|---|
| Popis chyby | Neplatná šablona. Nelze zpracovat výrazy jazyka šablony v akci 'Flat_File_Decoding' vstupy na řádku '1' a sloupec '1902': 'Required property 'content' očekává hodnotu, ale dostal null. Cesta ''.'. |
| Akce uživatele | Tato chyba označuje, že vstupní zpráva neobsahuje text. |
|   |   | 

