---
title: Chyby a řešení pro scénáře B2B – Azure Logic Apps | Dokumentace Microsoftu
description: Najít chyby a řešení pro scénáře B2B v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/02/2017
ms.openlocfilehash: f0591b47ce7ba6837f300088c856c0098fb66710
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60998816"
---
# <a name="b2b-errors-and-solutions-for-azure-logic-apps"></a>B2B chyby a řešení pro Azure Logic Apps

Tento článek pomáhá řešit chyby, které může dojít v Logic Apps B2B scénáře a navrhne odpovídající opatření pro opravu těchto chyb.

## <a name="agreement-resolution"></a>Řešení smlouvy

### <a name="no-agreement-found"></a>Nenašla se žádná smlouva 

|   |   |  
|---|---|
| Popis chyby | Nenašla se parametry řešení smlouvy se žádná smlouva. | 
| Akce uživatele | Smlouva přidaly do účtu pro integraci s dohodnutém obchodními identitami. </br>Vstupní zpráva ID by se měl shodovat obchodními identitami. |  
|   |   |

### <a name="no-agreement-found-with-identities"></a>S identitami nenašla se žádná smlouva

|   |   | 
|---|---|
| Popis chyby | S identitami nenašla se žádná smlouva: 'AS2Identity':: "Partner1" a 'AS2Identity':: 'Partner3. | 
| Akce uživatele | Neplatný AS2-od nebo AS2-do nakonfigurovaného pro smlouvy. </br>Opravte zprávu AS2 "AS2-z" nebo "AS2-na" záhlaví nebo smlouvy tak, aby odpovídaly AS2 ID v hlavičkách zpráv AS2 konfigurací smlouvy. |
|   |   |     

## <a name="as2"></a>AS2

### <a name="missing-as2-message-headers"></a>Chybí záhlaví zpráv AS2  

|   |   |  
|---|---|
| Popis chyby | Neplatné záhlaví AS2. Jeden z "AS2-na" nebo "AS2-z" záhlaví je prázdný. | 
| Akce uživatele | Byla přijata zpráva AS2, která neobsahovala AS2-od nebo AS2-k nebo oba záhlaví. </br> Zkontrolujte zprávu AS2 AS2-od a AS2-na záhlaví a správné je na základě smlouvy konfigurace. |
|  |  | 

### <a name="missing-as2-message-body-and-headers"></a>Chybí tělo zprávy AS2 a hlavičky    

|   |   |  
|---|---|
| Popis chyby | Obsah požadavku je null nebo prázdný. | 
| Akce uživatele | Byla přijata zpráva AS2, která neobsahovala tělo zprávy. |
|  |  | 

### <a name="as2-message-decryption-failure"></a>Selhání dešifrování zprávy AS2

|   |   | 
|---|---|
| Popis chyby |  [zpracování/Chyba: dešifrování se nezdařilo] | 
| Akce uživatele | Přidat @base64ToBinary k AS2Message před odesláním partnerovi. |
|||

Příklad:

```json
"HTTP": {
   "inputs": {
   "body": "@base64ToBinary(body('Encode_to_AS2_message')?['AS2Message']?['Content'])",
   "headers": "@body('Encode_to_AS2_message')?['AS2Message']?['OutboundHeaders']",
   "method": "POST",
   "uri": "xxxxx.xxx"
},
``` 

### <a name="mdn-decryption-failure"></a>Selhání dešifrování zprávy MDN.

|   |   | 
|---|---|
| Popis chyby |  [zpracování/Chyba: dešifrování se nezdařilo] | 
| Akce uživatele | Přidat @base64ToBinary do zprávy MDN. před odesláním partnerovi. | 
|||

Příklad:

```json
"Response": {
   "inputs": {
   "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
   "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
   "statusCode": 200
},               
``` 

### <a name="missing-signing-certificate"></a>Chybí certifikát pro podpis

|   |   |  
|---|---|
| Popis chyby| Pro stranu AS2 není nakonfigurovaný certifikát pro podpis. </br>AS2-od: partner1 AS2-k: partner2 | 
| Akce uživatele | Konfigurace nastavení smlouvy AS2 pomocí správný certifikát pro podpis. |
|  |  | 

## <a name="x12-and-edifact"></a>X12 a EDIFACT

### <a name="leading-or-trailing-space-found"></a>Počáteční nebo koncová mezera nalezen    
    
|   |   | 
|---|---|
| Popis chyby | Během analýzy se zjistila chyba. Sady transakcí EDIFACT s ID "123456"obsažená ve výměně (bez skupiny) s ID ' 987654 ", ID odesílatele"Partner1!, ID příjemce "Partner2" je pozastavená s těmito chybami: <p>"Úvodní nalezen oddělovač Trailing" |
| Akce uživatele | Nastavení smlouvy, která se má nakonfigurovat tak, aby počátečních a koncových znaků. </br>Upravte nastavení smlouvy, které povolí počátečními a koncovou mezerou. |
|   |   |

![místo](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="duplicate-check-has-enabled-in-the-agreement"></a>Duplicitní kontrola povolila smlouvy

|   |   | 
|---|---| 
| Popis chyby | Duplicitní kontrolní číslo |
| Akce uživatele | Tato chyba označuje, že má duplicitní kontrolní čísla přijaté zprávy. </br>Opravte kontrolní číslo a opakujte odeslání zprávy. |
|   |   |

### <a name="missing-schema-in-the-agreement"></a>Chybí schéma smlouvy

|   |   | 
|---|---| 
| Popis chyby | Během analýzy se zjistila chyba. X12 s ID "564220001' obsažená ve funkční skupině s ID"56422', ve výměně s ID '000056422!, ID odesílatele 12345678 "", ID příjemce "87654321' sada transakcí se pozastavuje s těmito chybami: <p>"Zpráva má neznámý typ dokumentu a nepřeložila se na žádné existující schéma nakonfigurované ve smlouvě" |
| Akce uživatele | Schéma konfigurace v nastavení smlouvy.  |
|   |   |

### <a name="incorrect-schema-in-the-agreement"></a>Nesprávné schéma smlouvy

|   |   | 
|---|---| 
| Popis chyby | Zpráva má neznámý typ dokumentu a nepřeložila se na žádné existující schéma nakonfigurované ve smlouvě. |
| Akce uživatele | V nastavení smlouvy nakonfigurujte správné schéma. |
|   |   |

## <a name="flat-file"></a>Plochý soubor

### <a name="input-message-with-no-body"></a>Vstupní zpráva s hlavní části

|   |   | 
|---|---|
| Popis chyby | InvalidTemplate. Na řádku vstupů nelze zpracovat výrazy jazyka šablony v akci 'Flat_File_Decoding', '1' ve sloupci "1902": "Vyžaduje vlastnost 'content' očekává hodnotu, ale bylo obdrženo hodnotu null. Cesta ".". |
| Akce uživatele | Tato chyba označuje, že vstupní zprávy neobsahuje tělo. |
|   |   | 

