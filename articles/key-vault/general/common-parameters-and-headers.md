---
title: Běžné parametry a záhlaví
description: Parametry a hlavičky společné pro všechny operace, které můžete provádět v souvislosti s Key Vault prostředky.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430874"
---
# <a name="common-parameters-and-headers"></a>Běžné parametry a záhlaví

Následující informace jsou společné pro všechny operace, které můžete provádět v souvislosti s Key Vault prostředky:

- Nahraďte `{api-version}` verzí API-Version v identifikátoru URI.
- Nahraďte `{subscription-id}` identifikátorem vašeho předplatného v IDENTIFIKÁTORu URI.
- Nahraďte `{resource-group-name}` skupinou prostředků. Další informace najdete v článku Použití skupin prostředků ke správě prostředků Azure.
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

|Název elementu | Typ | Popis |
|---|---|---|
| kód | řetězec | Typ chyby, ke které došlo.|
| zpráva | řetězec | Popis toho, co způsobilo chybu. |



## <a name="see-also"></a>Viz také
 [Odkaz na Azure Key Vault REST API](/rest/api/keyvault/)
