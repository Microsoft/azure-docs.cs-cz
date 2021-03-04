---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 61576de4a57d55ea9d1ea209c52df556f0069617
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750835"
---
| Vlastnost | Popis |
|:--- |:---|
|**Identita/typ** | Typ ověřování, které se použilo k vytvoření žádosti. Například: `OAuth` , `Kerberos` , `SAS Key` , `Account Key` nebo `Anonymous` |
|**Identita/tokenHash**|Toto pole je rezervováno pouze pro interní použití. |
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
