---
title: Běžné parametry a záhlaví
description: Parametry a záhlaví společné pro všechny operace, které můžete provést v souvislosti s prostředky trezoru klíčů.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: d0ada9c1e6b45b1be17b15b67f67fc64fc266203
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430874"
---
# <a name="common-parameters-and-headers"></a>Běžné parametry a záhlaví

Následující informace jsou společné pro všechny operace, které můžete provést v souvislosti s prostředky trezoru klíčů:

- Nahraďte `{api-version}` verzi rozhraní API v identifikátoru URI.
- Nahrazení `{subscription-id}` identifikátorem předplatného v identifikátoru URI
- Nahraďte `{resource-group-name}` skupinou prostředků. Další informace najdete v článku Použití skupin prostředků ke správě prostředků Azure.
- Nahraďte `{vault-name}` název trezoru klíčů v identifikátoru URI.
- Nastavte hlavičku Content-Type na application/json.
- Nastavte hlavičku autorizace na webový token JSON, který získáte z Azure Active Directory (AAD). Další informace najdete v tématu ověřování požadavků [Azure Resource Manager.](authentication-requests-and-responses.md)

## <a name="common-error-response"></a>Běžná chybová odpověď
Služba použije stavové kódy HTTP k označení úspěchu nebo neúspěchu. Kromě toho chyby obsahují odpověď v následujícím formátu:

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|Název elementu | Typ | Popis |
|---|---|---|
| kód | řetězec | Typ chyby, ke které došlo.|
| zpráva | řetězec | Popis toho, co chybu způsobilo. |



## <a name="see-also"></a>Viz také
 [Odkaz na rozhraní API úložiště klíčů Azure](/rest/api/keyvault/)
