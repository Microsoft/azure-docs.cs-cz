---
title: Běžné parametry a záhlaví
description: Parametry a hlavičky společné pro všechny operace, které můžete provádět v souvislosti s Key Vault prostředky.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 7ec8e1df71e6513b13e9c37174a3363471be01d9
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70879283"
---
# <a name="common-parameters-and-headers"></a>Běžné parametry a záhlaví

Následující informace jsou společné pro všechny operace, které můžete provádět v souvislosti s Key Vault prostředky:

- Nahraďte `{api-version}` verzí API-Version v identifikátoru URI.
- Nahraďte `{subscription-id}` identifikátorem vašeho předplatného v identifikátoru URI.
- Nahraďte `{resource-group-name}` skupinou prostředků. Další informace najdete v tématu použití skupin prostředků ke správě prostředků Azure.
- Nahraďte `{vault-name}` názvem vašeho trezoru klíčů v identifikátoru URI.
- Nastavte hlavičku Content-Type na Application/JSON.
- Nastavte hlavičkou autorizace na JSON Web Token, kterou získáte z Azure Active Directory (AAD). Další informace najdete v tématu [ověřování žádostí Azure Resource Manager](authentication-requests-and-responses.md) .

## <a name="common-error-response"></a>Běžná chybová odpověď
Služba bude používat stavové kódy HTTP k indikaci úspěchu nebo selhání. Kromě toho chyby obsahují odpověď v následujícím formátu:

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|Název elementu | type | Popis |
|---|---|---|
| code | řetězec | Typ chyby, ke které došlo.|
| zpráva | řetězec | Popis toho, co způsobilo chybu. |



## <a name="see-also"></a>Viz také
 [Odkaz na Azure Key Vault REST API](/rest/api/keyvault/)
