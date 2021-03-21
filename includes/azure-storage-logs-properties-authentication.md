---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: ca8963ed8928745a6d5918c86021199432339c83
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104611930"
---
| Vlastnost | Popis |
|:--- |:---|
|**Identita/typ** | Typ ověřování, které se použilo k vytvoření žádosti. Například: `OAuth` , `Kerberos` , `SAS Key` , `Account Key` nebo `Anonymous` |
|**Identita/tokenHash**|Hodnota hash SHA-256 ověřovacího tokenu použitého v žádosti. <br>Pokud je typ ověřování `Account Key` , formát je "klíč1 \| key2 (SHA256 hash klíče)". Příklad: `key1(5RTE343A6FEB12342672AFD40072B70D4A91BGH5CDF797EC56BF82B2C3635CE)`. <br>Pokud je typ ověřování `SAS Key` , formát je "klíč1 \| key2 (SHA 256 hash klíče); SASSIGNATURE (SHA 256 hash tokenu SAS)". Příklad: `key1(0A0XE8AADA354H19722ED12342443F0DC8FAF3E6GF8C8AD805DE6D563E0E5F8A),SasSignature(04D64C2B3A704145C9F1664F201123467A74D72DA72751A9137DDAA732FA03CF)`. Pokud je typ ověřování `OAuth` , formát je "SHA 256 hash tokenu OAuth". Příklad: `B3CC9D5C64B3351573D806751312317FE4E910877E7CBAFA9D95E0BE923DW25C`<br> Pro jiné typy ověřování neexistuje žádné pole tokenHash. |
|**autorizace/akce** | Akce, která je přiřazena k žádosti. |
|**autorizace/roleAssignmentId** | ID přiřazení role Příklad: `4e2521b7-13be-4363-aeda-111111111111`.|
|**autorizace/roleDefinitionId** | ID definice role Příklad: `ba92f5b4-2d11-453d-a403-111111111111"`.|
|**objekty zabezpečení/ID** | ID objektu zabezpečení Příklad: `a4711f3a-254f-4cfb-8a2d-111111111111`.|
|**objekty zabezpečení/typ** | Typ objektu zabezpečení Příklad: `ServicePrincipal`. |
|**žadatel/appID** | ID aplikace Open Authorization (OAuth), které se používá jako žadatel. <br> Příklad: `d3f7d5fe-e64a-4e4e-871d-333333333333`.|
|**žadatel/cílová skupina** | Cílová skupina OAuth žádosti. Příklad: `https://storage.azure.com`. |
|**žadatel/objectId** | ID objektu OAuth žadatele. V případě ověřování protokolem Kerberos představuje identifikátor objektu ověřeného uživatele protokolu Kerberos. Příklad: `0e0bf547-55e5-465c-91b7-2873712b249c`. |
|**žadatel/tenantId** | ID tenanta OAuth identity. Příklad: `72f988bf-86f1-41af-91ab-222222222222`.|
|**žadatel/tokenIssuer** | Vystavitel tokenu OAuth. Příklad: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`.|
|**žadatel nebo hlavní název uživatele** | Hlavní název uživatele (UPN) žadatele. Příklad: `someone@contoso.com`. |
|**žadatel/uživatelské jméno** | Toto pole je rezervováno pouze pro interní použití.|
