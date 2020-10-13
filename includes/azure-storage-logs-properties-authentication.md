---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: e3732823be1c8391f2bec9018a094200c7c93a5e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711265"
---
| Vlastnost | Popis |
|:--- |:---|
|**Identita/typ** | Typ ověřování, které se použilo k vytvoření žádosti. Například: `OAuth` , `SAS Key` , `Account Key` nebo `Anonymous` |
|**Identita/tokenHash**|Toto pole je rezervováno pouze pro interní použití. |
|**autorizace/akce** | Akce, která je přiřazena k žádosti. |
|**autorizace/roleAssignmentId** | ID přiřazení role Například: `4e2521b7-13be-4363-aeda-111111111111`.|
|**autorizace/roleDefinitionId** | ID definice role Například: `ba92f5b4-2d11-453d-a403-111111111111"`.|
|**objekty zabezpečení/ID** | ID objektu zabezpečení Například: `a4711f3a-254f-4cfb-8a2d-111111111111`.|
|**objekty zabezpečení/typ** | Typ objektu zabezpečení Například: `ServicePrincipal`. |
|**žadatel/appID** | ID aplikace Open Authorization (OAuth), které se používá jako žadatel. <br> Například: `d3f7d5fe-e64a-4e4e-871d-333333333333`.|
|**žadatel/cílová skupina** | Cílová skupina OAuth žádosti. Například: `https://storage.azure.com`. |
|**žadatel/objectId** | ID objektu OAuth žadatele. V případě ověřování protokolem Kerberos představuje identifikátor objektu ověřeného uživatele protokolu Kerberos. Například: `0e0bf547-55e5-465c-91b7-2873712b249c`. |
|**žadatel/tenantId** | ID tenanta OAuth identity. Například: `72f988bf-86f1-41af-91ab-222222222222`.|
|**žadatel/tokenIssuer** | Vystavitel tokenu OAuth. Například: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`.|
|**žadatel nebo hlavní název uživatele** | Hlavní název uživatele (UPN) žadatele. Například: `someone@contoso.com`. |
|**žadatel/uživatelské jméno** | Toto pole je rezervováno pouze pro interní použití.|