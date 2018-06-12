---
title: 'Seznam B2B aplikace logiky chyby a řešení: Azure App Service | Microsoft Docs'
description: Seznam B2B aplikace logiky chyby a řešení
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/02/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 8503792d57dd7ed64434d5a306afce850ced77db
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298334"
---
# <a name="logic-apps-b2b-list-of-errors-and-solutions"></a>Seznam B2B aplikace logiky chyby a řešení  
Tento článek vám pomůže vyřešit chyby, které by se mohlo stát v scénáře B2B aplikace logiky a navrhne příslušné akce při opravách tyto chyby.


## <a name="agreement-resolution"></a>Smlouva řešení

### <a name="no-agreement-found"></a>* Byla nalezena žádná smlouva. 

|   |   |  
|---|---|
| Popis chyby | Žádné smlouvy nalezen s parametry řešení smlouvy|    
| Akce uživatele | Smlouvy musí být přidaní do účtu integrace s identitami odsouhlaseného firmy.</br> Obchodní identity by se měl shodovat identifikátory vstupní zpráv|  
|   |   |

### <a name="-no-agreement-found-with-identities"></a>* Žádná smlouva se identity nalezena.

|   |   | 
|---|---|
| Popis chyby | Žádná nalezena s identitami smlouva: 'AS2Identity':: 'Partner1' a 'AS2Identity':: 'Partner3.| 
| Akce uživatele | Neplatný AS2-z nebo AS2-na nakonfigurované pro smlouvu. </br> Správné AS2 zpráva AS2-z nebo AS2-hlavičky nebo dohody, tak, aby odpovídaly AS2 ID v AS2 zprávy záhlaví s konfigurací smlouvy |
|   |   |     

## <a name="as2"></a>AS2

### <a name="-missing-as2-message-headers"></a>* Chybí záhlaví AS2 zpráv  

|   |   |  
|---|---|
| Popis chyby| Neplatný AS2 hlavičky. Jeden z ' AS2-k ' nebo ' AS2-z, hlavičky jsou prázdné| 
| Akce uživatele | Byla přijata zpráva AS2, který neobsahoval AS2-z nebo AS2-na nebo oba hlavičky. </br> Zkontrolujte zprávy a AS2 AS2-z a AS2-hlavičky a správnou je na základě smlouvy konfigurace |
|  |  | 


### <a name="-missing-as2-message-body-and-headers"></a>* Chybí tělo zprávy AS2 a hlavičky    

|   |   |  
|---|---|
| Popis chyby| Obsah žádosti je null nebo prázdná | 
| Akce uživatele | Byla přijata zpráva AS2, který neobsahoval text zprávy |
|  |  | 

### <a name="-as2-message-decryption-failure"></a>* Chyba při dešifrování zprávy AS2

|   |   | 
|---|---|
| Popis chyby |  [zpracování chybou: Neúspěšné dešifrování] | 
| Akce uživatele | Přidat @base64ToBinary k AS2Message před odesláním partnerovi 
```java
            "HTTP": {
                "inputs": {
                    "body": "@base64ToBinary(body('Encode_to_AS2_message')?['AS2Message']?['Content'])",
                    "headers": "@body('Encode_to_AS2_message')?['AS2Message']?['OutboundHeaders']",
                    "method": "POST",
                    "uri": "xxxxx.xxx"
                },
                
``` 

### <a name="-mdn-decryption-failure"></a>* Chyba při dešifrování MDN

|   |   | 
|---|---|
| Popis chyby |  [zpracování chybou: Neúspěšné dešifrování] | 
| Akce uživatele | Přidat @base64ToBinary k MDN před odesláním partnerovi 
```java
            "Response": {
                "inputs": {
                    "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
                    "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
                    "statusCode": 200
                },
                
``` 

### <a name="-missing-signing-certificate"></a>* Chybí podpisový certifikát

|   |   |  
|---|---|
| Popis chyby| Podpisový certifikát nebyl nakonfigurován pro stranu AS2. </br> AS2-z: partner1 AS2-k: partner2 | 
| Akce uživatele | Konfigurace nastavení smlouvy AS2 s správný certifikát pro podpis |
|  |  | 

## <a name="x12-and-edifact"></a>X12 a EDIFACT

### <a name="-leading-or-trailing-space-found"></a>* Počáteční nebo koncové mezery nalezené    
    
|   |   | 
|---|---|
| Popis chyby | Během analýzy došlo k chybě. Transakce Edifact nastavit s id ' 123456 'obsažená v výměnu (bez skupina) s id ' 987654', s id odesílatele 'Partner1', id příjemce 'Partner2' bylo pozastaveno s následujícími chybami: úvodní koncové oddělovače nalezen |
| Akce uživatele | Nastavení smlouvy a nakonfigurovat tak, aby počátečních a koncových znaků. </br> Upravit nastavení smlouvy, které povolí úvodní a koncové místa |
|   |   |

![Povolit místa](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="-duplicate-check-has-enabled-in-the-agreement"></a>* Duplicitní kontrola povolil smlouvy

|   |   | 
|---|---| 
| Popis chyby | Duplicitní kontrolní číslo |
| Akce uživatele | Tato chyba znamená, že má duplicitní řízení čísla přijaté zprávy. </br> Opravte řízení číslo a opakujte odeslání zprávy |
|   |   |

### <a name="-missing-schema-in-the-agreement"></a>* Chybějící schématu smlouvy

|   |   | 
|---|---| 
| Popis chyby | Během analýzy došlo k chybě. X12 sadu transakce s id '564220001' obsažená v funkční skupiny s id '56422', v výměnu s id '000056422"s id odesílatele ' 12345678', id příjemce ' 87654321' bylo pozastaveno s následujícími chybami" zpráva má typ Neznámý dokumentu ND nebylo možné přeložit do jakéhokoli z existující schémata nakonfigurované smlouvy" |
| Akce uživatele | Konfigurace schématu v nastavení smlouvy  |
|   |   |

### <a name="-incorrect-schema-in-the-agreement"></a>* Nesprávné schéma smlouvy

|   |   | 
|---|---| 
| Popis chyby | Zpráva má neznámý typ dokumentu a nepřeložila se na žádné existující schéma nakonfigurované ve smlouvě. |
| Akce uživatele | Konfigurace správné schéma v nastavení smlouvy  |
|   |   |

## <a name="flat-file"></a>Plochý soubor

### <a name="-input-message-with-no-body"></a>* Vstupní zprávu s žádný text

|   |   | 
|---|---|
| Popis chyby | InvalidTemplate. Nelze výrazy jazyka šablony procesů v vstupy 'Flat_File_Decoding' akce v řádku '1' a ve sloupci '1902': ' požadované vlastnosti 'content' očekává hodnotu ale byla null. Cesta k ".". |
| Akce uživatele | Tato chyba označuje, že vstupní zprávy neobsahuje text |
|   |   | 

## <a name="learn-more"></a>Další informace
[Další informace o Enterprise integračního balíčku](logic-apps-enterprise-integration-overview.md)