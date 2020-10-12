---
title: Řešení běžných chyb a problémů ve scénářích B2B
description: Vyhledá řešení běžných chyb a problémů při řešení potíží s scénáři B2B v Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/02/2017
ms.openlocfilehash: 6400cfe7e524dcc16e08c2bba7dfba4a62d00b2e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86232555"
---
# <a name="b2b-errors-and-solutions-for-azure-logic-apps"></a>Chyby a řešení B2B pro Azure Logic Apps

Tento článek vám pomůže řešit chyby, ke kterým může dojít v Logic Apps B2Bch scénářích, a navrhuje příslušné akce pro opravu těchto chyb.

## <a name="agreement-resolution"></a>Řešení smlouvy

### <a name="no-agreement-found"></a>Nenašla se žádná smlouva. 

**Popis chyby**: nebyla nalezena žádná smlouva s parametry řešení smlouvy.

**Akce uživatele**: smlouva by se měla přidat do účtu pro integraci s dohodnutými obchodními identitami. Obchodní identity by se měly shodovat s ID vstupních zpráv.

### <a name="no-agreement-found-with-identities"></a>Nenašla se žádná smlouva s identitami.

**Popis chyby**: nenašla se žádná smlouva s identitami: ' AS2Identity ':: ' Partner1 ' and'AS2Identity ':: ' Partner3 '

**Akce uživatele**: neplatný AS2-From nebo AS2-To nakonfigurována pro smlouvu. Opravte záhlaví "AS2-from" nebo "AS2-do" zprávy AS2 nebo smlouvu tak, aby odpovídala identifikátorům AS2 v záhlavích zpráv AS2 s konfiguracemi smluv.

## <a name="as2"></a>AS2

### <a name="missing-as2-message-headers"></a>Chybějící záhlaví zpráv AS2  

**Popis chyby**: neplatné hlavičky AS2. Jedna z hlaviček "AS2-do" nebo "AS2-from" je prázdná.

**Akce uživatele**: byla přijata zpráva AS2, která neobsahovala AS2-From nebo AS2-To nebo obě hlavičky. Ověřte, AS2-From zprávy a AS2-To záhlaví a opravte je na základě konfigurace smlouvy.

### <a name="missing-as2-message-body-and-headers"></a>Chybí text a záhlaví zprávy AS2.    

**Popis chyby**: obsah požadavku má hodnotu null nebo je prázdný.

**Akce uživatele**: byla přijata zpráva AS2, která neobsahovala tělo zprávy.

### <a name="as2-message-decryption-failure"></a>Selhání dešifrování zprávy AS2

**Popis chyby**: [zpracováno/Chyba: dešifrování – neúspěšné]

**Akce uživatele**: @base64ToBinary před odesláním partnerovi přidejte do AS2Message.

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

**Popis chyby**: [zpracováno/Chyba: dešifrování – neúspěšné]

**Akce uživatele**: @base64ToBinary před odesláním partnerovi přidejte do MDN.

Například:

```json
"Response": {
   "inputs": {
   "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
   "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
   "statusCode": 200
},               
``` 

### <a name="missing-signing-certificate"></a>Chybí podpisový certifikát.

**Popis chyby**: podpisový certifikát nebyl KONFIGUROVÁN pro AS2 stranu. AS2-from: partner1 AS2-to: partner2

**Akce uživatele**: Nakonfigurujte nastavení smlouvy AS2 se správným certifikátem pro podpis.

## <a name="x12-and-edifact"></a>X12 a EDIFACT

### <a name="leading-or-trailing-space-found"></a>Bylo nalezeno počáteční nebo koncové místo.    

**Popis chyby**: při analýze došlo k chybě. Sada transakcí EDIFACT s ID "123456" obsažená v výměny (bez skupiny) s ID "987654" s ID odesílatele "Partner1", ID příjemce "Partner2" se pozastavuje s následujícími chybami: "nalezen úvodní oddělovač na konci"

**Akce uživatele**: nastavení smlouvy, které se má nakonfigurovat tak, aby umožňovalo počáteční a koncové místo. Upravte nastavení smlouvy tak, aby umožňovalo počáteční a koncové místo.

![povolené místo](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="duplicate-check-has-enabled-in-the-agreement"></a>V této smlouvě je povolená duplicitní kontroly.

**Popis chyby**: duplicitní kontrolní číslo

**Akce uživatele**: Tato chyba znamená, že přijatá zpráva obsahuje duplicitní řídicí čísla. Opravte kontrolní číslo a odešlete zprávu znovu.

### <a name="missing-schema-in-the-agreement"></a>Chybí schéma v rámci smlouvy.

**Popis chyby**: při analýze došlo k chybě. Sada transakcí X12 s ID "564220001" obsažená ve skupině funkcí s ID "56422" v rámci výměny s ID "000056422", s ID odesílatele "12345678", ID příjemce "87654321" je pozastavena s následujícími chybami: "zpráva má neznámý typ dokumentu a nebyla přeložena na žádná ze stávajících schémat nakonfigurovaných v této smlouvě"

**Akce uživatele**: konfigurace schématu v nastavení smlouvy.

### <a name="incorrect-schema-in-the-agreement"></a>Nesprávné schéma v rámci smlouvy

**Popis chyby**: zpráva má neznámý typ dokumentu a nevyřešila se na žádná ze stávajících schémat nakonfigurovaných v této smlouvě.

**Akce uživatele**: Konfigurace správného schématu v nastavení smlouvy.

## <a name="flat-file"></a>Plochý soubor

### <a name="input-message-with-no-body"></a>Vstupní zpráva bez těla

**Popis chyby**: InvalidTemplate. Nelze zpracovat výrazy jazyka šablony ve vstupech akce Flat_File_Decoding na řádku 1 a ve sloupci 1902: požadovaná vlastnost content očekává hodnotu, ale měla hodnotu null. Cesta ' '. '.

**Akce uživatele**: Tato chyba indikuje, že vstupní zpráva neobsahuje tělo.
